---
title: Vue-表单控件绑定
date: 2017-06-09 21:19:22
tags: Vue.js
---

# 基础用法
我们可以用 v-model 指令在表单控件元素上创建双向数据绑定：
```
    <div id="vm-1">
        <p>{{ message }}</p>
        <!-- v-model指令能轻松实现表单输入和应用状态之间的双向绑定 -->
        <input v-model="message">
    </div>
    <script>
        var vm1 = new Vue({
            el: '#vm-1',
            data: {
                message: 'Hello Vue!'
            }
        })
    </script>
```
它会根据控件类型自动选取正确的方法来更新元素。
但 v-model 本质上不过是语法糖，它负责监听用户的输入事件以更新数据，并特别处理一些极端的例子。
<!-- more -->
**v-model 并不关心表单控件初始化所生成的值。因为它会选择 Vue 实例数据来作为具体的值。**

**对于要求 IME （如中文、 日语、 韩语等） 的语言，v-model不会在 ime 构成中得到更新。如果你也想实现更新，请使用 input事件。**

## 文本
```
<div id="vm-2">
    <input v-model="message" placeholder="edit me">
    <p>Message is: {{ message }}</p>
    </div>
    <script>
        var vm2=new Vue({
            el: '#vm-2',
            data: {
                message: ''               
            }
        })
    </script>
```

