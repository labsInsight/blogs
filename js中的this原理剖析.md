相信js开发的初学者，会对js中this指向，在各种应用场景中的不同绑定感到疑惑。本文详解下js中this在不同场景中的指向问题。   

**this的指向在函数定义的时候是确定不了的，只有函数执行的时候才能确定this到底指向谁，实际上this的最终指向的是那个调用它的对象。** 以上这句话，从原理上概括了this的指向。下面，我们分不同的场景来解释这句话。我们分析这些场景的时候，一定要牢记这句话。    
# this 的四种绑定规则
this的4种绑定规则分别是：默认绑定、隐式绑定、显示绑定、new 绑定。优先级从低到高。
## 默认绑定
1，默认绑定规则：this绑定给window：

```
function foo() { 
console.log(this.a);
}    
var a = 1; 
foo(); // 1
```
2，在严格模式下（strict mode），全局对象将无法使用默认绑定，即执行会报undefined的错误：

```
function foo() { 
"use strict";
console.log(this.a);
}    
var a = 1; 
foo(); // TypeError: Cannot read property 'a' of undefined
```
3，但是，这里的严格模式仅限于foo()本身的运行， foo()的调用是不受影响的：

```
function foo() { 
console.log(this.a);
}    
var a = 1; 
(function(){
	"use strict";
	foo(); //1
})();

```
## 隐式绑定
1，隐式绑定的规则是调用位置是否有上下文对象，或者说是否被某个对象拥有或者包含；当函数引用有上下文对象时，隐式绑定规则会把函数调用中的 this 绑定到这个上下文对象。

```
function foo() {
    console.log( this.a );
}
var a = 1;
var obj = {
	a: 2,
	foo: foo
};
obj.foo(); // 2
```
这里的调用过程是这样的：   
获取obj.foo属性 -> 根据引用关系找到foo函数，执行调用所以这里对foo的调用存在上下文对象obj，this进行了隐式绑定，即this绑定到了obj上，所以this.a被解析成了obj.a，即2。   
   
2，对象属性引用链中只有最顶层或者说最后一层会影响调用位置：

```
function foo() { 
    console.log( this.a );
}
var a = 1;
var obj1 = { 
    a: 3,
    foo: foo 
};
var obj2 = { 
    a: 2,
    obj1: obj1
};
obj2.obj1.foo(); // 3
```
函数的调用过程：    
先获取obj1.obj2 -> 通过引用获取到obj2对象，再访问 obj2.foo -> 最后执行foo函数调用这里调用链不只一层，存在obj1、obj2两个对象，隐式绑定原则是获取最后一层调用的上下文对象，即obj2，所以结果是3。    

3，隐式丢失。隐式丢失存在以下三个陷阱，需要特别注意。

- 将函数通过隐式调用的形式赋值给一个变量;
```
function foo() { 
    console.log( this.a );
}
var a = 1;
var obj = { 
    a: 2,
    foo: foo 
};
var bar = obj.foo;
bar(); // 1
```
把obj.foo赋予别名bar，造成了隐式丢失，因为只是把foo()函数赋给了bar，而bar与obj对象则毫无关系
- 将函数通过隐式调用的形式进行传参；

```
var a = 1;
function foo() {
    console.log(this.a);
}
function bar(fn) {
    fn();
}
var obj = {
    a : 2,
    foo:foo
}
bar(obj.foo); // 1
```
把obj.foo当作参数传递给bar函数时，有隐式的函数赋值fn=obj.foo。与上例类似，只是把foo函数赋给了fn，而fn与obj对象则毫无关系

- 内置函数：内置函数与上例类似，也会造成隐式丢失

```
var a = 1;
function foo() {
    console.log(this.a);
}
var obj = {
    a : 2,
    foo:foo
}
setTimeout(obj.foo,100); // 1
```

## 显示绑定
1，相对隐式绑定，this值在调用过程中会动态变化，可是我们就想绑定指定的对象，这时就用到了显示绑定。显式绑定通过call()、apply()、bind()方法把对象绑定到this上。

