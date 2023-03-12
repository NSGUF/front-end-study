# 第一部分 类型和语法

## 第一章 类型

JavaScript 有七种内置类型：

• 空值（null）

• 未定义（undefined）

• 布尔值（ boolean）

• 数字（number）

• 字符串（string）

• 对象（object）

• 符号（symbol，ES6 中新增）

```javascript
typeof undefined === "undefined"; // true
typeof true === "boolean"; // true
typeof 42 === "number"; // true
typeof "42" === "string"; // true
typeof { life: 42 } === "object"; // true
// ES6中新加入的类型
typeof Symbol() === "symbol"; // true
typeof null === "object"; // true
// 使用复合条件来检测 null 值的类型
var a = null;
(!a && typeof a === "object"); // true
typeof function a(){ /* .. */ } === "function"; // true 是 object 的一个“子类型”
typeof [1,2,3] === "object"; // true 也是 object 的一个“子类型”
```

JavaScript 中的变量是没有类型的，只有值才有。变量可以随时持有任何类型的值。

已在作用域中声明但还没有赋值的变量，是 undefined 的。相反，还没有在作用域中声明过的变量，是 undeclared 的。

直接调用 undefined 的变量不会报错，但是直接调用 undeclared 的会报错，所以判断变量的 typeof 比直接判断变量更安全；如下；

```javascript
var a;
if (a) {} //这里会报错；
if (typeof a === undefined) {} // 这里不会报错
if (window.a) {} // 这种方式也可以
```

## 第二章 值

### 数组

字符串键值能够被强制类型转换为十进制数字的话，它就会被当作数字索引来处理。如

```javascript
var a = [];
a["123"] = 23;
a.length;// 124
```

类数组转换成数组：

1.  Array.prototype.slice.call(arguments);
1.  Array.from(arguements);

### 字符串

JavaScript 中字符串是不可变的，而数组是可变的。

字符串不可变是指字符串的成员函数不会改变其原始值，而是创建并返回一个新的字符串。而数组的成员函数都是在其原始值上进行操作。

```javascript
c = a.toUpperCase();
a === c; // false
a; // "foo"
c; // "FOO"
// 字符串反转
var c = a
 // 将a的值转换为字符数组
 .split( "" )
 // 将数组中的字符进行倒转
 .reverse()
 // 将数组中的字符拼接回字符串
 .join( "" );
```

### 数字

JavaScript 只有一种数值类型：number（数字），包括“整数”和带小数的十进制数。

```javascript
// tofixed指定小数位数
var a = 1.234
a.toFixed(0);// 1;
a.toFixed(4);// 1.2340
// toPrecision(..) 方法用来指定有效数位的显示位数
var a = 42.59;
a.toPrecision( 1 ); // "4e+1"
a.toPrecision( 2 ); // "43"
a.toPrecision( 3 ); // "42.6"
a.toPrecision( 4 ); // "42.59"
a.toPrecision( 5 ); // "42.590"
a.toPrecision( 6 ); // "42.5900"
42.toFixed( 3 ); // SyntaxError
// 下面的语法都有效：
(42).toFixed( 3 ); // "42.000"
0.42.toFixed( 3 ); // "0.420"
42..toFixed( 3 ); // "42.000"
42 .toFixed(3); // "42.000" 注意其中的空格
var onethousand = 1E3; // 即 1 * 10^3
var onemilliononehundredthousand = 1.1E6; // 即 1.1 * 10^6
0xf3; // 243的十六进制
0Xf3; // 同上
0363; // 243的八进制
// 从 ES6 开始，严格模式（strict mode）不再支持 0363 八进制格式（新格式如
//下）。0363 格式在非严格模式（non-strict mode）中仍然受支持，但是考虑到
//将来的兼容性，最好不要再使用（我们现在使用的应该是严格模式）。
0o363; // 243的八进制
0O363; // 同上
0b11110011; // 243的二进制
0B11110011; // 同上
0.1 + 0.2 === 0.3 // false 合适因为在js中，二进制浮点数不是十分精确的
console.log(.1 + .2); // 0.30000000000000004
```

如何解决浮点数不精确的问题：最常见的方法是设置一个误差范围值，通常称为“机器精度”（machine epsilon），对 JavaScript 的数字来说，这个值通常是 2^-52 (2.220446049250313e-16)。从 ES6 开始，该值定义在 Number.EPSILON 中；

```javascript
// polyfill
if (!Number.EPSILON) {
 Number.EPSILON = Math.pow(2,-52);
}

function numbersCloseEnoughToEqual(n1,n2) {
 return Math.abs( n1 - n2 ) < Number.EPSILON;
}
var a = 0.1 + 0.2;
var b = 0.3;
numbersCloseEnoughToEqual( a, b ); // true
numbersCloseEnoughToEqual( 0.0000001, 0.0000002 ); // false
```

PS：会有误差的原因是：计算机是通过二进制的方式存储数据的，在相加的时候，是拿二进制去相加，0.1 的二进制是 0.0001100110011001100...（1100 循环），0.2 的二进制是：0.00110011001100...（1100 循环）；在 JavaScript 的 Number 实现遵循 IEEE 754 标准，使用 64 位固定长度来表示，也就是标准的 double 双精度浮点数。在二进制科学表示法中，双精度浮点数的小数部分最多只能保留 52 位，再加上前面的 1，其实就是保留 53 位有效数字，剩余的需要舍去，遵从“0 舍 1 入”的原则。

JS 中能够被最大呈现的整数为：2^53 - 1，即 9007199254740991，在 ES6 中被定义为 Number.MAX_SAFE_INTEGER。最小整数是 -9007199254740991，在 ES6 中被定义为 Number.MIN_SAFE_INTEGER。如果需要精确呈现，需要将其转成 string；

整数检测：

```javascript
// es6 是否是整数
Number.isInteger( 42 ); // true
Number.isInteger( 42.000 ); // true
Number.isInteger( 42.3 ); // false
// polyfill
if (!Number.isInteger) {
 Number.isInteger = function(num) {
 return typeof num == "number" && num % 1 == 0;
 };
}
// es6 是否是安全的整数
Number.isSafeInteger( Number.MAX_SAFE_INTEGER ); // true
Number.isSafeInteger( Math.pow( 2, 53 ) ); // false
Number.isSafeInteger( Math.pow( 2, 53 ) - 1 ); // true

// 最大安全数为什么不安全？
2**53 //9007199254740992
2**53 + 1 //9007199254740992
2**53 + 2 //9007199254740994
2**53 + 3 //9007199254740996
2**53 + 4 //9007199254740996

// polyfill
if (!Number.isSafeInteger) {
 Number.isSafeInteger = function(num) {
 return Number.isInteger( num ) &&
 Math.abs( num ) <= Number.MAX_SAFE_INTEGER;
 };
}
```

undefined 指从未赋值

null 指曾赋过值，但是目前没有值

null 是一个特殊关键字，不是标识符，我们不能将其当作变量来使用和赋值。然而 undefined 却是一个标识符，可以被当作变量来使用和赋值

void 运算符：它的值为 undefined

```javascript
var a = 42;
console.log( void a, a ); // undefined 42
```

NaN

```javascript
var a = 2 / "foo";
a == NaN; // false
a === NaN; // false

var a = 2 / "foo";
var b = "foo";
a; // NaN
b; "foo"
window.isNaN( a ); // true
window.isNaN( b ); // true——晕！ 只要用数值除以某个变量，这个变量就是NaN
2/{};
isNaN({})// true;

// es6有Number.isNaN
// polyfill
if (!Number.isNaN) {
 Number.isNaN = function(n) {
 return (
 typeof n === "number" &&
 window.isNaN( n )
 );
 };
}
var a = 2 / "foo";
var b = "foo";
Number.isNaN( a ); // true
Number.isNaN( b ); // false——好！

// 还有个更为简单的方法 即利用 NaN 不等于自身这个特点
if (!Number.isNaN) {
 Number.isNaN = function(n) {
 return n !== n;
 };
}
```

无穷数

