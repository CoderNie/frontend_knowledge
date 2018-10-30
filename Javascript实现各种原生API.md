<!--lang: javascript-->
## Javascript实现各种API

* [call](#1-call)
* [bind](#2-bind)
* [new](#3-new)
* [instanceof](#4-instanceof)
* [JSON.parse](#5-jsonparse)
* [JSON.stringify](#6-jsonstringify)
* [throttle 函数节流](#7-throttle-函数节流)
* [debounce 函数防抖](#8-debounce-函数防抖)
* [Array.isArray](#9-arrayisarray)

---

### 1. call

实现思路：

a. 将要执行的函数设置为对象的 fn 属性

b. 使用 eval 关键字执行 fn 函数

c. 执行完毕后删除对象的 fn 属性

d. 为防止对象本来就具有 fn 属性，先把它原来的 fn 属性保存起来

```
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

### 2. bind

实现思路：

a. 将函数参数分两部分，一部分在执行 bind 时传入，一部分在执行函数是传入，最后使用 apply 执行函数

b. 如果 bind 后的函数被当做构造函数，则绑定 this 指针

c. 让新函数原型链继承原函数

```
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

### 3. new

实现思路：

a. 新建空对象、让对象的 \_\_proto\_\_ 指向函数的 prototype、执行构造函数、返回该对象

b. 如果构造函数返回的值是对象或函数，则返回构造函数返回的对象或函数

```
function divNew() {
    var obj = new Object()
    var Constructor = Array.prototype.shift.call(arguments)
    obj.__proto__ = Constructor.prototyep
    var ret = Constructor.apply(obj, arguments)
    return (typeof ret === 'object' || typeof ret === 'function') ? ret : obj
}
```

### 4. instanceof

实现思路：

a. 基于原型链，沿着原型链寻找，所以迭代条件是 L = L.

b. 空对象的原型的原型指向 null，所以终止条件是 L === null

```
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

### 5. JSON.parse

### 6. JSON.stringify

### 7. throttle 函数节流

### 8. debounce 函数防抖

### 9. Array.isArray

实现思路：

a.

```
Array.prototype.divIsArray(){
    return Object.prototype.toString.call(arr) === '[object Array]'
}
```



