# 第一部分 作用域和闭包

## 第一章 作用域

当前代码所执行的上下文，即当前可访问的所有变量的集合；

### 编译原理

编译的三个步骤：

1.  **分词/词法分析（Tokenizing/Lexing）** ：将字符串分解成有意义的代码块，这些代码块被称为词法单元（token）。如将var a = 2;分解为var、a、=、2、；。空格是否被当做词法单元，取决于空格在这门语言中是否有意义；（分词和词法的区别在于拆分的词法单元是否有状态，有状态是词法，无状态是分词）；
1.  **解析/语法分析（Parsing）** **：** 将词法单元流（数组）转换成一个由元素逐级嵌套所组成的代表了程序语法结构的树，即AST抽象语法树；
1.  **代码生成** **：** 将AST转换为可执行代码（机器指令）的过程；



JS引擎除了要做上面那三步，还需要在语法分析和代码生成阶段有特定的步骤来对运行性能进行优化，如对冗余元素进行优化；JS的编译过程也不是发生在构建之前，而是发生在代码执行前的几微秒（甚至更短）的时间内。

作用域的背后是用各种如JIT、延迟编译、重编译等办法来保证性能的最佳；



编译完成上面三个步骤之后，则需要把生成的运行时所需的代码给引擎做处理；

如var a = 2；这段赋值操作，编译器会在当前作用域中声明一个变量（如果作用域已经有了，会直接忽略），然后在运行时引擎会在作用域中查找该变量，如果有，才会对它进行赋值；

引擎：从头到尾负责整个javascript程序的编译及执行；

编译器：负责语法分析及代码生成等脏活累活；

作用域：负责收集并维护由所有声明的标识符（变量）组成的一系列查询，并实施一套非常严格的规则，确定当前执行的代码对这些标识符的访问权限；



**RHS（retrieve his source value）查询：** 查找某个变量的值；

**LHS（list his source value）查询：** 找到变量的容器，从而对其赋值；



例子：

```javascript
function foo(a) {
	console.log(a); // 2
}
foo(2);
```

1.  对foo进行RHS查询；
1.  对a进行LHS查询，把2赋值給它；
1.  对console进行RHS查询；
1.  对a进行RHS查询；

PS：编译器可以在代码生成的同时处理声明和值的定义，所以在引擎执行代码时，不会有专门的线程将函数分配給foo，所以，这里没有LHS查询；



### 作用域嵌套

当一个块或函数嵌套在另一个块或函数中时，则发生了作用域嵌套；当在当前作用域中无法找到某个变量时，引擎就会在外层嵌套的作用域中继续查找；如果继续找不到，则继续向上一层级继续查找，直到抵达全局作用域时，还没找到，则停止；



### 异常

在作用域链中找不到对应的变量时，RHS查询会抛出ReferenceError异常；LHS查询在严格模式下，也会抛出ReferenceError，宽松/懒惰模式则会自动在全局作用域下创建声明该变量；

对变量的值进行不合理操作会抛出TypeError异常；

ReferenceError同作用域判别失败相关，而TypeError代表作用域判断成功了，但是对结果的操作不合法；



## 第二章 词法作用域

词法作用域意味着作用域是由书写代码时变量声明的位置来决定的，编译的词法分析阶段基本能够知道全部标识符在哪里以及如何声明的，从而能预测在执行过程中如何对他们进行查找；

全局变量会自动成为全局对象的属性；

遮蔽效应：在多层嵌套的作用域中，在作用域链中查找变量，当找到变量就会停止，所以会“遮蔽”了外部的同名变量；



### 欺骗词法

词法作用域一般完全由写代码期间函数所声明的位置定义，欺骗此法即是修改词法作用域；

欺骗词法作用域会导致性能下降；

1.  用eval修改词法作用域（修改到全局）；严格模式下，eval运行在自己的词法作用域中，所以无法修改所在的作用域；
1.  setTimeout、setInterval、new Function同上；
1.  with；with可以将一个对象处理为一个完全隔离的词法作用域，因此这个对象的属性也会被处理为定义在这个作用域中的词法标识符；eval是修改其所处的词法作用域，with是根据传递的对象凭空创建一个全新的词法作用域；



