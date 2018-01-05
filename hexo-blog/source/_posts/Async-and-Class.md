---
title: Async and Class
date: 2017-06-03 21:54:48
tags: 
---

# async函数
含义：async函数就是Generator函数的语法糖。
形式：将*替换成async，将yield替换成await。
async函数对Generator函数的改进：
1. async函数自带执行器，执行时与普通函数一样
> 示例：` var it=asyncXxxxx();     //调用时自动执行输出最后结果 `
2. async表示函数里有异步操作，await表示紧跟在后面的表达式需要等待结果
3. await命令后面可以是Promise对象和原始类型的值
> 注意：yield命令后面只能是Thunk函数或Promise对象。
4. async函数的返回值是 Promise 对象，可以用then方法指定下一步的操作
> 注意：Generator 函数的返回值是 Iterator 对象。
<!-- more -->

用法：
1. async函数可以使用then方法添加回调函数。
>1. 当函数执行的时候，一旦遇到await就会先返回，等到异步操作完成，再接着执行函数体内后面的语句。
>2. 由于async函数返回的是 Promise 对象，可以作为await命令的参数。
>3. async函数内部return语句返回的值，会成为then方法回调函数的参数。
>4. async函数内部抛出错误，会导致返回的 Promise 对象变为reject状态。抛出的错误对象会被catch方法回调函数接收到。

async函数的使用形式：
1. 函数声明
2. 函数表达式
3. 对象的方法
4. Class的方法
5. 箭头函数

async函数返回的 Promise 对象的状态变化：
1. 内部所有await命令后面的Promise对象执行完才会发生状态改变；
2. 内部遇到return语句也会发生状态改变；
3. 内部抛出错误也会发生状态改变；
4. 总结：即只有async函数内部的异步操作执行完才会执行then方法指定的回调函数。

await命令的后面：
1. 正常情况：是一个Promise对象；
>1. 若后面的Promise对象变为reject状态：
>>1. 则reject的参数会被catch方法的回调函数接收到。
>>2. 只要一个await语句后面的 Promise 变为reject，那么整个async函数都会中断执行。
>>>1. 希望前面的异步操作失败不要中断后面的异步操作：
>>>>1. 将第一个await放在try...catch结构里面，那么第二个await都会执行。
>>>>2. await后面的 Promise 对象再跟一个catch方法，处理前面可能出现的错误。
2. 若不是一个Promise对象：会被转成一个Promise对象，并立即resolve。


错误处理：
await后面的异步操作出错等同于async函数返回的 Promise 对象被reject。
防止出错方法：
1. 将其放在try...catch代码块之中
2. 有多个await命令：统一放在try...catch结构中

使用注意点：
1. 最好把await命令放在try...catch代码块中；
2. 多个await命令后面的异步操作，如果不存在继发关系，最好让它们同时触发。
格式示例：` let [foo, bar] = await Promise.all([getFoo(), getBar()]); `
3. await命令只能用在async函数之中；
4. 希望多个请求并发执行时可以使用Promise.all方法。

for await...of
用途：
1. 用于遍历异步的 Iterator 接口；
for...of循环自动调用遍历器的next方法，会得到一个Promise对象。await用来处理这个Promise对象，一旦resolve，就把得到的zhi传入for...of的循环体
2. 但也可以用于同步遍历器；
3. 会展开yield*（yield*语句也可以跟一个异步遍历器）。


# Class
ES6中新增了class关键字来定义类：

![image.png](http://upload-images.jianshu.io/upload_images/1393082-c687d008ee9f25e1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![image.png](http://upload-images.jianshu.io/upload_images/1393082-39149305a3485932.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



![image.png](http://upload-images.jianshu.io/upload_images/1393082-c3c73f6c0465477a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)




![image.png](http://upload-images.jianshu.io/upload_images/1393082-133aa533ff3d9565.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



![image.png](http://upload-images.jianshu.io/upload_images/1393082-2f8f3e4d954e646d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
