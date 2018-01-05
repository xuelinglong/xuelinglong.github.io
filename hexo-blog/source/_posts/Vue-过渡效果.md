---
title: Vue-过渡效果
date: 2017-06-26 17:19:05
tags: Vue.js
---

# 什么是过渡
Vue只有在插入，更新或者移除DOM元素时才会应用过渡效果，过渡效果的应用可以通过不同方式实现，官方文档中提到了如下几种：
1. 在CSS过渡和动画中自动应用class；
2. 配合使用第三方的CSS动画库，如Animate.css；
3. 在过渡钩子函数中使用JavaScript直接操作DOM；
4. 配合使用第三方JavaScript动画库，如Velocity；

上面四种方式其实主要就是两种，一个是利用CSS过渡或者动画，另一个是利用JavaScript钩子函数。
<!-- more -->

# 单元素/组件的过渡
Vue 提供的 transition 的来封装组件成为过渡组件，可以给任何元素和组件添加 entering/leaving 过渡，transition需要与如下情景中的任一种一起使用：
1. 条件渲染 （使用 v-if）
2. 条件展示 （使用 v-show）
3. 动态组件
4. 组件根节点

```
    <div id="demo">
        <button v-on:click="show = !show">
            Toggle
        </button>
        <transition name="fade">
            <p v-if="show">hello</p>
        </transition>
    </div>
    <script>
        new Vue({
            el: '#demo',
            data: {
                show: true
            }
        })
    </script>
/* demo的CSS样式 */
.fade-enter-active, .fade-leave-active {
  transition: opacity .5s
  /* 
    transition:创建简单的过渡，在一条声明中指定所有过渡细节的简写属性；
    transition-delay：指定过渡开始之前的延迟时间；
    transition-duration：指定过渡的持续时间；
    transition-property：指定应用过渡的属性；
    transition-timing-function：指定过渡期间计算中间值的方式；
   */
}
.fade-enter, .fade-leave-to  {
  opacity: 0
}
```
fade-enter-active和fade-leave-active在整个进入或离开过程中都有效，所以CSS的transition属性在这两个类下进行设置。 
fade-enter和fade-leave-active类设置CSS为opacity:0，说明过渡刚进入和离开的时候透明度为0，即不显示。

当插入或删除包含在 transition 组件中的元素时，Vue 将会做以下处理：
1. 自动嗅探目标元素是否应用了 CSS 过渡或动画，如果是，在恰当的时机添加/删除 CSS 类名。
2. 如果过渡组件提供了 JavaScript 钩子函数，这些钩子函数将在恰当的时机被调用。
3. 如果没有找到 JavaScript 钩子并且也没有检测到 CSS 过渡/动画，DOM 操作（插入/删除）在下一帧中立即执行。(注意：此指浏览器逐帧动画机制，和 Vue的 nextTick概念不同)

# 过渡的-CSS-类名
会有6个（CSS）类名在enter/leave 的过渡中切换：
1. v-enter：定义进入过渡的开始状态。在元素被插入时生效，在下一个帧移除。
2. v-enter-active：定义过渡的状态。在元素整个过渡过程中作用，在元素被插入时生效，在 transition/animation 完成之后移除。这个类可以被用来定义过渡的过程时间，延迟和曲线函数。
3. v-enter-to：**2.1.8版及以上** 定义**进入**过渡的结束状态。在元素被插入一帧后生效（于此同时 v-enter 被删除），在 transition/animation 完成之后移除。
4. v-leave： 定义离开过渡的开始状态。在离开过渡被触发时生效，在下一个帧移除。
5. v-leave-active： 定义离开过渡的开始状态。在离开过渡被触发时生效，在下一个帧移除。
6. v-leave-to：**2.1.8版及以上** 定义**离开**过渡的结束状态。在离开过渡被触发一帧后生效（于此同时 v-leave 被删除），在 transition/animation 完成之后移除。

v- 是这些类名的前缀。
使用<transition name="重置后的前缀">可以重置前缀。
v-enter-active 和 v-leave-active 可以控制 进入/离开 过渡的不同阶段。

