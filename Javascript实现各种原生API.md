# Javascript实现各种API

* [call](#1-call)
* [bind](#2-bind)
* [new](#3-new)
* [instanceof](#4-instanceof)
* [JSON.parse](#5-jsonparse)
* [JSON.stringify](#6-jsonstringify)
* [throttle 函数节流](#7-throttle-函数节流)
* [debounce 函数防抖](#8-debounce-函数防抖)
* [Array.isArray](#9-arrayisarray)

## 1. call

函数作用：改变函数内部 this 指针指向，并运行函数

实现思路：

a. 将要执行的函数设置为对象的 fn 属性

b. 使用 eval 关键字执行 fn 函数

c. 执行完毕后删除对象的 fn 属性

d. 为防止对象本来就具有 fn 属性，先把它原来的 fn 属性保存起来

```js
Function.prototype.divCall = function (obj) {
    var obj = obj || window
    var flag = false, temp
    if (obj.hasOwnProperty('fn')){
      flag = true;
      temp = obj.fn
    }
    obj.fn = this

    var args = []
    for (var i = 1; i < arguments.length; i++)
        args.push('arguments[' + i + ']')
    var result = eval('obj.fn(' + args + ')')
    delete obj.fn
    if (flag) 
      obj.fn = temp
    return result
}
```

## 2. bind

函数作用：改变函数内部 this 指针指向，并返回改变 this 指针指向后的函数

函数作用：改变函数内部 this 指针指向

实现思路：

a. 将函数参数分两部分，一部分在执行 bind 时传入，一部分在执行函数是传入，最后使用 apply 执行函数

b. 如果 bind 后的函数被当做构造函数，则绑定 this 指针

c. 让新函数原型链继承原函数

```js
Function.prototype.divBind = function (obj) {
    var func = this
    var args = Array.prototype.slice.call(arguments, 1)
    var returnFunc = function() {
        args = args.concat(Array.prototype.slice.call(arguments))
        return func.apply(this instanceof returnFunc ? this : obj, args)
    }
    var Dump = function (){}
    Dump.prototype = func.prototype
    returnFunc.prototype = new Dump()
    return returnFunc
}
```

## 3. new

操作符作用：新建一个对象

实现思路：

a. 新建空对象、让对象的 \_\_proto\_\_ 指向函数的 prototype、执行构造函数、返回该对象

b. 如果构造函数返回的值是对象或函数，则返回构造函数返回的对象或函数

```js
function divNew() {
    var obj = new Object()
    var Constructor = Array.prototype.shift.call(arguments)
    obj.__proto__ = Constructor.prototyep
    var ret = Constructor.apply(obj, arguments)
    return (typeof ret === 'object' || typeof ret === 'function') ? ret : obj
}
```

## 4. instanceof

操作符作用：判断实例是否属于某个类

实现思路：

a. 基于原型链，沿着原型链寻找，所以迭代条件是 L = L.

b. 空对象的原型的原型指向 null，所以终止条件是 L === null

```js
function instanceOf(L, R) {
    R = R.prototype
    L = L.__proto__
    while (true){
        if (L === null)
            return false
        if (R === L) 
            return true
        L = L.__proto__
    }
}
```

## 5. JSON.parse

## 6. JSON.stringify

## 7. throttle 函数节流

函数作用：让某个函数在每个规定时间间隔内只会被触发一次

实现思路：

a. 设置定时器，如果在定时器时间范围内触发事件，

b. 为解决 setTimeout 执行过程中 this 指向问题和函数参数传递问题，使用 apply 函数绑定 this 指针

```js
function throttle(func, wait){
    var timeout
    return function(){
        var that = this
        var args = arguments
        if (!timeout) {
            timeout = setTimeout(function (){
                timeout = null;
                func.apply(that, args)
            }, wait)
        }
    }
}
```

## 8. debounce 函数防抖

函数作用：让某个函数在事件触发 n 秒后才执行，如果一个事件触发的 n 秒内又触发了这个事件，那就以新的事件的触发时间为准

实现思路：

a. 设置定时器，如果在定时器时间范围内触发事件，则先清除定时器再重新设置定时器

b. 为解决 setTimeout 执行过程中 this 指向问题和函数参数传递问题，使用 apply 函数绑定 this 指针

```js
function debounce (func, wait){
    var timeout
    return function (){
        var args = arguments
        var that = this
        clearTimeout(timeout)
        timeout = setTimeout(function(){
            func.apply(that, args)
        }, wait)
    }
}
```

## 9. Array.isArray

函数作用：判断变量是否为数组

实现思路：

使用 Object 的 toString 方法，对于任何数组调用该方法都会返回 '\[object Array\]'，而其他基本类型和普通对象类型则会返回其他字符串

```js
Array.prototype.divIsArray(){
    return Object.prototype.toString.call(arr) === '[object Array]'
}
```



