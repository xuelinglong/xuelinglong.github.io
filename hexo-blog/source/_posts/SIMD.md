---
title: SIMD
date: 2017-05-30 22:18:54
tags:
---

SIMD：
意为“单指令，多数据”；
含义是使用一个指令，完成多个数据的运算；
它是 JavaScript 操作 CPU 对应指令的接口；
可以看做这是一种不同的运算执行模式；

SISD：
意为“单指令，单数据”；
含义是使用一个指令，完成单个数据的运算；
是 JavaScript 的默认运算模式。

SIMD 的执行效率要高于 SISD，所以被广泛用于3D图形运算、物理模拟等运算量超大的项目之中。
<!-- more -->

SIMD 运算示例：
` var a = SIMD.Float32x4(1, 2, 3, 4);
var b = SIMD.Float32x4(5, 6, 7, 8);
var c = SIMD.Float32x4.add(a, b); // Float32x4[6, 8, 10, 12] `
一次 SIMD 运算，可以处理多个数据，这些数据被称为“通道”（lane）,示例中运算的四个数据就是四个通道。

SIMD 通常用于矢量运算。
  ` v + w    = 〈v1, …, vn〉+ 〈w1, …, wn〉
      = 〈v1+w1, …, vn+wn〉  `
v和w是两个多元矢量。

SIMD 是数据并行处理（parallelism）的一种手段，可以加速一些运算密集型操作的速度。将来与 WebAssembly 结合以后，可以让 JavaScript 达到二进制代码的运行速度。

# 二. 数据类型
SIMD 提供12种数据类型，总长度都是128个二进制位。
通道类型，宽度x通道数
1. Float32x4：四个32位浮点数
2. Float64x2：两个64位浮点数
3. Int32x4：四个32位整数
4. Int16x8：八个16位整数
5. Int8x16：十六个8位整数
6. Uint32x4：四个无符号的32位整数
7. Uint16x8：八个无符号的16位整数
8. Uint8x16：十六个无符号的8位整数
9. Bool32x4：四个32位布尔值
10. Bool16x8：八个16位布尔值
11. Bool8x16：十六个8位布尔值
12. Bool64x2：两个64位布尔值
每种数据类型被x符号分隔成两部分，后面的部分表示通道数，前面的部分表示每个通道的宽度和类型。


每个通道之中，可以放置四种数据：
1. 浮点数（float，比如1.0）
2. 带符号的整数（Int，比如-1）
3. 无符号的整数（Uint，比如1）
4. 布尔值（Bool，包含true和false两种值）

每种 SIMD 的数据类型都是一个函数方法，可以传入参数，生成对应的值。
` var a = SIMD.Float32x4(1.0, 2.0, 3.0, 4.0); `
变量a就是一个128位、包含四个32位浮点数（即四个通道）的值。

注意：这些数据类型方法都不是构造函数，前面不能加new。


# 三. 静态方法：数学运算
每种数据类型都有一系列运算符，支持基本的数学运算。
参数为SIMD值，返回运算后的新的SIMD值

### SIMD.%type%.abs() ，SIMD.%type%.neg() 

SIMD.%type%.abs()：1个参数，每个通道都转成绝对值。
示例：
` var a = SIMD.Float32x4(-1, -2, 0, NaN);      //生成一个SIMD值
SIMD.Float32x4.abs(a)     //接受生成的这个SIMD值为参数
// Float32x4[1, 2, 0, NaN] `

SIMD.%type%.neg() ：1个参数，每个通道都转成负值。
` var a = SIMD.Float32x4(-1, -2, 3, 0);
SIMD.Float32x4.neg(a)
// Float32x4[1, 2, -3, -0] `


### SIMD.%type%.add() ，SIMD.%type%.addSaturate() 
SIMD.%type%.add() ：2个参数，每个通道相加，如果两个值相加发生溢出，溢出的二进制位会被丢弃。

