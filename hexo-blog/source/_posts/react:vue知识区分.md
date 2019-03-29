---
title: react/vue知识区分
date: 2019-03-29 14:04:19
tags: 面试准备
---

# 知识盲区回顾：类型判断、闭包、Promise与Async/await、react/vue生命周期等区分

## 引用类型和基础类型的含义与区别

基本类型：存放的是数据本身(如str1)，且把一个基本类型(str1)变量赋值给另外一个变量(str2)时，其实是分配了一个新的内存空间(str2的内存空间)，所以改变其中一个变量值(如str1)对另外一个变量(str2)无影响。

引用类型：存放的是数据地址(对象在堆内存中的地址指针)。把一个引用类型(obj1)变量赋值给另外一个变量(obj2)时，其实是复制了obj1中的地址指针赋值给obj2，此时obj1与obj2中的地址指向对内存中的同一个位置，因此改写obj1会同时改变obj2，同理改变obj2也会同时改变obj1。

<!-- more -->

区别：

1. 基本类型的值是一经确定就不可变的；引用类型的值是可变的(可以为引用类型添加属性和方法，或者删除其属性和方法)。
2. 基本类型的比较是值的比较；引用类型的比较是引用的比较(即比较两个对象的堆内存中的地址)
3. 基本类型的变量是存放在栈内存的；引用类型的值是同时保存在栈内存(保存变量标识符、和指向堆内存中该对象的指针)和堆内存()中的对象(引用类型的值是按引用访问的)

## 类型判断

基本类型除null外，都可用typeof得到正确类型；NaN是Number类型，且不等于它自身。

对象除了函数都会显示Object。获得变量的正确类型可使用Object.prototype.toString.call(xxx)。

判断是否为数组还有一种特殊方法是Array.isArray(xxx)。

## 闭包

JavaScript变量作用域分为两种：全局变量、局部变量。

JavaScript中，函数内部可以直接读取全局变量，但是在函数外部无法读取函数内的局部变量。

我们有时需要得到函数内部的局部变量，通常情况下这是不行的，闭包就是为了解决此种情况而产生的变通方法。原理是在函数f1内部再定义一个函数f2，此时f1中的所有局部变量对f2都是可见的，但是f2中的局部变量对f1仍然是不可见的，若把f2作为返回值那么我们就可以在f1外部读取它的内部变量。

```闭包示例
function f1() {
  let n=1;
  function f2() {
    console.log(n);
  };
  return f2;
}
var test = f1();
test(); // 1
```

含义：简单来说闭包是指有权访问另一个函数作用域中的变量的函数。

用途：1.读取函数内部的变量；2.让某些变量的饿值始终保存在内存中。

闭包的特性：

* 函数嵌套函数
* 函数内部可以读取外部的变量
* 参数和变量不会被垃圾回收机制回收

创建闭包的常用方法：在一个函数内部创建另一个函数，同个另一个函数访问这个函数内部的局部变量。

闭包的缺点：常驻内存，会增加内存使用量，使用不当会造成内存泄漏。

闭包的优点：避免全局变量的污染；允许私有成员的存在；可以达到使某个变量常驻内存的目的。

闭包的使用场合：设计私有的方法和变量；创建特权方法用于访问控制；事件处理程序及回调。

## Promise

JavaScript是单线程的，就导致一些耗费时间的操作会阻塞当前运行线程，为了解决这个问题，出现了“同步”和“异步”这两个概念。

“同步”是指在一个任务梯队里面，只有当前一个任务取到返回值才会继续进行下一个任务。

“异步”是指在一个任务梯队里面，前面的函数开始执行时即使未立即拿到返回值，也会进行下一个函数，然后在将来通过回调函数拿到返回值，这就是异步。

回调地狱：当多个异步任务需要顺序执行时，层层回调会造成回调地狱。

Promise：Promise对象内部保存着异步操作的结果，并通过链式调用的方式避免了回调函数层层嵌套的写法。

含义：简单来讲就是一个内部存储着异步操作(未来才会结束)的结果的容器。

特性：

1. Promise函数接收一个函数作为参数，分别为resolve(一个把Promise从pending变为fufilled的函数)、reject(一个把Promise从pending变为rejected的函数)。
2. Promise对象的状态不受外界影响，有三种状态：pending（进行中）、fufilled(已成功)、rejected(已失败)。且只有异步操作的结果能决定当前是哪一种状态。
3. Promise一旦状态改变，就不会再变，任何时候都会得到这个结果。只有两种可能：pending->fufilled、pending->rejected。
4. Promise实例生成后可以使用then方法分别指定resolved状态和rejected状态的回调函数，模式为

```then方法
promise.then(res => {
  resolve函数内容
}, err => {
  rejected函数内容
})
```

缺陷：

1. Promise一旦创建就会立即执行，一经执行，无法中断，除非抛出异常。
2. Promise函数虽然改变了回调地狱的写法，但根本上还是嵌套函数。
3. 在Promise外部无法通过try/catch捕获内部抛出的异常。

Promise我的应用场景：用户使用token登录->登录成功取得账户信息->使用用户名获取用户信息。

```简易Promise例子
export const actions = {
  get:(url, params, cb) => {
    axios.get(url, {params: params}).then(res => {
      let DATA = res.data.data;
      cb && cb(DATA);
    })
  },
  post:(url, params, cb) => {
    axios.post(url, params).then(res => {
      let DATA = res.data;
      cb && cb(DATA);
    })
  }
}
```

