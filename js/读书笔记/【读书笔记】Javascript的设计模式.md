# Part 1 面向对象的JavaScript

## [](#第1章-富有表现力的javascript)第1章 富有表现力的JavaScript

### [](#11-javascript的灵活性)1.1 JavaScript的灵活性

即可采用函数式编程风格，也可采用面向对象变成风格；

### [](#12-弱类型语言)1.2 弱类型语言

不必声明数据类型，根据所赋的值改变类型，且原始类型之间也可进行类型转换。

### [](#13-函数是一等对象)1.3 函数是一等对象

函数可以存储在变量中，可以作为参数传递，可以作为返回值从其他函数传出，还可在运行时进行构造，因此可创建闭包。

### [](#14-对象的易变性)1.4 对象的易变性

所有的对象和类都是易变的，可以对先前定义的类和实例化的对象进行修改；

**反射：** 在运行时检查对象所具有属性和方法，还可使用这种信息动态实例化类和执行其他方法；

PS：这里模仿传统的面向对象的特性都依赖于对象的易变性和反射，而在其他静态语言如C++，java，则不能对已经实例化的对象进行扩展，也不能对已经定义好的类进行修改；

### [](#15-继承)1.5 继承

js可供使用的继承有两种，一种是通过对象的原型继承的；一种是类式继承，各有优缺点。

### [](#16-javascript中的设计模式)1.6 JavaScript中的设计模式

js使用设计模式的优点：

1.  可维护性
1.  沟通
1.  性能（如享元模式、代理模式）

缺点：

1.  复杂性
1.  性能（多数模式对性能有所拖累）

## [](#第2章-接口)第2章 接口

### [](#21-什么是接口)2.1 什么是接口

接口提供了一种用以说明一个对象应该具有哪些方法的手段。

#### [](#211-接口之利)2.1.1 接口之利

1.  既定的一批接口具有自我描述性，并能促进代码的重用。
1.  有助于稳定不同类之间的通信方式。
1.  测试和调试变得更轻松。

#### [](#212-接口之弊)2.1.2 接口之弊

1.  降低灵活性。
1.  js并没有提供接口的内置支持，若试图模仿总会有一些风险。
1.  影响性能。
1.  无法强迫其他程序员遵守定义的接口。

### [](#22-其他面向对象语言处理接口的方式)2.2 其他面向对象语言处理接口的方式

接口结构包含的信息说明了需要实现什么方法以及这些方法具备的参数，类定义明确地说明他们实现了这个接口（implements），一个类可实现多个接口，若接口中的方法没有被实现，则会产生一个错误，有的语言在编译时报错，有的在运行时报错，错误信息包含类名，接口名和未被实现的方法名。

### [](#23-在javascript中模仿接口)2.3 在Javascript中模仿接口

#### [](#231-用注释描述接口)2.3.1 用注释描述接口

优点： 易于实现，提高代码可重用性，不影响文件尺寸或执行速度。

缺点：不会进行检查、抛错，对接口的约定完全靠自觉。对调试，测试没有帮助。

#### [](#232-用属性检查模仿接口)2.3.2 用属性检查模仿接口

for循环检查是否实现这个方法。

优点：会检查，抛错，对调试测试有帮助，规范其他程序员声明。 缺点：声明了，但是不一定实现了。

#### [](#233-用鸭式辨型模仿接口)2.3.3 用鸭式辨型模仿接口

如果对象具有与接口定义的方法同名的所有方法，那么就可以认为它实现了这个接口。

优点：三种方法中最有用的。 缺点：降低了代码的可重用性，缺乏自我描述性；需要使用辅助类Interface和辅助函数ensureImplements只关心方法名，不检查其参数的名称、数目、类型。

### [](#24-本书采用的接口实现方法)2.4 本书采用的接口实现方法

第一种和第三种的结合；

### [](#25-interface类)2.5 Interface类

```javascript
var Interface = function(name, methods) {
    if(arguments.length != 2) {
        throw new Error("Interface constructor called with " + arguments.length
          + "arguments, but expected exactly 2.");
    }
    
    this.name = name;
    this.methods = [];
    for(var i = 0, len = methods.length; i < len; i++) {
        if(typeof methods[i] !== 'string') {
            throw new Error("Interface constructor expects method names to be " 
              + "passed in as a string.");
        }
        this.methods.push(methods[i]);        
    }    
};    
// Static class method.
Interface.ensureImplements = function(object) {
    if(arguments.length < 2) {
        throw new Error("Function Interface.ensureImplements called with " + 
          arguments.length  + "arguments, but expected at least 2.");
    }
    for(var i = 1, len = arguments.length; i < len; i++) {
        var interface = arguments[i];
        if(interface.constructor !== Interface) {
            throw new Error("Function Interface.ensureImplements expects arguments "   
              + "two and above to be instances of Interface.");
        }
        
        for(var j = 0, methodsLen = interface.methods.length; j < methodsLen; j++) {
            var method = interface.methods[j];
            if(!object[method] || typeof object[method] !== 'function') {
                throw new Error("Function Interface.ensureImplements: object " 
                  + "does not implement the " + interface.name 
                  + " interface. Method " + method + " was not found.");
            }
        }
    } 
};
```

#### [](#251-interface类的使用场合)2.5.1 Interface类的使用场合

接口既能向函数传递任何类型的参数，还能保证只会使用那些具有必要方法的对象。在大型项目中，若还没编写出api或需要提供一些占位代码以免延误开发进度。

#### [](#252-interface类的用法)2.5.2 Interface类的用法

1.  将Interface类纳入HTML文件中。
1.  逐一检查代码中所有以对象为参数的方法。
1.  为你需要的每个不同方法集创建一个Interface对象。
1.  剔除所有针对构造器的显示检查。
1.  以Interf。ensureImplements取代原来的构造器检查。

#### [](#253-实例使用interface类)2.5.3 实例：使用Interface类

只要使用了实现所需方法集的类的实例即可，而不需要确定是哪个类。

### [](#26-依赖于接口的设计模式)2.6 依赖于接口的设计模式

1.  工厂模式
1.  组合模式
1.  装饰者模式
1.  命令模式

### [](#27-小结)2.7 小结

强制进行不必要的严格类型检查会损害js的灵活性，谨慎使用有助于创建更健壮的类和更稳定的代码；

## [](#第3章-封装和信息隐藏)第3章 封装和信息隐藏

私有变量可降低对象之间的耦合，保持数据的完整性并对其修改方式进行约束。

### [](#31-信息隐藏原则)3.1 信息隐藏原则

两个参与者必须通过明确的通道传送信息，这样即使有一方换了，由于没有依赖，另一方还可正常执行。

#### [](#311-封装与信息隐藏)3.1.1 封装与信息隐藏

封装：对对象的内部数据表现形式和实现细节进行隐藏，若想要访问封装后的对象中的数据，只有使用已定义的操作这一种方法。信息隐藏是目的，封装是技术。JavaScript是使用闭包达到该效果。

#### [](#312-接口扮演的角色)3.1.2 接口扮演的角色

接口提供了一份记载着可供公众访问的方法的契约，定义了两个对象可以具有的关系，只要接口不变，这个关系的双方都是可替换的。

### [](#32-创建对象的基本模式)3.2 创建对象的基本模式

JavaScript中创建对象的基本模式有3种：门户大开型、命名规范区别、闭包创建私有变量。

下面分三小节分别使用三种方法完成下面的代码执行：

```javascript
// Book(isbn, title, author)
var theHobbit = new Book('0-395-07122-4', 'The Hobbit', 'J. R. R. Tolkein');
theHobbit.display(); // Outputs the data by creating and populating an HTML element.
```

#### [](#321-门户大开型对象)3.2.1 门户大开型对象

设置性提供对数据具有保护作用的取值器和赋值器。

优点：易学会、可读性好。

缺点：无法保护内部数据，取值器和赋值器引入额外的代码。

```javascript
var Book = function(isbn, title, author) { // implements Publication
  this.setIsbn(isbn);
  this.setTitle(title);
  this.setAuthor(author);
}
Book.prototype = {
  checkIsbn: function(isbn) {
    ...
  },
  getIsbn: function() {
    return this.isbn;
  },
  setIsbn: function(isbn) {
    if(!this.checkIsbn(isbn)) throw new Error('Book: Invalid ISBN.');
    this.isbn = isbn;
  },
  getTitle: function() {
    return this.title;
  },
  setTitle: function(title) {
    this.title = title || 'No title specified';
  },
  getAuthor: function() {
    return this.author;
  },
  setAuthor: function(author) {
    this.author = author || 'No author specified';
  },
  display: function() {
    ...
  }
};
```

#### [](#322-用命名规范区别私有成员)3.2.2 用命名规范区别私有成员

在门户大开的基础上，将变量添加_前缀，以表示变量私有，不能私自更改；

```javascript
var Book = function(isbn, title, author) { // implements Publication
  this.setIsbn(isbn);
  this.setTitle(title);
  this.setAuthor(author);
}
Book.prototype = {
  _checkIsbn: function(isbn) {
    ...
  },
  getIsbn: function() {
    return this._isbn;
  },
  setIsbn: function(isbn) {
    if(!this._checkIsbn(isbn)) throw new Error('Book: Invalid ISBN.');
    this._isbn = isbn;
  },
  getTitle: function() {
    return this._title;
  },
  setTitle: function(title) {
    this._title = title || 'No title specified';
  },
  getAuthor: function() {
    return this._author;
  },
  setAuthor: function(author) {
    this._author = author || 'No author specified';
  },
  
  display: function() {
    ...
  }
};
```

#### [](#333-用闭包实现私有成员)3.3.3 用闭包实现私有成员

优点：真正实现私有；

缺点：占用更多内存。

```javascript
var Book = function(newIsbn, newTitle, newAuthor) { // implements Publication
  // Private attributes.
  var isbn, title, author;
  // Private method.
  function checkIsbn(isbn) {
    ... 
  }  
  // Privileged methods.
  this.getIsbn = function() {
    return isbn;
  };
  this.setIsbn = function(newIsbn) {
    if(!checkIsbn(newIsbn)) throw new Error('Book: Invalid ISBN.');
    isbn = newIsbn;
  };
  this.getTitle = function() {
    return title;
  };
  this.setTitle = function(newTitle) {
    title = newTitle || 'No title specified';
  };
  this.getAuthor = function() {
    return author;
  };
  this.setAuthor = function(newAuthor) {
    author = newAuthor || 'No author specified';
  };
  // Constructor code.
  this.setIsbn(newIsbn);
  this.setTitle(newTitle);
  this.setAuthor(newAuthor);
};
// Public, non-privileged methods.
Book.prototype = {
  display: function() {
    ...
  }
};
```

### [](#33-更多高级对象创建模式)3.3 更多高级对象创建模式

#### [](#331-静态方法和属性)3.3.1 静态方法和属性

静态私有方法checkIsbn：每个Book实例逗生成这个方法的一个新副本占用内存，所以可保存在闭包中，每个实例用同一个。

静态共有方法：convertToTitleCase；

```javascript
var Book = (function() {
  
  // Private static attributes.
  var numOfBooks = 0;
  // Private static method.
  function checkIsbn(isbn) {
    ... 
  }    
  // Return the constructor.
  return function(newIsbn, newTitle, newAuthor) { // implements Publication
    // Private attributes.
    var isbn, title, author;
    // Privileged methods.
    this.getIsbn = function() {
      return isbn;
    };
    this.setIsbn = function(newIsbn) {
      if(!checkIsbn(newIsbn)) throw new Error('Book: Invalid ISBN.');
      isbn = newIsbn;
    };
    this.getTitle = function() {
      return title;
    };
    this.setTitle = function(newTitle) {
      title = newTitle || 'No title specified';
    };
    this.getAuthor = function() {
      return author;
    };
    this.setAuthor = function(newAuthor) {
      author = newAuthor || 'No author specified';
    };
    // Constructor code.
    numOfBooks++; // Keep track of how many Books have been instantiated
                  // with the private static attribute.
    if(numOfBooks > 50) throw new Error('Book: Only 50 instances of Book can be '
        + 'created.');
    this.setIsbn(newIsbn);
    this.setTitle(newTitle);
    this.setAuthor(newAuthor);
  }
})();
// Public static method.
Book.convertToTitleCase = function(inputString) {
  ...
};
// Public, non-privileged methods.
Book.prototype = {
  display: function() {
    ...
  }
};
```

#### [](#332-常量)3.3.2 常量

通过只创建取值器而没有赋值器的私有变量来模仿常量。

```javascript
var Class = (function() {
  
  // Constants (created as private static attributes).
  var UPPER_BOUND = 100;
  
  // Constructor
  var ctor = function(constructorArgument) {
  ...
  };
  
  // Privileged static method.
  ctor.getUPPER_BOUND = function() {
    return UPPER_BOUND;
  };
  ...
  // Return the constructor.
  return ctor;
})();
/* Grouping constants together. */
var Class = (function() {
  
  // Private static attributes.
  var constants = {
    UPPER_BOUND: 100,
    LOWER_BOUND: -100
  };
  
  var ctor = function(constructorArgument) {
  ...
  };
  
  // Privileged static method.
  ctor.getConstant = function(name) {
    return constants[name];
  };
  ...
  // Return the constructor.
  return 
})();
/* Usage. */
Class.getConstant('UPPER_BOUND');
```

### [](#34-封装之利)3.4 封装之利

1.  保护内部数据的完整性；
1.  利于重构；
1.  提高对象的可重用性；
1.  避免命名空间冲突；
1.  代码修改更轻松；
1.  减少其他函数所需的错误检查代码的数量，并确保数据不会处于无效状态；

### [](#35-封装之弊)3.5 封装之弊

1.  难以进行单元测试；
1.  调试更加困难；
1.  可能存在过度封装；
1.  可能损害类的灵活性；
1.  可能难以理解既有代码；

## [](#第4章-继承)第4章 继承

### [](#41-为什么需要继承)4.1 为什么需要继承

优点：

1.  减少重复性代码，弱化对象间的耦合；
1.  对设计进行修改更轻松； 缺点：
1.  父类和子类产生强耦合；

### [](#42-类式继承)4.2 类式继承

父类；

```javascript
/* Class Person. */
function Person(name) {
  this.name = name;
}
Person.prototype.getName = function() {
  return this.name;
}
var reader = new Person('John Smith');
reader.getName();
```

#### [](#421-原型链方式)4.2.1 原型链方式

让子类的prototype指向父类的实例即可；

```javascript
/* Class Author. */
function Author(name, books) {
  Person.call(this, name); // Call the superclass' constructor in the scope of this.
  this.books = books; // Add an attribute to Author.
}
Author.prototype = new Person(); // Set up the prototype chain.
Author.prototype.constructor = Author; // Set the constructor attribute to Author.
Author.prototype.getBooks = function() { // Add a method to Author.
  return this.books;
};
var author = [];
author[0] = new Author('Dustin Diaz', ['JavaScript Design Patterns']);
author[1] = new Author('Ross Harmes', ['JavaScript Design Patterns']);
author[1].getName();
author[1].getBooks();
```

#### [](#422-extend-函数)4.2.2 extend 函数

不需要手工设置prototype和constructor属性；通过superclass去获取父类的属性和方法；

```javascript
/* Extend function, improved. */
function extend(subClass, superClass) {
  var F = function() {};
  F.prototype = superClass.prototype;
  subClass.prototype = new F();
  subClass.prototype.constructor = subClass;
  subClass.superclass = superClass.prototype;
  if(superClass.prototype.constructor == Object.prototype.constructor) {
    superClass.prototype.constructor = superClass;
  }
}
/* Class Author. */
function Author(name, books) {
  Author.superclass.constructor.call(this, name);
  this.books = books;
}
extend(Author, Person);
Author.prototype.getBooks = function() {
  return this.books;
};
Author.prototype.getName = function() {
  var name = Author.superclass.getName.call(this);
  return name + ', Author of ' + this.getBooks().join(', ');
};
```

### [](#43-原型式继承)4.3 原型式继承

使用原型式继承时，并不需要用类来定义对象的结构，只需要创建一个对象即可。这得益于原型链查找的工作机制。

```javascript
/* Person Prototype Object. */
var Person = {
  name: 'default name',
  getName: function() {
    return this.name;
  }
};
var reader = clone(Person);
alert(reader.getName()); // This will output 'default name'.
reader.name = 'John Smith';
alert(reader.getName()); // This will now output 'John Smith'.
/* Author Prototype Object. */
var Author = clone(Person);
Author.books = []; // Default value.
Author.getBooks = function() {
  return this.books;
}
var author = [];
author[0] = clone(Author);
author[0].name = 'Dustin Diaz';
author[0].books = ['JavaScript Design Patterns'];
author[1] = clone(Author);
author[1].name = 'Ross Harmes';
author[1].books = ['JavaScript Design Patterns'];
author[1].getName();
author[1].getBooks();
```

#### [](#431-对继承而来的成员的读和写的不对等性)4.3.1 对继承而来的成员的读和写的不对等性

由于原型链查找的关系，修改成员不仅会影响当前对象，还会影响到所有继承了当前对象的对象。所以父类的任何复杂的子对象都应该用方法创建。如下：

```javascript
// Best approach. Uses a method to create a new object, with the same structure and
// defaults as the original.
var CompoundObject = {};
CompoundObject.string1 = 'default value',
CompoundObject.createChildObject = function() {
  return {
    bool: true,
    num: 10
  }
};
CompoundObject.childObject = CompoundObject.createChildObject();
var compoundObjectClone = clone(CompoundObject);
compoundObjectClone.childObject = CompoundObject.createChildObject();
compoundObjectClone.childObject.num = 5;
```

#### [](#432-clone函数)4.3.2 clone函数

该函数返回的是一个以给定对象为原型对象的空对象。

