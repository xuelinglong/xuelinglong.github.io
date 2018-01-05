---
title: Class
date: 2017-05-27 23:20:31
tags:
---

# Class基本语法
### 概述
JavaScript语言的传统方法是通过构造函数，定义并生成新对象。
` function Point(x, y) {
    this.x = x;
    this.y = y;
  }
Point.prototype.toString = function () {
  return '(' + this.x + ', ' + this.y + ')';
};
var p = new Point(1, 2);
p       // Point {x: 1, y: 2} `
<!-- more -->

ES6提供了更接近传统语言的写法，引入了Class（类）这个概念，作为对象的模板。通过class关键字，可以定义类。基本上，ES6的class可以看作只是一个语法糖，它的绝大部分功能，ES5都可以做到，新的class写法只是让对象原型的写法更加清晰、更像面向对象编程的语法而已。上面的代码用ES6的“类”改写，就是下面这样。
` //定义类
class Point {
  constructor(x, y) {
    this.x = x;
    this.y = y;
  }
  toString() {
    return '(' + this.x + ', ' + this.y + ')';
  }
} `
上面代码定义了一个“类”，里面有一个constructor方法，这就是构造方法，而this关键字则代表实例对象。也就是说，ES5的构造函数Point，对应ES6的Point类的构造方法。

Point类除了构造方法，还定义了一个toString方法。
注意：
定义“类”的方法的时候，前面不需要加上function这个关键字，直接把函数定义放进去了就可以了。
另外，方法之间不需要逗号分隔，加了会报错。

ES6的类，完全可以看作构造函数的另一种写法。
` class Point {
  // ...
}
typeof Point     // "function"        类的数据类型就是函数
Point === Point.prototype.constructor    // true   类本身就指向构造函数。 ` 

使用的时候，也是直接对类使用new命令，跟构造函数的用法完全一致。
` class Bar {
  doStuff() {             //类的方法
    console.log('stuff');
  }
}
var b = new Bar();
b.doStuff()     // "stuff" `

构造函数的prototype属性，在ES6的“类”上面继续存在。事实上，类的所有方法都定义在类的prototype属性上面。
` class Point {
      constructor(){             //构造方法
      // ...
     }
    toString(){                     //定义的类的第一个方法：toString
      // ...
    }
    toValue(){                     //定义的类的第二个方法：toValue
      // ...
    }
  }
// 等同于
Point.prototype = {
  toString(){},
  toValue(){}
};  `

在类的实例上面调用方法，其实就是调用原型上的方法。
` class B {}
let b = new B();
b.constructor === B.prototype.constructor      // true `
b是B类的实例，它的constructor方法就是B类原型的constructor方法。

由于类的方法都定义在prototype对象上面，所以类的新方法可以添加在prototype对象上面。Object.assign方法可以很方便地一次向类添加多个方法。
` class Point {
  constructor(){
    // ...
  }
}
Object.assign(Point.prototype, {
  toString(){},
  toValue(){}
}); `

prototype对象的constructor属性，直接指向“类”的本身(同ES5)：
` Point.prototype.constructor === Point   // true `

另外，类的内部所有定义的方法，都是不可枚举的（与ES5不同）。
` class Point {
  constructor(x, y) {              //构造方法
    // ...
  }
  toString() {           //类的方法
    // ...
  }
}
Object.keys(Point.prototype)
// []
Object.getOwnPropertyNames(Point.prototype)
// ["constructor","toString"] `

ES5中类的内部所有定义的方法，都是可枚举的：
` var Point = function (x, y) {
  // ...
};
Point.prototype.toString = function() {
  // ...
};
Object.keys(Point.prototype)
// ["toString"]
Object.getOwnPropertyNames(Point.prototype)
// ["constructor","toString"]  `

类的属性名，可以采用表达式。
` let methodName = "getArea";
class Square{
  constructor(length) {
    // ...
  }
  [methodName]() {
    // ...
  }
}  ` 
上面代码中，Square类的方法名getArea，是从表达式得到的。

### constructor方法
constructor方法是类的默认方法，通过new命令生成对象实例时，自动调用该方法。一个类必须有constructor方法，如果没有显式定义，一个空的constructor方法会被默认添加。
` constructor() {} `

