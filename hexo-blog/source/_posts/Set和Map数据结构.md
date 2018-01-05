---
title: Set和Map数据结构
date: 2017-05-27 23:03:36
tags:
---

# 一. Set
## 基本语法
Set类似数组，成员唯一，是一组key的集合，但不存储value。
要创建一个Set，需要提供一个Array作为输入，或者直接创建一个空Set：
` var s1=new Set();  //空Set
  var s2=new Set([1,2,3]); 
  添加成员：s1.add(key);  //可以重复添加，但是不会有效果
  删除成员：s2.delete(key);  `
<!-- more -->
Set本身是一个构造函数，可用来生成Set数据结构：Set 函数可以接受一个数组（或类似数组的对象）作为参数，用来初始化。
接受数组作为参数：
` const set = new Set([1, 2, 3, 4, 4]);
  [...set]  //[1,2,3,4] `
接受类似数组的对象作为参数：
` function divs () {
   return [...document.querySelectorAll('div')];
 }
 const set = new Set(divs()); `

Set可以当做去除数组中重复的成员的方法：
` [...new Set(array)] `

向Set加入值时，不会发生类型转换，Set内部使用“Same-value equality”算法判断两个值是否相等，它类似于精确相等运算符（===），主要的区别是精确相等运算符（===）认为NaN不等于自身，而它认为NaN等于自身。
因此：在 Set 内部，两个NaN是相等。
但是：两个对象总是不相等的，两个空对象不相等，它们被视为两个值。

## Set实例的属性和方法
Set 结构的实例有以下属性：
1. Set.prototype.constructor：构造函数，默认就是Set函数。
2. Set.prototype.size：返回Set实例的成员总数。

Set 实例的方法分为两大类：
1. 操作方法（用于操作数据）
2. 遍历方法（用于遍历成员）

操作方法（4个）：
1. add(value)：添加某个值，返回Set结构本身。
2. delete(value)：删除某个值，返回一个布尔值，表示删除是否成功。
3. has(value)：返回一个布尔值，表示该值是否为Set的成员。
4. clear()：清除所有成员，没有返回值。

判断是否包括一个键上面，对象的写法：
` const s1 = {        
  'width': 1,
  'height': 1
  };
  if (s1[someName]) {
    // do something
  } `
判断是否包括一个键上面，Set的写法：
`  const s2 = new Set();
  s2.add('width');
  s2.add('height');
  if (s2.has(someName)) {
    // do something
  } 
`
Array.from方法可以将 Set 结构转为数组：
` const items = new Set([1, 2, 3, 4, 5]);
const array = Array.from(items); `
去除数组重复成员的另一种方法：
` function dedupe(array) {
  return Array.from(new Set(array));
}
dedupe([1, 1, 2, 3])  // [1, 2, 3] `

##遍历操作
Set 结构的实例有四个遍历方法，可以用于遍历成员：
1. keys()：返回键名的遍历器
2. values()：返回键值的遍历器
3. entries()：返回键值对的遍历器
4. forEach()：使用回调函数遍历每个成员
注：Set的遍历顺序就是插入顺序
###1. keys()，values()，entries()
keys方法、values方法、entries方法返回的都是遍历器对象。由于 Set 结构没有键名，只有键值（或者说键名和键值是同一个值），所以keys方法和values方法的行为完全一致。

keys()：
` let set = new Set(['A', 'B', 'C']);
for (let item of set.keys()) {
  console.log(item);
}  //A //B //C `
values()：
` let set = new Set(['A', 'B', 'C']);
for (let item of set.values()) {
  console.log(item);
}  //A  //B //C `
entries()：
` let set = new Set(['A', 'B', 'C']);
for (let item of set.entries()) {
  console.log(item);
}  //["A" , "A"]  //["B" , "B"] //["C" , "C"] `

Set 结构的实例默认可遍历，它的默认遍历器生成函数就是它的values方法，这意味着，可以省略values方法，直接用for...of循环遍历 Set。
` let set = new Set(['A', 'B', 'C']);
for (let x of set) {
  console.log(x);
}  //A //B //C `

### 2. forEach()
Set结构的实例的forEach方法，用于对每个成员执行某种操作，没有返回值。
forEach方法的参数就是一个处理函数。该函数的参数依次为键值、键名、集合本身（可以省略该参数）。另外，forEach方法还可以有第二个参数，表示绑定的this对象。
` let set = new Set([1, 2, 3]);
set.forEach((value, key) => console.log(value * 2) )  //2 //4 //6 `

### 3. 遍历的应用
扩展运算符（...）内部使用for...of循环可以用于 Set 结构：
` let set = new Set(['A', 'B', 'C']);
  let arr = [...set];
  // ["A" , "B" , "C"] `