```javascript
/* Clone function. */
function clone(object) {
    function F() {}
    F.prototype = object;
    return new F;
}
```

### [](#44-类式继承与原型式继承的对比)4.4 类式继承与原型式继承的对比

1.  原型式继承更能节约内存，更为简练。
1.  类式继承和其他语言一样，更为熟悉。

### [](#45-继承与封装)4.5 继承与封装

门户大开类型最适合于派生子类，如果某个成员需要稍加隐藏，可以下划线符号规范。在派生具有真正的私用变量时，可在子类中，间接访问父类的私有属性，但子类自身的实例方法都不能直接访问这些私用属性；父类的私有成员只能通过这些既有的特权方法进行访问，不能在子类中添加能够直接访问他们的新的特权方法。

### [](#46-掺元类)4.6 掺元类

由于一个对象只能有一个原型对象，所以JavaScript中是不允许对继承，但是可以通过掺元类进行扩充，以实现多继承的效果。就是将掺元类的所有属性赋值給子元素。

**掺元类：** 包含通用方法的类。掺元类非常适合于组织那些彼此迥然不同的类所共享的方法。

```javascript
/* Mixin class. */
var Mixin = function() {};
Mixin.prototype = {
  serialize: function() {
    var output = [];
    for(key in this) {
      output.push(key + ': ' + this[key]);
    }
    return output.join(', ');
  }
};
```

实现多继承的效果，如果只有父类和子类，则将父类的属性全部給子类；如果只需要固定的属性，则只給固定的属性；

```javascript
function augment(receivingClass, givingClass) {
  if(arguments[2]) { // Only give certain methods.
    for(var i = 2, len = arguments.length; i < len; i++) {
      receivingClass.prototype[arguments[i]] = givingClass.prototype[arguments[i]];
    }
  } 
  else { // Give all methods.
    for(methodName in givingClass.prototype) { 
      if(!receivingClass.prototype[methodName]) {
        receivingClass.prototype[methodName] = givingClass.prototype[methodName];
      }
    }
  }
}
augment(Author, Minxin, 'serilalize');
```

### [](#47-继承的适用场所)4.7 继承的适用场所

优点：

1.  代码重用度高；
1.  修改、排查方便，省时省力；

在内存效率比较重要的场合原型式继承时最佳选择；如果对面向对象语言的继承机制熟悉的话，用类式继承，这两种都适用于类间差异较小的类层次体系，如果差异较大，可用掺元类更为合理。

## [](#第5章-单体模式)第5章 单体模式

在网页上使用全局变量有很大的风险，而用单体对象创建的命名空间则是清楚这些全局变量的最佳手段之一。单体模式提供了一种将代码组织为一个逻辑单元的手段，这个逻辑单元中的代码可以通过单一的变量进行访问。还可以在一种名为分支的技术中用来封装浏览器之间的差异（借助分支技术，你在使用各种常用的工具函数时就不必再操心浏览器嗅探的事）；借助于单体模式，你可以把代码组织的更为一致，更易于阅读和维护。

### [](#51-单体的基本结构)5.1 单体的基本结构

传统意义的单体：只能被实例化一次并且可以通过一个众所周知的方法点访问的类； 本书的单体概念：单体是一个用来划分命名空间并将一批相关方法和属性组织在一起的对象，如果它可以被实例化，只能实例化一次。对象字面量只是用以创建单体的方法之一。

```javascript
/* Basic Singleton. */
var Singleton = {
  attribute1: true,
  attribute2: 10,
  method1: function() {
  },
  method2: function(arg) {
  }
};
Singleton.attribute1 = false;
var total = Singleton.attribute2 + 5;
var result = Singleton.method1();
```

### [](#52-划分命名空间)5.2 划分命名空间

单体对象由两个部分组成：包含着犯法和属性成员的对象自身，以及用于访问它的变量。这个变量通常是全局性的，以便在网页上任何地方都能直接访问到它所指向的单体对象。有助于让用户了解代码的组织结构及其各部分的用途；

```javascript
/* Declared globally. */	
function findProduct(id) {
  ...
}
...
// Later in your page, another programmer adds...
var resetProduct = $('reset-product-button');
var findProduct = $('find-product-button'); // The findProduct function just got
                                            // overwritten.
/* Using a namespace. */
var MyNamespace = {
  findProduct: function(id) {
	  ...
  },
  // Other methods can go here as well.
}
...
// Later in your page, another programmer adds...
var resetProduct = $('reset-product-button');
var findProduct = $('find-product-button'); // Nothing was overwritten.
/* GiantCorp namespace. */
var GiantCorp = {};
GiantCorp.Common = {
  // A singleton with common methods used by all objects and modules.
};
GiantCorp.ErrorCodes = {
  // An object literal used to store data.
};
GiantCorp.PageHandler = {
  // A singleton with page specific methods and attributes.
};
```

### [](#53-用作特定网页专用代码的包装器的单体)5.3 用作特定网页专用代码的包装器的单体

用于封装一些数据（如常量），为各网页特有的行为定义一些方法以及定义初始化方法；以及涉及DOM中特有元素的大多数代码。

```javascript
/* Generic Page Object. */
Namespace.PageName = {
  // Page constants.
  CONSTANT_1: true,
  CONSTANT_2: 10,
  // Page methods.
  method1: function() {
  },
  method2: function() {
  },
  // Initialization method.
  init: function() {
  }
}
// Invoke the initialization method after the page loads.
addLoadEvent(Namespace.PageName.init);
var GiantCorp = window.GiantCorp || {};
/* RegPage singleton, page handler object. */
GiantCorp.RegPage = {
  // Constants.
  FORM_ID: 'reg-form',
  OUTPUT_ID: 'reg-results',
  // Form handling methods.
  handleSubmit: function(e) {
    e.preventDefault(); // Stop the normal form submission.
    var data = {};
    var inputs = GiantCorp.RegPage.formEl.getElementsByTagName('input');
    // Collect the values of the input fields in the form.
    for(var i = 0, len = inputs.length; i < len; i++) {
      data[inputs[i].name] = inputs[i].value;
    }
    // Send the form values back to the server.
    GiantCorp.RegPage.sendRegistration(data);
  },
  sendRegistration: function(data) {
    // Make an XHR request and call displayResult() when the response is
    // received.
    ...
  },
  displayResult: function(response) {
    // Output the response directly into the output element. We are
    // assuming the server will send back formatted HTML.
    GiantCorp.RegPage.outputEl.innerHTML = response;
  },
  // Initialization method.
  init: function() {
    // Get the form and output elements.
    GiantCorp.RegPage.formEl = $(GiantCorp.RegPage.FORM_ID);
    GiantCorp.RegPage.outputEl = $(GiantCorp.RegPage.OUTPUT_ID);
    // Hijack the form submission.
    addEvent(GiantCorp.RegPage.formEl, 'submit', GiantCorp.RegPage.handleSubmit);
  }
};
// Invoke the initialization method after the page loads.
addLoadEvent(GiantCorp.RegPage.init);
```

### [](#54-拥有私用成员单体)5.4 拥有私用成员单体

使用真正的司用方法的一个缺点在于比较耗费内存，因为每个实例都具有方法的一个新副本。而单体对象只会被实例化一次。

#### [](#541-使用下划线表示法)5.4.1 使用下划线表示法

```javascript
/* DataParser singleton, converts character delimited strings into arrays. */ 
GiantCorp.DataParser = {
  // Private methods.
  _stripWhitespace: function(str) {
    return str.replace(/\s+/, '');
  },
  _stringSplit: function(str, delimiter) {
    return str.split(delimiter);
  },
  
  // Public method.
  stringToArray: function(str, delimiter, stripWS) {
    if(stripWS) {
      str = this._stripWhitespace(str);
    }
    var outputArray = this._stringSplit(str, delimiter);
    return outputArray;
  }
};
```

#### [](#542-使用闭包)5.4.2 使用闭包

这种单体又称为模块模式，它可把一批相关方法和属性组织为模块并起到划分命名空间的作用。

```javascript
/* Singleton with Private Members, step 3. */
MyNamespace.Singleton = (function() {
  // Private members.
  var privateAttribute1 = false;
  var privateAttribute2 = [1, 2, 3];
  
  function privateMethod1() {
    ...
  }
  function privateMethod2(args) {
    ...
  }
  return { // Public members.
    publicAttribute1: true,
    publicAttribute2: 10,
    
    publicMethod1: function() {
      ...
    },
    publicMethod2: function(args) {
      ...
    }
  };
})();
```

#### [](#543-两种技术的比较)5.4.3 两种技术的比较

下划线的方式简单易用，但是并没有做到真正的私有，而后者可以享受到真正私有成员带来的所有好处，而且也不需要付出什么代价。

### [](#55-惰性实例化)5.5 惰性实例化

前面所讲的单体模式都是在脚本加载时被创建出来，对于资源密集型或配置开销较大的单体，可将其实例化推迟到需要使用的时候，这种技术称为惰性加载。而被用作命名空间，特定网页专用代码包装器或组织相关实用方法的工具单体最好还是立即实话。

实现方式是调用方法之前先调用特定方法（如果被实例化了则返回实例，如果没有则实例化并返回），

```javascript
MyNamespace.Singleton = (function() {
  
  var uniqueInstance; // Private attribute that holds the single instance.
  
  function constructor() { // All of the normal singleton code goes here.
    ...
  }
  
  return {
    getInstance: function() {
      if(!uniqueInstance) { // Instantiate only if the instance doesn't exist.
        uniqueInstance = constructor();
      }
      return uniqueInstance;
    }
  }
})();
```

惰性加载单体的缺点之一是增加复杂性，容易被别人当作普通单体，所以需要注释解释清楚

### [](#56-分支)5.6 分支

分支是一种用来把浏览器之间的差异封装到运行期间进行设置的动态方法中的技术。如我们需要返回一个xhr对象，使用分支技术的做法是在脚本加载时一次性确定针对特定浏览器的代码。

```javascript
/* Branching Singleton (skeleton). */
MyNamespace.Singleton = (function() {
  var objectA = {
    method1: function() {
      ...
    },
    method2: function() {
      ...
    }
  };
  var objectB = {
    method1: function() {
      ...
    },
    method2: function() {
      ...
    }
  };
  return (someCondition) ? objectA : objectB;
})();
```

考虑是否使用该技术，必须在缩短计算事件和占用更多内存这一利弊之间权衡。

### [](#57-示例用分支技术创建xhr对象)5.7 示例：用分支技术创建XHR对象

```javascript
var SimpleXhrFactory = (function() {
  
  // The three branches.
  var standard = {
    createXhrObject: function() {
      return new XMLHttpRequest();
    }
  };
  var activeXNew = {
    createXhrObject: function() {
      return new ActiveXObject('Msxml2.XMLHTTP');
    }
  };
  var activeXOld = {
    createXhrObject: function() {
      return new ActiveXObject('Microsoft.XMLHTTP');
    }
  };
  
  // To assign the branch, try each method; return whatever doesn't fail.
  var testObject;
  try {
    testObject = standard.createXhrObject();
    return standard; // Return this if no error was thrown.
  }
  catch(e) {
    try {
      testObject = activeXNew.createXhrObject();
      return activeXNew; // Return this if no error was thrown.
    }
    catch(e) {
      try {
        testObject = activeXOld.createXhrObject();
        return activeXOld; // Return this if no error was thrown.
      }
      catch(e) {
        throw new Error('No XHR object found in this environment.');
      }
    }
  }
})();
```

### [](#58-单体模式的使用场合)5.8 单体模式的使用场合

1.  需要为代码提供命名空间和增加其模块性；
1.  想控制实例数目，节省系统资源的时候。

### [](#59-单体模式之利)5.9 单体模式之利

1.  对代码的组织作用；
1.  易于调试和维护；
1.  有利于阅读和理解；
1.  减少全局变量的数目；
1.  提升性能，减少不需要的内存消耗和带宽消耗；

### [](#510-单体模式之弊)5.10 单体模式之弊

1.  导致模块间的强耦合,与单一职责原则冲突；
1.  不利于单元测试；

## [](#第6章-方法的链式调用)第6章 方法的链式调用

通过返回实例对象的引用来对该实例进行一个或多个操作的过程。

### [](#61-调用链的结构)6.1 调用链的结构

```javascript
(function() {
  function _$(els) {
    this.elements = [];
    for (var i = 0, len = els.length; i < len; ++i) {
      var element = els[i];
      if (typeof element == 'string') {
        element = document.getElementById(element);
      }
      this.elements.push(element);
    }
  }
  _$.prototype = {
    each: function(fn) {
      for ( var i = 0, len = this.elements.length; i < len; ++i ) {
        fn.call(this, this.elements[i]);
      }
      return this;
    },
    setStyle: function(prop, val) {
      this.each(function(el) {
        el.style[prop] = val;
      });
      return this;
    },
    show: function() {
      var that = this;
      this.each(function(el) {
        that.setStyle('display', 'block');
      });
      return this;
    },
    addEvent: function(type, fn) {
      var add = function(el) {
        if (window.addEventListener) {
          el.addEventListener(type, fn, false);
        } 
        else if (window.attachEvent) {
          el.attachEvent('on'+type, fn);
        }
      };
      this.each(function(el) {
        add(el);
      });
      return this;
    }
  };
  window.$ = function() {
    return new _$(arguments);
  };
})();
/* Usage. */
$(window).addEvent('load', function() {
  $('test-1', 'test-2').show().
    setStyle('color', 'red').
    addEvent('click', function(e) {
      $(this).setStyle('color', 'green');
    });
});
```

### [](#62-设计一个支持方法链式调用的javascript库)6.2 设计一个支持方法链式调用的JavaScript库

```javascript
// Include syntactic sugar to help the development of our interface.
Function.prototype.method = function(name, fn) {
  this.prototype[name] = fn;
  return this;
};
(function() {
  function _$(els) {
    // ...
  }
  /*
    Events
      * addEvent
      * getEvent
  */
  _$.method('addEvent', function(type, fn) {
    // ...
  }).method('getEvent', function(e) {
    // ...
  }).
  /*
    DOM
      * addClass
      * removeClass
      * replaceClass
      * hasClass
      * getStyle
      * setStyle
  */
  method('addClass', function(className) {
    // ...
  }).method('removeClass', function(className) {
    // ...
  }).method('replaceClass', function(oldClass, newClass) {
    // ...
  }).method('hasClass', function(className) {
    // ...
  }).method('getStyle', function(prop) {
    // ...
  }).method('setStyle', function(prop, val) {
    // ...
  }).
  /*
    AJAX
      * load. Fetches an HTML fragment from a URL and inserts it into an element.
  */
  method('load', function(uri, method) {
    // ...
  });
  window.$ = function() {
    return new _$(arguments);
  });
})();
Function.prototype.method = function(name, fn) {
  // ...
};
(function() {
  function _$(els) {
    // ...
  }
  _$.method('addEvent', function(type, fn) {
    // ...
  })
  // ...
    
  window.installHelper = function(scope, interface) {
    scope[interface] = function() {
      return new _$(arguments);
    }
  };
})();
/* Usage. */
installHelper(window, '$');
$('example').show();
/* Another usage example. */
// Define a namespace without overwriting it if it already exists.
window.com = window.com || {};
com.example = com.example || {}; 
com.example.util = com.example.util || {};
installHelper(com.example.util, 'get');
(function() {
  var get = com.example.util.get;
  get('example').addEvent('click', function(e) {
    get(this).addClass('hello');
  });
})();
```

### [](#63-使用回调从支持链式调用的方法获取数据)6.3 使用回调从支持链式调用的方法获取数据

```javascript
// Accessor without function callbacks: returning requested data in accessors.
window.API = window.API || function() {
  var name = 'Hello world';
  // Privileged mutator method.
  this.setName = function(newName) {
    name = newName;
    return this;
  };
  // Privileged accessor method.
  this.getName = function() {
    return name;
  }
}();
// Implementation code.
var o = new API;
console.log(o.getName()); // Displays 'Hello world'.
console.log(o.setName('Meow').getName()); // Displays 'Meow'.
// Accessor with function callbacks.
window.API2 = window.API2 || {};
API2.prototype = function() {
  var name = 'Hello world';
  // Privileged mutator method.
  this.setName = function(newName) {
    name = newName;
    return this;
  };
  // Privileged accessor method.
  this.getName = function(callback) {
    callback.call(this, name);
    return this;
  }
}();
// Implementation code.
var o2 = new API2;
o2.getName(console.log).setName('Meow').getName(console.log);
// Displays 'Hello world' and then 'Meow'.
```

### [](#64-小结)6.4 小结

这种编程风格有助于简化代码；

# [](#part-2-第二部分-设计模式)Part 2 第二部分 设计模式

## [](#第7章-工厂模式)第7章 工厂模式

该模式有助于消除两个类之间的依赖性。在创建对象时不会对客户端暴露创建逻辑，并且是通过使用一个共同的接口来指向新创建的对象。

### [](#71-简单工厂)7.1 简单工厂

以下是一个卖自行车的故事，创建一个自行车商店：

```javascript
/* BicycleShop class. */
var BicycleShop = function() {};
BicycleShop.prototype = {
  sellBicycle: function(model) {
    var bicycle;
    
    switch(model) {
      case 'The Speedster':
        bicycle = new Speedster();
        break;
      case 'The Lowrider':
        bicycle = new Lowrider();
        break;
      case 'The Comfort Cruiser':
      default:
        bicycle = new ComfortCruiser();
    }
    Interface.ensureImplements(bicycle, Bicycle);
    
    bicycle.assemble();
    bicycle.wash();
    
    return bicycle;
  }
};
```

以上的商店，如果一旦内部的车型号发生改变，则需要改变商店的代码，而且是在商店的功能没发生变化的情况下，所以可以把生产车这部分交给简单工厂对象去做：

