---
title: Vue模板语法
date: 2017-06-06 22:15:16
tags: Vue.js
---

# 模板语法
Vue.js 使用了基于 HTML 的模版语法，允许开发者声明式地将 DOM 绑定至底层 Vue 实例的数据。所有 Vue.js 的模板都是合法的 HTML ，所以能被遵循规范的浏览器和 HTML 解析器解析。

在底层的实现上， Vue 将模板编译成虚拟 DOM 渲染函数。结合响应系统，在应用状态改变时， Vue 能够智能地计算出重新渲染组件的最小代价并应用到 DOM 操作上。
<!-- more -->
DOM：
1. DOM是一组对象的集合，这些对象代表了HTML文档里的各个元素。
2. DOM就像一个模型，它由代表文档的众多对象组成。
>1. 我们可以用DOM来获取文档信息，也可以对其进行修改。
>2. 显示HTML文档的过程中，浏览器会解析HTML并创建一个模型。
>3. 这个模型保存了各个HTML元素之间的层级关系，每个元素都由一个Javascript对象表示
>4. 模型里的每一个对象都有若干属性和方法。
>5. 当你用它们来修改对象的状态时，浏览器会让这些改动反映到对应的HTML元素上，并更新你的文档。

示例：
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Vue-模板语法</title>       
</head>
<body>
    <p id="apple">
        I am the <span>first</span> unit!
    </p>        
    <p id="orange">
        I am the second unit!
    </p> 
</body>
</html>
```
HTML文档里的元素层级关系：

![image.png](http://upload-images.jianshu.io/upload_images/1393082-20c28f4c76e840de.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 2.1 插值
## 文本
1. 数据绑定最常见的形式就是使用 “Mustache” 语法（双大括号）的文本插值：
` <span>Message: {{ msg }}</span> `
Mustache 标签将会被替代为对应数据对象上 msg 属性的值。无论何时，绑定的数据对象上 msg 属性发生了改变，插值处的内容都会更新。
2. 通过使用 v-once 指令可以执行一次性地插值：
` <span v-once>This will never change: {{ msg }}</span> `
当数据改变时，插值处的内容不会更新。这会影响到该节点上所有的数据绑定。

## 纯HTML
双大括号会将数据解释为纯文本，而非 HTML 。

为了输出真正的 HTML 需要使用 v-html 指令：
` <div v-html="rawHtml"></div> `
被插入的内容都会被当做 HTML —— 数据绑定会被忽略。
注意：不能使用 v-html 来复合局部模板，因为 Vue 不是基于字符串的模板引擎。组件更适合担任 UI 重用与复合的基本单元。

你的站点上动态渲染的任意 HTML 可能会非常危险，因为它很容易导致 XSS 攻击。请只对可信内容使用 HTML 插值，**绝不要**对用户提供的内容插值。

## 属性
Mustache不能在 HTML 属性中使用，应使用 v-bind 指令：
```
 <div v-bind:id="dynamicId"></div>  
<!--将这个元素节点的 id 属性和Vue实例的 dynamicld 属性保持一致-->
```
这对布尔值的属性也有效 —— 如果条件被求值为 false 的话该属性会被移除：
```
<button v-bind:disabled="someDynamicCondition">Button</button>
```

## 使用JavaScript 表达式
对于所有的数据绑定， Vue.js 都提供了完全的 JavaScript 表达式支持。
这些表达式会在所属 Vue 实例的数据作用域下作为 JavaScript 被解析。
有个限制就是，每个绑定都只能包含单个表达式：
```
{{ number + 1 }}
{{ ok ? 'YES' : 'NO' }}
{{ message.split('').reverse().join('') }}
<div v-bind:id="'list-' + id"></div>
```

模板表达式都被放在沙盒中，只能访问全局变量的一个白名单，如 Math 和 Date 。你不应该在模板表达式中试图访问用户定义的全局变量。

# 2.2 指令
指令是带有 v- 前缀的特殊属性。
指令属性的值预期是单一 JavaScript 表达式（除了 v-for，之后再讨论）。指令的职责就是当其表达式的值改变时相应地将某些行为应用到 DOM 上。

## 参数
一些指令能接受一个“参数”，在指令后以冒号指明。
例如：
```
<a v-bind:href="url"></a>
<!--  href 是参数，告知 v-bind 指令将该元素的 href 属性与表达式 url 的值绑定  -->
<a v-on:click="doSomething">
<!--  参数是监听的事件名 -->
```

## 修饰符
修饰符（Modifiers）是以半角句号 . 指明的特殊后缀，用于指出一个指令应该以特殊方式绑定。
v-on 与 v-model指令有很多修饰符的使用，例：
```
<form v-on:submit.prevent="onSubmit"></form>
<!-- .prevent 修饰符告诉 v-on 指令对于触发的事件调用 event.preventDefault() -->
```


## 过滤器
Vue.js 允许我们自定义过滤器，可被用作一些常见的文本格式化。

Vue 2.x 中过滤器只能用在两个地方：mustache 插值和 v-bind 表达式。
过滤器应该被添加在 JavaScript 表达式的尾部，由“管道”符指示：
```
<!-- in mustaches -->
{{ message | capitalize }}
<!-- in v-bind -->
<div v-bind:id="rawId | formatId"></div>
```

过滤器设计目的就是用于文本转换。

过滤器函数总接受**表达式的值**作为第一个参数。

过滤器可以串联：
` {{ message | filterA | filterB }} `


过滤器是 JavaScript 函数，因此可以接受参数：
` {{ message | filterA('arg1', arg2) }} `

字符串 'arg1' 将传给过滤器作为第二个参数， arg2 表达式的值将被求值然后传给过滤器作为第三个参数。

## 缩写（只有两个Vue命令有缩写形式）
1. v-bind缩写：
```
<!-- 完整语法 -->
<a v-bind:href="url"></a>
<!-- 缩写 -->
<a :href="url"></a>
```

2. v-on缩写：
```
<!-- 完整语法 -->
<a v-on:click="doSomething"></a>
<!-- 缩写 -->
<a @click="doSomething"></a>
```

: 与 @ 对于属性名来说都是合法字符，在所有支持 Vue.js 的浏览器都能被正确地解析。而且，它们不会出现在最终渲染的标记。
