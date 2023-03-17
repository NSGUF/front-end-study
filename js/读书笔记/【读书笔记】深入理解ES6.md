# 第一章 块级绑定

var没有块级作用域，只要创建了，就会提升声明；

let：

1.  禁止重复声明；
1.  不会提升变量，存在暂时性死区（temporal dead zone），声明之前使用变量会报错，暂时性死区是绑定在let声明的作用域内的，对作用域的外层作用域不影响；
1.  全局块级绑定：即使在全局作用域上创建一个变量，会屏蔽window.xx上的，不会重写window上的变量；

const：

1.  拥有let的那些特性；
1.  会阻止对于变量绑定与变量自身值的修改，并不会阻止对 变量成员的修改；
1.  for (const i = 0; i < 10; i++)是会报错的；
1.  在 for-in 或 for-of 循环中使用时，与 let 变量效果相同，因为这种每次迭代创建了一个新的变量绑定，而不是试图去修改已绑定的变量的值；

块级绑定当前的最佳实践就是：在默认情况下使用 const ，而只在你知道变量值需要被更改 的情况下才使用 let 。这在代码中能确保基本层次的不可变性，有助于防止某些类型的错误。

# 第二章 字符串和正则表达式

**字符串新增方法：**

1.  includes 存在某个字符串
1.  startsWith 以某个字符串开头 第一个参数是字符，第二个参数是开始位置
1.  endsWith 以某个字符串结尾 第一个参数是字符，第二个参数是开始位置，从后面开始
1.  repeat 将初始字符串重复指定次数的新字符串
1.  模板字面量：``
1.  多行字符串：使用模板，直接换行，模板字符串会识别；
1.  完整的 Unicode 支持允许 JS 以合理的方式处理 UTF-16 字符；

```javascript
var msg = "Hello world!";
console.log(msg.startsWith("Hello")); // true 
console.log(msg.endsWith("!")); // true 
console.log(msg.includes("o")); // true
console.log(msg.startsWith("o")); // false 
console.log(msg.endsWith("world!")); // true 
console.log(msg.includes("x")); // false 
console.log(msg.startsWith("o", 4)); // true 
console.log(msg.endsWith("o", 8)); // true 
console.log(msg.includes("o", 8)); // false
console.log("x".repeat(3)); // "xxx"
```

**正则修改：**

正则表达式 u 标志：可以识别码点大于 oxFFFF 的 Unicode 字符；

正则表达式 y 标志：从正则表达式的 lastIndex 属性值的 位置开始检索字符串中的匹配字符。如果在该位置没有匹配成功，那么正则表达式将停止检索。y 修饰符的作用与 g 修饰符类似，也是全局匹配，后一次匹配都从上一次匹配成功的下一个位置开始。不同之处在于，g 修饰符只要剩余位置中存在匹配就可，而 y 修饰符确保匹配必须从剩余的第一个位置开始，这也就是“粘连”的含义。

复制正则表达式：将正则表达式传递给 RegExp 构造器来复制它，但有下面的区别；

```javascript
var re1 = /ab/i, // ES5 中会抛出错误, ES6 中可用 
    re2 = new RegExp(re1, "g");
```

新增flags 属性：

```javascript
var re = /ab/g;
console.log(re.source); // "ab" 
console.log(re.flags); // "g"
```

# 第三章 函数

1.  参数默认值；
1.  arguments在es5中会反映出具名参数的变化，严格模式下不会，es6和严格模式一致；
1.  参数默认值表达式：参数默认值可以执行一个函数来产生参数的默认值，函数声明初次被解析时并不会进行调用，只有整个函数被调用的时候，且没有传值，才会调用默认值的表达式，所以这个默认值可以是可变的；
1.  默认参数中，后面的参数可以引用前面的参数；前面的参数不可引用后面的，这是因为后面的参数存在暂时性死区，会报错；

```javascript
// 默认值
// 默认值
function makeRequest(url, timeout = 2000, callback = function () { }) { // 函数的剩余部分 
}
// arguments
function mixArgs(first, second) {
    console.log(first === arguments[0]);
    console.log(second === arguments[1]);
    first = "c"; second = "d";
    console.log(first === arguments[0]);

    console.log(second === arguments[1]);
}
mixArgs("a", "b");
// es5输出
// true true true true
// 严格模式 true true false false

// 参数默认值表达式
let value = 5;
function getValue() { return value++; }
function add(first, second = getValue()) {
    return first + second;
}
console.log(add(1, 1)); // 2 
console.log(add(1)); // 6 
console.log(add(1)); // 7

// 默认参数中
function getValue(value) {
    return value + 5;
}
function add(first, second = getValue(first)) {
    return first + second;
}
console.log(add(1, 1)); // 2 
console.log(add(1)); // 7
```

5.  剩余参数：...表示；剩余参数受到两点限制。一是函数只能有一个剩余参数，并且它必须被放在最后；第二个限制是剩余参数不能在对象字面量的 setter 属性中使用，这是因为对象字面量的 setter 被限定只能使用单个参数；
5.  函数构造器的增强能力：允许在Function 构造器中使用默认参数以及剩余参数；
5.  扩展运算符：JS 引擎将会将该数组分割为独立参数并把它们传递进去;
5.  名称属性:ES6 给所有函数 添加了 name 属性。
5.  名称属性的特殊情况：该函数表达式自己拥有一个名称，并且此名称的优先级要高于赋值目标的变量名；getter 函数的名称是 get xxx；setter函数的名称是set xxx；bind创建的是bound xxx；Function构造器创建的函数，为anonymous xxx；
5.  明确函数的双重用途：当使用 new 时，  [[Construct]] 方法则会被执行，函数内部的 this 是一个新对象，并作为函数的返回值；未使用 new 来调用， [[call]] 方法会被执行，运行的是代码中显示的函数体，this会绑定到全局对象中；

```javascript
function pick(object, ...keys) {
    let result = Object.create(null);
    for (let i = 0, len = keys.length; i < len; i++) {
        result[keys[i]] = object[keys[i]];
    }
    return result;
}
// 语法错误：不能在剩余参数后使用具名参数 
function pick(object, ...keys, last) { }
let object = {
    // 语法错误：不能在 setter 中使用剩余参数 
    set name(...value) { // 一些操作 
    }
};

var pickFirst = new Function("...args", "return args[0]");
console.log(pickFirst(1, 2)); // 1

// 扩展运算符
let values = [-25, -50, -75, -100]
console.log(Math.max(...values, 0)); // 0

var doSomething = function doSomethingElse() { // ... 
};
var person = {
    get firstName() {
        return "Nicholas"
    },
    sayName: function () {
        console.log(this.name);
    }
}
console.log(doSomething.name); // "doSomethingElse" 
console.log(person.sayName.name); // "sayName" 
var descriptor = Object.getOwnPropertyDescriptor(person, "firstName");
console.log(descriptor.get.name); // "get firstName"
//名称属性的特殊情况
var doSomething = function () {
};
console.log(doSomething.bind().name); // "bound doSomething" 
console.log((new Function()).name); // "anonymous"
```

11. 在 ES5 中判断函数如何被调用：this instanceof Fun；
11. new.target 元属性：当函数的 [[Construct]] 方法被调用 时， new.target 会被填入 new 运算符的作用目标，该目标通常是新创建的对象实例的构造器，并且会成为函数体内部的 this 值。而若 [[Call]] 被执行， new.target 的值则会是 undefined 。
11. 块级函数：块级函数会被提升到定义所在的代码块的顶部；而使用 let 的函数表达式则不会；
11. ES6 在非严格模式下同样允许使用块级函数，但行为有细微不同。块级函数的作用域会被提升到所在函数或全局环境的顶部，而不是代码块的顶部。

```javascript
function Person(name) {
    if (this instanceof Person) {
        this.name = name; // 使用 new 
    } else {
        throw new Error("You must use new with Person.")
    }
}
var person = new Person("Nicholas");
var notAPerson = Person("Nicholas"); // 抛出错误
// 上面方式不绝对可靠
var person = new Person("Nicholas");
var notAPerson = Person.call(person, "Michael"); // 奏效了！

function Person(name) {
    if (typeof new.target !== "undefined") {
        this.name = name;
    } else {
        throw new Error("You must use new with Person.")
    }
}
var person = new Person("Nicholas");
var notAPerson = Person.call(person, "Michael"); // 出错！

function Person(name) {
    if (new.target === Person) {
        this.name = name; // 使用 new 
    } else {
        throw new Error("You must use new with Person.")
    }
}
function AnotherPerson(name) {
    Person.call(this, name);
}
var person = new Person("Nicholas");
var anotherPerson = new AnotherPerson("Nicholas"); // 出错！

// ES5 的严格模式为代码块内部的函数声明引入了一个错误
"use strict";
if (true) {
    // 在 ES5 会抛出语法错误， ES6 则不会 
    function doSomething() { // ... 
    }
}
// ES6 会将 doSomething() 函数视为块级声明， 并允许它在定义所在的代码块内部被访问
"use strict";
if (true) {
    console.log(typeof doSomething); // "function" 

    function doSomething() { // ... 
    }

    doSomething();
}
console.log(typeof doSomething); // "undefined"
// let的时候报错
"use strict";
if (true) {
    console.log(typeof doSomething); // 抛出错误 

    let doSomething = function () { // ... 
    }
    doSomething();
}
console.log(typeof doSomething);

// ES6 behavior 
if (true) {
    console.log(typeof doSomething); // "function" 
    function doSomething() { // ... 
    }
    doSomething();
}
console.log(typeof doSomething); // "function"
```