扩展运算符和 Set 结构相结合，就可以去除数组的重复成员：
` let arr = [3, 5, 2, 2, 5, 5];
let unique = [...new Set(arr)];
// [3, 5, 2] `
数组的map和filter方法也可以用于 Set ：
` let set = new Set([1, 2, 3]);
set = new Set([...set].map(x => x * 2));
// 返回Set结构：{2, 4, 6}
let set = new Set([1, 2, 3, 4, 5]);
set = new Set([...set].filter(x => (x % 2) == 0));
// 返回Set结构：{2, 4} `
使用 Set 可以很容易地实现并集（Union）：
` let a = new Set([1, 2, 3]);
let b = new Set([4, 3, 2]);
let union = new Set([...a, ...b]);
// Set {1, 2, 3, 4} `
交集（Intersect）：
` let a = new Set([1, 2, 3]);
let b = new Set([4, 3, 2]);
let intersect = new Set([...a].filter(x => b.has(x)));
// set {2, 3}  `
差集（Difference）：
` let a = new Set([1, 2, 3]);
let b = new Set([4, 3, 2]);
let difference = new Set([...a].filter(x => !b.has(x)));
// Set {1} `

在遍历操作中，同步改变原来的 Set 结构，没有直接方法，但是有两种变通方法：
1. 利用原 Set 结构映射出一个新的结构，然后赋值给原来的 Set 结构:
` let set = new Set([1, 2, 3]);
set = new Set([...set].map(val => val * 2));
// set的值是2, 4, 6 `
2. 利用Array.from方法:
` let set = new Set([1, 2, 3]);
set = new Set(Array.from(set, val => val * 2));
// set的值是2, 4, 6 `

# 二. WeakSet
### 含义
WeakSet 结构与 Set 类似，也是不重复的值的集合。
但是，它与 Set 有两个区别：
1. WeakSet 的成员只能是对象，而不能是其他类型的值。
` var foo='bar';
const ws = new WeakSet();
ws.add({foo})
//WeakSet {Object {foo: "bar"}} `
2. WeakSet 中的对象都是弱引用，即垃圾回收机制不考虑 WeakSet 对该对象的引用，也就是说，如果其他对象都不再引用该对象，那么垃圾回收机制会自动回收该对象所占用的内存，不考虑该对象还存在于 WeakSet 之中

ES6 规定 WeakSet 不可遍历（原因）：
垃圾回收机制依赖引用计数，一个值的引用次数不为0，垃圾回收机制就不会释放这块内存，WeakSet 里面的引用，都不计入垃圾回收机制，因此WeakSet 适合临时存放一组对象，以及存放跟对象绑定的信息。只要这些对象在外部消失，它在 WeakMap 里面的引用就会自动消失。
1. 它会随时消失
2.  WeakSet 内部有多少个成员，取决于垃圾回收机制有没有运行，运行前后很可能成员个数是不一样的，而垃圾回收机制何时运行是不可预测的。

### 语法
WeakSet 是一个构造函数，可以使用new命令，创建 WeakSet 数据结构。
` const ws = new WeakSet(); `
作为构造函数，WeakSet 可以接受一个数组或类似数组的对象作为参数。（实际上，任何具有 Iterable 接口的对象，都可以作为 WeakSet 的参数。）该数组的所有成员，都会自动成为 WeakSet 实例对象的成员。
` const a = [[1, 2], [3, 4]];
const ws = new WeakSet(a);
// WeakSet {[1, 2], [3, 4]} `
注意：是a数组的成员(2个，也是数组)成为 WeakSet 的成员，而不是a数组本身。这意味着，数组的成员只能是对象。
` var foo='foo';                                                                       
 const a = [{foo}, {bar}];
 const s1 = new WeakSet(a);
 s1
//WeakSet {Object {bar: "bar"}, Object {foo: "foo"}} `

WeakSet 结构有以下三个方法：
1. WeakSet.prototype.add(value)：向 WeakSet 实例添加一个新成员。
` const ws = new WeakSet();
ws.add(window); `
2. WeakSet.prototype.delete(value)：清除 WeakSet 实例的指定成员。
` const ws = new WeakSet();
ws.delete(window); `
3. WeakSet.prototype.has(value)：返回一个布尔值，表示某个值是否在 WeakSet 实例之中。
` const ws = new WeakSet();
ws.has(window);   // false `

WeakSet没有size属性，没有办法遍历它的成员。获取size和forEach属性，结果都不能成功。
` ws.size // undefined
ws.forEach // undefined `

WeakSet 不能遍历，是因为成员都是弱引用，随时可能消失，遍历机制无法保证成员的存在，很可能刚刚遍历结束，成员就取不到了。WeakSet 的一个用处，是储存 DOM 节点，而不用担心这些节点从文档移除时，会引发内存泄漏。

