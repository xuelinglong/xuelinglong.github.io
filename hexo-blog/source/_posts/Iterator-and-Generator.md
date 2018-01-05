---
title: Iterator and Generator
date: 2017-06-02 21:23:02
tags:
---

# 十五. Iterator和for...of循环
Iterator(遍历器)是一种接口，为各种不同的数据结构提供统一的访问机制。数据结构只要有了Iterator接口，就可以被遍历。

for...of循环：针对所有具有Iterator接口的数据结构。

Iterator遍历时会创建一个指针对象，遍历器本质上也是一个指针对象。调用指针对象的next方法就可以遍历事先给定的数据结构。
<!-- more -->
示例：
![image.png](http://upload-images.jianshu.io/upload_images/1393082-3e6b507df516df71.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

原生具有Iterator接口的数据结构（3类）：
数组、某些类似数组的对象、Set和Map结构。
此类数据结构可以直接被for...of循环遍历。
原因：具有Symbol.iterator属性（称部署了遍历器接口）

除以上三类之外的其他数据结构（主要是对象）的Iterator接口，都需要自己在Symbol.iterator属性上面部署，这样才会被for...of循环遍历。


Symbol.iterator属性：本身是一个函数，就是当前数据结构默认的遍历器生成函数。执行这个函数，就会返回一个遍历器对象（特征是具有next方法）。

调用Iterator接口（即Symbol.iterator方法）的场合：
1. 解构赋值；
2. 扩展运算符（...）
3. yield*
4. 任何接受数组作为参数的场合：
>1. for...of
>2. Array.from()
>3. Map(), Set(), WeakMap(), WeakSet()（比如new >>4. Map([['a',1],['b',2]])）
>5. Promise.all()
>6. Promise.race()

遍历器对象除了具有三个方法：
1. next方法(返回的是一个对象)；
2. return方法（可选）；
>1. 作用：for...of循环提前退出（出错，或者有break语句或continue语句）
>2. return方法必须返回一个对象
3. throw方法（可选）。
>1. 作用：抛出错误
>2. throw方法主要是配合Generator函数使用。

for...of循环：
可以遍历数组、Set 和 Map 结构、某些类似数组的对象（比如arguments对象、DOM NodeList 对象）、后文的 Generator 对象，以及字符串。

for...of循环解析：
1. 遍历返回的是成员的值
2. 遍历所有部署了Symbol.iterator属性（即被视为具有iterator接口）的数据结构
3. for...of循环内部调用的是数据结构的Symbol.iterator方法（本质上是调用这个接口产生的遍历器）
4. 是遍历所有数据结构的统一的方法。

循环方法比较：
1. for...in：只能获得键名，不能直接获取键值
>1. 缺点1：数组的键名是数字，但是for...in循环是以字符串作为键名“0”、“1”、“2”等等
>2. 缺点2：for...in循环不仅遍历数字键名，还会遍历手动添加的其他键，甚至包括原型链上的键。
>3. 缺点3：某些情况下，for...in循环会以任意顺序遍历键名。
>4. 总结：for...in循环主要是为遍历对象而设计的，不适用于遍历数组。
2. for...of：允许遍历获得键值，
>1. 语法简洁，但是没有for...in那些缺点。
>2. 可以与break、continue和return配合使用。
>3. 提供了遍历所有数据结构的统一操作接口。
>4. 获取数组的索引，可以借助数组实例的entries方法和keys方法，
>>1. entries() 返回一个遍历器对象，用来遍历[键名, 键值]组成的数组。
>>2. keys() 返回一个遍历器对象，用来遍历所有的键名。
>>3. values() 返回一个遍历器对象，用来遍历所有的键值。
>5. 注意：数组的遍历器接口只返回具有数字索引的属性（即不返回手动另加的其他键，例：arr.foo = 'hello';）
3. 数组的foreach：无法中途跳出forEach循环。


# 十六. Generator 函数
概念：
1. Generator 函数是 ES6 提供的一种异步编程解决方案。
2. 从语法上，首先可以把它理解成，Generator 函数是一个状态机，封装了多个内部状态。
>1. 执行 Generator 函数会返回一个遍历器对象
>2. 即Generator 函数还是一个遍历器对象生成函数
>3. 返回的遍历器对象，可以依次遍历 Generator 函数内部的每一个状态。
3. 形式上，Generator 函数是一个普通函数，但是有两个特征。

Generator 函数的形式特征：
1. function关键字与函数名之间有一个星号；
> 默认写法是星号紧跟在function关键字后面
2. 函数体内部使用yield表达式，定义不同的内部状态。


Generator 函数的调用：
1. 调用 Generator 函数，返回一个遍历器对象，代表 Generator 函数的内部指针。
2. 以后，每次调用遍历器对象的next方法，就会返回一个有着value和done两个属性的对象。
>1. value属性表示当前的内部状态的值，是yield表达式后面那个表达式的值；
>2. done属性是一个布尔值，表示是否遍历结束。

示例：
![image.png](http://upload-images.jianshu.io/upload_images/1393082-aea8e197f02964e0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

遍历器对象的next方法的运行逻辑：
1. 遇到yield表达式就暂停执行，返回一个对象：value值为yield后面那个表达式的值。
2. 下一次调用next方法时，再继续往下执行，直到遇到下一个yield表达式。
3. 如果没有再遇到新的yield表达式，就一直运行到函数结束，直到return语句为止，返回一个对象：value值为return语句后面那个表达式的值。
4. 如果该函数没有return语句，则返回的对象的value属性值为undefined。
>1. 注意：一个函数里面，只能执行一次（或者说一个）return语句。
>2. 注意：Generator 函数可以不用yield表达式，这时就变成了一个单纯的暂缓执行函数（只有调用next方法时函数才会执行）。
>3. 注意：yield表达式只能用在 Generator 函数里面



![image.png](http://upload-images.jianshu.io/upload_images/1393082-2c6b3ec9826e1ef7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


Generator 函数执行后，返回一个遍历器对象。该对象本身也具有Symbol.iterator属性，执行后返回自身：
![image.png](http://upload-images.jianshu.io/upload_images/1393082-3781267463917721.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

next方法可以带一个参数：
该参数就会被当作上一个yield表达式的返回值。
>1. 原因：yield表达式本身没有返回值，或者说总是返回undefined
>2. 作用：通过next方法的参数，可以在 Generator 函数运行的不同阶段，从外部向内部注入不同的值，从而调整函数行为。
>3. 想要第一次调用next方法时，就能够输入值的方法：在 Generator 函数外面再包一层。


for...of循环可以自动遍历 Generator 函数时生成的Iterator对象，且此时不再需要调用next方法：
> 注意：一旦next方法的返回对象的done属性为true，for...of循环就会中止，且不包含该返回对象。

Generator 函数返回的遍历器对象，都有一个throw方法：
1. 作用：抛出错误
2. 与Generator函数体内的catch语句配合使用，遍历器对象的throw方法抛出的错误只能被函数体内外部的catch语句捕获。
> 注意：如果 Generator 函数内部和外部，都没有部署try...catch代码块，那么程序将报错，直接中断执行。
3. throw方法可以接受一个参数，该参数会被catch语句接收，建议抛出Error对象的实例。
4. throw方法被捕获以后，会附带执行下一条yield表达式。即会附带执行一次next方法。
> 注意：只要 Generator 函数内部部署了try...catch代码块，那么遍历器的throw方法抛出的错误，不影响下一次遍历。
5. Generator 函数体外抛出的错误，可以在函数体内捕获；反过来，Generator 函数体内抛出的错误，也可以被函数体外的catch捕获

Generator 函数返回的遍历器对象，还有一个return方法：
1. 作用：返回给定的值，并且终结遍历Generator函数
2. 如果return方法调用时，不提供参数，则返回值的value属性为undefined。
3. 如果 Generator 函数内部有try...finally代码块，那么return方法会推迟到finally代码块执行完再执行。


yield*表达式可以用来在一个 Generator 函数里面执行另一个 Generator 函数：
1. 如果yield表达式后面跟的是一个遍历器对象，需要在yield表达式后面加上星号，表明它返回的是一个遍历器对象。这被称为yield*表达式。
2. yield generator（）调用返回一个遍历器对象，yield* generator（）调用返回该遍历器对象的内部值。
>1. yield命令后面如果不加星号，返回的是整个数组,整个字符串......
>2. yield*，返回的是数组的遍历器对象，单个字符......
3. yield*后面的 Generator 函数（没有return语句时），等同于在 Generator 函数内部，部署一个for...of循环。
4. 如果被代理的 Generator 函数有return语句，那么就可以向代理它的 Generator 函数返回数据。
5. yield*命令可以很方便地取出嵌套数组的所有成员。


Generator 函数总是返回一个遍历器，这个遍历器是 Generator 函数的实例，也继承了 Generator 函数的prototype对象上的方法。
` let obj = g(); `
Generator 函数g返回的遍历器obj，是g的实例，而且继承了g.prototype。g返回的总是遍历器对象，而不是this对象。


Thunk 函数：
1. 是自动执行 Generator 函数的一种方法。
2. 编译器的“传名调用”实现，往往是将参数放到一个临时函数之中，再将这个临时函数传入函数体。这个临时函数就叫做 Thunk 函数。
3. 它是“传名调用”的一种实现策略，用来替换某个表达式。


Thunk 函数现在可以用于 Generator 函数的自动流程管理：
1. yield命令用于将程序的执行权移出 Generator 函数，
2. Thunk 函数可以在回调函数里将执行权交还给 Generator 函数。
3. Thunk 函数真正的威力，在于可以自动执行 Generator 函数。