### 性能

使用eval和with会使得引擎无法在编译时对作用域查找进行优化，过多使用会导致运行变慢；

## 第三章 函数作用域和块作用域

**函数作用域：** 属于这个函数的全部变量都可以在整个函数的范围内使用及复用；

**隐藏内部实现：** 通过作用域，将一些变量、方法封装起来，避免污染全局；

**规避冲突：** 使用命令空间、模块管理；

**自执行函/函数表达式：** 隐藏标识符且不污染所在作用域的方法；

**函数声明和函数表达式：** 他们的名称标识符是否会绑定到某个作用域中，表达式不会，声明会；

**匿名函数表达式：** 省略函数名；缺点如下：

1.  在栈追踪中不会显示出有意义的函数名，影响调试；
1.  递归只能使用过期的arguments.callee；
1.  可读性、可理解性不好；



行内表达式在封装上非常有用，所以推荐具名行内表达式；

**立即执行函数表达式（Immediately Invoked Function Expression）** ：（function() {})();

进阶用法： 把参数传递进去；

```javascript
var a = 2;
(function IIFE(global) {
	var a = 3;
  console.log(a);// 3
  console.log(global.a);// 2;
})(window);
console.log(a);// 2
```

**块作用域：** 其他类型的作用域单元；表面上看 JavaScript 并没有块作用域的相关功能；for、if这种都是绑定到上层；

**with：** with从对象中创建出的作用域仅在with声明中而非外部作用域有效；

**try/catch：** catch会创建一个块作用域，声明的变量仅在catch内部有效；

**let：** 存在块级作用域；不存在变量提升；

**块作用域重要作用：** 帮助将一些没有用的变量被回收；

**const：** 常量，值是不能改的；

## 第四章 提升

**变量提升：** 将变量的声明提升到当前作用域的最上方；

编译器在编译时，会将所有的声明找到并绑定当前作用域，而其他操作会留在原地待命；

**函数声明会被提升，但函数表达式不会被提升；**

**函数优先：** 变量和函数提升的时候，先把所有的函数提升到最上面，然后依次是变量；

## 第五章 作用域闭包

闭包：能够访问自由变量的方法所在的作用域形成的闭包；

### 模块

模块模式需具备的两个必要条件：

1.  必须有外部的封闭函数，该函数必须至少被调用一次；
1.  封闭函数返回至少一个内部函数，这样内部函数才能在私有作用域中形成闭包，并且可以访问或修改私有的状态；

如下：

```javascript
function CoolModule() {
  var something = "cool"; 
  var another = [1, 2, 3];
  function doSomething() { 
    console.log( something ); 
  }
  function doAnother() { 
    console.log( another.join( " ! " ) ); 
  }
  return { 
    doSomething: doSomething, 
    doAnother: doAnother 
  }; 
}
var foo = CoolModule(); 
foo.doSomething(); // cool foo.doAnother(); // 1 ! 2 ! 3
```



单例模式：

```javascript
var foo = (function CoolModule() {
  var something = "cool";
  var another = [1, 2, 3];
  function doSomething() { 
    console.log( something ); 
  }
  function doAnother() { 
    console.log( another.join( " ! " ) ); 
  }
  return { 
    doSomething: doSomething, 
    doAnother: doAnother 
  }; 
})(); 
foo.doSomething(); // cool foo.doAnother(); // 1 ! 2 ! 3
```



未来的模块机制：es6的导入导出import和export；

# 第二部分 this和对象原型

## 第一章 关于this

this的指向：最后调用它的那个对象或上下文；



## 第二章 this全面解析

函数调用栈：执行当前操作时，整个执行函数的调用层级列表；

隐式绑定：默认绑定最后调用他的对象或上下文；

显式绑定：bind、call、apply等操作；

硬绑定：