```javascript
console.log(1 / 0); // Infinity
console.log(-1 / 0); // -Infinity
a = Number.MAX_VALUE;
// 就近取整模式
console.log(a); // 1.7976931348623157e+308
console.log(a * 2); // Infinity
console.log(a + Math.pow( 2, 969 )); // 1.7976931348623157e+308
console.log(Infinity / Infinity); // NaN
1/Infinity // 0
1/-Infinity //-0
Infinity / 1 //Infinity
-Infinity / 1 //-Infinity
```

零值

```javascript
// 加法和减法运算不会得到负零（negative zero）
var a = 0 / -3;
// 至少在某些浏览器的控制台中显示是正确的
console.log(a); // -0
// 但是规范定义的返回结果是这样！
console.log(a.toString()); // "0"
console.log(a + ""); // "0"
console.log(String( a )); // "0"
// JSON也如此，很奇怪
console.log(JSON.stringify( a )); // "0"
console.log(+"-0"); // -0
console.log(Number( "-0") ); // -0
console.log(JSON.parse( "-0" )); // -0 JSON.stringify(-0) 返回 "0"，而 JSON.parse("-0") 返回 -0。
var a = 0;
var b = 0 / -3;
console.log(a == b); // true
console.log(-0 == 0); // true
console.log(a === b); // true
console.log(-0 === 0); // true
console.log(0 > -0); // false
console.log(a > b); // false

// 是否是－0
function isNegZero(n) {
    n = Number( n );
    return (n === 0) && (1 / n === -Infinity);
}
isNegZero( -0 ); // true
isNegZero( 0 / -3 ); // true
isNegZero( 0 ); // false
```

特殊等式 Object.is

```javascript
var a = 2 / "foo";
var b = -3 * 0;
Object.is( a, NaN ); // true
Object.is( b, -0 ); // true
Object.is( b, 0 ); // false
// polyfill
if (!Object.is) {
    Object.is = function(v1, v2) {
        // 判断是否是-0
        if (v1 === 0 && v2 === 0) {
            return 1 / v1 === 1 / v2;
        }
        // 判断是否是NaN
        if (v1 !== v1) {
            return v2 !== v2;
        }
        // 其他情况
        return v1 === v2;
    };
}
```

简单值通过值复制的方式来赋值 / 传递，包括 null、undefined、字符串、数字、布尔和 ES6 中的 symbol。

复合值（compound value）——对象（包括数组和封装对象）和函数，则总是通过引用复制的方式来赋值 / 传递。

## 第三章 原生函数

内部属性 [[Class]]

```javascript
Object.prototype.toString.call( [1,2,3] );
// "[object Array]"
Object.prototype.toString.call( /regex-literal/i );
// "[object RegExp]"
Object.prototype.toString.call( null );
// "[object Null]"
Object.prototype.toString.call( undefined );
// "[object Undefined]"
Object.prototype.toString.call( "abc" );
// "[object String]"
Object.prototype.toString.call( 42 );
// "[object Number]"
Object.prototype.toString.call( true );
// "[object Boolean]"
```

封装对象包装:  由于基本类型值没有 .length 和 .toString() 这样的属性和方法，需要通过封装对象才能访问，此时 JavaScript 会自动为基本类型值包装（box 或者 wrap）一个封装对象；

拆封：得到封装对象中的基本类型值，可以使用 valueOf() 函数；

构造函数 Array(..) 不要求必须带 new 关键字。不带时，它会被自动补上。因此 Array(1,2,3) 和 new Array(1,2,3) 的效果是一样的。

稀疏数组：将包含至少一个“空单元”的数组；

```javascript
var a = new Array( 3 );
var b = [ undefined, undefined, undefined ];
var c = [];
c.length = 3;
a.join( "-" ); // "--"
b.join( "-" ); // "--"
a.map(function(v,i){ return i; }); // [ undefined x 3 ]
b.map(function(v,i){ return i; }); // [ 0, 1, 2 ]
var a = Array.apply( null, { length: 3 } );// 等同于Array(undefined, undefined, undefined)
a; // [ undefined, undefined, undefined ]
```

永远不要创建和使用空单元数组

除非万不得已，否则尽量不要使用 Object(..)/Function(..)/RegExp(..)

RegExp(..) 有时还是很有用的，比如动态定义正则表达式时

Date(..) 和 Error(..)

```javascript
// Date.now的polyfill
if (!Date.now) {
 Date.now = function(){
 return (new Date()).getTime();
 };
}
```

如果调用 Date() 时不带 new 关键字，则会得到当前日期的字符串值。其具体格式规范没有规定，浏览器使用 "Fri Jul 18 2014 00:31:02 GMT-0500 (CDT)"这样的格式来显示。

String#indexOf(..)：在字符串中找到指定子字符串的位置。

String#charAt(..)：获得字符串指定位置上的字符。

String#substr(..)、String#substring(..) 和 String#slice(..) ：获得字符串的指定部分。

String#toUpperCase() 和 String#toLowerCase()：将字符串转换为大写或小写。

String#trim()：去掉字符串前后的空格，返回新的字符串。

以上方法并不改变原字符串的值，而是返回一个新字符串。

```javascript
var a = 'abcde';
a.charAt(1) // 'b'
a.charCodeAt(1) //98
a.concat(1) //'abcde1'
a.endsWith('e') // true
a.indexOf('b') //1
a.includes('b') //true
a.lastIndexOf('d') //3
a.match(/e/g) //['e']
a.repeat()//''
a.repeat(2)//'abcdeabcde'
a.replace('d', 4)//'abc4e'
a.replaceAll('e', '5')//'abcd5'
a.search(/b/)//1
a.slice(0, 1)//'a'
a.split('')//(5) ['a', 'b', 'c', 'd', 'e']
a.startsWith(1)//false
a.substr(0,2) // 'ab' 第一个参数 起始下标 第二参数 长度
a.substring(1,2)//'b' 第一个参数 起始下标 第二个参数 结束下标
'B'.toLowerCase()//'b'
a.toUpperCase()//'ABCDE'
' fd '.trim() //'fd'
```

## 第四章 强制类型转换

类型转换发生在静态类型语言的编译阶段，而强制类型转换则发生在动态类型语言的运行时（runtime）。

### 抽象值操作

#### toString

1.  该方法可重新定义；
1.  JSON.stringfy 在将 JSON 对象序列化为字符串时也用到了 ToString 在对象中遇到 undefined、function 和 symbol 时会自动将其忽略，在数组中则会返回 null（以保证单元位置不变）。

JSON.stringify(value[, replacer[, space]])

- - **value:** 必需， 要转换的 JavaScript 值（通常为对象或数组）。
  - **replacer:** 可选。用于转换结果的函数或数组。  
    如果 replacer 为函数，则 JSON.stringify 将调用该函数，并传入每个成员的键和值。使用返回值而不是原始值。如果此函数返回 undefined，则排除成员。根对象的键是一个空字符串：""。  
    如果 replacer 是一个数组，则仅转换该数组中具有键值的成员。成员的转换顺序与键在数组中的顺序一样。
  - **space:** 可选，文本添加缩进、空格和换行符，如果 space 是一个数字，则返回值文本在每个级别缩进指定数目的空格，如果 space 大于 10，则文本缩进 10 个空格。space 也可以使用非数字，如：\t。

(1) 字符串、数字、布尔值和 null 的 JSON.stringify(..) 规则与 ToString 基本相同。

(2) 如果传递给 JSON.stringify(..) 的对象中定义了 toJSON() 方法，那么该方法会在字符串化前调用，以便将对象转换为安全的 JSON 值。

JSON.stringify(..) 并不是强制类型转换。在这里介绍是因为它涉及 ToString 强制类型转换

