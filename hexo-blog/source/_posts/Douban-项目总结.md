---
title: '[Douban]项目总结'
date: 2017-09-16 20:42:49
tags: 项目总结
---

项目内容：豆瓣电影
本项目地址：[github地址](https://github.com/xuelinglong/vue_gh/tree/master/Douban)

# Muse-UI库
本项目使用了基于 Vue 2.0 和 Material Desigin 的 UI 组件库 [Muse-UI](https://museui.github.io/)。
## 安装
` npm install muse-ui -save `
## 全部加载
```
import Vue from 'vue'
import MuseUI from 'muse-ui'
import 'muse-ui/dist/muse-ui.css'
Vue.use(MuseUI) 
```
## 引入 material design 的字体和图标
```
<link rel="stylesheet" href="https://fonts.googleapis.com/css?family=Roboto:300,400,500,700,400italic">
<link rel="stylesheet" href="https://fonts.googleapis.com/icon?family=Material+Icons">
```
<!-- more -->
## 注意事项
1. html 中标签必须闭合；
` <mu-flat-button></mu-flat-button> `
2. html 不支持大小写所有的驼峰属性(或者事件)需要用 - 分隔；
` <mu-flat-button background-color="red" @hover-exit="hoverExit"></mu-flat-button> `

## 更改主题
```
import 'muse-ui/dist/muse-ui.css'
import 'muse-ui/dist/theme-carbon.css' // 使用 carbon 主题
```
## 基础组件的使用
以 App Bar 为例：
App Bar相当于安卓应用中的 action bar, 是一种特殊的工具栏，用于放置 logo，导航菜单， 搜索以及一些按钮。
```
<template>
  <mu-appbar title="">
        <mu-flat-button label="郑州" slot="left"/>
        <mu-icon-menu icon="expand_more" :maxHeight="200" slot="left">
            <mu-menu-item v-for="city in cities" :title="city"/>
        </mu-icon-menu>
        <mu-icon-button icon="search" slot="right"/>
        <input type="text" name="search" value="" style="background: #f5f5f5">
  </mu-appbar>
</template>


<style lang="less">
// 需要安装 less 与 less-loader
.tab{
  margin: -60px 0;
}
  input{
    height: 40px;
    border-radius: 9px;
  }
</style>
```
![image.png](http://upload-images.jianshu.io/upload_images/1393082-10a7739b20300939.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### API
1. title 
类型：String ；
描述： 标题，显示在中间。
2. Slots
left：  用于分发 appbar 左边的内容；
right： 用于分发 appbar 右边的内容；
default： 用于分发 appbar 中间的内容，设置此 slot，title参数将没有作用。

# 设计思路
本次只制作了两个页面：**首页** 和 **电影详情** 页面。
可分为三步：
1. 布局、tab、下方listview使用 Muse-UI编写；
2. axios 获取API的数据；
3. vuex 控制状态。

# 项目过程
## API的请求转发配置
在`  ./config/index.js  `中的`  proxyTable ` 配置代理：
```
proxyTable: {
    '/api': {
        target: 'http://api.douban.com/v2',
        changeOrigin: true,
        pathRewrite: {
            '^/api': ''
        }
    }
}
```
在应用中调用`  /api/movie/in_theaters  `来访问 ` http://api.douban.com/v2/movie/in_theaters `，从而解决跨域的问题。

## 使用axios
### 安装
` npm install axios --save `
### 使用
#### 执行 GET 请求
```
// 向具有指定ID的用户发出请求
axios.get('/user?ID=12345')
  .then(function (response) {
    console.log(response);
  })
  .catch(function (error) {
    console.log(error);
  });

// 也可以通过 params 对象传递参数
axios.get('/user', {
    params: {
      ID: 12345
    }
  })
  .then(function (response) {
    console.log(response);
  })
  .catch(function (error) {
    console.log(error);
  });
```
#### axios API
```
// 发送一个 GET 请求 (GET请求是默认请求模式)
axios('/user/12345');
```
axios为所有支持的请求方法提供了别名：
` axios.get（url [，config]） `
使用别名方法时，不需要在config中指定url，method和data属性。
本项目中：
```
// http://api.douban.com/v2/
const HOST = '/api/';

export default function(url, params = {}) {
    return new Promise((resolve, reject) => {
        axios.get(HOST + url, { params })
            .then((res) => {
                resolve(res.data);
            }).catch(err => reject(err));
    });
}
```


# vuex
将**store**分割成模块（module）；
每个模块拥有自己的 **state、mutation、action、getter、**甚至是嵌套子模块。
本次只做电影部分的内容，因此只需要**movies模块**：
1. 将` store.js  `作为入口文件，去挂载各个模块；
2. ` /movies/module.js  `为电影模块的主要 Vuex 文件。

movies模块的初始状态：
```
state: {
        movies: {
            [api.MOVIESTYPE.inTheaters]: {
                total: 0,
                subjects: []
            },
            [api.MOVIESTYPE.comingSoon]: {
                total: 0,
                subjects: []
            }
        },
        tab: 'in_theaters'
            // menu: '全部'
    }
```
更改 Vuex 的 store 中的状态的唯一方法是提交 mutation。
```
const mutations = {
    // 提交载荷(Payload)  传入额外的参数，即 mutation 的载荷(payload)
    //  事件类型为type.CHANGE_MOVIES_TAB
    [type.CHANGE_MOVIES_TAB](state, tab) {
        //  变更状态
        state.tab = tab;
    }
};
```
使用常量替代 Mutation 事件类型：
把这些常量放在单独的文件中可以我们对整个 app 包含的 mutation 一目了然：~/src/axios/movies/type.js
```
// 事件类型 (type)
// 使用常量替代 Mutation 事件类型
export const CHANGE_MOVIES_TAB = 'CHANGE_MOVIES_TAB';
export const CHANGE_MOVIES_MENU = 'CHANGE_MOVIES_MENU';
......
```
Action 提交 mutation：
```
// Action 提交的是 mutation，而不是直接变更状态;
// Action 可以包含任意异步操作;
const actions = {
    [type.CHANGE_MOVIES_TAB](context, tab) {
        context.commit(type.CHANGE_MOVIES_TAB, tab);
    }
};
```

整合初始状态和变更函数，我们就得到了我们所需的 movies模块：
```
export default {
    state: {
        ......
    },
    mutations,
    actions
};
```
store.js 作为入口文件来挂载movies模块：
```
export default new Vuex.Store({
    modules: {
        movie
    }
});
```

# vue-router
## 命名路由
通过一个名称来标识一个路由会显得更方便一些，特别是在链接一个路由，或者是执行一些跳转的时候。可以在创建 Router 实例的时候，在 routes 配置中给某个路由设置名称：

```
export default new Router({
    routes: [
        { path: '/', name: 'tab', component: tab },
        { path: '/subject/:id', name: 'subject', component: subject }
    ]
});
```
要链接到一个命名路由，可以给 router-link 的 to 属性传一个对象：
listview.vue
```
<router-link :to="{name: 'subject', params:{id: 245576}}">
......
</router-link>
```
以上方式会把路由导航到 /subject/245576 路径。

# 编写程序过程中的难点
## 自动获取本机时间未解决

![image.png](http://upload-images.jianshu.io/upload_images/1393082-c3f0ade91f029abf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

此处需要自动获取**第一个月份**为**当前月份**。

## 电影详情页面布局不稳定。
第一次打开：

![image.png](http://upload-images.jianshu.io/upload_images/1393082-8529a1bdcdb92ce4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

刷新过后：
![image.png](http://upload-images.jianshu.io/upload_images/1393082-2bb4f38692777398.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

布局不合理。

## Muse-UI库没研究透彻，刚开始没理解如何改变组件的颜色。

## 获取API数据花费时间很长，且代码不是原创，第二遍一定要自己好好理解JS,ES6，自主把代码写出来。