# CSS过渡
常用的过渡都是CSS过渡。
```
<!-- 首先将要过渡的元素用transition包裹，并设置过渡的name，然后添加触发这个元素过渡的按钮（实际项目中不一定是按钮，任何能触发过渡组件的DOM操作的操作都可以） -->
    <div id="example-1">
        <button @click="show = !show">
            Toggle render
        </button>
        <transition name="slide-fade">  
            <!--把前缀重置为slide-fade-->
            <p v-if="show">hello</p>
        </transition>
    </div>
    <script>
        new Vue({
            el: '#example-1',
            data: {
                show: true
            }
        })
    </script>
// 接着为过渡类名添加规则
/* example-1的CSS样式 */
/* 可以设置不同的进入和离开动画 */
/* 设置持续时间和动画函数 */
.slide-fade-enter-active {
  transition: all .3s ease;
}
.slide-fade-leave-active {
  transition: all .8s cubic-bezier(1.0, 0.5, 0.8, 1.0);
}
.slide-fade-enter, .slide-fade-leave-active {
  transform: translateX(10px);
  opacity: 0;
  /* 
    transform:为元素应用变换          属性--值：
    translate：在水平方向、垂直方向或者两个方向上平移元素； translateX,translateY
    scale（<数值>):在水平方向、垂直方向或者两个方向上缩放元素；scaleX，scaleY
    rotate（角度）:旋转元素；
    skew（<角度>）:在水平方向、垂直方向或者两个方向上使元素倾斜一定的角度；skewX,skewY
    matrix（4-6个数值，逗号隔开）:指定自定义变换。最后两个数字是 Z 轴缩放；
   */
}
```


# CSS动画
CSS 动画用法同 CSS 过渡，区别是在动画中 v-enter 类名在节点插入 DOM 后不会立即删除，而是在 animationend 事件触发时删除。
```
    <div id="example-2">
        <button @click="show = !show">Toggle show</button>
        <transition name="bounce">
            <p v-if="show">Look at me!</p>
        </transition>
    </div>
    <script>
        new Vue({
            el: '#example-2',
            data: {
                show: true
            }
        })
    </script>
/* example-2的CSS样式 */
.bounce-enter-active {
  animation: bounce-in .5s; 
  /* 
    animation:动画          属性--值：
    animation-delay：设置动画开始前的延迟；
    animation-direction：设置动画循环播放的时候是否反向播放；
    animation-duration：设置动画播放的持续时间；
    animation-iteration-count：设置动画的播放次数；
    animation-name：指定动画名称；
    animation-play-state：允许动画暂停和重新播放；
    animation-timing-function：指定如何计算中间动画值    值的列表在下方
      1.ease：默认值
      2.linear：
      3.ease-in：
      4.ease-out：
      5.ease-in-out：
      以上为五种：四个点控制的三次贝塞尔曲线
      6.cubic-bezier：指定自定义曲线
    animation简写属性格式：
    animation：<animation-name> <animation-duration> <animation-timing-function>
               <animation-delay> <animation-iteration-count>
   */
}
.bounce-leave-active {
  animation: bounce-out .5s;
}
@keyframes bounce-in {
  0% {
    transform: scale(0);
  }
  50% {
    transform: scale(1.5);
  }
  100% {
    transform: scale(1);
  }
}
@keyframes bounce-out {
  0% {
    transform: scale(1);
  }
  50% {
    transform: scale(1.5);
  }
  100% {
    transform: scale(0);
  }
}
```

# 自定义过渡类名
我们可以通过以下特性来自定义过渡类名：
1. enter-class
2. enter-active-class
3. leave-class
4. leave-active-class

自定义过渡类名的优先级高于普通的类名，这对于 Vue 的过渡系统和其他第三方 CSS 动画库如 Animate.css 结合使用十分有用。
```
<transition
    name="custom-classes-transition"
/* 自定义过渡类名 */
    enter-active-class="animated tada"
    leave-active-class="animated bounceOutRight"
  >
    <p v-if="show">hello</p>
  </transition>
```
# 同时使用Transitions和Animations
必须设置相应的事件监听器才能让Vue知道过渡的完成。
事件监听器可以是 transitionend 或 animationend ，这取决于给元素应用的 CSS 规则。使用其中任何一种，Vue 都能自动识别类型并设置监听。