```javascript
JSON.stringify( undefined ); // undefined
JSON.stringify( function(){} ); // undefined
JSON.stringify(
 [1,undefined,function(){},4]
); // "[1,null,null,4]"
JSON.stringify(
 { a:2, b:function(){} }
); // "{"a":2}"

// 对包含循环引用的对象执行 JSON.stringify(..) 会出错。
// Uncaught TypeError: Converting circular structure to JSON
//     --> starting at object with constructor 'Object'
//     |     property 'c' -> object with constructor 'Object'
var o = { };
var a = {
 b: 42,
 c: o,
 d: function(){}
};
// 在a中创建一个循环引用
o.e = a;
// 循环引用在这里会产生错误
// JSON.stringify( a );
// 自定义的JSON序列化 toJSON() 应该“返回一个能够被字符串化的安全的 JSON 值”，而不是“返回一个 JSON 字符串”。
a.toJSON = function() {
 // 序列化仅包含b
 return { b: this.b };
};
JSON.stringify( a ); // "{"b":42}"
var a = {
 b: 42,
 c: "42",
 d: [1,2,3]
};
JSON.stringify( a, ["b","c"] ); // "{"b":42,"c":"42"}"
JSON.stringify( a, function(k,v){
 if (k !== "c") return v;
} );
// "{"b":42,"d":[1,2,3]}"
var a = {
 b: 42,
 c: "42",
 d: [1,2,3]
};
JSON.stringify( a, null, 3 );
// "{
// 		"b": 42,
// 		"c": "42",
// 		"d": [
// 				1,
// 				2,
// 				3
// 		]
// }"
JSON.stringify( a, null, "-----" );
// "{
// -----"b": 42,
// -----"c": "42",
// -----"d": [
// ----------1,
// ----------2,
// ----------3
// -----]
// }"
```

#### ToNumber

true 转换为 1，false 转换为 0。undefined 转换为 NaN，null 转换为 0。

为了将值转换为相应的基本类型值，抽象操作 ToPrimitive（参见 ES5 规范 9.1 节）会首先检查该值是否有 valueOf() 方法。如果有并且返回基本类型值，就使用该值进行强制类型转换。如果没有就使用 toString()的返回值（如果存在）来进行强制类型转换。如果 valueOf() 和 toString() 均不返回基本类型值，会产生 TypeError 错误。

```javascript
var a = {
 valueOf: function(){
 return "42";
 }
};
var b = {
 toString: function(){
 return "42";
 }
};
var c = [4,2];
c.toString = function(){
 return this.join( "" ); // "42"
};
Number( a ); // 42
Number( b ); // 42
Number( c ); // 42
Number( "" ); // 0
Number( [] ); // 0
Number( [ "abc" ] ); // NaN
```

#### ToBoolean

假值：

• undefined

• null

• false

• +0、-0 和 NaN

• ""

假值列表以外的值都是真值。

**假值对象（falsy object）**

document.all 浏览器自带的来判断浏览器是否是老版本的 IE。

：if(document.all) { /_ it’s IE _/ }

```javascript
var a = "false";
var b = "0";
var c = "''";
var d = Boolean( a && b && c );// "''"是真值
```

显式强制类型转换

```javascript
var a = 42;
var b = String( a );// String(..) 遵循前面讲过的 ToString 规则
// var b = a.toString(); 同上
var c = "3.14";
var d = Number( c ); // Number(..) 遵循前面讲过的 ToNumber 规则
// var d = +c; 同上   +是运算符的一元（unary）形式
b; // "42"
d; // 3.14
var c = "3.14";
var d = 5+ +c;
d; // 8.14
```

一元运算符 - 和 + 一样，并且它还会反转数字的符号位。由于 -- 会被当作递减运算符来处理，所以我们不能使用 -- 来撤销反转，而应该像 - -"3.14" 这样，在中间加一个空格，才能得到正确结果 3.14。

```javascript
1 + - + + + - + 1; // 2 负负得正
+new Date();// 日期显式转换为数字
 Date.now();// 比较好的写法
// Date.now的polyfill 不建议对日期类型使用强制类型转换，应该使用 Date.now() 来获得当前的时间戳，使用 new Date(..).getTime() 来获得指定时间的时间戳。
if (!Date.now) {
 Date.now = function() {
 return +new Date();
 };
}
```

JavaScript 有一处奇特的语法，即构造函数没有参数时可以不用带 ()。于是我们可能会碰到 var timestamp = +new Date; 这样的写法。这样能否提高代码可读性还存在争议，因为这仅用于 new fn()，对一般的函数调用 fn() 并不适用。

---

**奇特的 ~ 运算符**

~x 大致等同于 -(x+1)。

用法

```javascript
if (!~a.indexOf( "ol" )) { // true 这里~ 比 >= 0 和 == -1 更简洁。
 // 没有找到匹配！
}
~12.1 // -13
-(12.1+1) // -13.1
~~12.1 // 12
```

**字位截除**

~~x 能将值截除为一个 32 位整数，x | 0 也可以，而且看起来还更简洁。

```javascript
Math.floor( -49.6 ); // -50
~~-49.6; // -49
~~1E20 / 10; // 166199296
1E20 | 0 / 10; // 1661992960
(1E20 | 0) / 10; // 166199296
```

**显式解析数字字符串**

解析允许字符串中含有非数字字符，解析按从左到右的顺序，如果遇到非数字字符就停止。而转换不允许出现非数字字符，否则会失败并返回 NaN。解析字符串中的浮点数可以使用 parseFloat(..) 函数；

```javascript
var a = "42";
var b = "42px";
Number( a ); // 42
parseInt( a ); // 42
Number( b ); // NaN
parseInt( b ); // 42
```

ES5 之前的 parseInt(..) 有一个坑导致了很多 bug。即如果没有第二个参数来指定转换的基数（又称为 radix），parseInt(..) 会根据字符串的第一个字符来自行决定基数。从 ES5 开始 parseInt(..) 默认转换为十进制数，除非另外指定。如果你的代码需要在 ES5 之前的环境运行，请记得将第二个参数设置为 10。

**解析非字符串**

```javascript
parseInt( 1/0, 19 ); // 18
parseInt( 0.000008 ); // 0 ("0" 来自于 "0.000008")
parseInt( 0.0000008 ); // 8 ("8" 来自于 "8e-7")
parseInt( false, 16 ); // 250 ("fa" 来自于 "false")
parseInt( parseInt, 16 ); // 15 ("f" 来自于 "function..")
parseInt( "0x10" ); // 16
parseInt( "103", 2 ); // 2 3在二进制中不存在，所以取10 转10进制 为2
```

parseInt(1/0, 19) 实际上是 parseInt("Infinity", 19)。第一个字符是 "I"，以 19 为基数时值为 18。第二个字符 "n" 不是一个有效的数字字符，解析到此为止，和 "42px" 中的 "p"一样  


**显式转换为布尔值：** 使用 Boolean(a) 和 !!a 来进行显式强制类型转换

**隐式强制类型转换：** 代码可读性不好，但是也是可以减少冗余，让代码更简洁 抽象和隐藏那些细枝末节，有助于提高代码的可读性

**字符串和数字之间的隐式强制类型转换**

```javascript
var a = [1,2];
var b = [3,4];
a + b; // "1,23,4" 数组的valueOf() 操作无法得到简单基本类型值，于是它转而调用 toString()
```

a + "" 会对 a 调用 valueOf() 方法，然后通过 ToString 抽象操作将返回值转换为字符串。

```javascript
var a = {
 valueOf: function() { return 42; },
 toString: function() { return 4; }
};
a + ""; // "42"
String( a ); // "4"
```

**布尔值到数字的隐式强制类型转换**

如果其中有且仅有一个参数为 true，则 onlyOne(..) 返回 true。

```javascript
function onlyOne() {
   var sum = 0;
   for (var i=0; i < arguments.length; i++) {
     // 跳过假值，和处理0一样，但是避免了NaN
     if (arguments[i]) {
     		sum += arguments[i];
     }
   }
   return sum == 1;
}
var a = true;
var b = false;
onlyOne( b, a ); // true
onlyOne( b, a, b, b, b ); // true
```

|| 和 &&：选择器运算符”（selector operators）或者“操作数选择器运算符”（operand selector operators）

在 a ? a : b 中，如果 a 是一个复杂一些的表达式（比如有副作用的函数调用等），它有可能被执行两次（如果第一次结果为真）。而在 a || b 中 a 只执行一次，其结果用于条件判断和返回结果（如果适用的话）。

**Symbol 符号的强制类型转换**

```javascript
var s1 = Symbol( "cool" );
String( s1 ); // "Symbol(cool)"
var s2 = Symbol( "not cool" );
s2 + ""; // TypeError
```

== 允许在相等比较中进行强制类型转换，而 === 不允许