# 三. Map
## 含义和基本用法
JavaScript 的对象（Object），本质上是键值对的集合（Hash 结构），但是传统上只能用字符串当作键，Object 结构提供了“字符串—值”的对应。
ES6 提供的 Map 数据结构：它类似于对象，也是键值对的集合，但是各种类型的值（包括对象）都可以当作键。Map结构提供了“值—值”的对应，是一种更完善的 Hash 结构实现。如果你需要“键值对”的数据结构，Map 比 Object 更合适。

` const m = new Map();
const o = {p: 'Hello World'}; `
Map 结构的set方法，将对象o当作m的一个键：
` m.set(o, 'content') `
使用get方法读取这个键：
` m.get(o) // "content"   `
使用has方法查询这个键是否存在：
` m.has(o) // true `
 使用delete方法删除了这个键：
` m.delete(o) // true `


作为构造函数，Map 也可以接受一个数组作为参数。该数组的成员是一个个表示键值对的数组：
` const map = new Map([
  ['name', '张三'],
  ['title', 'Author']
]); `
实际上执行的是下面的算法：
` const items = [
  ['name', '张三'],
  ['title', 'Author']
];
const map = new Map();
items.forEach(
  ([key, value]) => map.set(key, value)
); `

事实上，不仅仅是数组，任何具有 Iterator 接口的数据结构都可以当作Map构造函数的参数。即Set和Map都可以用来生成新的 Map。

使用Set 对象当作Map构造函数的参数新的 Map 对象：
` const set = new Set([
  ['foo', 1],
  ['bar', 2]
]);
const m1 = new Map(set);
m1.get('foo') // 1 `
使用Map 对象当作Map构造函数的参数新的 Map 对象：
` const m2 = new Map([['baz', 3]]);
const m3 = new Map(m2);
m3.get('baz') // 3 `
对同一个键多次赋值，后面的值将覆盖前面的值：
` const map = new Map();
map
.set(1, 'aaa')
.set(1, 'bbb');
map.get(1) // "bbb" `
读取一个未知的键，则返回undefined：
` new Map().get('asfddfsasadf')
// undefined `

注意：
1. 只有对同一个对象的引用，Map 结构才将其视为同一个键。
2. 同样的值的两个实例，在 Map 结构中被视为两个键，Map 的键实际上是跟内存地址绑定的，只要内存地址不一样，就视为两个键。
3. 如果 Map 的键是一个简单类型的值（数字、字符串、布尔值），则只要两个值严格相等，Map 将其视为一个键，包括0和-0。
4. 布尔值true和字符串true则是两个不同的键。
5. undefined和null也是两个不同的键。虽然NaN不严格相等于自身，但 Map 将其视为同一个键。

## Map 结构的实例的属性和操作方法。
### 1. size属性
返回 Map 结构的成员总数。
### 2.set(key, value)
设置键名key对应的键值为value，然后返回整个 Map 结构。如果key已经有值，则键值会被更新，否则就新生成该键。
返回的是当前的Map对象，因此可以采用链式写法：
` let map = new Map()
  .set(1, 'a')
  .set(2, 'b')
  .set(3, 'c'); `
### 3. get(key)
读取key对应的键值，如果找不到key，返回undefined。
### 4. has(key)
返回一个布尔值，表示某个键是否在当前 Map 对象之中。
### 5. delete(key)
删除某个键，删除成功返回true，删除失败返回false。
### 6. clear()
clear方法清除所有成员，没有返回值。

## 遍历方法
Map 结构原生提供三个遍历器生成函数和一个遍历方法：
1. keys()：返回键名的遍历器。
2. values()：返回键值的遍历器。
3. entries()：返回所有成员的遍历器。
4. forEach()：遍历 Map 的所有成员。
需要特别注意的是，Map 的遍历顺序就是插入顺序。
Map 结构的默认遍历器接口（Symbol.iterator属性），就是entries方法。
` map[Symbol.iterator] === map.entries
// true `
Map 结构转为数组结构，比较快速的方法是使用扩展运算符（...）。
` [...map.keys()]
[...map.values()]
[...map.entries()]
[...map] `
结合数组的map方法、filter方法，可以实现 Map 的遍历和过滤（Map 本身没有map和filter方法）。

Map 的forEach方法，与数组的forEach方法类似，也可以实现遍历。
` map.forEach(function(value, key, map) {
  console.log("Key: %s, Value: %s", key, value);
}); `
forEach方法还可以接受第二个参数，用来绑定this。
` const reporter = {
  report: function(key, value) {
    console.log("Key: %s, Value: %s", key, value);
  }
};
map.forEach(function(value, key, map) {
  this.report(key, value);
}, reporter); `
forEach方法的回调函数的this，就指向reporter
## 与其它数据结构的互相转换
### 1. Map转为数组
Map 转为数组最方便的方法，就是使用扩展运算符（...）：
` [...map.keys()]
[...map.values()]
[...map.entries()]
[...map] `

