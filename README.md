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

#### iterator
* [参考阮一峰ECMAScript 6 入门](http://es6.ruanyifeng.com/#docs/iterator) 
* 参考阮一峰ECMAScript 6 入门：http://es6.ruanyifeng.com/#docs/iterator
* 它是一种接口，为各种不同的数据结构提供统一的访问机制。任何数据结构只要部署 Iterator 接口，就可以完成遍历操作（即依次处理该数据结构的所有成员）。
+ Iterator 的遍历过程是这样的。

 + （1）创建一个指针对象，指向当前数据结构的起始位置。也就是说，遍历器对象本质上，就是一个指针对象。

 + （2）第一次调用指针对象的next方法，可以将指针指向数据结构的第一个成员。

 + （3）第二次调用指针对象的next方法，指针就指向数据结构的第二个成员。

 + （4）不断调用指针对象的next方法，直到它指向数据结构的结束位置。
* 每一次调用next方法，都会返回数据结构的当前成员的信息。具体来说，就是返回一个包含value和done两个属性的对象。其中，value属性是当前成员的值，done属性是一个布尔值，表示遍历是否结束。

```javascript
var it = makeIterator(['a', 'b']);

it.next() // { value: "a", done: false }
it.next() // { value: "b", done: false }
it.next() // { value: undefined, done: true }

function makeIterator(array) {
  var nextIndex = 0;
  return {
    next: function() {
      return nextIndex < array.length ?
        {value: array[nextIndex++], done: false} :
        {value: undefined, done: true};
    }
  };
}
``` 

#### bind、call、apply 区别和实现原理
* call 和 apply 都是为了解决改变 this 的指向。作用都是相同的，只是传参的方式不同。除了第一个参数外，call 可以接收一个参数列表，apply 只接受一个参数数组。
* func.call(thisArg, arg1, arg2, ...)：第一个参数是 this 指向的对象，其它参数依次传入。
* func.apply(thisArg, [argsArray])：第一个参数是 this 指向的对象，第二个参数是数组或类数组。
* bind 和其他两个方法作用也是一致的，只是该方法会返回一个函数。并且我们可以通过 bind 实现柯里化。
```javascript
let a = {
    value: 1
}
function getValue(name, age) {
    console.log(name)
    console.log(age)
    console.log(this.value)
}
getValue.call(a, 'yck', '24')       //yck, 24, 1
getValue.apply(a, ['yck', '24'])    //yck, 24, 1

```
参考 https://segmentfault.com/a/1190000018270750 
```javascript
function fruits() {}
 
fruits.prototype = {
    color: "red",
    say: function() {
        console.log("My color is " + this.color);
    }
}
 
var apple = new fruits;
apple.say();    //My color is red

banana = {
    color: "yellow"
}
apple.say.call(banana);     //My color is yellow
apple.say.apply(banana);    //My color is yellow
``` 
* 数组的追加
```javascript
var array1 = [12 , "foo" , {name:"Joe"} , -2458]; 
var array2 = ["Doe" , 555 , 100]; 
Array.prototype.push.apply(array1, array2); 
// array1 值为  [12 , "foo" , {name:"Joe"} , -2458 , "Doe" , 555 , 100] 
``` 
* 获取数组的最大值
```javascript
var  numbers = [5, 458 , 120 , -215 ]; 
// number 本身没有 max 方法，但是 Math 有，我们就可以借助 call 或者 apply 使用其方法。
var maxInNumbers = Math.max.apply(Math, numbers),   //458
    maxInNumbers = Math.max.call(Math,5, 458 , 120 , -215); //458
```
##### call、apply实现原理
参考 https://blog.csdn.net/weixin_30949361/article/details/97354136
* 我们知道，函数都可以调用 call，说明 call 是函数原型上的方法，所有的实例都可以调用。即: Function.prototype.call；
* 其次，如果第一个参数没有传入，在全局环境下，那么默认this指向 window（浏览器） / global（node）(非严格模式)；
* 传入 call 的第一个参数是 this 指向的对象，根据隐式绑定的规则，我们知道 obj.foo()，foo() 中的 this 指向 obj，因此我们可以这样调用函数 
```javascript
Function.prototype.call = function(thisArg, args) {
    // this指向调用call的对象
    if (typeof this !== 'function') { // 调用call的若不是函数则报错
        throw new TypeError('Error')
    }
    thisArg = thisArg || window
    thisArg.fn = this   // 将调用call函数的对象添加到thisArg的属性中
    const result = thisArg.fn(...[...arguments].slice(1)) // 执行该属性
    delete thisArg.fn   // 删除该属性
    return result
}
``` 
* apply的实现原理和call一样，只不过是传入的参数不同而已。下面只给出代码
```javascript
Function.prototype.apply = function(thisArg, args) {
    if (typeof this !== 'function') { 
        throw new TypeError('Error')
    }
    thisArg = thisArg || window
    thisArg.fn = this
    let result
    if(args) {
        result = thisArg.fn(...args)
    } else {
        result = thisArg.fn()
    }
    delete thisArg.fn
    return result
}
``` 
* bind()方法会创建一个新函数，称为绑定函数，当调用这个绑定函数时，绑定函数会以创建它时传入 bind()方法的第一个参数作为 this，传入 bind() 方法的第二个以及以后的参数加上绑定函数运行时本身的参数按照顺序作为原函数的参数来调用原函数。
* bind()不传入第一个参数，那么默认为 window 
* bind的实现原理比call和apply要复杂一些，bind中需要考虑一些复杂的边界条件。bind后的函数会返回一个函数，而这个函数也可能被用来实例化
```javascript
Function.prototype.bind = function(thisArg) {
    if(typeof this !== 'function'){
        throw new TypeError(this + 'must be a function');
    }
    // 存储函数本身
    const _this  = this;
    // 去除thisArg的其他参数 转成数组
    const args = [...arguments].slice(1)
    // 返回一个函数
    const bound = function() {
        // 可能返回了一个构造函数，我们可以 new F()，所以需要判断
        if (this instanceof bound) {
            return new _this(...args, ...arguments)
        }
        // apply修改this指向，把两个函数的参数合并传给thisArg函数，并执行thisArg函数，返回执行结果
        return _this.apply(thisArg, args.concat(...arguments))
    }
    return bound
}
```

#### 什么是原型、原型链
详细介绍 https://github.com/KieSun/Dream/issues/2
##### 原型 
* 所有引用类型都有一个__proto__(隐式原型)属性，属性值是一个普通的对象
* 所有函数都有一个prototype(原型)属性，属性值是一个普通的对象
* 所有引用类型的__proto__属性指向它构造函数的prototype
```javascript
var a = [1,2,3];
a.__proto__ === Array.prototype; // true
```
##### prototype
* prototype 属性。这是一个显式原型属性，只有函数才拥有该属性。基本上所有函数都有这个属性，但是也有一个例外:Function.prototype.bind()
* prototype 当我们声明一个函数时，这个属性就被自动创建了。
* 并且这个属性的值是一个对象（也就是原型），只有一个属性 constructor
##### constructor
* constructor 对应着构造函数，
* constructor 是一个公有且不可枚举的属性。一旦我们改变了函数的 prototype ，那么新对象就没有这个属性了（当然可以通过原型链取到 constructor）。
* 作用：1、让实例对象知道是什么函数构造了它 2、如果想给某些类库中的构造函数增加一些自定义的方法，就可以通过 xx.constructor.method 来扩展
##### _proto_
* 这是每个对象都有的隐式原型属性，指向了创建该对象的构造函数的原型。其实这个属性指向了 [[prototype]]，但是 [[prototype]] 是内部属性，我们并不能访问到，所以使用 _proto_ 来访问。
* 因为在 JS 中是没有类的概念的，为了实现类似继承的方式，通过 _proto_ 将对象和原型联系起来组成原型链，得以让对象可以访问到不属于自己的属性。
* 所以可以说，在 new 的过程中，新对象被添加了 _proto_ 并且链接到构造函数的原型上。

##### 原型链
当访问一个对象的某个属性时，会先在这个对象本身属性上查找，如果没有找到，则会去它的__proto__隐式原型上查找，即它的构造函数的prototype，如果还没有找到就会再在构造函数的prototype的__proto__中查找，这样一层一层向上查找就会形成一个链式结构，我们称为原型链。

#### js判断是否是对象
* 可以通过 Object.prototype.toString.call(xx)。这样我们就可以获得类似 [object Type] 的字符串。

#### js判断是否为对象
参考 https://juejin.im/post/5cab0c45f265da2513734390
* 使用 Array.isArray 判断，如果返回 true, 说明是数组
* 使用 instanceof Array 判断，如果返回true, 说明是数组
* 使用 Object.prototype.toString.call 判断，如果值是 [object Array], 说明是数组
```javascript

function fn() {
    console.log(Array.isArray(arguments));   //false; 因为arguments是类数组，但不是数组
    console.log(Array.isArray([1,2,3,4]));   //true
    console.log(arguments instanceof Array); //fasle
    console.log([1,2,3,4] instanceof Array); //true
    console.log(Object.prototype.toString.call(arguments)); //[object Arguments]
    console.log(Object.prototype.toString.call([1,2,3,4])); //[object Array]
    console.log(arguments.constructor === Array); //false
    arguments.constructor = Array;
    console.log(arguments.constructor === Array); //true
    console.log(Array.isArray(arguments));        //false
}
fn(1,2,3,4);

```

#### ES6 async await
参考 http://es6.ruanyifeng.com/#docs/async
[阮一峰的ES6教程](http://es6.ruanyifeng.com/#docs/async)
* async 函数是什么？一句话，它就是 Generator 函数的语法糖。
* Generator函数
```javascript
const fs = require('fs');

const readFile = function (fileName) {
  return new Promise(function (resolve, reject) {
    fs.readFile(fileName, function(error, data) {
      if (error) return reject(error);
      resolve(data);
    });
  });
};

const gen = function* () {
  const f1 = yield readFile('/etc/fstab');
  const f2 = yield readFile('/etc/shells');
  console.log(f1.toString());
  console.log(f2.toString());
};
```
* 可以替换成async函数
```javascript
const asyncReadFile = async function () {
  const f1 = await readFile('/etc/fstab');
  const f2 = await readFile('/etc/shells');
  console.log(f1.toString());
  console.log(f2.toString());
};
```
* 一比较就会发现，async函数就是将 Generator 函数的星号（*）替换成async，将yield替换成await。
+ async函数对 Generator 函数的改进，体现在以下四点
    + Generator 函数的执行必须靠执行器，所以才有了co模块，而async函数自带执行器。也就是说，async函数的执行，与普通函数一模一样，只要一行。
    + async和await，比起星号和yield，语义更清楚了。async表示函数里有异步操作，await表示紧跟在后面的表达式需要等待结果。
* async函数返回一个 Promise 对象，可以使用then方法添加回调函数。当函数执行的时候，一旦遇到await就会先返回，等到异步操作完成，再接着执行函数体内后面的语句。
```javascript
//一个获取股票报价的函数，函数前面的async关键字，表明该函数内部有异步操作。调用该函数时，会立即返回一个Promise对象。
async function getStockPriceByName(name) {
  const symbol = await getStockSymbol(name);
  const stockPrice = await getStockPrice(symbol);
  return stockPrice;
}

getStockPriceByName('goog').then(function (result) {
  console.log(result);
});
```

```javascript
//指定 50 毫秒以后，输出hello world。
function timeout(ms) {
  return new Promise((resolve) => {
    setTimeout(resolve, ms);
  });
}

async function asyncPrint(value, ms) {
  await timeout(ms);
  console.log(value);
}

asyncPrint('hello world', 50);
``` 
* 由于async函数返回的是 Promise 对象，可以作为await命令的参数。
```javascript
async function timeout(ms) {
  await new Promise((resolve) => {
    setTimeout(resolve, ms);
  });
}

async function asyncPrint(value, ms) {
  await timeout(ms);
  console.log(value);
}

asyncPrint('hello world', 50);
```
* async的多种写法
```javascript
// 函数声明
async function foo() {}

// 函数表达式
const foo = async function () {};

// 对象的方法
let obj = { async foo() {} };
obj.foo().then(...)

// Class 的方法
class Storage {
  constructor() {
    this.cachePromise = caches.open('avatars');
  }

  async getAvatar(name) {
    const cache = await this.cachePromise;
    return cache.match(`/avatars/${name}.jpg`);
  }
}

const storage = new Storage();
storage.getAvatar('jake').then(…);

// 箭头函数
const foo = async () => {};

``` 

#### generator
参考：https://blog.csdn.net/Dora_5537/article/details/90266777
* Generator 函数是 ES6 提供的一种异步编程解决方案。
* Generator 函数是一个状态机，封装了多个内部状态。同时，它还是一个遍历器对象生成函数，返回的是一个遍历器对象，遍历器对象可以依次遍历 Generator 函数内部的每一个状态。
* Generator 函数的两个特征：（1）function关键字与函数名之间有一个星号（*的位置无所谓，通常采用下述写法）；（2）函数体内部使用yield表达式，定义不同的内部状态。
```javascript
function* helloWorldGenerator() {
  yield 'hello';
  yield 'world';
  return 'ending';
}
 
var hw = helloWorldGenerator();
``` 
* 上面代码定义了一个 Generator 函数 helloWorldGenerator，它内部有两个yield表达式（hello和world），即该函数有三个状态：hello，world 和 return 语句（结束执行）。
* 然而，Generator 函数的调用方法与普通函数一样，也是在函数名后面加上一对圆括号。不同的是，调用 Generator 函数后，该函数并不执行，返回的也不是函数运行结果，而是一个指向内部状态的指针对象，也就是遍历器对象（Iterator Object）。
* 下一步，必须调用遍历器对象的next方法，使得指针移向下一个状态。也就是说，每次调用next方法，内部指针就从函数头部或上一次停下来的地方开始执行，直到遇到下一个yield表达式（或return语句）为止。换言之，Generator 函数是分段执行的，yield表达式是暂停执行的标记，而next方法可以恢复执行。
```javascript
hw.next()
// { value: 'hello', done: false }
 
hw.next()
// { value: 'world', done: false }
 
hw.next()
// { value: 'ending', done: true }
 
hw.next()
// { value: undefined, done: true }
``` 
* 调用 Generator 函数，返回一个遍历器对象，代表 Generator 函数的内部指针。以后，每次调用遍历器对象的next方法，就会返回一个有着value和done两个属性的对象。value属性表示当前的内部状态的值，是yield表达式后面那个表达式的值；done属性是一个布尔值，表示是否遍历结束。
* next()方法会执行generator的代码，然后，每次遇到yield x;就返回一个对象{value: x, done: true/false}，然后“暂停”。返回的value就是yield的返回值，done表示这个generator是否已经执行结束了。如果done为true，则value就是return的返回值。 

#### 垃圾回收机制
[参考](https://segmentfault.com/a/1190000018605776?utm_source=tag-newest)
+ 基本的垃圾回收算法称为“标记-清除 
    + 垃圾回收器获取根并“标记”(记住)它们。
    + 然后它访问并“标记”所有来自它们的引用。
    + 然后它访问标记的对象并标记它们的引用。所有被访问的对象都被记住
    + 直到有未访问的引用(可以从根访问)为止
    + 除标记的对象外，所有对象都被删除。
+ 垃圾回收机制的一些优化
    + 分代回收——对象分为两组:“新对象”和“旧对象”。许多对象出现，完成它们的工作并迅速结 ，它们很快就会被清理干净。那些活得足够久的对象，会变“老”，并且很少接受检查。
    + 增量回收——如果有很多对象，并且我们试图一次遍历并标记整个对象集，那么可能会花费一些时间，并在执行中会有一定的延迟。因此，引擎试图将垃圾回收分解为多个部分。然后，各个部分分别执行。这需要额外的标记来跟踪变化，这样有很多微小的延迟，而不是很大的延迟。
    + 空闲时间收集——垃圾回收器只在 CPU 空闲时运行，以减少对执行的可能影响。

#### 闭包
* 什么是闭包: 函数funOne返回了函数funTwo；并且函数funTwo能够访问函数funOne中的变量，函数funTwo称为闭包，简单的说，闭包就是函数能够访问另一个函数里的变量
```javascript
function funOne() {
  let a = 1
  function funTwo() {
      console.log(a)
  }
  return funTwo
}
```
* 闭包解决的问题
```javascript
//因为setTimeout是异步函数，所以会等循环都走完以后才执行setTimeout函数
for ( var i=1; i<=5; i++) {
	setTimeout( function timer() {
		console.log( i );
	}, i*1000 );
}

```
* 可以使用闭包解决此问题
```javascript
for (var i = 1; i <= 5; i++) {
  (function(j) {
    setTimeout(function timer() {
      console.log(j);
    }, j * 1000);
  })(i);
}

```
* 也可以使用let来解决
```javascript
for (let i=1; i<=5; i++) {
	setTimeout( function timer() {
		console.log( i );
	}, i*1000 );
}

```

#### js中的数据类型，基本数据类型和引⽤数据类型的区别
+ js分为基本数据类型和引用数据类型
    + 基本数据类型： Number、String、undefined、null、boolean、Symbol
    + 引用数据类型： Array、Object、 类数组
* 基本数据类型的变量存放的是基本类型数据的实际值，是存放在栈中
* 引用数据类型的变量保存对它的引用，即指针，是存放在堆中

#### 浏览器与Node的事件循环(Event Loop)有何区别?
参考 ：https://blog.csdn.net/Fundebug/article/details/86487117
+ 什么是进程和线程
  + JS 是门非阻塞单线程语言，JS 是单线程执行的，指的是一个进程里只有一个主线程。如果 JS 是门多线程的语言话，我们在多个线程中处理 DOM 就可能会发生问题（一个线程中新加节点，另一个线程中删除节点），当然可以引入读写锁
  + 进程是 CPU 资源分配的最小单位；线程是 CPU 调度的最小单位。
  + 一个进程的内存空间是共享的，每个线程都可用这些共享内存。
##### 浏览器内核
+ 浏览器内核
  + 浏览器内核通常称为渲染引擎
    + GUI渲染线程
      + 主要负责页面的渲染，解析 HTML、CSS，构建 DOM 树，布局和绘制等。
      + 当界面需要重绘或者由于某种操作引发回流时，将执行该线程。
      + 该线程与 JS 引擎线程互斥，当执行 JS 引擎线程时，GUI 渲染会被挂起，当任务队列空闲时，JS 引擎才会去执行 GUI 渲染。
    + js引擎线程
      + 负责处理javascript脚本，执行代码
      + 负责执行准备好待执行的事件，即定时器计数结束，或者异步请求成功并正确返回时，将依次进入任务队列，等待 JS 引擎线程的执行。
      + 当然，该线程与 GUI 渲染线程互斥，当 JS 引擎线程执行 JavaScript 脚本时间过长，将导致页面渲染的阻塞。
    + 定时器触发线程
      + 负责执行异步定时器一类的函数的线程，如： setTimeout，setInterval。
      + 主线程依次执行代码时，遇到定时器，会将定时器交给该线程处理，当计数完毕后，事件触发线程会将计数完毕后的事件加入到任务队列的尾部，等待 JS 引擎线程执行。
    + 异步 http 请求线程
      + 负责执行异步请求一类的函数的线程，如： Promise，axios，ajax 等。
      + 主线程依次执行代码时，遇到异步请求，会将函数交给该线程处理，当监听到状态码变更，如果有回调函数，事件触发线程会将回调函数加入到任务队列的尾部，等待 JS 引擎线程执行。


#### js防抖和节流
* 防抖是将多次执行变为最后一次执行，节流是将多次执行变为每隔一段时间执行
* 防抖和节流的作用都是防止函数多次调用。区别在于，假设一个用户一直触发这个函数，且每次触发函数的间隔小于wait，防抖的情况下只会调用一次，而节流的 情况会每隔一定时间（参数wait）调用函数。
##### js防抖
* 当持续触发事件时，一定时间段内没有再触发事件，事件处理函数才会执行一次，如果设定时间到来之前，又触发了事件，就重新开始延时。也就是说当一个用户一直触发这个函数，且每次触发函数的间隔小于既定时间，那么防抖的情况下只会执行一次。
一个简易的防抖函数
```javascript
function debounce(fn, wait = 50) {
    // 缓存一个定时器id
    var timeout = null;    
    // 这里返回的函数是每次用户实际调用的防抖函数
    // 如果已经设定过定时器了就清空上一次的定时器
    // 开始一个新的定时器，延迟执行用户传入的方法  
    return function() {
        if(timeout !== null) 
          clearTimeout(timeout);  //清除这个定时器
        timeout = setTimeout(fn, wait);  
    }
}
// 处理函数
function handle() {
    console.log(Math.random()); 
}
// 不难看出如果用户调用该函数的间隔小于wait的情况下，上一次的时间还未到就被清除了，并不会执行函数
window.addEventListener('scroll', debounce(handle, 1000));
```
+ 这是一个简单版的防抖，但是有缺陷，这个防抖只能在最后调用。一般的防抖会有immediate选项，表示是否立即调用。
  + 例如在搜索引擎搜索问题的时候，我们当然是希望用户输入完最后一个字才调用查询接口，这个时候适用延迟执行的防抖函数，它总是在一连串（间隔小于wait的）函数触发之后调用。
  + 例如用户点赞的时候，我们希望用户点第一下的时候就去调用接口，并且成功之后改变点赞按钮的样子，用户就可以立马得到反馈是否star成功了，这个情况适用立即执行的防抖函数，它总是在第一次调用，并且下一次调用必须与前一次调用的时间间隔大于wait才会触发。

* 带有立即执行选项的防抖函数
此处参考 https://juejin.im/post/5ba34e54e51d450e5162789b#heading-15
```javascript
/**
 * 防抖函数，返回函数连续调用时，空闲时间必须大于或等于 wait，func 才会执行
 *
 * @param  {function} func        回调函数
 * @param  {number}   wait        表示时间窗口的间隔
 * @param  {boolean}  immediate   设置为ture时，是否立即调用函数
 * @return {function}             返回客户调用函数
 */
function debounce (func, wait = 50, immediate = true) {
  let timer, context, args
  
  // 延迟执行函数
  const later = () => setTimeout(() => {
    // 延迟函数执行完毕，清空缓存的定时器序号
    timer = null
    // 延迟执行的情况下，函数会在延迟函数中执行
    // 使用到之前缓存的参数和上下文
    if (!immediate) {
      func.apply(context, args)
      context = args = null
    }
  }, wait)

  // 这里返回的函数是每次实际调用的函数
  return function(...params) {
    // 如果没有创建延迟执行函数（later），就创建一个
    if (!timer) {
      timer = later()
      // 如果是立即执行，调用函数
      // 否则缓存参数和调用上下文
      if (immediate) {
        func.apply(this, params)
      } else {
        context = this
        args = params
      }
    // 如果已有延迟执行函数（later），调用的时候清除原来的并重新设定一个
    // 这样做延迟函数会重新计时
    } else {
      clearTimeout(timer)
      timer = later()
    }
  }
}

function handle() {
      console.log(Math.random());
}
window.addEventListener('scroll', debounce(handle, 1000));
```

##### 函数节流
此处参考  https://www.cnblogs.com/youma/p/10559331.html 
* 当持续触发事件时，保证在一定时间内只调用一次事件处理函数，意思就是说，假设一个用户一直触发这个函数，且每次触发小于既定值，函数节流会每隔这个时间调用一次
```javascript
var throttle = function(func, delay) {
    var prev = Date.now();
    return function() {
        var context = this;   //this指向window
        var args = arguments;
        var now = Date.now();
        if (now - prev >= delay) {
            func.apply(context, args);
            prev = Date.now();
        }
    }
}
function handle() {
    console.log(Math.random());
}
window.addEventListener('scroll', throttle(handle, 1000));
```
* 这个节流函数利用时间戳让第一次滚动事件执行一次回调函数，此后每隔1000ms执行一次，在小于1000ms这段时间内的滚动是不执行的
```javascript
var throttle = function(func, delay) {
    var timer = null;
    return function() {
        var context = this;
        var args = arguments;
        if (!timer) {
            timer = setTimeout(function() {
                func.apply(context, args);
                timer = null;
            }, delay);
        }
    }
}
function handle() {
    console.log(Math.random());
}
window.addEventListener('scroll', throttle(handle, 1000));
``` 