== 和 === 都会检查操作数的类型。区别在于操作数类型不同时它们的处理方式不同。

**抽象相等：==**

**字符串和数字之间的相等比较：**

(1) 如果 Type(x) 是数字，Type(y) 是字符串，则返回 x == ToNumber(y) 的结果。

(2) 如果 Type(x) 是字符串，Type(y) 是数字，则返回 ToNumber(x) == y 的结果。

**其他类型和布尔类型之间的相等比较：**

(1) 如果 Type(x) 是布尔类型，则返回 ToNumber(x) == y 的结果；

(2) 如果 Type(y) 是布尔类型，则返回 x == ToNumber(y) 的结果。

**null 和 undefined 之间的相等比较：**

(1) 如果 x 为 null，y 为 undefined，则结果为 true。

(2) 如果 x 为 undefined，y 为 null，则结果为 true。

```javascript
var a = 42;
var b = "42";
a === b; // false
a == b; // true
var x = true;
var y = "42";
x == y; // false
var a = null;
var b;
a == b; // true
a == null; // true
b == null; // true
a == false; // false
b == false; // false
a == ""; // false
b == ""; // false
a == 0; // false
b == 0; // false
```

**a == null 等价为 a===null&& a===undefined**

**对象和非对象之间的相等比较：**

(1) 如果 Type(x) 是字符串或数字，Type(y) 是对象，则返回 x == ToPrimitive(y) 的结果；

(2) 如果 Type(x) 是对象，Type(y) 是字符串或数字，则返回 ToPromitive(x) == y 的结果。

```javascript
var a = "abc";
var b = Object( a ); // 和new String( a )一样 其他类型和这个相似，比如number symbol boolean
a === b; // false
a == b; // true
var a = null;
var b = Object( a ); // 和Object()一样
a == b; // false
var c = undefined;
var d = Object( c ); // 和Object()一样
c == d; // false
var e = NaN;
var f = Object( e ); // 和new Number( e )一样
e == f; // false
```

因为没有对应的封装对象，所以 null 和 undefined 不能够被封装（boxed），Object(null)和 Object() 均返回一个常规对象。NaN 能够被封装为数字封装对象，但拆封之后 NaN == NaN 返回 false，因为 NaN 不等于 NaN

```javascript
"0" == null; // false
"0" == undefined; // false
"0" == false; // true -- 晕！
"0" == NaN; // false
"0" == 0; // true
"0" == ""; // false
false == null; // false
false == undefined; // false
false == NaN; // false
false == 0; // true -- 晕！
false == ""; // true -- 晕！
false == []; // true -- 晕！
false == {}; // false
"" == null; // false
"" == undefined; // false
"" == NaN; // false
"" == 0; // true -- 晕！
"" == []; // true -- 晕！
"" == {}; // false
0 == null; // false
0 == undefined; // false
0 == NaN; // false
0 == []; // true -- 晕！
0 == {}; // false

[] == ![] // true
2 == [2]; // true
"" == [null]; // true
0 == "\n"; // true

"0" == false; // true -- 晕！
false == 0; // true -- 晕！
false == ""; // true -- 晕！
false == []; // true -- 晕！
"" == 0; // true -- 晕！
"" == []; // true -- 晕！
0 == []; // true -- 晕！
```

**安全运用隐式强制类型转换**

如果两边的值中有 true 或者 false，千万不要使用 ==。

如果两边的值中有 []、"" 或者 0，尽量不要使用 ==。

### 抽象关系比较

比较双方首先调用 ToPrimitive，如果结果出现非字符串，就根据 ToNumber 规则将双方强制类型转换为数字来进行比较。

```javascript
var a = [ 42 ];
var b = [ "43" ];
a < b; // true
b < a; // false
var a = [ "42" ];
var b = [ "043" ];
a < b; // false '42' < '043'
var a = [ 4, 2 ];
var b = [ 0, 4, 3 ];
a < b; // false "4, 2" < "0, 4, 3"
var a = { b: 42 };
var b = { b: 43 };
a < b; // false 都是[object Object]
a == b; // false
a > b; // false
a <= b; // true
a >= b; // true // 根据规范 a <= b 被处理为 b < a，然后将结果反转。因为 b < a 的结果是 false，所以 a <= b 的结果是 true。
```

JavaScript 中 <= 是“不大于”的意思（即 !(a > b)，处理为 !(b < a)）。同理 a >= b 处理为 b <= a。

相等比较有严格相等，关系比较却没有“严格关系比较”（strict relational comparison）。也就是说如果要避免 a < b 中发生隐式强制类型转换，我们只能确保 a 和 b 为相同的类型，除此之外别无他法。

**比较的时候，最好保证一下左右的类型一致；**

---

## 第五章 语法

### 5.1 语法和表达式

代码块的结果值就如同一个隐式的返回，即返回最后一个语句的结果值。

```javascript
var a, b;
a = if (true) { //  Uncaught SyntaxError: Unexpected token 'if'
 b = 4 + 38;
};
var a, b;
a = eval( "if (true) { b = 4 + 38; }" ); // 但是这样可以
a; // 42

var a = 42, b;
b = ( a++, a );
a; // 43
b; // 43
```

++a++ 会产生 ReferenceError 错误，因为运算符需要将产生的副作用赋值给一个变量。以 ++a++ 为例，它首先执行 a++（根据运算符优先级，如下），返回 42，然后执行 ++42，这时会产生 ReferenceError 错误，因为 ++ 无法直接在 42 这样的值上产生副作用。

```javascript
var obj = {
 a: 42
};
obj.a; // 42
delete obj.a; // true 操作成功是指对于那些不存在或者存在且可配置 的属性，delete 返回 true，否则返回 false 或者报错。
obj.a; // undefined
```

= 赋值运算符： a =2 这里把 2 赋值给了 a，并且会返回这个结果，所以可以使用链式赋值；

[] + {}; // "[object Object]" [] + {} 会被当作一个值（空对象）来处理

{} + []; // 0 {}会当成一个独立的空代码块（不执行任何操作），所以结果是 + [] 为 0

JavaScript 没有 else if，但 if 和 else 只包含单条语句的时候可以省略代码块的{ }。

### 5.2 运算符优先级

,的优先级是最低的；

&& 运算符的优先级高于 =；

&& 运算符先于 || 执行；

|| 的优先级又高于 ? :；

短路：对 && 和 || 来说，如果从左边的操作数能够得出结果，就可以忽略右边的操作数。我们将这种现象称为“短路”（即执行最短路径）。

&& 运算符是左关联（|| 也是）

? : 是右关联

= 是右关联

```javascript
var a = foo() && bar(); // foo() 首先执行，它的返回结果决定了 bar() 是否执行 左关联
true ? false : true ? true : true; // false
true ? false : (true ? true : true); // false 右关联
(true ? false : true) ? true : true; // true

var a = 42;
var b = "foo";
var c = false;
var d = a && b || c ? c || b ? a : c && b : a;
d; // 42 (a && b || c) ? (c || (b ? a : c && b)) : a; 这个顺序
```

**自动分号：** 有时 JavaScript 会自动为代码行补上缺失的分号，即自动分号插入（Automatic Semicolon  Insertion，ASI）。只有在代码行末尾与换行符之间除了空格和注释之外没有别的内容时，它才会这样做。

语法规定 do..while 循环后面必须带 ;，而 while 和 for 循环后则不需要。大多数开发人员都不记得这一点，此时 ASI 就会自动补上分号。

其他涉及 ASI 的情况是 break、continue、return 和 yield（ES6）等关键字，如果换行了也没有分号，会自动补全分号；

### 5.4 错误

JavaScript 中有很多错误类型，分为两大类：早期错误（编译时错误，无法被捕获）和运行时错误（可以通过 try..catch 来捕获）。所有语法错误都是早期错误，程序有语法错误则无法运行。

TDZ（Temporal Dead Zone，暂时性死区）：由于代码中的变量还没有初始化而不能被引用的情况，不能提升变量；

对未声明变量使用 typeof 不会产生错误（参见第 1 章），但在 TDZ 中却会报错；

### 5.5 函数参数

