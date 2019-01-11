apply、call、bind、new这几个概念，可以说是前端面试中的必考点。为何必考？因为这几个概念涉及到JS中的多个核心知识。本文从基础到实现，深入地介绍这几个核心知识，包括主要概念、调用示例，以及对应的模拟实现。
# call
一句话介绍call:

```
call() 方法在使用一个指定的 this 值和若干个指定的参数值的前提下调用某个函数或方法。

```
语法：

```
fun.call(thisArg[, arg1[, arg2[, ...]]])
```
## 调用示例
call() 方法在使用一个指定的 this 值和若干个指定的参数值的前提下调用某个函数或方法。


```
var foo = {
    name: 'aa'
};
var name = 'bb';
function bar(age) {
	console.log(this.name);
	console.log(age);
}

bar(); // bb undefined
bar.call(null); // bb undefined
bar.call(foo, 21); // aa 21
```
这里有几个关键点：
1. call 改变了 this 的指向，指向到 foo；
2. call 函数还能给定参数执行函数；
3. 当传入参数为null时，视为指向 window；    
## 模拟实现
### 基础版本
试想当调用 call 的时候，把 foo 对象改造成如下：

```
var foo = {
    name: 'aa',
    bar: function() {
        console.log(this.name)
    }
};

foo.bar(); // aa
```
其本质，就是将函数绑定到对象上。所以我们模拟的步骤可以分为：    
1，将函数设为对象的属性    
2，执行该函数    
3，删除该函数

```
// 第一步
foo.fn = bar
// 第二步
foo.fn()
// 第三步
delete foo.fn
```
根据这个思路，我们可以写出第一个简单的模拟实现版本：

```
// 第一版
Function.prototype.call2 = function(context) {
    // 首先要获取调用call的函数，用this可以获取
    context.fn = this;
    context.fn();
    delete context.fn;
}

// 测试一下
var foo = {
    name: 'aa'
};
var name = 'bb';
function bar() {
    console.log(this.name);
}

bar.call2(foo); // aa
```
### 模拟传参版本
接下来，我们实现模拟传入参数的版本：

```
// 第二版
Function.prototype.call2 = function(context) {
    context.fn = this;
    var args = Array.prototype.slice.call(arguments, 1);
    context.fn(...args);
    delete context.fn;
}
var foo = {
    name: 'aa'
};
function bar(age) {
	console.log(this.name);
	console.log(age);
}
bar.call2(foo, 21); // aa 21
```
在这里，我们利用Array.prototype.slice.call将 call里arguments的函数取出来，作为bar函数的参数。
### 终极版本
终极版本一方面解决传入null的问题，另一方面也加入了函数返回值功能：

```
// 最终版
Function.prototype.call2 = function(context) {
    var context = context || window;
    context.fn = this;

    var args = Array.prototype.slice.call(arguments, 1);
    let result = context.fn(...args);
    delete context.fn;
    return result;
}
var foo = {
    name: 'aa'
};
function bar(age) {
	console.log(this.name);
	console.log(age);
	return `name:${this.name},age:${age}`
}
bar.call2(null); // bb undefine
let fnRes = bar.call2(foo, 21); // aa 21
console.log(fnRes) // name:aa,age:21
```
# apply
apply 和 call 基本类似，他们的区别只是传入的参数不同。     

在 MDN 中定义 apply 如下:

```
apply() 方法调用一个函数, 其具有一个指定的this值，以及作为一个数组（或类似数组的对象）提供的参数
```
语法：

```
fun.apply(thisArg, [argsArray])
```
所以 apply 和 call 的区别是 call 方法接受的是若干个参数列表，而 apply 接收的是一个包含多个参数的数组。
## 调用示例
类似call，我们的调用示例如下：

```
var foo = {
    name: 'aa'
};
var name = 'bb';
function bar(age) {
	console.log(this.name);
	console.log(age);
}

bar(); // bb undefined
bar.apply(null); // bb undefined
bar.apply(foo, [21]); // aa 21
```
可以看看，除了传参的区别，其它基本上都是一致的。

## 模拟实现
apply的实现可以参考call，主要是入参的不同：

```
// 最终版
Function.prototype.apply2 = function(context, arr) {
    var context = context || window;
    context.fn = this;

    var result;
    if (!arr) {
        result = context.fn();
    } else {
    	var args = Array.prototype.slice.call(arr, 0);
    	result = context.fn(...args);
    }

    delete context.fn;
    return result;
}
var foo = {
    name: 'aa'
};
var name = 'bb'
function bar(age, sex) {
	console.log(this.name);
	console.log(age);
	console.log(sex);
	return `name:${this.name},age:${age},sex:${sex}`
}
bar.apply2(null); // bb undefine undefine
let fnRes = bar.apply2(foo, [21, 'g']); // aa 21 g
console.log(fnRes) // name:aa,age:21,sex:g
```

# bind
一句话介绍bind:

```
bind() 方法会创建一个新函数。当这个新函数被调用时，bind() 的第一个参数将作为它运行时的 this，之后的一序列参数将会在传递的实参前传入作为它的参数。
```
## 调用示例
bind除了绑定对象之外，还可以传入额外的参数：
```
var foo = {
    name: 'bb'
};
var name = 'aa'
function bar(age) {
    console.log(this.name);
    console.log(age);
}
// 返回了一个函数
bar(); // aa undefined
var bindFoo = bar.bind(foo, 18); 
bindFoo(); // bb 18
```

## 模拟实现
### 初级版本

```
Function.prototype.bind2 = function (context) {

    var self = this;
    // 获取bind2函数从第二个参数到最后一个参数
    var args = Array.prototype.slice.call(arguments, 1);

    return function () {
        // 这个时候的arguments是指bind返回的函数传入的参数
        var bindArgs = Array.prototype.slice.call(arguments);
        self.apply(context, args.concat(bindArgs));
    }

}

var foo = {
    name: 'bb'
};
var name = 'aa'
function bar(age, sex) {
    console.log(this.name);
    console.log(age);
    console.log(sex);
}
// 返回了一个函数
var bindFoo = bar.bind2(foo, 18); 
bindFoo('girl'); // bb 18 girl

```
### 终极版本
终极版本考虑了prototype，以及构造函数new（参见下一个主题）的问题

```
Function.prototype.bind2 = function (context) {

    var self = this;
    var args = Array.prototype.slice.call(arguments, 1);

    var fNOP = function () {};

    var fbound = function () {
        var bindArgs = Array.prototype.slice.call(arguments);
        self.apply(this instanceof self ? this : context, args.concat(bindArgs));
    }
    fNOP.prototype = this.prototype;
    fbound.prototype = new fNOP();
    return fbound;

}
```


# new
一句话介绍 new:

```
new 运算符创建一个用户定义的对象类型的实例或具有构造函数的内置对象类型之一
```
## 调用示例

```
function Foo (name, age) {
    this.name = name;
    this.age = age;
}
Foo.prototype.sex = 'girl';

Foo.prototype.sayYourName = function () {
    console.log('I am ' + this.name);
}

var person = new Foo('xiaoshu', '18');

console.log(person.name) // xiaoshu
console.log(person.age) // 18
console.log(person.sex) // girl

person.sayYourName(); // I am xiaoshu
```
从这个例子中，可以看出实例既可以访问构造函数中的属性，也可以访问prototype的属性。
## 模拟实现
因为 new 是关键字，所以无法像 bind 函数一样直接覆盖，所以我们写一个函数，命名为 objectFactory，来模拟 new 的效果:

```
function Foo () {
    ……
}

// 使用 new
var person = new Foo(……);
// 使用 objectFactory
var person = objectFactory(Foo, ……)
```
### 初级版本

```
function objectFactory() {

    var obj = new Object(),

    Constructor = [].shift.call(arguments);

    obj.__proto__ = Constructor.prototype;

    Constructor.apply(obj, arguments);

    return obj;

};
```
流程具体是：
1. 用new Object() 的方式新建了一个对象 obj
2. 取出第一个参数，就是我们要传入的构造函数。此外因为 shift 会修改原数组，所以 arguments 会被去除第一个参数
3. 将 obj 的原型指向构造函数，这样 obj 就可以访问到构造函数原型中的属性
4. 使用 apply，改变构造函数 this 的指向到新建的对象，这样 obj 就可以访问到构造函数中的属性
5. 返回 obj
   
    
我们再看一下效果：

```
function Foo (name, age) {
    this.name = name;
    this.age = age;
}
Foo.prototype.sex = 'girl';

Foo.prototype.sayYourName = function () {
    console.log('I am ' + this.name);
}

function objectFactory() {
    var obj = new Object(),
    Constructor = [].shift.call(arguments);
    obj.__proto__ = Constructor.prototype;
    Constructor.apply(obj, arguments);
    return obj;
};

var person = objectFactory(Foo, 'xiaoshu', '18');

console.log(person.name) // xiaoshu
console.log(person.age) // 18
console.log(person.sex) // girl

person.sayYourName(); // I am xiaoshu

```

### 终极版本
终版还需要判断返回的值是不是一个对象，如果是一个对象，我们就返回这个对象，如果没有，我们该返回什么就返回什么。

```
function objectFactory() {

    var obj = new Object(),

    Constructor = [].shift.call(arguments);

    obj.__proto__ = Constructor.prototype;

    var ret = Constructor.apply(obj, arguments);

    return typeof ret === 'object' ? ret : obj;

};
```


> 参考文档：    
this、apply、call、bind ：https://juejin.im/post/59bfe84351882531b730bac2         
JavaScript深入之call和apply的模拟实现：https://juejin.im/post/5907eb99570c3500582ca23c    
JavaScript深入之bind的模拟实现：https://juejin.im/post/59093b1fa0bb9f006517b906    
JavaScript深入之new的模拟实现：https://juejin.im/post/590a99015c497d005852cf26