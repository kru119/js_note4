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
# Javascript中this关键字详解
```
var name = "Bob";
var nameObj ={
    name : "Tom",
    showName : function(){
        alert(this.name);
    },
    waitShowName : function(){
        setTimeout(this.showName, 1000);
    }
};

nameObj.waitShowName();
```
一般而言，在Javascript中，this指向函数执行时的当前对象。

值得注意，该关键字在Javascript中和执行环境，而非声明环境有关。

我们举个例子来说明这个问题：
```
var someone = {
    name: "Bob",
    showName: function(){
        alert(this.name);
    }
};

var other = {
    name: "Tom",
    showName: someone.showName
}

other.showName();　　//Tom
```
this关键字虽然是在someone.showName中声明的，但运行的时候是other.showName，所以this指向other.showName函数的当前对象，即other，故最后alert出来的是other.name。

当没有明确的执行时的当前对象时，this指向全局对象window。

例如对于全局变量引用的函数上我们有：

```
var name = "Tom";

var Bob = {
    name: "Bob",
    show: function(){
        alert(this.name);
    }
}

var show = Bob.show;
show();　　//Tom
```
你可能也能理解成show是window对象下的方法，所以执行时的当前对象时window。但局部变量引用的函数上，却无法这么解释：
```
var name = "window";

var Bob = {
    name: "Bob",
    showName: function(){
        alert(this.name);
    }
};

var Tom = {
    name: "Tom",
    showName: function(){
        var fun = Bob.showName;
        fun();
    }
};

Tom.showName();　　//window
```
### setTimeout、setInterval和匿名函数

文章开头的问题的答案是Bob。

在浏览器中setTimeout、setInterval和匿名函数执行时的当前对象是全局对象window，这条我们可以看成是上一条的一个特殊情况。

所以在运行this.showName的时候，this指向了window，所以最后显示了window.name。浏览器中全局变量可以当成是window对象下的变量，例如全局变量a，可以用window.a来引用。

我们将代码改成匿名函数可能更好理解一些：
```
var name = "Bob";  
 var nameObj ={  
     name : "Tom",  
     showName : function(){  
         alert(this.name);  
     },  
     waitShowName : function(){  
         !function(__callback){
            __callback();
        }(this.showName);  
     }  
 };  
 
 nameObj.waitShowName();　　//Bob
 ```
 在调用nameObj.waitShowName时候，我们运行了一个匿名函数，将nameObj.showName作为回调函数传进这个匿名函数，然后匿名函数运行时，运行这个回调函数。由于匿名函数的当前对象是window，所以当在该匿名函数中运行回调函数时，回调函数的this指向了window，所以alert出来window.name。

由此看来setTimeout可以看做是一个延迟执行的：
```
function(__callback){
    __callback();
}
```
setInterval也如此类比。

但如果我们的确想得到的回答是Tom呢？通过一些技巧，我们能够得到想要的答案：
```
var name = "Bob";  
var nameObj ={  
    name : "Tom",  
    showName : function(){  
        alert(this.name);  
    },  
    waitShowName : function(){
        var that = this;
        setTimeout(function(){
            that.showName();
        }, 1000);
    }
}; 
 
 nameObj.waitShowName();　　//Tom
 ```
 在执行nameObj.waitShowName函数时，我们先对其this赋给变量that（这是为了避免setTimeout中的匿名函数运行时，匿名函数中的this指向window），然后延迟运行匿名函数，执行that.showName，即nameObj.showName，所以alert出正确结果Tom。
 ### eval

对于eval函数，其执行时候似乎没有指定当前对象，但实际上其this并非指向window，因为该函数执行时的作用域是当前作用域，即等同于在该行将里面的代码填进去。下面的例子说明了这个问题：
```
var name = "window";

var Bob = {
    name: "Bob",
    showName: function(){
        eval("alert(this.name)");
    }
};

Bob.showName();    //Bob
```
### apply和call

