---
title: Vue-组件
date: 2017-06-10 22:53:15
tags: Vue.js
---

# 什么是组件（Component）？
组件可以扩展 HTML 元素，封装可重用的代码。在较高层面上，组件是自定义元素， Vue.js 的编译器为它添加特殊功能。在有些情况下，组件也可以是原生 HTML 元素的形式，以 is 特性扩展。

# 使用组件
## 注册
可以通过以下方式创建一个 Vue 实例：
```
new Vue({
  el: '#xxxx',
  // 选项
})
```
使用Vue.component(tagName, options)注册一个**全局组件**：
```
Vue.component('my-component', {
  // 选项
})
```
对于自定义标签名，Vue.js 不强制要求遵循 W3C规则（小写，并且包含一个短杠），尽管遵循这个规则比较好。
<!-- more -->
组件在注册之后，便可以在父实例的模块中以自定义元素 ` <my-component></my-component>  `的形式使用。

示例：
```
    <div id="example">
        <!-- #example 是Vue实例挂载的元素，应该在挂载元素范围内使用组件-->   
        <my-component></my-component>
    </div>
    <script>
        //1.注册组件，并制定组件的HTML标签：此处为my-component
        Vue.component('my-component',{
            template: '<div>This is my first component!</div>'
        })
        //2.创建根实例
        new Vue({
            el: '#example'
        })
    </script>
```
渲染为：
```
    <div id="example">
        <div>This is my first component!</div>
    </div>
```
**注意：要确保在初始化根实例 之前 注册了组件。**

