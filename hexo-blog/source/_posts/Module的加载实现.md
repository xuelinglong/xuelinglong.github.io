---
title: Module的加载实现
date: 2017-05-27 23:26:36
tags:
---

# 一. 浏览器标签加载
### 传统方法
在 HTML 网页中，浏览器通过<script>标签加载 JavaScript 脚本。
` <!-- 页面内嵌的脚本 -->
<script type="application/javascript">
  // module code
</script> `

` <!-- 外部脚本 -->
<script type="application/javascript" src="path/to/myModule.js">
</script> `
由于浏览器脚本的默认语言是 JavaScript，因此type="application/javascript"可以省略

如果脚本体积很大，下载和执行的时间就会很长，因此造成浏览器堵塞，用户会感觉到浏览器“卡死”了，没有任何响应。这显然是很不好的体验，所以浏览器允许脚本异步加载
<!-- more -->
两种异步加载的语法：
` <script src="path/to/myModule.js" defer></script>
<script src="path/to/myModule.js" async></script> `
<script>标签打开defer或async属性，脚本就会异步加载。渲染引擎遇到这一行命令，就会开始下载外部脚本，但不会等它下载和执行，而是直接执行后面的命令。

defer与async的区别是：
1. defer要等到整个页面正常渲染结束，才会执行；
2. async一旦下载完，渲染引擎就会中断渲染，执行这个脚本以后，再继续渲染。
3. 即defer是“渲染完再执行”，async是“下载完就执行”。
4. 另外，如果有多个defer脚本，会按照它们在页面出现的顺序加载，而多个async脚本是不能保证加载顺序的。

### 加载规则
浏览器加载 ES6 模块，也使用<script>标签，但是要加入type="module"属性。

在网页中插入一个模块foo.js，由于type属性设为module，所以浏览器知道这是一个 ES6 模块。
` <script type="module" src="foo.js"></script> `
浏览器对于带有type="module"的<script>，都是异步加载，不会造成堵塞浏览器，即等到整个页面渲染完，再执行模块脚本，等同于打开了<script>标签的defer属性。
` <script type="module" src="foo.js"></script>
<!-- 等同于 -->
<script type="module" src="foo.js" defer></script> `

<script>标签的async属性也可以打开，这时只要加载完成，渲染引擎就会中断渲染立即执行。执行完成后，再恢复渲染。
` <script type="module" src="foo.js" async></script> `

ES6 模块也允许内嵌在网页中，语法行为与加载外部脚本完全一致。
` <script type="module">
  import utils from "./utils.js";
  // other code
</script> `

对于外部的模块脚本（上例是foo.js），有几点需要注意：
1. 代码是在模块作用域之中运行，而不是在全局作用域运行。模块内部的顶层变量，外部不可见。
2. 模块脚本自动采用严格模式，不管有没有声明use strict。
3. 模块之中，可以使用import命令加载其他模块（.js后缀不可省略，需要提供绝对 URL 或相对 URL），也可以使用export命令输出对外接口。
4. 模块之中，顶层的this关键字返回undefined，而不是指向window。也就是说，在模块顶层使用this关键字，是无意义的。
同一个模块如果加载多次，将只执行一次。

利用顶层的this等于undefined这个语法点，可以侦测当前代码是否在 ES6 模块之中。
` const isNotModuleScript = this !== undefined; `

# 二. ES6 模块与 CommonJS 模块的差异
ES6 模块与 CommonJS 模块有两个重大差异：
1. CommonJS 模块输出的是一个值的拷贝，ES6 模块输出的是值的引用。
2. CommonJS 模块是运行时加载，ES6 模块是编译时输出接口。

第二个差异是因为CommonJS 加载的是一个对象（即module.exports属性），该对象只有在脚本运行完才会生成。而 ES6 模块不是对象，它的对外接口只是一种静态定义，在代码静态解析阶段就会生成。

CommonJS 模块输出的是值的拷贝，也就是说，一旦输出一个值，模块内部的变化就影响不到这个值