constructor方法默认返回实例对象（即this），完全可以指定返回另外一个对象。
` class Foo {
  constructor() {
    return Object.create(null);
  }
}
new Foo() instanceof Foo
// false `
上面代码中，constructor函数返回一个全新的对象，结果导致实例对象不是Foo类的实例。

类的构造函数，不使用new是没法调用的，会报错。这是它跟普通构造函数的一个主要区别，后者不用new也可以执行。
` class Foo {
  constructor() {
    return Object.create(null);
  }
}
Foo()
// TypeError: Class constructor Foo cannot be invoked without 'new' 
 `

### 类的实例对象
生成类的实例对象的写法，也是使用new命令。
` var point = new Point(2, 3);     // 正确      `
生成类的实例对象的写法，与ES5完全一样，也是使用new命令。
` var point = Point(2, 3);    // 报错     `

与ES5一样，实例的属性除非显式定义在其本身（即定义在this对象上），否则都是定义在原型上（即定义在class上）。
` //定义类
class Point {
  constructor(x, y) {          //x和y都是实例对象point自身的属性
    this.x = x;         //因为x和y都定义在this变量上
    this.y = y;         //所以hasOwnProperty方法返回true
  }
  toString() {       //toString是原型对象的属性,因为定义在Point类上
    return '(' + this.x + ', ' + this.y + ')';     
  }      //所以 toString属性的hasOwnProperty方法返回false
}
var point = new Point(2, 3);
point.toString() // (2, 3)
point.hasOwnProperty('x') // true
point.hasOwnProperty('y') // true
point.hasOwnProperty('toString') // false
point.__proto__.hasOwnProperty('toString') // true `

与ES5一样，类的所有实例共享一个原型对象。
` var p1 = new Point(2,3);
  var p2 = new Point(3,2);
  p1.__proto__ === p2.__proto__
  //true       `
p1和p2都是Point的实例，它们的原型都是Point.prototype，所以__proto__属性是相等的。

这也意味着，可以通过实例的__proto__属性为Class添加方法。
` var p1 = new Point(2,3);
var p2 = new Point(3,2);
p1.__proto__.printName = function () { return 'Oops' };
p1.printName() // "Oops"
p2.printName() // "Oops"
var p3 = new Point(4,2);
p3.printName() // "Oops"   `
上面代码在p1的原型上添加了一个printName方法，由于p1的原型就是p2的原型，因此p2也可以调用这个方法。而且，此后新建的实例p3也可以调用这个方法。
所以不要轻易使用实例的__proto__属性改写原型，因为这会改变Class的原始定义，影响到所有实例。

### 不存在变量提升
变量提升：就是把定义在后面的东东（变量或函数）提升到前面中定义。
` var v='Hello World';
(function(){
    //变量提升：此处相当于有一句 var v;
    alert(v);
    var v='I love you';
})()            //undefined `

Class不存在变量提升（hoist）。
` new Foo(); // ReferenceError
class Foo {} `
Foo类使用在前，定义在后，这样会报错，因为ES6不会把类的声明提升到代码头部。

Class不存在变量提升的原因：
        这种规定的原因与下文要提到的继承有关，必须保证子类在父类之后定义。
` {
  let Foo = class {};
  class Bar extends Foo {
  }
} `
上面的代码不会报错，因为Bar继承Foo的时候，Foo已经有定义了。但是，如果存在class的提升，上面代码就会报错，因为class会被提升到代码头部，而let命令是不提升的，所以导致Bar继承Foo的时候，Foo还没有定义。

### Class表达式
与函数一样，类也可以使用表达式的形式定义。
` const MyClass = class Me {     //使用表达式定义了一个类
      getClassName() {
        return Me.name;
      }
  };  `
注意：这个类的名字是MyClass而不是Me，Me只在Class的内部代码可用，指代当前类。
` let inst = new MyClass();
  inst.getClassName() // Me
  Me.name // ReferenceError: Me is not defined `
上面代码表示，Me只在Class内部有定义。