15. 箭头函数：
    1. 没有 this 、 super 、 arguments ，也没有 new.target 绑定： this 、 super 、 arguments 、以及函数内部的 new.target 的值由所在的、最靠近的非箭头函数来决定 。 
    2. 不能被使用 new 调用： 箭头函数没有 [[Construct]] 方法，因此不能被用为构造函 数，使用 new 调用箭头函数会抛出错误。
    3. 没有原型： 既然不能对箭头函数使用 new ，那么它也不需要原型，也就是没有 prototype 属性。
    4. 不能更改 this ： this 的值在函数内部不能被修改，在函数的整个生命周期内其值会保持不变。
    5. 没有 arguments 对象： 既然箭头函数没有 arguments 绑定，你必须依赖于具名参数或 剩余参数来访问函数的参数。
    6. 不允许重复的具名参数： 箭头函数不允许拥有重复的具名参数，无论是否在严格模式 下；而相对来说，传统函数只有在严格模式下才禁止这种重复。
    7. 虽然仍然可以对箭头函数使用 call() 、 apply() 与 bind() 方法，但是不能改变其 this 值；
    8. 尾调用优化：允许某些函数的调用被优化，以保持更小的调用栈、使用更少的内存，并防止堆栈溢出；这要满足三个条件：
        1. 尾调用不能引用当前栈帧中的变量（意味着该函数不能是闭包）；
        2. 进行尾调用的函数在尾调用返回结果后不能做额外操作；
        3. 尾调用的结果作为当前函数的返回值；




# 第四章 扩展的对象功能

1. 对象类别：
    1.  普通对象：拥有 JS 对象所有默认的内部行为。
    1.  奇异对象：其内部行为在某些方面有别于默认行为。
    1.  标准对象：在 ES6 中被定义的对象，例如 Array 、 Date ，等等。标准对象可以是普通的，也可以是奇异的。
    1.  内置对象：在脚本开始运行时由 JS 运行环境提供的对象。所有的标准对象都是内置对 象。
2.  属性简写；
2.  方法简写：省略冒号和function；
2.  计算属性名：属性名可以是个变量或计算出来的；
2.  新的方法：
    1.  Object.is()：弥补严格相等运算符残留的怪异点；行为基本与全等一致，只是+0和-0不相等，NaN等于NaN；
    1.  Object.assign()：供应者的访问器属性转变成接收者的数据属性，但并未在接收者上创建访问器属性；
6.  移除了重复属性的检查：严格模式与非严格模式都不再检查重复的属性。当存在重复属性时，排在后面的属性的值会成为该属性的实际值；
6.  自有属性的枚举顺序：ES6 则严格定义了对象自有属性在被枚举时返回的顺序；for-in 循环的枚举顺序仍未被明确规定，因为并非所有的 JS 引擎都采用相同的方式。 而 Object.keys() 和 JSON.stringify() 也使用了与 for-in 一样的枚举顺序。
    1.  所有的数字类型键，按升序排列。
    1.  所有的字符串类型键，按被添加到对象的顺序排列。
    1.  所有的符号类型键，也按添加顺序排列。
8.  Object.setPrototypeOf：修改任意指定对象的原型；
8.  使用 super 引用的简单原型访问：只能用对象中简写的方式xxx() {}去调用，如果使用xxx:function() 这种方式会报错；
8.  正式的“方法”定义：方法是一个拥有 [[HomeObject]] 内部属性的函数，此内部属性指向该方法所属的对象；

```javascript
let name = 1;
// 属性简写
let test = {
    name
};
// 方法简写
var test = {
    say() {
    }
}
// 计算属性名
var suffix = " name";
var person = {
    ["first" + suffix]: "Nicholas",
    ["last" + suffix]: "Zakas"
};
console.log(person["first name"]); // "Nicholas" 
console.log(person["last name"]); // "Zakas"

var receiver = {}, supplier = {
    get name() {
        return "file.js"
    }
};
Object.assign(receiver, supplier);
var descriptor = Object.getOwnPropertyDescriptor(receiver, "name");
console.log(descriptor.value); // "file.js" 
console.log(descriptor.get); // undefined
```

# 第五章 解构：更方便的数据访问

1.  对象解构：
    1.  当解构赋值表达式的右侧（ = 后面的表达式）的计算结果为 null 或 undefined 时， 会抛出错误。因为任何读取 null 或 undefined 的企图都会导致“运行时”错误（ runtime error ）。
    1.  可设置2默认参数；
    1.  赋值给不同的本地变量名；
    1.  嵌套的对象解构；

```javascript
// 初始化结构
let { type, name } = node;

// 赋值的时候使用解构 
let node = { type: 'Identifier', name: 'foo' }, type = 'Literal', name = 5;
({ type, name } = node);

// 赋值给不同的本地变量名
let node = { type: "Identifier", name: "foo" };
let { type: localType, name: localName } = node;
console.log(localType); // "Identifier" 
console.log(localName); // "foo"
```

2.  数组解构：
    1.  可轻易地互换两个变量的值，不需要第三个变量；
    1.  剩余项：剩余的项目赋值给一个指定的变量；
    1.  和对象机制差不多；

```javascript
let colors = [ "red", "green", "blue" ];
let [ firstColor, secondColor ] = colors;
console.log(firstColor); // "red" 
console.log(secondColor); // "green"
// 要第三个值
let [ , , thirdColor ] = colors;
// 在 ES6 中互换值 
let a = 1, b = 2;
[ a, b ] = [ b, a ];


let colors = [ "red", "green", "blue" ];
let [ firstColor, ...restColors ] = colors;
console.log(firstColor); // "red" 
console.log(restColors.length); // 2 
console.log(restColors[0]); // "green" 
console.log(restColors[1]); // "blue"
```

3.  混合解构：对象与数组解构能被用在一起，以创建更复杂的解构表达式。

```javascript
let node = {
    type: "Identifier",
    name: "foo",
    loc: {
        start: {
            line: 1,
            column: 1
        },
        end: {
            line: 1,
            column: 4
        }
    },
    range: [0, 3]
};
let {
    loc: { start },
    range: [startIndex]
} = node;
console.log(start.line); // 1 
console.log(start.column); // 1 
console.log(startIndex); // 0
```
4.  参数解构：函数的参数的解构；
4.  解构的参数是必需的：函数的参数解构时，被解构的参数如果没有赋值，会报错，可设置默认值避免；

# 第六章 符号与符号属性
1.  符号：基本类型中是独一无二的；
1.  共享符号值Symbol.for()：首先会搜索全局符号注册表，看是否存在一个键值为 "uid" 的符号值。 若是，该方法会返回这个已存在的符号值；否则，会创建一个新的符号值，并使用该键值将 其记录到全局符号注册表中，然后返回这个新的符号值;
1.  Symbol.keyFor()：在全局符号注册表中根据符号值检索出对应的键值；
1.  符号值的转换：
    1.  调用 String() 方法来获取；
    1.  uid + ""这种隐式会报错；
    1.  uid/1 这种隐式会报错；
    1.  逻辑运算符则不会报错，因为符号值在逻辑运算中会被认为等价于 true；
5.  Object.getOwnPropertySymbols()：Object.keys() 或 Object.getOwnPropertyNames() 不会返回符号值，所以这个是获取对象自有属性名中的符号值；

```javascript
let firstName = Symbol("first name");
let person = {};
person[firstName] = "Nicholas";
console.log("first name" in person); // false 
console.log(person[firstName]); // "Nicholas" 
console.log(firstName); // "Symbol(first name)"
let uid = Symbol.for("uid");
let object = { [uid]: "12345" };
console.log(object[uid]); // "12345" 
console.log(uid); // "Symbol(uid)" 
let uid2 = Symbol.for("uid");
console.log(uid === uid2); // true 
console.log(object[uid2]); // "12345" 
console.log(uid2); // "Symbol(uid)"
let uid2 = Symbol.for("uid");
console.log(Symbol.keyFor(uid2)); // "uid" 
let uid3 = Symbol("uid");
console.log(Symbol.keyFor(uid3)); // undefined

let uid = Symbol.for("uid");
let object = { [uid]: "12345" };
let symbols = Object.getOwnPropertySymbols(object);
console.log(symbols.length); // 1 
console.log(symbols[0]); // "Symbol(uid)" 
console.log(object[symbols[0]]); // "12345"
```

6.  使用知名符号暴露内部方法：对原先属于语言内部逻辑的部分进行了进一步的暴露，允 许使用符号类型的原型属性来定义某些对象的基础行为；
    1.  Symbol.hasInstance ：供 instanceof 运算符使用的一个方法，用于判断对象继承关系。可通过Object.defineProperty重写对象的Symbol.hasInstance属性改变instanceof的结果；
    1.  Symbol.isConcatSpreadable ：一个布尔类型值，在集合对象作为参数传递给 Array.prototype.concat() 方法时，指示是否要将该集合的元素扁平化。
    1.  Symbol.iterator ：返回迭代器的一个方法。
    1.  Symbol.match ：此函数接受一个字符串参数，并返回一个包含匹配结果的数组；若匹配失败，则返回 null 。供 String.prototype.match() 函数使用的一个方法，用于比较字符串。
    1.  Symbol.replace ：此函数接受一个字符串参数与一个替换用的字符串，并返回替换后的 结果字符串。供 String.prototype.replace() 函数使用的一个方法，用于替换子字 符串。
    1.  Symbol.search ：此函数接受一个字符串参数，并返回匹配结果的数值索引；若匹配失 败，则返回 -1。供 String.prototype.search() 函数使用的一个方法，用于定位子字符串；
    1.  Symbol.species ：用于产生派生对象的构造器。
    1.  Symbol.split ：此函数接受一个字符串参数，并返回一个用匹配值分割而成的字符串数 组。供 String.prototype.split() 函数使用的一个方法，用于分割字符串。
    1.  Symbol.toPrimitive ：重写默认的转换行为；返回对象所对应的基本类型值的一个方法。
    1.  Symbol.toStringTag ：供 String.prototype.toString() 函数使用的一个方法，用于创建 对象的描述信息。
    1.  Symbol.unscopables ：一个对象，该对象的属性指示了哪些属性名不允许被包含在 with 语句中。