` // lib.js
var counter = 3;
function incCounter() {
  counter++;
}
module.exports = {
  counter: counter,           //输出内部变量counter
  incCounter: incCounter,     //输出counter变量的内部方法incCounter
}; `
在main.js里面加载这个模块：
` // main.js
var mod = require('./lib');
console.log(mod.counter);  // 3
mod.incCounter();
console.log(mod.counter); // 3 `
因为mod.counter是一个原始类型的值，会被缓存，所以lib.js模块加载以后，它的内部变化就影响不到输出的mod.counter了。
除非写成一个函数，才能得到内部变动后的值。
` // lib.js
var counter = 3;
function incCounter() {
  counter++;
}
module.exports = {
  get counter() {
    return counter  //输出的counter属性实际上是一个取值器函数
  },
  incCounter: incCounter,
}; `
 现在再执行main.js，就可以正确读取内部变量counter的变动了。

ES6 模块的运行机制与 CommonJS 不一样。
JS 引擎对脚本静态分析的时候，遇到模块加载命令import，就会生成一个只读引用。等到脚本真正执行时，再根据这个只读引用，到被加载的那个模块里面去取值。
换句话说，ES6 的import有点像 Unix 系统的“符号连接”，原始值变了，import加载的值也会跟着变。因此，ES6 模块是动态引用，并且不会缓存值，模块里面的变量绑定其所在的模块。


# 三. Node 加载 
Node 对 ES6 模块的处理比较麻烦，因为它有自己的 CommonJS 模块格式，与 ES6 模块格式是不兼容的。目前的解决方案是，将两者分开，ES6 模块和 CommonJS 采用各自的加载方案。

在静态分析阶段，一个模块脚本只要有一行import或export语句，Node 就会认为该脚本为 ES6 模块，否则就为 CommonJS 模块。如果不输出任何接口，但是希望被 Node 认为是 ES6 模块，可以在脚本中加一行语句。
` export {}; `
上面的命令并不是输出一个空对象，而是不输出任何接口的 ES6 标准写法。

如果不指定绝对路径，Node 加载 ES6 模块会依次寻找以下脚本，与require()的规则一致。
` import './foo';
// 依次寻找
//   ./foo.js
//   ./foo/package.json
//   ./foo/index.js
import 'baz';
// 依次寻找
//   ./node_modules/baz.js
//   ./node_modules/baz/package.json
//   ./node_modules/baz/index.js
// 寻找上一级目录
//   ../node_modules/baz.js
//   ../node_modules/baz/package.json
//   ../node_modules/baz/index.js
// 再上一级目录 `

ES6 模块之中，顶层的this指向undefined；CommonJS 模块的顶层this指向当前模块，这是两者的一个重大差异。

### import命令加载 CommonJS 模块
Node 采用 CommonJS 模块格式，模块的输出都定义在module.exports这个属性上面。在 Node 环境中，使用import命令加载 CommonJS 模块，Node 会自动将module.exports属性，当作模块的默认输出，即等同于export default。



CommonJS 模块的输出缓存机制，在 ES6 加载方式下依然有效。
` // foo.js
module.exports = 123;
setTimeout(_ => module.exports = null); `
上面代码中，对于加载foo.js的脚本，module.exports将一直是123，而不会变成null。


由于 ES6 模块是编译时确定输出接口，CommonJS 模块是运行时确定输出接口，所以采用import命令加载 CommonJS 模块时，不允许采用下面的写法。
` import {readfile} from 'fs'; `
上面的写法不正确，因为fs是 CommonJS 格式，只有在运行时才能确定readfile接口，而import命令要求编译时就确定这个接口。


### require命令加载 ES6 模块
采用require命令加载 ES6 模块时，ES6 模块的所有输出接口，会成为输入对象的属性。