如果类的内部没用到的话，可以省略Me，也就是可以写成下面的形式。
` const MyClass = class { /* ... */ }; `

采用Class表达式，可以写出立即执行的Class。 
` let person = new class {
  constructor(name) {
    this.name = name;
  }
  sayName() {
    console.log(this.name);
  }
}('张三');
person.sayName(); // "张三"  `
person是一个立即执行的类的实例。

### 私有方法 
私有方法是常见需求，但 ES6 不提供，只能通过变通方法模拟实现。
一种做法是在命名上加以区别（方法名前面加下划线）：
` class Widget {
  // 公有方法
  foo (baz) {
    this._bar(baz);
  }
  // 私有方法
  _bar(baz) {
    return this.snaf = baz;
  }
  // ...
} `
但是，这种命名是不保险的，在类的外部，还是可以调用到这个方法。

另一种方法就是索性将私有方法移出模块，因为模块内部的所有方法都是对外可见的。
` class Widget {
  foo (baz) {
    bar.call(this, baz);
  }
  // ...
}
function bar(baz) {
  return this.snaf = baz;
} `
foo是公有方法，内部调用了bar.call(this, baz)。这使得bar实际上成为了当前模块的私有方法。

还有一种方法是利用Symbol值的唯一性，将私有方法的名字命名为一个Symbol值。
` const bar = Symbol('bar');
const snaf = Symbol('snaf');
export default class myClass{
  // 公有方法
  foo(baz) {
    this[bar](baz);
  }
  // 私有方法
  [bar](baz) {
    return this[snaf] = baz;
  }
  // ...
}; `
bar和snaf都是Symbol值，导致第三方无法获取到它们，因此达到了私有方法和私有属性的效果。

### this的指向
类的方法内部如果含有this，它默认指向类的实例。但是，必须非常小心，一旦单独使用该方法，很可能报错。
` class Logger {
  printName(name = 'there') { 
    this.print(`Hello ${name}`);
  }
  print(text) {
    console.log(text);
  }
}
const logger = new Logger();
const { printName } = logger;
printName(); // TypeError: Cannot read property 'print' of undefined `
printName方法中的this默认指向Logger类的实例。但是，如果将这个方法提取出来单独使用，this会指向该方法运行时所在的环境，因为找不到print方法而导致报错。

这种情况有三种解决方法：
一个比较简单的解决方法是，在构造方法中绑定this，这样就不会找不到print方法了。
` class Logger {
  constructor() {
    this.printName = this.printName.bind(this);
  }
  // ...
} `
另一种解决方法是使用箭头函数。
` class Logger {
  constructor() {
    this.printName = (name = 'there') => {
      this.print(`Hello ${name}`);
    };
  }
  // ...
} `
还有一种解决方法是使用Proxy，获取方法的时候，自动绑定this。
` function selfish (target) {
  const cache = new WeakMap();
  const handler = {
    get (target, key) {
      const value = Reflect.get(target, key);
      if (typeof value !== 'function') {
        return value;
      }
      if (!cache.has(value)) {
        cache.set(value, value.bind(target));
      }
      return cache.get(value);
    }
  };
  const proxy = new Proxy(target, handler);
  return proxy;
}
const logger = selfish(new Logger()); `

### 严格模式 
类和模块的内部，默认就是严格模式，所以不需要使用use strict指定运行模式。只要你的代码写在类或模块之中，就只有严格模式可用。

考虑到未来所有的代码，其实都是运行在模块之中，所以ES6实际上把整个语言升级到了严格模式。

### name属性
由于本质上，ES6的类只是ES5的构造函数的一层包装，所以函数的许多特性都被Class继承，包括name属性。
` class Point {}
Point.name // "Point" `
name属性总是返回紧跟在class关键字后面的类名

# 二. Class的继承
### 基本用法
在ES6中Class之间可以通过extends关键字实现继承：
` class ColorPoint extends Point {} `
上面代码定义了一个ColorPoint类，该类通过extends关键字，继承了Point类的所有属性和方法。但是由于没有部署任何代码，所以这两个类完全一样，等于复制了一个Point类。下面，我们在ColorPoint内部加上代码。
` class ColorPoint extends Point {
  constructor(x, y, color) {
    super(x, y); // 调用父类的constructor(x, y)
    this.color = color;
  }
  toString() {
    return this.color + ' ' + super.toString(); // 调用父类的toString()
  }
} `
上面代码中，constructor方法和toString方法之中，都出现了super关键字，它在这里表示父类的构造函数，用来新建父类的this对象。