```javascript
function foo() {
    console.log(this.a);
}

var obj = { a: 2 };
var bar = function () {
    foo.call(obj);
};
bar(); // 2
setTimeout( bar, 100 ); // 2 硬绑定的 bar 不可能再修改它的this 
bar.call( window ); // 2

function foo(something) {
    console.log(this.a, something);
    return this.a + something;
}

var obj = { a: 2 };
var bar = function () {
    return foo.apply(obj, arguments);
};
var b = bar(3); // 2 3 
console.log( b ); // 5
```

new：绑定this；

步骤：

1.  创建（或者说构造）一个全新的对象。
1.  这个新对象会被执行 [[ 原型 ]] 连接。
1.  这个新对象会绑定到函数调用的 this。
1.  如果函数没有返回其他对象，那么 new 表达式中的函数调用会自动返回这个新对象。



new和显示绑定优先级高于隐式绑定；

1. 函数是否在 new 中调用（new 绑定）？如果是的话 this 绑定的是新创建的对象。 var bar = new foo()

2. 函数是否通过 call、apply（显式绑定）或者硬绑定调用？如果是的话，this 绑定的是 指定的对象。 var bar = foo.call(obj2)

3. 函数是否在某个上下文对象中调用（隐式绑定）？如果是的话，this 绑定的是那个上 下文对象。 var bar = obj1.foo()

4. 如果都不是的话，使用默认绑定。如果在严格模式下，就绑定到 undefined，否则绑定到 全局对象。 var bar = foo()



apply的其他用处：展开字符串

```javascript
function foo(a, b) {
    console.log('a:' + a + ';b:' + b);
}

foo.apply(null, [1, 2]); // a:1;b:2
```

bind其他用法：柯里化

```javascript
function foo(a, b) {
    console.log('a:' + a + ';b:' + b);
}

let bar = foo.bind(null, 2);
bar(3);// a:2;b:3
```

Object.create(null) 和{}很像，但是并不会创建 Object. prototype 这个委托，所以它比 {}“更空”：



软绑定：会对指定的函 数进行封装，首先检查调用时的 this，如果 this 绑定到全局对象或者 undefined，那就把 指定的默认对象 obj 绑定到 this，否则不会修改 this。

```javascript
if (!Function.prototype.softBind) {
    Function.prototype.softBind = function (obj) {
        var fn = this; // 捕获所有 curried 参数
        var curried = [].slice.call(arguments, 1);
        var bound = function () {
            return fn.apply((!this || this === (window || global)) ? obj : this, curried.concat.apply(curried, arguments));
        };
        bound.prototype = Object.create(fn.prototype);
        return bound;
    };
}

function foo() {
    console.log('name: ' + this.name);
}

var obj = {name: 'obj'},
    obj2 = {name: 'obj2'},
    obj3 = {name: 'obj3'};
var fooOBJ = foo.softBind(obj);
fooOBJ(); // name: obj
obj2.foo = foo.softBind(obj);
obj2.foo(); // name: obj2 <---- 看！！！
fooOBJ.call(obj3); // name: obj3 <---- 看！
setTimeout(obj2.foo, 10); // name: obj <---- 应用了软绑定
```

箭头函数：绑定this，而且绑定后无法被修改，new也不行；

```javascript
function foo() { // 返回一个箭头函数
    return (a) => { //this 继承自 foo()
        console.log(this.a);
    };
}

var obj1 = {a: 2};
var obj2 = {
    a: 3
};
var bar = foo.call(obj1);
bar.call(obj2); // 2, 不是 3 ！
```

## 第三章 对象

字面量形式：

```javascript
var myObj = {

}
```

构造形式：

```javascript
var myObj = new Object();
muObj.key = value;
```

js基础类型：string、number、boolean、null、undefined、object；

简单基础类型并不是对象。

JavaScript 中有许多特殊的对象子类型，我们可以称之为复杂基本类型。如函数、数组；

内置对象：String • Number • Boolean • Object • Function • Array • Date • RegExp • Error

内置函数可以当作构造函数 （由 new 产生的函数调用——参见第 2 章）来使用，从而可以构造一个对应子类型的新对 象。

