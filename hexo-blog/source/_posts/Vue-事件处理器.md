---
title: Vue-事件处理器
date: 2017-06-08 20:06:48
tags: Vue.js
---

# 监听事件
v-on 指令：可以用v-on 指令监听 DOM 事件来触发一些 JavaScript 代码。
示例：
```
    <div id="example-1">
        <button v-on:click="counter += 1">增加 1</button>
        <p>这个按钮被点击了 {{ counter }} 次。</p>
    </div>
    <script>
        var example1 = new Vue({
            el: '#example-1',
            data: {
                counter: 0
            }
        })
    </script>
```
运行结果：
![image.png](http://upload-images.jianshu.io/upload_images/1393082-3c064f280b5af364.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
<!-- more -->
# 方法事件处理器
许多事件处理的逻辑都很复杂，所以直接把 JavaScript 代码写在 v-on 指令中是不可行的。因此 v-on 可以接收一个定义的方法来调用。
```
    <div id="example-2">
        <!-- `greet` 是在下面定义的方法名 -->
        <button v-on:click="greet">Greet</button>
    </div>
    <script>
        var example2 = new Vue({
    el: '#example-2',
        data: {
            name: 'Vue.js'
        },
        // 在 `methods` 对象中定义方法
        methods: {
            greet: function (event) {
            // `this` 在方法里指当前 Vue 实例
            alert('Hello ' + this.name + '!')
            // `event` 是原生 DOM 事件
            alert(event.target.tagName)
            }
        }
    })
    // 也可以用 JavaScript 直接调用方法
    // example2.greet() // -> 'Hello Vue.js!'
    </script>
```
运行结果：
![image.png](http://upload-images.jianshu.io/upload_images/1393082-aa4c2c7fc8d3a698.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 内联处理器方法
除了直接绑定到一个方法，也可以用内联 JavaScript 语句：
```
    <div id="example-3">
        <button v-on:click="say('hi')">Say hi</button>
        <button v-on:click="say('what')">Say what</button>
    </div>
    <script>
        new Vue({
            el: '#example-3',
            methods: {
                say: function (message) {
                alert(message)
                }
            }
        })
    </script>
```
运行结果：
![image.png](http://upload-images.jianshu.io/upload_images/1393082-2c33a3c4b71de83a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在内联语句处理器中访问原生 DOM 事件。可以用特殊变量 $event 把它传入方法：
```
<button v-on:click="warn('Form cannot be submitted yet.', $event)">Submit</button>
    <script>
     // ...
     methods: {
          warn: function (message, event) {
                // 现在我们可以访问原生事件对象
                if (event) event.preventDefault()
                    alert(message)
           }
      }
//...    
    </script>
```

# 事件修饰符
Vue.js 为 v-on 提供了 **事件修饰符**。通过由点(.)表示的指令后缀来调用修饰符。
1 **.stop**
2 **.prevent**
3 **.capture**
4 **.self**
5 **.once**
```
<!-- 阻止单击事件冒泡 -->
<a v-on:click.stop="doThis"></a>

<!-- 提交事件不再重载页面 -->
<form v-on:submit.prevent="onSubmit"></form>

<!-- 修饰符可以串联  -->
<a v-on:click.stop.prevent="doThat"></a>

<!-- 只有修饰符 -->
<form v-on:submit.prevent></form>

<!-- 添加事件侦听器时使用事件捕获模式 -->
<div v-on:click.capture="doThis">...</div>

<!-- 只当事件在该元素本身（而不是子元素）触发时触发回调 -->
<div v-on:click.self="doThat">...</div>

<!-- 点击事件将只会触发一次    2.1.4新增-->
<a v-on:click.once="doThis"></a>
```
.once修饰符还能被用到自定义的组件事件上。

# 按键修饰符
在监听键盘事件时，我们经常需要监测常见的键值。 
Vue 允许为 v-on 在监听键盘事件时添加按键修饰符：
```
<!-- 只有在 keyCode 是 13 时调用 vm.submit() -->
<input v-on:keyup.13="submit">
```

Vue 为最常用的按键提供了别名：
```
<!-- 同上 -->
<input v-on:keyup.enter="submit">

<!-- 缩写语法 -->
<input @keyup.enter="submit">
```

全部的按键别名：
**.enter**
**.tab**
**.delete (捕获 “删除” 和 “退格” 键)**
**.esc**
**.space**
**.up**
**.down**
**.left**
**.right**

可以通过全局 config.keyCodes 对象自定义按键修饰符别名：
```
// 可以使用 v-on:keyup.f1
Vue.config.keyCodes.f1 = 112
```
## 按键修饰符（2.1.0新增）
可以用如下修饰符开启鼠标或键盘事件监听，使在按键按下时发生响应。
**.ctrl**
**.alt**
**.shift**
**.meta**
在Mac系统键盘上，meta对应命令键 (⌘)；
在Windows系统键盘meta对应windows徽标键(⊞)；
在其他特定键盘上...

例如：
```
<!-- Alt + C -->
<input @keyup.alt.67="clear">

<!-- Ctrl + Click -->
<div @click.ctrl="doSomething">Do something</div>
```

# 为什么在HTML中监听事件
所有的 Vue.js 事件处理方法和表达式都严格绑定在当前视图的 ViewModel 上，它不会导致任何维护上的困难。

使用 v-on 有几个好处：
1. 扫一眼 HTML 模板便能轻松定位在 JavaScript 代码里对应的方法。
2. 因为你无须在 JavaScript 里手动绑定事件，你的 ViewModel 代码可以是非常纯粹的逻辑，和 DOM 完全解耦，更易于测试。
3. 当一个 ViewModel 被销毁时，所有的事件处理器都会自动被删除。你无须担心如何自己清理它们。