子类必须在constructor方法中调用super方法，否则新建实例时会报错。这是因为子类没有自己的this对象，而是继承父类的this对象，然后对其进行加工。如果不调用super方法，子类就得不到this对象。

ES6的继承，实质是先创造父类的实例对象this（所以必须先调用super方法），然后再用子类的构造函数修改this。
` class 子类名 extends 父类名 {
  constructor(x, y, color) {
    super(x, y); // 调用父类的constructor(x, y)
    this.color = color;   //用子类的构造函数修改this
  }
  toString() {
    return this.color + ' ' + super.toString(); // 调用父类的toString()
  }
} `

如果子类没有定义constructor方法，这个方法会被默认添加，代码如下。也就是说，不管有没有显式定义，任何一个子类都有constructor方法。
` constructor(...args) {
  super(...args);
} `

另一个需要注意的地方是，在子类的构造函数中，只有调用super之后，才可以使用this关键字，否则会报错。这是因为子类实例的构建，是基于对父类实例加工，只有super方法才能返回父类实例。

下面是生成子类实例的代码。
` let cp = new ColorPoint(25, 8, 'green');
cp instanceof ColorPoint // true
cp instanceof Point // true `
上面代码中，实例对象cp同时是ColorPoint和Point两个类的实例，这与ES5的行为完全一致。

###　类的prototype属性和__proto__属性
大多数浏览器的ES5实现之中，每一个对象都有__proto__属性，指向对应的构造函数的prototype属性。Class作为构造函数的语法糖，同时有prototype属性和__proto__属性，因此同时存在两条继承链。
1. 子类的__proto__属性，表示构造函数的继承，总是指向父类。
2. 子类prototype属性的__proto__属性，表示方法的继承，总是指向父类的prototype属性。
` class A {
}
class B extends A {
}
B.__proto__ === A // true         //子类B的__proto__属性指向父类A
B.prototype.__proto__ === A.prototype // true 
子类B的prototype属性的__proto__属性指向父类A的prototype属性。`

类的继承模式：
` class A {
}
class B {
}
// B的实例继承A的实例
Object.setPrototypeOf(B.prototype, A.prototype);
const b = new B();
// B的实例继承A的静态属性
Object.setPrototypeOf(B, A);
const b = new B(); `

` Object.setPrototypeOf = function (obj, proto) {
  obj.__proto__ = proto;
  return obj;
} `
 
` Object.setPrototypeOf(B.prototype, A.prototype);
// 等同于
B.prototype.__proto__ = A.prototype; `

` Object.setPrototypeOf(B, A);
// 等同于
B.__proto__ = A; `
这两条继承链，可以这样理解：作为一个对象，子类（B）的原型（__proto__属性）是父类（A）；作为一个构造函数，子类（B）的原型（prototype属性）是父类的实例。
` Object.create(A.prototype);
// 等同于
B.prototype.__proto__ = A.prototype; `

### extends的继承目标
extends关键字后面可以跟多种类型的值。
` class B extends A {
} `
上面代码的A，只要是一个有prototype属性的函数，就能被B继承。由于函数都有prototype属性（除了Function.prototype函数），因此A可以是任意函数。

下面，讨论三种特殊情况：
第一种特殊情况，子类继承Object类。
` class A extends Object {
}
A.__proto__ === Object // true
A.prototype.__proto__ === Object.prototype // true `
这种情况下，A其实就是构造函数Object的复制，A的实例就是Object的实例。

第二种特殊情况，不存在任何继承。
` class A {
}
A.__proto__ === Function.prototype // true
A.prototype.__proto__ === Object.prototype // true `
这种情况下，A作为一个基类（即不存在任何继承），就是一个普通函数，所以直接继承Function.prototype。但是，A调用后返回一个空对象（即Object实例），所以A.prototype.__proto__指向构造函数（Object）的prototype属性。