有些场景中需要给同一个元素同时设置两种过渡动效，比如 animation 很快的被触发并完成了，而 transition 效果还没结束。此种情况就需要使用 type 特性并设置 animation 或 transition 来明确声明你需要 Vue 监听的类型。

# JavaScript 钩子
可以在属性中声明JavaScript 钩子。
```
<transition
  v-on:before-enter="beforeEnter"
  v-on:enter="enter"
  v-on:after-enter="afterEnter"
  v-on:enter-cancelled="enterCancelled"
  v-on:before-leave="beforeLeave"
  v-on:leave="leave"
  v-on:after-leave="afterLeave"
  v-on:leave-cancelled="leaveCancelled"
>
  <!-- ... -->
</transition>

JS:
// ...
methods: {
  // --------
  // 进入中
  // --------
  beforeEnter: function (el) {
    // ...
  },
  // 此回调函数是可选项的设置
  // 与 CSS 结合时使用
  enter: function (el, done) {
    // ...
    done()
  },
  afterEnter: function (el) {
    // ...
  },
  enterCancelled: function (el) {
    // ...
  },
  // --------
  // 离开时
  // --------
  beforeLeave: function (el) {
    // ...
  },
  // 此回调函数是可选项的设置
  // 与 CSS 结合时使用
  leave: function (el, done) {
    // ...
    done()
  },
  afterLeave: function (el) {
    // ...
  },
  // leaveCancelled 只用于 v-show 中
  leaveCancelled: function (el) {
    // ...
  }
}
```
钩子函数可以结合 CSS transitions/animations 使用，也可以单独使用。
> 1. 当只用 JavaScript 过渡的时候， 在 enter 和 leave 中，回调函数 done 是必须的 。 否则，它们会被同步调用，过渡会立即完成。
> 2. 对于仅使用 JavaScript 过渡的元素添加 v-bind:css="false"，Vue 会跳过 CSS 的检测。这也可以避免过渡过程中 CSS 的影响。

# 初始渲染的过渡
appear 特性可以设置节点的在初始渲染的过渡：
```
<transition appear>
  <!-- ... -->
</transition>
```
这里默认和进入和离开过渡一样。

也可以自定义 CSS 类名。
```
<transition
  appear
  appear-class="custom-appear-class"
  appear-active-class="custom-appear-active-class"
>
  <!-- ... -->
</transition>
```
自定义 JavaScript 钩子：
```
<transition
  appear
  v-on:before-appear="customBeforeAppearHook"
  v-on:appear="customAppearHook"
  v-on:after-appear="customAfterAppearHook"
>
  <!-- ... -->
</transition>
```

# 多个元素的过渡
对于原生标签可以使用 v-if/v-else 。
最常见的多标签过渡是一个列表和描述这个列表为空消息的元素：
```
<transition>
  <table v-if="items.length > 0">
    <!-- ... -->
  </table>
  <p v-else>Sorry, no items found.</p>
</transition>
```
当有**相同标签名**的元素切换时，需要通过 key 特性设置唯一的值来标记以让 Vue 区分它们，否则 Vue 为了效率只会替换相同标签内部的内容。

即使在技术上没有必要，给在 <transition> 组件中的多个元素设置 key 是一个更好的实践。
```
<transition>
  <button v-if="isEditing" key="save">
    Save
  </button>
  <button v-else key="edit">
    Edit
  </button>
</transition>
```
在一些场景中，也可以给通过给同一个元素的 key 特性设置不同的状态来代替 v-if 和 v-else。
```
<transition>
  <button v-bind:key="isEditing">
    {{ isEditing ? 'Save' : 'Edit' }}
  </button>
</transition>
```

使用多个 v-if 的多个元素的过渡可以重写为绑定了动态属性的单个元素过渡。
示例：使用多个 v-if 的多个元素的过渡
```
<transition>
  <button v-if="docState === 'saved'" key="saved">
    Edit
  </button>
  <button v-if="docState === 'edited'" key="edited">
    Save
  </button>
  <button v-if="docState === 'editing'" key="editing">
    Cancel
  </button>
</transition>
```
重写为绑定了动态属性的单个元素过渡。
```
<transition>
  <button v-bind:key="docState">
    {{ buttonMessage }}
  </button>
</transition>
<script>
// ...
computed: {
  buttonMessage: function () {
    switch (docState) {
      case 'saved': return 'Edit'
      case 'edited': return 'Save'
      case 'editing': return 'Cancel'
    }
  }
}
</script>
```