```javascript
var strPrimitive = "I am a string";
typeof strPrimitive; // "string" strPrimitive instanceof String; // false
var strObject = new String( "I am a string" );
typeof strObject; // "object" 
strObject instanceof String; // true 
// 检查 sub-type 对象 
Object.prototype.toString.call( strObject ); // [object String]
```

会自动把字符串字面量转换成一个 String 对象，可以访问属性和方法



属性访问方法：.方式，称之为属性；[]方式，称之为键；

```javascript
var myObject = {};
myObject[true] = 'foo';
myObject[3] = 'bar';
myObject[myObject] = 'baz';
myObject['true']; // "foo"
myObject["3"]; // "bar"
myObject["[object Object]"]; // "baz"
```

可计算属性名：最常用的场景可能是es6的符号（Symbol）

```javascript
var prefix = 'foo';
var myObject = {
    [prefix + 'bar']: 'hello',
    [prefix + 'baz']: 'world'
};
myObject['foobar']; // hello 
myObject["foobaz"]; // world
```



方法：属于对象（也被称为“类”）的函数通常被称为方法；

这里作者的意思是，所有的函数都可以被绑定this，而js中从本质上讲，并不是把函数归属到某个对象中，而this是在运行时根据调用位置动态绑定的，所以他们最多算间接关系，不是归属关系；所以js的函数访问为属性访问，不是方法访问；



这里我并不完全赞同作者的看法，举个例子

```javascript
var test = function () {
    console.log(1, this.a)
}
class Test {
    constructor(a) {
        this.a = a;
    }

}
var a = 'window a';

var obj = new Test('test a');
obj.test = test;
console.log(obj.test()) // test a
test = function () {
    console.log(2, this.a)
}
console.log(test()) // 2 window a
```

这里把函数test赋值给obj.test之后，更改了test，test变了，但是obj的test并没有变，也就是说，当你操作obj的test的时候，只能通过obj才能去取得或操作它，所以它是属于obj的，而方法的定义是属于对象的函数，因此把对象的函数称为方法，我觉得没毛病；作者说的函数中的this可以绑定到其他地方去，所以不属于对象，但是 如果去重新绑定this是不是还是得去用obj去重新绑？



**数组：** 比普通对象多了下标和根据数组行为和用途进行了优化；



复制对象

浅复制：复制的属性如果是对象，那么只是复制引用；

深复制：复制的属性如果是对象，那么是新创建一个对象；



由于 Object.assign(..) 就是使用 = 操作符来赋值，所以源对象属性的一些特性（比如 writable）不会被复制到目标对象。



属性描述符

```javascript
// writable
var myObject = {};
Object.defineProperty(myObject, 'a', {
    value: 2,
    writable: false, // 不可写
    configurable: true,
    enumerable: true
});
myObject.a = 3; // 严格模式下会报错TypeError setter也会报这个错
myObject.a; // 2
// configurable
var myObject = {a: 2};
myObject.a = 3;
myObject.a; // 3
Object.defineProperty(myObject, 'a', {
    value: 4,
    writable: true,
    configurable: false, // 不可配置！
    enumerable: true
});
myObject.a;// 4
myObject.a = 5;
myObject.a; // 5
Object.defineProperty(myObject, 'a', {
    value: 6,
    writable: true,
    configurable: true,
    enumerable: true
}); // TypeError 不可逆过程 但是这里可以把 writable 的状态由 true 改为 false，但是无法由 false 改为 true。还会禁止删除这个属性；如delete myObject.a;会不生效
```

enumerable

配置为false，就不可以对该属性进行for in循环



不变性：在es5中如何实现属性或对象不可改变，操作有如下；

1.  对象常量：可以把writable和configurable设置为false；
1.  禁止扩展：禁止对对象添加属性并且保留已有属性，使用Object.preventExtensions(myObj)；在非严格模式下，创建属性 b 会静默失败。在严格模式下，将会抛出 TypeError 错误。
1.  密封：Object.seal(..)，会在一个现有对象上调用 Object.preventExtensions(..) 并把所有现有属性标记为 configurable:false。不仅不能添加新属性，也不能重新配置或者删除任何现有属性（虽然可以 修改属性的值）。
1.  冻结：Object.freeze(..)，会在一个现有对象上调用 Object.seal(..) 并把所有“数据访问”属性标记为 writable:false，这样就无法修改它们 的值。