## 局部注册
通过使用组件实例选项注册，可以使组件仅在另一个实例**/**组件的作用域中可用：
```
    <div id="example-1">
        <!--  my-component1 只能在#example-1下使用-->   
        <my-component1></my-component1>
    </div>
    <script>
        var Child={
            template: '<div>This is my second component!</div>'
        }
        new Vue({
            el: '#example-1',
            components:{
                // 将myComponent1 组件注册到Vue实例下
                'my-component1': Child
            }
        })
    </script>
```
这种封装也适用于其它可注册的 Vue 功能，如指令。

## 注册语法糖
```
//在一个步骤中扩展与注册
Vue.component('my-component', {
  template: '<div>A custom component!</div>'
})
```
```
// 局部注册也可以这么做
    <script>
        // var Child={
        //     template: '<div>This is my second component!</div>'
        // }
        // new Vue({
        //     el: '#example-1',
        //     components:{
        //         // 将myComponent1 组件注册到Vue实例下
        //         'my-component1': Child
        //     }
        // })
        //等同于：
        new Vue({
            el: '#example-1',
            components:{
                // 将myComponent1 组件注册到Vue实例下
                'my-component1': {
                    template: '<div>This is my second component!</div>'
                }
            }
        })
    </script>
```

## DOM模版解析说明
当使用 DOM 作为模版时（例如，将 el 选项挂载到一个已存在的元素上）, 你会受到 HTML 的一些限制，因为 Vue 只有在浏览器解析和标准化 HTML 后才能获取模版内容。尤其像这些元素 ` <ul> ，<ol>，<table> ，<select>  `限制了能被它包裹的元素， 而一些像` <option> ` 这样的元素只能出现在某些其它元素内部。
在自定义组件中使用这些受限制的元素时会导致一些问题，例如：
```
<table>
  <my-row>...</my-row>
</table>
```
自定义组件` <my-row> `被认为是无效的内容，因此在渲染的时候会导致错误。变通的方案是使用特殊的 is 属性：
一些 HTML 元素，如` <table> `，限制什么元素可以放在它里面。自定义元素不在白名单上，将被放在元素的外面，因而渲染不正确。这时应当使用 is 特性，指示它是一个自定义元素：
```
<table>
  <tr is="my-row"></tr>
</table>
```
**注意：当使用来自以下来源之一的字符串模板，这些限制将不适用：**
```
<script type="text/x-template">
JavaScript内联模版字符串
.vue 组件
```
因此，有必要的话请使用字符串模版。

## data必须是函数
通过Vue构造器传入的各种选项大多数都可以在组件里用。

data 是一个例外，它必须是函数。
```
    <!--组建中使用data时：data必须是函数-->
    <div id="example-4">
        <simple-counter></simple-counter>
        <simple-counter></simple-counter>
        <simple-counter></simple-counter>
    </div>
    <script>
        var data = { counter: 0 }
        Vue.component('simple-counter', {
            template: '<button v-on:click="counter += 1">{{ counter }}</button>',
            // 技术上 data 的确是一个函数了，因此 Vue 不会警告，
            // 但是我们返回给每个组件的实例的却引用了同一个data对象
            // data: function () {
            //     return data      //增加一个counter会影响所有组件
            // }
            data: function () {
                return {
                    counter: 0      //每个 counter 都有它自己内部的状态
                } 
            }
        })
        new Vue({
            el: '#example-4'
        })
    </script>
```

## 构成组件
组件意味着协同工作。
通常父子组件会是这样的关系：组件 A 在它的模版中使用了组件 B 。
它们之间必然需要相互通信：父组件要给子组件传递数据，子组件需要将它内部发生的事情告知给父组件。

在 Vue.js 中：
父子组件的关系可以总结为 props down, events up 。
父组件通过 props 向下传递数据给子组件；
子组件通过 events 给父组件发送消息。

# Prop
## 使用Prop传递数据
组件实例的作用域是**孤立的**。
这意味着不能(也不应该)在子组件的模板内直接引用父组件的数据。要让子组件使用父组件的数据，我们需要通过子组件的props选项。
子组件要显式地用 props 选项声明它期待获得的数据：
```
    <div id="prop-example-1">
        <!--向子组件传入一个普通字符串-->
        <child message="hello!"></child>
    </div>
    <script>           
        new Vue({
            el: '#prop-example-1',
            components:{
                  child: {
                        // 声明 props
                        props: ['message'],
                        // 就像 data 一样，prop 可以用在模板内
                        // 同样也可以在 vm 实例中像 “this.message” 这样使用
                        template: '<span>{{ message }}</span>'
                  }  
            }
        })        
    </script>
```

## camelCase VS kebab-case
HTML 特性是不区分大小写的。所以，当使用的不是字符串模版，camelCased (驼峰式) 命名的 prop 需要转换为相对应的 kebab-case (短横线隔开式) 命名：
```
    <div id="prop-example-2">        
        <input v-model="parentmsg">
        <br>
        <!--用 v-bind 动态地绑定父组件的数据到子模板的props-->
                        <!-- kebab-case in HTML -->
        <child v-bind:my-message="parentmsg"></child>
        <p>{{parentmsg}}</p>
    </div>
    <script>           
        new Vue({
            el: '#prop-example-2',
            data:{
                parentmsg: 'Message from parent'
            },
            components: {
                child: {
                    // camelCase in JavaScript
                    props: ['myMessage'],
                    // prop 可以用在模板内
                    // 可以用 `this.msg` 设置
                    template: '<span>{{myMessage}}</span>'
                }
            }
        })     
    </script>
```
如果使用的是字符串模版，则没有这些限制。

## 动态Prop
类似于绑定一个普通的特性到一个表达式，也可以用 v-bind 绑定动态 Props 到父组件的数据。每当父组件的数据变化时，也会传导给子组件：
```
    <div id="prop-example-2">        
        <input v-model="parentmsg">
        <br>
        <!--用 v-bind 动态地绑定父组件的数据到子模板的props-->
                        <!-- kebab-case in HTML -->
        <child v-bind:my-message="parentmsg"></child>
        <p>{{parentmsg}}</p>
    </div>
    <script>           
        new Vue({
            el: '#prop-example-2',
            data:{
                parentmsg: 'Message from parent'
            },
            components: {
                child: {
                    // camelCase in JavaScript
                    props: ['myMessage'],
                    // prop 可以用在模板内
                    // 可以用 `this.msg` 设置
                    template: '<span>{{myMessage}}</span>'
                }
            }
        })     
    </script>
```

## 字面量语法 VS 动态语法
不能使用字面量语法传递数值（下面是**错误**的）：
```
<!-- 传递了一个字面 prop ：值是字符串 "1" 而不是number。-->
<comp some-prop="1"></comp>
```
如果想传递一个实际的number，需要使用 v-bind ，从而让它的值被当作 JavaScript 表达式计算：
```
<!-- 传递实际的 number -->
<comp v-bind:some-prop="1"></comp>
```

## 单项数据流
prop 是单向绑定的：即当父组件的属性变化时，将会传导给子组件；但是子组件的属性变化不会传导给父组件。
原因：防止子组件无意修改了父组件的状态。

注意：每次父组件更新时，子组件的所有 prop 都会更新为最新值。
因此：我们**不能**在子组件内部改变 prop。

1. prop 作为初始值传入后，子组件想把它当作局部数据来用。
解决办法：
定义一个局部变量，并用 prop 的值初始化它：
```
props: ['initialCounter'],
data: function () {
  return { counter: this.initialCounter }
}
```

2. prop 作为初始值传入，由子组件处理成其它数据输出。
解决办法：
定义一个计算属性，处理 prop 的值并返回。
```
props: ['size'],
computed: {
  normalizedSize: function () {
    return this.size.trim().toLowerCase()
  }
}
```
注意在 JavaScript 中对象和数组是引用类型，指向同一个内存空间，如果 prop 是一个对象或数组，在子组件内部改变它**会影响**父组件的状态。

## Prop验证
我们可以为组件的 props 指定验证规格。如果传入的数据不符合规格，Vue 会发出警告。当组件给其他人使用时，这很有用。

要指定验证规格，需要用对象的形式，而不能用字符串数组：
```
Vue.component('example', {
            props: {
                // 基础类型检测 （`null` 意思是任何类型都可以）
                propA: Number,
                // 多种类型
                propB: [String, Number],
                // 必传且是字符串
                propC: {
                    type: String,
                    required: true
                },
                // 数字，有默认值
                propD: {
                    type: Number,
                    default: 100
                },
                // 数组／对象的默认值应当由一个工厂函数返回
                propE: {
                    type: Object,
                    default: function () {
                        return { message: 'hello' }
                    }
                },
                // 自定义验证函数
                propF: {
                    validator: function (value) {
                        return value > 10
                    }
                }
            }
        })
```
type 可以是下面原生构造器：
1. String；
2. Number；
3. Boolean；
4. Function；
5. Object；
6. Array；
7. 一个自定义函数，使用 instanceof 检测。

当 prop 验证失败，Vue会在抛出警告 (如果使用的是开发版本)。

# 自定义事件
父组件是使用 **props** 传递数据给子组件，
子组件使用**自定义事件**把数据传递回去。

## 使用 v-on 绑定自定义事件
每个vue实例都实现了时间接口，即：
1. 使用 **` $on(eventName) `**监听事件；
2. 使用**` $emit(eventName) `**触发事件。

 **父组件**可以在使用子组件的地方直接**用 v-on** 来**监听子组件触发的事件**。
注意：不能用**` $on `**侦听子组件抛出的事件，而必须在模板里直接用v-on绑定。

示例：
```
    <div id="counter-event-example">
        <p>{{ total }}</p>
        <!--当子组件触发了 "increment" 事件，父组件的 incrementTotal 方法将被调用。-->
        <button-counter v-on:increment="incrementTotal"></button-counter>
        <button-counter v-on:increment="incrementTotal"></button-counter>
    </div>
    <script>
        // 注册子组件button-counter
        Vue.component('button-counter', {
            //v-on 指令绑定一个事件监听器，通过它调用increment方法
            template: '<button v-on:click="increment">{{ counter }}</button>',
            data: function () {
                return {
                counter: 0
                }
            },
            methods: {
                increment: function () {
                    this.counter += 1
                    this.$emit('increment')
                }
            },
        })

        new Vue({
            el: '#counter-event-example',
            data: {
                total: 0
            },
            methods: {
                incrementTotal: function () {
                    this.total += 1
                }
            }
        })
    </script>
```
子组件只是报告自己的内部事件，至于父组件是否关心则与它无关。

### 给组件绑定原生事件
使用**` .native `**修饰 v-on可以在某个组件的根元素上监听一个原生事件。
示例：
` <my-component v-on:click.native="doTheThing"></my-component> `
 
## .sync修饰符(2.3.0+)
在 2.0 发布之后的实际应用中，我们发现**` .sync `**还是有其适用之处，比如在开发可复用的组件库时。我们需要做的只是**让子组件改变父组件状态的代码更容易被区分**。

在 2.3 我们重新引入了**` .sync `**修饰符，但是这次它只是作为一个编译时的语法糖存在。它会被扩展为**一个自动更新父组件属性的 v-on 侦听器**。
` <comp :foo.sync="bar"></comp> `
会被扩展为：
` <comp :foo="bar" @update:foo="val => bar = val"></comp> `
当子组件需要更新 foo 的值时，它需要显式地触发一个更新事件：
` this.$emit('update:foo', newValue) `

## 使用自定义时间的表单输入组件
自定义事件可以用来创建自定义的表单输入组件，使用 v-model 来进行数据双向绑定。

` <input v-model="something"> `
上面代码是以下示例的语法糖：
` <input v-bind:value="something" v-on:input="something = $event.target.value"> `
所以在组件中使用时，它相当于下面的简写：
` <custom-input v-bind:value="something" v-on:input="something = arguments[0]"></custom-input> `

所以要让组件的 v-model 生效，它必须：
1. 接受一个 value 属性；
2. 在有新的 value 时触发 input 事件。

事件接口不仅仅可以用来连接组件内部的表单输入，也很容易集成你自己创造的输入类型。例如：
```
<voice-recognizer v-model="question"></voice-recognizer>
<webcam-gesture-reader v-model="gesture"></webcam-gesture-reader>
<webcam-retinal-scanner v-model="retinalImage"></webcam-retinal-scanner>
```

## 非父子组件通信
有时候两个组件也需要通信(非父子关系)。

在简单的场景下，可以使用一个空的 Vue 实例作为中央事件总线：
```
//定义一个空的 Vue 实例作为中央事件总线
var bus = new Vue()

// 触发组件 A 中的事件
bus.$emit('id-selected', 1)

// 在组件 B 创建的钩子中监听事件
bus.$on('id-selected', function (id) {
  // ...
})
```

在复杂的情况下，我们应该考虑使用专门的**状态管理模式**。

# 使用Slot分发内容
在使用组件时，我们常常要像这样组合它们：
```
<app>
  <app-header></app-header>
  <app-footer></app-footer>
</app>
```
注意：
1. **` <app> `**组件不知道它的挂载点会有什么内容。挂载点的内容是由**` <app> `**的父组件决定的。
2. **` <app> `**组件很可能有它自己的模版。

为了让组件可以组合，我们需要一种方式来混合父组件的内容与子组件自己的模板。这个过程被称为 **内容分发**。

Vue.js 实现了一个内容分发 API ，参照了当前 Web 组件规范草案，使用特殊的 **` <slot> `**元素作为原始内容的插槽。

## 编译作用域
假定模板为：
```
<child-component>
  {{ message }}
</child-component>
<!--message 应该绑定到父组件的数据-->
```

组件作用域简单地说是：
父组件模板的内容在父组件作用域内编译；
子组件模板的内容在子组件作用域内编译。

**错误：**试图在父组件模板内将一个指令绑定到子组件的属性/方法：
```
<!-- 无效 -->
<child-component v-show="someChildProperty"></child-component>
```

如果要绑定作用域内的指令到一个组件的根节点，应当在组件自己的模板上做：
```
Vue.component('child-component', {
  // 有效，因为是在正确的作用域内
  template: '<div v-show="someChildProperty">Child</div>',
  data: function () {
    return {
      someChildProperty: true
    }
  }
})
```

类似地，分发内容是在父作用域内编译。

## 单个Slot
除非子组件模板包含至少一个**` <slot> `**插口，否则父组件的内容将会被丢弃。当子组件模板只有一个没有属性的 slot 时，父组件整个内容片段将插入到 slot 所在的 DOM 位置，并替换掉 slot 标签本身。

最初在**` <slot> `**标签中的任何内容都被视为备用内容。备用内容在子组件的作用域内编译，并且只有在宿主元素为空，且没有要插入的内容时才显示备用内容。
示例：
假定 my-component 组件有下面模板：
```
<div>
  <h2>我是子组件的标题</h2>
  <slot>
    只有在没有要分发的内容时才会显示。
  </slot>
</div>
```
父组件模版：
```
<div>
  <h1>我是父组件的标题</h1>
  <my-component>
    <p>这是一些初始内容</p>
    <p>这是更多的初始内容</p>
  </my-component>
</div>
```
渲染结果：
```
<div>
  <h1>我是父组件的标题</h1>
  <div>
    <h2>我是子组件的标题</h2>
    <p>这是一些初始内容</p>
    <p>这是更多的初始内容</p>
  </div>
</div>
```

## 具名Slot
**` <slot> `** 元素可以用一个特殊的属性 name 来配置如何分发内容。
多个 slot 可以有不同的名字。
具名 slot 将匹配内容片段中有对应 slot 特性的元素。
仍然可以有一个匿名 slot ，它是**默认 slot **，作为找不到匹配的内容片段的备用插槽。
如果没有默认的 slot ，这些找不到匹配的内容片段将被抛弃。

假定我们有一个 app-layout 组件的模板为：
```
<div>
  <slot name="one"></slot>
  <slot></slot>  
  <slot name="two"></slot>
</div>
```
父组件模版：
```
<app-layout>
  <p slot="one">One</p>
  <p slot="two">Two</p>
  <p>Default A</p>
</app-layout>
```
渲染结果为：
```
<div>
  <p slot="one">One</p>
  <p>Default A</p>   
  <p slot="two">Two</p>
</div>
```
在组合组件时，内容分发 API 是非常有用的机制。

## 作用域插槽（2.1.0新增）
作用域插槽是一种特殊类型的插槽，用作使用一个（能够传递数据到）可重用模板替换已渲染元素。

在子组件中，只需将数据传递到插槽，就像将 prop 传递给组件一样：
```
<div class="child">
  <slot text="hello from child"></slot>
</div>
```
在父级中，具有特殊属性 scope 的**` <template> `**元素，表示它是作用域插槽的模板。scope 的值对应一个临时变量名，此变量接收从子组件中传递的 prop 对象：
```
<div class="parent">
  <child>
    <template scope="props">
      <span>hello from parent</span>
      <span>{{ props.text }}</span>
    </template>
  </child>
</div>
```
如果我们渲染以上结果，得到的输出会是：
```
<div class="parent">
  <div class="child">
    <span>hello from parent</span>
    <span>hello from child</span>
  </div>
</div>
```

作用域插槽更具代表性的用例：
列表组件，允许组件自定义应该如何渲染列表每一项：
```
<my-awesome-list :items="items">
  <!-- 作用域插槽也可以是具名的 -->
  <template slot="item" scope="props">
    <li class="my-fancy-item">{{ props.text }}</li>
  </template>
</my-awesome-list>
```
列表组件的模板：
```
<ul>
  <slot name="item"
    v-for="item in items"
    :text="item.text">
    <!-- 这里写入备用内容 -->
  </slot>
</ul>
```

# 动态组件
多个组件可以使用同一个挂载点，并动态地在它们之间切换。使用保留的**` <component> `**元素：
```
var vm = new Vue({
  el: '#example',
  data: {
    currentView: 'home'
  },
  components: {
    home: { /* ... */ },
    posts: { /* ... */ },
    archive: { /* ... */ }
  }
})
```
```
<component v-bind:is="currentView">
  <!-- 组件在 vm.currentview 变化时改变 -->