```javascript
var b = 3;
function foo( a = 42, b = a + b + 5 ) {
 // ..
}
foo()// Uncaught ReferenceError: Cannot access 'b' before initialization
```

b = a + b + 5 在参数 b（= 右边的 b，而不是函数外的那个）的 TDZ 中访问 b，所以会出错。而访问 a 却没有问题，因为此时刚好跨出了参数 a 的 TDZ。

```javascript
function foo( a = 42, b = a + 1 ) {
 console.log( a, b );
}
foo(); // 42 43
foo( undefined ); // 42 43
foo( 5 ); // 5 6
foo( void 0, 7 ); // 42 7
foo( null ); // null 1
```

ES6 参数默认值会导致 arguments 数组和相对应的命名参数之间出现偏差，ES5 也会出现这种情况：向函数传递参数时，arguments 数组中的对应单元会和命名参数建立关联（linkage）以得到相同的值。相反，不传递参数就不会建立关联。**在严格模式中并没有建立关联**

```javascript
function foo(a) {
 a = 42;
 console.log( arguments[0] );
}
foo( 2 ); // 42 (linked)
foo(); // undefined (not linked)
```

### 5.6 try..finally

先执行 finally，再执行 try 或 catch；

如果 finally 中抛出异常，函数就会在此处终止。如果此前 try 中已经有 return 设置了返回值，则该值会被丢弃：

```javascript
function foo() {
 try {
 return 42;
 }
 finally {
 console.log( "Hello" );
 }
 console.log( "never runs" );
}
console.log( foo() );
// Hello
// 42

function foo() {
 try {
 throw 42;
 }
 finally {
 console.log( "Hello" );
 }
 console.log( "never runs" );
}
console.log( foo() );
// Hello
// Uncaught Exception: 42
function foo() {
 try {
 return 42;
 }
 finally {
 throw "Oops!";
 }
 console.log( "never runs" );
}
console.log( foo() );
// Uncaught Exception: Oops!
```

### 5.7 switch

case 表达式的匹配算法与 ===相同

```javascript
var a = "42";
switch (true) {
 case a == 10:
 console.log( "10 or '10'" );
 break;
 case a == 42:
 console.log( "42 or '42'" );
 break;
 default:
 // 永远执行不到这里
}
// 42 or '42'
```

# 第二部分 异步和性能

## 第 1 章 异步：现在与将来

事件循环：线程提供了一种机制来处理程序中多个块的执行，且执行每块时调用 JavaScript 引擎；

setTimeout(..) 并没有把回调函数挂在事件循环队列中。它所做的是设定一个定时器。当定时器到时后，环境会把你的回调函数放在事件循环中，这样，在未来某个时刻的 tick 会摘下并执行这个回调。如果事件循环中已经有很多个函数，则会按顺序执行，所以定时器的精度不高，只能保证不会在设置的时间之前执行

异步是关于现在和将来的时间间隙，而并行是关于能够同时发生的事情；

并行计算最常见的工具就是进程和线程。进程和线程独立运行，并可能同时运行：在不同的处理器，甚至不同的计算机上，但多个线程能够共享单个进程的内存。

异步是单线程执行，只是不确定代码的执行顺序，具有完整运行特性，但是有顺序执行的，并行是多线程同时执行，共享内存，不具有完整运行特性；

完整运行（run-to-completion）特性：由于 JavaScript 的单线程特性，foo()（以及 bar()）中的代码具有原子性。也就是说如果 foo() 开始运行，它的所有代码都会在 bar() 中的任意代码运行之前完成；

竞态条件：函数顺序的不确定性，无法可靠预测最终结果；

### 1.4 　并发

单线程事件循环是并发的一种形式

如果进程间没有相互影响的话，不确定性是完全可以接受的。

并发协作：取到一个长期运行的“进程”，并将其分割成多个步骤或多批任务，使得其他并发“进程”有机会将自己的运算插入到事件循环队列中交替运行。js 可以通过 setTimeout 将分成其他小段的任务重新进行异步调度，把剩下的插入到当前时间循环队列的结尾处；

```javascript
var res = [];
// response(..)从Ajax调用中取得结果数组
function response(data) {
 // 一次处理1000个
 var chunk = data.splice( 0, 1000 );
 // 添加到已有的res组
 res = res.concat(
 // 创建一个新的数组把chunk中所有值加倍
 chunk.map( function(val){
 return val * 2;
 } )
 );
 // 还有剩下的需要处理吗？
 if (data.length > 0) {
 // 异步调度下一次批处理
 setTimeout( function(){
 response( data );
 }, 0 );
 }
}
// ajax(..)是某个库中提供的某个Ajax函数
ajax( "http://some.url.1", response );
ajax( "http://some.url.2", response );
```

严格说来，setTimeout(..0) 并不直接把项目插入到事件循环队列。定时器会在有机会的时候插入事件。举例来说，两个连续的 setTimeout(..0) 调用不能保证会严格按照调用顺序处理，所以各种情况都有可能出现;

代码中语句的顺序和 JavaScript 引擎执行语句的顺序并不一定要一致；

一旦有事件需要运行，事件循环就会运行，直到队列清空。事件循环的每一轮称为一个 tick。用户交互、IO 和定时器会向事件队列中加入事件。

并发是指两个或多个事件链随时间发展交替执行，以至于从更高的层次来看，就像是同时在运行（尽管在任意时刻只处理一个事件）。

通常需要对这些并发执行的“进程”（有别于操作系统中的进程概念）进行某种形式的交互协调，比如需要确保执行顺序或者需要防止竞态出现。这些“进程”也可以通过把自身分割为更小的块，以便其他“进程”插入进来。

## 第 2 章 回调

程序的延续（continuation）。

一旦我们以回调函数的形式引入了单个 continuation（或者几十个），我们就容许了大脑工作方式和代码执行方式的分歧。一旦这两者出现分歧，我们就得面对这样一个无法逆转的事实：代码变得更加难以理解、追踪、调试和维护。

第一，大脑对于事情的计划方式是线性的、阻塞的、单线程的语义，但是回调表达异步流程的方式是非线性的、非顺序的，这使得正确推导这样的代码难度很大。难于理解的代码是坏代码，会导致坏 bug。

第二，也是更重要的一点，回调会受到控制反转的影响，因为回调暗中把控制权交给第三方（通常是不受你控制的第三方工具！）来调用你代码中的 continuation。这种控制转移导致一系列麻烦的信任问题，比如回调被调用的次数是否会超出预期。

## 第 3 章 Promise

### 3.1 什么是 promise

1.  现在值与将来值，实现 x + y，x 和 y 都是异步获取的；
1.  promise；

```javascript
// 回调方法
function add(getX,getY,cb) {
 var x, y;
 getX( function(xVal){
 x = xVal;
 // 两个都准备好了？
 if (y != undefined) {
 cb( x + y ); // 发送和
 }
 } );
 getY( function(yVal){
 y = yVal;
 // 两个都准备好了？
 if (x != undefined) {
 cb( x + y ); // 发送和
 }
 } );
}
// fetchX() 和fetchY()是同步或者异步函数
add( fetchX, fetchY, function(sum){
 console.log( sum ); // 是不是很容易？
} );

// promise实现
function add(xPromise,yPromise) {
 // Promise.all([ .. ])接受一个promise数组并返回一个新的promise，
 // 这个新promise等待数组中的所有promise完成
 return Promise.all( [xPromise, yPromise] )
 // 这个promise决议之后，我们取得收到的X和Y值并加在一起
 .then( function(values){
 // values是来自于之前决议的promisei的消息数组
 return values[0] + values[1];
 } );
}
// fetchX()和fetchY()返回相应值的promise，可能已经就绪，
// 也可能以后就绪
add( fetchX(), fetchY() )
.then(
 // 完成处理函数
 function(sum) {
 console.log( sum );
 },
 // 拒绝处理函数
 function(err) {
 console.error( err ); // 烦！
 }
);
```

Promise 决议后就是外部不可变的值，我们可以安全地把这个值传递给第三方，并确信它不会被有意无意地修改。特别是对于多方查看同一个 Promise 决议的情况，尤其如此。一方不可能影响另一方对 Promise 决议的观察结果。

Promise 是一种封装和组合未来值的易于复用的机制。