第三种特殊情况，子类继承null。
` class A extends null {
}
A.__proto__ === Function.prototype // true
A.prototype.__proto__ === undefined // true `
这种情况与第二种情况非常像。A也是一个普通函数，所以直接继承Function.prototype。但是，A调用后返回的对象不继承任何方法，所以它的__proto__指向Function.prototype，即实质上执行了下面的代码。
` class C extends null {
  constructor() { return Object.create(null); }
} `

### Object.getPrototypeOf()
Object.getPrototypeOf方法可以用来从子类上获取父类。
` Object.getPrototypeOf(ColorPoint) === Point
// true `
因此，可以使用这个方法判断，一个类是否继承了另一个类。

### super关键字
super这个关键字，既可以当作函数使用，也可以当作对象使用。在这两种情况下，它的用法完全不同。

第一种情况，super作为函数调用时：
代表父类的构造函数。ES6 要求，子类的构造函数必须执行一次super函数。
` class A {}
class B extends A {
  constructor() {
    super(); //super()代表调用父类的构造函数
  }
} `
注意，super虽然代表了父类A的构造函数，但是返回的是子类B的实例，即super内部的this指的是B，因此super()在这里相当于A.prototype.constructor.call(this)。

` class A {
  constructor() {
    console.log(new.target.name);
  }
}
class B extends A {
  constructor() {
    super();
  }
}
new A() // A
new B() // B `
上面代码中，new.target指向当前正在执行的函数。可以看到，在super()执行时，它指向的是子类B的构造函数，而不是父类A的构造函数。也就是说，super()内部的this指向的是B。

作为函数时，super()只能用在子类的构造函数之中，用在其他地方（例如子类的方法中）就会报错。

第二种情况，super作为对象时：
在普通方法中，指向父类的原型对象；在静态方法中，指向父类。
` class A {
  p() {
    return 2;
  }
}
class B extends A {
  constructor() {
    super();
    console.log(super.p()); // 2  
  }
}
let b = new B(); `
上面代码中，子类B当中的super.p()，就是将super当作一个对象使用。这时，super在普通方法之中，指向A.prototype，所以super.p()就相当于A.prototype.p()。

这里需要注意，由于super指向父类的原型对象，所以定义在父类实例上的方法或属性，是无法通过super调用的。
` class A {
  constructor() {
    this.p = 2;
  }
}
class B extends A {
  get m() {
    return super.p;
  }
}
let b = new B();
b.m // undefined `
p是父类A实例的属性，super.p就引用不到它。

如果属性定义在父类的原型对象(prototype)上，super就可以取到。
` class A {}
A.prototype.x = 2;
class B extends A {
  constructor() {
    super();
    console.log(super.x) // 2
  }
}
let b = new B(); `
 上面代码中，属性x是定义在A.prototype上面的，所以super.x可以取到它的值。

ES6 规定，通过super调用父类的方法时，super会绑定子类的this。
`class A {
  constructor() {
    this.x = 1;
  }
  print() {
    console.log(this.x);
  }
}
class B extends A {
  constructor() {
    super();
    this.x = 2;
  }
  m() {
    super.print(); //调用父类的printf方法，super会绑定子类B的this
  }
}
let b = new B();
b.m() // 2 `
上面代码中，super.print()虽然调用的是A.prototype.print()，但是A.prototype.print()会绑定子类B的this，导致输出的是2，而不是1。

也就是说，实际上执行的是super.print.call(this)。由于绑定子类this，所以如果通过super对某个属性赋值，这时super就是this，赋值的属性会变成子类实例的属性。
` class A {
  constructor() {
    this.x = 1;
  }
}
class B extends A {
  constructor() {
    super();
    this.x = 2;
    super.x = 3;      //等同于给this赋值为3
    console.log(super.x); // undefined   (此时读取的是A.prototype.x)
    console.log(this.x); // 3
  }
}
let b = new B(); `