```javascript
/* BicycleFactory namespace. */
var BicycleFactory = {
  createBicycle: function(model) {
    var bicycle;
    
    switch(model) {
      case 'The Speedster':
        bicycle = new Speedster();
        break;
      case 'The Lowrider':
        bicycle = new Lowrider();
        break;
      case 'The Comfort Cruiser':
      default:
        bicycle = new ComfortCruiser();
    }
    
    Interface.ensureImplements(bicycle, Bicycle);
    return bicycle;
  }
};
/* BicycleShop class, improved. */
var BicycleShop = function() {};
BicycleShop.prototype = {
  sellBicycle: function(model) {
    var bicycle = BicycleFactory.createBicycle(model);
    
    bicycle.assemble();
    bicycle.wash();
    
    return bicycle;
  }
};
```

### [](#72-工厂模式)7.2 工厂模式

工厂模式和简单工厂模式的区别在于，它不是另外使用一个类或对象来创建自行车，而是使用子类。一般性操作可以放在父类中，个体性的代码则封装在子类型中；

```javascript
/* BicycleShop class (abstract). */
var BicycleShop = function() {};
BicycleShop.prototype = {
  sellBicycle: function(model) {
    var bicycle = this.createBicycle(model);
    
    bicycle.assemble();
    bicycle.wash();
    
    return bicycle;
  },
  createBicycle: function(model) {
    throw new Error('Unsupported operation on an abstract class.');
  }
};
/* AcmeBicycleShop class. */
var AcmeBicycleShop = function() {};
extend(AcmeBicycleShop, BicycleShop);
AcmeBicycleShop.prototype.createBicycle = function(model) {
  var bicycle;
  switch(model) {
    case 'The Speedster':
      bicycle = new AcmeSpeedster();
      break;
    case 'The Lowrider':
      bicycle = new AcmeLowrider();
      break;
    case 'The Flatlander':
      bicycle = new AcmeFlatlander();
      break;
    case 'The Comfort Cruiser':
    default:
      bicycle = new AcmeComfortCruiser();
  }
  Interface.ensureImplements(bicycle, Bicycle);
  return bicycle;  
};
/* GeneralProductsBicycleShop class. */
var GeneralProductsBicycleShop = function() {};
extend(GeneralProductsBicycleShop, BicycleShop);
GeneralProductsBicycleShop.prototype.createBicycle = function(model) {
  var bicycle;
  switch(model) {
    case 'The Speedster':
      bicycle = new GeneralProductsSpeedster();
      break;
    case 'The Lowrider':
      bicycle = new GeneralProductsLowrider();
      break;
    case 'The Flatlander':
      bicycle = new GeneralProductsFlatlander();
      break;
    case 'The Comfort Cruiser':
    default:
      bicycle = new GeneralProductsComfortCruiser();
  }
  Interface.ensureImplements(bicycle, Bicycle);
  return bicycle;
};
/* Usage. */
var alecsCruisers = new AcmeBicycleShop();
var yourNewBike = alecsCruisers.sellBicycle('The Lowrider');
var bobsCruisers = new GeneralProductsBicycleShop();
var yourSecondNewBike = bobsCruisers.sellBicycle('The Lowrider');
```

### [](#73-工厂模式的使用场所)7.3 工厂模式的使用场所

#### [](#731-动态实现)7.3.1 动态实现

通常要与一系列实现了同一个接口、可以被同等对待的类打交道。所实例化的类的类型不能在开发期间确定，只能在运行期间确定的情况。

#### [](#732-节省设置开销)7.3.2 节省设置开销

如果对象需要进行复杂且彼此相关的设置，那么使用工厂模式可以减少每种对象所需要的代码量。

#### [](#733-用许多小型对象组成一个大对象)7.3.3 用许多小型对象组成一个大对象

工厂方法可以用来创建封装了许多较小对象的对象。

### [](#74-实例xhr工厂)7.4 实例：XHR工厂

使用简单工厂模式,createXhrObject来生产xhr对象：

```javascript
/* AjaxHandler interface. */
var AjaxHandler = new Interface('AjaxHandler', ['request', 'createXhrObject']);
/* SimpleHandler class. */
var SimpleHandler = function() {}; // implements AjaxHandler
SimpleHandler.prototype = {
  request: function(method, url, callback, postVars) {
    var xhr = this.createXhrObject();
    xhr.onreadystatechange = function() {
      if(xhr.readyState !== 4) return;
      (xhr.status === 200) ? 
        callback.success(xhr.responseText, xhr.responseXML) : 
        callback.failure(xhr.status);
    };
    xhr.open(method, url, true);
    if(method !== 'POST') postVars = null;
    xhr.send(postVars);
  },
  createXhrObject: function() { // Factory method.
    var methods = [
      function() { return new XMLHttpRequest(); },
      function() { return new ActiveXObject('Msxml2.XMLHTTP'); },
      function() { return new ActiveXObject('Microsoft.XMLHTTP'); }
    ];
    
    for(var i = 0, len = methods.length; i < len; i++) {
      try {
        methods[i]();
      }
      catch(e) {
        continue;
      }
      // If we reach this point, method[i] worked.
      this.createXhrObject = methods[i]; // Memoize the method.
      return methods[i];
    }
    
    // If we reach this point, none of the methods worked.
    throw new Error('SimpleHandler: Could not create an XHR object.');
  } 
};
/* Usage. */
var myHandler = new SimpleHandler();
var callback = { 
  success: function(responseText) { alert('Success: ' + responseText); }, 
  failure: function(statusCode) { alert('Failure: ' + statusCode); } 
};
myHandler.request('GET', 'script.php', callback);
```

#### [](#741-专用型连接对象)7.4.1 专用型连接对象

工厂模式，派生自SimpleHandler；

```javascript
/* QueuedHandler class.发起请求之前会确保所有请求都已经处理成功 */
var QueuedHandler = function() { // implements AjaxHandler
  this.queue = [];
  this.requestInProgress = false;
  this.retryDelay = 5; // In seconds.
};
extend(QueuedHandler, SimpleHandler);
QueuedHandler.prototype.request = function(method, url, callback, postVars, 
  override) {
  if(this.requestInProgress && !override) {
    this.queue.push({ 
      method: method, 
      url: url, 
      callback: callback, 
      postVars: postVars 
    });
  }
  else {
    this.requestInProgress = true;
    var xhr = this.createXhrObject();
    var that = this;
    xhr.onreadystatechange = function() {
      if(xhr.readyState !== 4) return;
      if(xhr.status === 200) {
        callback.success(xhr.responseText, xhr.responseXML);
        that.advanceQueue();
      }
      else {
        callback.failure(xhr.status);
        setTimeout(function() { that.request(method, url, callback, postVars); }, 
          that.retryDelay * 1000);
      }
    };
    xhr.open(method, url, true);
    if(method !== 'POST') postVars = null;
    xhr.send(postVars);    
  }
}; 
QueuedHandler.prototype.advanceQueue = function() {
  if(this.queue.length === 0) {
    this.requestInProgress = false;    
    return;
  }
  var req = this.queue.shift();
  this.request(req.method, req.url, req.callback, req.postVars, true);
};
/* OfflineHandler class. 离线状态会把请求缓存起来*/
var OfflineHandler = function() { // implements AjaxHandler
  this.storedRequests = [];
};
extend(OfflineHandler, SimpleHandler);
OfflineHandler.prototype.request = function(method, url, callback, postVars) {
  if(XhrManager.isOffline()) { // Store the requests until we are online.
    this.storedRequests.push({ 
      method: method, 
      url: url, 
      callback: callback, 
      postVars: postVars 
    });
  }
  else { // Call SimpleHandler's request method if we are online.
    this.flushStoredRequests();
    OfflineHandler.superclass.request(method, url, callback, postVars);
  }
};
OfflineHandler.prototype.flushStoredRequests = function() {
  for(var i = 0, len = storedRequests.length; i < len; i++) {
    var req = storedRequests[i];
    OfflineHandler.superclass.request(req.method, req.url, req.callback,
      req.postVars);
  }
};
```

#### [](#742-在运行时选择连接对象)7.4.2 在运行时选择连接对象

因为用户的网络情况不确定，所以并不知道用哪个处理器类，隐藏所有处理器都实现了AjaxHandler接口，所以他们可以被同等对待：

```javascript
/* XhrManager singleton. */
var XhrManager = {
  createXhrHandler: function() {
    var xhr;
    if(this.isOffline()) {
      xhr = new OfflineHandler();
    }
    else if(this.isHighLatency()) {
      xhr = new QueuedHandler();
    }
    else {
      xhr = new SimpleHandler()
    }
    
    Interface.ensureImplements(xhr, AjaxHandler);
    return xhr
  },
  isOffline: function() { // Do a quick request with SimpleHandler and see if
    ...                   // it succeeds.
  },
  isHighLatency: function() { // Do a series of requests with SimpleHandler and
  // 检查请求得到回应所经历的时间，根据长短返回true或false
    ...                       // time the responses. Best done once, as a 
                              // branching function.
  }
};
/* Usage. */
var myHandler = XhrManager.createXhrHandler();
var callback = { 
  success: function(responseText) { alert('Success: ' + responseText); }, 
  failure: function(statusCode) { alert('Failure: ' + statusCode); } 
};
myHandler.request('GET', 'script.php', callback);
```

### [](#75-工厂模式之利)7.5 工厂模式之利

弱化耦合，消除重复代码；

### [](#76-工厂模式之弊)7.6 工厂模式之弊

不能知道对象识别的问题(对象的类型不知道)；

## [](#第8章-桥接模式)第8章 桥接模式

桥接模式的作用在于“将抽象与其实现隔离开来，以便二者对变化”；这种模式对于JavaScript中常见的事件驱动的编程大有裨益。

### [](#81-示例事件监听器)8.1 示例：事件监听器

常用事件监听编码如下：

```JAVASCRIPT
addEvent(element, 'click', getBeerById);
function getBeerById(e) {
  var id = this.id;
  asyncRequest('GET', 'beer.uri?id=' + id, function(resp) {
    // Callback response.
    console.log('Requested Beer: ' + resp.responseText);
  });
}
```

该方法中id为事件中传递过来，所以getBeerById方法在测试时不便；改为如下：

```JAVASCRIPT
addEvent(element, 'click', getBeerByIdBridge);
function getBeerByIdBridge (e) {
  getBeerById(this.id, function(beer) {
    console.log('Requested Beer: '+beer);
  });
}
```

通过getBeerByIdBridge这层桥接，getBeerById便于测试；

### [](#82-桥接模式的其他例子)8.2 桥接模式的其他例子

访问私有变量空间；

```JAVASCRIPT
var Public = function() {
  var secret = 3;
  this.privilegedGetter = function() {
    return secret;
  };
};
var o = new Public;
var data = o.privilegedGetter();
```

### [](#83-用桥接模式联结多个类)8.3 用桥接模式联结多个类

这里使用桥接模式是为了让Class1和Class2能够独立于BridgeClass而发生改变。

```JAVASCRIPT
var Class1 = function(a, b, c) {
  this.a = a;
  this.b = b;
  this.c = c;
}
var Class2 = function(d) {
  this.d = d;
};
var BridgeClass = function(a, b, c, d) {
  this.one = new Class1(a, b, c);
  this.two = new Class2(d);
};
```

### [](#84-桥接模式的适用场合)8.4 桥接模式的适用场合

当一个方法里面的逻辑中使用的数据是通过一段处理时，可以将逻辑通过桥接封装出来。

```JAVASCRIPT
// Original function.
var addRequest = function(request) {
  var data = request.split('-')[1];
  // etc...
//----------------------------
// Function de-coupled.
var addRequest = function(data) {
  // etc...
};
// Bridge
var addRequestFromClick = function(request) {
  addRequest(request.split(‘-‘)[0]);
};
```

### [](#85-桥接模式之利)8.5 桥接模式之利

有助于独立地管理软件的各个组成部分，更容易查找bug。让api更加健壮，提高组件的模块化程度。

### [](#86-桥接模式之弊)8.6 桥接模式之弊

提高系统的复杂度。

## [](#第9章-组合模式)第9章 组合模式

组合对象带来的两大好处：

1.  可以用同样的方法处理对象的几何和其中的特定子对象。
1.  把一批子对象组织成属性结构，并且使整棵树都可被遍历。

### [](#91-组合对象的结构)9.1 组合对象的结构

叶对象和组合对象，一个组合对象由一些别的组合对象和叶对象组成，其中叶对象不再包含子对象，是最基本的元素，也是各种操作的落实地点。

### [](#92-使用组合模式)9.2 使用组合模式

组合模式擅长于对大批对象进行操作，它专为组织这类对象并把操作从一个层级向下一层次传递而设计；同时具备如下两个条件时才适合使用组合模式：

1.  存在一批组织成某种层次体系的对象（具体的结构在开发期间可能无法得知）；
1.  希望对这批对象或其中一部分对象实施一个操作；

### [](#93-示例-表单验证)9.3 示例 表单验证

当表单内部的子组件不确定的时候，就不能在编写的时候统一save和validate，这时候可以使用组合模式，只调用一次save和validate，便会递归的去调用每个叶组件的save和validate。

### [](#94-实例图片库)9.4 实例：图片库

在组织图片时，DynamicGallery用多少次都行，属于组合对象，叶子对象为GaleryImage，当需要对某个组合对象进行hide或show时，会遍历所有的子组件操作hide或show；

```JAVASCRIPT
// Interfaces.
var Composite = new Interface('Composite', ['add', 'remove', 'getChild']);
var GalleryItem = new Interface('GalleryItem', ['hide', 'show']);
// DynamicGallery class.
var DynamicGallery = function(id) { // implements Composite, GalleryItem
  this.children = [];
  this.element = document.createElement('div');
  this.element.id = id;
  this.element.className = 'dynamic-gallery';
}
DynamicGallery.prototype = {
  // Implement the Composite interface.
  add: function(child) {
    Interface.ensureImplements(child, Composite, GalleryItem);
    this.children.push(child);
    this.element.appendChild(child.getElement());
  },
  remove: function(child) {
    for(var node, i = 0; node = this.getChild(i); i++) {
      if(node == child) {
        this.formComponents[i].splice(i, 1);
        break;
      }
    }
    this.element.removeChild(child.getElement());
  },
  getChild: function(i) {
    return this.children[i];
  },
  // Implement the GalleryItem interface.
  
  hide: function() {
    for(var node, i = 0; node = this.getChild(i); i++) {
      node.hide();
    }
    this.element.style.display = 'none';
  },
  show: function() {
    this.element.style.display = 'block';
    for(var node, i = 0; node = this.getChild(i); i++) {
      node.show();
    }    
  },
  
  // Helper methods.
  
  getElement: function() {
    return this.element;
  }
};
// GalleryImage class.
var GalleryImage = function(src) { // implements Composite, GalleryItem
  this.element = document.createElement('img');
  this.element.className = 'gallery-image';
  this.element.src = src;
}
GalleryImage.prototype = {
  // Implement the Composite interface.
  add: function() {},       // This is a leaf node, so we don't
  remove: function() {},    // implement these methods, we just
  getChild: function() {},  // define them.
  // Implement the GalleryItem interface.
  
  hide: function() {
    this.element.style.display = 'none';
  },
  show: function() {
    this.element.style.display = ''; // Restore the display attribute to its 
                                     // previous setting.
  },
  
  // Helper methods.
  
  getElement: function() {
    return this.element;
  }
};
// Usage.
var topGallery = new DynamicGallery('top-gallery');
topGallery.add(new GalleryImage('/img/image-1.jpg'));
topGallery.add(new GalleryImage('/img/image-2.jpg'));
topGallery.add(new GalleryImage('/img/image-3.jpg'));
var vacationPhotos = new DynamicGallery('vacation-photos');
for(var i = 0; i < 30; i++) {
  vacationPhotos.add(new GalleryImage('/img/vac/image-' + i + '.jpg'));
}
topGallery.add(vacationPhotos);
topGallery.show();      // Show the main gallery,
vacationPhotos.hide();  // but hide the vacation gallery.
```

### [](#95-组合模式之利)9.5 组合模式之利

1.  省去大量手工遍历数组或其他数据结构的粘合代码，只需对最顶层的对象执行操作，让每个子对象自己传递这个操作即可；
1.  利于重构，对象之间耦合性松散；
1.  深度优先的搜索查找结点，所以在层次体系中操作十分方便；

### [](#96-组合模式之弊)9.6 组合模式之弊

1.  若层次体系很大，系统的性能将受到影响；
1.  限制较大，只有层级关系才可使用此模式，如表格很难转成该模式，因为表格标签中只能包含几种特定的标签，且叶节点并不是那么显而易见，内部也很可能有其他元素，这些限制降低了组合对象的有用性，有损代码的模块性；

## [](#第10章-门面模式)第10章 门面模式

作用：

1.  简化类的接口；
1.  消除类与使用它的客户代码之间的耦合；

### [](#101-一些你可能已经知道的门面元素)10.1 一些你可能已经知道的门面元素

把检查代码封装在一个地方。

```JAVASCRIPT
function addEvent(el, type, fn) {
  if (window.addEventListener) {
    el.addEventListener(type, fn, false);
  } 
  else if (window.attachEvent) {
    el.attachEvent('on' + type, fn);
  } 
  else {
    el['on' + type] = fn;
  }
}
```

### [](#102-javascript库的门面性质)10.2 JavaScript库的门面性质

JavaScript库目的在于节省事件、简化常见任务和提供比每个浏览器都实现了的内置JS函数更易于使用接口。

### [](#103-用作便利方法的门面元素)10.3 用作便利方法的门面元素

对函数的组合是门面模式的另外一个优点，这些组合而得的函数又叫遍历函数。这里门面模式只是用于提供一个简化的接口。

