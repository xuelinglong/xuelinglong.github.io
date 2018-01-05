---
title: Proxy
date: 2017-05-27 23:06:18
tags:
---

# Proxy
## 一. 概述
Proxy 用于修改某些操作的默认行为，等同于在语言层面做出修改，所以属于一种“元编程”（meta programming），即对编程语言进行编程。

Proxy 可以理解成，在目标对象之前架设一层“拦截”，外界对该对象的访问，都必须先通过这层拦截，因此提供了一种机制，可以对外界的访问进行过滤和改写。Proxy 这个词的原意是代理，用在这里表示由它来“代理”某些操作，可以译为“代理器”。
<!-- more -->
` var obj = new Proxy({}, {
  get: function (target, key, receiver) {
    console.log(`getting ${key}!`);
    return Reflect.get(target, key, receiver);
  },
  set: function (target, key, value, receiver) {
    console.log(`setting ${key}!`);
    return Reflect.set(target, key, value, receiver);
  }
});                                                               
obj.count = 1
obj.count
//setting count!                 
//getting count!
// 1 `
注解：目标对象为空对象（即对空对象架设了一层拦截），重定义了属性的读取（get）和设置（set）行为：
` obj.count = 1           // 设置（set）行为
//  setting count!
obj.count             // 读写（get）行为
//  getting count!
//  1 `
Proxy 实际上重载（overload）了点运算符，即用自己的定义覆盖了语言的原始定义。

ES6 原生提供 Proxy 构造函数，用来生成 Proxy 实例。
格式：` var proxy = new Proxy(target, handler); `
Proxy 对象的所有用法，都是这种格式，不同的只是handler参数的写法。

注解：
new Proxy()表示生成一个Proxy实例。
第一个参数：target参数表示所要拦截的目标对象（即如果没有Proxy的介入，操作原来要访问的就是这个对象），可以是任意类型的对象，比如数组，函数，甚至是另外一个代理对象。
第二个参数：handler参数是一个配置对象，用来定制拦截行为，对于每一个被代理的操作，需要提供一个对应的处理函数，该函数将拦截对应的操作。

` var proxy = new Proxy({}, {
  get: function(target, property) {
    return 35;
  }
});
proxy.time // 35
proxy.name // 35
proxy.title // 35 `
配置对象有一个get方法，用来拦截对目标对象属性的访问请求。get方法的两个参数分别是目标对象和所要访问的属性。

注意：要使得Proxy起作用，必须针对Proxy实例（上例是proxy对象）进行操作，而不是针对目标对象（上例是空对象）进行操作。如果handler没有设置任何拦截，那就等同于直接通向原对象。
将 Proxy 对象，设置到object.proxy属性，从而可以在object对象上调用：
` var object = { proxy: new Proxy(target, handler) }; `

Proxy 实例也可以作为其他对象的原型对象。
` var proxy = new Proxy({}, {
  get: function(target, property) {
    return 35;
  }
});
let obj = Object.create(proxy);
obj.time // 35 `
proxy对象是obj对象的原型，obj对象本身并没有time属性，所以根据原型链，会在proxy对象上读取该属性，导致被拦截。

同一个拦截器函数，可以设置拦截多个操作。
Proxy 支持的拦截操作：
对于可以设置、但没有设置拦截的操作，则直接落在目标对象上，按照原先的方式产生结果。
1. get(target, propKey, receiver)
拦截对象属性的读取，比如proxy.foo和proxy['foo']。
最后一个参数receiver是一个对象，可选，参见下面Reflect.get的部分。

2. set(target, propKey, value, receiver)
拦截对象属性的设置，比如proxy.foo = v或proxy['foo'] = v，返回一个布尔值。

3. has(target, propKey)
拦截propKey in proxy的操作，返回一个布尔值。

4. deleteProperty(target, propKey)
拦截delete proxy[propKey]的操作，返回一个布尔值。

5. ownKeys(target)
拦截Object.getOwnPropertyNames(proxy)、Object.getOwnPropertySymbols(proxy)、Object.keys(proxy)，返回一个数组。该方法返回目标对象所有自身的属性的属性名，而Object.keys()的返回结果仅包括目标对象自身的可遍历属性。

6. getOwnPropertyDescriptor(target, propKey)
拦截Object.getOwnPropertyDescriptor(proxy, propKey)，返回属性的描述对象。

