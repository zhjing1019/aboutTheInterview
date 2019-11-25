### 参考原文地址
[掘金1](https://juejin.im/post/5ba34e54e51d450e5162789b)
[掘金2](https://juejin.im/post/5cab0c45f265da2513734390)

#### 1. 基本类型有哪几种？null 是对象吗？基本数据类型和复杂数据类型存储有什么区别？
* undefined、null、boolean、string、number、symbol
* 虽然 typeof null 返回的值是 object,但是null不是对象，而是基本数据类型的一种。
* 基本数据类型存储在栈内存，存储的是值。复杂数据类型的值存储在堆内存，地址（指向堆中的值）存储在栈内存。当我们把对象赋值给另外一个变量的时候，复制的是地址，指向同一块内存空间，当其中一个对象改变时，另一个对象也会变化。 

#### 2、变量提升？
* 第一阶段：第一个阶段是创建的阶段，JS 解释器会找出需要提升的变量和函数，并且给他们提前在内存中开辟好空间，函数的话会将整个函数存入内存中，变量只声明并且赋值为 undefined
* 所以在第二个阶段，也就是代码执行阶段，我们可以直接提前使用。

#### 3. typeof 是否正确判断类型? instanceof呢？ instanceof 的实现原理是什么？
* typeof 对于基本类型，除了 null 都可以显示正确的类型， typeof null 返回object； 
```javascript

 typeof 1 // 'number'
typeof '1' // 'string'
typeof undefined // 'undefined'
typeof true // 'boolean'
typeof Symbol() // 'symbol'
typeof b // b 没有声明，但是还会显示 undefined

```
+ 对于 null 来说，虽然它是基本类型，但是会显示 object，这是一个存在很久了的 Bug
    + 因为在 JS 的最初版本中，使用的是 32 位系统，为了性能考虑使用低位存储了变量的类型信息，000 开头代表是对象，然而 null 表示为全零，所以将它错误的判断为 object 。虽然现在的内部类型判断代码已经改变了，但是对于这个 Bug 却是一直流传下来。
* instanceof 可以正确的判断对象的类型，因为内部机制是通过判断对象的原型链中是不是能找到类型的 prototype。
* instanceof 是通过原型链判断的，A instanceof B, 在A的原型链中层层查找，是否有原型等于B.prototype，如果一直找到A的原型链的顶端(null;即Object.proptotype.__proto__),仍然不等于B.prototype，那么返回false，否则返回true.
```javascript

// L instanceof R
function instance_of(L, R) {//L 表示左表达式，R 表示右表达式
    var O = R.prototype;// 取 R 的显式原型
    L = L.__proto__;    // 取 L 的隐式原型
    while (true) { 
        if (L === null) //已经找到顶层
            return false;  
        if (O === L)   //当 O 严格等于 L 时，返回 true
            return true; 
        L = L.__proto__;  //继续向上一层原型链查找
    } 
}

```

#### 4、什么是Symbol
[参考Symbol](https://juejin.im/post/5cab0c45f265da2513734390)
参烤阮一峰 ES6标准入门
* ES6 引入了一种新的原始数据类型Symbol，表示独一无二的值。它是 JavaScript 语言的第七种数据类型，前六种是：undefined、null、布尔值（Boolean）、字符串（String）、数值（Number）、对象（Object）。
```javascript
let s = Symbol();
typeof s // "symbol"
```
* Symbol函数前不能使用new命令，否则会报错。这是因为生成的 Symbol 是一个原始类型的值，不是对象。也就是说，由于 Symbol 值不是对象，所以不能添加属性。基本上，它是一种类似于字符串的数据类型。
* Symbol函数可以接受一个字符串作为参数，表示对 Symbol 实例的描述，主要是为了在控制台显示，或者转为字符串时，比较容易区分。
```javascript
let s1 = Symbol('foo');
let s2 = Symbol('bar');
s1 // Symbol(foo)
s2 // Symbol(bar)

s1.toString() // "Symbol(foo)"
s2.toString() // "Symbol(bar)"
```
* 如果 Symbol 的参数是一个对象，就会调用该对象的toString方法，将其转为字符串，然后才生成一个 Symbol 值。
* Symbol 值不能与其他类型的值进行运算，会报错。
* Symbol 值可以显式转为字符串。
* Symbol 值也可以转为布尔值，但是不能转为数值。
* Symbol.prototype.description创建Symbol的时候，可以添加一个描述
```javascript
const sym = Symbol('foo');
```
* 由于每个Symbol值都是不相等的，这意味着Symbol值可以作为标识符，用于对象的属性名，就能保证不会出现同名的属性。这对于一个对象有多个模块构成的情况非常有用，能防止其一个键被不小心改写或覆盖。
```javascript
let sym = Symbol();
// 第一种写法
let a = {};
a[sym] = "hello";

// 第二种写法
let b = {
    [sym]: "hello"
};
```
* Symbol值作为对象属性名时，不能用点运算符。因为点运算符后面总是字符串，所以不会读取Symbol
* 在对象的内部，使用Symbol值定义属性时，Symbol值必须放在方括号之中。
* Symbol 值作为属性名时，该属性还是公开属性，不是私有属性。
* Object.getOwnPropertySymbols方法，可以获取指定对象的所有 Symbol 属性名。Object.getOwnPropertySymbols方法返回一个数组，成员是当前对象的所有用作属性名的 Symbol 值。
* Object.getOwnPropertySymbols方法与for...in循环、Object.getOwnPropertyNames方法进行对比的例子。使用Object.getOwnPropertyNames方法得不到Symbol属性名，需要使用Object.getOwnPropertySymbols方法。
```javascript
const obj = {};

let foo = Symbol("foo");

Object.defineProperty(obj, foo, {
  value: "foobar",
});

for (let i in obj) {
  console.log(i); // 无输出
}

Object.getOwnPropertyNames(obj)
// []

Object.getOwnPropertySymbols(obj)
// [Symbol(foo)]
```

#### for of , for in 和 forEach,map 的区别。
* [参考掘金1](https://juejin.im/post/5ba34e54e51d450e5162789b)
* for...of循环：具有 iterator 接口，就可以用for...of循环遍历它的成员(属性值)。for...of循环可以使用的范围包括数组、Set 和 Map 结构、某些类似数组的对象、Generator 对象，以及字符串。for...of循环调用遍历器接口，数组的遍历器接口只返回具有数字索引的属性。对于普通的对象，for...of结构不能直接使用，会报错，必须部署了 Iterator 接口后才能使用。可以中断循环。
* for...in循环：遍历对象自身的和继承的可枚举的属性, 不能直接获取属性值。可以中断循环。
* forEach: 只能遍历数组，不能中断，没有返回值(或认为返回值是undefined)。
* map: 只能遍历数组，不能中断，返回值是修改后的数组。