```javascript
function MyObj() {
}

var obj = new MyObj();
Object.defineProperty(MyObj, Symbol.hasInstance, {
    value: function (v) {
        return false;
    }
});
console.log(obj instanceof MyObj); // false
let collection = {
    0: "Hello",
    1: "world",
    length: 2,
    [Symbol.isConcatSpreadable]: true
};
let messages = ["Hi"].concat(collection);
console.log(messages.length); // 3 
console.log(messages); // ["hi","Hello","world"]

// 有效等价于 /^.{10}$/ 
let hasLengthOf10 = {
    [Symbol.match]: function (value) {
        return value.length === 10 ? [value.substring(0, 10)] : null;
    },
    [Symbol.replace]: function (value, replacement) {
        return value.length === 10 ? replacement + value.substring(10) : value;
    },
    [Symbol.search]: function (value) { return value.length === 10 ? 0 : -1; },
    [Symbol.split]: function (value) { return value.length === 10 ? ["", ""] : [value]; }
};
let message1 = "Hello world", // 11 characters 
    message2 = "Hello John"; // 10 
characters let match1 = message1.match(hasLengthOf10),
    match2 = message2.match(hasLengthOf10); console.log(match1); // null 
console.log(match2); // ["Hello John"] 
let replace1 = message1.replace(hasLengthOf10, "Howdy!"),
    replace2 = message2.replace(hasLengthOf10, "Howdy!");
console.log(replace1); // "Hello world" 
console.log(replace2); // "Howdy!" 
let search1 = message1.search(hasLengthOf10),
    search2 = message2.search(hasLengthOf10);
console.log(search1); // -1 console.log(search2); // 0 
let split1 = message1.split(hasLengthOf10),
    split2 = message2.split(hasLengthOf10);
console.log(split1); // ["Hello world"] 
console.log(split2); // ["", ""]

function Temperature(degrees) { this.degrees = degrees; }
Temperature.prototype[Symbol.toPrimitive] = function (hint) {
    switch (hint) {
        case "string":
            return this.degrees + "\u00b0"; // 温度符号 
        case "number": return this.degrees;
        case "default": return this.degrees + " degrees";
    }
};
let freezing = new Temperature(32);
console.log(freezing + "!"); // "32 degrees!" 
console.log(freezing / 2); // 16 
console.log(String(freezing)); // "32°"

function Person(name) {
    this.name = name;
}

Person.prototype[Symbol.toStringTag] = 'Person';
Person.prototype.toString = function () {
    return this.name;
};
let me = new Person('Nicholas');
console.log(me.toString()); // "Nicholas" 
console.log(Object.prototype.toString.call(me)); // "[object Person]"
```

每个“域”都拥有各自的全局作用域以及各自的全局对象拷贝。无论哪个“域”创建的数组都是正规的数组，但当它跨域进行传递时，使用 instanceof Array 进行检测却会得到 false 的结果，因为该数组是由 另外一个“域”的数组构造器创建的，有别于当前“域”的数组构造器。




# 第七章 Set与Map

Set 是不包含重复值的列表；

Map 则是键与相对应的值的集合；Map 常被用作缓存，存储数据以便此后快速检索。

es5中对map或set的模拟是有缺陷的，对象模拟map的时候，会自动把key转成字符串，会导致不同类型转成key会重复；

## Set

1.  方法：
    1.  add 新增；
    1.  delete 删除单个
    1.  has 是否有
    1.  clear 清空
2.  Set 构造器实际上可以接收任意可迭代对象作为参数；
2.  Set 版本的 forEach() 方法的回调函数中，将 Set 中的每一项同时认定为键与值，所以第一个参数和第二个参数是一样的；
2.  将 Set 转换为数组：[...set]；
2.  当存储对象时，即使对象被清空为null，但是set内并没有删掉，那引用依然在，set中不会释放内存，会保存之前的值；

```javascript
let set = new Set([1, 2]);
set.forEach(function (value, key, ownerSet) {
    console.log(key + ' ' + value);
    console.log(ownerSet === set);
});
// 1 1 
// true 
// 2 2 
// true
```

## Weak Set

该类型只允许存储对象弱引用，而不能存储基本类型的值。对象的弱引用在它自己成为该对象的唯一引用时，不会阻止垃圾回收；

一个数组被传给了 WeakSet 构造器去new。要记住若数组中包含了非对象的值，就会抛出错误，因为 WeakSet 构造器不接受基本类型的值。

1.  对于 WeakSet 的实例，若调用 add() 方法时传入了非对象的参数，就会抛出错误（ has() 或 delete() 则会在传入了非对象的参数时返回 false ）；
1.  Weak Set 不可迭代，因此不能被用在 for-of 循环中；
1.  Weak Set 无法暴露出任何迭代器（例如 keys() 与 values() 方法），因此没有任何编程手段可用于判断 Weak Set 的内容；
1.  Weak Set 没有 forEach() 方法；
1.  Weak Set 没有 size 属性。

```javascript
let key1 = {}, key2 = {}, set = new WeakSet([key1, key2]);
console.log(set.has(key1)); // true 
console.log(set.has(key2)); // true

let set = new WeakSet(), key = {};
// 将对象加入 set 
set.add(key);
console.log(set.has(key)); // true 
// 移除对于键的最后一个强引用，同时从 Weak Set 中移除 
key = null;
```

## Map

键的比较使用的是 Object.is();

方法：

1.  has(key) ：判断指定的键是否存在于 Map 中；
1.  delete(key) ：移除 Map 中的键以及对应的值；
1.  clear() ：移除 Map 中所有的键与值。

```javascript
let map = new Map([["name", "Nicholas"], ["age", 25]]);
console.log(map.has("name")); // true 
console.log(map.get("name")); // "Nicholas" 
console.log(map.has("age")); // true 
console.log(map.get("age")); // 25 
console.log(map.size); // 2

// forEach
let map = new Map([["name", "Nicholas"], ["age", 25]]);
map.forEach(function(value, key, ownerMap) {
    console.log(key + " " + value);
    console.log(ownerMap === map);  // true
});
```

## Weak Map

1.  Weak 版本都是存储对象弱引用的方 式。在 Weak Map 中，所有的键都必须是对象（尝试使用非对象的键会抛出错误），而且这些对象都是弱引用，不会干扰垃圾回收。当 Weak Map 中的键在 Weak Map 之外不存在引用 时，该键值对会被移除。
1.  Weak Map 的最佳用武之地，就是在浏览器中创建一个关联到特定 DOM 元素的对象。
1.  该方法的困难之处在于：如何判断一个 DOM 元素已不复存在于网页中，以便该库能移除此元素的关联对象。
1.  Weak Map 的键才是弱引用，而值不是。在 Weak Map 的值中存储对象 会阻止垃圾回收，即使该对象的其他引用已全都被移除。
1.  在传递 给 WeakMap 构造器的参数中，若任意键值对使用了非对象的键，构造器就会抛出错误。
1.  方法：
    -   has(key) ：判断指定的键是否存在于 Map 中；
    -   delete(key) ：移除 Map 中的键以及对应的值；
    -   clear() ：移除 Map 中所有的键与值。

7.  使用 delete() 方法则会把键从 Weak Map 中强制移除，此后 has() 方法就会对该键返回 false ， get() 方法则会返回 undefined 。
7.  es5中用闭包的方式模拟私有变量，这会导致即使对象实例被销毁，但自由变量不能被回收，从而内存泄露，这时候，可以用Weak map解决；
7.  当决定是要使用 Weak Map 还是使用正规 Map 时，首要考虑因素在于你是否只想使用对象类型的键。如果你打算这么做，那么最好的选择就是 Weak Map 。因为它能确保额外数据在不再可用后被销毁，从而能优化内存使用并规避内存泄漏。
7.  不能使用 forEach() 方法、 size 属性或 clear() 方法来管理其中的项。

# 第八章 迭代器与生成器

迭代器是被设计专用于迭代的对象，带有特定接口。所有的迭代器对象都拥有 next() 方 法，会返回一个结果对象。该结果对象有两个属性：对应下一个值的 value ，以及一个布尔 类型的 done ，其值为 true 时表示没有更多值可供使用。

```javascript
// es5的迭代器
function createIterator (items) {
    var i = 0;
    return {
        next: function () {
            var done = (i >= items.length);
            var value = !done ? items[i++] : undefined;
            return { done: done, value: value };
        }
    };
}

var iterator = createIterator([1, 2, 3]);
console.log(iterator.next()); // "{ value: 1, done: false }" 
console.log(iterator.next()); // "{ value: 2, done: false }" 
console.log(iterator.next()); // "{ value: 3, done: false }" 
console.log(iterator.next()); // "{ value: undefined, done: true }" 
// 之后的所有调用 
console.log(iterator.next()); // "{ value: undefined, done: true }"
```

1.  生成器（ generator ）是能返回一个迭代器的函数。生成器函数由放在 function 关键字之 后的一个星号（ * ）来表示，并能使用新的 yield 关键字。
1.  yield 关键字只能用在生成器内部，用于其他任意位置都是语法错误，即使在生成器内 部的函数中也不行；
1.  生成器函数表达式：let createIterator = function *(items) {}，这种写法也可以；
1.  生成器对象方法：

```javascript
var o = {
    createIterator: function* (items) {
        for (let i = 0; i < items.length; i++) {
            yield items[i];
        }
    }
};
let iterator = o.createIterator([1, 2, 3]);

// es6的简写
var o = {
    * createIterator (items) {
        for (let i = 0; i < items.length; i++) {
            yield items[i];
        }
    }
};
let iterator = o.createIterator([1, 2, 3]);
```

5.  生成器创建的所有迭代器都是可迭代对象，因为生成器默认就会为 Symbol.iterator 属 性赋值。
5.  可用for-of直接循环迭代器获取到value；

```javascript
function * test () {
    let arr = [1,2,3,4,5];
    for(let i = 0; i < arr.length; i++) {
        yield arr[i];
    }
}
let getTest = test();
for(var item of getTest) {
    console.log(item);
}
// 1
// 2
// 3
// 4
// 5
```

7.  在不可迭代对象、 null 或 undefined 上使用 for-of 语句，会抛出错误。
7.  访问默认迭代器：

```javascript
let values = [1, 2, 3];
let iterator = values[Symbol.iterator]();
console.log(iterator.next()); // "{ value: 1, done: false }" 
console.log(iterator.next()); // "{ value: 2, done: false }" 
console.log(iterator.next()); // "{ value: 3, done: false }" 
console.log(iterator.next()); // "{ value: undefined, done: true }"
```

9.  Symbol.iterator 指定了默认迭代器，你就可以使用它来检测一个对象是否能进行迭代；
9.  开发者自定义对象默认情况下不是可迭代对象，但你可以创建一个包含生成器的 Symbol.iterator 属性，让它们成为可迭代对象。