7. defineProperty(target, propKey, propDesc)
拦截Object.defineProperty(proxy, propKey, propDesc）、Object.defineProperties(proxy, propDescs)，返回一个布尔值

8. preventExtensions(target)
拦截Object.preventExtensions(proxy)，返回一个布尔值。

9. getPrototypeOf(target)
拦截Object.getPrototypeOf(proxy)，返回一个对象。

10. isExtensible(target)
拦截Object.isExtensible(proxy)，返回一个布尔值。

11. setPrototypeOf(target, proto)
拦截Object.setPrototypeOf(proxy, proto)，返回一个布尔值。
如果目标对象是函数，那么还有两种额外操作可以拦截。

12. apply(target, object, args)
拦截 Proxy 实例作为函数调用的操作，比如proxy(...args)、proxy.call(object, ...args)、proxy.apply(...)。

13. construct(target, args)
拦截 Proxy 实例作为构造函数调用的操作，比如new proxy(...args)。

## 二. Proxy实例的方法
### get()
用于拦截某个属性的读取操作，如果访问目标对象不存在的属性，会抛出一个错误。如果没有这个拦截函数，访问不存在的属性，只会返回undefined。

get方法可以继承：
` let proto = new Proxy({}, {
  get(target, propertyKey, receiver) {
    console.log('GET '+propertyKey);
    return target[propertyKey];
  }
}); 
let obj = Object.create(proto);
obj.xxx // "GET xxx" `
拦截操作定义在Prototype对象上面，所以如果读取obj对象继承的属性时，拦截会生效。

使用get拦截，可以实现数组读取负数的索引。
利用 Proxy，可以将读取属性的操作（get），转变为执行某个函数，从而实现属性的链式操作。
利用get拦截，实现一个生成各种DOM节点的通用函数dom。
如果一个属性不可配置（configurable）和不可写（writable），则该属性不能被代理，通过 Proxy 对象访问该属性会报错。

### set()
set方法用来拦截某个属性的赋值操作。
set方法可以当做数据验证的一种实现方法，还可以数据绑定，即每当对象发生变化时，会自动更新 DOM。

有时，我们会在对象上面设置内部属性，属性名的第一个字符使用下划线开头，表示这些属性不应该被外部使用。结合get和set方法，就可以做到防止这些内部属性被外部读写。

注意，如果目标对象自身的某个属性，不可写也不可配置，那么set不得改变这个属性的值，只能返回同样的值，否则报错。

### apply()
apply方法拦截函数的调用、call和apply操作。
apply方法可以接受三个参数，分别是目标对象、目标对象的上下文对象（this）和目标对象的参数数组。
` var handler = {
  apply (target, ctx, args) {
    return Reflect.apply(...arguments);
  }
}; `

### has()
has方法用来拦截HasProperty操作，即判断对象是否具有某个属性时，这个方法会生效。典型的操作就是in运算符。

设置如果原对象的属性名的第一个字符是下划线，proxy.has就会返回false，从而隐藏某些属性不会被in运算符发现。
` var handler = {
  has (target, key) {
    if (key[0] === '_') {
      return false;
    }
    return key in target;
  }
};
var target = { _prop: 'foo', prop: 'foo' };
var proxy = new Proxy(target, handler);
'_prop' in proxy // false `

如果原对象不可配置或者禁止扩展，这时has拦截会报错，即如果某个属性不可配置（或者目标对象不可扩展），则has方法就不得“隐藏”（即返回false）目标对象的该属性。

has方法拦截的是HasProperty操作，而不是HasOwnProperty操作，即has方法不判断一个属性是对象自身的属性，还是继承的属性。

has拦截只对in循环生效，对for...in循环不生效！

### construct()
construct方法用于拦截new命令，下面是拦截对象的写法：
` var handler = {
  construct (target, args, newTarget) {
    return new target(...args);
  }
}; `
construct方法可以接受两个参数：target（目标对象）  、args（构建函数的参数对象）
construct方法返回的必须是一个对象，否则会报错。

### deleProperty()
deleteProperty方法用于拦截delete操作，如果这个方法抛出错误或者返回false，当前属性就无法被delete命令删除。

注意，目标对象自身的不可配置（configurable）的属性，不能被deleteProperty方法删除，否则报错。

### defineProperty()
defineProperty方法拦截了Object.defineProperty操作。
注意，如果目标对象不可扩展（extensible），则defineProperty不能增加目标对象上不存在的属性，否则会报错。另外，如果目标对象的某个属性不可写（writable）或不可配置（configurable），则defineProperty方法不得改变这两个设置。

### getOwnPropertyDescriptor()
getOwnPropertyDescriptor方法拦截Object.getOwnPropertyDescriptor()，返回一个属性描述对象或者undefined。
` var handler = {
  getOwnPropertyDescriptor (target, key) {
    if (key[0] === '_') {
      return;          
    }
    return Object.getOwnPropertyDescriptor(target, key);   
  }
};
var target = { _foo: 'bar', baz: 'tar' };
var proxy = new Proxy(target, handler);
Object.getOwnPropertyDescriptor(proxy, 'wat')
// undefined
Object.getOwnPropertyDescriptor(proxy, '_foo')
// undefined
Object.getOwnPropertyDescriptor(proxy, 'baz')   //返回一个属性描述对象
// { value: 'tar', writable: true, enumerable: true, configurable: true } `

### getPrototypeOf()
getPrototypeOf方法主要用来拦截获取对象原型。具体来说，拦截下面这些操作：
1. Object.prototype.__proto__
2. Object.prototype.isPrototypeOf()
3. Object.getPrototypeOf()
4. Reflect.getPrototypeOf()
5. instanceof

getPrototypeOf方法方法拦截Object.getPrototypeOf()，返回proto对象。
` var proto = {};
var p = new Proxy({}, {
  getPrototypeOf(target) {
    return proto;
  }
});
Object.getPrototypeOf(p) === proto // true `
getPrototypeOf方法的返回值必须是对象或者null，否则报错。另外，如果目标对象不可扩展（extensible）， getPrototypeOf方法必须返回目标对象的原型对象。


### isExtensible()
isExtensible方法拦截Object.isExtensible操作。
该方法只能返回布尔值，否则返回值会被自动转为布尔值。
这个方法有一个强限制，它的返回值必须与目标对象的isExtensible属性保持一致，否则就会抛出错误。
` Object.isExtensible(proxy) === Object.isExtensible(target) `

### ownKeys（）
ownKeys方法用来拦截对象自身属性的读取操作。具体来说，拦截以下操作：
Object.getOwnPropertyNames()  //返回的数组成员，只能是字符串或 Symbol 值
Object.getOwnPropertySymbols()
Object.keys()
使用Object.keys方法时有三类属性会被ownKeys方法自动过滤不会返回：
目标对象上不存在的属性
属性名为 Symbol 值
不可遍历（enumerable）的属性

如果目标对象自身包含不可配置的属性，则该属性必须被ownKeys方法返回，否则报错。
如果目标对象是不可扩展的（non-extensition），这时ownKeys方法返回的数组之中，必须包含原对象的所有属性，且不能包含多余的属性，否则报错。

### preventExtensions()
preventExtensions方法拦截Object.preventExtensions()。该方法必须返回一个布尔值，否则会被自动转为布尔值。

这个方法有一个限制，只有目标对象不可扩展时（即Object.isExtensible(proxy)为false），proxy.preventExtensions才能返回true，否则会报错。为了防止出现这个问题，通常要在proxy.preventExtensions方法里面，调用一次Object.preventExtensions：
` var p = new Proxy({}, {
  preventExtensions: function(target) {
    console.log('called');
    Object.preventExtensions(target);
    return true;
  }
});
Object.preventExtensions(p)
// "called"
// true `

### setPrototypeOf()
setPrototypeOf方法主要用来拦截Object.setPrototypeOf方法。

该方法只能返回布尔值，否则会被自动转为布尔值。另外，如果目标对象不可扩展（extensible），setPrototypeOf方法不得改变目标对象的原型。

## 3. Proxy.revocable()
Proxy.revocable方法返回一个可取消的 Proxy 实例。该方法返回一个对象，该对象的proxy属性是Proxy实例，revoke属性是一个函数，可以取消Proxy实例。
Proxy.revocable的一个使用场景是，目标对象不允许直接访问，必须通过代理访问，一旦访问结束，就收回代理权，不允许再次访问。

## 4. This问题
虽然 Proxy 可以代理针对目标对象的访问，但它不是目标对象的透明代理，即不做任何拦截的情况下，也无法保证与目标对象的行为一致。主要原因就是在 Proxy 代理的情况下，目标对象内部的this关键字会指向 Proxy 代理。