## Async函数

Async函数是ES7中新的异步操作方案，它使异步操作变得更加方便，有以下特点：

1. 内置执行器，即可以像普通函数那样执行，AsyncFunName()。
2. 表示内部存在异步操作，await表示紧跟在后面的表达式需要等待结果
3. await函数外面可以是Promise对象和原始类型的值
4. Async函数可看作多个异步操作包装成的一个Promise对象，await命令就是内部then方法的语法糖。

```Promise与Async/await
loginFun(params) {
  axios.post('url',{num: params.num}).then(res => {
    getAccountInfo(res.name);
    getAuthorInfo(res.name);
  })
}

async loginFun(params) {
  try {
    const res = await axios.post('url',{num:params.num})
    await getAccountInfo(res.name);
    await getAuthorInfo(res.name);
  } catch (err) {
    console.log(err);
  }
}
```

## SPA

SPA是单页Web应用，浏览器一开始会加载必需的HTML、css、javascript，之后所有的操作都在这张页面上完成，这一切都由JavaScript控制。

特点：

* 更好的用户体验，让用户在web app感受native app的速度和流畅。
* MVVM：前后端各负其责。
* ajax：业务逻辑在本地操作，数据通过AJAX同步、提交。
* 路由：在URL中采用#号作为当前路由的地址，改变#号后的参数，页面并不会重载。

注意：单页面跳转仅刷新局部资源，公共资源(js、css等)仅需加载一次，常用于PC端官网、购物等网站。

![单页面应用结构图.png](https://upload-images.jianshu.io/upload_images/1393082-31d41cfc4aea51c4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

路由：前端路由目前主要有两种方法，1.利用URL的hash，就是常用的锚点操作。2.利用HTML5的history模式，

## 多页面应用

多页面跳转刷新所有资源，每个公共资源(js、css等)需选择性重新加载，常用于app或客户端等。

![多页面应用结构图.png](https://upload-images.jianshu.io/upload_images/1393082-141f5726b7f53c00.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 组件通信

### 组件通信-react

1.父子组件沟通

> 父组件更新组件状态 –> 通过传递props更新子组件状态。
> 父组件传递回调函数给子组件 –> 子组件调用触发 –> 父组件更新组件状态。

2.兄弟组件沟通

> 通过props传递父组件回调函数。
> 使用context达到沟通效果。

### 组件通信-vue

父传子：父组件v-bind:data=”data”传递值，子组件props获取值，获取到的值可以像data中定义的数据那样使用。

子传父：子组件$emit触发自定义事件，父组件v-on监听子组件触发的事件。

非父子：定义一个空的Vue实例eventBus，第一个组件中通过eventBus.$emit传递，第二个组件创建组件的钩子中通过eventBus.$on监听。

## 生命周期

### 生命周期-vue

八大生命周期：

beforeCreate：可添加loading事件
created：此时data已经初始化了，可结束loading事件，调用异步请求
beforeMount
mounted：挂载元素，获取到DOM节点
beforeUpdate
updated
beforeDestroy：可写一个确认停止时间的确认框
destroyed

* 第一次页面加载时触发 beforeCreate, created, beforeMount, mounted 这几个钩子，data 数据在 created 中可获取到。
* 更新数据时触发 beforeUpdate 和 updated 钩子，且在 beforeUpdate 触发时，数据已更新完毕。
* 销毁完成后，vue不再对组件内元素的改变做出响应。但是原先生成的dom元素还存在，可以理解为执行了destroy操作，后续就不再受vue控制了。

### 生命周期-react

React的组件的生命周期分为三个过程：

装载过程(Mount)：第一次把组件渲染到DOM树的过程；
更新过程(Update)：组件进行渲染更新的过程；
卸载过程(Unmount)：组件从DOM树中删除的过程。

挂载阶段

* constructor()在组件创建阶段调用，且从父组件接收props：constructor(props)。
* componentWillMount() 在组件即将挂载时调用。
* render() 组件渲染至 DOM，必须有返回值，返回null或false表示不渲染任何DOM元素。
* componentDidMount() 在组件挂载完成时调用。

更新阶段

* componentWillReceiveProps(nextProps)在组件进行更新以及父组件render函数（不管数据是否发生了改变）被调用后执行，this.props取得当前的props，nextProps传入的是要更新的props。
* shouldComponentUpdate(nextProps, nextState)：返回布尔值，true表示要更新，false表示不更新。
* componentWillUpdate(nextProps,nextState)：预更新函数。
* render：渲染函数。
* componentDidUpdate(prevProps,PrevState)：更新完成函数。

卸载阶段

* compontentWillUnmount() 组件即将被卸载。

## 组件化设计

组件化设计的要点在于分拆组件，将复杂的UI和逻辑拆分开来，第一是减小开发难度，第二能够更清晰的组织代码，第三是增加复用。

* 将组件尽可能的细分，便于复用和优化。
* 尽量使拆分后的组件更容易判断是否更新。

## 状态管理

* 集中式存储管理应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化。
* 此种方式下集中存储的是全局通用且状态持久的数据，此类状态的变更都会留下记录。
* 此外，每个实例/组件仍然可以拥有和管理自己的私有状态。
* 适用于多个组件共享状态，如果应用比较简单，不建议使用。