[[Get]]：获取值的算法；

[[Put]]：修改值的算法；

[[Put]] 算法：

1. 属性是否是访问描述符？如果是并且存在 setter 就调用 setter。

2. 属性的数据描述符中 writable 是否是 false ？如果是，在非严格模式下静默失败，在 严格模式下抛出 TypeError 异常。

3. 如果都不是，将该值设置为属性的值。



Getter和Setter

```javascript
var myObject = { // 给 a 定义一个 getter
    get a() {
        return 2;
    }
};
Object.defineProperty(myObject, // 目标对象
    'b', // 属性名 对象 ｜ 117
    { // 描述符 // 给 b 设置一个 getter
        get: function () {
            return this.a * 2;
        }, // 确保 b 会出现在对象的属性列表中
        set: function (val) {
            console.log(val);
        },
        enumerable: true
    });
myObject.a; // 2 myObject.b; // 4
```

判断对象中是否存在某个属性：

1.  'a' in myObj; 这种会遍历原型链，只是判断属性，而不是值，所以4 in [1,3,4]是false；
1.  myObj.hasOwnProperty('a');不会遍历原型链；



枚举：通过enumerable设置是否可枚举，不管是不是可以枚举，都可以通过hasOwnProperty判断是否存在，但是不能通过for in去遍历到；是否可枚举可用myObj.propertyIsEnumerable('a')去判断（不遍历原型链）；

1.  Object.keys返回的数组只包括可枚举的；
1.  Object.getOwnPropertyNames返回的数组包含所有属性；
1.  上面两种形式 都不遍历原型链；



遍历：

1.  forEach遍历且忽略回调函数的返回值；
1.  every遍历直到回调函数返回false停止；
1.  somoe遍历直到回调函数返回true停止；
1.  for of 遍历值；



写一个迭代器

```javascript
var myObject = {a: 2, b: 3};
Object.defineProperty(myObject, Symbol.iterator, {
    enumerable: false, writable: false, configurable: true, value: function () {
        var o = this;
        var idx = 0;
        var ks = Object.keys(o);
        return {
            next: function () {
                return {value: o[ks[idx++]], done: (idx > ks.length)};
            }
        };
    }
}); // 手动遍历 myObject
var it = myObject[Symbol.iterator]();
it.next(); // { value:2, done:false }
it.next(); // { value:3, done:false }
it.next(); // { value:undefined, done:true }
// 用 for..of 遍历 myObject
for (var v of myObject) {
    console.log(v);
}// 2 // 3
```

无限迭代器：

```javascript
var randoms = {
    [Symbol.iterator]: function () {
        return {
            next: function () {
                return {value: Math.random()};
            }
        };
    }
};
var randoms_pool = [];
for (var n of randoms) {
    randoms_pool.push(n); // 防止无限运行！
    if (randoms_pool.length === 100) break;
}
```



## 第四章 混合对象“类”

封装、继承、多态；

js中，类是一种设计模式；

类意味着复制，实例化时复制到实例中，继承复制到子类；

混入模式可以用来模拟类的复制行为，但是通常会产生丑陋且脆弱的语法，让代码更加难懂和难以维护；



## 第五章 原型

属性设置和屏蔽

myObj.foo = 'bar';

如果foo不存在myObj，则会遍历原型链，如果原型链上没有，foo会被直接添加到myObj上，如果foo位于原型链上层，则是以下三种情况；

1. 如果在 [[Prototype]] 链上层存在名为 foo 的普通数据访问属性（参见第 3 章）并且没有被标记为只读（writable:false），那就会直接在 myObject 中添加一个名为 foo 的新 属性，它是屏蔽属性。

2. 如果在 [[Prototype]] 链上层存在 foo，但是它被标记为只读（writable:false），那么无法修改已有属性或者在 myObject 上创建屏蔽属性（这个限制只存在于 = 赋值中，使用 Object. defineProperty(..) 并不会受到影响）。如果运行在严格模式下，代码会抛出一个错误。否则，这条赋值语句会被忽略。总之，不会发生屏蔽。

