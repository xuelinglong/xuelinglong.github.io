---
title: Reflect
date: 2017-05-27 23:08:31
tags:
---

# Reflect
## 一. 概述
Reflect对象与Proxy对象一样，也是 ES6 为了操作对象而提供的新 API。Reflect对象的设计目的有这样几个：
1.  将Object对象的一些明显属于语言内部的方法（比如Object.defineProperty），放到Reflect对象上。现阶段，某些方法同时在Object和Reflect对象上部署，未来的新方法将只部署在Reflect对象上。也就是说，从Reflect对象上可以拿到语言内部的方法。
<!-- more -->
2. 修改某些Object方法的返回结果，让其变得更合理。比如，Object.defineProperty(obj, name, desc)在无法定义属性时，会抛出一个错误，而Reflect.defineProperty(obj, name, desc)则会返回false。

3. 让Object操作都变成函数行为。某些Object操作是命令式，比如name in obj和delete obj[name]，而Reflect.has(obj, name)和Reflect.deleteProperty(obj, name)让它们变成了函数行为。
` Reflect.has(Object, 'assign') // true `

4. Reflect对象的方法与Proxy对象的方法一一对应，只要是Proxy对象的方法，就能在Reflect对象上找到对应的方法。这就让Proxy对象可以方便地调用对应的Reflect方法，完成默认行为，作为修改行为的基础。也就是说，不管Proxy怎么修改默认行为，你总可以在Reflect上获取默认行为。

每一个Proxy对象的拦截操作（get、delete、has），内部都调用对应的Reflect方法，保证原生行为能够正常执行。添加的工作，就是将每一个操作输出一行日志。

## 二. 静态方法（13个）
Reflect不是构造函数， 要使用的时候直接通过Reflect.method()调用。

这些方法的作用，大部分与Object对象的同名方法的作用都是相同的，而且它与Proxy对象的方法一一对应,它使得我们可以直接操纵对象的原生行为
### 1. Reflect.apply(target,thisArg,args)
Reflect.apply方法等同于Function.prototype.apply.call(func, thisArg, args)，用于绑定this对象后执行给定函数。
三个参数：
1. func：需要执行的函数；
2. thisArg：需要执行函数的上下文this；
3. args：一个数组或者伪数组，会作为执行函数的参数。

要执行一个函数f，并给它传一组参数args， 还要绑定this，就用Reflect：
` Reflect.apply(f, obj, args) `

` const ages = [11, 33, 12, 54, 18, 96];
// 旧写法
const oldest = Math.max.apply(Math, ages);
const type = Object.prototype.toString.call(youngest);
// 新写法
const oldest = Reflect.apply(Math.max, Math, ages);
const type = Reflect.apply(Object.prototype.toString, youngest, []); `
### 2. Reflect.construct(target,args)
Reflect.construct方法等同于new target(...args)，这提供了一种不使用new，来调用构造函数的方法。
` function Greeting(name) {
  this.name = name;
}
// new 的写法
const instance = new Greeting('张三');
// Reflect.construct 的写法
const instance = Reflect.construct(Greeting, ['张三']); `

通过不确定长度的参数实例化一个构造函数：
` var obj = Reflect.construct(F, args) `

### 3. Reflect.get(target,name,receiver)
Reflect.get方法查找并返回target对象的name属性，如果没有该属性，则返回undefined。
` var myObject = {
  foo: 1,
  bar: 2,
  get baz() {
    return this.foo + this.bar;
  },
}
Reflect.get(myObject, 'foo') // 1 `

如果name属性部署了读取函数（getter），则读取函数的this绑定receiver。
` var myObject = {
  foo: 1,
  bar: 2,
  get baz() {
    return this.foo + this.bar;
  },
};
var myReceiverObject = {
  foo: 4,
  bar: 4,
};
Reflect.get(myObject, 'baz', myReceiverObject) // 8 `

如果第一个参数不是对象，Reflect.get方法会报错。

### 4. Reflect.set(target,name,value,receiver)
Reflect.set方法设置target对象的name属性等于value。
` var myObject = {
  foo: 1,
  set bar(value) {
    return this.foo = value;
  },
}
myObject.foo         // 1
Reflect.set(myObject, 'foo', 2);
myObject.foo         // 2 `

如果name属性设置了赋值函数，则赋值函数的this绑定receiver。
` var myObject = {
  foo: 1,
  set bar(value) {
    return this.foo = value;
  },
};
var myReceiverObject = {
  foo: 0,
};
Reflect.set(myObject, 'bar', 2, myReceiverObject);
myObject.foo // 1
myReceiverObject.foo // 2 `
注意，Reflect.set会触发Proxy.defineProperty拦截。

### 5. Reflect.defineProperty(target,name,desc)
Reflect.defineProperty方法基本等同于Object.defineProperty，用来为对象定义属性。未来，Object.defineProperty会被逐渐废除。
` function MyDate() {
  /*…*/
}
// 旧写法
Object.defineProperty(MyDate, 'now', {
  value: () => Date.now()
});
// 新写法
Reflect.defineProperty(MyDate, 'now', {
  value: () => Date.now()
}); `
如果Reflect.defineProperty的第一个参数不是对象，就会抛出错误，比如Reflect.defineProperty(1, 'foo')。