```javascript
function isIterable (object) {
    return typeof object[Symbol.iterator] === 'function';
}

console.log(isIterable([1, 2, 3])); // true 
console.log(isIterable("Hello")); // true 
console.log(isIterable(new Map())); // true 
console.log(isIterable(new Set())); // true 
console.log(isIterable(new WeakMap())); // false 
console.log(isIterable(new WeakSet())); // false

// 创建可迭代对象
let collection = {
    items: [], * [Symbol.iterator] () {
        for (let item of this.items) {
            yield item;
        }
    }
};
collection.items.push(1);
collection.items.push(2);
collection.items.push(3);
for (let x of collection) {
    console.log(x);
}
```

11. 内置迭代器：
    1.  数组
    1.  Map
    1.  Set
12. 提取集合中的迭代器：
    1.  entries() ：返回一个包含键值对的迭代器；
    1.  values() ：返回一个包含集合中的值的迭代器；
    1.  keys() ：返回一个包含集合中的键的迭代器。
13. 字符串的迭代器，for of迭代字符串;
13. NodeList 的迭代器：for of迭代dom节点；
13. 迭代器的next可以传值给对应的yield，传递给 next() 的参数会成为 yield 语句的值，则首次调用给 next() 提供 的参数就只会替换生成器函数中的第一个 yield 语句；
13. 在迭代器中抛出错误：iterator.throw(new Error("Boom"));
13. 生成器的 Return 语句:单 纯使用 return 让生成器更早返回，功能和yeild一样；
13. 扩展运算符与 for-of 循环会忽略 return 语句所指定的任意值。一旦它们看到 done 的值为 true ，它们就会停止操作而不会读取对应的 value 值。

```
function *createIterator() { 
  let first = yield 1; 
  let second; 
  try {
  second = yield first + 2; // yield 4 + 2 ，然后抛出错误
  } catch (ex) { 
    second = 6; // 当出错时，给变量另外赋值 
  }
  yield second + 3; 
}

let iterator = createIterator(); 
console.log(iterator.next()); // "{ value: 1, done: false }" 
console.log(iterator.next(4)); // "{ value: 6, done: false }" 
console.log(iterator.throw(new Error("Boom"))); // "{ value: 9, done: false }" 
console.log(iterator.next()); // "{ value: undefined, done: true }"
```

19. 生成器委托：将多个迭代器放到生成器中调用；
19. 异步任务运行器；

```javascript
function* createNumberIterator () {
    yield 1;
    yield 2;
    return 3;
}

function* createRepeatingIterator (count) {
    for (let i = 0; i < count; i++) {
        yield 'repeat';
    }
}

function* createCombinedIterator () {
    let result = yield* createNumberIterator();
    yield* createRepeatingIterator(result);
}

var iterator = createCombinedIterator();
console.log(iterator.next()); // "{ value: 1, done: false }" 
console.log(iterator.next()); // "{ value: 2, done: false }" 
console.log(iterator.next()); // "{ value: "repeat", done: false }" 
console.log(iterator.next()); // "{ value: "repeat", done: false }" 
console.log(iterator.next()); // "{ value: "repeat", done: false }" 
console.log(iterator.next()); // "{ value: undefined, done: true }"

// 异步任务运行器
let fs = require('fs');

function readFile (filename) {
    return function (callback) {
        fs.readFile(filename, callback);
    };
}

run(function* () {
    let contents = yield readFile('config.json');
    doSomethingWith(contents);
    console.log('Done');
});
```

# 第九章 JS的类

1.  为何要使用类的语法：
    1.  类声明不会被提升，这与函数定义不同。类声明的行为与 let 相似，因此在程序的执行到达声明处之前，类会存在于暂时性死区内。
    1.  类声明中的所有代码会自动运行在严格模式下，并且也无法退出严格模式。
    1.  类的所有方法都是不可枚举的，这是对于自定义类型的显著变化，后者必须用 Object.defineProperty() 才能将方法改变为不可枚举。
    1.  类的所有方法内部都没有 [[Construct]] ，因此使用 new 来调用它们会抛出错误。
    1.  调用类构造器时不使用 new ，会抛出错误。
    1.  试图在类的方法内部重写类名，会抛出错误、可以在外部重写类名。
2.  自有属性（ Own properties ）：该属性出现在实例上而不是原型上，只能在类的构造 器或方法内部进行创建。
2.  基本的类表达式：let PersonClass = class {}，类声明与类表达式都不会被提升；
2.  具名类表达式：let PersonClass = class PersonClass2 {}；PersonClass2 标识符只在类定义内部存在，因 此只能用在类方法内部（例如本例的 sayName() 内）。在类的外部， typeof PersonClass2 的结果为 "undefined" ，这是因为外部不存在 PersonClass2 绑定。
2.  一级公民（ first-class citizen ）：能被当作值来使用的，意味着它能作为参数传给函数、能作为函数返回值、能用来给变量赋值。

```javascript
function createObject (classDef) {
    return new classDef();
}

let obj = createObject(class {
    sayHi () {
        console.log('Hi!');
    }
});
obj.sayHi(); // "Hi!"

let person = new class {
    constructor (name) {
        this.name = name;
    }

    sayName () {
        console.log(this.name);
    }
}('Nicholas');
person.sayName(); // "Nicholas"
```

6.  访问器属性：自有属性需要在类构造器中创建，而类还允许你在原型上定义访问器属性；

```javascript
class CustomHTMLElement {
    constructor (element) {
        this.element = element;
    }

    get html () {
        return this.element.innerHTML;
    }

    set html (value) {
        this.element.innerHTML = value;
    }
}

var descriptor = Object.getOwnPropertyDescriptor(CustomHTMLElement.prototype, 'html');
console.log('get' in descriptor); // true 
console.log("set" in descriptor); // true 
console.log(descriptor.enumerable); // false
```

7.  需计算的成员名：允许成员用变量来声明；

```javascript
let methodName = 'sayName';

class PersonClass {
    constructor (name) {
        this.name = name;
    }

    [methodName] () {
        console.log(this.name);
    }
}

let me = new PersonClass('Nicholas');
me.sayName(); // "Nicholas
let propertyName = 'html';

class CustomHTMLElement {
    constructor (element) {
        this.element = element;
    }

    get [propertyName] () {
        return this.element.innerHTML;
    }

    set [propertyName] (value) {
        this.element.innerHTML = value;
    }
}
```

8.  生成器方法：当你使用一个对象来表示值的集合、并要求能简单 迭代这些值，那么生成器方法就非常有用。可以使用 Symbol.iterator 来定义生成器方法，从而定义出类的默认迭代器；

```javascript
class Collection {
    constructor () {
        this.items = [];
    }

    * [Symbol.iterator] () {
        yield* this.items.values();
    }
}

var collection = new Collection();
collection.items.push(1);
collection.items.push(2);
collection.items.push(3);
for (let x of collection) {
    console.log(x);
}
```

9.  静态成员：静态成员不能用实例来访问，你始终需要直接用类自身来访问它们;只要在方法与访问器属性 的名称前添加正式的 static 标注，就是静态属性；
9.  使用派生类进行继承：extends，。如果派生类指定了构造器，就需要 使用 super() ，否则会造成错误。若你选择不使用构造器， super() 方法会被自动调用， 并会使用创建新实例时提供的所有参数。
9.  super注意的点：
    1.  只能在派生类中使用 super() 。若尝试在非派生的类（即：没有使用 extends 关键字的类）或函数中使用它，就会抛出错误。
    1.  在构造器中，你必须在访问 this 之前调用 super() 。由于 super() 负责初始化 this ，因此试图先访问 this 自然就会造成错误。
    1.  唯一能避免调用 super() 的办法，是从类构造器中返回一个对象。
12. 屏蔽类方法：子可以重写父的方法，并且可以在子重写的方法内部使用super.xx调用父类的方法；
12. 继承静态成员：子可以直接掉父的静态成员；
12. extends后面可以是任何表达式，但并非所有表达式的结果都是一个有效的 类。特别的，下列表达式类型会导致错误：
    1.  null
    1.  生成器函数
15. 继承内置对象：es5不支持继承内置对象，即使模拟出来了，但是其行为还是和内置的对象不一致，es6中class支持，直接extends即可；
15. 任意能返回内置对象实例的方法，在派生类上却会自动返 回派生类的实例。若你拥有一个继承了 Array 的派生类 MyArray ，诸如 slice() 之 类的方法都会返回 MyArray 的实例。
15. Symbol.species 属性：用于定义一个能返回函数的静态访问器属性。每当类实例的方法 （构造器除外）必须创建一个实例时，前面返回的函数就被用为新实例的构造器。
15. 每个类型都拥有默认的 Symbol.species 属性，其返回值为 this ，意味着该属性总是会返回自身的构造器函数,只有 getter 而没有 setter ，这是因为修改类的 species 是不允许的

```javascript
class MyClass {
    static get [Symbol.species]() {
        return this;
    }

    constructor(value) {
        this.value = value;
    }

    clone() {
        return new this.constructor[Symbol.species](this.value);
    }
}

// this.constructor[Symbol.species] 的调用都会返回 MyClass
class MyClass {
    static get [Symbol.species]() {
        return this;
    }

    constructor(value) {
        this.value = value;
    }

    clone() {
        return new this.constructor[Symbol.species](this.value);
    }
}

class MyDerivedClass1 extends MyClass { // 空代码块 
}

class MyDerivedClass2 extends MyClass {
    static get [Symbol.species]() {
        return MyClass;
    }
}

let instance1 = new MyDerivedClass1("foo"),
    clone1 = instance1.clone(),
    instance2 = new MyDerivedClass2("bar"),
    clone2 = instance2.clone();
console.log(
    clone1
    instanceof
    MyClass
); 
// true 
console.log(clone1 instanceof MyDerivedClass1); // true 
console.log(clone2 instanceof MyClass); // true 
console.log(clone2 instanceof MyDerivedClass2); // false
class MyArray
    extends Array {
    static get [Symbol.species]() {
        return Array;
    }
}

let items = new MyArray(1, 2, 3, 4), subitems = items.slice(1, 3);
console.log(items instanceof MyArray); // true 
console.log(subitems instanceof Array); // true 
console.log(subitems instanceof MyArray); // false
```