```
function foo(a,b) {
	console.log( this.a,a,b );
}
var obj = {
	a:2
};
foo.call( obj,"call-a","call-b"); // 2 call-a call-b
foo.apply(obj,["apply-a","apply-b"]) // 2 apply-a apply-b
```
2，硬绑定：硬绑定是显式绑定的一个变种，使this不能再被修改。

```
function foo() { 
    console.log( this.a );
}
var a = 1;
var obj1 = { 
    a: 2,
};
var obj2 = { 
    a: 3,
};
var bar = function(){
    foo.call( obj1 );
}
setTimeout( bar, 100 ); // 2
bar.call( obj2 ); // 2，不是3

```

## new 绑定
js中的new操作符，和其他语言中（如JAVA）的new机制是不一样的。js中，它就是一个普通函数调用，只是被new修饰了而已。

```
function foo(a) {
	this.a = a;
}
var bar = new foo(1);
console.log( bar.a ); // 1
```
使用 new 来调用 foo(..) 时，我们会构造一个新对象并把它绑定到 foo(..) 调用中的 this 上。 new 是最后一种可以影响函数调用时 this 绑定行为的方法，我们称之为 new 绑定。
# 绑定例外
1，箭头函数：this的绑定和作用域有关。如果在当前的箭头函数作用域中找不到变量，就像上一级作用域里去找。

```
function foo() {
    let id = 3
	 setTimeout(() => {
	    console.log('id:', this.id); // 2
	  }, 100);
}
var id = 1;
foo.call({ id: 2 })
```
2，被忽略的this：当被绑定的是null，则使用的是默认绑定规则。

```
function foo() {
	console.log( this.a );
}
var a = 1;
foo.call( null ); // 1
```
如果你把 null 或者 undefined 作为 this 的绑定对象传入 call 、 apply 或者 bind ，这些值在调用时会被忽略，实际应用的是默认绑定规则。     

3，柯里化

```
function foo(a,b) {	
	console.log( "a:" + a + ", b:" + b );
}
// 把数组“展开”成参数
foo.apply( null, [2, 3] ); // a:2, b:3
// 使用 bind(..) 进行柯里化
var bar = foo.bind( null, [2] );
bar( 3 ); // a:2, b:3
```
升级版：

```
function foo(a,b) {
	console.log( "a:" + a + ", b:" + b );
}
// 我们的DMZ空对象,“DMZ”（demilitarized zone，非军事区）
var dmz = Object.create( null );//{}
// 把数组展开成参数
foo.apply( dmz, [2, 3] ); // a:2, b:3
// 使用bind(..)进行柯里化
var bar = foo.bind( dmz, 2 );
bar( 3 ); // a:2, b:3
```
# 总结
1. this是函数执行的上下文对象；
2. 根据函数调用的方式不同this的值也不同：
- 以函数的形式调用，this是window
- 以方法的形式调用，this是调用方法的对象
- 以构造函数的形式调用，this是新创建的那个对象
- 使用call和apply调用的函数，第一个参数就是this
- 在全局作用域中，this是window
- 在响应函数中，给谁绑定的事件this就是谁
3. 在事件处理函数中：获取触发当前事件的元素；
4. 在普通函数中（直接调用）：获取的是window对象；
5. 作为对象的方法中：获取的是当前对象。
6. 在全局作用域中：this指代window对象。
7. 构造函数中的this：指代创建的对象。
8. 注意：
- 当调用方式不同时，this指代的含义不同，得到的结果也不同。
- this本身不具备任何含义。


> 参考文档：    
彻底理解js中this的指向，不必硬背 ：http://www.cnblogs.com/pssp/p/5216085.html          
彻底搞懂 JS 中 this 机制：https://blog.csdn.net/cjgeng88/article/details/79846670    
JS中的this原理详解：https://blog.csdn.net/mutouafangzi/article/details/76563005