### 6. Reflect.deleteProperty(target,name)
Reflect.deleteProperty方法等同于delete obj[name]，用于删除对象的属性。
该方法返回一个布尔值。如果删除成功，或者被删除的属性不存在，返回true；删除失败，被删除的属性依然存在，返回false。
` const myObj = { foo: 'bar' };
delete myObj.foo;  //true
Reflect.deleteProperty(myObj, 'foo'); // true`

### 7. Reflect.has(target,name)
Reflect.has方法对应name in obj里面的in运算符。
` var myObject = {
  foo: 1,
};
'foo' in myObject // true 
Reflect.has(myObject, 'foo') // true
Reflect.has(myObject, 'bar')     //false `
如果第一个参数不是对象，Reflect.has和in运算符都会报错。

### 8. Reflect.ownKeys(target)
Reflect.ownKeys方法用于返回对象的所有属性，基本等同于Object.getOwnPropertyNames与Object.getOwnPropertySymbols之和。
` var myObject = {
  foo: 1,
  bar: 2,
  ` ` [Symbol.for('baz')]: 3,
  [Symbol.for('bing')]: 4,  ` `
};
// 旧写法
Object.getOwnPropertyNames(myObject)
// ['foo', 'bar']
Object.getOwnPropertySymbols(myObject)
//[Symbol.for('baz'), Symbol.for('bing')]
// 新写法
Reflect.ownKeys(myObject)
// ['foo', 'bar', Symbol.for('baz'), Symbol.for('bing')] `

### 9. Reflect.isExtensible(target)
Reflect.isExtensible方法对应Object.isExtensible，返回一个布尔值，表示当前对象是否可扩展。
`const myObject = {};
// 旧写法
Object.isExtensible(myObject) // true
// 新写法
Reflect.isExtensible(myObject) // true  `

如果参数不是对象，Object.isExtensible会返回false，因为非对象本来就是不可扩展的，而Reflect.isExtensible会报错。
` Object.isExtensible(1) // false
Reflect.isExtensible(1) // 报错 `
### 10. Reflect.preventExtensions(target)
Reflect.preventExtensions对应Object.preventExtensions方法，用于让一个对象变为不可扩展。它返回一个布尔值，表示是否操作成功
` var myObject = {};
// 旧写法
Object.isExtensible(myObject) // true
// 新写法
Reflect.preventExtensions(myObject) // true `

如果参数不是对象，Object.isExtensible在 ES5 环境报错，在 ES6 环境返回这个参数，而Reflect.preventExtensions会报错。

### 11. Reflect.getOwnPropertyDescriptor(target, name)
Reflect.getOwnPropertyDescriptor基本等同于Object.getOwnPropertyDescriptor，用于得到指定属性的描述对象，将来会替代掉后者。
` var myObject = {};
Object.defineProperty(myObject, 'hidden', {
  value: true,
  enumerable: false,
});
// 旧写法
var theDescriptor = Object.getOwnPropertyDescriptor(myObject, 'hidden');
// 新写法
var theDescriptor = Reflect.getOwnPropertyDescriptor(myObject, 'hidden'); `

Reflect.getOwnPropertyDescriptor和Object.getOwnPropertyDescriptor的一个区别是，如果第一个参数不是对象，Object.getOwnPropertyDescriptor(1, 'foo')不报错，返回undefined，而Reflect.getOwnPropertyDescriptor(1, 'foo')会抛出错误，表示参数非法。

### 12. Reflect.getPrototypeOf(target)
Reflect.getPrototypeOf方法用于读取对象的__proto__属性，对应Object.getPrototypeOf(obj)。
` const myObj = new FancyThing();
// 旧写法
Object.getPrototypeOf(myObj) === FancyThing.prototype;
// 新写法
Reflect.getPrototypeOf(myObj) === FancyThing.prototype; `

Reflect.getPrototypeOf和Object.getPrototypeOf的一个区别是，如果参数不是对象，Object.getPrototypeOf会将这个参数转为对象，然后再运行，而Reflect.getPrototypeOf会报错。
` Object.getPrototypeOf(1) // Number {[[PrimitiveValue]]: 0}
Reflect.getPrototypeOf(1) // 报错 `

### 13. Reflect.setPrototypeOf(target, prototype)
Reflect.setPrototypeOf方法用于设置对象的__proto__属性，返回第一个参数对象，对应Object.setPrototypeOf(obj, newProto)。
` const myObj = new FancyThing();
// 旧写法
Object.setPrototypeOf(myObj, OtherThing.prototype);
// 新写法
Reflect.setPrototypeOf(myObj, OtherThing.prototype); `

如果第一个参数不是对象，Object.setPrototypeOf会返回第一个参数本身，而Reflect.setPrototypeOf会报错。
` Object.setPrototypeOf(1, {})
// 1
Reflect.setPrototypeOf(1, {})
// TypeError: Reflect.setPrototypeOf called on non-object `

如果第一个参数是undefined或null，Object.setPrototypeOf和Reflect.setPrototypeOf都会报错。
` Object.setPrototypeOf(null, {})
// TypeError: Object.setPrototypeOf called on null or undefined
Reflect.setPrototypeOf(null, {})
// TypeError: Reflect.setPrototypeOf called on non-object `

# 三. 使用Proxy实现观察者模式
观察者模式（Observer mode）指的是函数自动观察数据对象，一旦对象有变化，函数就会自动执行。