# 过渡模式
<transition> 的默认行为 - 进入和离开同时发生。
即一个离开过渡的时候另一个开始进入过渡。

同时生效的进入和离开的过渡不能满足所有要求，所以 Vue 提供了 过渡模式：
1. in-out: 新元素先进行过渡，完成之后当前元素过渡离开。
2. out-in: 当前元素先进行过渡，完成之后新元素过渡进入。

in-out 模式不是经常用到，但对于一些稍微不同的过渡效果还是有用的。

# 多个组件的过渡
**不需要使用 key 特性！！！**
只需要使用**动态组件**:
```
<!-- 多个组件的过渡：使用动态组件 -->
    <div id="transition-components-demo">
        <input v-model="view" type="radio" value="v-a" id="a" name="view"><label for="a">A</label>    <!--单选按钮-->
        <input v-model="view" type="radio" value="v-b" id="b" name="view"><label for="b">B</label>
        <transition name="component-fade" mode="out-in">
            <component v-bind:is="view"></component>
        </transition>
    </div>
    
    <script>
        new Vue({
            el: '#transition-components-demo',
            data: {
                view: 'v-a'
            },
            components: {
                'v-a': {
                template: '<div>Component A</div>'
                },
                'v-b': {
                template: '<div>Component B</div>'
                }
            }
        })
    </script>  
/* 动态组件的CSS样式 */
.component-fade-enter-active, .component-fade-leave-active {
  transition: opacity .3s ease;
}
.component-fade-enter, .component-fade-leave-active {
  opacity: 0;
}
```

# 列表过渡
目前为止，关于过渡我们已经讲到：
1. 单个节点；
2. 同一时间渲染多个节点中的一个。

那么怎么同时渲染整个列表，比如使用 v-for ？在这种场景中，使用 <transition-group> 组件。在我们深入例子之前，先了解关于这个组件的几个特点：
1. 不同于 <transition>， 它会以一个真实元素呈现：默认为一个 <span>。你也可以通过 tag 特性更换为其他元素。
2. 内部元素 **总是需要** 提供唯一的 key 属性值。

# 列表的进入和离开过渡
```
<transition-group name="list" tag="p">
    <span v-for="item in items" v-bind:key="item" class="list-item">
      {{ item }}
    </span>
  </transition-group>
```


# 列表的位移过渡
<transition-group> 组件不仅可以进入和离开动画，还可以**改变定位**。
v-move 特性会在元素的改变定位的过程中应用。像之前的类名一样，可以通过 name 属性来自定义前缀，也可以通过 move-class 属性手动设置。

v-move 对于设置过渡的切换时机和过渡曲线非常有用。

注意：使用 FLIP 过渡的元素不能设置为 display: inline 。作为替代方案，可以设置为 display: inline-block 或者放置于 flex 中。

LIP 动画不仅可以实现单列过渡，也可以实现多维网格的过渡。

# 列表的渐进过渡
通过 data 属性与 JavaScript 通信 ，就可以实现列表的渐进过渡。


# 可复用的过渡
过渡可以通过 Vue 的组件系统实现复用。

将 <transition> 或者 <transition-group> 作为根组件，然后将任何子组件放置在其中就可以创建一个可复用过渡组件。

# 动态过渡
在 Vue 中即使是过渡也是数据驱动的！
动态过渡最基本的例子是通过 name 特性来绑定动态值。
```
<transition v-bind:name="transitionName">
  <!-- ... -->
</transition>
```
当你想用 Vue 的过渡系统来定义的 CSS 过渡/动画 在不同过渡间切换会非常有用。

所有的过渡特性都是动态绑定。它不仅是简单的特性，通过事件的钩子函数方法，可以在获取到相应上下文数据。这意味着，可以根据组件的状态通过 JavaScript 过渡设置不同的过渡效果。

创建动态过渡的最终方案是组件通过接受 props 来动态修改之前的过渡。

