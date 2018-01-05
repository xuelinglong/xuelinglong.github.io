---
title: Vue介绍
date: 2017-06-05 20:54:38
tags: Vue.js
---
```
  <!DOCTYPE html>
  <html lang="en">
  <head>
      <meta charset="UTF-8">
      <title>Hello World</title>
      <script src="https://unpkg.com/vue/dist/vue.js"></script>
  </head>
  <body>
        
      <div id="app">
          <p>{{ message }}</p>
      </div>          
      <script>
        var app=new Vue({
            el:'#app',
            data: {
                message:'Hello World!'
            }
        })
      </script>

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
 

      
    <div id="app-3">
        <!--  条件与循环，控制切换一个元素的显示用v-if  -->
        <p v-if="seen">现在你看到我了</p>
    </div>
    <script>
        var app3 = new Vue({
            el: '#app-3',       //vue实例属性与方法el    app3.$el === document.getElementById('app-3') // -> true
            data: {
            seen: true    //app3.seen=false   //字体消失
            }
        })
    </script>



    <div id="app-4">
        <ol>            <!--有序列表-->
        <!--  v-for 指令可以绑定数组的数据来渲染一个项目列表  -->
            <li v-for="todo in todos">
                {{ todo.text }}
            </li>
        </ol>
    </div>
    <script>
        var app4 = new Vue({
            el: '#app-4',
            data: {
                todos: [
                { text: '学习 JavaScript' },
                { text: '学习 Vue' },
                { text: '整个牛项目' }          
                ]         
            }       //app4.todos.push({text:'111'}) 列表中添加一行显示信息
        })
</script>

    <div id="app-5">
        <p>{{ message }}</p>
        <!--  v-on 指令绑定一个事件监听器，通过它调用我们 Vue 实例中定义的方法  -->
        <button v-on:click="reverseMessage">逆转消息</button>
    </div>
    <script>
        var app5 = new Vue({   //生成一个vue实例
            el: '#app-5',    
            data: {
                message: 'Hello Vue.js!'
            },
            methods: {     //vue实例的方法
                reverseMessage: function () {
                    this.message = this.message.split('').reverse().join('')
                    //String对象的split() 方法用于把一个字符串分割成字符串数组。
                    //Array对象的reverse() 方法用于颠倒数组中元素的顺序。
                    //Array对象的join() 方法用于把数组中的所有元素放入一个字符串。
                    //例：join('*')括号中可以指定元素通过指定的分隔符进行分隔。
                }
            }
        })
    </script>


    <div id="app-6">
        <p>{{ message }}</p>
        <!-- v-model指令能轻松实现表单输入和应用状态之间的双向绑定 -->
        <input v-model="message">
    </div>
    <script>
        var app6 = new Vue({
            el: '#app-6',
            data: {
                message: 'Hello Vue!'
            }
        })
    </script>
    
    <div id="app-7">
        <ol>
            <!-- 现在我们为每个todo-item提供待办项对象    -->
            <!-- 待办项对象是变量，即其内容可以是动态的 -->
            <todo-item v-for="item in groceryList" v-bind:todo="item"></todo-item>
            <!--v-for指令绑定groceryList数组的数据来渲染项目列表
            v-bind指令将元素节点的todo属性和vue实例的item属性保持一致-->
        </ol>
    </div>
    <script>
        // 定义名为 todo-item 的新组件
        Vue.component('todo-item', {
            // todo-item 组件现在接受一个
            // "prop"，类似于一个自定义属性
            // 这个属性名为 todo。
            props: ['todo'],
            template: '<li>{{ todo.text }}</li>'
            //选项对象的template属性用于定义组件要渲染的HTML

        })
        var app7 = new Vue({
            el: '#app-7',
            data: {
                 groceryList: [             //数组
                    { text: '蔬菜' },
                    { text: '奶酪' },
                    { text: '随便其他什么人吃的东西' }
                ]
            }
        })
    </script>

  </body>
  </html>
  ```