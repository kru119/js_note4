# 构造函数
除了创建对象，构造函数(constructor) 还做了另一件有用的事情—自动为创建的新对象设置了原型对象(prototype object) 。原型对象存放于 ConstructorFunction.prototype 属性中。

例如，我们重写之前例子，使用构造函数创建对象“b”和“c”，那么对象”a”则扮演了“Foo.prototype”这个角色：
```
// 构造函数
function Foo(y) {
  // 构造函数将会以特定模式创建对象：被创建的对象都会有"y"属性
  this.y = y;
}
 
// "Foo.prototype"存放了新建对象的原型引用
// 所以我们可以将之用于定义继承和共享属性或方法
// 所以，和上例一样，我们有了如下代码：
 
// 继承属性"x"
Foo.prototype.x = 10;
 
// 继承方法"calculate"
Foo.prototype.calculate = function (z) {
  return this.x + this.y + z;
};
 
// 使用foo模式创建 "b" and "c"
var b = new Foo(20);
var c = new Foo(30);
 
// 调用继承的方法
b.calculate(30); // 60
c.calculate(40); // 80
 
// 让我们看看是否使用了预期的属性
 
console.log(
 
  b.__proto__ === Foo.prototype, // true
  c.__proto__ === Foo.prototype, // true
 
  // "Foo.prototype"自动创建了一个特殊的属性"constructor"
  // 指向a的构造函数本身
  // 实例"b"和"c"可以通过授权找到它并用以检测自己的构造函数
 
  b.constructor === Foo, // true
  c.constructor === Foo, // true
  Foo.prototype.constructor === Foo // true
 
  b.calculate === b.__proto__.calculate, // true
  b.__proto__.calculate === Foo.prototype.calculate // true
 
);
```
![](https://pic3.zhimg.com/58f2b9242ceddea0462689a4b71486ee_r.jpg)
![](http://www.nowamagic.net/librarys/images/201203/2012_03_21_03.png)

上述图示可以看出，每一个object都有一个prototype. 构造函数Foo也拥有自己的__proto__, 也就是Function.prototype, 而Function.prototype的__proto__指向了Object.prototype. 重申一遍，Foo.prototype只是一个显式的属性，也就是b和c的__proto__属性。
prototype是“类”的原型，__proto__是对象的原型。  JS当然没有“类”，只有constructor。  constructor就是当你new fn()时的那个“fn”。  当你new fn的时候，产生的实例的__proto__指向fn.prototype，两者是同一个东西。 
```
function Foo() {} 
var myfoo = new Foo(); 
myfoo.__proto__ === Foo.prototype
```
constructor是什么

简单的理解,constructor指的就是对象的构造函数。请看如下示例：
```
 
function Foo(){}; 
var foo = new Foo(); 
alert(foo.constructor);//Foo 
alert(Foo.constructor);//Function 
alert(Object.constructor);//Function 
alert(Function.constructor);//Function 
```
对于foo.constructor为Foo,我想应该很好理解，因为foo的构造函数为Foo。对于Foo、Object、Function的构造函数为Function,我想也没什么好争议的。（因为Foo,Object,Function都是函数对象，又因为所有的函数对象都是Function这个函数对象构造出来,所以它们的constructor为Function,详细请参考《js_函数对象》)
### Prototype与Constructor的关系
```
 
function Dog(){} 
alert(Dog === Dog.prototype.constructor);//true 
```
在 JavaScript 中，每个函数都有名为“prototype”的属性，用于引用原型对象。此原型对象又有名为“constructor”的属性，它反过来引用函数本身。这是一种循环引用，如图：
!(https://www.jb51.net/upload/201010/20101018004308867.gif)
constructor属性来自何方
我们来看一下Function构造String的构造过程：
!(https://www.jb51.net/upload/201010/20101018004330346.png)
注：Function构造任何函数对象的过程都是一样的，所以说不管是String,Boolean,Number等内置对象，还是用户自定义对象,其构造过程都和上图一样。这里String只是一个代表而矣！
图中可以看出constructor是Function在创建函数对象时产生的，也正如'prototype与constructor的关系'中讲的那样，constructor是函数对象prototype链中的一个属性。即String=== String.prototype.constructor。
```
 
function Person(){} 
var p = new Person(); 
alert(p.constructor);//Person 
alert(Person.prototype.constructor);//Person 
alert(Person.prototype.hasOwnProperty('constructor'));//true 
alert(Person.prototype.isPrototypeOf(p));//true 
alert(Object.prototype.isPrototypeOf(p));//true 
alert(Person.prototype == Object.prototype);//false 

```
```
 
function Animal(){} 
function Person(){} 
var person = new Person(); 
alert(person.constructor); //Person 

```
内存表示如图：
!(https://www.jb51.net/upload/201010/20101018004602580.png)
```
 
function Animal(){} 
function Person(){} 
Person.prototype = new Animal(); 
var person = new Person(); 
alert(person.constructor); //Animal 
```
这个时候，person的构造函数成了Animal，怎么解释？
!(https://www.jb51.net/upload/201010/20101018004632634.png)
注：图中的虚线表示Person默认的prototype指向(只作参考的作用)。但是我们将Person.prototype指向了new Animal。
此时，Person的prototype指向的是Animal的实例，所以person的constructor为Animal这个构造函数。

结论：constructor的原理非常简单，就是在对象的原型链上寻找constructor属性。
