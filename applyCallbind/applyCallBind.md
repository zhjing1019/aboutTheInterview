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