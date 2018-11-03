---
title: '原生JavaScript知识弥补'
date: 2018-06-06 20:45:39
tags: JavaScript
---

# 原生JavaScript知识弥补

## confirm确认框

* confirm(str)
* str：消息对话框中要显示的文本，返回值为Boolean
* 返回值：点击确定返回true，点击取消返回false

## prompt消息对话框

* prompt(str1, str2)
* str1：要显示在消息对话框中的文本，不可修改
* str2：文本框的内容，可以修改
* 返回值：点击确定-把文本框中的内容作为函数返回值，点击取消-返回null

## 打开新窗口

* window.open(‘URL’, ‘窗口名称’, ‘参数字符串’)
* 窗口名称：_blank新窗口打开，_self当前窗口显示，_top
* 参数字符串：设置窗口的参数，参数之间用逗号分隔

## 关闭窗口

* window.close() 关闭本窗口
* <窗口对象>.close() 关闭指定窗口

## DOM

* 文档对象模型，DOM 将HTML文档呈现为带有元素、属性和文本的树结构（节点树）

## 通过id获取元素

* 通过ID找到标签然后操作
* document.getElementById(“id”)

## innerHTML属性

* 用于获取或替换 HTML 元素的内容
* Object.innerHTML
* Object：获取的元素对象，如通过document.getElementById(“ID”)获取的元素

```示例
<h2 id="con">javascript</H2>
<p> JavaScript是一种基于对象、事件驱动的简单脚本语言，嵌入在HTML文档中，由浏览器负责解释和执行，在网页上产生动态的显示效果并实现与用户交互功能。</p>
<script type="text/javascript">
  var mychar=document.getElementById("con");
  document.write("原标题:"+mychar.innerHTML+"<br>"); //输出原h2标签内容
  mychar.innerHTML="Hello world!"
  document.write("修改后的标题:"+mychar.innerHTML); //输出修改后h2标签内容
</script>
```

```示例
function getOption() {
  var ele = document.getElementById("ele");
  let lists = ["1","2","3","4","5"];
  var frag = document.createDocumentFragment(); // 创建文档片段，为了避免多次操作DOM
  for(let d of lists) {
    var opt = document.createElement("option");
    opt.value = d;
    opt.text = d;
    frag.appendChild(opt);  // 将设置好的option插入文档片段
  }
  ele.appendChild(frag);  // 循环结束后将文档片段插入指定Dom中
}
```

```示例
el.innerHTML;
el.innerHTML = '<p>新的html内容</p>';
```

### options数组的属性

* selectedIndex：select选中项option的index
* length：长度属性

### option的方法

* 增加一个option：add(new(“文本”, “值”))
* 删除一个option：remove(obj.selectedIndex)
* 获取一个option：obj.options[obj.selectedIndex].text
* 修改一个option：obj.options[obj.selectedIndex] = new Option(“新文本”, “值”)
* 删除所有option：obj.options.length = 0
* 获取一个option的值：obj.options[obj.selectedIndex].value

### 单个option的属性

* text：文本
* value：value属性值
* index：下标
* selected：是否被选中
* defaultSelected：默认是否被选中

## 改变HTML样式

* Object.style.property=new style
* Object是获取的元素对象，如通过document.getElementById(“id”)获取的元素

```示例
var mychar= document.getElementById("con");
mychar.style.color="#4caf50";
mychar.style.width="400px";
```

## 显示和隐藏display属性

* Object.style.display = value // (“none” 或 “block”)
* Object是获取的元素对象，如通过document.getElementById(“id”)获取的元素
* value是none(隐藏)，block(显示为块级元素)

## 原生js方法

* el.getAttribute(“class”) // 获取属性值
* el.setAttribute(“foo”, “node”) // 设置属性值
* el.removeAttribute(“foo”) // 删除属性
* document.documentElement.scrollHeight // Document高度

## 控制类名ClassName属性

* 设置或返回元素的class属性
* object.className = classname // (“className”)
* 可用来为网页内的某个元素指定一个css样式来更改该元素的外观