SIMD.%type%.addSaturate() ：2个参数，每个通道相加，如果两个值相加时发生溢出时addSaturate方法则是返回该数据类型的最大值。
示例：
` var a = SIMD.Uint16x8(65533, 65534, 65535, 65535, 1, 1, 1， 1);
var b = SIMD.Uint16x8(1, 1, 1, 5000, 1, 1, 1, 1);
SIMD.Uint16x8.addSaturate(a, b);
// Uint16x8[65534, 65535, 65535, 65535, 2, 2, 2, 2] `
解释：unit16的最大值为65535所以溢出后返回最大值65535.

注意：Uint32x4和Int32x4这两种数据类型没有addSaturate方法。

### SIMD.%type%.sub() ，SIMD.%type%.subSaturate() 
SIMD.%type%.sub()：2个参数，每个通道相减，如果两个值相减发生溢出，溢出的二进制位会被丢弃。

SIMD.%type%.subSaturate() ：2个参数，每个通道相减，如果两个值相减时发生溢出时subSaturate方法则是返回该数据类型的最小值。
示例：
` var c = SIMD.Int16x8(-100, 0, 0, 0, 0, 0, 0, 0);
var d = SIMD.Int16x8(32767, 0, 0, 0, 0, 0, 0, 0);
SIMD.Int16x8.subSaturate(c, d)
// Int16x8[-32768, 0, 0, 0, 0, 0, 0, 0, 0] `
解释：Int16的最小值是-32678所以溢出后返回最小值-32768.


### SIMD.%type%.mul() , SIMD.%type%.div() , SIMD.%type%.sqrt()

SIMD.%type%.mul()：2个参数，每个通道相乘。
SIMD.%type%.div()：2个参数，每个通道相除。
SIMD.%type%.sqrt()：1个参数，求出每个通道的平方根。
示例：
` var b = SIMD.Float64x2(4, 8);
SIMD.Float64x2.sqrt(b)
// Float64x2[2, 2.8284271247461903] `

### SIMD.%FloatType%.reciprocalApproximation() , SIMD.%Type%.reciprocalSqrtApproximation()

SIMD.%FloatType%.reciprocalApproximation()：1个参数，求出每个通道的倒数（1 / x）。

SIMD.%Type%.reciprocalSqrtApproximation()：1个参数，求出每个通道的平方根的倒数（1 / (x^0.5)）。

注意，只有浮点数的数据类型才有这两个方法。


### SIMD.%IntegerType%.shiftLeftByScalar()

SIMD.%IntegerType%.shiftLeftByScalar()：1个参数，将每个通道的值左移指定的位数。如果左移后，新的值超出了当前数据类型的位数，溢出的部分会被丢弃。
示例：
` var a = SIMD.Int32x4(1, 2, 4, 8);
SIMD.Int32x4.shiftLeftByScalar(a, 1);   
// Int32x4[2, 4, 8, 16]
var jx4 = SIMD.Int32x4.shiftLeftByScalar(ix4, 32);   //有溢出
// Int32x4[0, 0, 0, 0] ` 
注意：只有整数的数据类型才有这个方法。


### SIMD.%IntegerType%.shiftRightByScalar()

SIMD.%IntegerType%.shiftRightByScalar()：1个参数，将每个通道的值右移指定的位数。如果原来通道的值是带符号的值，则符号位保持不变，不受右移影响。如果是不带符号位的值，则右移后头部会补0。
示例：
var a = SIMD.Int32x4(1, 2, 4, -8);
SIMD.Int32x4.shiftRightByScalar(a, 1);
// Int32x4[0, 1, 2, -4]
SIMD.Uint32x4.shiftRightByScalar(a, 1);
// Uint32x4[0, 1, 2, 2147483644]
解释：对于32位无符号整数来说，-8的二进制形式是11111111111111111111111111111000，右移一位就变成了01111111111111111111111111111100，相当于2147483644。

注意：只有整数的数据类型才有这个方法。


# 四. 静态方法：通道处理

### SIMD.%type%.check()