如果super作为对象，用在静态方法之中，这时super将指向父类，而不是父类的原型对象。
` class Parent {
  static myMethod(msg) {
    console.log('static', msg);
  }
  myMethod(msg) {
    console.log('instance', msg);
  }
}
class Child extends Parent {
  static myMethod(msg) {
    super.myMethod(msg);
  }
  myMethod(msg) {
    super.myMethod(msg);
  }
}
Child.myMethod(1); // static 1
var child = new Child();
child.myMethod(2); // instance 2 `

注意，使用super的时候，必须显式指定是作为函数、还是作为对象使用，否则会报错。
` class A {}
class B extends A {
  constructor() {
    super();
    console.log(super.valueOf() instanceof B); // true
  }
}
let b = new B(); `
super.valueOf()表明super是一个对象，同时，由于super绑定B的this，所以super.valueOf()返回的是一个B的实例。

最后，由于对象总是继承其他对象的，所以可以在任意一个对象中，使用super关键字。
` var obj = {
  toString() {
    return "MyObject: " + super.toString();
  }
};
obj.toString(); // MyObject: [object Object] `

### 实例的__proto__属性
子类实例的__proto__属性的__proto__属性，指向父类实例的__proto__属性。也就是说，子类的原型的原型，是父类的原型。
` var p1 = new Point(2, 3);
var p2 = new ColorPoint(2, 3, 'red');
p2.__proto__ === p1.__proto__ // false
p2.__proto__.__proto__ === p1.__proto__ // true `
ColorPoint继承了Point，导致前者原型的原型是后者的原型。

因此，通过子类实例的__proto__.__proto__属性，可以修改父类实例的行为。
` p2.__proto__.__proto__.printName = function () {
  console.log('Ha');
};
p1.printName() // "Ha"       `
在ColorPoint的实例p2上向Point类添加方法，结果影响到了Point的实例p1。


# 三. 原生构造函数的继承
原生构造函数是指语言内置的构造函数，通常用来生成数据结构。ECMAScript的原生构造函数大致有下面这些。
Boolean()
Number()
String()
Array()
Date()
Function()
RegExp()
Error()
Object()


ES5是先新建子类的实例对象this，再将父类的属性添加到子类上，由于父类的内部属性无法获取，导致无法继承原生的构造函数。比如，Array构造函数有一个内部属性[[DefineOwnProperty]]，用来定义新属性时，更新length属性，这个内部属性无法在子类获取，导致子类的length属性行为不正常。

ES6允许继承原生构造函数定义子类，因为ES6是先新建父类的实例对象this，然后再用子类的构造函数修饰this，使得父类的所有行为都可以继承。
下面是一个继承Array的例子。
` class MyArray extends Array {    
  constructor(...args) {
    super(...args);
  }
}
var arr = new MyArray();
arr[0] = 12;
arr.length // 1
arr.length = 0;
arr[0] // undefined `
上面代码定义了一个MyArray类，继承了Array构造函数，因此就可以从MyArray生成数组的实例。这意味着，ES6可以自定义原生数据结构（比如Array、String等）的子类，这是ES5无法做到的。

上面这个例子也说明，extends关键字不仅可以用来继承类，还可以用来继承原生的构造函数。因此可以在原生数据结构的基础上，定义自己的数据结构。

注意，继承Object的子类，有一个行为差异。
` class NewObj extends Object{
  constructor(){
    super(...arguments);
  }
}
var o = new NewObj({attr: true});
console.log(o.attr === true);  // false `
上面代码中，NewObj继承了Object，但是无法通过super方法向父类Object传参。这是因为ES6改变了Object构造函数的行为，一旦发现Object方法不是通过new Object()这种形式调用，ES6规定Object
构造函数会忽略参数。

# 四. Class的取值函数（getter）和存值函数（setter）
与ES5一样，在Class内部可以使用get和set关键字，对某个属性设置存值函数和取值函数，拦截该属性的存取行为。
` class MyClass {
  constructor() {
    // ...
  }
  get prop() {
    return 'getter';
  }
  set prop(value) {
    console.log('setter: '+value);
  }
}
let inst = new MyClass();
inst.prop = 123;
// setter: 123
inst.prop
// 'getter' `
prop属性有对应的存值函数和取值函数，因此赋值和读取行为都被自定义了。

