---
title: html/css回顾
date: 2019-03-29 12:04:19
tags: 面试准备
---

# Html、Html5、CSS3基础回顾

## html

### 属性

html标签可以拥有属性，属性以键值对存在，如class="vale"，属性在开始标签中规定，属性值要加引号，适用于大多数元素的属性有class,id,style,title。

style：是一个html属性，可用来改变html样式。

title是标题，可从h1-h6逐步变小，浏览器会自动在标题后面添加空行。

### 元素

元素是指从开始标签到结束标签的所有代码。

开始标签：开放标签。  
结束标签：闭合标签。
元素内容：开始标签与闭合标签之间的内容。元素内容可以是空，此时在开始标签关闭。

<!-- more -->

块级元素(block level element)：显示时会以新行来开始和结束，如div、标题(h1-h6)、段落(p)、ul、table(HTML会自动在块级元素前后添加一个额外空行)。

内联元素(inline element)：显示时不以新行开始，如img、a、span等。

html水平线：`<hr />`标签可在html页面中创建水平线，可用来分隔文章中的小节。

html注释：`<!-- 注释内容 -->`

段落：`<p>段落内容</p>`可用来定义段落，这是一个块级元素，浏览器会自动在段落前后添加空行，不产生新段落的换行可嵌套`<br />代表空的HTML元素`。

格式化：粗体文字`<b>`、大号字`<big>`、着重文字`<em>`、斜体字`<i>`、小号字`<small>`、加重语气`<strong>`、下标字`<sub>`、上标字`<sup>`、插入字`<ins>`、删除字`<del>`。

引用：`<q>被引用内容</q>`，短引用、显示时此部分内容会被加上引号。`<blockquote>被引用内容<blockquote/>`长引用、显示时会进行缩进处理。`<abbr></abbr>`定义缩写。`<address></address>`定义文档或联系信息，此部分显示为斜体字。`<cite></cite>`著作标题显示为斜体，`<bdo></bdo>`覆盖当前文本方向，可反向显示。

编程代码：`<code></code>`不保留多余空格和折行输出代码。`<pre></pre>`可保留空格和折行输出代码。数学变量用`<var></var>`。`<kbd></kbd>`键盘文本。`<samp></samp>`计算机输出示例。

超链接：a标签的href属性规定链接的目标，target属性定义被链接的文档在何处打开，target="_blank"在新窗口中打开，name属性定义锚点的名字，其他地方可通过href跳转到该锚点，如:`<a name="label">锚（显示在页面上的文本）</a>`,`<a href="#tips">有用的提示</a>`。注意：href的网址最后面要加上斜杠，不然会发出两次HTTP请求。

图像：`<img />`，img是空标签即没有闭合标签，源属性src定义url（即存储位置），替换文本属性alt定义图像的替换文本在加载图片失败时显示文本。

表格：`<table></table>`，caption定义表格标题。th定义表头是居中粗体文字，tr定义行，td定义单元格的数据。数据单元格可包含文本、图片、列表、段落、表格等等。单元格内容为空时不显示该单元格边框，输入空格占位符`&nbsp;`可显示边框。

无序列表：`<ul type="disc/circle/square/"><li></li></ul>`显示时会有小黑圈圈，内部可使用段落、换行符、图片、链接、以及其它列表。

有序列表：`<ol type="A/1/a"><li></li></ol>`使用数字进行标记。

定义列表：`<dl><dt></dt><dd></dd></dl>`dt自定义列表项，dd列表项内容。

布局：html5中的header定义文档或节的页眉，nav定义导航容器，section定义文档中的节，article定义独立的自包含文章，aside定义内容之外的内容，footer定义页脚，details定义额外的细节，summary定义details元素的标题。

iframe：用于在网页内显示网页，src属性指定URL，width指定宽度，height指定高度，name指定锚点名称，其他地方a标签的target设置为刚才的name，此时ifram会作为链接的目标，把src的内容显示在ifram中。

头部元素：`<head></head>`html头部元素的容器，包含文档标题`<title>`、文档描述`<meta>`等，以下标签都可写在head中：title、meta、link、base、script、style。base元素为页面中的所有链接规定默认地址或默认目标(target)，link元素链接外部资源，style定义样式信息，meta元数据供浏览器、搜索引擎、web使用，script元素定义客户端脚本。

字符实体：有的字符是被预留的，如果想显示则需要使用字符实体，如`&lt;`是小于号，`&nbsp;`是空格，`&gt;`是大于号，`&amp;`是&符号...

样式：link标签可以连接一个外部样式表如`<head><link rel="stylesheet" type="text/css" href="mystyle.css"></head>`，head标签内部可以使用`<style></style>`定义内联样式表，元素的开始标签可以使用style属性定义内联样式。

框架：使用框架可以在同一个浏览器窗口中显示不止一个页面，每份HTML文档就是一个框架，且每个框架都独立于其他的饿框架。`<frameset cols="25%,75%">`框架结构标签定义如何讲将窗口分割为框架，rows或columns的值规定了每行或者每列占据屏幕的面积。`<frame src="a.htm" noresize="noresize">`框架标签noresize属性使得用户不能通过拖动边框改变框架大小。

iframe：iframe用于在网页内显示网页，`<iframe src="URL" width="200/30%" height="300/50%" frameborder="0" name="target的值"></iframe>`

`<!DOCTYPE>`声明html版本，`<!DOCTYPE html>`即使用html5。

### 响应式Web设计

响应式Web设计就是能够以可变尺寸传递网页，对于平板和移动设备是必须的。

### css

## XHML

XHML是一种可扩展超文本标记语言，有几个特性：

* 标签必须闭合
* 必须拥有doctype
* 标签名必须使用小写
* 必须拥有根元素
* 元素必须正确的被嵌套
* 属性不能简写
* 用id代替name属性
* 属性值必须加引号
* /之前应该添加一个空格

## HTML5

HTML5是下一代的html。减少flash的使用。

新特性：

1. 用于绘画的canvas元素
2. 用于媒介回放的video和audio元素
3. 特殊元素内容article、section、footer、header、nav。
4. 新的表单控件：calendar、date、time、email、url、search。

SVG：可伸缩矢量图形，有svg标签可实现。

画布canvas：用于在网页上绘制图形，它的绘画工作必须在JavaScript内部完成。JavaScript通过id寻找canvas元素，使用`var c=document.getElementById("myCanvas"); var cxt=getContext("2d");`创建context对象（getContext("2d")是html5内建对象，拥有多种绘制路径、矩形、圆形、字符、以及添加图像的方法）

多媒体：object元素可以支持HTML插件，如使用flash播放视频。H5中有一个`<embed>`元素定义外部（非HTML）内容的容器，必须使用HTML5才可。H5中还有一个`<audio>`标签可定义声音。

视频：video元素可用来包含视频，该元素支持三种视频格式：Ogg(带有 Theora 视频编码和 Vorbis 音频编码)、MPEG4(带有 H.264 视频编码和 AAC 音频编码)、WebM(带有 VP8 视频编码和 Vorbis 音频编码)。src属性提供视频位置，controls属性提供播放、暂停、音量控件，width设置宽度，height设置高度。video元素里面可嵌套多个source元素（用来链接不同的视频文件），浏览器会使用第一个能够识别的格式。

未整理完，待续。。。