SIMD.%type%.check()：用于检查一个值是否为当前类型的SIMD值。如果是的，就返回这个值，否则就报错。
示例：
` var a = SIMD.Float32x4(1, 2, 3, 9);
SIMD.Float32x4.check(a);
// Float32x4[1, 2, 3, 9]
SIMD.Float32x4.check([1,2,3,4]) // 报错
SIMD.Int32x4.check(a) // 报错
SIMD.Int32x4.check('hello world') // 报错 `

### SIMD.%type%.extractLane() , SIMD.%type%.replaceLane()

SIMD.%type%.extractLane()：用于返回给定通道的值。它接受两个参数，分别是SIMD值和通道编号。
示例：
` var t = SIMD.Float32x4(1, 2, 3, 4);
SIMD.Float32x4.extractLane(t, 2) // 3 `

SIMD.%type%.replaceLane()：用于替换指定通道的值，并返回一个新的SIMD值。它接受三个参数，分别是原来的SIMD值、通道编号和新的通道值。
示例：
` var t = SIMD.Float32x4(1, 2, 3, 4);
SIMD.Float32x4.replaceLane(t, 2, 42)
// Float32x4[1, 2, 42, 4]   `


### SIMD.%type%.load()

SIMD.%type%.load()：用于从二进制数组读入数据，生成一个新的SIMD值。
load方法接受两个参数：一个二进制数组和开始读取的位置（从0开始）。如果位置不合法（比如-1或者超出二进制数组的大小），就会抛出一个错误。
示例：
` var b = new Int32Array([1,2,3,4,5,6,7,8]);
SIMD.Int32x4.load(a, 2);
// Int32x4[3, 4, 5, 6] `

该方法还有三个变种:
load1()、load2()、load3()，表示从指定位置开始，只加载一个通道、二个通道、三个通道的值。
示例：
` var a = new Int32Array([1,2,3,4,5,6,7,8]);
SIMD.Int32x4.load1(a, 0);
// Int32x4[1, 0, 0, 0]
SIMD.Int32x4.load2(a, 0);
// Int32x4[1, 2, 0, 0]
SIMD.Int32x4.load3(a, 0);
// Int32x4[1, 2, 3,0] `


### SIMD.%type%.store()

SIMD.%type%.store()：用于将一个SIMD值，写入一个二进制数组。
store方法接受三个参数，分别是二进制数组、开始写入的数组位置、SIMD值。
store方法返回写入值以后的二进制数组。
示例：
` var t2 = new Int32Array(8);
var v2 = SIMD.Int32x4(1, 2, 3, 4);
SIMD.Int32x4.store(t2, 2, v2)    //从索引2位置开始写入
// Int32Array[0, 0, 1, 2, 3, 4, 0, 0] `

该方法还有三个变种：
store1()、store2()和store3()，表示只写入一个通道、二个通道和三个通道的值。
示例：
`  var tarray = new Int32Array(8);
var value = SIMD.Int32x4(1, 2, 3, 4);
SIMD.Int32x4.store1(tarray, 0, value);
// Int32Array[1, 0, 0, 0, 0, 0, 0, 0] `


### SIMD.%type%.splat()

SIMD.%type%.splat()：返回一个新的SIMD值，该值的所有通道都会设成同一个预先给定的值。
示例：
` SIMD.Float64x2.splat(3);
// Float64x2[3, 3] `

如果省略参数，所有整数型的SIMD值都会设定0，浮点型的SIMD值都会设成NaN。


### SIMD.%type%.swizzle()

SIMD.%type%.swizzle()：返回一个新的SIMD值，重新排列原有的SIMD值的通道顺序。
swizzle方法除了第一个参数以外，最多还可以接受16个参数。
第一个参数是原有的SIMD值，，后面的参数对应将要返回的SIMD值的n个通道。
示例：
` var a = SIMD.Float32x4(1.0, 2.0, 3.0, 4.0);
var b = SIMD.Float32x4.swizzle(a, 0, 0, 1, 1);
// Float32x4[1.0, 1.0, 2.0, 2.0] `
 

### SIMD.%type%.shuffle()

