---
title: Promise+Vuex+Axios
date: 2017-09-18 21:23:46
tags: 查漏补缺
---

目标：
获取豆瓣API的数据：http://api.douban.com/v2/movie/${type}；

# Promise
1. Promise可以理解为**一个里面保存着某个未来才会结束的事件（通常是一个异步操作）的结果**的容器。
2. Promise是一个对象，它可以获取异步操作的消息，并且提供统一的API，各种异步操作可以使用同样的方法处理。
<!-- more -->
## Promise对象
### Promise对象的特点（两个）
1. 对象的状态不受外界影响。
2. 一旦状态改变，就不会再变，任何时候都可以得到这个结果。

### Promise对象的状态
Promise对象代表一个异步操作，有三种状态：pending（进行中）、fulfilled（已成功）和rejected（已失败）。只有异步操作的结果，可以决定当前是哪一种状态。

### Promise对象的状态改变（两种可能）
1. 从pending（进行中）变为fulfilled（已成功）；
2. 从pending（进行中）变为rejected（已失败）；
3. 只要上面两种情况发生，状态就不会再变了，会一直保持这个结果，这时就称为 resolved（已定型）。
4. 如果**改变已经发生后**再对Promise对象添加回调函数，也会立即得到这个结果。
5. **后面的 resolved（已定型）统一只指 fulfilled（已成功）状态，不包含 rejected（已失败）状态。**

### Promise对象的基本用法
1. Promise对象是一个构造函数，用来生成Promise实例。
2. Promise函数接受一个函数作为参数，该函数的两个参数分别是resolve、reject，这（resolve和reject）是两个由JavaScript 引擎提供的函数。
#### resolve函数
resolve函数将Promise对象的状态从“未完成”变为“成功”（即从 pending 变为 resolved）。
resolve函数在**异步操作成功时调用**，并**将异步操作的结果，作为参数**传递出去。

#### reject函数
reject函数将Promise对象的状态从“未完成”变为“失败”（即从 pending 变为 rejected）。
reject方法的作用，等同于抛出错误。
reject函数在**异步操作失败时调用**，并**将异步操作报出的错误，作为参数**传递出去。

#### then方法
##### then方法的作用
Promise实例生成以后，可以用then方法分别指定resolved状态和rejected状态的回调函数。

##### then方法的使用
then方法可以接受两个回调函数作为参数。
1. 第一个回调函数是Promise对象的状态变为resolved时调用；
2. 第二个回调函数是Promise对象的状态变为rejected时调用。（可选，不一定要提供）
3. 这两个函数都接受Promise对象传出的值作为参数。

##### Promise.prototype.then()
###### 含义
Promise 实例具有then方法（即：then方法是定义在原型对象Promise.prototype上的）。
###### 作用
为 Promise 实例添加状态改变时的回调函数。
###### 返回值
then方法返回的是一个新的Promise实例（注意，不是原来那个Promise实例）。
###### 链式写法
then方法返回一个新的Promise实例，因此可以采用链式写法，即then方法后面再调用另一个then方法。

#### catch方法
##### catch方法的作用
catch方法用于指定发生错误时的回调函数。
catch方法会捕获**异步操作抛出错误**和**then方法指定的回调函数运行中抛出的错误**。

### 总结
1. Promise 新建后就会立即执行。
2. 然后，then方法指定的回调函数，将在当前脚本所有同步任务执行完才会执行，所以resolved最后输出。
3. Promise 在resolve语句后面，再抛出错误，不会被捕获，等于没有抛出。（即：Promise状态已经变成resolved后再抛出错误是无效的）
4. Promise 对象的错误具有“冒泡”性质，会一直向后传递，直到被捕获为止。（即：错误总是会被下一个catch语句捕获）
5. 不要在then方法里面定义Reject状态的回调函数（即then的第二个参数），总是使用catch方法。

```
// 推荐写法
promise
  .then(function(data) { //cb
    // success
  })
  .catch(function(err) {
    // error
  });
```
6. 如果没有使用catch方法指定错误处理的回调函数，Promise对象抛出的错误不会传递到外层代码，即不会有任何反应。
7. catch方法返回的还是一个 Promise 对象，因此后面还可以接着调用then方法。catch之后的then方法里面报错，就与前面的catch无关了。
8. catch方法之中，还能再抛出错误。（可以写两个catch方法，第二个catch方法用来捕获前一个catch方法抛出的错误）

# vuex
## State
Vuex 使用 **单一状态树**（即用一个对象就包含了全部的应用层级状态），**这意味着，每个应用将仅仅包含一个 store 实例**。

### 在 Vue 组件中获得 Vuex 状态
Vuex 通过 store 选项，提供了一种机制将状态从根组件『注入』到每一个子组件中（需调用 Vue.use(Vuex)）
```
import store from './vuex/store';

new Vue({
    el: '#app',
    router,
    // 把 store 对象提供给 “store” 选项，这可以把 store 的实例注入所有的子组件
    store,
    template: '<App/>',
    components: { App }
});
```
通过在根实例中注册 store 选项，该 store 实例会注入到根组件下的所有子组件中，且子组件能通过 this.$store 访问到。

### mapState 辅助函数
当一个组件需要获取多个状态时可以使用 mapState 辅助函数帮助我们生成计算属性：
```
import { mapState } from 'vuex';

export default {
  // ...
  computed: mapState({
    count: state => state.count,

    // 传字符串参数 'count' 等同于 `state => state.count`
    countAlias: 'count',
  })
}
```
当映射的计算属性的名称与 state 的子节点名称相同时可以给 mapState 传一个字符串数组。
```
computed: mapState([
  // 映射 this.count 为 store.state.count
  'count'
])
```
mapState 函数返回的是一个对象。

### 小结
1. 单状态树和模块化并不冲突，我们可以将状态和状态变更事件分布到各个子模块中。
2. 组件仍然保有局部状态。

## modules
 store 分割成的每个模块拥有自己的 state、mutation、action、getter、甚至是嵌套子模块。
豆瓣可划分模块movie，book ，music，event......
本次仅写 movie 模块。
```
const moduleA = {
  state: { ... },
  mutations: { ... },
  actions: { ... },
  getters: { ... }
}

const store = new Vuex.Store({
  modules: {
    a: moduleA
  }
})
```