19. 在类构造器中使用 new.target：new的谁，new.target就是谁；
19. 由于调用类时不能缺少 new ，于是 new.target 属性在类构造器内部就绝不会是undefined 。

# 第十章 增强的数组功能

1.  Array.of() ：创建一个具有可变数量参数的新数组实例，而不考虑参数的数量或类型；
1.  Array.form():在可迭代对象上使用;

```javascript
if (!Array.of) {
  Array.of = function() {
    return Array.prototype.slice.call(arguments);
  };
}
// Array.from(arrayLike[, mapFn[, thisArg]]) mapFn 新数组中的每个元素会执行该回调函数执行回调函数 thisArg mapFn 时 this 对象。
let helper = { diff: 1, add(value) { return value + this.diff; } };function translate() { return Array.from(arguments, helper.add, helper); }let numbers = translate(1, 2, 3); 
console.log(numbers); // 2,3,4
```

3.  所有数组上的新方法
    1.  find：返回匹配的值
    1.  findIndex：返回匹配位置的索引
    1.  fill：使用特定值填充数组中的一个或多个元素，三个参数，填充的值，start位置和end位置；如果提供的起始位置或结束位置为负数，则它们会被加上数组的长度来算出最终的位 置。例如：将起始位置指定为 -1 ，就等于是 array.length - 1
    1.  copyWithin：允许你在数组内部复制自身元素.为此你需要 传递两个参数给,从什么位置开始进行填充，以及被用来复制的数据的起 始位置索引类似于 fill() 方法，如果你向 copyWithin() 方法传递负数参数，数组的长度会自动 被加到该参数的值上，以便算出正确的索引位置。

```javascript
let numbers = [25, 30, 35, 40, 45];
console.log(numbers.find(n => n > 33)); // 35
console.log(numbers.findIndex(n => n > 33)); // 2
let numbers = [1, 2, 3, 4];
numbers.fill(1, 2);
console.log(numbers.toString()); // 1,2,1,1
numbers.fill(0, 1, 3);
console.log(numbers.toString()); // 1,0,0,1
let numbers = [1, 2, 3, 4]; // 从索引 2 的位置开始粘贴
// 从数组索引 0 的位置开始复制数据
// 在遇到索引 1 时停止复制 
numbers.copyWithin(2, 0, 1);
console.log(numbers.toString()); // 1,2,1,4
```

4.  类型化数组：引入类型化数组突破了格式限制并带来了更好的数学运算性能；类型化数组并不是严格的数组，它们并没有继承 Array 对象，但它们的外观和行为都与数组 有许多相似点。类型化数组包含的数据类型是八种数值数据类型之一，基于数组缓冲区对象 建立，用于表示按位存储的一个数值或一系列数值。类型化数组能够明显提升按位运算的性 能，因为它不像 JS 的常规数值类型那样需要频繁进行格式转换。
4.  数值数据类型：JS 数值使用 IEEE 754 标准格式存储，使用 64 位来存储一个数值的浮点数表示形式，该格式 在 JS 中被同时用来表示整数与浮点数；当值改变时，可能会频繁发生整数与浮点数之间的格 式转换。而类型化数组则允许存储并操作八种不同的数值类型：
    1.  8 位有符号整数（int8）
    1.  8 位无符号整数（uint8）
    1.  16 位有符号整数（int16）
    1.  16 位无符号整数（uint16）
    1.  32 位有符号整数（int32）
    1.  32 位无符号整数（uint32）
    1.  32 位浮点数（float32）
    1.  64 位浮点数（float64）

6.  数组缓冲区：内存中包含一定数量字节的区域，而所有的类型化数组都基于数组缓冲区。
6.  数组缓冲区总是保持创建时指定的字节数，你可以修改其内部的数据，但永远不能修改它的容量。
6.  使用视图操作数组缓冲区：视图（**views**）则是你操作这块区域的接口；

    1.  getInt8(byteOffset, littleEndian) ：从 byteOffset 处开始读取一个 int8 值；
    1.  setInt8(byteOffset, value, littleEndian) ：从 byteOffset 处开始写入一个 int8 值；
    1.  ...后面和前面一样

9.  视图允许你使用任意格式对任意位置进行读写，而无须考虑这些数据此前是使用什么格式存 储的，这非常有意思。例如，向缓冲区写入两个 int8 值，并将其作为一个 int16 值读取出来， 这是完全可行的
9.  每一种类型化数组都由一定数量的元素构成，而“元素大小”则代表每个类型的单个元素所 包含的字节数。这个数字被存储在类型化数组每个构造器与每个实例的 BYTES_PER_ELEMENT 属性中，方便你查询元素的大小：
9.  构造器方式创建：
    1.  类型化数组：数组所有元素都会被复制到新的类型化数组中。例如，如果你传递一个 int8 类型的数组给 Int16Array 构造器，这些 int8 的值会被复制到 int16 数组中。新的类型化 数组与原先的类型化数组会使用不同的数组缓冲区。
    1.  可迭代对象：该对象的迭代器会被调用以便将数据插入到类型化数组中。如果其中包含 了不匹配视图类型的值，那么构造器就会抛出错误。
    1.  数组：该数组的元素会被插入到新的类型化数组中。如果其中包含了不匹配视图类型的 值，那么构造器就会抛出错误。
    1.  类数组对象：与传入数组的表现一致。

```javascript
let buffer = new ArrayBuffer(10); // 分配了 10 个字节
console.log(buffer.byteLength); // 10
let buffer = new ArrayBuffer(10); // 分配了 10 个字节 
let buffer2 = buffer.slice(4, 6);
console.log(buffer2.byteLength); // 2
let buffer = new ArrayBuffer(10), view1 = new DataView(buffer),
// 包含所有字节 
    view2 = new DataView(buffer, 5, 2);
// 包含位置 5 与位置 6 的字节 
console.log(view1.buffer === buffer); // true 
console.log(view2.buffer === buffer); // true 
console.log(view1.byteOffset); // 0 
console.log(view2.byteOffset); // 5
console.log(view1.byteLength); // 10 
console.log(view2.byteLength); // 2
let buffer = new ArrayBuffer(2), 
    view = new DataView(buffer);
view.setInt8(0, 5);
view.setInt8(1, -1);
console.log(view.getInt8(0)); // 5 
console.log(view.getInt8(1)); // -1
let buffer = new ArrayBuffer(2), 
    view = new DataView(buffer);
view.setInt8(0, 5);
view.setInt8(1, -1);
console.log(view.getInt16(0)); // 1535 
console.log(view.getInt8(0)); // 5 
console.log(view.getInt8(1)); // -1
let ints1 = new Int16Array([25, 50]), 
    ints2 = new Int32Array(ints1);
console.log(ints1.buffer === ints2.buffer); // false 
console.log(ints1.byteLength); // 4 
console.log(ints1.length); // 2 
console.log(ints1[0]); // 25 
console.log(ints1[1]); // 50
console.log(ints2.byteLength); // 8 
console.log(ints2.length); // 2
console.log(ints2[0]); // 25 
console.log(ints2[1]); // 50
```

12. 类型化数组与常规数组的相似点和不同点：
    1.  可用length；
    1.  类型化数组并不是从 Array 对象派生 的，使用 Array.isArray() 去检测会返回 false
    1.  与常规数组不同的是，你不能使用 length 属性来改变类型化数组的大小。该属性是不可写的，在非严格模式下写入操作会被忽略，而严格模式下则会抛出错误。
    1.  常规数组可以被伸展或是收缩，然而类型化数组却会始终保持自身大小不变。你可以对常规数组一个不存在的索引位置进行赋值，但在类型化数组上这么做则会被忽略。
    1.  类型化数组也会对数据类型进行检查以保证只使用有效的值，当无效的值被传入时，将会被替换为 0

13. 类型化数组也拥有大量与常规数组等效的方法，虽然这些方法的表现与数组原型上的对应方法相似，但它们并不完全相同。类型化数 组的方法会进行额外的类型检查以确保安全，并且返回值会是某种类型化数组，而不是常规 数组（归结于 Symbol.species 属性）。
13. 相同的迭代器 ：与常规数组相同，类型化数组也拥有三个迭代器，它们是 entries() 方法、 keys() 方法与 values() 方法。
13. 类型化数组没有的方法：
    1.  concat()
    1.  pop()
    1.  push()
    1.  shift()
    1.  splice()
    1.  unshift()

16. 附加的方法 ：
    1.  set() 方法从另一个数组中复制元素到当前的类型化数组
    1.  subarray() 方法则是将当前类型化数组的一部分提取为新的类型化数组。

```javascript
let ints = new Int16Array([25, 50]);
console.log(ints.length); // 2
console.log(ints[0]); // 25
console.log(ints[1]); // 50
ints[2] = 5;
console.log(ints.length); // 2
console.log(ints[2]); // undefined
let ints = new Int16Array(["hi"]);
console.log(ints.length); // 1
console.log(ints[0]); // 0
let ints = new Int16Array(4);
ints.set([25, 50]);
ints.set([75, 100], 2);
console.log(ints.toString()); // 25,50,75,100
let ints = new Int16Array([25, 50, 75, 100]),
    subints1 = ints.subarray(),
    subints2 = ints.subarray(2),
    subints3 = ints.subarray(1, 3);
console.log(subints1.toString()); // 25,50,75,100
console.log(subints2.toString()); // 75,100
console.log(subints3.toString()); // 50,75
```

# 第十一章 Promise与异步编程

1.  若你传递一个 Promise 给 Promise.resolve() 或 Promise.reject() 方法，该 Promise 会不作修改原样返回。 译注： 经过测试，在几大浏览器中都存在与上一句话不符的情况。对挂起态或完成态的 Promise 使用 Promise.resolve() 没问题，会返回原 Promise ；对拒绝态的 Promise 使用 Promise.reject() 也没问题。而除此之外的情况全 都会在原 Promise 上包装出一个新的 Promise 。

# 第十二章 代理与反射接口