SIMD.%type%.shuffle()：从两个SIMD值之中取出指定通道，返回一个新的SIMD值。
示例：
` var a = SIMD.Float32x4(1, 2, 3, 4);      //a在前，0-3通道 
var b = SIMD.Float32x4(5, 6, 7, 8);       //b在后，4-7通道
SIMD.Float32x4.shuffle(a, b, 1, 5, 7, 2);
// Float32x4[2, 6, 8, 3] `
 
# 五. 静态方法：比较运算
### SIMD.%type%.equal() , SIMD.%type%.notEqual()

SIMD.%type%.equal()：用来比较两个SIMD值a和b的每一个通道，根据两者是否精确相等（a === b），得到一个布尔值。最后，所有通道的比较结果，组成一个新的SIMD值，作为掩码返回。

SIMD.%type%.notEqual()：比较两个通道是否不相等（a !== b），得到一个布尔值。最后，所有通道的比较结果，组成一个新的SIMD值，作为掩码返回。


### SIMD.%type%.greaterThan() , SIMD.%type%.greaterThanOrEqual()

SIMD.%type%.greaterThan()：用来比较两个SIMD值a和b的每一个通道，如果在该通道中，a较大就得到true，否则得到false。最后，所有通道的比较结果，组成一个新的SIMD值，作为掩码返回。

SIMD.%type%.greaterThanOrEqual()：比较a是否大于等于b。最后，所有通道的比较结果，组成一个新的SIMD值，作为掩码返回。


### SIMD.%type%.lessThan() , SIMD.%type%.lessThanOrEqual()

SIMD.%type%.lessThan()：用来比较两个SIMD值a和b的每一个通道，如果在该通道中，a较小就得到true，否则得到false。最后，所有通道的比较结果，会组成一个新的SIMD值，作为掩码返回。

SIMD.%type%.lessThanOrEqual()：比较a是否小于等于b。最后，所有通道的比较结果，会组成一个新的SIMD值，作为掩码返回。


### SIMD.%type%.select()

SIMD.%type%.select()：通过掩码生成一个新的SIMD值。
select方法接受三个参数，分别是掩码和两个SIMD值。
当某个通道对应的掩码为true时，会选择第一个SIMD值的对应通道，否则选择第二个SIMD值的对应通道。

这个方法通常与比较运算符结合使用。
示例：
` var a = SIMD.Float32x4(0, 12, 3, 4);
var b = SIMD.Float32x4(0, 6, 7, 50);
var mask = SIMD.Float32x4.lessThan(a,b);  
// Bool32x4[false, false, true, true]
var result = SIMD.Float32x4.select(mask, a, b);
// Float32x4[0, 6, 3, 4] `
先通过lessThan方法生成一个掩码，然后通过select方法生成一个由每个通道的较小值组成的新的SIMD值。



### SIMD.%Booleantype%.allTrue() , SIMD.%Booleantype%.anyTrue()

SIMD.%Booleantype%.allTrue() ：1个参数，返回一个布尔值，表示该SIMD值的所有通道是否都为true。
` var a = SIMD.Bool32x4(true, true, true, true);
SIMD.Bool32x4.allTrue(a); // true `

SIMD.%Booleantype%.anyTrue()：1个参数，只要有一个通道为true，就返回true，否则返回false。
` var b = SIMD.Bool32x4(false, false, true, false);
SIMD.Bool32x4.anyTrue(b); // true `

注意，只有四种布尔值数据类型（Bool32x4、Bool16x8、Bool8x16、Bool64x2）才有这两个方法。

这两个方法通常与比较运算符结合使用。
示例：
` var ax4    = SIMD.Float32x4(1.0, 2.0, 3.0, 4.0);
var bx4    = SIMD.Float32x4(0.0, 6.0, 7.0, 8.0);
var ix4    = SIMD.Float32x4.lessThan(ax4, bx4);
var b1     = SIMD.Int32x4.allTrue(ix4); // false
var b2     = SIMD.Int32x4.anyTrue(ix4); // true `


### SIMD.%type%.min() , SIMD.%type%.minNum()

SIMD.%type%.min()：2个参数，将两者的对应通道的较小值，组成一个新的SIMD值返回。如果有一个通道的值是NaN，则会优先返回NaN。


