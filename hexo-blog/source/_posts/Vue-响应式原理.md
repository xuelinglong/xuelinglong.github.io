---
title: Vue-响应式原理
date: 2017-06-25 20:50:04
tags: Vue.js
---

模型层(model)只是普通 JavaScript 对象，修改它则更新视图(view)。

# 如何追踪变化
1. 把一个普通 JavaScript 对象传给 Vue 实例的 data 选项；
2. Vue 将遍历此对象所有的属性；
3. 使用 Object.defineProperty 把这些属性全部转为 getter/setter。

用户看不到 getter/setter，但是在内部它们让 Vue 追踪依赖，在属性被访问和修改时通知变化。

每个组件实例都有相应的 watcher 实例对象，它会在组件渲染的过程中把属性记录为依赖，之后当依赖项的 setter 被调用时，会通知 watcher 重新计算，从而致使它关联的组件得以更新。
<!-- more -->

# 变化检测问题
**Vue 不能检测到对象属性的添加或删除。**
原因： Vue 会在初始化实例时对属性执行 getter/setter 转化过程，所以属性必须在 data 对象上存在才能让 Vue 转换它，这样才能让它是响应的。


Vue 不允许在已经创建的实例上动态添加新的根级响应式属性。

**在实例创建之后添加属性并且让它是响应的**方法：
1. 对于 Vue 实例，可以使用 $set(key, value) 实例方法;
>  vm.$set 实例方法是全局 Vue.set 方法的别名。
2. 对于普通数据对象，可以使用全局方法 Vue.set(object, key, value)
> 可以使用 Vue.set(object, key, value) 方法将响应属性添加到嵌套的对象上。


若使用 Object.assign() 或 _.extend() 方法来向已有对象上添加属性，那么新添加的属性不会触发更新，此时可创建一个新的对象：包含原对象的属性和新添加的属性。


# 声明响应式属性
Vue 不允许动态添加根级响应式属性，所以**必须在初始化实例前声明**根级响应式属性，哪怕只是一个空值:
```
var vm = new Vue({
  data: {
    // 声明 message 为一个空值字符串
    message: ''
  },
  template: '<div>{{ message }}</div>'
})
// 之后设置 `message` 
vm.message = 'Hello!'
```
data 对象就像组件状态的概要，提前声明所有的响应式属性，可以让组件代码在以后重新阅读或其他开发人员阅读时更易于被理解。

# 异步更新队列
Vue **异步**执行 DOM 更新。
只要观察到数据变化，Vue 将开启一个队列，并缓冲在同一事件循环中发生的所有数据改变。
如果同一个 watcher 被多次触发，只会一次推入到队列中（在缓冲时去除重复数据）。然后，在下一个的事件循环“tick”中，Vue 刷新队列并执行实际（已去重的）工作。

示例：
1. 设置 vm.someData = 'new value';
2. 该组件不会立即重新渲染。
3. 当刷新队列时，组件会在事件循环队列清空时的下一个“tick”更新。
4. 在数据变化之后等待 Vue 完成更新 DOM ，可以在数据变化之后立即使用 Vue.nextTick(callback) 。这样回调函数在 DOM 更新完成后就会调用。

在组件内使用 vm.$nextTick() 实例方法特别方便，因为它不需要全局 Vue ，并且回调函数中的 this 将自动绑定到当前的 Vue 实例上.