3. 如果在 [[Prototype]] 链上层存在 foo 并且它是一个 setter（参见第 3 章），那就一定会调用这个 setter。foo 不会被添加到（或者说屏蔽于）myObject，也不会重新定义 foo 这 个 setter。

```javascript
// 这种也会产生隐式屏蔽
var anotherObject = {a: 2};
var myObject = Object.create(anotherObject);
anotherObject.a; // 2 myObject.a; // 2 原型 ｜ 145
anotherObject.hasOwnProperty('a'); // true 
myObject.hasOwnProperty('a'); // false 
myObject.a++; // 隐式屏蔽！ 同 myObject.a = myObject.a + 1
anotherObject.a; // 2 
myObject.a; // 3 
myObject.hasOwnProperty('a'); // true
```

类

javaScript 才是真正应该被称为“面向对象”的语言，因为它是少有的可以不通过类，直接创建对象的语言。 在 JavaScript 中，类无法描述对象的行，（因为根本就不存在类！）对象直接定义自己的行为。

JavaScript 中只有对象。



原型继承：继承意味着复制操作，js并不会复制，而是一个对象可以通过委托访问另一个对象的属性和函数。



new 会劫持所有普通函数并用构造对象的形式来调用它



构造函数：

```javascript
function Foo(name) {
    this.name = name;
}
var a = new Foo('a');
a.constructor
// ƒ Foo(name) {
//    this.name = name;
// }
// 这里看起来像Foo是a的构造函数，但是实际上只是a委托了Foo.prototype的所有对象而已，新对象并不会自动获取.construstor
Foo.prototype.constructor // 打印一下，
// ƒ Foo(name) {
//    this.name = name;
// }

function Foo() { /* .. */ } 
Foo.prototype = { /* .. */ }; // 创建一个新原型对象
var a1 = new Foo(); 
a1.constructor === Foo; // false! 
a1.constructor === Object; // true!
```

给 Foo.prototype 添加一个 .constructor 属性，不过这需要手动添加一个符合正常行为的不可枚举（参见第 3 章）属性。

```javascript
function Foo() { /* .. */ } 
Foo.prototype = { /* .. */ }; // 创建一个新原型对象 
// 需要在 Foo.prototype 上“修复”丢失的 .constructor 属性 
// 新对象属性起到 Foo.prototype 的作用 
// 关于 defineProperty(..)，参见第 3 章 
Object.defineProperty( Foo.prototype, "constructor" , { 
  enumerable: false, 
  writable: true, 
  configurable: true, 
  value: Foo // 让 .constructor 指向 Foo 
} );
```

.constructor 并不是一个不可变属性。它是不可枚举（参见上面的代码）的，但是它的值是可写的（可以被修改）。



比较好的委托的写法：

```javascript
function Foo(name) {
  this.name = name; 
}
Foo.prototype.myName = function() {
  return this.name; 
};
function Bar(name,label) { 
  Foo.call( this, name );
  this.label = label; 
}
// 我们创建了一个新的 Bar.prototype 对象并关联到 Foo.prototype 
Bar.prototype = Object.create( Foo.prototype ); 
// 注意！现在没有 Bar.prototype.constructor 了 
// 如果你需要这个属性的话可能需要手动修复一下它 
Bar.prototype.myLabel = function() {
  return this.label; 
};
var a = new Bar( "a", "obj a" ); 
a.myName(); // "a" 
a.myLabel(); // "obj a"
```

上面为什么用了Object.create这种方式，其实还有两种方式；

1.  Bar.prototype=Foo.prototype；这种就是直接引用到Foo.prototype上，会导致修改Bar的原型方法或属性会修改到Foo上去，这样子的话直接修改Foo就好了，Bar的意义就不存在了；
1.  Bar.prototype=new Foo();这种会产生副作用，之前说过，new的时候会调用对应的函数，如果函数中有多余的操作，会影响到Bar；



但是Object.create有个缺点：需要创建一个新对象然后把旧对象抛弃掉，所以不能直接修改已有的默认对象。