存值函数和取值函数是设置在属性的descriptor对象上的。
` class CustomHTMLElement {
  constructor(element) {
    this.element = element;
  }
  get html() {
    return this.element.innerHTML;
  }
  set html(value) {
    this.element.innerHTML = value;
  }
}
var descriptor = Object.getOwnPropertyDescriptor(
  CustomHTMLElement.prototype, "html");
"get" in descriptor  // true
"set" in descriptor  // true
上面代码中，存值函数和取值函数是定义在html属性的描述对象上面，这与ES5完全一致。 `

# 五. Class的Generator方法
如果某个方法之前加上星号（*），就表示该方法是一个 Generator 函数。
` class Foo {
  constructor(...args) {
    this.args = args;
  }
  * [Symbol.iterator]() {      //该方法是一个 Generator 函数
    for (let arg of this.args) {
      yield arg;
    }
  }
}
for (let x of new Foo('hello', 'world')) {
  console.log(x);
}
// hello
// world `
Foo类的Symbol.iterator方法前有一个星号，表示该方法是一个 Generator 函数。Symbol.iterator方法返回一个Foo类的默认遍历器，for...of循环会自动调用这个遍历器。

# 六. Class的静态方法
类相当于实例的原型，所有在类中定义的方法，都会被实例继承。如果在一个方法前，加上static关键字，就表示该方法不会被实例继承，而是直接通过类来调用，这就称为“静态方法”。
` class Foo {
  static classMethod() {
    return 'hello';
  }
}
Foo.classMethod() // 'hello' `
Foo类的classMethod方法前有static关键字，表明该方法是一个静态方法，可以直接在Foo类上调用（Foo.classMethod()）
` var foo = new Foo();
foo.classMethod()
// TypeError: foo.classMethod is not a function `
foo是Foo类的实例，如果在实例上调用静态方法，会抛出一个错误，表示不存在该方法。

父类的静态方法，可以被子类继承。
` class Foo {
  static classMethod() {
    return 'hello';
  }
}
class Bar extends Foo {
}
Bar.classMethod(); // 'hello' `
上面代码中，父类Foo有一个静态方法，子类Bar可以调用这个方法。

静态方法也是可以从super对象上调用的。
` class Foo {
  static classMethod() {
    return 'hello';
  }
}
class Bar extends Foo {
  static classMethod() {
    return super.classMethod() + ', too';
  }
}
Bar.classMethod(); `

# 七. Class的静态属性和实例属性
静态属性指的是Class本身的属性，即Class.propname，而不是定义在实例对象（this）上的属性。
` class Foo {
}
Foo.prop = 1;  //为Foo类定义了一个静态属性prop
Foo.prop // 1 `
目前，只有这种写法可行，因为ES6明确规定，Class内部只有静态方法，没有静态属性。 

ES7有一个静态属性的提案，目前Babel转码器支持。
这个提案对实例属性和静态属性，都规定了新的写法。
1. 类的实例属性
类的实例属性可以用等式，写入类的定义之中。
` class MyClass {
  myProp = 42;     //myProp就是MyClass的实例属性
  constructor() {
    console.log(this.myProp); // 42
  }
} `
在MyClass的实例上，可以读取myProp这个属性。

以前，我们定义实例属性，只能写在类的constructor方法里面。
` class ReactCounter extends React.Component {
  constructor(props) {    //构造方法constructor
    super(props);
    this.state = {          //构造方法constructor里面定义了this.state属性
      count: 0
    };
  }
} `

有了新的写法以后，可以不在constructor方法里面定义。
` class ReactCounter extends React.Component {
  state = {
    count: 0
  };
} `
这种写法比以前更清晰。

为了可读性的目的，对于那些在constructor里面已经定义的实例属性，新写法允许直接列出。
` class ReactCounter extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      count: 0
    };
  }
  state;
} `