1.  代理与反射是什么：通过调用 new Proxy() ，你可以创建一个代理用来替代另一个对象(被称为目标)，这个代 理对目标对象进行了虚拟，因此该代理与该目标对象表面上可以被当作同一个对象来对待。
1.  代理允许你拦截在目标对象上的底层操作，而这原本是 JS 引擎的内部能力。拦截行为使用了 一个能够响应特定操作的函数(被称为陷阱)。
1.  创建一个简单的代理：

```javascript
let target = {};
let proxy = new Proxy(target, {});
proxy.name = "proxy";
console.log(proxy.name);
console.log(target.name);
target.name = "target";
console.log(proxy.name);
console.log(target.name);
// "proxy"
// "proxy"
// "target"
// "target"
```

4.  使用 **set** 陷阱函数验证属性值
    1.  trapTarget :将接收属性的对象(即代理的目标对象);
    1.  key :需要写入的属性的键(字符串类型或符号类型);
    1.  value :将被写入属性的值;
    1.  receiver :操作发生的对象(通常是代理对象).

```javascript
let target = {
    name: "target"
};
let proxy = new Proxy(target, {
    set(trapTarget, key, value, receiver) {
// 忽略已有属性，避免影响它们
if (!trapTarget.hasOwnProperty(key)) {
            if (isNaN(value)) {
                throw new TypeError("Property must be a number.");
} }
// 添加属性
        return Reflect.set(trapTarget, key, value, receiver);
    }
});
/ 添加一个新属性 

proxy.count = 1; 
console.log(proxy.count); // 1
console.log(target.count);// 1
 
// 你可以为 name 赋一个非数值类型的值，因为该属性已经存在 
proxy.name = "proxy";
console.log(proxy.name); // "proxy" 
console.log(target.name); // "proxy"
// 抛出错误
proxy.anotherName = "proxy";
```

5.  Reflect.set() 是 set 陷阱函数对应的反射方法，同时也是 set 操作的默认行为。该陷阱函数需要在属性被设置完成的情况下返回 true ，否则就要返回 false，而 Reflect.set() 也会基于操作是否成功而返回相应的结果。
5.  使用 **get** 陷阱函数进行对象外形验证:

```javascript
let proxy = new Proxy({}, {
        get(trapTarget, key, receiver) {
            if (!(key in receiver)) {
                throw new TypeError("Property " + key + " doesn't exist.");
}
            return Reflect.get(trapTarget, key, receiver);
        }
});
// 添加属性的功能正常 
proxy.name = "proxy"; 
console.log(proxy.name);// "proxy"
// 读取不存在属性会抛出错误 
console.log(proxy.nme);// 抛出错误
```

7.  使用 **has** 陷阱函数隐藏属性

```javascript
let target = {name: "target", value: 42};
let proxy = new Proxy(target, {
    has(trapTarget, key) {
        if (key === "value") {
            return false;
        } else {
            return Reflect.has(trapTarget, key);
        }
    }
});
console.log("value" in proxy); // false 
console.log("name" in proxy); // true 
console.log("toString" in proxy); // true
```

8.  使用 **deleteProperty** 陷阱函数避免属性被删除

```javascript
// 配置对象的configurable为false，可不能被删掉，且严格模式会报错；
let target = {name: "target", value: 42};
Object.defineProperty(target, "name", {configurable: false});
console.log("value" in target); // true 
let result1 = delete target.value;
console.log(result1); // true 
console.log("value" in target);
// false 
// 注：下一行代码在严格模式下会抛出错误 
let result2 = delete target.name;
console.log(result2); // false 
console.log("name" in target); // true
let target = {name: "target", value: 42};
let proxy = new Proxy(target, {
    deleteProperty(trapTarget, key) {
        if (key === "value") {
            return false;
        } else {
            return Reflect.deleteProperty(trapTarget, key);
        }
    }
});
// 尝试删除 proxy.value
console.log("value" in proxy); // true 
let result1 = delete proxy.value;
console.log(result1); // false 
console.log("value" in proxy); // true 
// 尝试删除 proxy.name 
console.log("name" in proxy); // true 
let result2 = delete proxy.name;
console.log(result2); // true 
console.log("name" in proxy); // false
```

9.  原型代理的陷阱函数；

```javascript
// 返回 null 隐藏了代理对象的原型
let target = {};
let proxy = new Proxy(target, {
    getPrototypeOf(trapTarget) {
        return null;
    }, setPrototypeOf(trapTarget, proto) {
        return false;
    }
});
let targetProto = Object.getPrototypeOf(target);
let proxyProto = Object.getPrototypeOf(proxy);
console.log(targetProto === Object.prototype); // true 
console.log(proxyProto === Object.prototype); // false 
console.log(proxyProto); // null 
// 成功 
Object.setPrototypeOf(target, {});
// 抛出错误 
Object.setPrototypeOf(proxy, {});
// 如果你想在这两个陷阱函数中使用默认的行为，那么只需调用 Reflect 对象上的相应方法。
let target = {};
let proxy = new Proxy(target, {
    getPrototypeOf(trapTarget) {
        return Reflect.getPrototypeOf(trapTarget);
    }, setPrototypeOf(trapTarget, proto) {
        return Reflect.setPrototypeOf(trapTarget, proto);
    }
});
let targetProto = Object.getPrototypeOf(target);
let proxyProto = Object.getPrototypeOf(proxy);
console.log(targetProto === Object.prototype); // true 
console.log(proxyProto === Object.prototype); // true 
// 成功 
Object.setPrototypeOf(target, {});
// 同样成功 
Object.setPrototypeOf(proxy, {});
```

10. Reflect.getPrototypeOf() 与 Reflect.setPrototypeOf() 和Object.getPrototypeOf() 与 Object.setPrototypeOf() 的差异
    1.  Object.getPrototypeOf() 与 Object.setPrototypeOf() 属于高级操作，从产生之初便 已提供给开发者使用；而 Reflect.getPrototypeOf() 与 Reflect.setPrototypeOf() 属于底层 操作，允许开发者访问 [[GetPrototypeOf]] 与 [[SetPrototypeOf]] 这两个原先仅供语言内 部使用的操作。
    1.  Reflect.getPrototypeOf() 方法在接收到的参数不是一个对象时会抛出错误，而 Object.getPrototypeOf() 则会在操作之前先将参数值转换为一个对象。
    1.  Reflect.setPrototypeOf() 方法返回一个布尔值用于表示操作是否已成功，成功时返回 true ，而失败时返回 false ；但若 Object.setPrototypeOf() 方法的操作失败，它会抛出错误。
    1.  Object.setPrototypeOf() 方法会将传入的第一个参数作为自身的返回值，因此并不适合用来 实现 setPrototypeOf 代理陷阱的默认行为。
    1.  在使用代理时，这两组方法都会调用 getPrototypeOf 与 setPrototypeOf 陷阱函数。

```javascript
let result1 = Object.getPrototypeOf(1);
console.log(result1 === Number.prototype); // true // 抛出错误 Reflect.getPrototypeOf(1);
let target1 = {};
let result1 = Object.setPrototypeOf(target1, {});
console.log(result1 === target1); // true 
let target2 = {};
let result2 = Reflect.setPrototypeOf(target2, {});
console.log(result2 === target2); // false 
console.log(result2); // true
```

11. 对象可扩展性的陷阱函数 ：ES5 通过 Object.preventExtensions() 与 Object.isExtensible() 方法给对象增加了可扩展 性。而 ES6 则通过 preventExtensions 与 isExtensible 陷阱函数允许代理拦截对于底层对 象的方法调用。
11. 底层功能的方法与对应的高层方法相比，会进行更严格的错误检查。
11. 属性描述符的陷阱函数：
    1.  defineProperty 在操作成功时返回 true ，否则返回 false 。
    1.  getOwnPropertyDescriptor 则只接受 trapTarget 与 key 这两个参数，并会返回对 应的描述符。
14. 可以让陷阱函数返回 true ，同时不去调用 Reflect.defineProperty() 方法，这样 Object.defineProperty() 就会静默失败，如此便可在未实际去定义属性的情况下抑制运 行错误。
14. Object.getOwnPropertyDescriptor() 方法会在接收的第一个参数是一个基本类型值时，将该 参数转换为一个对象。另一方面， Reflect.getOwnPropertyDescriptor() 方法则会在第一个参 数是基本类型值的时候抛出错误。

```javascript
let target = {};
let proxy = new Proxy(target, {
    isExtensible(trapTarget) {
        return Reflect.isExtensible(trapTarget);
    }, preventExtensions(trapTarget) {
        return Reflect.preventExtensions(trapTarget);
    }
});
console.log(Object.isExtensible(target)); // true 
console.log(Object.isExtensible(proxy)); // true 
Object.preventExtensions(proxy);
console.log(Object.isExtensible(target)); // false 
console.log(Object.isExtensible(proxy)); // false

// 此代码在 FireFox 和 Edge 中能够正常执行，但在 Chrome 中却会在 Object.preventExtensions(proxy) 这一行抛出错误。
let target = {};
let proxy = new Proxy(target, {
    isExtensible(trapTarget) {
        return Reflect.isExtensible(trapTarget);
    }, preventExtensions(trapTarget) {
        return false
    }
});
console.log(Object.isExtensible(target)); // true 
console.log(Object.isExtensible(proxy)); // true 
Object.preventExtensions(proxy);
console.log(Object.isExtensible(target)); // true 
console.log(Object.isExtensible(proxy)); // true
let proxy = new Proxy({}, {
    defineProperty(trapTarget, key, descriptor) {
        return Reflect.defineProperty(trapTarget, key, descriptor);
    }, getOwnPropertyDescriptor(trapTarget, key) {
        return Reflect.getOwnPropertyDescriptor(trapTarget, key);
    }
});
Object.defineProperty(proxy, "name", {value: "proxy"});
console.log(proxy.name); // "proxy" 
let descriptor = Object.getOwnPropertyDescriptor(proxy, "name");
console.log(descriptor.value); // "proxy"
let proxy = new Proxy({}, {
    defineProperty(trapTarget, key, descriptor) {
        if (typeof key === "symbol") {
            return false;
        }
        return Reflect.defineProperty(trapTarget, key, descriptor);
    }
});
Object.defineProperty(proxy, "name", {value: "proxy"});
console.log(proxy.name); // "proxy"
let nameSymbol = Symbol("name");
// 抛出错误 
Object.defineProperty(proxy, nameSymbol, {value: "proxy"});
// defineProperty
let proxy = new Proxy({}, {
    defineProperty(trapTarget, key, descriptor) {
        console.log(descriptor.value); // "proxy" 
        console.log(descriptor.name); // undefined 
        return Reflect.defineProperty(trapTarget, key, descriptor);
    }
});
Object.defineProperty(proxy, "name", {value: "proxy", name: "custom"});
// getOwnPropertyDescriptor 要求返回值必须是 null 、 undefined ，或者是一个对象。如果返回值是一个对象，则只允许该对象拥有 enumerable 、 configurable 、 value 、 writable 、 get 或 set 这些自有属性。如果你返回的对 象包含了不被许可的自有属性，则程序会抛出错误，
let proxy = new Proxy({}, {
    getOwnPropertyDescriptor(trapTarget, key) {
        return {name: "proxy"};
    }
});
// 抛出错误 
let descriptor = Object.getOwnPropertyDescriptor(proxy, "name");
                                                                         
```

