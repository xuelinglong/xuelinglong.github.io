---
title: Vue-列表渲染
date: 2017-06-08 20:04:04
tags: Vue.js
---

# v-for
用 v-for 指令根据一组数组的选项列表进行渲染。
v-for 指令需要以 item in items 形式的特殊语法， items 是源数据数组并且 item 是数组元素迭代的别名。
<!-- more -->
## 基本用法
 ```
<div id="app-1">
        <ol>            <!--有序列表-->
        <!--  v-for 指令可以绑定数组的数据来渲染一个项目列表  -->
            <li v-for="todo in todos">
            <!--<li v-for="todo of todos">   结果相同-->
                {{ todo.text }}
            </li>
        </ol>
    </div>
    <script>
        var app1 = new Vue({
            el: '#app-1',
            data: {
                todos: [
                { text: '学习 JavaScript' },
                { text: '学习 Vue' },
                { text: '整个牛项目' }          
                ]         
            }       //app1.todos.push({text:'111'}) 列表中添加一行显示信息
        })
    </script>
```
运行结果：
![image.png](http://upload-images.jianshu.io/upload_images/1393082-54fe167c4d5fb043.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
<ul id="app-2">      <!--无序列表-->
        <li v-for="item in items">
        <!--<li v-for="item of items">    结果相同-->  
            {{ item.message }}
        </li>
    </ul>
    <script>
        var app2 = new Vue({
            el: '#app-2',
            data: {
                items: [
                {message: 'Foo' },
                {message: 'Bar' }
                ]
            }
        })
    </script>
 ```
运行结果：

![image.png](http://upload-images.jianshu.io/upload_images/1393082-afb57fe80426fa40.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


在 v-for 块中，我们拥有对父作用域属性的完全访问权限。 v-for 还支持一个可选的第二个参数为当前项的索引。
```
<ul id="app-3">      <!--无序列表-->
        <!--index作为v-for的第二个参数（可选参数）表示当前项的索引-->
        <li v-for="(item, index) in items">
        <!--<li v-for="(item, index) of items">    结果相同-->       
            {{ parentMessage }} - {{ index }} - {{ item.message }}
            <!--输出形式为：Parent-索引号-值 -->
        </li>
    </ul>
    <script>
        var app3 = new Vue({
            el: '#app-3',
            data: {
                parentMessage: 'Parent',
                items: [
                {message: 'Foo' },
                {message: 'Bar' }
                ]
            }
        })
    </script>
```
运行结果：

![image.png](http://upload-images.jianshu.io/upload_images/1393082-467c347cfc50e44f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以用 of 替代 in 作为分隔符，因为它是最接近 JavaScript 迭代器的语法。

## Template v-for
如同 v-if 模板：
```
<div id="vm-3">
        <template v-if="ok">
            <h1>Title</h1>
            <p>Paragraph 1</p>
            <p>Paragraph 2</p>
        </template>   
    </div>
    <script>
        var vm3 = new Vue({
            el: '#vm-3',   //app3.$el === document.getElementById('app-3') // -> true
            data: {   
            ok: true
            }
        })
    </script>
```
你也可以用带有 v-for 的`  <template>  `标签来渲染多个元素块：
```
<ul>
  <template v-for="item in items">
    <li>{{ item.msg }}</li>
    <li class="divider"></li>
  </template>
</ul>
```

## 迭代对象v-for
可以用 v-for 通过一个对象的属性来迭代：
```
<ul id="repeat-object" class="demo">
        <li v-for="value in object">
            {{ value }}
        </li>
    </ul>
    <script>
        new Vue({
            el: '#repeat-object',
            data: {
                object: {
                FirstName: 'John',
                LastName: 'Doe',
                Age: 30
                }
            }
        })
    </script>
```
运行结果：
![image.png](http://upload-images.jianshu.io/upload_images/1393082-d86441a79eac9b15.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以提供第二个的参数为键名：
```
<ul id="repeat-object1">
        <li v-for="(value, key) in object">
            {{ key }} : {{ value }}
        </li>
    </ul>
    <script>
        new Vue({
            el: '#repeat-object1',
            data: {
                object: {
                FirstName: 'John',
                LastName: 'Doe',
                Age: 30
                }
            }
        })
    </script>
```
运行结果：

![image.png](http://upload-images.jianshu.io/upload_images/1393082-45ed010a893053dd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以提供第三个参数为索引：
```
<div v-for="(value, key, index) in object">
  {{ index }}. {{ key }} : {{ value }}
</div>
```
运行结果：

![image.png](http://upload-images.jianshu.io/upload_images/1393082-ee1b869d6656a40b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**在遍历对象时，是按 Object.keys() 的结果遍历，但是不能保证它的结果在不同的 JavaScript 引擎下是一致的。**

## 整数迭代v-for
v-for 也可以取整数。此时它将重复多次模板。
```
    <div id="range" class="demo">
        <span v-for="n in 10">{{ n }} </span>
    </div>
    <script>
        new Vue({ el: '#range' })
    </script>
```
结果：
![image.png](http://upload-images.jianshu.io/upload_images/1393082-af56a295a6cfb67d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##  v-for VS v-if
当它们处于同一节点， v-for 的优先级比 v-if 更高，这意味着 v-if 将分别重复运行于每个 v-if 循环中。
当你想**为仅有的一些项渲染节点时**，这种优先级的机制会十分有用，如下：
```
<li v-for="todo in todos" v-if="!todo.isComplete">
  {{ todo }}        <!--代码只传递了未complete的todos-->
</li>
```

如果目的是有条件地跳过循环的执行，那么将 v-if 置于包装元素 (或 <template>)上。如:
```
<ul v-if="shouldRenderTodos">
  <li v-for="todo in todos">
    {{ todo }}
  </li>
</ul>
```

# key
当 Vue.js 用 v-for 正在更新已渲染过的元素列表时，它默认用 “就地复用” 策略。如果数据项的顺序被改变，Vue将不是移动 DOM 元素来匹配数据项的顺序， 而是简单复用此处每个元素，并且确保它在特定索引下显示已被渲染过的每个元素。这个类似 Vue 1.x 的 track-by="$index" 。

Vue 1.x 的 track-by="$index" :
如果没有唯一的键供追踪，可以使用 track-by="$index"，它强制让 v-for 进入原位更新模式：片断不会被移动，而是简单地以对应索引的新值刷新。这种模式也能处理数据数组中重复的值。
这让数据替换非常高效，但是也会付出一定的代价。因为这时 DOM 节点不再映射数组元素顺序的改变，不能同步临时状态（比如 <input> 元素的值）以及组件的私有状态。因此，如果 v-for 块包含 <input> 元素或子组件，要小心使用 track-by="$index"

为了给 Vue 一个提示，以便它能跟踪每个节点的身份，从而重用和重新排序现有元素，你需要为每项提供一个唯一 key 属性。理想的 key 值是每项都有唯一 id。这个特殊的属性相当于 Vue 1.x 的 track-by ，但它的工作方式类似于一个属性，所以你需要用 v-bind 来绑定动态值（在这里使用简写）：
```
<div v-for="item in items" :key="item.id">
  <!-- 内容 -->
</div>
```
建议尽可能使用 v-for 来提供 key ，除非迭代 DOM 内容足够简单，或者你是故意要依赖于默认行为来获得性能提升。

因为它是 Vue 识别节点的一个通用机制， key 并不特别与 v-for 关联，key 还具有其他用途。

# 数组更新检测

## 变异方法
Vue 包含一组观察数组的变异方法，所以它们也将会触发视图更新：
1. push()     ：向数组的末尾添加一个或多个元素，并返回新的长度；
2. pop()      ：删除并返回数组的最后一个元素；
3. shift()   ：把数组的第一个元素从其中删除并返回第一个元素的值；
4. unshift()  ：向数组的开头添加一个或更多元素，并返回新的长度；
5. splice()   ：向/从数组中添加/删除项目，然后返回被删除的项目；
6. sort()       ：于对数组的元素进行排序；
7. reverse()    ： 颠倒数组中元素的顺序；

## 重塑数组
**变异方法**会改变被这些方法调用的原始数组。
**非变异方法**不会改变原始数组，但总是返回一个新数组。

非变异方法（3个）：
1. filter()
2. concat()    ：连接两个或多个数组，返回被连接数组的一个副本；
3. slice()      ：从已有的数组中返回选定的元素；

当使用非变异方法时，可以用新数组替换旧数组：
```
    <ul id="app-2">      <!--无序列表-->
        <li v-for="item in items">
        <!--<li v-for="item of items">-->
            {{ item.message }}
        </li>
    </ul>
    <script>
        var app2 = new Vue({
            el: '#app-2',
            data: {
                items: [
                {message: 'Foo' },
                {message: 'Bar' }
                ]
            }
        })
        app2.items = app2.items.filter(function (item) {   //非变异方法filter
            return item.message.match(/Foo/)      
        })
    </script>
```
运行结果：
![image.png](http://upload-images.jianshu.io/upload_images/1393082-e5abadc06013854e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

Vue 实现了一些智能启发式方法来最大化 DOM 元素重用，所以用一个含有相同元素的数组去替换原来的数组是非常高效的操作。

## 注意事项
由于 JavaScript 的限制， Vue 不能检测以下变动的数组：
1. 当你利用索引直接设置一个项时：
例如： ` vm.items[indexOfItem] = newValue `
> 解决方法：实现和 vm.items[indexOfItem] = newValue 相同的效果， 同时也将触发状态更新。
>>1. ` // Vue.set
Vue.set(example1.items, indexOfItem, newValue) `
>>2. ` // Array.prototype.splice
example1.items.splice(indexOfItem, 1, newValue) `
2. 当你修改数组的长度时：
 例如：`  vm.items.length = newLength `
> 解决方法：实现和 vm.items.length = newLength 相同的效果， 同时也将触发状态更新。
>> ` //Array.prototype.splice
example1.items.splice(newLength) `

# 显示过滤/排序结果
当我们想要显示一个数组的过滤或排序副本，而不实际改变或重置原始数据 时，可以创建返回过滤或排序数组的计算属性。

示例：
```
<div id="range1" class="demo1">
        <ul>
            <li v-for="n in evenNumbers">{{ n }}</li>
        </ul>
    </div>
    <script>
        new Vue({
            el: '#range1',
            data: {
                numbers: [ 1, 2, 3, 4, 5 ]
             },
            computed: {
                evenNumbers: function () {
                    return this.numbers.filter(function (number) {
                        return number % 2 === 0
                    })
                }
            } 
        })
    </script>
```
运行结果：
![image.png](http://upload-images.jianshu.io/upload_images/1393082-22a59488b1e64b33.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

或者，也可以在计算属性不适用的情况下 (例如，在嵌套 v-for 循环中) 使用 method 方法：
```
<div id="range2" class="demo2">
        <ul>
            <li v-for="n in even(numbers)">{{ n }}</li>
        </ul>
    </div>
    <script>
        new Vue({
            el: '#range2',
            data: {
                numbers: [ 1, 2, 3, 4, 5 ]
             },
            methods: {
                even: function (numbers) {
                    return numbers.filter(function (number) {
                        return number % 2 === 0
                    })
                }
            } 
        })
    </script>
```
运行结果（同上）：
![image.png](http://upload-images.jianshu.io/upload_images/1393082-3f2500e57f5aa971.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