```JAVASCRIPT
function a(x) {
  // do stuff here...
}
function b(y) {
  // do stuff here...
}
function ab(x, y) {
  a(x);
  b(y);
}
var DED = window.DED || {};
DED.util = {
  stopPropagation: function(e) {
    if (ev.stopPropagation) {
      // W3 interface
      e.stopPropagation();
    } 
    else {
      // IE's interface
      e.cancelBubble = true;
    }
  },
  preventDefault: function(e) {
    if (e.preventDefault) {
      // W3 interface
      e.preventDefault();
    } 
    else {
      // IE's interface
      e.returnValue = false;
    }
  },
  /* our convenience method */
  stopEvent: function(e) {
    DED.util.stopPropagation(e);
    DED.util.preventDefault(e);
  }
};
```

### [](#104-实例设置html元素的样式)10.4 实例：设置HTML元素的样式

每次单独设置一个样式很麻烦，所以可以有以下这种封装；

```JAVASCRIPT
var element = document.getElementById('content');
element.style.color = 'red';
element.style.fontSize = '16px';
var element1 = document.getElementById('foo');
element1.style.color = 'red';
var element2 = document.getElementById('bar');
element2.style.color = 'red';
var element3 = document.getElementById('baz');
element3.style.color = 'red';
setStyle(['foo', 'bar', 'baz'], 'color', 'red');
function setStyle(elements, prop, val) {
  for (var i = 0, len = elements.length-1; I < len; ++i) {
    document.getElementById(elements[i]).style[prop] = val;
  }
}
setStyle(['foo'], 'position', 'absolute');
setStyle(['foo'], 'top', '50px');
setStyle(['foo'], 'left', '300px');
setCSS(['foo'], {
  position: 'absolute',
  top: '50px',
  left: '300px'
});
function setCSS(el, styles) {
  for ( var prop in styles ) {
    if (!styles.hasOwnProperty(prop)) continue;
    setStyle(el, prop, styles[prop]);
  }
}
setCSS(['foo', 'bar', 'baz'], {
  color: 'white',
  background: 'black',
  fontSize: '16px',
  fontFamily: 'georgia, times, serif'
});
```

### [](#105-示例设计一个事件工具)10.5 示例：设计一个事件工具

js兼容性判断封装；

```JAVASCRIPT
DED.util.Event = {
  getEvent: function(e) {
    return e || window.event;
  },
  getTarget: function(e) {
    return e.target || e.srcElement;
  },
  stopPropagation: function(e) {
    if (e.stopPropagation) {
      e.stopPropagation();
    } 
    else {
      e.cancelBubble = true;
    }
  },
  preventDefault: function(e) {
    if (e.preventDefault) {
      e.preventDefault();
    } 
    else {
      e.returnValue = false;
    }
  },
  stopEvent: function(e) {
    this.stopPropagation(e);
    this.preventDefault(e);
  }
};
addEvent($('example'), 'click', function(e) {
  // Who clicked me.
  console.log(DED.util.Event.getTarget(e));
  // Stop propgating and prevent the default action.
  DED.util.Event.stopEvent(e);
});
```

### [](#106-实现门面模式的一般步骤)10.6 实现门面模式的一般步骤

感觉适合门面模式后，就加入便利方法；取名合理，并给予描述；

### [](#107-门面模式的使用场合)10.7 门面模式的使用场合

是否有反复成组出现的代码；

### [](#108-门面模式之利)10.8 门面模式之利

1.  节省时间和精力；
1.  简化代码，易于维护和理解；
1.  降低依赖程度；
1.  增加灵活性；
1.  避免与下层子系统紧密耦合；

### [](#109-门面模式之弊)10.9 门面模式之弊

1.  不符合开闭原则，如果要改东西很麻烦，继承重写都不合适；

## [](#第11章-适配器模式)第11章 适配器模式

在现有接口和不兼容的类之间进行适配；这种模式又叫包装器（wrapper）；

### [](#111-适配器的特点)11.1 适配器的特点

协调两个不同的接口，把一个接口转换为另一个接口，并不会滤除某些能力，也不会简化接口。

```JAVASCRIPT
var clientObject = {
  string1: 'foo',
  string2: 'bar',
  string3: 'baz'
};
function interfaceMethod(str1, str2, str3) {
  ...
}
function clientToInterfaceAdapter(o) {
  interfaceMethod(o.string1, o.string2, o.string3);
}
/* Usage. */
clientToInterfaceAdapter(clientObject);
```

### [](#112-适配原有实现)11.2 适配原有实现

有时候是不能从客户一方对代码进行修改，所以需要适配器适配。

### [](#113-示例适配两个库)11.3 示例：适配两个库

当从一个库过度到另一个库时，在不改变原有的情况下，可进行适配，如下：

```JAVASCRIPT
// Prototype $ function.
function $() {
  var elements = new Array();
  for(var i = 0; i < arguments.length; i++) {
    var element = arguments[i];
    if(typeof element == 'string')
      element = document.getElementById(element);
    if(arguments.length == 1)
      return element;
    elements.push(element);
  }
  return elements;
}
/* YUI get method. */
YAHOO.util.Dom.get = function(el) {
  if(YAHOO.lang.isString(el)) { 
    return document.getElementById(el);
  }
  if(YAHOO.lang.isArray(el)) {
    var c = [];
    for(var i = 0, len = el.length; i < len; ++i) {
      c[c.length] = Y.Dom.get(el[i]);
    }
    return c;
  }
  if(el) {
    return el;
  }
  return null;
};
function PrototypeToYUIAdapter() {
  return YAHOO.util.Dom.get(arguments);
}
function YUIToPrototypeAdapter(el) {
  return $.apply(window, el);
}
$ = PrototypeToYUIAdapter;
// or vice-versa, for those who are migrating from YUI to Prototype:
YAHOO.util.Dom.get = YUIToPrototypeAdapter;
```

### [](#114-适配器模式的使用场合)11.4 适配器模式的使用场合

客户系统期待的接口和现有API提供的接口不兼容。

### [](#115-适配器模式之利)11.5 适配器模式之利

1.  有助于避免大规模改写现有客户代码；

### [](#116-适配器模式之弊)11.6 适配器模式之弊

1.  当一种标准已经成形，使用适配器才有意义，否则完全重写也未尝不可；

## [](#第12章-装饰者模式)第12章 装饰者模式

用来透明地把对象包装在具有同样接口地另一个对象之中。用于在不修改现有对象或从其派生的子类的前提下为其增添职责。

### [](#121-装饰器的结构)12.1 装饰器的结构

用于替换大量品种子类，当品种的组合种类有很多的时候，只需要创建每个品种的装饰器类即可；

```JAVASCRIPT
/* The Bicycle interface. */
var Bicycle = new Interface('Bicycle', ['assemble', 'wash', 'ride', 'repair', 
    'getPrice']);
/* The AcmeComfortCruiser class. */
var AcmeComfortCruiser = function() { // implements Bicycle
  ...
};
AcmeComfortCruiser.prototype = {
  assemble: function() {
    ...
  },
  wash: function() {
    ...
  },
  ride: function() {
    ...
  },
  repair: function() {
    ...
  },
  getPrice: function() {
    return 399.00;
  }
};
/* The BicycleDecorator abstract decorator class. */
var BicycleDecorator = function(bicycle) { // implements Bicycle
  Interface.ensureImplements(bicycle, Bicycle);
  this.bicycle = bicycle;
}
BicycleDecorator.prototype = {
  assemble: function() {
    return this.bicycle.assemble();
  },
  wash: function() {
    return this.bicycle.wash();
  },
  ride: function() {
    return this.bicycle.ride();
  },
  repair: function() {
    return this.bicycle.repair();
  },
  getPrice: function() {
    return this.bicycle.getPrice();
  }
};
/* HeadlightDecorator class. */
var HeadlightDecorator = function(bicycle) { // implements Bicycle
  this.superclass.constructor(bicycle); // Call the superclass's constructor.
}
extend(HeadlightDecorator, BicycleDecorator); // Extend the superclass.
HeadlightDecorator.prototype.assemble = function() {
  return this.bicycle.assemble() + ' Attach headlight to handlebars.';
};
HeadlightDecorator.prototype.getPrice = function() {
  return this.bicycle.getPrice() + 15.00;
};
/* TaillightDecorator class. */
var TaillightDecorator = function(bicycle) { // implements Bicycle
  this.superclass.constructor(bicycle); // Call the superclass's constructor.
}
extend(TaillightDecorator, BicycleDecorator); // Extend the superclass.
TaillightDecorator.prototype.assemble = function() {
  return this.bicycle.assemble() + ' Attach taillight to the seat post.';
};
TaillightDecorator.prototype.getPrice = function() {
  return this.bicycle.getPrice() + 9.00;
};
/* Usage. */
var myBicycle = new AcmeComfortCruiser(); // Instantiate the bicycle.
alert(myBicycle.getPrice()); // Returns 399.00
myBicycle = new TaillightDecorator(myBicycle); // Decorate the bicycle object
                                               // with a taillight.
alert(myBicycle.getPrice()); // Now returns 408.00
myBicycle = new HeadlightDecorator(myBicycle); // Decorate the bicycle object
                                               // again, now with a headlight.
alert(myBicycle.getPrice()); // Now returns 423.00
```

#### [](#1212-装饰者模式与组合模式的比较)12.1.2 装饰者模式与组合模式的比较

共同点：

1.  都是用来包装别的对象（组合模式中称为子对象，而在装饰者模式中称为组件）；
1.  都与所包装的对象实现同样的接口并且会把任何方法调用传递給这些对象； 不同点：
1.  组合模式把众多子对象组织为一个整体，装饰者模式用于在不修改现有对象或从其派生子类的前提下为其增添职责；
1.  组合模式通常不会修改方法调用，只是向下传递并落实到叶对象，而装饰者会修改方法调用。

### [](#122-装饰者修改其组件的方式)12.2 装饰者修改其组件的方式

装饰着的作用就在于以某种方式对其组件对象的行为进行修改。

#### [](#1221-在方法之后添加行为)12.2.1 在方法之后添加行为

下面将会获得一辆带有两个前灯和一个尾灯的自行车，并且可以获取最终价格，自动叠加；

```JAVASCRIPT
HeadlightDecorator.prototype.getPrice = function() {
  return this.bicycle.getPrice() + 15.00;
};
var myBicycle = new AcmeComfortCruiser(); // Instantiate the bicycle.
alert(myBicycle.getPrice()); // Returns 399.00
myBicycle = new HeadlightDecorator(myBicycle); // Decorate the bicycle object
                                               // with the first headlight.
myBicycle = new HeadlightDecorator(myBicycle); // Decorate the bicycle object
                                               // with the second headlight.
myBicycle = new TaillightDecorator(myBicycle); // Decorate the bicycle object
                                               // with a taillight.
alert(myBicycle.getPrice()); // Now returns 438.00
```

#### [](#1222-在方法之前添加行为)12.2.2 在方法之前添加行为

可以把装饰者行为安排在调用组件方法之前，也可设法修改传递給组件方法的参数；

```JAVASCRIPT
/* FrameColorDecorator class. */
var FrameColorDecorator = function(bicycle, frameColor) { // implements Bicycle
  this.superclass.constructor(bicycle); // Call the superclass's constructor.
  this.frameColor = frameColor;
}
extend(FrameColorDecorator, BicycleDecorator); // Extend the superclass.
FrameColorDecorator.prototype.assemble = function() {
  return 'Paint the frame ' + this.frameColor + ' and allow it to dry. ' + 
      this.bicycle.assemble();
};
FrameColorDecorator.prototype.getPrice = function() {
  return this.bicycle.getPrice() + 30.00;
}; 
var myBicycle = new AcmeComfortCruiser(); // Instantiate the bicycle.
myBicycle = new FrameColorDecorator(myBicycle, 'red'); // Decorate the bicycle
                                               // object with the frame color.
myBicycle = new HeadlightDecorator(myBicycle); // Decorate the bicycle object
                                               // with the first headlight.
myBicycle = new HeadlightDecorator(myBicycle); // Decorate the bicycle object
                                               // with the second headlight.
myBicycle = new TaillightDecorator(myBicycle); // Decorate the bicycle object
                                               // with a taillight.
alert(myBicycle.assemble()); 
/* Returns:
    "Paint the frame red and allow it to dry. (Full instructions for assembling
    the bike itself go here) Attach headlight to handlebars. Attach headlight 
    to handlebars. Attach taillight to the seat post."
*/
```

#### [](#1223-替换方法)12.2.3 替换方法

根据保修期动态的判断和调用不同的方法；

```JAVASCRIPT
/* LifetimeWarrantyDecorator class. */
var LifetimeWarrantyDecorator = function(bicycle) { // implements Bicycle
  this.superclass.constructor(bicycle); // Call the superclass's constructor.
}
extend(LifetimeWarrantyDecorator, BicycleDecorator); // Extend the superclass.
LifetimeWarrantyDecorator.prototype.repair = function() {
  return 'This bicycle is covered by a lifetime warranty. Please take it to ' +
      'an authorized Acme Repair Center.';
};
LifetimeWarrantyDecorator.prototype.getPrice = function() {
  return this.bicycle.getPrice() + 199.00;
};
/* TimedWarrantyDecorator class. */
var TimedWarrantyDecorator = function(bicycle, coverageLengthInYears) { 
    // implements Bicycle
  this.superclass.constructor(bicycle); // Call the superclass's constructor.
  this.coverageLength = coverageLengthInYears;
  this.expDate = new Date();
  var coverageLengthInMs = this.coverageLength * 365 * 24 * 60 * 60 * 1000;
  expDate.setTime(expDate.getTime() + coverageLengthInMs);
}
extend(TimedWarrantyDecorator, BicycleDecorator); // Extend the superclass.
TimedWarrantyDecorator.prototype.repair = function() {
  var repairInstructions;
  var currentDate = new Date();
  if(currentDate < expDate) {
    repairInstructions = 'This bicycle is currently covered by a warranty. ' +
        'Please take it to an authorized Acme Repair Center.';
  }
  else {
    repairInstructions = this.bicycle.repair();
  }
  return repairInstructions;
};
TimedWarrantyDecorator.prototype.getPrice = function() {
  return this.bicycle.getPrice() + (40.00 * this.coverageLength);
};
```

#### [](#1224-添加新方法)12.2.4 添加新方法

当对某个装饰器添加新接口时，必须将这个装饰器放在最后，因为其他装饰者只能传递他们知道的方法，如果在添加了其他装饰器之后再添加该装饰器，新方法将无法访问。解决方法：

1.  在接口中添加新方法，并在BicycleDecorator中实现，但是这样的话，所有的子类都要实现该方法。
1.  设置过程，确保使用了新方法的装饰器一定是处于最外层的，但是这样只能为一个装饰器添加新方法。
1.  在BicycleDecorator中的构造函数中添加一些代码，对组件对象进行检查，并为其拥有的每个方法创建一个通道方法。这样如果在任何实现了新方法的装饰着外再裹上另一个装饰着的话，内层装饰者定义的新方法仍然可以访问。

```JAVASCRIPT
/* BellDecorator class. */
var BellDecorator = function(bicycle) { // implements Bicycle
  this.superclass.constructor(bicycle); // Call the superclass's constrcutor.
}
extend(BellDecorator, BicycleDecorator); // Extend the superclass.
BellDecorator.prototype.assemble = function() {
  return this.bicycle.assemble() + ' Attach bell to handlebars.';
};
BellDecorator.prototype.getPrice = function() {
  return this.bicycle.getPrice() + 6.00;
};
BellDecorator.prototype.ringBell = function() {
  return 'Bell rung.';
};
var myBicycle = new AcmeComfortCruiser(); // Instantiate the bicycle.
myBicycle = new BellDecorator(myBicycle); // Decorate the bicycle object 
                                          // with a bell.
alert(myBicycle.ringBell()); // Returns 'Bell rung.'
var myBicycle = new AcmeComfortCruiser(); // Instantiate the bicycle.
myBicycle = new BellDecorator(myBicycle); // Decorate the bicycle object 
                                          // with a bell.
myBicycle = new HeadlightDecorator(myBicycle); // Decorate the bicycle object
                                               // with a headlight.                                          
alert(myBicycle.ringBell()); // Method not found.
/* The BicycleDecorator abstract decorator class, improved. */
var BicycleDecorator = function(bicycle) { // implements Bicycle
  this.bicycle = bicycle;
  this.interface = Bicycle;
  
  // Loop through all of the attributes of this.bicycle and create pass-through
  // methods for any methods that aren't currently implemented.
  outerloop: for(var key in this.bicycle) {
    // Ensure that the property is a function.
    if(typeof this.bicycle[key] !== 'function') {
      continue outerloop;
    }
    
    // Ensure that the method isn't in the interface.
    for(var i = 0, len = this.interface.methods.length; i < len; i++) {
      if(key === this.interface.methods[i]) {
        continue outerloop;
      }
    }
    
    // Add the new method.
    var that = this;
    (function(methodName) {
      that[methodName] = function() {
        return that.bicycle[methodName]();
      };
    })(key); 
  }
}
BicycleDecorator.prototype = {
  assemble: function() {
    return this.bicycle.assemble();
  },
  wash: function() {
    return this.bicycle.wash();
  },
  ride: function() {
    return this.bicycle.ride();
  },
  repair: function() {
    return this.bicycle.repair();
  },
  getPrice: function() {
    return this.bicycle.getPrice();
  }
};
```

### [](#123-工厂的角色)12.3 工厂的角色

通过工厂模式給装饰器的应用添加顺序；工厂可以改成一个对象，通过key获取对应的装饰；

