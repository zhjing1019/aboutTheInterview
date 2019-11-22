#### 什么是Symbol
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
