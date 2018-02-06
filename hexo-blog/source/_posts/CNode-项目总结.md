---
title: '[CNode]项目总结'
date: 2018-02-06 10:29:44
tags: 项目总结
---

项目内容：Node中文社区<br>
项目地址：[github地址](https://github.com/xuelinglong/vue-CNode)<br>
预览地址：[demo](https://xuelinglong.github.io/vue-CNode/#/)

# 项目过程
之前写项目都是：先写大体样式 --> vuex管理状态 --> axios获取数据。
缺点：进度很慢，样式改很久写完了，一旦加上vuex就会发现有些地方能更简洁的实现，获取到数据后发现样式又不太合适，这时候又要更改，整个进度都很缓慢。
<!-- more -->

本次项目：严格按照之前 [vue中文网项目总结](https://xuelinglong.github.io/2018/01/23/vue%E4%B8%AD%E6%96%87%E7%BD%91-%E9%A1%B9%E7%9B%AE%E6%80%BB%E7%BB%93/#more) 中写的那样，以页面为基准，一个页面一个页面的进行，把当前所写页面的内容全部完成后在进行下一个，这个过程中发现速度快了许多，而且我对于整个项目中的状态管理也比较清晰。

# 遇到的重难点
## 发布后样式变化
由于使用muse-ui中的组件，有时候同一个组件在项目的不同页面中会使用好几个，例如` <mu-raised-button label="xxx" :fullWidth="true" class="demo-raised-button" @click.native="xxx" primary/> `，开发过程中样式改写后Chrome预览正确，但是build之后不同页面中的class名相同的部分样式会混乱，我的解决方法是在button外部写一个div，指定与其他地方的raiser-button所不相同的样式，例如宽度。

## muse-ui中的 infinite-scroll 无法触发
```
// ...
    <div class="contentlist">
        <mu-refresh-control :refreshing="refreshing" :trigger="trigger" @refresh="loadTop"/>
        <v-contentitem v-for="topic in topics.topicsdata" :key="topic.id" :topic="topic"></v-contentitem>
        <mu-infinite-scroll :scroller="scroller" :loading="loading" @load="loadMore" loadingText="正在加载..."/>
    </div>
// ...
<script>
export default {
    data () {
        return {
            refreshing: false,
            trigger: null,
            loading: false,
            scroller: null
        }
    },
    mounted () {
        this.trigger = this.$el
        this.scroller = this.$el
    },
    methods: {
        loadMore () {
            this.loading = true
            setTimeout(() => {
                this.page += 1
                this.fetchtopics(this.activeTab, this.page, 20)
                this.loading = false
            }, 3000)
        }
    }
}
</script>
```
表现：滑动到底部时触发不了loadMore，没找到错误的点，就又引入了vue-infinite-scroll，改为使用vue-infinite-scroll。

# 收获
* 改变了自己的开发模式，效率更高。
* 编写时尽可能的规范代码格式，看着更整洁清晰。

总体上来说，本次项目过程比较顺利，只遇到过两个大难题，muse-ui的infinite-scroll没找到不能使用的原因，项目之后再去研究研究，希望能解决。