---
title: Vue实例
date: 2017-06-05 20:58:10
tags: Vue.js
---

# vue实例
## 1.1 构造器
```
<div id="app">
          <p>{{ message }}</p>
      </div>          
      <script>
        // Vue.js 应用都通过构造函数 Vue 创建一个 Vue 的根实例 启动：
        var app=new Vue({
            el:'#app',
            data: {
                message:'Hello World!'
            }
        })
      </script>
```
实例化 Vue 时，需要传入一个选项对象，它可以包含数据、模板、挂载元素、方法、生命周期钩子等选项。
<!-- more -->
扩展 Vue 构造器，从而用预定义选项创建可复用的组件构造器：
```
var MyComponent = Vue.extend({
  // 扩展选项
})
// 所有的 `MyComponent` 实例都将以预定义的扩展选项被创建
var myComponentInstance = new MyComponent()
```

所有的 Vue.js 组件其实都是被扩展的 Vue 实例

## 1.2 属性与方法
每个 Vue 实例都会代理其 data 对象里所有的属性：
```
var data = { a: 1 }
var vm = new Vue({
el: '#example',
  data: data      //key:value
})
vm.a === data.a // -> true
// 设置属性也会影响到原始数据
vm.a = 2
data.a // -> 2
// ... 反之亦然
data.a = 3
vm.a // -> 3
vm.$data === data // -> true
vm.$el === document.getElementById('example') // -> true
```
这些被代理的属性（例如：data）是响应的。
除了 data 属性， Vue 实例暴露了一些有用的实例属性与方法。这些属性与方法都有前缀 $。
不要在实例属性或者回调函数中使用箭头函数（this.myMethod 未被定义）。

### 1.3 实例生命周期
Vue 实例在被创建之前的初始化过程中，实例会调用一些 生命周期钩子 ，这就给我们提供了执行自定义逻辑的机会。created、mounted、 updated 、destroyed等钩子，在实例生命周期的不同阶段调用，钩子的 this 指向调用它的 Vue 实例。组件的自定义逻辑可以分布在这些钩子中。
