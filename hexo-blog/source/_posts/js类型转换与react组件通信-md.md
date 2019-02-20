---
title: 'js类型转换与react组件通信'
date: 2019-2-20 18:00:39
tags: 月度总结
---

# js知识深入扩展与react二次理解

## 内置类型与判断

js分为7中内置类型，这7种内置类型又分为两大类：基本类型、对象。

基本类型(6种): null、undefined、boolean、number、string、symbol。

JS的数字类型是浮点型的，没有整型。NaN也属于number类型，并且NaN不等于自身。

### Typeof

对于基本类型，除了null之外都可使用typeof显示正确的类型。

```typeof判断基本类型
typeof 1  // 'number'
typeof '1'  // 'string'
typeof undefined  // 'undefined'
typeof true   // 'boolean'
typeof Symbol() // 'symbol'
```

对于对象，除了函数都会显示Object

```typeof判断对象
typeof [] // 'object'
typeof {} // 'object'
typeof console.log('111')   // 'function'
```

<!-- more -->

对于null，虽然null是基本类型，但是typeof null会显示object。原因：JS最初版本低位存储变量的类型信息，000开头代表的是对象，然而null标示为全零，所以会将它误判为object。

想要获得一个变量的正确类型，可以通过Object.prototype.toString.call(xxx)这样可以获得类似[object Type]的字符串。

还有两种特殊方式：

* instanceof

instanceof是用来判断A是否为B的实例，表达式为A instanceof B，如果A是B的实例则返回true，反之返回false，注意：instanceof检测的是原型，因此会出现以下现象：

```instanceof特殊现象
[] instanceof Array // true
[] instanceof Object  // true

原因：[] -> Array.prototype -> Object.prototype -> null
```

故instanceof只能用来判断两个对象是否属于实例关系，并不能判断一个对象实例具体属于哪种类型。

* constructor

此方式需了解学习过原型与原型链后再弥补，暂无详解。

判断一个对象a是否是数组：

* a instanceof Array
* Array.isArray(a)
* Object.prototype.toString.call(a)

### 类型转换

条件判断时，除<font color=#D2691E>undefined、null、false、NaN、''、0、-0</font>外，其他所有值都转换为true，包括所有对象。

对象转换基本类型时，会先调用valueOf，然后调用toString，并且这两个方法可以被重写。当然也可以重写Symbol.toPrimitive，该方法在转换基本类型时调用优先级最高。