```JAVASCRIPT
/* Original AcmeBicycleShop factory class. */
var AcmeBicycleShop = function() {};
extend(AcmeBicycleShop, BicycleShop);
AcmeBicycleShop.prototype.createBicycle = function(model) {
  var bicycle;
  switch(model) {
    case 'The Speedster':
      bicycle = new AcmeSpeedster();
      break;
    case 'The Lowrider':
      bicycle = new AcmeLowrider();
      break;
    case 'The Flatlander':
      bicycle = new AcmeFlatlander();
      break;
    case 'The Comfort Cruiser':
    default:
      bicycle = new AcmeComfortCruiser();
  }
  Interface.ensureImplements(bicycle, Bicycle);
  return bicycle;  
};
/* AcmeBicycleShop factory class, with decorators. */
var AcmeBicycleShop = function() {};
extend(AcmeBicycleShop, BicycleShop);
AcmeBicycleShop.prototype.createBicycle = function(model, options) {
  // Instantiate the bicycle object.
  var bicycle = new AcmeBicycleShop.models[model]();
  // Iterate through the options and instantiate decorators.
  for(var i = 0, len = options.length; i < len; i++) {
    var decorator = AcmeBicycleShop.options[options[i].name];
    if(typeof decorator !== 'function') {
      throw new Error('Decorator ' + options[i].name + ' not found.');
    }
    var argument = options[i].arg;
    bicycle = new decorator(bicycle, argument);
  }
  // Check the interface and return the finished object.
  Interface.ensureImplements(bicycle, Bicycle);
  return bicycle;  
};
// Model name to class name mapping.
AcmeBicycleShop.models = {
  'The Speedster': AcmeSpeedster,
  'The Lowrider': AcmeLowrider,
  'The Flatlander': AcmeFlatlander,
  'The Comfort Cruiser': AcmeComfortCruiser
};
// Option name to decorator class name mapping.
AcmeBicycleShop.options = {
  'headlight': HeadlightDecorator,
  'taillight': TaillightDecorator,
  'bell': BellDecorator,
  'basket': BasketDecorator,
  'color': FrameColorDecorator,
  'lifetime warranty': LifetimeWarrantyDecorator,
  'timed warranty': TimedWarrantyDecorator
};
var myBicycle = new AcmeSpeedster();
myBicycle = new FrameColorDecorator(myBicycle, 'blue');
myBicycle = new HeadlightDecorator(myBicycle);
myBicycle = new TaillightDecorator(myBicycle);
myBicycle = new TimedWarrantyDecorator(myBicycle, 2);
var alecsCruisers = new AcmeBicycleShop();
var myBicycle = alecsCruisers.createBicycle('The Speedster', [
  { name: 'color', arg: 'blue' }, 
  { name: 'headlight' }, 
  { name: 'taillight' }, 
  { name: 'timed warranty', arg: 2 }
]);
```

### [](#124-函数装饰着)12.4 函数装饰着

创建用来包装独立的函数和方法的装饰者。

```JAVASCRIPT
function upperCaseDecorator(func) {
  return function() {
    return func.apply(this, arguments).toUpperCase();
  }
}
function getDate() {
  return (new Date()).toString();
}
getDateCaps = upperCaseDecorator(getDate);
alert(getDate()); // Returns Wed Sep 26 2007 20:11:02 GMT-0700 (PDT)
alert(getDateCaps()); // Returns WED SEP 26 2007 20:11:02 GMT-0700 (PDT)
BellDecorator.prototype.ringBellLoudly = 
    upperCaseDecorator(BellDecorator.prototype.ringBell);
var myBicycle = new AcmeComfortCruiser(); 
myBicycle = new BellDecorator(myBicycle);
alert(myBicycle.ringBell()); // Returns 'Bell rung.'
alert(myBicycle.ringBellLoudly()); // Returns 'BELL RUNG.'
```

### [](#125-装饰者模式的使用场合)12.5 装饰者模式的使用场合

如果需要为类增添特性或职责，而从该类派生子类的解决方法又很麻烦，如上例，一个车子可以有很多的选件，组成的车子种类就非常多，为每个车子种类创建一个类将会十分麻烦。

### [](#126-示例方法性能分析器)12.6 示例：方法性能分析器

为各种对象增添新特性。

```JAVASCRIPT
/* ListBuilder class. */
var ListBuilder = function(parent, listLength) {
  this.parentEl = $(parent);
  this.listLength = listLength;
};
ListBuilder.prototype = {
  buildList: function() {
    var list = document.createElement('ol');
    this.parentEl.appendChild(list);
    
    for(var i = 0; i < this.listLength; i++) {
      var item = document.createElement('li');
      list.appendChild(item);
    }
  }
};
/* SimpleProfiler class. */
var SimpleProfiler = function(component) {
  this.component = component;
};
SimpleProfiler.prototype = {
  buildList: function() {
    var startTime = new Date();
    this.component.buildList();
    var elapsedTime = (new Date()).getTime() - startTime.getTime();
    console.log('buildList: ' + elapsedTime + ' ms');
  }
};
/* Usage. */
var list = new ListBuilder('list-container', 5000); // Instantiate the object.
list = new SimpleProfiler(list); // Wrap the object in the decorator.
list.buildList(); // Creates the list and displays "buildList: 298 ms".
/* MethodProfiler class. */
var MethodProfiler = function(component) {
  this.component = component;
  this.timers = {};
  for(var key in this.component) {
    // Ensure that the property is a function.
    if(typeof this.component[key] !== 'function') {
      continue;
    }
    // Add the method.
    var that = this;
    (function(methodName) {
      that[methodName] = function() {
        that.startTimer(methodName);
        var returnValue = that.component[methodName].apply(that.component, 
          arguments);
        that.displayTime(methodName, that.getElapsedTime(methodName));
        return returnValue;
      };
    })(key); }
};
MethodProfiler.prototype = {
  startTimer: function(methodName) {
    this.timers[methodName] = (new Date()).getTime();
  },
  getElapsedTime: function(methodName) {
    return (new Date()).getTime() - this.timers[methodName];
  },
  displayTime: function(methodName, time) {
    console.log(methodName + ': ' + time + ' ms');
  }
};
/* Usage. */
var list = new ListBuilder('list-container', 5000);
list = new MethodProfiler(list);
list.buildList('ol'); // Displays "buildList: 301 ms".
list.buildList('ul'); // Displays "buildList: 287 ms".
list.removeLists('ul'); // Displays "removeLists: 10 ms".
list.removeLists('ol'); // Displays "removeLists: 12 ms".
```

### [](#127-装饰者模式之利)12.7 装饰者模式之利

1.  大幅减少子类；
1.  增大灵活性，不需要事先知道组件对象的接口；

### [](#128-装饰者模式之弊)12.8 装饰者模式之弊

1.  在遇到装饰者包装起来的对象时，那些依赖于类型检查的代码会出问题；
1.  增加架构的复杂程度。

### [](#129-小结)12.9 小结

装饰者是一种既不用创建子类，又能透明、动态地为对象增添功能地设计模式。

## [](#第13章-享元模式)第13章 享元模式

适合于解决因创建大量类似对象而累及性能的问题，适合几天都不会重新加载的大型应用系统。

### [](#131-享元的结构)13.1 享元的结构

享元模式用于减少应用程序所需对象的数量。这是通过将对象的内部状态划分为内在数据和外在数据。内在数据指的是类的内部方法所需要的信息，没有这种数据类不能正常运转，外在数据则是可以从类身上剥离并存储在其外部的信息。将内在状态相同的所有对象替换为同一个共享对象。

### [](#132-示例汽车登记)13.2 示例：汽车登记

保存每一辆汽车的详细情况及所有权；将每个汽车表示一个对象的方式：

```JAVASCRIPT
/* Car class, un-optimized. */
var Car = function(make, model, year, owner, tag, renewDate) {
  this.make = make;
  this.model = model;
  this.year = year;
  this.owner = owner;
  this.tag = tag;
  this.renewDate = renewDate;
};
Car.prototype = {
  getMake: function() {
    return this.make;
  },
  getModel: function() {
    return this.model;
  },
  getYear: function() {
    return this.year;
  },
  
  transferOwnership: function(newOwner, newTag, newRenewDate) {
    this.owner = newOwner;
    this.tag = newTag;
    this.renewDate = newRenewDate;
  },
  renewRegistration: function(newRenewDate) {
    this.renewDate = newRenewDate;    
  },
  isRegistrationCurrent: function() {
    var today = new Date();
    return today.getTime() < Date.parse(this.renewDate);
  }
};
```

#### [](#1321-内在状态和外在状态)13.2.1 内在状态和外在状态

车的自然数据为内在数据，所有权数据为外数据；

```JAVASCRIPT
/* Car class, optimized as a flyweight. */
var Car = function(make, model, year) {
  this.make = make;
  this.model = model;
  this.year = year;
};
Car.prototype = {
  getMake: function() {
    return this.make;
  },
  getModel: function() {
    return this.model;
  },
  getYear: function() {
    return this.year;
  }
};
```

#### [](#1322-用工厂进行实例化)13.2.2 用工厂进行实例化

一辆车的所有信息为key，车的示例为value，保存在一个数据中；

```JAVASCRIPT
/* CarFactory singleton. */
var CarFactory = (function() {
  
  var createdCars = {};
  
  return {
    createCar: function(make, model, year) {
      // Check to see if this particular combination has been created before.
      if(createdCars[make + '-' + model + '-' + year]) {
        return createdCars[make + '-' + model + '-' + year];
      }
      // Otherwise create a new instance and save it.
      else {
        var car = new Car(make, model, year);
        createdCars[make + '-' + model + '-' + year] = car;
        return car;
      }
    }
  };
})();
```

#### [](#1323-封装在管理器中的外在状态)13.2.3 封装在管理器中的外在状态

外在状态也封装在一个数据管理器中；

```JAVASCRIPT
/* CarRecordManager singleton. */
var CarRecordManager = (function() {
  
  var carRecordDatabase = {};
  
  return {
    // Add a new car record into the city's system. 
    addCarRecord: function(make, model, year, owner, tag, renewDate) {
      var car = CarFactory.createCar(make, model, year);
      carRecordDatabase[tag] = {
        owner: owner,
        renewDate: renewDate,
        car: car
      };
    },
    
    // Methods previously contained in the Car class.
    transferOwnership: function(tag, newOwner, newTag, newRenewDate) {
      var record = carRecordDatabase[tag];
      record.owner = newOwner;
      record.tag = newTag;
      record.renewDate = newRenewDate;
    },
    renewRegistration: function(tag, newRenewDate) {
      carRecordDatabase[tag].renewDate = newRenewDate;    
    },
    isRegistrationCurrent: function(tag) {
      var today = new Date();
      return today.getTime() < Date.parse(carRecordDatabase[tag].renewDate);
    }
  };
})();
```

### [](#133-管理外在状态)13.3 管理外在状态

1.  使用集中管理的数据库。
1.  使用组合模式，利用自身的层级体系保存信息。组合对象的叶节点全都可以是享元对象。

### [](#134-示例web日历)13.4 示例：Web日历

上面的例子是使用集中管理的键值对保存数据，这里使用组合模式。这里的例子有问题，但是意思应该是如果一年需要创建365个day的话，数量太多了，可以使用享元共享day的实例，然后月的话可以每月天数相同的共享实例。

### [](#135-示例工具提示对象)13.5 示例：工具提示对象

在js对象需要创建html内容时，享元特别适用，因为dom节点对象如果众多，会十分占用内存，享元可用少许对象即可。工具提示就是将鼠标移到工具图标上时出现的浮动文本框。

不用享元模式的代码：

```JAVASCRIPT
/* Tooltip class, un-optimized. */
var Tooltip = function(targetElement, text) {
  this.target = targetElement;
  this.text = text;
  this.delayTimeout = null;
  this.delay = 1500; // in milliseconds.
  // Create the HTML.
  this.element = document.createElement('div');
  this.element.style.display = 'none';  
  this.element.style.position = 'absolute';    
  this.element.className = 'tooltip';
  document.getElementsByTagName('body')[0].appendChild(this.element);
  this.element.innerHTML = this.text;
  // Attach the events.
  var that = this; // Correcting the scope.
  addEvent(this.target, 'mouseover', function(e) { that.startDelay(e); });
  addEvent(this.target, 'mouseout', function(e) { that.hide(); });  
};
Tooltip.prototype = {
  startDelay: function(e) {
    if(this.delayTimeout == null) {
      var that = this;
      var x = e.clientX;
      var y = e.clientY;
      this.delayTimeout = setTimeout(function() { 
        that.show(x, y); 
      }, this.delay);
    }
  },
  show: function(x, y) {
    clearTimeout(this.delayTimeout);
    this.delayTimeout = null;
    this.element.style.left = (x) + 'px';    
    this.element.style.top = (y + 20) + 'px';
    this.element.style.display = 'block';    
  },
  hide: function() {
    clearTimeout(this.delayTimeout);
    this.delayTimeout = null;
    this.element.style.display = 'none';
  }
};
/* Tooltip usage. */
var linkElement = $('link-id');
var tt = new Tooltip(linkElement, 'Lorem ipsum...');
```

使用了享元：

```JAVASCRIPT
/* Tooltip class, as a flyweight. */
var Tooltip = function() {
  this.delayTimeout = null;
  this.delay = 1500; // in milliseconds.
  // Create the HTML.
  this.element = document.createElement('div');
  this.element.style.display = 'none';  
  this.element.style.position = 'absolute';    
  this.element.className = 'tooltip';
  document.getElementsByTagName('body')[0].appendChild(this.element);
};
Tooltip.prototype = {
  startDelay: function(e, text) {
    if(this.delayTimeout == null) {
      var that = this;
      var x = e.clientX;
      var y = e.clientY;
      this.delayTimeout = setTimeout(function() { 
        that.show(x, y, text); 
      }, this.delay);
    }
  },
  show: function(x, y, text) {
    clearTimeout(this.delayTimeout);
    this.delayTimeout = null;
    this.element.innerHTML = text;
    this.element.style.left = (x) + 'px';    
    this.element.style.top = (y + 20) + 'px';
    this.element.style.display = 'block';    
  },
  hide: function() {
    clearTimeout(this.delayTimeout);
    this.delayTimeout = null;
    this.element.style.display = 'none';
  }
};
/* TooltipManager singleton, a flyweight factory and manager. */
var TooltipManager = (function() {
  var storedInstance = null;
  
  /* Tooltip class, as a flyweight. */
  var Tooltip = function() {
    ...
  };
 Tooltip.prototype = {
   ...
  };
  return {
    addTooltip: function(targetElement, text) {
      // Get the tooltip object.
      var tt = this.getTooltip();
      
      // Attach the events.
      addEvent(targetElement, 'mouseover', function(e) { tt.startDelay(e, text); });
      addEvent(targetElement, 'mouseout', function(e) { tt.hide(); });      
    },
    getTooltip: function() {
      if(storedInstance == null) {
        storedInstance = new Tooltip();
      }
      return storedInstance;
    }
  };
})();
/* Tooltip usage. */
TooltipManager.addTooltip($('link-id'), 'Lorem ipsum...');
```

### [](#136-保存实例供以后重用)13.6 保存实例供以后重用

通过把外在数据从资源密集型对象剥离以实现对这种对象的重用；用一个管理器控制对象的个数并保存外在数据；并且在实例化开销较大的情况下，可将实例保存供以后重用。

### [](#137-享元模式的适用场合)13.7 享元模式的适用场合

满足以下前提条件，即可使用享元模式：

1.  网页中使用了大量资源密集型对象。
1.  这些对象中所保存的数据至少有一部分能被转化为外在数据。也就是说必须能够把存储在对象内部的部分数据分离出来，然后将其作为参数提供给各个方法。
1.  将外在数据分离出去之后，独一无二的对象的数目相对较少。

### [](#138-实现享元模式的一般步骤)13.8 实现享元模式的一般步骤

1.  将所有外在数据从目标类玻璃。
1.  创建一个用来控制该类的实例化的工厂。
1.  创建一个用来保存外在数据的管理器。

### [](#139-享元模式之利)13.9 享元模式之利

1.  降低资源负荷，提升速度；

### [](#1310-享元模式利弊)13.10 享元模式利弊

1.  提高复杂程度，不利于调试和维护。

## [](#第14章-代理模式)第14章 代理模式

一个对象可以用来控制对另一个对象的访问。它于另外哪个对象实现了同样的接口，并且会把任何方法调用传递給那个对象；那个对象通常被称为本体。代理可以代替其本体被实例化，并使其可被远程访问。它还可以把本体的实例化推迟到真正需要的时候，对于实例化比较费时的本体，或因尺寸较大以至于不用时不宜保存在内存中的本体。代理特别适合处理需要较长时间才能把数据载入用户界面的类。

### [](#141-代理的结构)14.1 代理的结构

对控制进行访问，虽然实现的是同样的接口，但是实际上工作的是本体。

#### [](#1411-代理如何控制对本体的访问)14.1.1 代理如何控制对本体的访问

下面创建一个图书馆类；