</component>
```
也可以直接绑定到组件对象上：
```
var Home = {
  template: '<p>Welcome home!</p>'
}
var vm = new Vue({
  el: '#example',
  data: {
    currentView: Home
  }
})
```

## keep-alive
如果把切换出去的组件保留在内存中，可以保留它的状态或避免重新渲染。为此可以添加一个 keep-alive 指令参数：
```
<keep-alive>
  <component :is="currentView">
    <!-- 非活动组件将被缓存！ -->
  </component>
</keep-alive>
```

# 杂项
## 编写可复用组件
在编写组件时，记住是否要复用组件有好处。
一次性组件跟其它组件紧密耦合没关系，但是可复用组件应当定义一个清晰的公开接口。

Vue 组件的 API 来自三部分 - props, events 和 slots ：
1. Props 允许外部环境传递数据给组件
2. Events 允许组件触发外部环境的副作用
3. Slots 允许外部环境将额外的内容组合在组件中。

使用 v-bind 和 v-on 的简写语法，模板的缩进清楚且简洁：
```
<my-component
  :foo="baz"
  :bar="qux"
  @event-a="doThis"
  @event-b="doThat"
>
  <img slot="icon" src="...">
  <p slot="main-text">Hello!</p>
</my-component>
```

## 子组件索引
使用 ref 为子组件指定一个索引 ID ，可以在 JavaScript 中直接访问子组件。

示例：
```
<div id="parent">
  <user-profile ref="profile"></user-profile>