![类型转换.png](https://upload-images.jianshu.io/upload_images/1393082-353cbff7f31e5d23.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 四则运算符

加法运算：其中一方是字符串类型，就会把另一个也转为字符串类型。会触发三种类型转换：将值转换为原始值，转换为数字，转换为字符串。

减法运算：其中一方是数字类型，就会把另一个也转为数字类型。

乘法运算：其中一方是数字类型，就会把另一个也转为数字类型。

除法运算：其中一方是数字类型，就会把另一个也转为数字类型。

## 原型与原型链、继承

_proto_属性、prototype：每个实例都有一个私有属性_proto_指向它的原形对象prototype。

即_proto_是从实例指向原型对象的私有属性，而prototype就是原型对象。

原型对象也有一个自己的原型对象，层层向上直到一个对象的原型是null(null没有原型，是原型链中的最后一个环节)。

```原型链解析
[] -> Array.prototype -> Object.prototype -> null

{} -> Object.prototype -> null

f() -> Function.prototype -> Object.prototype -> null

var a = {id: 1};
var b = Object.create(a);
b -> a -> Object.prototype -> null
```

## React中setState延迟

工作中遇到此种情况：主页有两个tab，tab分别对应一个数据列表(根据originList渲染列表)，这两个数据列表使用同一个变量page存储当前页数、同一个数组originList存储已经加载到数据对象，切换tab时从数据库按页加载当前tab状态下的数据，此时出现一个问题就是tab切换之后加载数据时状态并没有立即更改(tab状态切换是本组件内的事件，并未全局存储状态与更改)。

经过查找学习发现setState并不能保证更新是立即的，为了性能，React会把多个组件的更新放在一次操作里，因此我们可以把setState当作是请求更新组件，而不是立即更新组件。

## React中的context

vuex中context对象里面包含state、commit、dispatch，即可使用context.state.xxx来获取状态，使用context.commit(xxx)来触发mutation，可以使用context.dispatch来触发action。

react中，我们总是通过改变state和传递prop对view进行控制，但在组件嵌套很深时，如果要把数据从最外层传递到最里层，讲使用prop也是没问题的，但是会很麻烦，这时context就派上用场了，可以把context理解为跨层次传递，即不需要非得是父子组件之间传递。

使用方式:

```传递context数据的组件Test
const PropTypes = require('prop-types');

class Test extends Component {
  getChildContext() {
    return {color: "purple"};
  }
}

Test.childContextTypes = {
  color: PropTypes.string
}

export default Test;
```

```接收context数据的组件Button
const PropTypes = require('prop-types');

class Button extends Component {
  constructor(props, context) {
    super(props, context);
    console.log(this.context.color);
  }

  render() {
    return (
      <div className="button">
        <button style={{background: this.context.color}}>按钮</button>
      </div>
    )
  }
}

Button.contextTypes = {
  color: PropTypes.string
}

export default Button;
```

注：context可以使 子组件 直接访问 祖先组件 的数据或者函数。

## React中的组件沟通

### 父子组件沟通

父组件更新组件状态 --> 通过传递props更新子组件状态。

父组件传递回调函数给子组件 --> 子组件调用触发 --> 父组件更新组件状态。

示例：

```父子组件沟通
class Child extends React.Component{
  constructor(props){
    super(props);
    this.state = {}
  }
  
  render(){
    return (
      <div>
        {this.props.text}
        <br />
        <button onClick={this.props.refreshParent}>
            更新父组件
        </button>
      </div>
    )
  }
}
class Parent extends React.Component{
  constructor(props){
    super(props);
    this.state = {}
  }
  refreshChild(){
    return (e)=>{
      this.setState({
        childText: "父组件沟通子组件成功",
      })
    }
  }
  refreshParent(){
    this.setState({
      parentText: "子组件沟通父组件成功",
    })
  }
  render(){
    return (
      <div>
        <h1>父子组件沟通</h1>
        <button onClick={this.refreshChild()} >
            更新子组件
        </button>
        <Child text={this.state.childText || "子组件未更新"} refreshParent={this.refreshParent.bind(this)}/>
        {this.state.parentText || "父组件未更新"}
      </div>
    )
  }
}
```

### 兄弟组件沟通

方式一： 通过props传递父组件回调函数。

方式二：使用context达到沟通效果。

```context组件沟通
class Brother1 extends React.Component{
  constructor(props){
    super(props);
    this.state = {}
  }
  
  render(){
    return (
      <div>
        <button onClick={this.context.refresh}>
            更新兄弟组件
        </button>
      </div>
    )
  }
}
Brother1.contextTypes = {
  refresh: React.PropTypes.any
}
class Brother2 extends React.Component{
  constructor(props){
    super(props);
    this.state = {}
  }
  
  render(){
    return (
      <div>
         {this.context.text || "兄弟组件未更新"}
      </div>
    )
  }
}
Brother2.contextTypes = {
  text: React.PropTypes.any
}
class Parent extends React.Component{
  constructor(props){
    super(props);
    this.state = {}
  }
  
  getChildContext(){
    return {
      refresh: this.refresh(),
          text: this.state.text,
      }
    }
  
  refresh(){
    return (e)=>{
      this.setState({
        text: "兄弟组件沟通成功",
      })
    }
  }
  render(){
    return (
      <div>
        <h2>兄弟组件沟通</h2>
        <Brother1 />
        <Brother2 text={this.state.text}/>
      </div>
    )
  }
}
Parent.childContextTypes = {
  refresh: React.PropTypes.any,
  text: React.PropTypes.any,
}
```

## 疑难点

* 是否有做到同步setState效果的方法？
* context中PropType的深入了解。
* 异步action