```JAVASCRIPT
/* From chapter 3. */
var Publication = new Interface('Publication', ['getIsbn', 'setIsbn', 'getTitle', 
  'setTitle', 'getAuthor', 'setAuthor', 'display']);
var Book = function(isbn, title, author) { ... } // implements Publication
/* Library interface. */
var Library = new Interface('Library', ['findBooks', 'checkoutBook', 'returnBook']);
/* PublicLibrary class. */
var PublicLibrary = function(books) { // implements Library
  this.catalog = {};
  for(var i = 0, len = books.length; i < len; i++) {
    this.catalog[books[i].getIsbn()] = { book: books[i], available: true };
  }
};
PublicLibrary.prototype = {
  findBooks: function(searchString) {
    var results = [];
    for(var isbn in this.catalog) {
      if(!this.catalog.hasOwnProperty(isbn)) continue;
      if(searchString.match(this.catalog[isbn].getTitle()) ||
          searchString.match(this.catalog[isbn].getAuthor())) {
        results.push(this.catalog[isbn]);
      }
    }
    return results;
  },
  checkoutBook: function(book) {
    var isbn = book.getIsbn();
    if(this.catalog[isbn]) {
      if(this.catalog[isbn].available) {
        this.catalog[isbn].available = false;
        return this.catalog[isbn];
      }
      else {
        throw new Error('PublicLibrary: book ' + book.getTitle() + 
          ' is not currently available.');
      }
    }
    else {
      throw new Error('PublicLibrary: book ' + book.getTitle() + ' not found.');
    }
  },
  returnBook: function(book) {
    var isbn = book.getIsbn();
    if(this.catalog[isbn]) {
      this.catalog[isbn].available = true;
    }
    else {
      throw new Error('PublicLibrary: book ' + book.getTitle() + ' not found.');
    }    
  }
};
```

创建一个不具备任何用处的代理；

```JAVASCRIPT
/* PublicLibraryProxy class, a useless proxy. */
var PublicLibraryProxy = function(catalog) { // implements Library
  this.library = new PublicLibrary(catalog);
};
PublicLibraryProxy.prototype = {
  findBooks: function(searchString) {
    return this.library.findBooks(searchString);
  },
  checkoutBook: function(book) {
    return this.library.checkoutBook(book);
  },
  returnBook: function(book) {
    return this.library.returnBook(book);
  }
};
```

虚拟代理：将本体的实例化推迟到有方法被调用的时候；

```JAVASCRIPT
/* PublicLibraryVirtualProxy class. */
var PublicLibraryVirtualProxy = function(catalog) { // implements Library
  this.library = null;
  this.catalog = catalog; // Store the argument to the constructor.
};
PublicLibraryVirtualProxy.prototype = {
  _initializeLibrary: function() {
    if(this.library === null) {
      this.library = new PublicLibrary(this.catalog);
    }
  },
  findBooks: function(searchString) {
    this._initializeLibrary();
    return this.library.findBooks(searchString);
  },
  checkoutBook: function(book) {
    this._initializeLibrary();
    return this.library.checkoutBook(book);
  },
  returnBook: function(book) {
    this._initializeLibrary();
    return this.library.returnBook(book);
  }
};
```

#### [](#1412-虚拟代理-远程代理和保护代理)14.1.2 虚拟代理、远程代理和保护代理

以下代理都不易于在js中使用，常用的是虚拟代理。

-   远程代理：用于访问位于另一个环境的对象。
-   保护代理：根据身份控制对特定方法的代理。

#### [](#1413-代理模式和装饰者模式的比较)14.1.3 代理模式和装饰者模式的比较

1.  装饰者模式会对被包装对象的功能进行修改或补充，而代理只控制对他的访问。
1.  在被包装对象的创建方式上，装饰者模式的被包装对象的实例化过程是完全独立的，而代理模式会改变实例化的时间。
1.  代理不会像装饰者那样相互包装，代理一次只使用一个。

### [](#142-代理模式的使用场合)14.2 代理模式的使用场合

1.  开销昂贵的资源的访问或实例化。
1.  进行过程中提供“正在加载”的消息。

### [](#143-示例网页统计)14.3 示例：网页统计

感觉这里名字取的有点误解了，这里例子是，编写一个发送请求，而请求的接口有好几个，通过代理模式时实现请求发送；

下面是普通编写方式：

```JAVASCRIPT
/* Manually making the calls. */
var xhrHandler = XhrManager.createXhrHandler();
/* Get the pageview statistics. */
var callback = { 
  success: function(responseText) {
    var stats = eval('(' + responseText + ')'); // Parse the JSON data.
    displayPageviews(stats); // Display the stats on the page.
  }, 
  failure: function(statusCode) { 
    throw new Error('Asynchronous request for stats failed.');
  } 
};
xhrHandler.request('GET', '/stats/getPageviews/?page=index.html', callback);
```

适用代理模式：

```JAVASCRIPT
/* Using a remote proxy. */
/* PageStats interface. */
var PageStats = new Interface('PageStats', ['getPageviews', 'getUniques', 
  'getBrowserShare', 'getTopSearchTerms', 'getMostVisitedPages']);
/* StatsProxy singleton. */
var StatsProxy = function() { // implements PageStats
  
  /* Private attributes. */
  
  var xhrHandler = XhrManager.createXhrHandler();
  var urls = {
    pageviews: '/stats/getPageviews/',
    uniques: '/stats/getUniques/',
    browserShare: '/stats/getBrowserShare/',
    topSearchTerms: '/stats/getTopSearchTerms/',
    mostVisitedPages: '/stats/getMostVisitedPages/'
  };
  
  /* Private methods. */
  
  function xhrFailure() {
    throw new Error('StatsProxy: Asynchronous request for stats failed.');
  }
  
  function fetchData(url, dataCallback, startDate, endDate, page) {
    var callback = { 
      success: function(responseText) {
        var stats = eval('(' + responseText + ')');
        dataCallback(stats);
      }, 
      failure: xhrFailure
    };
    
    var getVars = [];
    if(startDate != undefined) {
      getVars.push('startDate=' + encodeURI(startDate));
    }
    if(endDate != undefined) {
      getVars.push('endDate=' + encodeURI(endDate));
    }    
    if(page != undefined) {
      getVars.push('page=' + page);
    }
    
    if(getVars.length > 0) {
      url = url + '?' + getVars.join('&');
    }
    
    xhrHandler.request('GET', url, callback);    
  }
  
  /* Public methods. */
  
  return {
    getPageviews: function(callback, startDate, endDate, page) {
      fetchData(urls.pageviews, callback, startDate, endDate, page);
    },
    getUniques: function(callback, startDate, endDate, page) {
      fetchData(urls.uniques, callback, startDate, endDate, page);    
    },
    getBrowserShare: function(callback, startDate, endDate, page) {
      fetchData(urls.browserShare, callback, startDate, endDate, page);    
    },
    getTopSearchTerms: function(callback, startDate, endDate, page) {
      fetchData(urls.topSearchTerms, callback, startDate, endDate, page);    
    },
    getMostVisitedPages: function(callback, startDate, endDate) {
      fetchData(urls.mostVisitedPages, callback, startDate, endDate);    
    }  
  };
}();
```

### [](#144-包装web服务的通用模式)14.4 包装Web服务的通用模式

使用派生子类的方式去扩展代理模式；

```JAVASCRIPT
/* WebserviceProxy class */
var WebserviceProxy = function() {
  this.xhrHandler = XhrManager.createXhrHandler();
};
WebserviceProxy.prototype = {  
  _xhrFailure: function(statusCode) {
    throw new Error('StatsProxy: Asynchronous request for stats failed.');
  },
  _fetchData: function(url, dataCallback, getVars) {
    var that = this;
    var callback = { 
      success: function(responseText) {
        var obj = eval('(' + responseText + ')');
        dataCallback(obj);
      }, 
      failure: that._xhrFailure
    };
    
    var getVarArray = [];
    for(varName in getVars) {
      getVarArray.push(varName + '=' + getVars[varName]);
    }
    if(getVarArray.length > 0) {
      url = url + '?' + getVarArray.join('&');
    }
    
    xhrHandler.request('GET', url, callback);    
  }
};
/* StatsProxy class, using WebserviceProxy. */
var StatsProxy = function() {}; // implements PageStats
extend(StatsProxy, WebserviceProxy);
/* Implement the needed methods. */
StatsProxy.prototype.getPageviews = function(callback, startDate, endDate, 
    page) {
  this._fetchData('/stats/getPageviews/', callback, { 
    'startDate': startDate, 
    'endDate': endDate, 
    'page': page 
  });
};
StatsProxy.prototype.getUniques = function(callback, startDate, endDate, 
    page) {
  this._fetchData('/stats/getUniques/', callback, { 
    'startDate': startDate, 
    'endDate': endDate, 
    'page': page 
  });
};
StatsProxy.prototype.getBrowserShare = function(callback, startDate, endDate, 
   page) {
  this._fetchData('/stats/getBrowserShare/', callback, { 
    'startDate': startDate, 
    'endDate': endDate, 
    'page': page 
  });
};
StatsProxy.prototype.getTopSearchTerms = function(callback, startDate, 
    endDate, page) {
  this._fetchData('/stats/getTopSearchTerms/', callback, { 
    'startDate': startDate, 
    'endDate': endDate, 
    'page': page 
  });
};
StatsProxy.prototype.getMostVisitedPages = function(callback, startDate, 
    endDate) {
  this._fetchData('/stats/getMostVisitedPages/', callback, { 
    'startDate': startDate, 
    'endDate': endDate 
  });
};
```

### [](#145-示例目录查找)14.5 示例：目录查找

由于数据源较大，且网页的访问量交大，为了节约带宽和提高性能，我们使用虚拟代理把加载资源推迟到需要的时候，并在加载数据的时候提供提示信息。

```javascript
/* DirectoryProxy class, with loading message. */
var DirectoryProxy = function(parent) { // implements Directory
  this.parent = parent;
  this.directory = null;
  this.warning = null;
  this.interval = null;
  this.initialized = false;
  var that = this;
  addEvent(parent, 'mouseover', that._initialize); // Initialization trigger.
};
DirectoryProxy.prototype = {
  _initialize: function() {
    this.warning = document.createElement('div');
    this.parent.appendChild(this.warning);
    this.warning.innerHTML = 'The company directory is loading...';
    this.directory = new PersonnelDirectory(this.parent);
    var that = this;
    this.interval = setInterval(that._checkInitialization, 100);
  },
  _checkInitialization: function() {
    if(this.directory.currentPage != null) {
      clearInterval(this.interval);
      this.initialized = true;
      this.parent.removeChild(this.warning);
    }
  },
  showPage: function(page) {
    if(!this.initialized) {
      return;
    }
    return this.directory.showPage(page);
  }
};
```

### [](#146-创建虚拟代理的通用模式)14.6 创建虚拟代理的通用模式

将本体的实例化推迟到你认为必要的时候；将本体作为参数传入，在内部有必要的时候去实例化，而不是传入一个实例化对象；

```javascript
/* DynamicProxy abstract class, complete. */
var DynamicProxy = function() {
  this.args = arguments;
  this.initialized = false;
  
  if(typeof this.class != 'function') {
    throw new Error('DynamicProxy: the class attribute must be set before ' + 
      'calling the super-class constructor.');
  }
  
  // Create the methods needed to implement the same interface.
  for(var key in this.class.prototype) {
    // Ensure that the property is a function.
    if(typeof this.class.prototype[key] !== 'function') {
      continue;
    }
    // Add the method.
    var that = this;
    (function(methodName) {
      that[methodName] = function() {
        if(!that.initialized) {
          return
        }
        return that.subject[methodName].apply(that.subject, arguments);
      };
    })(key);
  }
};
DynamicProxy.prototype = {
  _initialize: function() {
    this.subject = {}; // Instantiate the class.
    this.class.apply(this.subject, this.args);
    this.subject.__proto__ = this.class.prototype;
    var that = this;
    this.interval = setInterval(function() { that._checkInitialization(); }, 100);
  },
  _checkInitialization: function() {
    if(this._isInitialized()) {
      clearInterval(this.interval);
      this.initialized = true;
    }
  },
  _isInitialized: function() { // Must be implemented in the subclass.
    throw new Error('Unsupported operation on an abstract class.');
  }
};
/* TestProxy class. */
var TestProxy = function() {
  this.class = TestClass;
  var that = this;
  addEvent($('test-link'), 'click', function() { that._initialize(); }); 
    // Initialization trigger.
  TestProxy.superclass.constructor.apply(this, arguments);
};
extend(TestProxy, DynamicProxy);
TestProxy.prototype._isInitialized = function() {
  ... // Initialization condition goes here.
};
```

### [](#147-代理模式之利)14.7 代理模式之利

1.  远程代理：减少为访问远程资源而不得不编写的粘合性代码；
1.  虚拟代理：用代理代替本体，不用操心实例化开销的问题；

### [](#148-代理模式之弊)14.8 代理模式之弊

1.  刻意掩盖了大量复杂行为，增加了复杂性，可能不知道这是干嘛的，文档需要全面一点。

## [](#第15章-观察者模式)第15章 观察者模式

用于管理对象及其行为和状态之间的关系。

### [](#151-示例报纸的投送)15.1 示例：报纸的投送

订阅者要从发布者接受数据，一个订阅者可能会订阅很多家报纸，一个发布者也可以被很多人订阅，是多对多的关系。

#### [](#1511-推与拉的比较)15.1.1 推与拉的比较

订阅者获取报纸的方式有两种，一种是自己去某地拿（拉），一种是别人送到家门口（推）。

#### [](#1512-模式的实践)15.1.2 模式的实践

-   订阅者可以订阅和退订，还可选择“推”还是“拉”；
-   发布者负责投送，可以选择“推“还是”拉“；

以下发布者处理主导地位，负责登记顾客，有权停止投送，并且投送报纸

```javascript
/* From http://pluralsight.com/blogs/dbox/archive/2007/01/24/45864.aspx */
/*
  * Publishers are in charge of "publishing" i.e. creating the event.
  * They're also in charge of "notifying" (firing the event).
*/
var Publisher = new Observable;
/*
  * Subscribers basically... "subscribe" (or listen).
  * Once they've been "notified" their callback functions are invoked.
*/
var Subscriber = function(news) {
  // news delivered directly to my front porch
};
Publisher.subscribeCustomer(Subscriber);
/*
  * Deliver a paper:
  * sends out the news to all subscribers.
*/
Publisher.deliver('extre, extre, read all about it');
/*
  * That customer forgot to pay his bill.
*/
Publisher.unSubscribeCustomer(Subscriber);
```

以下是拥有订阅和退订权的一方变成了订阅者

```javascript
/*
  * Newspaper Vendors
  * setup as new Publisher objects
*/
var NewYorkTimes = new Publisher;
var AustinHerald = new Publisher;
var SfChronicle = new Publisher;
/*
  * People who like to read
  * (Subscribers)
  *
  * Each subscriber is set up as a callback method.
  * They all inherit from the Function prototype Object.
*/
var Joe = function(from) {
  console.log('Delivery from '+from+' to Joe');
};
var Lindsay = function(from) {
  console.log('Delivery from '+from+' to Lindsay');
};
var Quadaras = function(from) {
  console.log('Delivery from '+from+' to Quadaras ');
};
/*
  * Here we allow them to subscribe to newspapers 
  * which are the Publisher objects.
  * In this case Joe subscribes to the NY Times and
  * the Chronicle. Lindsay subscribes to NY Times
  * Austin Herald and Chronicle. And the Quadaras
  * respectfully subscribe to the Herald and the Chronicle
*/
Joe.
  subscribe(NewYorkTimes).
  subscribe(SfChronicle);
Lindsay.
  subscribe(AustinHerald).
  subscribe(SfChronicle).
  subscribe(NewYorkTimes);
Quadaras.
  subscribe(AustinHerald).
  subscribe(SfChronicle);
    
/* 
  * Then at any given time in our application, our publishers can send 
  * off data for the subscribers to consume and react to.
*/
NewYorkTimes.
  deliver('Here is your paper! Direct from the Big apple');
AustinHerald.
  deliver('News').
  deliver('Reviews').
  deliver('Coupons');
SfChronicle.
  deliver('The weather is still chilly').
  deliver('Hi Mom! I'm writing a book');
```

### [](#152-构建观察者api)15.2 构建观察者API

发布者的构造函数，会保存订阅者：

```javascript
function Publisher() {
  this.subscribers = [];
}
```

#### [](#1521-投送方式)15.2.1 投送方式

对所有订阅者投送

```javascript
Publisher.prototype.deliver = function(data) {
  this.subscribers.forEach(
    function(fn) {
      fn(data);
    }
  );
  return this;
};
```

#### [](#1522-订阅方法)15.2.2 订阅方法

订阅者添加订阅能力；

```javascript
Function.prototype.subscribe = function(publisher) {
  var that = this;
  var alreadyExists = publisher.subscribers.some(
    function(el) {
      if ( el === that ) {
        return;
      }
    }
  );
  if ( !alreadyExists ) {
    publisher.subscribers.push(this);
  }
  return this;
};
```

#### [](#1523-订退方法)15.2.3 订退方法

```javascript
Function.prototype.unsubscribe = function(publisher) {
  var that = this;
  publisher.subscribers = publisher.subscribers.filter(
    function(el) {
      if ( el !== that ) {
        return el;
      }
    }
  );
  return this;
};
```

### [](#153-现实生活中的观察者)15.3 现实生活中的观察者

提高API的灵活性，使并行开发的多个实现能够彼此独立地进行修改。

### [](#154-示例动画)15.4 示例：动画

设置在某个地方执行什么操作。

```javascript
// Publisher API
var Animation = function(o) {
  this.onStart = new Publisher,
  this.onComplete = new Publisher,
  this.onTween = new Publisher;
};
Animation.
  method('fly', function() {
    // begin animation
    this.onStart.deliver();
    for ( ... ) { // loop through frames
      // deliver frame number
      this.onTween.deliver(i); 
    }
    // end animation
    this.onComplete.deliver();
  });
// setup an account with the animation manager
var Superman = new Animation({...config properties...});
// Begin implementing subscribers
var putOnCape = function(i) { };
var takeOffCape = function(i) { };
putOnCape.subscribe(Superman.onStart);
takeOffCape.subscribe(Superman.onComplete);
// fly can be called anywhere
Superman.fly();
// for instance:
addEvent(element, 'click', function() {
  Superman.fly();
});
```

### [](#155-事件监听器也是观察者模式)15.5 事件监听器也是观察者模式

让多个函数响应一个事件；