一旦 Promise 决议，它就永远保持在这个状态。此时它就成为了不变值（immutable  value），可以根据需求多次查看。

### 3.2 具有 then 方法的鸭子类型

鸭子类型（duck typing）：“如果它看起来像只鸭子，叫起来像只鸭子，那它一定就是只鸭子“

thenable 对象的判断如下：

```javascript
if (
 p !== null &&
 (
 typeof p === "object" ||
 typeof p === "function"
 ) &&
 typeof p.then === "function"
) {
 // 假定这是一个thenable!  这种方法可能会导致我正好有个then方法，被判断为thenable对象了
}
else {
 // 不是thenable
}
```

### 3.3 Promise 信任问题

异步编码的可信任问题：

1.  调用回调过早；一个 Promise 调用 then(..) 的时候，即使这个 Promise 已经决议，提供给 then(..) 的回调也总会被异步调用，所以 promise 不会有这种问题；
1.  调用回调过晚；一个 Promise 决议后，这个 Promise 上所有的通过 then(..) 注册的回调都会在下一个异步时机点上依次被立即调用；

```javascript
p.then( function(){
 p.then( function(){
 console.log( "C" );
 } );
 console.log( "A" );
} );
p.then( function(){
 console.log( "B" );
} );
// A B C
```

3.  回调未调用：没有任何东西（甚至 JavaScript 错误）能阻止 Promise 向你通知它的决议（如果它决议了的话）。如果你对一个 Promise 注册了一个完成回调和一个拒绝回调，那么 Promise 在决议时总是会调用其中的一个；

```javascript
// 如果 Promise 本身永远不被决议呢？即使这样，Promise 也提供了解决方案，其使用了一种称为竞态的高级抽象机制：
// 用于超时一个Promise的工具
function timeoutPromise(delay) {
 return new Promise( function(resolve,reject){
 setTimeout( function(){
 reject( "Timeout!" );
 }, delay );
 } );
}
// 设置foo()超时
Promise.race( [
 foo(), // 试着开始foo()
 timeoutPromise( 3000 ) // 给它3秒钟
] )
.then(
 function(){
 // foo(..)及时完成！
 },function(err){
 // 或者foo()被拒绝，或者只是没能按时完成
 // 查看err来了解是哪种情况
 }
);
```

4.  调用回调次数过少或过多；Promise 只能被决议一次，所以任何通过 then(..) 注册，被调用的次数就会和注册次数相同。
5.  未能传递所需的环境和参数：Promise 至多只能有一个决议值（完成或拒绝），后面的决议全部失效，包括参数；
6.  吞掉可能出现的错误和异常：在决议之前有任何的错误或异常，会直接走到拒绝中去，但是如果在完成里面有异常，会直接抛出异常。
7.  Promise.resolve 可以规范化一个类 thenable，返回一个真正的 Promise，如果是 promise，那就是返回其本身；

```javascript
// 不要只是这么做：
foo( 42 )
.then( function(v){
 console.log( v );
} );
// 而要这么做：
Promise.resolve( foo( 42 ) )
.then( function(v){
 console.log( v );
} );
```

### 3.4 链式流

Promise 的 then 会返回一个新的 promise 对象

```javascript
var p = Promise.resolve( 21 );
p.then( function(v){
 console.log( v ); // 21
   // 创建一个promise并返回
 return new Promise( function(resolve,reject){
 // 引入异步！
 setTimeout( function(){
 // 用值42填充
 resolve( v * 2 );
 }, 100 );
 } );
} )
.then( function(v){
 // 在前一步中的100ms延迟之后运行
 console.log( v ); // 42
} );
```

Promise 固有特性：

1.  调用 Promise 的 then(..) 会自动创建一个新的 Promise 从调用返回。
1.  在完成或拒绝处理函数内部，如果返回一个值或抛出一个异常，新返回的（可链接的）Promise 就相应地决议。
1.  如果完成或拒绝处理函数返回一个 Promise，它将会被展开，这样一来，不管它的决议值是什么，都会成为当前 then(..) 返回的链接 Promise 的决议值。

### 3.5 错误处理

try..catch 不能处理异步代码模块；

避免丢失被忽略和抛弃的 Promise 错误：

```javascript
var p = Promise.resolve( 42 );
p.then(
 function fulfilled(msg){
 // 数字没有string函数，所以会抛出错误
 console.log( msg.toLowerCase() );
 }
)
.catch( handleErrors );
```

### 3.6 Promise 模式

### 3.6.1 Promise.all([...])

要等待两个或更多并行 / 并发的任务都完成才能继续；

Promise.all([ .. ]) 返回的主 promise 在且仅在所有的成员 promise 都完成后才会完成。如果这些 promise 中有任何一个被拒绝的话，主 Promise.all([ .. ])promise 就会立即被拒绝，并丢弃来自其他所有 promise 的全部结果。

永远要记住为每个 promise 关联一个拒绝 / 错误处理函数，特别是从 Promise.all([ .. ])返回的那一个。

```javascript
// request(..)是一个Promise-aware Ajax工具
// 就像我们在本章前面定义的一样
var p1 = request( "http://some.url.1/" );
var p2 = request( "http://some.url.2/" );
Promise.all( [p1,p2] )
.then( function(msgs){
 // 这里，p1和p2完成并把它们的消息传入
 return request(
 "http://some.url.3/?v=" + msgs.join(",")
 );
} )
.then( function(msg){
 console.log( msg );
} );
```

### 3.6.2 Promise.race([...])

与 Promise.all([ .. ]) 类似，一旦有任何一个 Promise 决议为完成，Promise.race([ .. ])就会完成；一旦有任何一个 Promise 决议为拒绝，它就会拒绝。

一项竞赛需要至少一个“参赛者”。所以，如果你传入了一个空数组，主 race([..]) Promise 永远不会决议，而不是立即决议。

```javascript
// request(..)是一个支持Promise的Ajax工具
// 就像我们在本章前面定义的一样
var p1 = request( "http://some.url.1/" );
var p2 = request( "http://some.url.2/" );
Promise.race( [p1,p2] )
.then( function(msg){
 // p1或者p2将赢得这场竞赛
 return request(
 "http://some.url.3/?v=" + msg
 );
} )
.then( function(msg){
 console.log( msg );
} );
```

这个可以处理之前说过的超时竞赛问题：

```javascript
// foo()是一个支持Promise的函数
// 前面定义的timeoutPromise(..)返回一个promise，
// 这个promise会在指定延时之后拒绝
// 为foo()设定超时
Promise.race( [
 foo(), // 启动foo()
 timeoutPromise( 3000 ) // 给它3秒钟
] )
.then(
 function(){
 // foo(..)按时完成！
 },
 function(err){
 // 要么foo()被拒绝，要么只是没能够按时完成，
 // 因此要查看err了解具体原因
 }
);
```

### 3.6.3 all([...])和 race([...]的变体

1.  none([ .. ])：这个模式类似于 all([ .. ])，不过完成和拒绝的情况互换了。所有的 Promise 都要被拒绝，即拒绝转化为完成值，反之亦然。
1.  any([ .. ])：这个模式与 all([ .. ]) 类似，但是会忽略拒绝，所以只需要完成一个而不是全部。
1.  first([ .. ])：这个模式类似于与 any([ .. ]) 的竞争，即只要第一个 Promise 完成，它就会忽略后续的任何拒绝和完成。
1.  last([ .. ])：这个模式类似于 first([ .. ])，但却是只有最后一个完成胜出。

```javascript
// polyfill安全的guard检查
if (!Promise.first) {
 Promise.first = function(prs) {
 return new Promise( function(resolve,reject){
 // 在所有promise上循环
 prs.forEach( function(pr){
 // 把值规整化
 Promise.resolve( pr )
 // 不管哪个最先完成，就决议主promise
 .then( resolve );
 } );
 } );
 };
}
```

### 3.6.4 并发迭代

在一列 Promise 中迭代，并对所有 Promise 都执行某个任务；

