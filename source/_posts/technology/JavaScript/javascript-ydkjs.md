---
title: You Don't Know JS
date: 2018-01-19 09:00:00
toc: true
categories:
- Technology
- JavaScript
tags:
- JavaScript
- ES6
---

![You Don't Know JS Study Notes](https://ksr-ugc.imgix.net/assets/011/512/107/1988f3a0fc77cc3eac49cac6f1b7ebc0_original.png?ixlib=rb-2.1.0&crop=faces&w=1552&h=873&fit=crop&v=1463683806&auto=format&frame=1&q=92&s=7037537d3031513ff4a7222daf1256a3)

You Don't Know JS Study Notes

<!-- more -->

# ES

**JavaScript**标准的官方名称是**ECMAScript**，简称**ES**。

最早版本ES1和ES2，实现很少，不怎么为人所知。第一个流行起来的版本是ES3，它成为浏览器IE6-8和早前的旧版Android 2.x移动浏览器的JavaScript标准。出于某些政治原因，倒霉的ES4从来没有成形。

2009年，ES5正式发布(然后是2011年的ES5.1)，在当代浏览器(包括Firefox、Chrome、Opera、Safari以及许多其他类型)的进化和爆发中成为JavaScript广泛使用的标准。

ES6，发布日期从2013年拖到2014年，然后又到2015年。

在ES6之前的JavaScript标准通常被称为ES5(严格说是ES5.1)。

## 1. 作用域

**作用域是一套规则，用于确定在何处以及如何查找变量(标识符)。**

有两种主要工作模型: **词法作用域**和**动态作用域**。

**词法作用域意味着作用域是由书写代码时函数声明的位置来决定的。**是一套关于引擎如何寻找变量及会在何处找到变量的规则。编译的词法分析阶段基本能够知道全部标识符在哪里以及是如何声明的，从而能够预测在执行过程中如何对它们进行查找。

而动态作用域并不关心函数和作用域是如何声明以及在何处声明的，只关心它们从**从何处调用**。

两者之间主要区别:

- 词法作用域是在写代码或者说定义时确定的，而动态作用域是在运行时确定的。
- 词法作用域关注函数在何处声明，而动态作用域关注函数从何处调用。

### 编译执行

```js
var a = 2;
```

- 编译阶段

	遇到`var a`，**编译器**会询问**作用域**是否已经有一个该名称的变量存在于同一个作用域的集合中。如果是，编译器会忽略该声明，继续进行编译;否则它会要求作用域在当前作 用域的集合中声明一个新的变量，并命名为`a`。

- 执行阶段

	接下来编译器会为**引擎**生成运行时所需的代码，这些代码被用来处理`a = 2`这个赋值操作。引擎运行时会首先询问作用域，在当前的作用域集合中是否存在一个叫作`a`的 变量。如果是，引擎就会使用这个变量;如果否，引擎会继续查找该变量。

```js
'use strict'; // ES5引入
a = 2;
```

上述代码对于`a = 2`，如果**引擎**最终在所有可访问的**词法作用域**中找到了`a`变量，就会将`2`赋值给它。否则引擎就会抛出一个**异常**。

```js
function foo(a) {
	console.log(a, b); // ReferenceError: b is not defined
}

foo(2);
```

当获取某个变量的值，在所有**嵌套作用域**中找不到所需变量，引擎就会抛出**ReferenceError**，即作用域判别失败。而**TypeError**则是作用域判别成功了，但是对结果的操作是非法或不合理的。

### 常见作用域单元

- 函数声明
```js
var a = 4;

function foo() {
	var a = 3;
	console.log(a); // 3
}
foo();

console.log(a); // 4
```

- 匿名函数表达式
```js
var a = 4;

setTimeout(function() {
	var a = 3;
	console.log(a); // 3
}, 1000);

console.log(a); // 4
```

- 具名函数表达式
```js
setTimeout(function hadnler() {
	// ...
}, 1000);
```

- 立即执行函数表达式
```js
var a = 4;

(function() {
	var a = 3;
	console.log(a); // 3
})();

console.log(a); // 4
```
社区规定术语：**IIFE(Immediately Invoked Function Expression)**。最佳实践：
```js
(function IIFE() {
	// ...
})();
```

- __块__
```js
for (var i = 0; i < 10; i++) {
	console.log(i);
}

console.log(i); // 10
```
```js
var t = true;
if (t) {
	var a = 3;
	console.log(a);
}

console.log(a); // 3
```
变量的声明应该距离使用的地方越近越好。但是，上述代码中变量`i`和`a`，会被绑定在外部作用域(**函数或全局**)([提升](#提升))，它们只是为了风格而伪装出的形式上的块作用域。为了防止在作用域内被提升，ES6引入了[let](#let)关键字。

### 提升

```js
a = 2;
var a;
console.log(a);
```

```js
console.log(a);
var a = 2;
```

**变量和函数的所有声明都会在任何代码被执行前首先被处理。**就好像它们在代码中出现的位置被“移动”到了最上面，这个过程就叫**提升**。

So，以上两段代码会以如下形式进行处理：
```js
var a;
a = 2;
console.log(a);
```

```js
var a;
console.log(a);
a = 2;
```

> - **只有声明本身会被提升，赋值或其他运行逻辑会留在原地，等待被执行。如果提改变了代码的执行顺序，会造成非常严重的破坏**
> - **每个作用域都会进行提升操作**
> - **函数会优先被提升**

## 2. 闭包

### 识别闭包

当函数可以记住并访问所在的词法作用域时，就产生了闭包，即使函数是在当前词法作用域之外执行。

```js
function foo() {
	var a = 2;

	function bar() {
		console.log(a);
	}

	return bar;
}

var baz = foo();

baz(); // 2 —— 闭包的效果
```

在`foo()`执行后，通常会期待`foo()`的整个内部作用域都被销毁，因为引擎有垃回收器用来释放不再使用的内存空间。由于看上去`foo()`的内容不会再被使用，所以很自然地会考虑对其进行回收。

**闭包**的存在使得`foo()`内部作用域没有被回收。`bar()`本身在使用内部作用域，它依然持有对该作用域的引用，这个引用就叫“**闭包**”。

无论使用哪种形式对函数类型的值**进行传递**，当函数在别处被调用时，就会产生闭包。

```js
function foo() {
	var a = 2;

	function baz() {
		console.log(a); // 2
	}

	bar(baz);
}

function bar(fn) {
	fn(); // 闭包
}
```

```js
var fn;

function foo() {
	var a = 2;

	function baz() {
		console.log(a);
	}

	fn = baz; // 将baz分配给全局变量，间接传递
}

function bar() {
	fn(); // 闭包
}

foo();

bar(); // 2
```

```js
function wait(msg) {
	var type = 'info:';

	setTimeout(function timer() {
		console.log(type, msg); // info: hello, closure
	}, 1000);
}

wait('hello, closure');
```

### 循环和闭包

* 共享全局作用域
```js
for (var i = 1; i <= 5; i++) {
	setTimeout(function timer() { // timer会在循环结束时才执行
		console.log(i);
	}, i*1000);
}
```
理想情况是循环中的每个迭代在运行时都会给自己“捕获”一个`i`的副本。但是根据作用域的工作原理，尽管五个函数是在各个迭代中分别定义的，但其实它们都**被封闭在一个共享的全局作用域中**，因此实际上只有一个`i`。

* 每个迭代创建一个闭包作用域
```js
for (var i = 1; i <= 5; i++) {
	(function() {
		var j = i;
		setTimeout(function timer() {
			console.log(j);
		}, j*1000);
	})();
}

// 改进版
for (var i = 1; i <= 5; i++) {
	(function(j) {
		setTimeout(function timer() {
			console.log(j);
		}, j*1000);
	})(i);
}
```

* 使用块作用域
```js
for (let i = 1; i <= 5; i++) {
	setTimeout(function timer() {
		console.log(i);
	}, i*1000);
}
```

### 模块

```js
function CoolModule() {
	var something = "cool";
	var another = [1, 2, 3];

	function doSomething() {
		console.log(something);
	}

	function doAnother() {
		console.log(another.join(" ! "));
	}

	return {
		doSomething: doSomething,
		doAnother: doAnother
	};
}

var foo = CoolModule();

foo.doSomething(); // cool
foo.doAnother(); // 1 ! 2 ! 3
```
以上模式在JavaScript中被称为**模块**。最常见的实现模块模式的方法通常被称为**模块暴露**，这里展示的是其变体。

模块模式需要具备两个**必要条件**：

- 必须有外部的封闭函数，该函数必须至少被调用一次(每次调用都会创建一个新的模块实例)。
- 封闭函数必须返回至少一个内部函数，这样内部函数才能在私有作用域中形成闭包，并且可以访问或者修改私有的状态。

上一段代码中有一个叫作`CoolModule()`的独立的模块创建器，可以被调用任意多次，每次调用都会创建一个新的模块实例。当只需要一个实例时，可以对这个模式进行简单的改进来实现**单例模式**:
```js
var foo = (function CoolModule() {
	var something = "cool";
	var another = [1, 2, 3];

	function doSomething() {
		console.log(something);
	}

	function doAnother() {
		console.log(another.join(" ! "));
	}

	return {
		doSomething: doSomething,
		doAnother: doAnother
	};
})();

foo.doSomething(); // cool
foo.doAnother(); // 1 ! 2 ! 3
```

#### 现代模块机制

模块加载器/管理器：
```js
var MyModules = (function Manager() {
	var modules = {};

	function define(name, deps, impl) {
		for (var i = 0; i < deps.length; i++) {
			deps[i] = modules[deps[i]];
		}
		modules[name] = impl.apply(impl, deps); // 核心
	}

	function get(name) {
		return modules[name];
	}

	return {
		define: define,
		get: get
	};
})();
```

定义模块：
```js

MyModules.define( "bar", [], function() {
	function hello(who) {
		return "Let me introduce: " + who;
	}

	return {
		hello: hello
	};
});

MyModules.define( "foo", ["bar"], function(bar) {
	var hungry = "hippo";

	function awesome() {
		console.log(bar.hello( hungry ).toUpperCase());
	}

	return {
		awesome: awesome
	};
});

var bar = MyModules.get( "bar" );
var foo = MyModules.get( "foo" );

console.log(bar.hello( "hippo" )); // Let me introduce: hippo

foo.awesome(); // LET ME INTRODUCE: HIPPO
```

#### 未来模块机制

- bar.js
```js
function hello(who) {
	return "Let me introduce: " + who;
}

export hello;
```

- foo.js
```js
// 仅从 "bar" 模块导入 hello()
import hello from "bar";

var hungry = "hippo";

function awesome() {
	console.log(hello(hungry).toUpperCase());
}

export awesome;
```

- baz.js
```js
module foo from "foo";
module bar from "bar";

console.log(bar.hello("rhino")); // Let me introduce: rhino

foo.awesome(); // LET ME INTRODUCE: HIPPO
```

## 3. this

`this`可以优雅的隐式“传递”一个对象引用，让API设计的更加简洁并且易于使用。如果显示传递上下文对象会让代码变的越来越混乱。

`this`是在运行时进行绑定的，并不是在编写时绑定，它的上下文取决于函数调用时的各种条件。`this`的绑定和函数声明的位置没有任何关系，只取决于函数的调用方式。

当一个函数被调用时，会创建一个活动记录(有时候也称为执行上下文)。这个记录会包含函数在哪里被调用(调用栈)、函数的调用方法、传入的参数等信息。`this`就是记录的 其中一个属性，会在函数执行的过程中用到。

### 误解

#### 指向函数自身

记录函数`foo`的被调用次数：
```js
function foo(num) {
	console.log("foo: " + num);

	// 记录 foo 被调用的次数
	this.count++;
}

foo.count = 0;

var i;

for (i = 0; i < 10; i++) {
	if (i > 5) { foo(i); }
}

console.log(foo.count); // 0
console.log(count); // NaN
```

```js
function foo(num) {
	console.log("foo: " + num);

	// 记录 foo 被调用的次数
	this.count++;
}

foo.count = 0;

var i;

for (i = 0; i < 10; i++) {
	if (i > 5) {
		// 使用 call(..) 可以确保 this 指向函数对象 foo 本身
		foo.call(foo, i);
	}
}

console.log(foo.count); // 4
```

#### 指向函数的作用域

在某种情况下是正确的，但是在其他情况下却是错误的。

__this在任何情况下都不指向函数的词法作用域。__

```js
function foo() {
	var a = 2;
	this.bar();
}

function bar() {
	console.log(this.a);
}

foo();
```

### 调用位置

调用位置，即函数被调用的位置，可以分析**调用栈**得到。真正的的调用位置，决定了`this`绑定。

```js
function baz() {
	// 当前调用栈是:baz
	// 因此，当前调用位置是全局作用域

	console.log( "baz" );
	bar(); // <-- bar 的调用位置
}

function bar() {
	// 当前调用栈是 baz -> bar
	// 因此，当前调用位置在 baz 中

	console.log( "bar" );
	foo(); // <-- foo 的调用位置
}

function foo() {
	// 当前调用栈是 baz -> bar -> foo
	// 因此，当前调用位置在 bar 中

	console.log( "foo" );
}

baz(); // <-- baz 的调用位置 Chrome: anonymous Firefox: global
```

### 绑定规则

#### 默认绑定

最常用的函数调用类型：**独立函数调用**。

```js
function foo() {
	console.log(this.a); // this指向全局对象。
}

var a = 2;

foo(); 	// 2
```

```js
function foo() {
	"use strict";
	console.log(this); // this绑定到undefined
}

var a = 2;

foo(); 	// TypeError: Cannot read property 'a' of undefined
```

```js
function foo() {
	console.log(this.a); // this指向全局对象。
}

var a = 2;

(function() {
	"use strict";
	foo(); 	// 2
})();
```

通常不会在代码中混合使用`strict`模式和非`strict`模式。

#### 隐式绑定

在一个对象内部包含一个指向函数的属性，通过这个属性间接引用函数，从而把`this`隐式绑定到这个对象上。

当函数引用有上下文对象时，**隐式绑定**规则会把函数调用中的`this`绑定到这个上下文对象。

```js
function foo() {
	console.log(this.a); // this被绑定到obj
}

var obj = {
	a: 2,
	foo: foo
};

obj.foo(); // 2
```

对象属性引用链中只有上一层(最后一层)在调用位置中起作用。e.g.:
```js
function foo() {
	console.log(this.a);
}

var obj2 = {
	a: 42,
	foo: foo
};

var obj1 = {
	a: 2,
	obj2: obj2
};

obj1.obj2.foo(); //42
```

__隐式丢失__

丢失绑定对象，应用默认绑定，从而绑定到全局对象或`undefined`。

- 函数引用传递
```js
function foo() {
	console.log(this.a);
}

var obj = {
	a: 2,
	foo: foo
};

var bar = obj.foo; // 函数别名

var a = "oops, global";

bar(); // oops, global
```

- 传入回调
```js
function foo() {
	console.log(this.a);
}

function doFoo(fn) {
	// fn引用的是foo

	fn(); // <-- 调用位置
}

var obj = {
	a: 2,
	foo: foo
};

var a = "oops, global";

doFoo(obj.foo); // oops, global
```

#### 显式绑定

在某个对象上强制调用函数，可以使用`call(..)`和`apply(..)`。

```js
function foo() {
	console.log(this.a);
}

var obj = {
	a: 2
};

foo.call(obj); // 2
```

如果传入一个原始值(字符串类型/布尔类型/数字类型)来当作`this`的绑定对象，这个原始值会被转换成相应的对象形式(`new String()/new Boolean()/new Number()`)。即**装箱**。

显式绑定仍然会出现**丢失绑定**的问题。

```js
function foo() {
	console.log(this.a);
}

var obj = {
	a: 2,
	foo: foo
};

var bar = obj.foo;

var a = "oops, global";

bar(); // oops, global
```

使用显式绑定的一个变种可以解决这个问题，称为**硬绑定**。

```js
function foo() {
	console.log(this.a);
}

var obj = {
	a: 2
};

var bar = function() {
	foo.call(obj);
}

bar(); // 2

bar.call(window); // 2
```

由于硬绑定是一种非常常用的模式，所以在ES5中提供了内置的方法`Function.prototype.bind`，它的用法如下:

```js
function foo(something) {
	console.log(this.a, something);
	return this.a + something;
}

var obj = {
	a:2
};

var bar = foo.bind(obj);

var b = bar(3); // 2 3
console.log(b); // 5
```

`bind(..)`会返回一个硬编码的新函数，它会把你指定的参数设置为`this`的上下文并调用原始函数。

#### new绑定

在传统的面向类的语言中，“构造函数”是类中的一些特殊方法，使用`new`初始化类时会被调用。通常形式如下：

```java
User user = new User();
```

在JavaScript中，`new`的机制和面向类的语言完全不同。构造函数只是一些使用`new`操作符时被调用的普通函数。它们并不会属于某个类，也不会实例化一个类。

使用`new`来调用函数，或者说发生构造函数调用时，会自动执行下面的操作。

- 1.创建(或者说构造)一个全新的对象。
- 2.这个新对象会被执行**\[\[Prototype\]\]**连接。
- 3.这个新对象会绑定到函数调用的this。
- 4.如果函数没有返回其他对象，那么new表达式中的函数调用会自动返回这个新对象。

```js
function foo(a) {
	this.a = a;
}

var bar = new foo(2);
console.log(bar.a); // 2
```

### 优先级

关于`this`的四条绑定规则，只需要找到函数的调用位置，基本就可以判断该应用哪条规则。但是，有些调用位置可以应用多条规则，这时就必须根据**优先级**来进行判断了。

毫无疑问，**默认绑定**的优先级是最低的。

- 显式绑定 vs 隐式绑定
```js
function foo() {
	console.log(this.a);
}

var obj1 = {
	a: 2,
	foo: foo
};

var obj2 = {
	a: 3,
	foo: foo
};

obj1.foo(); // 2
obj2.foo(); // 3

obj1.foo.call(obj2); // 3
obj2.foo.call(obj1); // 2
```
显式绑定优先级高于隐式绑定。

- new绑定 vs 隐式绑定
```js
function foo(something) {
	this.a = something;
}

var obj1 = {
	foo: foo
};

var obj2 = {};

obj1.foo(2);
console.log(obj1.a); // 2

obj1.foo.call(obj2, 3);
console.log(obj2.a); // 3

var bar = new obj1.foo(4);
console.log(obj1.a); // 2
console.log(bar.a); // 4
```
new绑定优先级高于隐式绑定。

- new绑定 vs 显式绑定
```js
function foo(something) {
	this.a = something;
}

var obj1 = {};

var bar = foo.bind(obj1);
bar(2);
console.log(obj1.a); // 2

var baz = new bar(3);
console.log(obj1.a); // 2
console.log(baz.a); // 3
```
`bar`被硬绑定到`obj1`上，但是`new bar(3)`并没有把`obj1.a`修改为`3`。相反，`new`修改了硬绑定调用`bar(..)`中的`this`。使用`new`绑定，得到一个名为`baz`的新对象，并且`baz.a`的值是`3`。

__判断this__

- 1.函数是否在`new`中调用(**new绑定**)？如果是的话this绑定的是新创建的对象。
```js
var bar = new foo();
```

- 2.函数是否通过`call`、`apply`(**显式绑定**)或者硬绑定调用？如果是的话，this绑定的是指定的对象。
```js
var bar = foo.call(obj2);
```

- 3.函数是否在某个上下文对象中调用(**隐式绑定**)？如果是的话，`this`绑定的是那个上下文对象。
```js
var bar = obj1.foo();
```

- 4.如果都不是的话，使用**默认绑定**。如果在严格模式下，就绑定到`undefined`，否则绑定到全局对象。
```js
var bar = foo();
```

## 4. 原型

## 5. 异步

<!-- JavaScript程序卸载多个`.js`文件中，这个程序由多个**块**构成。最常见的**块**单位是函数。

**宿主环境**提供一种机制来处理程序中多个块的执行，且执行每块时调用JavaScript引擎，这种机制被称为**事件循环**。

“事件”(代码执行)调度总是由包含它的环境进行。 -->

**回调**是JavaScript中最基础的异步模式。

以下代码被称为**回调地狱(callback hell)**，有时也被称为**毁灭金字塔(pyramid of doom)**：
```js
listen("click", function handler(evt) {
	setTimeout(function request() {
		ajax("http://some.url.1", function response(text) {
			if (text == "hello") {
				handler(); }
			else if (text == "world") {
				request();
			}
		});
	}, 500) ;
});
```

为了更优雅的处理错误，有些API设计提供了**分离回调**：
```js
function success(data) {
	console.log(data);
}

function failure(err) {
	console.error(err);
}

ajax("http://some.url.1", success, failure);
```

# ES6特性

## 语法特性

### 1. 块作用域

#### let

`let`可以将变量绑定到所在的任意作用域中(通常是{..}内部)。换句话说，`let`为其声明的变量隐式的劫持了所在的块作用域。

```js
for (let i = 0; i < 10; i++) {
	console.log(i);
}

console.log(i); // ReferenceError: i is not defined
```

```js
var t = true;
if (t) {
	let a = 3;
	console.log(a);
}

console.log(a); // ReferenceError: a is not defined

if (t) {
	{ // 显式的块
		let b = 2;
		console.log(b);
	}
}
```

```js
var a = 2;

{
	let a = 3;
	console.log(a); // 3
}

console.log(a); // 2
```

__`let`声明不会在块作用域中进行提升:__
```js
{
	console.log(a); // undefined
	console.log(b); // ReferenceError: c is not defined

	var a;
	let b;
}
```

__垃圾收集:__

```js
function process(data) {
	// ...
}

var someReallyBigData = { .. };

process(someReallyBigData);

var btn = document.getElementById( "my_button" );

btn.addEventListener( "click", function click(evt) {
	console.log("button clicked");
}, /*capturingPhase=*/false );
```

`click`函数的点击回调并不需要`someReallyBigData`变量。理论上这意味着当`process(..)`执行后，在内存中占用大量空间的数据结构就可以被垃圾回收了。但是，由于`click`函数形成了一个覆盖整个作用域的闭包，JavaScript引擎极有可能依然保存着这个结构(取决于具体实现)。

块作用域可以打消这种顾虑，可以让引擎清楚地知道没有必要继续保存`someReallyBigData`了:
```js
function process(data) {
	// ...
}

// 在这个块中定义的内容可以销毁了!
{
	let someReallyBigData = { .. };
	process( someReallyBigData );
}

var btn = document.getElementById( "my_button" );

btn.addEventListener( "click", function click(evt){
	console.log("button clicked");
}, /*capturingPhase=*/false );
```

__注意:__

- 尽量把`let`声明放在块的最前面
- 声明多个变量，建议使用一个`let`
```js
let a, b, c;
```

#### const

```js
const a = 2;
console.log(a);
a = 3; //TypeError
```

- 用于创建常量
- 常量不是对这个值本身的限制，而是对赋值的那个变量的限制
- 大写字母+下划线
```js
const MAX_VALUE = 10;
```

#### 块作用域函数

__ES6之前`condition`无论值为什么，`bar`声明都会被提升，最后一个胜出。__

```js
if (condition) {
	function bar() {
		console.log(1);
	}
} else {
	function bar() {
		console.log(2);
	}
}

bar();
```

### 2. spread/rest

__新运算符`...`__

```js
// spread: 把变量展开为各个独立的值
function foo(x, y, z) {
	console.log(x, y, z);
}

foo(...[1, 2, 3]);

// [1].concat(a, [5])
var a = [2, 3, 4];
var b = [1, ...a, 5];
console.log(b);

// rest: 把一系列值收集到一起成为一个数组
function foo(x, y, ...z) {
	console.log(x, y, z);
}
foo(1, 2, 3, 4, 5);

function foo(...r) {
	console.log(r);
}
foo(1, 2, 3, 4, 5);
```

__`arguments`，并不是真正的数组，而是类似数组的对象。已被弃用，不推荐使用。__

```js
// 按照新的ES6的行为方式实现
function foo(...args) {
	// args已经是一个真正的数组

	// 丢弃args中第一个元素
    args.shift();

	// 把整个args作为参数传给console.log(..)
    console.log( ...args );
}

// 按照前ES6的老派行为方式实现
function bar() {
	// 把arguments转换为一个真正的数组
	var args = Array.prototype.slice.call( arguments );

	// 在尾端添加几个元素
	args.push( 4, 5 );

	// 过滤掉奇数
	args = args.filter( function(v){
		return v % 2 == 0;
	} );

	// 把整个args作为参数传给foo(..)
	foo.apply( null, args );
}

bar( 0, 1, 2, 3 ); // 2 4
```

### 3. 默认参数值

#### 简单默认值

__ES5中默认值的实现，作用很大，也很危险。__

```js
function foo(x, y) {
	x = x || 11;
	y = y || 31;

	// x = (x !== undefined) ? x : 11;
	// y = (y !== undefined) ? y : 31;

	console.log(x + y);
}

foo();
foo(5, 6);
foo(5);
foo(null, 6);
```

- 被认为是假的值

```js
foo(0, 42)
```

- 省略第一个参数

```js
foo(, 6);

foo.apply(null, [, 6]);

function foo(obj) {
	obj = obj || {};

	obj.a = obj.a || 11;
	// ..
}
```

__ES6缺失参数赋值__
```js
function foo(x = 11, y = 31) {
    console.log( x + y );
}

foo();
foo( 5, 6 );
foo( 0, 42 );
foo( 5 );
foo( 5, undefined );
foo( 5, null );
foo( undefined, 6 );
foo( null, 6 );
```

#### 表达式默认值

__函数默认值除了是简单值，可以是任意合法表达式，甚至是函数调用。__

```js
function bar(val) {
	console.log( "bar called!" );
	return y + val;
}

function foo(x = y + 3, z = bar( x )) {
	console.log(x, z);
}

var y = 5;
foo(); 	// bar called!
		// 8 13

foo( 10 );	// bar called!
			// 10 15

y = 6;
foo( undefined, 10 ); // 9 10
```

__默认值表达式中的`标识符`引用首先匹配到`形式参数作用域'(...)'`，然后才会搜索外层作用域。__

```js
// z已经声明，但未初始化
var w = 1, z = 2;
function foo(x = w + 1, y = x + 1, z = z + 1) {
	console.log(x, y, z);
}

foo(); // ReferenceError
```

默认回调函数

```js
function dialog(msg, cb = function() {}) {
	// ..
}

dialog('Hello');
```

### 4. 对象字面量扩展

#### 简洁属性

```js
var x = 2, y = 3;
var o = { x: x, y: y };
```

对象属性名与`词法标识符(变量)`同名，则可如下简写:

```js
var x = 2, y = 3;
var o = { x, y };
```

#### 简洁方法

```js
var o = {
	x: function() {},
	y: function() {}
};
```

```js
var o = {
	x() {},
	y() {}
};
```

#### 计算属性名

```js
var prefix = "user_";

var o = {
	baz: function(..) { .. }
};

o[prefix + "foo"] = function(..) { .. };
o[prefix + "bar"] = function(..) { .. };
o[prefix + "id"] = "9527";
```

```js
var prefix = "user_";

var o = {
	baz: function(..) { .. },
	[prefix + "foo"]: function(..) { .. },
	[prefix + "bar"]: function(..) { .. },
	[prefix + "id"]: "9527"
};
```

此外还有`[[Prototype]]`的设定，`super`对象。

### 5. 解构

**解构(destructuring)**可以看作是一个**结构化赋值(structured assignment)**方法。

曾经的手动赋值：
```js
// 数组
function foo() {
	return [1, 2, 3];
}

// 对象
function bar() {
	return {
		x: 4,
		y: 5,
		z: 6
	};
}

var tmp = foo(), // 临时变量
	a = tmp[0], b = tmp[1], c = tmp[2];

console.log(a, b, c); // 1 2 3

var obj = bar(), // 临时变量
	x = obj.x, y = obj.y, z = obj.z;

console.log(x, y, z); // 4 5 6
```

ES6新增了一个专门语法，专用于**数组解构**和**对象解构**。这个语法消除了前面代码对临时变量的需求，使代码更加简洁。

```js
// 数组解构
var [a, b, c] = foo();
console.log(a, b, c); // 1 2 3

// 对象解构
var { x: x, y: y, z: z } = bar();
console.log(x, y, z); // 4 5 6
```

#### 对象属性赋值模式

如果属性名和要赋值的变量名相同，可以使用简洁属性使得语法更加简短。

```js
var { x: x, y: y, z: z } = bar();

// 简洁属性
var { x, y, z } = bar();
```

__对于`{ x, .. }`，缩写语法省略了`x:`部分。__

简短的形式使得代码更简洁，但是更长的的形式支持把属性赋给非同名变量：
```js
var { x: bam, y: baz, z: bap } = bar();

console.log(bam, baz, bap); // 4 5 6
console.log(x, y, z); // ReferenceError
```

语法模式比较:

- 对象字面值：`target: source`(`target <-- source`)
- 对象解构赋值：`source: target`(`source --> target`)

```js
var aa = 10, bb = 20;

var o = { x: aa, y: bb };
var     { x: AA, y: BB } = o;

console.log(AA, BB); // 10 20
```

#### 不只是声明

解构是一个通用的赋值操作，不仅仅是声明。

```js
var a, b, c, x, y, z;

[a, b, c] = foo();
( { x, y, z } = bar() ); // 省略了声明符，必须把整个赋值表达式用(..)括起来

console.log(a, b, c); // 1 2 3
console.log(x, y, z); // 4 5 6
```

任何合法的赋值表达式都可以用解构赋值。

```js
var o = {};

[o.a, o.b, o.c] = foo();
( { x: o.x, y: o.y, z: o.z } = bar() );

console.info(o);
```

使用计算属性：
```js
var which = 'x',
	o = {};

( { [which]: o[which] } = bar() );

console.log(o);
```

不用临时变量**交换两个变量**：
```js
var x = 10, y = 20;

[y, x] = [x, y];

console.log(x, y); // 20 10
```

#### 重复赋值

```js
var { a: X, a: Y } = { a: 1 };

console.log(X, Y); // 1 1
```

解构子对象/数组属性：

```js
var { a: { x: X, x: Y }, a } = { a: { x: 1 } };

console.log(X, Y);
console.log(a);

( { a: X, a: Y, a: [Z] } = { a: [1] } );

X.push(2);
Y[0] = 10;

console.log(X); // [10, 2]
console.log(Y); // [10, 2]
console.log(Z); // 1
```

### 6. 模板字面量

模板字面量更应该被称为**插入字符串字面量**。

ES6之前的字符串连接方式：
```js
var name = "Kyle";

var greeting = "Hello " + name + "!";

console.log(greeting); // Hello Kyle!
console.log(typeof greeting); // string
```

ES6中的方式：
```js
var name = "Kyle";

var greeting = `Hello ${name}!`;

console.log(greeting); // Hello Kyle!
console.log(typeof greeting); // string
```

插入字符串字面量的一个优点是它们可以分散在多行:
```js
var text =
     `Now is the time for all good men
     to come to the aid of their
     country!`;

console.log( text );
```

#### 插入表达式

在插入字符串字面量的`${..}`内可以出现任何合法的表达式，包括函数调用、在线函数表
达式调用，甚至其他插入字符串字面量!

```js
function upper(s) {
	return s.toUpperCase();
}

var who = "reader";
var text =
	`A very ${upper( "warm" )} welcome
	to all of you ${upper( `${who}s` )}!`;

console.log( text );
```

#### 标签模板字面量

```js
function foo(strings, ...values) {
	console.log( strings );
	console.log( values );
}

var desc = "awesome";

foo`Everything is ${desc}!`; 	// ["Everything is ", "!"]
								// ["awesome"]
```

### 7. 箭头函数

普通函数和箭头函数对比：
```js
function foo(x, y) {
	return x + y;
}

// 对比

var foo = (x, y) => x + y;
```

不同形式的箭头函数：
```js
var f1 = () => 12;
var f2 = x => x * 2;
var f3 = (x, y) => {
	var z = x * 2 + y;
	y++;
	x *= 3;
	return (x + y + z) / 2;
};
```

箭头函数特点：

- 标识`=>`前面是参数，后面是函数体
- 零个或多个参数，需要用`( .. )`括起来
- 函数体的表达式多余1个，或者函数体包含非表达式语句的时候需要用`{ .. }`括起来
- 如果只有一个表达式，并且省略了`{ .. }`，则意味着表达式前面有一个隐含的`return`

箭头函数不仅仅是更短的语法，而是`this`。在箭头函数内部，`this`绑定不是动态的，而是词法的。

```js
function foo() {
	// 返回一个箭头函数
	return (a) => {
		// this继承自foo()
		console.log(this.a);
	};
}

var obj1 = {
	a: 2
};

var obj2 = {
	a: 3
};

var bar = foo.call(obj1);
bar.call(obj2); // 2， 不是3
```

箭头函数最常用于回调函数中，例如事件处理器或定时器：
```js
function foo() {
	setTimeout(() => {
		console.log(this.a);
	});
}

var obj = {
	a: 2
};

foo.call(obj); // 2
```

曾经使用的一种几乎和箭头函数完全一样的模式：
```js
function foo() {
	var self = this;

	setTimeout(function() {
		console.log(self.a);
	});
}

var obj = {
	a: 2
};

foo.call(obj); // 2
```

### 8. for..of循环

在`for`和`for..in`循环组合起来的基础上，ES6又新增了一个`for..of`循环，在**迭代器**产生的一系列值上循环。

`for..of`循环的值必须是一个`iterable`，或者可以转换/封箱到一个`iterable`对象的值。

```js
var a = ["a", "b", "c", "d", "e"];

for (var idx in a) {
	console.log(idx); // 0 1 2 3 4
}

for (var val of a) {
	console.log(val); // a b c d e
}

var b = { x: 1, y: 2, z: 3 };

for (var idx in b) {
	console.log(idx);
}

// TypeError: b is not iterable
for (var val of b) {
	console.log(val);
}
```

JavaScript中默认为`iterable`的标准内建值包括：

- Arrays
- Strings
- Generators
- Collections / TypedArrays

在原生字符串的字符上迭代：

```js
for (var c of "hello") {
	console.log(c); // h e l l o
}
```

原生字符串值"hello"被强制类型转换/封箱到邓建的`String`封装对象中。

### 9. 正则表达式

### 10. 数字字面量

### 11. Unicode

### 12. Symbol

## 代码组织

### 1. 迭代器

### 2. 生成器

### 3. 模块

### 4. 类

## 异步流控制

### 1. Promise

```js
function ajax(url, cb) {
	// 建立请求，最终调用cb
}

ajax("http://some.url.1", function handler(err, contents) {
	if (err) {
		// 处理错误
	} else {
		// 处理contents
	}
});
```

```js
function ajax(url) {
	return new Promise(function pr(resolve, reject) {
		// 建立请求，最终调用resolve(..)或reject(..)
	});
}

ajax("http://some.url.1").then(
	function fulfilled(contents) {
		// 处理contents
	},
	function rejected(reason) {
		// 处理错误原因
	}
);
```

### 2. 生成器 + Promise