16. ownKeys陷阱函数：过滤对象的key值；Reflect.ownKeys() 方法来获取目标对 象的键列表;ownKeys 陷阱函数也能影响 for-in 循环，因为这种循环调用了陷阱函数来决定哪些值 能够被用在循环内。

```javascript
let proxy = new Proxy({}, {
    ownKeys(trapTarget) {
        return Reflect.ownKeys(trapTarget).filter(key => {
            return typeof key !== "string" || key[0] !== "_";
}); }
});
let nameSymbol = Symbol("name");
proxy.name = "proxy";
proxy._name = "private";
proxy[nameSymbol] = "symbol";
let names = Object.getOwnPropertyNames(proxy),
    keys = Object.keys(proxy);
    symbols = Object.getOwnPropertySymbols(proxy);
console.log(names.length);
console.log(names[0]);
console.log(keys.length);
console.log(keys[0]);
console.log(symbols.length);
console.log(symbols[0]);
// 1
// "name"
// 1
// "name"
 // 1
 // "Symbol(name)"
```

17. 使用 **apply** 与 **construct** 陷阱函数的函数代理,在所有的代理陷阱中，只有 apply 与 construct 要求代理目标对象必须是一个函数。

```javascript
let target = function() { return 42 },
    proxy = new Proxy(target, {
        apply: function(trapTarget, thisArg, argumentList) {
            return Reflect.apply(trapTarget, thisArg, argumentList);
        },
        construct: function(trapTarget, argumentList) {
            return Reflect.construct(trapTarget, argumentList);
        }
});
// 使用了函数的代理，其目标对象会被视为函数 console.log(typeof proxy);
console.log(proxy());
var instance = new proxy();
console.log(instance instanceof proxy);
console.log(instance instanceof target);
// "function"
// 42
// true
// true

// 将所有参数相加
function sum(...values) {
    return values.reduce((previous, current) => previous + current, 0);
}
let sumProxy = new Proxy(sum, {
        apply: function(trapTarget, thisArg, argumentList) {
            argumentList.forEach((arg) => {
                if (typeof arg !== "number") {
                    throw new TypeError("All arguments must be numbers.");
                }
});
            return Reflect.apply(trapTarget, thisArg, argumentList);
        },
        construct: function(trapTarget, argumentList) {
            throw new TypeError("This function can't be called with new.");
} });
console.log(sumProxy(1, 2, 3, 4)); // 抛出错误
console.log(sumProxy(1, "2", 3, 4));
// 同样抛出错误
let result = new sumProxy();
// 10


function Numbers(...values) {
    this.values = values;
}
let NumbersProxy = new Proxy(Numbers, {
        apply: function(trapTarget, thisArg, argumentList) {
            throw new TypeError("This function must be called with new.");
},
        construct: function(trapTarget, argumentList) {
            argumentList.forEach((arg) => {
                if (typeof arg !== "number") {
                    throw new TypeError("All arguments must be numbers.");
} });
            return Reflect.construct(trapTarget, argumentList);
        }
    });
let instance = new NumbersProxy(1, 2, 3, 4);
console.log(instance.values);               // [1,2,3,4]
// 抛出错误 NumbersProxy(1, 2, 3, 4);

// 	new.target去限制某构造函数只能使用new的方式，不能直接调用，这里可以绕过这个判断
function Numbers(...values) {
    if (typeof new.target === "undefined") {
        throw new TypeError("This function must be called with new.");
}
    this.values = values;
}
let NumbersProxy = new Proxy(Numbers, {
        apply: function(trapTarget, thisArg, argumentsList) {
            return Reflect.construct(trapTarget, argumentsList);
        }
});
let instance = NumbersProxy(1, 2, 3, 4);
console.log(instance.values);               // [1,2,3,4]

// 拦截抽象基础类，实现抽象类不能被new的情况
class AbstractNumbers {
constructor(...values) {
    if (new.target === AbstractNumbers) {
        throw new TypeError("This function must be inherited from.");
    }
    this.values = values;
}
}
let AbstractNumbersProxy = new Proxy(AbstractNumbers, {
        construct: function(trapTarget, argumentList) {
            return Reflect.construct(trapTarget, argumentList, function() {});
        }
});
let instance = new AbstractNumbersProxy(1, 2, 3, 4);
console.log(instance.values);               // [1,2,3,4]

// 借助代理创建一个可被调用的类构造器
class Person {
    constructor(name) {
        this.name = name;
    }
}
let PersonProxy = new Proxy(Person, {
        apply: function(trapTarget, thisArg, argumentList) {
            return new trapTarget(...argumentList);
        }
});
let me = PersonProxy("Nicholas");
console.log(me.name);                   // "Nicholas"
console.log(me instanceof Person);      // true
console.log(me instanceof PersonProxy); // true
```

18. 可被撤销的代理,一旦 revoke() 函数被调用， proxy 就不再是一个函数;

```javascript
let target = {name: "target"};
let {proxy, revoke} = Proxy.revocable(target, {});
console.log(proxy.name); // "target" 
revoke(); // 抛出错误 
console.log(proxy.name);
```

19. 检测数组的索引：对于名为 P 的一个字符串属性名称来说，当且仅当 ToString(ToUint32(P)) 等于 P 、 并且 ToUint32(P) 不等于 2^32 - 1 时，它才能被用作数组的索引。
    1.  toUint32() 函数使用规范中描述的算法，将给定值转换为一个无符号的 32 位整数。
    1.  isArrayIndex() 函数首先将键值转换为一个 uint32 数，并执行了比较操作来判断该键是否能 够作为数组的索引。借助这两个工具函数，你就可以开始实现一个对象来模拟内置数组。

```javascript
function toUint32(value) {
    return Math.floor(Math.abs(Number(value))) % Math.pow(2, 32);
}

function isArrayIndex(key) {
    let numericKey = toUint32(key);
    return String(numericKey) == key && numericKey < (Math.pow(2, 32) - 1);
}

// 在添加新元素时增加长度属性
// 在减少长度属性时移除元素
function toUint32(value) {
    return Math.floor(Math.abs(Number(value))) % Math.pow(2, 32);
}

function isArrayIndex(key) {
    let numericKey = toUint32(key);
    return String(numericKey) == key && numericKey < (Math.pow(2, 32) - 1);
}

function createMyArray(length = 0) {
    return new Proxy({length}, {
        set(trapTarget, key, value) {
            let currentLength = Reflect.get(trapTarget, "length"); // 特殊情况 
            if (isArrayIndex(key)) {
                let numericKey = Number(key);
                if (numericKey >= currentLength) {
                    Reflect.set(trapTarget, "length", numericKey + 1);
                }
            } else if (key === "length") {
                if (value < currentLength) {
                    for (let index = currentLength - 1; index >= value; index--) {
                        Reflect.deleteProperty(trapTarget, index);
                    }
                }
            }
            // 无论键的类型是什么，都要执行这行代码 
            return Reflect.set(trapTarget, key, value);
        }
    });
}

let colors = createMyArray(3);
console.log(colors.length); // 3 
colors[0] = "red";
colors[1] = "green";
colors[2] = "blue";
colors[3] = "black";
console.log(colors.length); // 4 
colors.length = 2;
console.log(colors.length); // 2 
console.log(colors[3]); // undefined 
console.log(colors[2]); // undefined 
console.log(colors[1]); // "green" 
console.log(colors[0]); // "red"

//     实现 MyArray 类
function toUint32(value) {
    return Math.floor(Math.abs(Number(value))) % Math.pow(2, 32);
}

function isArrayIndex(key) {
    let numericKey = toUint32(key);
    return String(numericKey) == key && numericKey < (Math.pow(2, 32) - 1);
}

class MyArray {
    constructor(length = 0) {
        this.length = length;
        return new Proxy(this, {
            set(trapTarget, key, value) {
                let currentLength = Reflect.get(trapTarget, "length"); // 特殊情况 
                if (isArrayIndex(key)) {
                    let numericKey = Number(key);
                    if (numericKey >= currentLength) {
                        Reflect.set(trapTarget, "length", numericKey + 1);
                    }
                } else if (key === "length") {
                    if (value < currentLength) {
                        for (let index = currentLength - 1; index >= value; index--) {
                            Reflect.deleteProperty(trapTarget, index);
                        }
                    }
                }// 无论键的类型是什么，都要执行这行代码 
                return Reflect.set(trapTarget, key, value);
            }
        });
    }
}

let colors = new MyArray(3);
console.log(colors instanceof MyArray); // true 
console.log(colors.length); // 3 
colors[0] = "red";
colors[1] = "green";
colors[2] = "blue";
colors[3] = "black";
console.log(colors.length); // 4 
colors.length = 2;
console.log(colors.length); // 2 
console.log(colors[3]); // undefined 
console.log(colors[2]); // undefined 
console.log(colors[1]); // "green" 
console.log(colors[0]); // "red"
// 将代理对象作为原型使用在把代理对象作 为原型时，仅当操作的默认行为会按惯例追踪原型时，代理陷阱才会被调用，这就限制了代 理对象作为原型时的能力。
let target = {};
let newTarget = Object.create(new Proxy(target, { // 永远不会被调用 
    defineProperty(trapTarget, name, descriptor) {
        // 如果被调用就会引发错误 
        return false;
    }
}));
Object.defineProperty(newTarget, "name", {value: "newTarget"});
console.log(newTarget.name); // "newTarget" 
console.log(newTarget.hasOwnProperty("name")); // true
// 在原型上使用 get 陷阱函数

let target = {};
let thing = Object.create(new Proxy(target, {
    get(trapTarget, key, receiver) {
        throw new ReferenceError(`${key} doesn't exist`);
    }
}));
thing.name = "thing";
console.log(thing.name); // "thing" 
// 抛出错误 
let unknown = thing.unknown;
// 在原型上使用 set 陷阱函数
let target = {};
let thing = Object.create(new Proxy(target, {
    set(trapTarget, key, value, receiver) {
        return Reflect.set(trapTarget, key, value, receiver);
    }
}));
console
    .log(thing.hasOwnProperty("name")); // false 