```javascript
var p1 = Promise.resolve( 21 );
var p2 = Promise.resolve( 42 );
var p3 = Promise.reject( "Oops" );
// 把列表中的值加倍，即使是在Promise中
Promise.map( [p1,p2,p3], function(pr,done){
 // 保证这一条本身是一个Promise
 Promise.resolve( pr )
 .then(
 // 提取值作为v
 function(v){
 // map完成的v到新值
 done( v * 2 );
 },
 // 或者map到promise拒绝消息
 done
 );
  if (!Promise.map) {
 Promise.map = function(vals,cb) {
 // 一个等待所有map的promise的新promise
 return Promise.all(
 // 注：一般数组map(..)把值数组转换为 promise数组
 vals.map( function(val){
 // 用val异步map之后决议的新promise替换val
 return new Promise( function(resolve){
 cb( val, resolve );
 } );
 } )
 );
 };
}
```

### 3.7 Promise API 概述

1.  new Promise 构造器
1.  Promise.resolve(..) 和 Promise.reject(..)
1.  then(..) 和 catch(..)
1.  Promise.all([ .. ]) 和 Promise.race([ .. ])

### 3.8 Promise 局限性

1.  顺序错误处理，Promise 链中的错误很容易被无意中默默忽略掉
1.  单一值 ，可以用解构形式优化
1.  单决议：只能决议一次
1.  惯性：将回调的方式改为 then，把需要回调的函数封装为支持 Promise 的函数这个动作有时被称为“提升”或“Promise 工厂化”。
1.  无法取消的 Promise：一旦创建了一个 Promise 并为其注册了完成和 / 或拒绝处理函数，如果出现某种情况使得这个任务悬而未决的话，你也没有办法从外部停止它的进程。
1.  Promise 性能：认 Promise 通常要比其非 Promise、非可信任回调的等价系统稍微慢一点

## 第 4 章 生成器

### 4.1 打破完整运行

生成器就是一类特殊的函数，可以一次或多次启动和停止，并不一定非得要完成。

1.  迭代消息传递

```
function *foo(x) {
 var y = x * (yield);
 return y;
}
var it = foo( 6 );
// 启动foo(..)
it.next();
var res = it.next( 7 );
res.value; // 42
// 首先，传入 6 作为参数 x。然后调用 it.next()，这会启动 *foo(..)。 在 *foo(..) 内部，开始执行语句 var y = x ..，但随后就遇到了一个 yield 表达式。它
//就会在这一点上暂停 *foo(..)（在赋值语句中间！），并在本质上要求调用代码为 yield
//表达式提供一个结果值。接下来，调用 it.next( 7 )，这一句把值 7 传回作为被暂停的
//yield 表达式的结果。
// 第一个 next(..) 总是启动一个生成器，并运行到第一个 yield 处。不过，是第二个
//next(..) 调用完成第一个被暂停的 yield 表达式，第三个 next(..) 调用完成第二个 yield，
//以此类推。
```

2.  双向消息传递系统

```
function *foo(x) {
 var y = x * (yield "Hello"); // <-- yield一个值！
 return y;
}
var it = foo( 6 );
var res = it.next(); // 第一个next()，并不传入任何东西
res.value; // "Hello"
res = it.next( 7 ); // 向等待的yield传入7
res.value; // 42
// 在生成器的起始处我们调用第一个 next() 时，还没有暂停的 yield 来接受这样一个值
// 启动生成器时一定要用不带参数的 next()
```

如果生成器中没有 return 来停止迭代，那么会有个隐式的 return undefined;

同一个生成器可以同时生产多个迭代器，而这些迭代器可以交替执行；

### 4.2 生成器产生值

手写迭代器

```
var something = (function(){
 var nextVal;
 return {
 // for..of循环需要
 [Symbol.iterator]: function(){ return this; },  // 实现这个就可以通过for of迭代
 // 标准迭代器接口方法
 next: function(){
 if (nextVal === undefined) {
 nextVal = 1;
 }
 else {
 nextVal = (3 * nextVal) + 6;
 }
 return { done:false, value:nextVal };
 }
 };
})();
```

iterable（可迭代）：一个包含可以在其值上迭代的迭代器的对象。

从一个 iterable 中提取迭代器的方法是：iterable 必须支持一个函数，其名称是专门的 ES6 符号值 Symbol.iterator。调用这个函数时，它会返回一个迭代器。

也可以手工调用这个函数，然后使用它返回的迭代器：

```
var a = [1,3,5,7,9];
var it = a[Symbol.iterator]( "Symbol.iterator");
it.next().value; // 1
it.next().value; // 3
it.next().value; // 5
```

生成器会在每次迭代中暂停，通过 yield 返回到主程序或事件循环队列中

for..of 循环内的 break 会触发 finally 语句 ,也可以在外部通过 return(..) 手工终止生成器的迭代器实例

```
function *something() {
    try {
        var nextVal;
        while (true) {
            if (nextVal === undefined) {
                nextVal = 1;
            }
            else {
                nextVal = (3 * nextVal) + 6;
            }
            yield nextVal;
        }
    }
        // 清理子句
    finally {
        console.log( "cleaning up!" );
    }
}
var it = something();
for (var v of it) {
    console.log( v );
    // 不要死循环！
    if (v > 500) {
        console.log(
            // 完成生成器的迭代器
            it.return( "Hello World" ).value
        );
        // 这里不需要break
    }
}
// 1 9 33 105 321 969
// 清理！
// Hello World
```

### 4.3 异步迭代生成器

实现顺序同步：

```
function foo(x,y) {
    ajax(
        "http://some.url.1/?x=" + x + "&y=" + y,
        function(err,data){
            if (err) {
                // 向*main()抛出一个错误
                it.throw( err );
            }
            else {
                // 用收到的data恢复*main()
                it.next( data );
            }
        }
    );
}
function *main() {
    try {
        var text = yield foo( 11, 31 );
        console.log( text );
    }
    catch (err) {
        console.error( err );
    }
}
var it = main();
// 这里启动！
it.next();
```

同样可以实现同步错误处理

```
function *main() {
 var x = yield "Hello World";
 yield x.toLowerCase(); // 引发一个异常！
}
var it = main();
it.next().value; // Hello World
try {
 it.next( 42 );
}
catch (err) {
 console.error( err ); // TypeError
}
```

### 4.4 生成器+Promise

```
function foo(x,y) {
 return request(
 "http://some.url.1/?x=" + x + "&y=" + y
 );
}
function *main() {
 try {
 var text = yield foo( 11, 31 );
 console.log( text );
 }
 catch (err) {
 console.error( err );
 }
}
var it = main();
var p = it.next().value;
// 等待promise p决议
p.then(
 function(text){
 it.next( text );
 },
 function(err){
 it.throw( err );
 }
);
```

支持 Promise 的 Generator Runner：

```
// 在此感谢Benjamin Gruenbaum （@benjamingr on GitHub）的巨大改进！
function run(gen) {
 var args = [].slice.call( arguments, 1), it;
 // 在当前上下文中初始化生成器
 it = gen.apply( this, args );
 // 返回一个promise用于生成器完成
 return Promise.resolve()
 .then( function handleNext(value){
 // 对下一个yield出的值运行
 var next = it.next( value );
 return (function handleResult(next){
 // 生成器运行完毕了吗？
 if (next.done) {
 return next.value;
 }
 // 否则继续运行
 else {
 return Promise.resolve( next.value )
 .then(
 // 成功就恢复异步循环，把决议的值发回生成器
 handleNext,
 // 如果value是被拒绝的 promise，
 // 就把错误传回生成器进行出错处理
 function handleErr(err) {
 return Promise.resolve(
 it.throw( err )
    )
 .then( handleResult );
 }
 );
 }
 })(next);
 } );
}
```

上面的 run 虽然可以执行同步的 promise，但是不能让多个异步并发执行；

Promise 所有的并发能力在生成器 +Promise 方法中都可以使用

```
function *foo() {
 // 让两个请求"并行"，并等待两个promise都决议
 var results = yield Promise.all( [
 request( "http://some.url.1" ),
 request( "http://some.url.2" )
 ] );
 var r1 = results[0];
 var r2 = results[1];
 var r3 = yield request(
 "http://some.url.3/?v=" + r1 + "," + r2
 );
 console.log( r3 );
}
// 使用前面定义的工具run(..)
run( foo );
```