### 2. 数组转为Map 
将数组传入 Map 构造函数，就可以转为 Map：
` new Map([
  [true, 7],
  [{foo: 3}, ['abc']]
]) `

### 3. Map转为对象
如果所有 Map 的键都是字符串，它可以转为对象：
` function strMapToObj(strMap) {
  let obj = Object.create(null);
  for (let [k,v] of strMap) {
    obj[k] = v;
  }
  return obj;
}
const myMap = new Map()
  .set('yes', true)
  .set('no', false);
strMapToObj(myMap)
// { yes: true, no: false } `

### 4. 对象转为Map
` function objToStrMap(obj) {
  let strMap = new Map();
  for (let k of Object.keys(obj)) {
    strMap.set(k, obj[k]);
  }
  return strMap;
}
objToStrMap({yes: true, no: false})
// Map {"yes" => true, "no" => false} `

### 5. Map转为JSON
1. Map 的键名都是字符串，这时可以选择转为对象 JSON：
` function strMapToJson(strMap) {
  return JSON.stringify(strMapToObj(strMap));
}
let myMap = new Map().set('yes', true).set('no', false);
strMapToJson(myMap)
// '{"yes":true,"no":false}' `
2. Map 的键名有非字符串，这时可以选择转为数组 JSON：
` function mapToArrayJson(map) {
  return JSON.stringify([...map]);
}
let myMap = new Map().set(true, 7).set({foo: 3}, ['abc']);
mapToArrayJson(myMap)
// '[[true,7],[{"foo":3},["abc"]]]' `

### 6. JSON转为Map
SON 转为 Map，正常情况下，所有键名都是字符串。
` function jsonToStrMap(jsonStr) {
  return objToStrMap(JSON.parse(jsonStr));
}
jsonToStrMap('{"yes": true, "no": false}')
// Map {'yes' => true, 'no' => false} `
有一种特殊情况，整个 JSON 就是一个数组，且每个数组成员本身，又是一个有两个成员的数组。这时，它可以一一对应地转为Map。这往往是数组转为 JSON 的逆操作。
` function jsonToMap(jsonStr) {
  return new Map(JSON.parse(jsonStr));
}
jsonToMap('[[true,7],[{"foo":3},["abc"]]]')
// Map {true => 7, Object {foo: 3} => ['abc']} `

# 4. WeakMap
## 含义
WeakMap结构与Map结构类似，也是用于生成键值对。
` const wm1 = new WeakMap();      //创建WeakMap
  const key = {foo: 1};
  wm1.set(key, 2);                 //添加成员
  wm1.get(key)                     //获取成员  
`
WeakMap 也可以接受一个数组，作为构造函数的参数
` const k1 = [1, 2, 3];
const k2 = [4, 5, 6];
const wm2 = new WeakMap([[k1, 'foo'], [k2, 'bar']]);
wm2.get(k2) // "bar" `

WeakMap与Map的区别（两点）：
1. WeakMap只接受对象作为键名（null除外），不接受其他类型的值作为键名。
` const map = new WeakMap();
map.set({foo:1}, 2)    // WeakMap {Object {foo: 1} => 2} `
2. WeakMap的键名所指向的对象，不计入垃圾回收机制。

一旦不再需要，WeakMap 里面的键名对象和所对应的键值对会自动消失，不用手动删除引用。
如果你要往对象上添加数据，又不想干扰垃圾回收机制，就可以使用 WeakMap。WeakMap的专用场合就是，它的键所对应的对象，可能会在将来消失。WeakMap结构有助于防止内存泄漏。

注意，WeakMap 弱引用的只是键名，而不是键值。键值依然是正常引用:
` const wm = new WeakMap();
let key = {};
let obj = {foo: 1};
wm.set(key, obj);
obj = null;
wm.get(key)
// Object {foo: 1} `

## WeakMap 的语法
WeakMap 与 Map 在 API 上的区别（2个）：
1. 没有遍历操作（即没有key()、values()和entries()方法），也没有size属性。因为没有办法列出所有键名，这个键名是否存在完全不可预测，跟垃圾回收机制是否运行相关。
2. 无法清空，即不支持clear方法。因此，WeakMap只有四个方法可用：get()、set()、has()、delete()。

## WeakMap 的用途
WeakMap 应用的典型场合就是 DOM 节点作为键名
WeakMap 的另一个用处是部署私有属性
