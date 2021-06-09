---
title: Js原型链解读
date: 2017-11-06 21:41:43
tags : 前端
categories : 前端
---
### 前言
在JavaScript中没"子类”和“父类”的概念，进一步地也没有“类”和“实例”的的区分。它靠一种看上去十分古怪的”原型链“（prototype chain）模式来实现继承。学过JAVA等编程语言的人可能会认为这是对Java等语言的继承实现方式的一种拙劣并且失败的模仿，然而事实并非如此，原型链模式和我们常见的Class模式分别是两种编程范式prototype_base和class_base的实现，前者在动态语言中似乎十分常见，而后者主要在静态语言领域流行。下面是维基百科关于prototype_base模式的介绍：

> Prototype-based programming is a style of object-oriented programming in which behaviour reuse (known as inheritance) is performed via a process of reusing existing objects via delegation that serve as prototypes. This model can also be known as prototypal, prototype-oriented, classless, or instance-based programming. Delegation is the language feature that supports prototype-based programming.
> Prototype object oriented programming uses generalized objects, which can then be cloned and extended. Using fruit as an example, a "fruit" object would represent the properties and functionality of fruit in general. A "banana" object would be cloned from the "fruit" object, and would also be extended to include general properties specific to bananas. Each individual "banana" object would be cloned from the generic "banana" object. Compare to the class-based paradigm, where a "fruit" class (not object) would be extended by a "banana" class

[维基原文](https://en.wikipedia.org/wiki/Prototype-based_programming)
<!--more-->

### 如何理解原型链
我们以一个名字叫`Foo(）`的函数为例。我们定义：
```
function Foo(){


}
```
然后再`var f1 = new Foo()`,`var f2 = new Foo()`，这期间都有什么事情发生呢？我们通过一张图来看一下：
![prototype.jpg](http://oo3aq3ac8.bkt.clouddn.com/proto.jpg)

先介绍两个概念：`_proto_`和`prototype`:
+ `_proto_`:引用《JavaScript权威指南》中的说明：
> Every JavaScript object has a second JavaScript object (or null ,
> but this is rare) associated with it. This second object is known as a prototype, and the first 
> object inherits properties from the prototype.
就是说就是每个JS对象一定对应一个原型对象，并从原型对象继承属性和方法。既然有这么一个原型对象，那么对象怎么和它对应的？如何描述这种对应关系？答案就是通过`_proto_`,对象`__proto__`属性的值就是它所对应的原型对象。

+ `prototype`: 与`_proto_`不同,`prototype`是函数才有的属性。当你创建函数时，JS会为这个函数自动添加`prototype`属性，值是空对象。而一旦你把这个函数当作构造函数（`constructor`）调用（即通过`new`关键字调用），那么JS就会帮你创建该构造函数的实例，实例继承构造函数`prototype`的所有属性和方法（实例通过设置自己的`__proto__`指向承构造函数的`prototype`来实现这种继承）。它的存在主要是为了存放共享的数据和实现继承。


&ensp;&ensp;下面结合上面的图示来分析，我们可以看到`function Foo()`对应一个`Foo.prototype`的原型，那么`function Foo`和`Foo.prototype`之间的关系是什么尼？
&ensp;&ensp;图里其实已经展示得很清楚了，`functon Foo()`是`Foo.prototype`的构造函数，`Foo.prototype`是`function Foo()`的原型实例。当我们使用`new`关键字创建`var f1 = new Foo()`,`var f2 = new Foo()`后，`f1、f2`中会有一个`_proto_`字段指向`Foo.prototype`,这种`xxx._proto_._proto....`的指向就代表了原型链的结构（应该是个森林）。同时每个函数`function xxx()`其实都是通过`function Function()`来创造的，所以`function Foo()`的`_proto_`应该指向`Function.prototype`,并且`function Function()`自身的`_proto_`也指向`Function.prototype`。
&ensp;&ensp;事实上，所有`function xxx()`的`_proto_`最终都会指向`Function.prototype`，而所有的`xxx.prototype`最后都会指向`Object.prototype`,最终指向`null`。关于`function Object()`这个函数其实有点像Java中的Object对象，所有原型都会继承自它的原型。这里有个有意思的问题，`function Function()`也是个函数，所以`function Function()`的`_proto_`属性的值为`Function.prototype`，这也就意味着它自己创造了自己。这样的结果就是`function Object()._proto_ = Function.prototype`、而`function Function()._proto._proto_ = Object.prototype`,即`Object instanceof  Function == true`、`Function instanceof  Object == true`翻译过来就是`Object`是`Function`的实例，`Function`是`Object`的实例，这是一种类似先有鸡还是先有蛋的蜜汁尴尬局面。

总结：
+ 所有对象的`_proto_`字段都指向创建它的构造函数的原型， 最终都指向`Object.prototype`,类似`xxx.prototype._proto_._proto_..._proto_ = Object.prototype = null`就是原型链。
+ 所有函数都由`function Function()`创建，所以所有函数的(包括它本身）`_proto_`字段都会指向`Function.prototype`，最后才指向`Object.prototype`。

### 使用原型链实现继承
定义父函数：
```js
function Father() {
    this.age = "56"; }

Father.prototype.say = function () {
    alert("my age is "+this.age); 
} 
```
定义子函数：
```js
function Son() {
    this.age = '26';
  this.play = "football"; }

Son.prototype.play = function () {
    alert("I like play "+this.play);
 }
```
实现继承后的原型链应该是：`Son.prototype._proto_ = Father.prototype`
实现方式：借用第三个函数过渡
```js
function extends(Child,Father){
	var F = function(){};
	F.prototype = Father.prototype;
	//Child.prototype._proto_ = F.prototype = Father.prototype
	Child.prototype = new F();
	//原本Child.prototype.constructor = F,修改为Child
	Child.prototype.constructor = Child;
	
}

```
测试验证：`Son`的实例可以调用`say()`则说明继承成功。
```js
	function Father() {
    this.age = "56"; }

	Father.prototype.say = function () {
    alert("my age is "+this.age); }

	function Son() {
    this.age = '26';
  	this.play = "football"; }

	Son.prototype.play = function () {
    alert("I like play "+this.play); }

	function excents(Child,Father) {

    var F = function () {}
    F.prototype = Father.prototype;
  	Child.prototype = new F();
  	Child.prototype.constructor = Child;   }

	excents(Son,Father);  

 	var son = new Son(); 
	son.say();

```

运行结果：
![](http://oo3aq3ac8.bkt.clouddn.com/%E5%BC%B9%E7%AA%97)

继承成功！