![image.png](http://upload-images.jianshu.io/upload_images/1393082-e75cf39d8f1e38aa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image.png](http://upload-images.jianshu.io/upload_images/1393082-dd5a2417a4523378.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 多行文本
```
<div id="vm-3" style="margin-top:20px">
        <span>Multiline message is:</span>
        <p style="white-space: pre">{{ message }}</p>
        <br>
        <textarea v-model="message" placeholder="vm3:add multiple lines"></textarea>
    </div>
    <script>
        var vm3=new Vue({
            el: '#vm-3',
            data: {
                message: ''               
            }
        })
    </script>
```

![image.png](http://upload-images.jianshu.io/upload_images/1393082-5648d0cbe44e99f7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image.png](http://upload-images.jianshu.io/upload_images/1393082-d9e95c7d713ce28e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在文本区域插值( <textarea></textarea> ) 并不会生效，应用 v-model 来代替

## 复选框
单个复选框，逻辑值：
```
    <div id="checkbox-1">
        <input type="checkbox" id="checkbox" v-model="checked">
        <label for="checkbox">{{ checked }}</label>
    </div>
    <script>
        var checkbox1=new Vue({
            el: '#checkbox-1',
            data: {
                checked: false               
            }
        })
    </script>
```

![image.png](http://upload-images.jianshu.io/upload_images/1393082-b4192da1e2f145b0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image.png](http://upload-images.jianshu.io/upload_images/1393082-57765a21a4b82d2c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

多个勾选框，绑定到同一个数组：
```
    <div id="checkbox-2">
        <input type="checkbox" id="jack" value="Jack" v-model="checkedNames">
        <label for="jack">Jack</label>
        <input type="checkbox" id="john" value="John" v-model="checkedNames">
        <label for="john">John</label>
        <input type="checkbox" id="mike" value="Mike" v-model="checkedNames">
        <label for="mike">Mike</label>
        <br>
        <span>Checked names: {{ checkedNames }}</span>
    </div>
    <script>
        var checkbox2=new Vue({
            el: '#checkbox-2',
            data: {
                checkedNames: []               
            }
        })
    </script>  
```
![image.png](http://upload-images.jianshu.io/upload_images/1393082-49b8fa8dafbce2e6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image.png](http://upload-images.jianshu.io/upload_images/1393082-d00d4468da1d3a63.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 单选按钮
```
<div id="picked">
        <input type="radio" id="one" value="One" v-model="picked">
        <label for="one">One</label>
        <br>
        <input type="radio" id="two" value="Two" v-model="picked">
        <label for="two">Two</label>
        <br>
        <span>Picked: {{ picked }}</span>
    </div>
    <script>
        var picked=new Vue({
            el: '#picked',
            data: {
                picked: ''               
            }
        })
    </script>
```
![image.png](http://upload-images.jianshu.io/upload_images/1393082-3d37973ef09ae054.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image.png](http://upload-images.jianshu.io/upload_images/1393082-a2bb24cf20b4f51f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 选择列表
单选列表：
```
    <div id="selected-1">
        <select v-model="selected">
            <option>2015</option>
            <option>2016</option>
            <option>2017</option>
        </select>
        <span>Selected: {{ selected }}</span>
    </div>
    <script>
        var selected1=new Vue({
            el: '#selected-1',
            data: {
                selected: null               
            }
        })
    </script>
```
![image.png](http://upload-images.jianshu.io/upload_images/1393082-5da905253c3b6af2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

多选列表（绑定到同一数组）：
```
    <div id="selected-2" style="margin-top:20px">       <!--多选列表，绑定到同一数组-->
        <select v-model="selected" multiple style="width: 50px">
            <option>2015</option>
            <option>2016</option>
            <option>2017</option>
        </select>
        <br>
        <span>Selected: {{ selected }}</span>
    </div>
    <script>
        var selected2=new Vue({
            el: '#selected-2',
            data: {
                selected: []               
            }
        })
    </script>
```

![image.png](http://upload-images.jianshu.io/upload_images/1393082-31320380aaea2d89.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

动态选项，用 v-for 渲染：
```
    <div id="selected-3" style="margin-top:20px">             <!--动态渲染-->
        <select v-model="selected">
            <option v-for="option in options" v-bind:value="option.value">
                {{ option.text }}
            </option>
        </select>
        <span>Selected: {{ selected }}</span>
    </div>
    <script>
        var selected3=new Vue({
            el: '#selected-3',
            data: {
                selected: 'A',
                options: [
                    {text: 'One', value: 'A'},
                    {text: 'Two', value: 'B'},
                    {text: 'There', value: 'C'}
                ]               
            }
        })
    </script>
```

# 绑定value
对于单选按钮，勾选框及选择列表选项， v-model 绑定的 value 通常是静态字符串（对于勾选框是逻辑值）：
```
<!-- 当选中时，`picked` 为字符串 "a" -->
<input type="radio" v-model="picked" value="a">

<!-- `toggle` 为 true 或 false -->
<input type="checkbox" v-model="toggle">

<!-- 当选中时，`selected` 为字符串 "abc" -->
<select v-model="selected">
  <option value="abc">ABC</option>
</select>
```
我们想绑定 value 到 Vue 实例的一个动态属性上，这时可以用 v-bind 实现，并且这个属性的值可以不是字符串。

## 复选框(Checkbox)
```
<input
  type="checkbox"
  v-model="toggle"
  v-bind:true-value="a"
  v-bind:false-value="b"
>
```
```
// 当选中时
vm.toggle === vm.a
// 当没有选中时
vm.toggle === vm.b
```
## 单选按钮（Radio）
` <input type="radio" v-model="pick" v-bind:value="a"> `
```
// 选中
vm.pick === vm.a
```
## 选择列表设置（Select Options）
```
<select v-model="selected">
    <!-- 内联对象字面量 -->
  <option v-bind:value="{ number: 123 }">123</option>
</select>
```
```
// 当选中时
typeof vm.selected // -> 'object'
vm.selected.number // -> 123
```

# 修饰符
## **.lazy**
默认情况下： v-model 在 input 事件中同步输入框的值与数据 。

添加一个修饰符 lazy ，从而转变为在 change 事件中同步：
```
<div id="vm-1">
        <p>{{ message }}</p>
      <!-- 在 "change" 而不是 "input" 事件中更新 -->
      <input v-model.lazy="message">     
    </div>
    <script>
        var vm1 = new Vue({
            el: '#vm-1',
            data: {
                message: 'Hello Vue!'
            }
        })
    </script>
```

## **.number**
添加 number修饰符到 v-model 上可以自动将用户的输入值转为 Number 类型（如果原值的转换结果为 NaN 则返回原值）。
` <input v-model.number="age" type="number"> `

## **.trim**
添加 trim 修饰符到 v-model 上可以自动过滤用户输入的首尾空格：
` <input v-model.trim="message"> `
