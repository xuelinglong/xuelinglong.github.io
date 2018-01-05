---
title: Vue计算属性
date: 2017-06-06 22:21:39
tags: Vue.js
---

# 3.1 计算属性
模板内的表达式是非常便利的，但是它们实际上只用于简单的运算。在模板中放入太多的逻辑会让模板过重且难以维护。因此对于任何复杂逻辑，我们都应当使用**计算属性**。
<!-- more -->
## 基础例子
``` 
    <div id="example">
        <p>Original message: "{{ message }}"</p>
        <p>Computed reversed message: "{{ reversedMessage }}"</p>
                                        <!-- 模板中可以绑定计算属性  -->
    </div>

    <script>
        var vm = new Vue({
            el: '#example',
            data: {
                message: 'Hello'
            },
            computed: {
                // a computed getter       reversedMessage是一个计算属性
                reversedMessage: function () {   
                //我们提供的函数将用作属性 vm.reversedMessage 的 getter 
                // `this` points to the vm instance
                return this.message.split('').reverse().join('')
                }
            }
        })
    </script>
```
Vue 知道 vm.reversedMessage 依赖于 vm.message ，因此当 vm.message 发生改变时，所有依赖于 vm.reversedMessage 的绑定也会更新。

以声明的方式创建的这种依赖关系：计算属性的 getter 是没有副作用，这使得它易于测试和推理。

## 计算缓存 vs Methods
可以通过调用表达式中的 method 来达到同样的效果：
``` 
    <div id="example">
        <p>Original message: "{{ message }}"</p>
       <p>Reversed message: "{{ reversedMessage() }}"</p>
    </div>

    <script>
        var vm = new Vue({
            el: '#example',
            data: {
                message: 'Hello'
            },
            // in component
            methods: {
                reversedMessage: function () {
                    return this.message.split('').reverse().join('')
                 }
            }
        })
    </script>
```

不同点：
计算属性是**基于它们的依赖进行缓存的**。
计算属性只有在它的相关依赖发生改变时才会重新求值。
即只要 message 还没有发生改变，多次访问 reversedMessage 计算属性会立即返回之前的计算结果，而不必再次执行函数。
只要发生重新渲染，method 调用总会执行该函数（method没有缓存）。

## computed属性vs Watched属性
watch 属性：是Vue 提供的一种更通用的来观察和响应 Vue 实例上的数据变动的方式

当你有一些数据需要随着其它数据变动而变动时，更好的想法是使用 computed 属性而不是命令式的 watch 回调：
```
    <div id="demo">{{ fullName }}</div>
    <script>
        var vm = new Vue({
            el: '#demo',
            data: {
                firstName: 'Foo',
                lastName: 'Bar',
            //  fullName: 'Foo Bar'
            },
            // watch: {
            //     firstName: function (val) {
            //     this.fullName = val + ' ' + this.lastName
            //     },
            //     lastName: function (val) {
            //     this.fullName = this.firstName + ' ' + val
            //     }
            // }   //命令式和重复的代码

            computed: {
                fullName: function () {
                return this.firstName + ' ' + this.lastName
                }
            }
        })    
    </script>
```

## 计算setter
计算属性默认只有 getter ，不过在需要时你也可以提供一个 setter ：
```
<div id="demo">{{ fullName }}</div>
    <script>
        var vm = new Vue({
            el: '#demo',
            data: {
                firstName: 'Foo',
                lastName: 'Bar',
            },
            computed: {
                fullName:{
                    get: function () {
                        return this.firstName + ' ' + this.lastName
                    },
                    set: function (newValue) {
                        var names = newValue.split(' ') 
                        //把字符串以 空格 分为字符串数组
                        this.firstName = names[0]
                        this.lastName = names[names.length - 1]
                    }
                } 
            }
        })    
    </script>
```
现在在运行 vm.fullName = 'John Doe' 时， setter 会被调用， vm.firstName 和 vm.lastName 也相应地会被更新。

## 观察Watchers
Vue 提供一个更通用的方法通过 watch 选项，来响应数据的变化。

但是当我们想要在数据变化响应时，执行异步操作或开销较大的操作，就需要一个自定义的 watcher 。


使用 watch 选项允许我们执行异步操作（访问一个 API），限制我们执行该操作的频率，并在我们得到最终结果前，设置中间状态。这是计算属性无法做到的。