### [](#156-观察者模式的使用场合)15.6 观察者模式的使用场合

一个对象（目标对象）的状态发生改变，所有的依赖对象（观察者对象）都将得到通知，进行广播通知。

### [](#157-观察者模式之利)15.7 观察者模式之利

1.  降低内存消耗和提高互动性能；
1.  减少系统开销，提高程序的可维护性；
1.  有助于防止第三方开发人员和合作伙伴因为对应用程序地细节了解的太多而把事情搞糟

### [](#158-观察者模式之弊)15.8 观察者模式之弊

1.  创建可观察对象所带来的加载时间开销，可通过惰性加载技术加以化解，就是将新的可观察对象地实例化推迟到需要发送事件通知的时候；

## [](#第16章-命令模式)第16章 命令模式

是一种数据驱动的设计模式，它属于行为型模式。请求以命令的形式包裹在对象中，并传给调用对象。调用对象寻找可以处理该命令的合适的对象，并把该命令传给相应的对象，该对象执行命令。将一个请求封装成一个对象，从而使您可以用不同的请求对客户进行参数化。命令模式可以彻底消除用户界面元素与负责实际工作的类之间的耦合。

### [](#161-命令模式的结构)16.1 命令模式的结构

最简形式的命令对象是一个操作和用以调用这个操作的对象的结合体。所有的命令对象都有一个执行操作，其用途就是调用命令对象所绑定的操作。在大多数命令对象中，这个操作是一个名为execute或run的方法，使用同样接口的所有命令对象都可以被同等对待，并且可以随意互换。

模式创建步骤：

1.  创建一个命令接口；
1.  创建一个请求类；
1.  创建实现接口的实体类；
1.  创建命令调用类，该类可以添加命令；
1.  创建执行类，该类向命令调用类中依次添加各种命令，并执行命令；

因为感觉书上的例子不太好，所以这里重新改写了别的；

```javascript
// 1
var Order = new Interface('Order', ['execute']);
// 2
var Stock = function () {
    this.name = "ABC";
    this.quantity = 10;
    this.buy = function () {
        console.log("Stock [ Name: " + name + ",Quantity: " + quantity + " ] bought")
    };
    this.sell = function () {
        console.log("Stock [ Name: " + name + ",Quantity: " + quantity + " ] sold");
    };
};
// 3
var BuyStock = function (abcStock) { // implements AdCommand
    Interface.ensureImplements(abcStock, Order);
    this.abcStock = abcStock;
};
BuyStock.prototype.execute = function () {
    this.abcStock.buy();
};
var SellStock = function (abcStock) { // implements AdCommand
    Interface.ensureImplements(abcStock, Order);
    this.abcStock = abcStock;
};
SellStock.prototype.execute = function () {
    this.abcStock.sell();
};
// 4
var Broker = function () {
    this.orderList = [];
    this.takeOrder = function (order) {
        this.orderList.push(order);
    };
    this.placeOrders = function () {
        this.orderList.forEach((order) => {
            order.execute();
        });
        this.orderList = [];
    }
};
var CommandPatternDemo = function () {
    var stock = new Stock();
    
    var buyOrder = new BuyStock(stock);
    var sellStock = new SellStock(stock);
    
    var broker = new Broker();
    broker.takeOrder(buyOrder);
    broker.takeOrder(sellOrder);
    
    broker.placeOrders();
};
CommandPatternDemo();
```

#### [](#1611-用闭包创建命令对象)16.1.1 用闭包创建命令对象

这种方式不适合于需要多个命令方法的场合；

```javascript
/* Commands using closures. */
function makeStart(adObject) {
  return function() { 
    adObject.start();
  };
}
function makeStop(adObject) {
  return function() {
    adObject.stop();
  };
}
/* Implementation code. */
var startCommand = makeStart(ads[i]);
var stopCommand = makeStop(ads[i]);
startCommand(); // Execute the functions directly instead of calling a method.
stopCommand();
```

#### [](#1612-客户-调用者和接收者)16.1.2 客户、调用者和接收者

该模式中有三个参与者：客户、调用者和接收者，客户负责实例化命令并将其交给调用者。在上面的例子中，客户是CommandPatternDemo；它通常被包装为一个对象，但也不是非这样不可；调用者接过命令将其保存，它会在某个时候调用该命令对象的execute方法，或者将其交给另一个潜在的调用者；调用者是Broker，会调用命令独享的execute方法。

客户创建命令；调用者执行该命令，接收者在命令执行时执行相应的操作。

所有的命令模式都有客户和调用者，但不一定有接收者。

#### [](#1613-在命令模式中使用接口)16.1.3 在命令模式中使用接口

接口的作用：确保接收者实现了所需要的操作，以及命令对象实现了正确的执行操作。

```javascript
/* Command interface. */
var Command = new Interface('Command', ['execute']);
/* Checking the interface of a command object. */
// Ensure that the execute operation is defined. If not, a descriptive exception
// will be thrown.
Interface.ensureImplements(someCommand, Command);
// If no exception is thrown, you can safely invoke the execute operation. 
someCommand.execute(); 
/* Checking command functions. */
if(typeof someCommand != 'function') {
  throw new Error('Command isn't a function');
}
```

### [](#162-命令对象的类型)16.2 命令对象的类型

所有类型的命令对象执行的都是同样的任务：隔离调用操作的对象与实际实施操作的对象。有两种极端情况：

1.  简单命令对象：命令对象所起的作用只不过是把现有接收者的操作与调用者绑定在一起。一般用来消除两个对象之间的耦合；

```javascript
/* SimpleCommand, a loosely coupled, simple command class. */
var SimpleCommand = function(receiver) { // implements Command
  this.receiver = receiver;
};
SimpleCommand.prototype.execute = function() {
  this.receiver.action();
};
```

2.  复杂命令对象：封装着一套复杂指定的命令对象。这种命令对象实际上没有接收者，因为它自己提供了操作的具体实现。一般用来封装不可分的或事务性的指令。

```javascript
/* ComplexCommand, a tightly coupled, complex command class. */
var ComplexCommand = function() { // implements Command
  this.logger = new Logger();
  this.xhrHandler = XhrManager.createXhrHandler();
  this.parameters = {};
};
ComplexCommand.prototype = {
  setParameter: function(key, value) {
    this.parameters[key] = value;
  },
  execute: function() {
    this.logger.log('Executing command');
    var postArray = [];
    for(var key in this.parameters) {
      postArray.push(key + '=' + this.parameters[key]);
    }
    var postString = postArray.join('&');
    this.xhrHandler.request(
      'POST', 
      'script.php', 
      function() {}, 
      postString
    );
  }
};
```

在这两种极端之间存在一个灰色地带，有些命令对象不但封装着接收者的操作，而且其execute方法中也具有一些实现代码；

### [](#163-示例菜单项)16.3 示例：菜单项

用最简类型的命令对象构建模块化的用户界面，把菜单项（调用者）和实际执行操作（接收者）的对象隔开。菜单项无需了解接收者的用法，这需要知道所有命令对象都实现了一个execute方法即可。

#### [](#1631-菜单组合对象)16.3.1 菜单组合对象

MenuBar和Menu都是组合对象类，而MenuItem则是叶类，MenuBar保存着所有Menu实例；

```javascript
/* Command, Composite and MenuObject interfaces. */
var Command = new Interface('Command', ['execute']);
var Composite = new Interface('Composite', ['add', 'remove', 'getChild', 
  'getElement']);
var MenuObject = new Interface('MenuObject', ['show']);
/* MenuBar class, a composite. */
var MenuBar = function() { // implements Composite, MenuObject
  this.menus = {};
  this.element = document.createElement('ul');
  this.element.style.display = 'none';
};
MenuBar.prototype = {
  add: function(menuObject) {
    Interface.ensureImplements(menuObject, Composite, MenuObject);
    this.menus[menuObject.name] = menuObject;
    this.element.appendChild(this.menus[menuObject.name].getElement());
  },
  remove: function(name) {
    delete this.menus[name];
  },
  getChild: function(name) {
    return this.menus[name];
  },
  getElement: function() {
    return this.element;
  },
  
  show: function() {
    this.element.style.display = 'block';
    for(name in this.menus) { // Pass the call down the composite.
      this.menus[name].show();
    }
  }
};
/* Menu class, a composite. */
var Menu = function(name) { // implements Composite, MenuObject
  this.name = name;
  this.items = {};
  this.element = document.createElement('li');
  this.element.innerHTML = this.name;
  this.element.style.display = 'none';
  this.container = document.createElement('ul');
  this.element.appendChild(this.container);
};
Menu.prototype = {
  add: function(menuItemObject) {
    Interface.ensureImplements(menuItemObject, Composite, MenuObject);
    this.items[menuItemObject.name] = menuItemObject;
    this.container.appendChild(this.items[menuItemObject.name].getElement());
  },
  remove: function(name) {
    delete this.items[name];
  },
  getChild: function(name) {
    return this.items[name];
  },
  getElement: function() {
    return this.element;
  },
  
  show: function() {
    this.element.style.display = 'block';
    for(name in this.items) { // Pass the call down the composite.
      this.items[name].show();
    }
  }
};
/* MenuItem class, a leaf. */
var MenuItem = function(name, command) { // implements Composite, MenuObject
  Interface.ensureImplements(command, Command);
  this.name = name;
  this.element = document.createElement('li');
  this.element.style.display = 'none';
  this.anchor = document.createElement('a');
  this.anchor.href = '#'; // To make it clickable.
  this.element.appendChild(this.anchor);
  this.anchor.innerHTML = this.name;
  addEvent(this.anchor, 'click', function(e) { // Invoke the command on click.
    e.preventDefault(); 
    command.execute();
  });
};
MenuItem.prototype = {
  add: function() {},
  remove: function() {},
  getChild: function() {},
  getElement: function() {
    return this.element;
  },
  
  show: function() {
    this.element.style.display = 'block';
  }
};
```

#### [](#1632-命令类)16.3.2 命令类

只保存方法引用，并且执行即可；

```javascript
/* MenuCommand class, a command object. */
var MenuCommand = function(action) { // implements Command
  this.action = action;
};
MenuCommand.prototype.execute = function() {
  this.action();
};
```

#### [](#1633-汇合起来)16.3.3 汇合起来

创建MenuBar类的一个实例，并为它添加一些Menu和item对象，其中每个item都绑定一个命令对象；

```javascript
/* Implementation code. */
/* Receiver objects, instantiated from existing classes. */
var fileActions = new FileActions();
var editActions = new EditActions();
var insertActions = new InsertActions();
var helpActions = new HelpActions();
/* Create the menu bar. */
var appMenuBar = new MenuBar();
/* The File menu. */
var fileMenu = new Menu('File');
var openCommand = new MenuCommand(fileActions.open);
var closeCommand = new MenuCommand(fileActions.close);
var saveCommand = new MenuCommand(fileActions.save);
var saveAsCommand = new MenuCommand(fileActions.saveAs);
fileMenu.add(new MenuItem('Open', openCommand));
fileMenu.add(new MenuItem('Close', closeCommand));
fileMenu.add(new MenuItem('Save', saveCommand));
fileMenu.add(new MenuItem('Save As...', saveAsCommand));
appMenuBar.add(fileMenu);
/* The Edit menu. */
var editMenu = new Menu('Edit');
var cutCommand = new MenuCommand(editActions.cut);
var copyCommand = new MenuCommand(editActions.copy);
var pasteCommand = new MenuCommand(editActions.paste);
var deleteCommand = new MenuCommand(editActions.delete);
editMenu.add(new MenuItem('Cut', cutCommand));
editMenu.add(new MenuItem('Copy', copyCommand));
editMenu.add(new MenuItem('Paste', pasteCommand));
editMenu.add(new MenuItem('Delete', deleteCommand));
appMenuBar.add(editMenu);
/* The Insert menu. */
var insertMenu = new Menu('Insert');
var textBlockCommand = new MenuCommand(insertActions.textBlock);
insertMenu.add(new MenuItem('Text Block', textBlockCommand));
appMenuBar.add(insertMenu);
/* The Help menu. */
var helpMenu = new Menu('Help');
var showHelpCommand = new MenuCommand(helpActions.showHelp);
helpMenu.add(new MenuItem('Show Help', showHelpCommand));
appMenuBar.add(helpMenu);
/* Build the menu bar. */
document.getElementsByTagName('body')[0].appendChild(appMenuBar.getElement());
appMenuBar.show();
```

#### [](#1634-添加更多菜单项)16.3.4 添加更多菜单项

添加操作类，然后add一下（假设已经完成InsertActions类）；

```javascript
/* Adding more menu items later on. */
var imageCommand = new MenuCommand(insertActions.image);
insertMenu.add(new MenuItem('Image', imageCommand));
```

### [](#164-示例取消操作和命令日志)16.4 示例：取消操作和命令日志

调用者可以回滚用execute执行的操作，undo方法可以用来实现不受限制的取消功能。只需要把执行锅的命令对象压入栈顶即可实现对命令执行历史的记录；我们这里修改一下Command接口，添加undo方法；

```javascript
/* ReversibleCommand interface. */
var ReversibleCommand = new Interface('ReversibleCommand', ['execute', 'undo']);
/* Movement commands. */
var MoveUp = function(cursor) { // implements ReversibleCommand
  this.cursor = cursor;
};
MoveUp.prototype = {
  execute: function() {
    cursor.move(0, -10);
  },
  undo: function() {
    cursor.move(0, 10);    
  }
};
var MoveDown = function(cursor) { // implements ReversibleCommand
  this.cursor = cursor;
};
MoveDown.prototype = {
  execute: function() {
    cursor.move(0, 10);
  },
  undo: function() {
    cursor.move(0, -10);    
  }
};
var MoveLeft = function(cursor) { // implements ReversibleCommand
  this.cursor = cursor;
};
MoveLeft.prototype = {
  execute: function() {
    cursor.move(-10, 0);
  },
  undo: function() {
    cursor.move(10, 0);    
  }
};
var MoveRight = function(cursor) { // implements ReversibleCommand
  this.cursor = cursor;
};
MoveRight.prototype = {
  execute: function() {
    cursor.move(10, 0);
  },
  undo: function() {
    cursor.move(-10, 0);    
  }
};
/* Cursor class. */
var Cursor = function(width, height, parent) {
  this.width = width;
  this.height = height;
  this.position = { x: width / 2, y: height / 2 };
  this.canvas = document.createElement('canvas');
  this.canvas.width = this.width;
  this.canvas.height = this.height;
  parent.appendChild(this.canvas);
  
  this.ctx = this.canvas.getContext('2d');
  this.ctx.fillStyle = '#cc0000';
  this.move(0, 0);  
};
Cursor.prototype.move = function(x, y) {
  this.position.x += x;
  this.position.y += y; 
  this.ctx.clearRect(0, 0, this.width, this.height);
  this.ctx.fillRect(this.position.x, this.position.y, 3, 3);
};
/* UndoDecorator class. */
var UndoDecorator = function(command, undoStack) { // implements ReversibleCommand
  this.command = command;
  this.undoStack = undoStack;
};
UndoDecorator.prototype = {
  execute: function() {
    this.undoStack.push(this.command);
    this.command.execute();
  },
  undo: function() {
    this.command.undo();
  }
};
/* CommandButton class. */
var CommandButton = function(label, command, parent) {
  Interface.ensureImplements(command, ReversibleCommand);
  this.element = document.createElement('button');
  this.element.innerHTML = label;
  parent.appendChild(this.element);
  
 addEvent(this.element, 'click', function() {
    command.execute();
  });
};
/* UndoButton class. */
var UndoButton = function(label, parent, undoStack) {
  this.element = document.createElement('button');
  this.element.innerHTML = label;
  parent.appendChild(this.element);
  
  addEvent(this.element, 'click', function() {
    if(undoStack.length === 0) return;
    var lastCommand = undoStack.pop();
    lastCommand.undo();
  });
};
/* Implementation code. */
var body = document.getElementsByTagName('body')[0];
var cursor = new Cursor(400, 400, body);
var undoStack = [];
var upCommand = new UndoDecorator(new MoveUp(cursor), undoStack);
var downCommand = new UndoDecorator(new MoveDown(cursor), undoStack);
var leftCommand = new UndoDecorator(new MoveLeft(cursor), undoStack);
var rightCommand = new UndoDecorator(new MoveRight(cursor), undoStack);
var upButton = new CommandButton('Up', upCommand, body);
var downButton = new CommandButton('Down', downCommand, body);
var leftButton = new CommandButton('Left', leftCommand, body);
var rightButton = new CommandButton('Right', rightCommand, body);
var undoButton = new UndoButton('Undo', body, undoStack);
```

#### [](#1641-使用命令日志实现不可逆操作的取消)16.4.1 使用命令日志实现不可逆操作的取消

上面的例子是可逆转的，但是现实生活中会有很多不可逆的操作。这里可以这么做：将栈中最后一个操作弹出弃之不用，然后依次把剩下的所有命令执行一次。

