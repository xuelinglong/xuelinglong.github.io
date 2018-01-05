---
title: '[gank-wxl]Vue项目总结'
date: 2017-08-05 16:51:13
tags: 项目总结
---
项目内容：gank客户端
本项目地址：[github地址](https://github.com/xuelinglong/vue_gh/tree/master/gank-wxl)

# 创建项目(本次使用vue-cli脚手架快速搭建项目)
```
# 创建一个基于 webpack 模板的新项目
$  vue init webpack gank-wxl
# 安装依赖，
$ cd gank-wxl
$ npm install
$ npm run dev
```
## 端口
默认端口号是8080。
如果希望修改端口号，则进入~\config\index.js，修改dev下的port为希望启动的端口号
示例： ` port: 9090, `

# 项目文件结构分析
## package.json
package.json文件是项目根目录下的一个文件，定义该项目开发所需要的各种模块以及一些项目配置信息（如项目名称、版本、描述、作者等）。
<!-- more -->
在命令行中运行npm install命令，会自动安装dependencies和devDependencies字段中的模块。
### scripts字段
package.json文件里有一个scripts字段。
```
  "scripts": {
    "dev": "node build/dev-server.js",
    "start": "node build/dev-server.js",
    "build": "node build/build.js",
    "unit": "cross-env BABEL_ENV=test karma start test/unit/karma.conf.js --single-run",
    "e2e": "node test/e2e/runner.js",
    "test": "npm run unit && npm run e2e",
    "lint": "eslint --ext .js,.vue src test/unit/specs test/e2e/specs"
  }
```
在开发环境下，在命令行中运行npm run dev就相当于在执行node build/dev-server.js。所以script字段是用来指定npm相关命令的缩写的。
### dependencies字段
```
  "dependencies": {
    "iscroll": "^5.2.0",
    "vue": "^2.3.3",
    "vue-awesome-swiper": "^2.5.0",
    "vue-concise-slider": "^2.1.3",
    "vue-infinite-scroll": "^2.0.1",
    "vue-iscroll-view": "^1.0.3",
    "vue-lazyload": "^1.0.5",
    "vue-modal": "^1.0.2",
    "vue-resource": "^1.3.4",
    "vue-router": "^2.3.1",
    "vue-scroller": "^2.2.1"
  }
```
dependencies字段指定了项目运行时所依赖的模块。
### devDependencies字段
```
  "devDependencies": {
    "autoprefixer": "^6.7.2",
    "babel-core": "^6.22.1",
    "babel-eslint": "^7.1.1",
    "babel-loader": "^6.2.10",
    "babel-plugin-istanbul": "^4.1.1",
......
    "webpack-bundle-analyzer": "^2.2.1",
    "webpack-dev-middleware": "^1.10.0",
    "webpack-hot-middleware": "^2.18.0",
    "webpack-merge": "^4.1.0"
  }
```
devDependencies字段指定了项目开发时所依赖的模块。

## .eslintrc.js 文件
eslint定义的只是编码风格，我们可以利用自定义规则的功能，增减规则。
```
    'rules': {
        // allow paren-less arrow functions
        'arrow-parens': 0,
        // allow async-await
        'generator-star-spacing': 0,
        // allow debugger during development
        'no-debugger': process.env.NODE_ENV === 'production' ? 2 : 0,
        // 分号
        'semi': ['error', 'always'],
        // 不强制使用一致的缩进
        'indent': 0,
        // 非空文件末尾不需要保留空行
        'eol-last': ['error', 'never'],
        // 禁止函数圆括号之前的空格
        'space-before-function-paren': ["error", "never"]
    }
}
```
## 项目文件夹解析
1. config ------全局变量
2. dist ------编译后的项目代码
3. src ------项目源码
> assets ------图片资源
> common ------公共样式
> components ------组件
> vuex
> router ------路由
4. App.vue ------根组件
5. main.js ------vue入口文件

# 插件（以vue-infinite-scroll为例）
1. 安装 
` vue install vue-infinite-scroll --save `
会安装在~/node_modules文件夹下
2. 添加
` import infiniteScroll from 'vue-infinite-scroll'; // 引入滑动模块 `
3. 使用插件
` Vue.use(infiniteScroll); `
4. 按模块使用方法在需要的地方使用。

# 设计思路
vue版本的 gank。

界面总体分成--四部分：
1. 头部header
2. 头部延伸出来的切换主题色的弹窗
3. 导航栏menu
4. 下方listview区域（不同）

# 项目过程
## 以header部分为例
```
<template>
  <div id="app">
    <v-header></v-header>
  </div>
</template>

<script>
  // 引用
  import header from './components/header/header.vue';
  // 注册header组件,header是一个原生词，因此有冲突，需要起一个别名
  export default {
    name: 'app',
    components: {
      'v-header': header,
    }
  };
</script>
```
创建header组件文件
~/src/components/header/header.vue
```
<template>
  <!-- 组件内容 -->
</template>

<script type="text/ecmascript-6">
  // 组件属性定义
</script>

<style lang="stylus" rel="stylesheet/stylus">
  // 样式
</style>
```
## vue-router
菜单栏menu跳转使用router。
main.js文件
```
import VueRouter from 'vue-router'; // 引入router
// 安装这个插件
Vue.use(VueRouter);
// 定义路由
const routes = [
    { path: '/welfare', component: welfare },
    { path: '/android', component: android },
    { path: '/ios', component: ios },
    { path: '/web', component: web },
    { path: '/rest', component: rest },
    { path: '/recommend', component: recommend },
    { path: '/resources', component: resources }
];
// 3. 创建 router 实例， 然后传 `routes`配置
const router = new VueRouter({
    'linkActiveClass': 'active',
    routes
});
// 4. 创建和挂载根实例。
// 记得要通过 router 配置参数注入路由，从而让整个应用都有路由功能。
new Vue({
    el: '#app',
    template: '<App/>',
    components: { App },
    store,
    router
});
```
menu-jump.vue文件使用router
```
<template>
    <!-- 使用 router-link 组件来导航. -->
    <!-- 通过传入 `to` 属性指定链接. -->
    <!-- <router-link> 默认会被渲染成一个 `<a>` 标签 -->
    <router-link :to="menu.view">
        <!--  -->
    </router-link>
</template>
```
App.vue文件
```
<template>
  <div id="app">
    <v-header></v-header>
    <v-menu></v-menu>
    <!-- 路由出口 -->
    <!-- 路由匹配到的组件将渲染在这里 -->
    <router-view></router-view>
  </div>
</template>
```

## vuex
Vuex 是一个专为 Vue.js 应用程序开发的状态管理模式。
1. 安装vuex:
` npm install vuex --save `
2. 创建Vuex 应用的核心---store（仓库）：
~/src/vuex/store.js
```
import Vue from 'vue';
import Vuex from 'vuex'; // 引入vuex
Vue.use(Vuex);  // 安装这个插件

// 创建一个对象来保存应用启动时的初始状态
const state = {
    'modalShow': false
    // ...
};

// 创建一个对象存储一系列我们接下来要写的 mutation 函数
const mutations = {
    // 类型type (state) { 回调函数handler }
    UPDATE_MODALSHOW(state) {
        // 变更状态
        state.modalShow = !state.modalShow;
    }
};

// 整合初始状态和变更函数，我们就得到了我们所需的 store
// 至此，这个 store 就可以连接到我们的应用中
export default new Vuex.Store({
    state,
    mutations
});
```
3. 将状态从根组件『注入』到每一个子组件中
main.js
```
import store from './vuex/store';
new Vue({
    el: '#app',
    template: '<App/>',
    components: { App },
    // 把 store 对象提供给 “store” 选项，这可以把 store 的实例注入所有的子组件，子组件能通过 this.$store 访问到。
    store,
    router
});
```
4. 使用
modal包含在header之内。
header.vue
```
<template>
    <div class="header" :class="{'show': modalShow}">
        <!--  -->
        <img src="......" @click="showModal"/>
        <div class="name">干货集中营</div>
        <v-modal v-show="modalShow" :show="modalShow"></v-modal>
    </div>
</template>

<script type="text/ecmascript-6">
    import vModal from '../modal/modal.vue';
    import { mapState } from 'vuex';
    export default {
        name: 'header',
        components: {
            'v-modal': vModal
        },
        computed: {
            // mapState 辅助函数帮助我们生成计算属性.
            // 当映射的计算属性的名称与 state 的子节点名称相同时，我们也可以给 mapState 传一个字符串数组。
            ...mapState([
                // 传字符串参数 'modalShow' 等同于 `state => state.modalShow`
                'modalShow'
            ])
        },
        methods: {
            showModal() {
                // 以相应的 type 调用 store.commit 方法，唤醒一个 mutation handler
                this.$store.commit('UPDATE_MODALSHOW');
            }
        }
    };
</script>
```
modal.vue
```
<template>
    <div class="modal">
        <div class="clickArea-top" @click="falseModal"></div>
    </div>
</template>

<script>
    export default {
        name: 'v-modal',
        methods: {
            falseModal() {
                this.$store.commit('UPDATE_MODALSHOW');
            }
        }
    };
</script>
```
简单的使用vuex管理modalShow的状态。
本次并未深入使用vuex的Getters、Actions、Modules。

## vue.js
本次编写项目中理解了好多看书时的内容。
1. 每个组件文件的` <template><!-- 组件内容 --></template> `
2. 父组件使用props来向子组件传递属性
以父组件（header）向子组件（modal）传递modalShow为例：
header.vue
```
<template>
    <div class="header">
        <!-- :show="modalShow"使用v-bind把show与modalShow绑定使它们保持一致 -->
        <v-modal v-show="modalShow" :show="modalShow"></v-modal>
    </div>
</template>
```
modal.vue
```
<template>
    <div class="modal">
        <!-- 使用show，为真时显示class="modal-list  show"  -->
        <div class="modal-list" :class="{'show': show}">
              <!-- 中间省略 -->
        </div>
    </div>
</template>

<script>
    export default {
        name: 'v-modal',
        // 使用props从父组件获取数据
        props: ['show']
    };
</script>
```
3. 用 v-for 指令根据一组数组的选项列表进行渲染。
以 menu 为例：
menu.vue
```
<template>
         <div class="menu-list">
            <!-- menus 是源数据数组，menu 是数组元素迭代的别名 -->
            <div v-for="menu in menus" class="menu-item">
                <v-jump :menu="menu"></v-jump>
            </div>
         </div>
</template>

<script type="text/ecmascript-6">
    import vJump from '../menu-jump/menu-jump.vue';
    export default {
        name: 'v-menu',
        components: {
            'v-jump': vJump
        },
        data() {
            // 返回一个唯一的对象
            return {
                menus: [
                     { view: 'welfare', data: '福利' },
                     { view: 'android', data: '安卓' },
                     { view: 'ios', data: 'IOS' },
                     { view: 'web', data: '前端' },
                     { view: 'rest', data: '休息视频' },
                     { view: 'recommend', data: '瞎推荐' }
                ]
            };
        }
    };
</script>
```
4. export default { } 与 new Vue ({  })
new vue({}) 是为了实例化，创建一个Vue的实例 就是相当于创建一个根组件；
而export default {}是导出模块,供其他模块进行调用。一般都是导出一个组件，然后去父组件中定义引入就能使用。

## 编写程序过程中的难点
1. listview不同类型的控制
listview区域内容有两种类型：“ 福利 ” 页面是图片，其它页面是多个外链。
我把福利页面获取数据写在了welfare.vue之中；其他页面获取数据整合在 list.vue中，不同页面type变量的值不同，按值获取数据，这种方式在开始写的时候非常方便，但是后期想要添加滑动切换模块才发现组件太多没办法整合。
2. 最后发现vue-infinite-scroll（滑动模块）和 vue-scroller（下拉刷新）有冲突
添加完vue-infinite-scroll（滑动模块）之后可以向下无限加载，但是添加完vue-scroller（下拉刷新）之后虽然也可以向下无限加载，但是此时鼠标滚轮滑动无效，必须使用开发者工具中的Elements变为手机形式触屏向下拖动才有效，，，很苦恼但是没有找到原因。
3. 福利页面的图片点击之后放大图片
福利页面的图片点击之后应该放大图片，我是以创建时间的不同区分应该哪张图片：
welfare.vue
```
<!-- template -->
<div class="welfare-center">
            <div v-for="img in leftImg" class="box-img" @click="selectedImg(img.createdAt)">
                <v-card :img="img"></v-card>
            </div>
        </div>
        <div class="welfare-center">
            <div v-for="img in rightImg" class="box-img" @click="selectedImg(img.createdAt)">
                <v-card :img="img"></v-card>
            </div>
        </div>

<!-- script -->
selectedImg(time) {
                this.time = time;
                this.$store.commit('UPDATE_LOADING', true);
                let object = objectDate(this.time);
                // 以 2017/07/26 的 img 测试代码逻辑是否正确
                this.$http.get(`http://gank.io/api/history/content/day/${object.Y}/${object.M}/${object.D}`)
                .then((response) => {
                    this.magnifyData = response.body.results;
                });
                // 以相应的 type 调用 store.commit 方法，唤醒一个 mutation handler
                this.$store.commit('UPDATE_MAGNIFYSHOW');
            }
```

获取到的数据：
![image.png](http://upload-images.jianshu.io/upload_images/1393082-42639a3ec8c21606.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
没想到如何单独获取content中img的地址。