// 触发了 `set` 代理陷阱 
thing.name = "thing";
console.log(thing.name); // "thing" 
console.log(thing.hasOwnProperty("name")); // true 
// 没有触发 `set` 代理陷阱 
thing.name = "boo";
console.log(thing.name); // "boo"
// 当你对一个对象属性进行赋值时，如果指定名称的自有属性存在，值就会被赋在该属性上； 而若该自有属性不存在，则会继续检查对象的原型。微妙之处在于：尽管赋值操作在原型上 继续进行，但默认情况下它会在对象实例（而非原型）上创建一个新的属性用于赋值，无论 同名属性是否存在于原型上。

// 在原型上使用 has 陷阱函数
let target = {};
let thing = Object.create(new Proxy(target, {
    has(trapTarget, key) {
        return Reflect.has(trapTarget, key);
    }
})); // 触发了 `has` 代理陷阱 
console.log("name" in thing); // false 
thing.name = "thing"; // 没有触发 `has` 代理陷阱 
console.log("name" in thing); // true

// 将代理作为类的原型 类不能直接被修改为将代理用作自身的原型，因为它们的 prototype 属性是不可写入的。 所以这里需要用es5转一下
function NoSuchProperty() {
    // empty
}
NoSuchProperty.prototype = new Proxy({}, {
    get(trapTarget, key, receiver) {
        throw new ReferenceError(`${key} doesn't exist`);
    }
});
class Square extends NoSuchProperty {
    constructor(length, width) {
        super();
        this.length = length;
        this.width = width;
}
    getArea() {
        return this.length * this.width;
} }
let shape = new Square(2, 6);
let area1 = shape.length * shape.width;
console.log(area1); // 12
// 当 shape.getArea() 被调用时，对于 getArea() 方法的查找从 shape 实例上开始，并 延续到它的原型上。由于在原型上找到了 getArea() 方法，查找就停止了，代理也没有被调 用。
let area2 = shape.getArea();
console.log(area2); //  12
// 由于 "wdth" 不存在而抛出了错误
let area3 = shape.length * shape.wdth;

let shapeProto = Object.getPrototypeOf(shape);
console.log(shapeProto === proxy); // false
let secondLevelProto = Object.getPrototypeOf(shapeProto);
console.log(secondLevelProto === proxy);            // true
```

在 ES6 之前，特定对象(例如数组)会显示出一些非常规的、无法被开发者复制的行为，而 代理的出现改变了这种情况。代理允许你为一些 JS 底层操作自行定义非常规行为，因此你就 可以通过代理陷阱来复制 JS 内置对象的所有行为。




# 第十三章 用模块封装代码

1.  模块（ **Modules** ）是使用不同方式加载的 JS 文件（与 JS 原先的脚本加载方式相对）。这 种不同模式很有必要，因为它与脚本（ **script** ）有大大不同的语义：箭头函数自动把this绑定到执行它爹；严格模式下，this不能指向widnow；严格模式是对块生效；
    1.  模块代码自动运行在严格模式下，并且没有任何办法跳出严格模式；
    1.  在模块的顶级作用域创建的变量，不会被自动添加到共享的全局作用域，它们只会在模块顶级作用域的内部存在；
    1.  模块顶级作用域的 this 值为 undefined ；
    1.  模块不允许在代码中使用 HTML 风格的注释（这是 JS 来自于早期浏览器的历史遗留特性）；
    1.  对于需要让模块外部代码访问的内容，模块必须导出它们；
    1.  允许模块从其他模块导入绑定.
2.  基本的导出：export；
2.  基本的导入：import；当从模块导入了一个绑定时，该绑定表现得就像使用了 const 的定义。这意味着你不能再定 义另一个同名变量（包括导入另一个同名绑定），也不能在对应的 import 语句之前使用此 标识符（也就是要受暂时性死区限制），更不能修改它的值。
2.  完全导入的一个模块：import * as xxx from xxx；
2.  无论你对同一个模块使用了多少次 import 语句，该模块都只会被执行一次。 在导出模块的代码执行之后，已被实例化的模块就被保留在内存中，并随时都能被其他 import 所引用。
2.  若同一个应用 中的其他模块打算从 example.js 导入绑定，则那些模块都会使用这段代码中所用的同一个模块实例。
2.  模块语法的限制（Module Syntax Limitations） ：export 与 import 都有一个重要的限制，那就是它们必须被用在其他语句或表达式的外部。不能以任何方式动态使用，也不能动态导入绑定。原因之一是模块语法需要让 JS 能静态判断需要导出什么
2.  导入绑定的一个微妙怪异点：尽管导入绑定的模块无法修改绑定的值，但负责导出的模块却能做到这一点。
2.  重命名导出与导入:as ;
2.  导出默认值：export default;
2.  导入默认值；
2.  绑定再导出：export { sum } from "./example.js";；
2.  无绑定的导入：导入了某个js，这个js操作了全局对象也是有效的，无绑定的导入最有可能被用于创建 polyfill 与 shim （为新语法在旧环境中运行提供向下兼容的两种方式）。

```javascript
if (flag) { 
  export flag; // 语法错误       
}
// example.js
export var name = "Nicholas"; export function setName(newName) { name = newName; }

import { name, setName } from "./example.js"; console.log(name); // "Nicholas" setName("Greg"); console.log(name); // "Greg" name = "Nicholas"; // error
function sum(num1, num2) { return num1 + num2; }export { sum as add };
import { add as sum } from "./example.js";
console.log(typeof add); // "undefined" console.log(sum(1, 2)); // 3

export let color = "red"; export default function(num1, num2) { return num1 + num2; }
import sum, { color } from "./example.js"; console.log(sum(1, 2)); // 3 console.log(color); // "red"
// 等价于上个例子 import { default as sum, color } from "example";
// 没有导出与导入的模块 Array.prototype.pushAll = function(items) { // items 必须是一个数组 if (!Array.isArray(items)) { throw new TypeError("Argument must be an array."); }// 使用内置的 push() 与扩展运算符 return this.push(...items); };
```

14. \<script type="module"> 总是表现得像是已经应用了 defer 属性。 defer 属性是加载脚本文件时的可选项，但在加载模块文件时总是自动应用的。当 HTML 解 析到拥有 src 属性的 \<script type="module"> 标签时，就会立即开始下载模块文件，但并 不会执行它，直到整个网页文档全部解析完为止。模块也会按照它们在 HTML 文件中出现的 顺序依次执行，这意味着第一个 \<script type="module"> 总是保证在第二个之前执行，即使 其中有些模块不是用 src 指定而是包含了内联脚本。
14. 每个模块可能都用 import 导入了一个或多个其他模块，这就让事情变复杂了。这也就是模 块为何首先需要被解析，因为这样才能识别所有的 import 语句。每个 import 语句又会触 发一次 fetch （无论是从网络还是从缓存中获取），并且在所有用 import 导入的资源被加载 与执行完毕之前，没有任何模块会被执行。
14. 所有模块，无论是用 \<script type="module"> 显式包含的，还是用 import 隠式包含的，都 会依照次序加载与执行。在前面的范例中，完整的加载次序是：
    1.  下载并解析module1.js
    1.  递归下载并解析在 module1.js 中使用 import 导入的资源；
    1.  解析内联模块；
    1.  递归下载并解析在内联模块中使用 import 导入的资源；
17. 一旦加载完毕，直到页面文档被完整解析之前，都不会有任何代码被执行。在文档解析完毕后，会发生下列行为：
    1.  递归执行 module1.js 导入的资源；
    1.  执行 module1.js ；
    1.  递归执行内联模块导入的资源；
    1.  执行内联模块；
18. 注意内联模块除了不必先下载代码之外，与其他两个模块的行为一致，加载 import 的资源 与执行模块的次序都是完全一样的。
18. Web 浏览器中的异步模块加载： async 会导致脚本文件在下载并解析完毕后就立即执行。但带有 async 的脚本在文档中的顺序却并不会影响脚本执行的次序，脚本总是会在下载完成后就立即执行，而无须等待包含它的文档解析完毕。
18. async 属性也能同样被应用到模块上。在 \<script type="module"> 上使用 async 会导致模块的执行行为与脚本相似。唯一区别是模块中所有 import 导入的资源会在模块自身被执行前先下载。这保证了模块中所有需要的资源会在模块执行前被下载，你只是不能保证模块何时会执行。
18. 将模块作为 **Worker** 加载：let worker = new Worker("module.js", { type: "module" });
18. worker 模块通常与 worker 脚本一致，但存在两点例外：
    1.  worker 脚本被限制只能从同 源的网页进行加载，而 worker 模块可以不受此限制。尽管 worker 模块具有相同的默认限 制，但当响应头中包含恰当的跨域资源共享（ Cross-Origin Resource Sharing ， CORS ）时，就允许跨域加载文件。
    1.  worker 脚本可以使用 self.importScripts() 方法来将额外脚本引入 worker ，而 worker 模块上的 self.importScripts() 却总会失败，因为应当换用import 。
23. 浏览器模块说明符方案：import { first } from "example.js"; 无效：没有以 / 、 ./ 或 ../ 开始