```JAVASCRIPT
/* Movement commands. */
var MoveUp = function(cursor) { // implements Command
  this.cursor = cursor;
};
MoveUp.prototype = {
  execute: function() {
    cursor.move(0, -10);
  }
};
/* Cursor class, with an internal command stack. */
var Cursor = function(width, height, parent) {
  this.width = width;
  this.height = height;
  this.commandStack = [];
  this.canvas = document.createElement('canvas');
  this.canvas.width = this.width;
  this.canvas.height = this.height;
  parent.appendChild(this.canvas);
  
  this.ctx = this.canvas.getContext('2d');
  this.ctx.strokeStyle = '#cc0000';
  this.move(0, 0);
};
Cursor.prototype = {
  move: function(x, y) {
    var that = this;
    this.commandStack.push(function() { that.lineTo(x, y); });
    this.executeCommands();
  },
  lineTo: function(x, y) {
    this.position.x += x;
    this.position.y += y; 
    this.ctx.lineTo(this.position.x, this.position.y);
  },
  executeCommands: function() {
    this.position = { x: this.width / 2, y: this.height / 2 };
    this.ctx.clearRect(0, 0, this.width, this.height); // Clear the canvas.
    this.ctx.beginPath();
    this.ctx.moveTo(this.position.x, this.position.y);
    for(var i = 0, len = this.commandStack.length; i < len; i++) {
      this.commandStack[i]();
    }
    this.ctx.stroke();
  },
  undo: function() {
    this.commandStack.pop();
    this.executeCommands();
  }
};
/* UndoButton class. */
var UndoButton = function(label, parent, cursor) {
  this.element = document.createElement('button');
  this.element.innerHTML = label;
  parent.appendChild(this.element);
  
  addEvent(this.element, 'click', function() {
    cursor.undo();
  });  
};
/* Implementation code. */
var body = document.getElementsByTagName('body')[0];
var cursor = new Cursor(400, 400, body);
var upCommand = new MoveUp(cursor);
var downCommand = new MoveDown(cursor);
var leftCommand = new MoveLeft(cursor);
var rightCommand = new MoveRight(cursor);
var upButton = new CommandButton('Up', upCommand, body);
var downButton = new CommandButton('Down', downCommand, body);
var leftButton = new CommandButton('Left', leftCommand, body);
var rightButton = new CommandButton('Right', rightCommand, body);
var undoButton = new UndoButton('Undo', body, cursor);
```

#### [](#1642-用于崩溃恢复的命令日志)16.4.2 用于崩溃恢复的命令日志

因为将所有的命令都记录下来了，所以可以根据命令恢复其状态。如果系统比较复杂，那么这种类型的命令日志会有很大的存储需求，为此你可以提供一个按钮，供用户上传所有操作。

### [](#165-命令模式的使用场合)16.5 命令模式的使用场合

1.  命令模式的主要用途是把调用对象（用户界面、API和代理等）与实现操作的对象隔离开；
1.  需要对操作进行规范化处理的场合；
1.  封装XHR调用
1.  延迟性调用场合的回调函数。
1.  实现不受限制的取消机制，把执行过的命令保存在栈中。
1.  使用命令日志可以用来取消本质上不可逆的操作。
1.  可在程序崩溃之后用来恢复其整体状态。

### [](#166-命令模式之利)16.6 命令模式之利

1.  提高程序的模块化程度和灵活性；
1.  实现取消和状态恢复等复杂的有用特性十分容易；
1.  有助于创建高度模块化的调用者；
1.  只要是能够在execute方法中实现的操作，不管多复杂，差异多大，都能以任何别的命令完全相同的方式进行传送和调用。

### [](#167-命令模式之弊)16.7 命令模式之弊

1.  增加代码调试的难度；

## [](#第17章-职责链模式)第17章 职责链模式

用来消除请求的发送者和接收者之间的耦合。通过实现一个由隐式地对请求进行处理的对象组成地链而做到的。链中的每个对象可以处理请求，也可传给下一个对象。js的事件捕获和冒泡就是使用了这个模式。职责链上的处理者负责处理请求，客户只需要将请求发送到职责链上即可，无须关心请求的处理细节和请求的传递，所以职责链将请求的发送者和请求的处理者解耦了。

职责链的核心：知道什么时候处理请求以及什么时候传递请求。

### [](#171-职责链的结构)17.1 职责链的结构

职责链由多个不同类型的对象组成。发送者是发出请求的对象，而接收者则是链中那些接收这种请求并且对其进行处理或传递的对象，请求本身有时候也是一个对象，它封装着与操作相关的所有数据。典型运转流程大致如下：

1.  发送者知道链中的第一个接收者，它向这个接收者发出请求；
1.  每个接收者都对请求进行分析，然后处理或向下传递；
1.  每个接收者知道的其他对象只有一个，即它在链中的下家；
1.  如果没有任何接收者处理请求，那么请求将从链上离开。不同的实现对此有不同的反应。

下面是个例子，向图书馆存储书的时候，会根据书是否符合特定标准而决定是否将其编入内部目录中。职责链模式的方式：只需要将书传给链中第一个目录对象，第一个目录判断判断并处理完是否编入，会传给第二个目录，一直这样下去。

```JAVASCRIPT
var Publication = new Interface('Publication', ['getIsbn', 'setIsbn', 'getTitle',
    'setTitle', 'getAuthor', 'setAuthor', 'getGenres', 'setGenres', 'display']);
var Library = new Interface('Library', [‘addBook’, 'findBooks', 'checkoutBook',
    'returnBook']);
var Catalog = new Interface('Catalog', ['handleFilingRequest', 'findBooks',
    'setSuccessor']);
    
var PublicLibrary = function(books, firstGenreCatalog) { // implements Library
  this.catalog = {};
  this.firstGenreCatalog = firstGenreCatalog;
  for(var i = 0, len = books.length; i < len; i++) {
    this.addBook(books[i]);
  }
};
PublicLibrary.prototype = {
  findBooks: function(searchString) {
    var results = [];
    for(var isbn in this.catalog) {
      if(!this.catalog.hasOwnProperty(isbn)) continue;
      if(this.catalog[isbn].getTitle().match(searchString) ||
          this.catalog[isbn].getAuthor().match(searchString)) {
        results.push(this.catalog[isbn]);
      }
    }
    return results;
  },
  checkoutBook: function(book) {
    var isbn = book.getIsbn();
    if(this.catalog[isbn]) {
      if(this.catalog[isbn].available) {
        this.catalog[isbn].available = false;
        return this.catalog[isbn];
      }
      else {
        throw new Error('PublicLibrary: book ' + book.getTitle() +
            ' is not currently available.');
      }
    }
    else {
      throw new Error('PublicLibrary: book ' + book.getTitle() + ' not found.');
    }
  },
  returnBook: function(book) {
    var isbn = book.getIsbn();
    if(this.catalog[isbn]) {
      this.catalog[isbn].available = true;
    }
    else {
      throw new Error('PublicLibrary: book ' + book.getTitle() + ' not found.');
    }
  },
  addBook: function(newBook) {
    // Always add the book to the main catalog.
    this.catalog[newBook.getIsbn()] = { book: newBook, available: true };
    // Try to add the book to each genre catalog.
    this.firstGenreCatalog.handleFilingRequest(newBook);
  }
};
/* GenreCatalog class, used as a superclass for specific catalog classes. */
var GenreCatalog = function() { // implements Catalog
  this.successor = null;
  this.catalog = [];
};
GenreCatalog.prototype = {
  _bookMatchesCriteria: function(book) { 
    return false; // Default implementation; this method will be overriden in
                  // the subclasses.
  },
  handleFilingRequest: function(book) { // 判断是否可以添加到该目录，如果可以就加入，并且判断有没有下一个链，有就判断下一个
    // Check to see if the book belongs in this catagory.
    if(this._bookMatchesCriteria(book)) {
      this.catalog.push(book);
    }
    // Pass the request on to the next link.
    if(this.successor) {
      this.successor.handleFilingRequest(book);
    }
  },
  findBooks: function(request) {
    if(this.successor) {
      return this.successor.findBooks(request);
    }
  },
  setSuccessor: function(successor) {// 设置当前目录的下一个目录对象
    if(Interface.ensureImplements(successor, Catalog) {
      this.successor = successor;
    }
  }
};
/* SciFiCatalog class. */
var SciFiCatalog = function() {}; // implements Catalog
extend(SciFiCatalog, GenreCatalog);
SciFiCatalog.prototype._bookMatchesCriteria = function(book) {
  var genres = book.getGenres();
  if(book.getTitle().match(/space/i)) {
    return true;
  }
  for(var i = 0, len = genres.length; i < len; i++) {
    var genre = genres[i].toLowerCase();
    if(genres === 'sci-fi' || genres === 'scifi' || genres === 'science fiction') {
      return true;
    }
  }
  return false;
};
// -----------------------------------------------------------------------------
// Usage example.
// -----------------------------------------------------------------------------
 // Instantiate the catalogs.
var biographyCatalog = new BiographyCatalog();
var fantasyCatalog = new FantasyCatalog();
var mysteryCatalog = new MysteryCatalog();
var nonFictionCatalog = new NonFictionCatalog();
var sciFiCatalog = new SciFiCatalog();
// Set the links in the chain.
biographyCatalog.setSuccessor(fantasyCatalog);
fantasyCatalog.setSuccessor(mysteryCatalog);
mysteryCatalog.setSuccessor(nonFictionCatalog);
nonFictionCatalog.setSuccessor(sciFiCatalog);
// Give the first link in the chain as an argument to the constructor.
var myLibrary = new PublicLibrary(books, biographyCatalog);
// You can add links to the chain whenever you like.
var historyCatalog = new HistoryCatalog();
sciFiCatalog.setSuccessor(historyCatalog);
```

### [](#172-传递请求)17.2 传递请求

以下代码可分为三部分，第一部分逐一检查请求对象中的每一个类别名称，看看其是否与对象中保存的一组类别名称中的某一个匹配，如果匹配进入第二部分，第二部分会逐一检查目录中所有的图书，看看是否匹配，匹配的添加到results中，最后一部分，如果当前目录对象不是链中最后一环，继续传递下去，否则将返回请求对象。

```JAVASCRIPT
/* PublicLibrary class. */
var PublicLibrary = function(books) { // implements Library
  ...
};
PublicLibrary.prototype = {
  findBooks: function(searchString, genres) {
    // If the optional genres argument is given, search for books only in
    // those genres. Use the chain of responsibility to perform the search.
    if(typeof genres === 'array' && genres.length > 0) {
      var requestObject = {
        searchString: searchString,
        genres: genres,
        results: []
      };
      var responseObject = this.firstGenreCatalog.findBooks(requestObject);
      return responseObject.results;
    }
    // Otherwise, search through all books.
    else {
      var results = [];
      for(var isbn in this.catalog) {
        if(!this.catalog.hasOwnProperty(isbn)) continue;
        if(this.catalog[isbn].getTitle().match(searchString) ||
            this.catalog[isbn].getAuthor().match(searchString)) {
          results.push(this.catalog[isbn]);
        }
      }
      return results;
    }
  },
  checkoutBook: function(book) { ... },
  returnBook: function(book) { ... },
  addBook: function(newBook) { ... }
};
/* GenreCatalog class, used as a superclass for specific catalog classes. */
var GenreCatalog = function() { // implements Catalog
  this.successor = null;
  this.catalog = [];
  this.genreNames = [];
};
GenreCatalog.prototype = {
  _bookMatchesCriteria: function(book) { ... }
  handleFilingRequest: function(book) { ... },
  findBooks: function(request) {
    var found = false;
    for(var i = 0, len = request.genres.length; i < len; i++) {
      for(var j = 0, nameLen = this.genreNames.length; j < nameLen; j++) {
        if(this.genreNames[j] === request.genres[i]) {
          found = true; // This link in the chain should handle
                        // the request.
          break;
        }
      }
    }
    if(found) { // Search through this catalog for books that match the search
                // string and aren't already in the results.
      outerloop: for(var i = 0, len = this.catalog.length; i < len; i++) {
        var book = this.catalog[i];
        if(book.getTitle().match(searchString) ||
            book.getAuthor().match(searchString)) {
          for(var j = 0, requestLen = request.results.length; j < requestLen; j++) {
            if(request.results[j].getIsbn() === book.getIsbn()) {
              continue outerloop; // The book is already in the results; skip it.
            }
          }
          request.results.push(book); // The book matches and doesn't already
                                      // appear in the results. Add it.
        }
      }
    }
    // Continue to pass the request down the chain if the successor is set.
    if(this.successor) {
      return this.successor.findBooks(request);
    }
    // Otherwise, we have reached the end of the chain. Return the request
    // object back up the chain.
    else {
      return request;
    }
  },
  setSuccessor: function(successor) { ... }
};
/* SciFiCatalog class. */
var SciFiCatalog = function() { // implements Catalog
  this.genreNames = ['sci-fi', 'scifi', 'science fiction'];
};
extend(SciFiCatalog, GenreCatalog);
SciFiCatalog.prototype._bookMatchesCriteria = function(book) { ... };
```

### [](#173-在现有层级体系中实现职责链)17.3 在现有层级体系中实现职责链

该模式常与组合模式搭配使用，因为组合模式已经建立了一个对象层级体系，在此基础上添加一些处理请求会比较简单。组合模式和职责链模式结合之后，方法调用就不再总是不加分辨的一直往下传給叶对象了，而会对每层进行分析和处理。这种结合对双方都是一种优化，减少设置代码的数量和用于职责链的额外对象的数量，并且降低了整个树上执行该方法所需要的计算量。

### [](#174-事件委托)17.4 事件委托

事件模型本质上是作为一个职责链实现的。事件被触发时要经历两个阶段。第一个是事件捕获阶段，事件会沿html层次体系向下传播，从顶层开始经历各个子元素，直到到达被点击元素，从此时开始第二阶段，即事件冒泡，历经同一批元素升回到顶层祖先，绑定在事件经过的这些元素上的事件监听器既可以停止事件传播，也可以让其继续沿层次体系向上或向下传播。

### [](#175-职责链模式的适用场合)17.5 职责链模式的适用场合

1.  有多个对象可以处理同一个请求，具体哪个对象处理该请求由运行时刻自动确定。
1.  在不明确指定接收者的情况下，向多个对象中的一个提交一个请求。
1.  可动态指定一组对象处理请求。

### [](#176-图片库的进一步讨论)17.6 图片库的进一步讨论

目的：在第9章的图片库添加把图片动态加入该层次体系中的任意层次；方法：从顶层将请求逐级下传；

#### [](#1761-用职责链提高组合对象的效率)17.6.1 用职责链提高组合对象的效率

因为hiden的时候，子类就不需要再传递了，因为一起不显示了，但是show的时候需要传递，因为不知道是哪里hide了。

```JAVASCRIPT
/* DynamicGallery class. */
var DynamicGallery = function(id) { // implements Composite, GalleryItem
  ...
}
DynamicGallery.prototype = {
  add: function(child) { ... },
  remove: function(child) { ... },
  getChild: function(i) { ... },
  hide: function() {
    this.element.style.display = 'none';
  },
  show: function() { ... },
  getElement: function() { ... }
};
```

#### [](#1762-为图片添加标签)17.6.2 为图片添加标签

标签是一个描述性的标题，可以用来对图片进行分类。图片和图片库都可添加标签。可以再层次体系中任何层次上搜索具有指定标签的所有图像。addTag将为用以调用它的对象及其所有子对象添加一个标签，getPhotoWithTag将返回一个由具体特定标签的所有图片组成的数组。对任何组合对象调用agetAllLeaves将返回所有叶级后代节点组成的数组。

```JAVASCRIPT
/* Interfaces. */
var Composite = new Interface('Composite', ['add', 'remove', 'getChild',
    'getAllLeaves']);
var GalleryItem = new Interface('GalleryItem', ['hide', 'show', 'addTag',
    'getPhotosWithTag']);
/* DynamicGallery class. */
var DynamicGallery = function(id) { // implements Composite, GalleryItem
  this.children = [];
  this.tags = [];
  this.element = document.createElement('div');
  this.element.id = id;
  this.element.className = 'dynamic-gallery';
}
DynamicGallery.prototype = {
  ...
  addTag: function(tag) {
    this.tags.push(tag);
    for(var node, i = 0; node = this.getChild(i); i++) {
      node.addTag(tag);
    }
  },
  getAllLeaves: function() {
    var leaves = [];
    for(var node, i = 0; node = this.getChild(i); i++) {
      leaves.concat(node.getAllLeaves());
    }
    return leaves;
  },
  getPhotosWithTag: function(tag) {
    // First search in this object's tags; if the tag is found here, we can stop
    // the search and just return all the leaf nodes.
    for(var i = 0, len = this.tags.length; i < len; i++) {
      if(this.tags[i] === tag) {
        return this.getAllLeaves();
      }
    }
    // If the tag isn't found in this object's tags, pass the request down
    // the hierarchy.
    for(var results = [], node, i = 0; node = this.getChild(i); i++) {
      results.concat(node.getPhotosWithTag(tag));
    }
    return results;
  },
  ...
};
/* GalleryImage class. */
var GalleryImage = function(src) { // implements Composite, GalleryItem
  this.element = document.createElement('img');
  this.element.className = 'gallery-image';
  this.element.src = src;
  this.tags = [];
}
GalleryImage.prototype = {
  ...
  addTag: function(tag) {
    this.tags.push(tag);
  },
  getAllLeaves: function() { // Just return this.
    return [this];
  },
  getPhotosWithTag: function(tag) {
    for(var i = 0, len = this.tags.length; i < len; i++) {
      if(this.tags[i] === tag) {
        return [this];
      }
    }
    return []; // Return an empty array if no matches were found.
  },
  ...
};
```

### [](#177-职责链之利)17.7 职责链之利

1.  可以使用只有在运行期间才能知道的条件来把任务分派给最恰当的对象。
1.  消除发出请求的对象与处理请求的对象之间的耦合性；
1.  更大的灵活性；
1.  可重用组合对象的结构来传递请求，直到找到一个可以处理请求的对象。

### [](#178-职责链之弊)17.8 职责链之弊

1.  可能会在某个节点上断掉，因为并没有方法保存；
1.  无法得知如果请求能够得到处理的话具体由哪个对象处理它；虽然可以通过创建一个通用的接收者将其添加到所有链的尾端解决，但是这样失去了灵活性；
1.  和组合模式结合虽然效率高，但是复杂性提高了；

## [](#小结)小结

所有的模式都不能滥用，当模式的利大于弊的时候才考虑用，如果不清楚利和弊哪个更大，最好不要用。