# 四. 循环加载
“循环加载”（circular dependency）指的是，a脚本的执行依赖b脚本，而b脚本的执行又依赖a脚本。
` // a.js
var b = require('b');
// b.js
var a = require('a'); `
通常，“循环加载”表示存在强耦合，如果处理不好，还可能导致递归加载，使得程序无法执行，因此应该避免出现。

但是实际上，这是很难避免的，尤其是依赖关系复杂的大项目，很容易出现a依赖b，b依赖c，c又依赖a这样的情况。这意味着，模块加载机制必须考虑“循环加载”的情况。

对于JavaScript语言来说，目前最常见的两种模块格式CommonJS和ES6，处理“循环加载”的方法是不一样的，返回的结果也不一样。

### CommonJS模块格式的加载原理
CommonJS的一个模块，就是一个脚本文件。require命令第一次加载该脚本，就会执行整个脚本，然后在内存生成一个对象。
` {
  id: '...',
  exports: { ... },
  loaded: true,
  ...
} `
该对象的id属性是模块名；
exports属性是模块输出的各个接口；
loaded属性是一个布尔值，表示该模块的脚本是否执行完毕；其他还有很多属性，这里都省略了。

以后需要用到这个模块的时候，就会到exports属性上面取值。即使再次执行require命令，也不会再次执行该模块，而是到缓存之中取值。
即CommonJS模块无论加载多少次，都只会在第一次加载时运行一次，以后再加载，就返回第一次运行的结果，除非手动清除系统缓存。

### CommonJS模块的循环加载
CommonJS模块的重要特性是加载时执行，即脚本代码在require的时候，就会全部执行。一旦出现某个模块被"循环加载"，就只输出已经执行的部分，还未执行的部分不会输出。


### ES6模块的循环加载
ES6模块是动态引用，如果使用import从一个模块加载变量（即import foo from 'foo'），那些变量不会被缓存，而是成为一个指向被加载模块的引用，需要开发者自己保证，真正取值的时候能够取到值。
`  // a.js如下
import {bar} from './b.js';
console.log('a.js');
console.log(bar);
export let foo = 'foo';
// b.js
import {foo} from './a.js';
console.log('b.js');
console.log(foo);
export let bar = 'bar';
//执行a.js，结果如下:
$ babel-node a.js
b.js
undefined
a.js
bar `
1. 由于a.js的第一行是加载b.js，所以先执行的是b.js。
2. 而b.js的第一行又是加载a.js，这时由于a.js已经开始执行了，所以不会重复执行，而是继续往下执行b.js，所以第一行输出的是b.js。
3. 接着，b.js要打印变量foo，这时a.js还没执行完，取不到foo的值，导致打印出来是undefined。
4. b.js执行完，开始执行a.js，这时就一切正常了。

# 五. ES6模块的转码
浏览器目前还不支持ES6模块，为了现在就能使用，可以将转为ES5的写法。除了Babel可以用来转码之外，还有以下两个方法，也可以用来转码。

### 1. ES6 module transpiler
可以将 ES6 模块转为 CommonJS 模块或 AMD 模块的写法，从而在浏览器中使用。

首先，安装这个转码器。
` $ npm install -g es6-module-transpiler `

然后，使用compile-modules convert命令，将 ES6 模块文件转码。
` $ compile-modules convert file1.js file2.js `

-o参数可以指定转码后的文件名。
` $ compile-modules convert -o out.js file1.js `


### 2. SystemJS
它是一个垫片库（polyfill），可以在浏览器内加载 ES6 模块、AMD 模块和 CommonJS 模块，将其转为 ES5 格式。它在后台调用的是 Google 的 Traceur 转码器。

使用时，先在网页内载入system.js文件。
` <script src="system.js"></script> `

然后，使用System.import方法加载模块文件。
` <script>
   System.import('./app.js');   
 </script> `
./app，指的是当前目录下的app.js文件。它可以是ES6模块文件，System.import会自动将其转码。

需要注意的是，System.import使用异步加载，返回一个 Promise 对象，可以针对这个对象编程。