ES6 添加了辅助函数 **Object.setPrototypeOf(..)** ，可以用标准并且可靠的方法来修改关联。

// ES6 之前需要抛弃默认的

Bar.ptototype = Object.create( Foo.prototype );

// ES6 开始可以直接修改现有的

Object.setPrototypeOf( Bar.prototype, Foo.prototype );

如果忽略掉 Object.create(..) 方法带来的轻微性能损失（抛弃的对象需要进行垃圾回收），它实际上比 ES6 及其之后的方法更短而且可读性更高。不过无论如何，这是两种完全不同的语法。



**内省(或者反射)：** 检查一个实例(JavaScript 中的对象)的继承祖先(JavaScript 中的委托关联)。

1.  a instanceof b; 表示b的原型是否出现在a的原型链上，但是这种方法b不可以是对象实例；
1.  b.isPrototypeOf(a)；
1.  Object.getPrototypeOf(a) === b或Object.getPrototypeOf( a ) === Foo.prototype;
1.  a.__proto__ === b



__proto__ 的实现大致上是这样的

```javascript
Object.defineProperty( Object.prototype, "__proto__", { 
  get: function() {
		return Object.getPrototypeOf(this); 
  },
	set: function(o) {
	// ES6 中的 setPrototypeOf(..) 
    Object.setPrototypeOf(this, o); 
    return o;
	} 
});
```

Object.create模拟

```javascript
if(!Object.create) {
	Object.create = function (o) {
  	function F () {};
    F.prototype = o;
    return new F();
  }
}
```

Object.create(..) 的第二个参数指定了需要添加到新对象中的属性名以及这些属性的属性，但是这种用的不多，所以没有模拟出来

## 第六章 行为委托

JavaScript 中这个机制的本质就是对象之间的关联关系。

委托行为意味着某些对象（XYZ）在找不到属性或者方法引用时会把这个请求委托给另一 个对象（Task）。



1.  互相委托（禁止）
1.  调试

```javascript
function Foo() {
}

var a1 = new Foo();
a1.constructor; 
// 谷歌下为 Foo {} 火狐下为 Object {  }  
//chrome 实际上想说的是“{} 是一个空对象，由名为 Foo 的函数构造”。
//Firefox 想说的是“{} 是一个空对象，由 Object 构造”。之所以有这种细微的差别，
//是因为 Chrome 会动态跟踪并把 实际执行构造过程的函数名当作一个内置属性，
// 但是其他浏览器并不会跟踪这些额外的信息。
a1.constructor.name; // 都是Foo
function Foo() {
}

var a1 = new Foo();
Foo.prototype.constructor = function Gotcha() {
};
a1.constructor; // Gotcha(){} 
a1.constructor.name; // "Gotcha" 
a1; // Foo {}
// 即使我们把 a1.constructor.name 修改为另一个合理的值（Gotcha），Chrome 控制台仍然会 输出 Foo。
var Foo = {};
var a1 = Object.create(Foo);
a1; // Object {}
Object.defineProperty(Foo, "constructor", {
    enumerable: false, value: function Gotcha() {
    }
});
a1; // Gotcha {}
```

比较思维模型：通过对象关联比原型链挂载方式更加简洁（Foo.prototype.speak =...）；



我们用一种简单的设计实现了同样的功能，这就是对象关联风格代码和行为委托设计模式的力量。

反词法：简洁方法有一个非常小但是非常重要的缺点，就是去掉语法糖之后会变成匿名函数；就会带来匿名函数的缺点；

鸭子类型：如果看起来像鸭子，叫起来像鸭子， 那就一定是鸭子。

我们认为 JavaScript 中对象关联比类风格的代码更加简洁（而且功能相同）。

行为委托认为对象之间是兄弟关系，互相委托，而不是父类和子类的关系

当你只用对象来设计代码时，不仅可以让语法更加简洁，而且可以让代码结构更加清晰。



为 ES6 的 class 语法是向 JavaScript 中引入了一种新的“类”机制，其实不是这样。class 基本上只是现有 [[Prototype]]（委托！）机制的一种语法糖。