apply和call能够强制改变函数执行时的当前对象，让this指向其他对象。因为apply和call较为类似，所以我们以apply为例：
```
var name = "window";
    
var someone = {
    name: "Bob",
    showName: function(){
        alert(this.name);
    }
};

var other = {
    name: "Tom"
};    

someone.showName.apply();    //window
someone.showName.apply(other);    //Tom
```
apply用于改变函数执行时的当前对象，当无参数时，当前对象为window，有参数时当前对象为该参数。于是这个例子Bob成功偷走了Tom的名字。
### new关键字

new关键字后的构造函数中的this指向用该构造函数构造出来的新对象：
```
function Person(__name){
    this.name = __name;        //这个this指向用该构造函数构造的新对象，这个例子是Bob对象
}
Person.prototype.show = function(){
    alert(this.name);
}

var Bob = new Person("Bob");
Bob.show();        //Bob
```
### this的四种方法
this的值是什么呢？

函数的不同使用场合，this有不同的值。总的来说，this就是函数运行时所在的环境对象。下面分四种情况，详细讨论this的用法。
#### 情况一：纯粹的函数调用

这是函数的最通常用法，属于全局性调用，因此this就代表全局对象。请看下面这段代码，它的运行结果是1。
```

var x = 1;
function test() {
   console.log(this.x);
}
test();  // 1
```
函数也可以直接被调用，此时 this 绑定到全局对象。在浏览器中，window 就是该全局对象。比如下面的例子：函数被调用时，this 被绑定到全局对象，接下来执行赋值语句，相当于隐式的声明了一个全局变量，这显然不是调用者希望的。
```
function makeNoSense(x) { 
this.x = x; 
} 
makeNoSense(5); 
x;// x 已经成为一个值为 5 的全局变量
```
对于内部函数，即声明在另外一个函数体内的函数，这种绑定到全局对象的方式会产生另外一个问题。我们仍然以前面提到的 point 对象为例，这次我们希望在 moveTo 方法内定义两个函数，分别将 x，y 坐标进行平移。结果可能出乎大家意料，不仅 point 对象没有移动，反而多出两个全局变量 x，y。
```
var point = { 
x : 0, 
y : 0, 
moveTo : function(x, y) { 
    // 内部函数
    var moveX = function(x) { 
    this.x = x;//this 绑定到了哪里？
   }; 
   // 内部函数
   var moveY = function(y) { 
   this.y = y;//this 绑定到了哪里？
   }; 
 
   moveX(x); 
   moveY(y); 
   } 
}; 
point.moveTo(1, 1); 
point.x; //==>0 
point.y; //==>0 
x; //==>1 
y; //==>1
```
这属于 JavaScript 的设计缺陷，正确的设计方式是内部函数的 this 应该绑定到其外层函数对应的对象上，为了规避这一设计缺陷，聪明的 JavaScript 程序员想出了变量替代的方法，约定俗成，该变量一般被命名为 that。
```
var point = { 
x : 0, 
y : 0, 
moveTo : function(x, y) { 
     var that = this; 
    // 内部函数
    var moveX = function(x) { 
    that.x = x; 
    }; 
    // 内部函数
    var moveY = function(y) { 
    that.y = y; 
    } 
    moveX(x); 
    moveY(y); 
    } 
}; 
point.moveTo(1, 1); 
point.x; //==>1 
point.y; //==>1
```
#### 情况二：作为对象方法的调用

函数还可以作为某个对象的方法调用，这时this就指这个上级对象。
```
function test() {
  console.log(this.x);
}

var obj = {};
obj.x = 1;
obj.m = test;

obj.m(); // 1
```
#### 情况三 作为构造函数调用

所谓构造函数，就是通过这个函数，可以生成一个新对象。这时，this就指这个新对象。

JavaScript 支持面向对象式编程，与主流的面向对象式编程语言不同，JavaScript 并没有类（class）的概念，而是使用基于原型（prototype）的继承方式。相应的，JavaScript 中的构造函数也很特殊，如果不使用 new 调用，则和普通函数一样。作为又一项约定俗成的准则，构造函数以大写字母开头，提醒调用者使用正确的方式调用。如果调用正确，this 绑定到新创建的对象上。
```
function test() {
　this.x = 1;
}

var obj = new test();
obj.x // 1
```
运行结果为1。为了表明这时this不是全局对象，我们对代码做一些改变：