</div>
```
```
var parent = new Vue({ el: '#parent' })
// 访问子组件
var child = parent.$refs.profile
```

当 ref 和 v-for 一起使用时， ref 是一个数组或对象，包含相应的子组件。

**` $refs `**只在组件渲染完成后才填充，并且它是非响应式的。它仅仅作为一个直接访问子组件的应急方案——应当避免在模版或计算属性中使用**` $refs `**。

## 异步组件
在大型应用中，我们可能需要将应用拆分为多个小模块，按需从服务器下载。
Vue.js 允许将组件定义为一个工厂函数，动态地解析组件的定义。
Vue.js 只在组件需要渲染时触发工厂函数，并且把结果缓存起来，用于后面的再次渲染。
示例：
```
Vue.component('async-example', function (resolve, reject) {
  setTimeout(function () {
    // Pass the component definition to the resolve callback
    resolve({
      template: '<div>I am async!</div>'
    })
  }, 1000)
})
```
工厂函数接收一个 resolve 回调，在收到从服务器下载的组件定义时调用。也可以调用 reject(reason) 指示加载失败。

## 高级异步组件（2.3.0新增）
异步组件的工厂函数也可以返回一个如下的对象：
```
const AsyncComp = () => ({
  // 需要加载的组件. 应当是一个 Promise
  component: import('./MyComp.vue'),
  // loading 时应当渲染的组件
  loading: LoadingComp,
  // 出错时渲染的组件
  error: ErrorComp,
  // 渲染 loading 组件前的等待时间。默认：200ms.
  delay: 200,
  // 最长等待时间。超出此时间则渲染 error 组件。默认：Infinity
  timeout: 3000
})
```
若要在路由组件中上述写法，需要使用 vue-router 2.4.0+。

## 组件命名约定
当注册组件（或者 props）时，可以使用 kebab-case ，camelCase ，或 TitleCase 。

但在 HTML 模版中，请使用 kebab-case 形式：
```
<!-- 在HTML模版中始终使用 kebab-case -->
<kebab-cased-component></kebab-cased-component>
<camel-cased-component></camel-cased-component>
<title-cased-component></title-cased-component>
```

当使用字符串模式时，可以不受限制，即可以使用camelCase 、 TitleCase 或者 kebab-case 来引用：
```
<!-- 在字符串模版中可以用任何你喜欢的方式! -->
<my-component></my-component>
<myComponent></myComponent>
<MyComponent></MyComponent>
```
如果组件未经 slot 元素传递内容，则可以在组件名后使用 / 使其自闭合：
` <my-component/> `
当然，这只在字符串模版中有效。因为自闭的自定义元素是无效的 HTML ，浏览器原生的解析器也无法识别它。

## 递归组件
当组件具有 name 选项时才可以在它的模板内可以递归地调用自己：
` name: 'unique-name-of-my-component' `

利用Vue.component全局注册了一个组件, 全局的ID作为组件的 name 选项，被自动设置：
```
Vue.component('unique-name-of-my-component', {
  // ...
})
```

如果不够谨慎, 递归组件可能导致死循环（例如）：
```
name: 'stack-overflow',
template: '<div><stack-overflow></stack-overflow></div>'
```
因此：要确保递归调用有终止条件 (比如递归调用时使用 v-if 并让他最终返回 false )。

## 组件间的循环引用Circular References Between Components
告诉模块化管理系统循环引用的组件间的处理优先级

## 内联模板
如果子组件有 inline-template 特性，组件将把它的内容当作它的模板，而不是把它当作分发内容。这让模板更灵活。
```
<my-component inline-template>
  <div>
    <p>These are compiled as the component's own template.</p>
    <p>Not parent's transclusion content.</p>
  </div>
</my-component>
```
但是 inline-template 让模板的作用域难以理解。最佳实践是使用 template 选项在组件内定义模板或者在 .vue 文件中使用 template 元素。

## X-Templates
另一种**定义模版**的方式是在 JavaScript 标签里使用 text/x-template 类型，并且指定一个id。
```
<script type="text/x-template" id="hello-world-template">
  <p>Hello hello hello</p>
</script>
```
```
Vue.component('hello-world', {
  template: '#hello-world-template'
})
```