SIMD.%type%.minNum()：2个参数，将两者的对应通道的较小值，组成一个新的SIMD值返回。如果有一个通道的值是NaN，则会优先返回另一个通道的值。


### SIMD.%type%.max() , SIMD.%type%.maxNum()

SIMD.%type%.max()：2个参数，将两者的对应通道的较大值，组成一个新的SIMD值返回。如果有一个通道的值是NaN，则会优先返回NaN。


SIMD.%type%.maxNum()：2个参数，将两者的对应通道的较大值，组成一个新的SIMD值返回。如果有一个通道的值是NaN，则会优先返回另一个通道的值。


# 六. 静态方法：位运算
### SIMD.%type%.and() , SIMD.%type%.or() , SIMD.%type%.xor() , SIMD.%type%.not()

SIMD.%type%.and()：2个参数，返回两者对应的通道进行二进制AND运算（&）后得到的新的SIMD值。

SIMD.%type%.or()：2个参数，返回两者对应的通道进行二进制OR运算（|）后得到的新的SIMD值。

SIMD.%type%.xor()：2个参数，返回两者对应的通道进行二进制”异或“运算（^）后得到的新的SIMD值。

SIMD.%type%.not()：1个参数，返回每个通道进行二进制”否“运算（~）后得到的新的SIMD值。


# 七. 静态方法：数据类型转换
SIMD提供以下方法，用来将一种数据类型转为另一种数据类型。
SIMD.%type%.fromFloat32x4()
SIMD.%type%.fromFloat32x4Bits()
SIMD.%type%.fromFloat64x2Bits()
SIMD.%type%.fromInt32x4()
SIMD.%type%.fromInt32x4Bits()
SIMD.%type%.fromInt16x8Bits()
SIMD.%type%.fromInt8x16Bits()
SIMD.%type%.fromUint32x4()
SIMD.%type%.fromUint32x4Bits()
SIMD.%type%.fromUint16x8Bits()
SIMD.%type%.fromUint8x16Bits()

带有Bits后缀的方法，会原封不动地将二进制位拷贝到新的数据类型；不带后缀的方法，则会进行数据类型转换。
示例：
` var t = SIMD.Float32x4(1.0, 2.0, 3.0, 4.0);
SIMD.Int32x4.fromFloat32x4(t);
// Int32x4[1, 2, 3, 4]
SIMD.Int32x4.fromFloat32x4Bits(t);
// Int32x4[1065353216, 1073741824, 1077936128, 1082130432] `

Bits后缀的方法，还可以用于通道数目不对等的拷贝。
示例：
` var t = SIMD.Float32x4(1.0, 2.0, 3.0, 4.0);
SIMD.Int16x8.fromFloat32x4Bits(t);
// Int16x8[0, 16256, 0, 16384, 0, 16448, 0, 16512] `


# 八. 实例方法
### SIMD.%type%.prototype.toString()
SIMD.%type%.prototype.toString()：返回一个SIMD值的字符串形式。
示例：
` var a = SIMD.Float32x4(11, 22, 33, 44);
a.toString() // "SIMD.Float32x4(11, 22, 33, 44)" `

# 九. 实例：求平均值
` function average(list) {
  var n = list.length;
  var sum = SIMD.Float32x4.splat(0.0);
//一个新的SIMD值(sum)，该值的所有通道都会设成0.0
  for (var i = 0; i < n; i += 4) {
    sum = SIMD.Float32x4.add(   //两个参数的每个通道相加
      sum,
      SIMD.Float32x4.load(list, i)
//从list数组的索引i位置开始读入数据
    );
//每隔四位，将所有的值读入一个 SIMD
  }
  var total = SIMD.Float32x4.extractLane(sum, 0) +
              SIMD.Float32x4.extractLane(sum, 1) +
              SIMD.Float32x4.extractLane(sum, 2) +
              SIMD.Float32x4.extractLane(sum, 3);
//累加四个通道的总和
  return total / n;
//除以n求平均值
} `