2. 类的静态属性
类的静态属性只要在上面的实例属性写法前面，加上static关键字就可以了。
` class MyClass {
  static myStaticProp = 42;
  constructor() {
    console.log(MyClass.myStaticProp); // 42
  }
} `
这个新写法大大方便了静态属性的表达。
` // 老写法
class Foo {
}
Foo.prop = 1;
// 新写法
class Foo {
  static prop = 1;
} `
上面代码中，老写法的静态属性定义在类的外部。整个类生成以后，再生成静态属性。这样让人很容易忽略这个静态属性，也不符合相关代码应该放在一起的代码组织原则。另外，新写法是显式声明（declarative），而不是赋值处理，语义更好。

# 八. 类的私有属性
目前，有一个提案，为class加了私有属性。方法是在属性名之前，使用#表示。
` class Point {
  #x;     //#x就表示私有属性x，在Point类之外读取不到该属性
  constructor(x = 0) {
    #x = +x;
  }
  get x() { return #x }   //私有属性与实例的属性可以同名
  set x(value) { #x = +value }
} `

私有属性可以指定初始值，在构造函数执行时进行初始化。
` class Point {
  #x = 0;
  constructor() {
    #x; // 0
  }
} `
之所以要引入一个新的前缀#表示私有属性，而没有采用private关键字，是因为 JavaScript 是一门动态语言，使用独立的符号似乎是唯一的可靠方法，能够准确地区分一种属性是私有属性。另外，Ruby 语言使用@表示私有属性，ES6 没有用这个符号而使用#，是因为@已经被留给了 Decorator。

该提案只规定了私有属性的写法。但是，很自然地，它也可以用来写私有方法。
` class Foo {
  #a;     //私有属性a
  #b;    //私有属性b
  #sum() { return #a + #b; }      ////私有方法sum
  printSum() { console.log(#sum()); }
  constructor(a, b) { #a = a; #b = b; }
} `

# 九. new.target属性
new是从构造函数生成实例的命令。ES6为new命令引入了一个new.target属性，（在构造函数中）返回new命令作用于的那个构造函数。如果构造函数不是通过new命令调用的，new.target会返回undefined，因此这个属性可以用来确定构造函数是怎么调用的。

` function Person(name) {
  if (new.target !== undefined) {
    this.name = name;
  } else {
    throw new Error('必须使用new生成实例');
  }
}
// 另一种写法
function Person(name) {
  if (new.target === Person) {
    this.name = name;
  } else {
    throw new Error('必须使用new生成实例');
  }
}
var person = new Person('张三'); // 正确
var notAPerson = Person.call(person, '张三');  // 报错 `
上面代码确保构造函数只能通过new命令调用。

Class内部调用new.target，返回当前Class。
` class Rectangle {
  constructor(length, width) {
    console.log(new.target === Rectangle);
    this.length = length;
    this.width = width;
  }
}
var obj = new Rectangle(3, 4); // 输出 true `

需要注意的是，子类继承父类时，new.target会返回子类。
利用这个特点，可以写出不能独立使用、必须继承后才能使用的类。
` class Shape {       //Shape类不能被实例化，只能用于继承
  constructor() {
    if (new.target === Shape) {
      throw new Error('本类不能实例化');
    }
  }
}
class Rectangle extends Shape {
  constructor(length, width) {
    super();
    // ...
  }
}
var x = new Shape();  // 报错
var y = new Rectangle(3, 4);  // 正确 `

注意，在函数外部，使用new.target会报错。

# 十. Mixin模式
Mixin模式指的是，将多个类的接口“混入”（mix in）另一个类。
它在ES6的实现如下：
` function mix(...mixins) {
  class Mix {}
  for (let mixin of mixins) {
    copyProperties(Mix, mixin);
    copyProperties(Mix.prototype, mixin.prototype);
  }
  return Mix;
}
function copyProperties(target, source) {
  for (let key of Reflect.ownKeys(source)) {
    if ( key !== "constructor"
      && key !== "prototype"
      && key !== "name"
    ) {
      let desc = Object.getOwnPropertyDescriptor(source, key);
      Object.defineProperty(target, key, desc);
    }
  }
} `
上面代码的mix函数，可以将多个对象合成为一个类。使用的时候，只要继承这个类即可。
` class DistributedEdit extends mix(Loggable, Serializable) {
  // ...
} `
