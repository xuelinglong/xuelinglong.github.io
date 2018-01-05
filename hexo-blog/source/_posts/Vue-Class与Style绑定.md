---
title: Vue-Class与Style绑定
date: 2017-06-07 22:45:41
tags: Vue.js
---

数据绑定一个常见需求是操作元素的 class 列表和它的内联样式。因为它们都是属性 ，我们可以用v-bind 处理它们：只需要计算出表达式最终的字符串。不过，字符串拼接麻烦又易错。因此，在 v-bind 用于 class 和 style 时， Vue.js 专门增强了它。表达式的结果类型除了字符串之外，还可以是对象或数组。
<!-- more -->
普通的v-bind指令示例：
```
    <div id="app-2">
        <!--
            v-bind被称为指令，有前缀 v-的指令是Vue 提供的特殊属性
            作用：它们会在渲染的 DOM 上应用特殊的响应式行为    
        -->
        <!--该指令的作用是将这个元素节点的 title 属性和 Vue 实例的 message 属性保持一致-->
        <span v-bind:title="message">
        鼠标悬停几秒钟查看此处动态绑定的提示信息！
        </span>
    </div>
    <script>       
        var app2 = new Vue({
            el: '#app-2',
            data: {
                message: '页面加载于 ' + new Date()
            }
        })
    </script>
```


# 绑定HTML Class
## 对象语法
我们可以传给 v-bind:class 一个对象，以动态地切换 class 。
````
<div v-bind:class="{ active: isActive }"></div>
```
上面的语法表示 class active的更新将取决于数据属性 isActive是否为真值 。


我们也可以在对象中传入更多属性用来动态切换多个 class 。
```
<div class="static"
        v-bind:class="{ 'class-a': isActive, 'class-b': isB }">
        <!--text-danger是属性名   hasError是属性值-->
    </div>
```
data:
```
            data: {
                isA: true,
                isB: false
            }
```
渲染为：` <div class="static class-a"></div> `
当 isA 和 isB 变化时，class 列表将相应地更新：
例如，如果 isB 变为 true，class 列表将变为 "static class-a class-b"。

此外， v-bind:class 指令可以与普通的 class 属性共存：
```
<div class="static"
     v-bind:class="{ active: isActive, 'text-danger': hasError }">
</div>
```
data:
```
            data: {
                isActive: true,
                hasError: false
            }
```
渲染为：` <div class="static active"></div> `
当 isActive 或者 hasError 变化时，class 列表将相应地更新：
例如，如果 hasError 的值为 true ， class列表将变为 "static active text-danger"。


我们也可以直接绑定数据里的一个对象：
```
<div v-bind:class="classObject"></div>
```
data:
```
data: {
  classObject: {
    active: true,
    'text-danger': false
  }
}
```


也可以在这里绑定返回对象的计算属性。
这是一个常用且强大的模式：
```
<div v-bind:class="classObject"></div>
```
data:
```
data: {
  isActive: true,
  error: null
},
computed: {
  classObject: function () {
    return {
      active: this.isActive && !this.error,
      'text-danger': this.error && this.error.type === 'fatal',
    }
  }
}
```

## 数组语法
我们也可以把一个数组传给 v-bind:class ，以应用一个 class 列表：
```
<div v-bind:class="[classA, classB]">
```
data:
```
data: {
  classA: 'class-a',
  classB: 'class-b'
}
```
渲染为：` <div class="class-a class-b"></div> `
如果你也想根据条件切换列表中的 class ，可以用三元表达式：
``` 
<div v-bind:class="[classA, isB ? classB : '']"> 
<!-- 始终添加 classA，但是只有在 isB 是 true 时添加 classB 。-->
<div v-bind:class="[isA ? ClassA : '', ClassB]">
<!-- 始终添加 classB，但是只有在 isA 是 true 时添加 classA 。-->
```
不过，当有多个条件 class 时这样写有些繁琐。
可以在数组语法中使用对象语法：
` <div v-bind:class="[{ class-a: isA }, ClassB]"> `

# 绑定内联样式
## 对象语法
v-bind:style 的对象语法十分直观——看着非常像 CSS ，其实它是一个 JavaScript 对象。 CSS 属性名可以用驼峰式（camelCase）或短横分隔命名（kebab-case）：
```
<div v-bind:style="{ color: activeColor, fontSize: fontSize + 'px' }"></div>
```
data:
```
data: {
  activeColor: 'red',
  fontSize: 30
}
```

直接绑定到一个样式对象通常更好，让模板更清晰：
```
<div v-bind:style="styleObject"></div>
```
data:
```
data: {
  styleObject: {
    color: 'red',
    fontSize: '13px'
  }
}
```
对象语法也常常结合返回对象的计算属性使用。


## 数组语法
v-bind:style 的数组语法可以将多个样式对象应用到一个元素上：
` <div v-bind:style="[baseStyles, overridingStyles]"> `

## 自动添加前缀
当 v-bind:style 使用需要特定前缀的 CSS 属性时，如 transform ，Vue.js 会自动侦测并添加相应的前缀。

## 多重值 （vue2.3版本开始）
可以为 style 绑定中的属性提供一个包含多个值的数组，常用于提供多个带前缀的值：
` <div :style="{ display: ["-webkit-box", "-ms-flexbox", "flex"] }"> `