### 4.5 生成器委托

yield * 把迭代器实例控制（当前 *bar() 生成器的）委托给 / 转移到了这另一个 \*foo() 迭代器；

```
function* foo () {
    console.log('*foo() starting');
    yield 3;
    yield 4;
    console.log('*foo() finished');
}

function* bar () {
    yield 1;
    yield 2;
    yield* foo(); // yield委托！
    yield 5;
}

var it = bar();
console.log(it.next().value); // 1
console.log(it.next().value); // 2
console.log(it.next().value); // *foo()启动
// 3
console.log(it.next().value); // 4
console.log(it.next().value); // *foo()完成
// 5
```

yield * 暂停了迭代控制，而不是生成器控制。当你调用 *foo() 生成器时，现在 yield 委托到了它的迭代器。但实际上，你可以 yield 委托到任意 iterable，yield \*[1,2,3] 会消耗数组值 [1,2,3] 的默认迭代器。

yield 委托的主要目的是代码组织，以达到与普通函数调用的对称。

```
function *foo() {
    console.log( "inside *foo():", yield "B" );
    console.log( "inside *foo():", yield "C" );
    return "D";
}
function *bar() {
    console.log( "inside *bar():", yield "A" );
    // yield委托！
    console.log( "inside *bar():", yield *foo() );
    console.log( "inside *bar():", yield "E" );
    return "F";
}
var it = bar();
console.log( "outside:", it.next().value );
// outside: A
console.log( "outside:", it.next( 1 ).value );
// inside *bar(): 1
// outside: B
console.log( "outside:", it.next( 2 ).value );
// inside *foo(): 2
// outside: C
console.log( "outside:", it.next( 3 ).value );
// inside *foo(): 3
// inside *bar(): D
// outside: E
console.log( "outside:", it.next( 4 x).value );
// inside *bar(): 4
// outside: F
```

(1) 值 3（通过 *bar() 内部的 yield 委托）传入等待的 *foo() 内部的 yield "C" 表达式。

(2) 然后 \*foo() 调用 return "D"，但是这个值并没有一直返回到外部的 it.next(3) 调用。

(3) 取而代之的是，值 "D" 作为 *bar() 内部等待的 yield*foo() 表达式的结果发出——这个 yield 委托本质上在所有的 *foo() 完成之前是暂停的。所以 "D" 成为 *bar() 内部的最后结果，并被打印出来。

(4) yield "E" 在 \*bar() 内部调用，值 "E" 作为 it.next(3) 调用的结果被 yield 发出。

yield 委托甚至并不要求必须转到另一个生成器，它可以转到一个非生成器的一般 iterable。

```
function *bar() {
    console.log( "inside *bar():", yield "A" );
// yield委托给非生成器！
    console.log( "inside *bar():", yield *[ "B", "C", "D" ] );
    console.log( "inside *bar():", yield "E" );
    return "F";
}
var it = bar();
console.log( "outside:", it.next().value );
// outside: A
console.log( "outside:", it.next( 1 ).value );
// inside *bar(): 1
// outside: B
console.log( "outside:", it.next( 2 ).value );
// outside: C
console.log( "outside:", it.next( 3 ).value );
// outside: D
console.log( "outside:", it.next( 4 ).value );
// inside *bar(): undefined
// outside: E
console.log( "outside:", it.next( 5 ).value );
// inside *bar(): 5
// outside: F
```

异常也可以被委托

```
function *foo() {
    try {
        yield "B";
    }
    catch (err) {
        console.log( "error caught inside *foo():", err );
    }
    yield "C";
    throw "D";
}
function *bar() {
    yield "A";
    try {
        yield *foo();
    }
    catch (err) {
        console.log( "error caught inside *bar():", err );
    }
    yield "E";
    yield *baz();
    // 注：不会到达这里！
    yield "G";
}
function *baz() {
    throw "F";
}
var it = bar();
console.log( "outside:", it.next().value );
// outside: A
console.log( "outside:", it.next( 1 ).value );
// outside: B
console.log( "outside:", it.throw( 2 ).value );
// error caught inside *foo(): 2
// outside: C
console.log( "outside:", it.next( 3 ).value );
// error caught inside *bar(): D
// outside: E
try {
    console.log( "outside:", it.next( 4 ).value );
}
catch (err) {
    console.log( "error caught outside:", err );
}
// error caught outside: F
```

递归委托

```
function *foo(val) {
    if (val > 1) {
        // 生成器递归
        val = yield *foo( val - 1 );
    }
    return yield Promise.resolve(() => {
        console.log('settimeout', val);
    });
}
function *bar() {
    var r1 = yield *foo( 3 );
    // console.log( r1 );
}
```

### 4.6 生成器并发

通信顺序进程（Communicating Sequential Processes，CSP）： 讲道理 这里没看懂 跳过吧

### 4.7 形式转换程序

形实转换程序（thunk）：JavaScript 中的 thunk 是指一个用于调用另外一个函数的函数，没有任何参数

```
function foo(x,y,cb) {
 setTimeout( function(){
 cb( x + y );
 }, 1000 );
}
function fooThunk(cb) {
 foo( 3, 4, cb );
}
// 将来
fooThunk( function(sum){
 console.log( sum ); // 7
} );
```

## 第 5 章 程序性能

### 5.1 Web Worker

Worker 之间以及它们和主程序之间，不会共享任何作用域或资源，那会把所有多线程编程 的噩梦带到前端领域，而是通过一个基本的事件消息机制相互联系。

Web Worker 通常应用于哪些方面呢?

- 处理密集型数学计算
- 大数据集排序
- 数据处理(压缩、音频分析、图像处理等)

高流量网络通信

使用 worker 的应用需要在线程之间通过事件机制传递大量的信息，可能是双向的。

在早期的 Worker 中，唯一的选择就是把所有数据序列化到一个字符串值中。除了双向序 列化导致的速度损失之外，另一个主要的负面因素是数据需要被复制，这意味着两倍的内 存使用(及其引起的垃圾收集方面的波动)。

如果要传递一个对象，可以使用结构化克隆算法，这样就不用付出 to-string 和 from-string 的性能损失了，但是这种方案还是要使用双倍的内存。IE10 及更高版本以及所有其他主流浏览器都支持这种方案。

还有一个更好的选择，特别是对于大数据集而言，就是使用 Transferable 对象

这时发生的是对象所 有权的转移，数据本身并没有移动。一旦你把对象传递到一个 Worker 中，在原来的位置 上，它就变为空的或者是不可访问的，这样就消除了多线程编程作用域共享带来的混乱。 当然，所有权传递是可以双向进行的。

**共享 Worker：** 整个站点或 app 的所有页面实例都可以共享的中心 Worker；

如果有某个端口连接终止而其他端口连接仍然活跃，那么共享 Worker 不会 终止。而对专用 Worker 来说，只要到实例化它的程序的连接终止，它就会 终止。

## 第 6 章 性能测试和调优

### 6.1 性能测试

用重复运行，然后取平均值去测试性能误差会比较大，应该基于统计学去实践，比如 Benchmark.js 库；

### 6.2 环境为王

测试不真实的代码只能得出不真实的结论。如果有实际可能的话，你 应该测试实际的而非无关紧要的代码，测试条件与你期望的真实情况越接近越好。只有这 样得出的结果才有可能接近事实。

### 6.3 jsPerf.com

如果你想要得到可靠的测试结论的话，就需要在很多不同的环境(桌面浏览 器、移动设备，等等)中测试汇集测试结果。

### 6.4 写好测试用例

写有意义的测试用例；

### 6.5 微性能

只考虑微性能是不合理的，可读性，包括一些浏览器不同处理问题也要考虑；

### 6.6 尾调用优化

尾调用：一个出现在另一个函数“结尾”处的函数调用；

调用一个新的函数需要额外的一块预留内存来管理调用 栈，称为栈；

尾调用优化（TCO）：不需要创建一个新的栈帧，而是可以重用已 有的 bar(..) 的栈帧。这样不仅速度更快，也更节省内存

TCO 允许一个函数在结尾处调用另外一个函数来执行，不需要任何额外资源。这 意味着，对递归算法来说，引擎不再需要限制栈深度。