```示例
.one{
  color: red
}
p1.className="one";
```

## 自增减运算符

++a、a++、–a、a–  
区别：前自增减（++a、–a）是先用后加，后自增减（a++、a–）是先加后用

```示例
var a=6;
var i;
* i=a++; // i=6
* i=++a; // i=7
* i=a--; // i=6
* i=--a; // i=5
```

## 运算符优先级

* 算数运算符 > 比较运算符 > 逻辑运算符 > “=”赋值运算符
* 算术运算符：*、/ > +、-
* 改变优先级的方法：加括号

## 监听input输入事件

```示例
document.getElementById("myInput").oninput(function () {checkInput();});
function checkInput() {
  // to do something
}
```

```示例
$("[name='myInput']").on("input", function () {checkInput();});
function checkInput() {
  // to do something
}
```

## javascript数据类型转换

```类型转换
var num = 123;
// Number转为String
num.toString();
/*
  123转为'123'、'123'转为'123'、true转为'true'、false转为'false'、undefined转为'undefined'、null转为'null'、对象（{a:1}）转为'[object Object]'、数组([2,3,1])转为'2,3,1'
*/
var str = '23352';
// String转为Number
Number(str);
/*
  ''转为0、'2324abc'转为NaN、true转为1、false转为0、对象（{a:1}、[1,2,3]）转为NaN、单个数值的数组([2])转为2
*/
// 转为Boolean
Boolean(null);
/*
undefined、null、-0、+0、NaN、''转为false，其余全部转为true
```

## 判断一个String中是否只存在数字和‘、’

今天公司项目中有一个场景，定义一个class为js-addFaultInput的input，type是text，但是此处限制仅能输入单个number或者12、13形式的‘、’分隔的号码。

我的实现方式是使用jquery:

```实现过程
var ele = $(".js-addFaultInput").val(); // 获取输入的值
var list = ele.split("、");   // 分割为数组
var pramList = [];  // ajax请求时参数数组，int类型的数据才可以，且因为是仓库编号所以不能为小数
for(let i=0; i<list.length; i++) {
  var check = $.isNumeric(list[j]);  // jquery判断指定参数是否能转换为数字值的API
  if(check === true) {
    if(paramList.indexOf(Number(list[i])) === -1) {
      paramList.push(Number(list[i]));
    }
    /* 此后发现12.3等浮点数也可使check=true
    因此也能把Number('12.3')=12.3添加到参数数组中
    这是一个bug，之后就查找学习到了js的parseInt方法，
    此方法可用来转换为整数，默认10进制，因此把上面代码
    中的Number换为parseInt就可以满足要求了。
    */
  }
}
```

## 事件监听

```示例
$(".sel").change(function() {
  // to do something
})
```

但是有时select中的option需要从后端获取数据，然后通过数据来动态生成option(用js代码生成DOM)，此时监听select还是用以上方法的话就不可以，我猜测可能是监听与DOM插入的顺序问题吧（不清楚具体原因，待深入学习），但是此时可以通过：

```示例
$(".sel").on('click', function() {
  // to do something
})
```

## 给定一个数组对象，查找对象中有给定的属性值的对象

```过程
var placeId = 3;
var list = [{
  id: 123,
  name: '测试库'
}, {
  id: 148,
  name: '公司仓库'
}, {
  id: 123,
  name: '资方仓库'
}];
var idx = -1;
for(let i=0; i<list.length; i++) {
  if(list[i].id === placaId) {
    idx = i;
  }
}

// 使用for...of
var idx = -1;
for(let i of list) {
  if(i.id === placeId) {
    var self = i;
  }
}
idx = list.indexOf(self)
console.log(idx);

// 使用for...in
for(let i in list) {
  if(list[i].id === placeId) {
    var idx = i;
  }
}

// 使用find+indexOf，问题是find的函数中如何传入变量placeId？
var self = list.find(v => {
  return v.id === 123
})
idx = list.indexOf(self);
```