```
var x = 2;
function test() {
  this.x = 1;
}

var obj = new test();
x  // 2
```
运行结果为2，表明全局变量x的值根本没变。
#### 情况四 apply 调用
apply()是函数的一个方法，作用是改变函数的调用对象。它的第一个参数就表示改变后的调用这个函数的对象。因此，这时this指的就是这第一个参数。
```
var x = 0;
function test() {
　console.log(this.x);
}

var obj = {};
obj.x = 1;
obj.m = test;
obj.m.apply() // 0

apply()的参数为空时，默认调用全局对象。因此，这时的运行结果为0，证明this指的是全局对象。
```
如果把最后一行代码修改为
```
obj.m.apply(obj); //1
```
运行结果就变成了1，证明了这时this代表的是对象obj
```
function Point(x, y){ 
   this.x = x; 
   this.y = y; 
   this.moveTo = function(x, y){ 
       this.x = x; 
       this.y = y; 
   } 
} 
 
var p1 = new Point(0, 0); 
var p2 = {x: 0, y: 0}; 
p1.moveTo(1, 1); 
p1.moveTo.apply(p2, [10, 10]);
```
在上面的例子中，我们使用构造函数生成了一个对象 p1，该对象同时具有 moveTo 方法；使用对象字面量创建了另一个对象 p2，我们看到使用 apply 可以将 p1 的方法应用到 p2 上，这时候 this 也被绑定到对象 p2 上。另一个方法 call 也具备同样功能，不同的是最后的参数不是作为一个数组统一传入，而是分开传入的。
### 函数的执行环境
JavaScript 中的函数既可以被当作普通函数执行，也可以作为对象的方法执行，这是导致 this 含义如此丰富的主要原因。一个函数被执行时，会创建一个执行环境（ExecutionContext），函数的所有的行为均发生在此执行环境中，构建该执行环境时，JavaScript 首先会创建 arguments变量，其中包含调用函数时传入的参数。接下来创建作用域链。然后初始化变量，首先初始化函数的形参表，值为 arguments变量中对应的值，如果 arguments变量中没有对应值，则该形参初始化为 undefined。如果该函数中含有内部函数，则初始化这些内部函数。如果没有，继续初始化该函数内定义的局部变量，需要注意的是此时这些变量初始化为 undefined，其赋值操作在执行环境（ExecutionContext）创建成功后，函数执行时才会执行，这点对于我们理解 JavaScript 中的变量作用域非常重要，鉴于篇幅，我们先不在这里讨论这个话题。最后为 this变量赋值，如前所述，会根据函数调用方式的不同，赋给 this全局对象，当前对象等。至此函数的执行环境（ExecutionContext）创建成功，函数开始逐行执行，所需变量均从之前构建好的执行环境（ExecutionContext）中读取。
### Function.bind
有了前面对于函数执行环境的描述，我们来看看 this 在 JavaScript 中经常被误用的一种情况：回调函数。JavaScript 支持函数式编程，函数属于一级对象，可以作为参数被传递。请看下面的例子 myObject.handler 作为回调函数，会在 onclick 事件被触发时调用，但此时，该函数已经在另外一个执行环境（ExecutionContext）中执行了，this 自然也不会绑定到 myObject 对象上。
```
button.onclick = myObject.handler;
```
这是 JavaScript 新手们经常犯的一个错误，为了避免这种错误，许多 JavaScript 框架都提供了手动绑定 this 的方法。比如 Dojo 就提供了 lang.hitch，该方法接受一个对象和函数作为参数，返回一个新函数，执行时 this 绑定到传入的对象上。使用 Dojo，可以将上面的例子改为：
```
button.onclick = lang.hitch(myObject, myObject.handler);
```
在新版的 JavaScript 中，已经提供了内置的 bind 方法供大家使用。




