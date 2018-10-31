# Javascript重要概念

* [类](#1-类)
* [原型链](#2-原型链)
* [继承](#3-继承)
* [作用域链](#4-作用域链)
* [闭包](#5-闭包)
* [异步机制](#6-异步机制)

## 1. 类

> 和其他面向对象的语言（如Java）不同，Javascript语言对类的实现和继承的实现没有标准的定义，而是将这些交给了程序员，让程序员更加灵活地（当然刚开始也更加头疼）去定义类，实现继承。（以下不讨论ES6中利用class、extends关键字来实现类和继承；实质上，ES6中的class、extends关键字是利用语法糖实现的）

#### 我对类的理解

首先，我先说说我对类的理解：类是包含了一系列【属性/方法】的集合，可以通过类的构造函数创建一个实例对象（例如人类是一个类，而每一个人就是一个实例对象），而这个实例对象中会包含两方面内容：

**a. 类的属性**

属性就是每一个实例所特有的，属于个性。（例如每个人的名字都不相同）

**b. 类的方法**

方法就是每一个实例所共享的，属于共性。（例如每个人都要吃饭）

#### Javascript对类的实现

**a.利用函数创建类，利用new关键字生成实例对象**（话不多说，先上代码，以下没有特别说明的话，我都会先上代码，然后进行解释说明）

```js
function Human() {
    console.log('create human here')
}
var fakeperson = Human() // undefined
var person = new Human() // {}
```

这里Human既是一个普通函数，也是一个类的构造函数，当调用Human\(\)的时候，它作为一个普通函数会被执行，会输出create human here，但是没有返回值（即返回undefined）；而当调用new Human\(\)时，也会输出create human here并且返回一个对象。因为我们用Human这个函数来构造对象，所以我们也把Human称作构造函数。**所以通过定义构造函数，就相当于定义了一个类，通过new关键字，即可生成一个实例化的对象。**

**b.利用构造函数实现类的属性**

```js
function Human(name) {
    this.name = name
}
var person_1 = new Human('Jack')
var person_2 = new Human('Rose')
console.log(person_1.name)  // Jack
console.log(person_2.name)  // Rose
```

这里的Human构造函数中多了一个参数并且函数体中多了一句this.name = name，这句话的中的this指针指向new关键字返回的实例化对象，所以根据构造函数参数的不同，其生成的对象中的具有的属性name的值也会不同。而这里的name就是这个类的属性

**c.利用prototype实现类的方法**

这里因为要用到原型链的知识，所以放到原型链后面说。

## 2. 原型链

#### 类的prototype是什么？

在Javascript中，每当我们定义一个构造函数，Javascript引擎就会自动为这个类中添加一个prototype（也被称作原型）  

#### 对象的\_\_proto\_\_是什么？  

在Javascript中，每当我们使用new创建一个对象时，Javascript引擎就会自动为这个对象中添加一个\_\_proto\_\_属性，并让其指向其类的prototype

```javascript
function Human(name) {
    this.name = name
}
console.log(Human.prototype)
var person_test1 = new Human('Test1')
var person_test2 = new Human('Test2')
console.log(person_test1.__proto__)
console.log(person_test2.__proto__)
console.log(Human.prototype === person_test1.__proto__) // true
console.log(Human.prototype === person_test2.__proto__) // true
```

我们会发现Human.prototype是一个对象，Human类的实例化对象person\_test1、person\_test2下都有一个属性\_\_proto\_\_也是对象，并且它们都等于Human.prototype，我们知道在Javascript中引用类型的相等意味着他们所指向的是同一个对象。所以我们可以得到结论，任何一个实例化对象的\_\_proto\_\_属性都指向其类的prototype。  

#### 对象的\_\_proto\_\_有什么作用？

```javascript
var Pproto = {
	name:'jack'
}
var person = {
	__proto__:Pproto
}
console.log(person.name) // jack
person.name = 'joker'
console.log(person.name) // joker
```

我们发现最开始我们并没有给person定义name属性，为什么console出来jack呢？这就是Javascript著名的原型链的结果啦。话不多说，先上图：  


![proto](https://upload-images.jianshu.io/upload_images/12275140-ef84166de7582907.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 "prototype\_fig.png")


当我们访问person.name时，发生了什么呢？首先它会访问person对象本身的属性，如果本身没有定义name属性的话，它会去寻找它的\_\_proto\_\_属性对象，在这个例子中person的\_\_proto\_\_属性对应的是Pproto对象，所以person的\_\_proto\_\_指向了Pproto，然后我们发现Pproto对象是具有name属性的，那么person.name就到此为止，返回了jack，但是如果我们又给person加上了一个自身的属性name呢？这时，再次person.name就不会再寻找\_\_proto\_\_了，因为person本身已经具有了name属性，而且其值为joker，所以这里会返回joker.

> 我们注意到上图中Pproto的\_\_proto\_\_指向了Object，这是因为每一个通过字面量的方式创建出来的对象它们都默认是Object类的对象，所以它们的\_\_proto\_\_自然指向Object.prototype。

#### 利用prototype实现类的方法

```javascript
function Human(name) {
    this.name = name
}
Human.prototype.eat = function () {
    console.log('I eat!')
}
var person_1 = new Human('Jack')
var person_2 = new Human('Rose')
person_1.eat() // I eat!
person_2.eat() // I eat!
console.log(person_1.eat === person_2.eat) // true
```

这里我们在构造函数外多写了一句：Human.prototype.eat = function\(\) {...} 这样以后每个通过Human实例化的对象的\_\_proto\_\_都会指向Human.prototype，并且根据上述原型链知识，我们可以知道只要构造函数中没有定义同名的方法，那么每个对象访问say方法时，访问的其实都是Human.prototype.say方法，这样我们就利用prototype实现了类的方法，所有的对象实现了共有的特性，那就是eat

## 3. 继承

#### 我对继承的理解

假如有n（n>=2）个类，他们的一些【属性/方法】不一样，但是也有一些【属性/方法】是相同的，所以我们每次定义它们的时候都要重复的去定义这些相同的【属性/方法】，那样岂不是很烦？所以一些牛逼的程序员想到，能不能像儿子继承父亲的基因一样，让这些类也像“儿子们”一样去“继承”他们的“父亲”（而这里的父亲就是包含他们所具有的相同的【属性/方法】）。这样我们就可以多定义一个类，把它叫做父类，在它的里面包含所有的这些子类所具有的相同的【属性/方法】，然后通过继承的方式，让所有的子类都可以访问这些【属性/方法】，而不用每次都在子类的定义中去定义这些【属性/方法】了。

#### 原型链实现继承（让子类继承了父类的方法）

```javascript
function Father() {
}
Father.prototype.say = function() {
	console.log('I am talking...')
}
function Son() {
}
var sonObj_1 = new Son()
console.log(sonObj_1.say) // undefined

// 原型链实现继承的关键代码
Son.prototype = new Father()

var sonObj_2 = new Son()
console.log(sonObj_2.say) // function() {...}
```

看到这句Son.prototype = new Father()你可能有点蒙圈，没关系，我先上个原型链的图，你分分钟就能明白了

![jicheng.png](https://upload-images.jianshu.io/upload_images/12275140-f14db41a403a03f9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

对着图我们想一想，首先，一开始Son、Father两个类没有什么关系，所以在访问say的时候肯定是undefined，但是当我们使用了Son.prototype = new Father()后，我们知道通过new Son()生成的对象都会有\_\_proto\_\_属性，而这个属性指向Son.prototype，而这里我们又让它等于了一个Father的对象，而Father类又定义了静态方法say，所以这里我们的sonObj_2通过沿着原型链寻找，寻找到了say方法，于是就可以访问到Father类的静态方法say了。这样就实现了子类继承了父类的方法，那么如何让子类继承父类的属性呢？

#### 构造函数实现继承（让子类继承了父类的属性）

```javascript
function Father(name) {
	this.name = name
}
function Son() {
	Father.apply(this, arguments)
	this.sing = function() {
		console.log(this.name + ' is singing...')
	}
}
var sonObj_1 = new Son('jack')
var sonObj_2 = new Son('rose')
sonObj_1.sing() // jack is singing...
sonObj_2.sing() // rose is singing...
```

在这个例子中，通过在Son的构造函数中利用apply函数，执行了Father的构造函数，所以每一个Son对象实例化的过程中都会执行Father的构造函数，从而得到name属性，这样，每一个Son实例化的Son对象都会有不同的name属性值，于是就实现了子类继承了父类的属性

#### 组合方式实现继承（组合 原型链继承 + 构造函数继承）

顾名思义，就是结合上述两种方法，然后同时实现对父类的【属性/方法】的继承，代码如下：

```javascript
function Father(name) {
	this.name = name
}
Father.prototype.sayName = function() {
	console.log('My name is ' + this.name)
}
function Son() {
	Father.apply(this, arguments)
}
Son.prototype = new Father('father')
var sonObj_1 = new Son('jack')
var sonObj_2 = new Son('rose')
sonObj_1.sayName() // My name is jack
sonObj_2.sayName() // My name is rose
```

这里子类Son没有一个自己的方法，它的sayName方法继承自父类的静态方法sayName，构造函数中继承了父类的构造函数方法，所以得到了非静态的name属性，因此它的实例对象都可以调用静态方法sayName，但是因为它们各自的name不同，所以打印出来的name的值也不同。看到这里，大家可能认为这已经是一种完美无缺的Javascript的继承方式了，但是还差一丢丢，因为原型链继承不是一种纯粹的继承原型的方式，它有副作用，为什么呢？因为在我们调用Son.prototype = new Father()的时候，不仅仅使Son的原型指向了一个Father的实例对象，而且还让Father的构造函数执行了一遍，这样就会执行this.name = name；所以这个Father对象就不纯粹了，它具有了name属性，并且值为father，那为什么之后我们访问的时候访问不到这个值呢？这又是因为原型链的原因啦，话不多说先上图：

![combo.png](https://upload-images.jianshu.io/upload_images/12275140-6a06ff573861a090.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![combo1.png](https://upload-images.jianshu.io/upload_images/12275140-c2c71b4b9178d33c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

所以这里父类的构造函数在进行原型链继承的时候也执行了一次，并且在原型链上生成了一个我们永远也不需要访问的name属性，而这肯定是占内存的（想象一下name不是一个字符串，而是一个对象），那么我们怎么能让原型链继承更纯粹一点呢？让它只继承原型的方法）呢？

#### 寄生组合方式实现继承

为了让原型链继承的更纯粹，这里我们引入一个Super函数，让Father的原型寄生在Super的原型上，然后让Son去继承Super，最后我们把这个过程放到一个闭包内，这样Super就不会污染全局变量啦，话不多说上代码：

```javascript
function Father(name) {
	this.name = name
}
Father.prototype.sayName = function() {
	console.log('My name is ' + this.name)
}
function Son() {
	Father.apply(this, arguments)
}
(function () {
   function Super(){}
   Super.prototype = Father.prototype
   Son.prototype = new Super()
}())
var sonObj_1 = new Son('jack')
```

这个时候再去打印sonObj1就会发现，它的原型中已经没有name属性啦，如下所示:

![jisheng.png](https://upload-images.jianshu.io/upload_images/12275140-4d336c6423c3c184.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 4. 作用域链

有点类似于原型链（proto chain），Javascript中变量遵从作用域链（scope chain）规则。

![scope_chain.png](https://upload-images.jianshu.io/upload_images/12275140-ba9362bf04fe64a3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
如上图所示，在Javascript中，每一个函数体对应于一个作用域。

**当访问一个变量时，我们会先访问当前作用域内是否有定义该变量，如果没有就会在该作用域外的作用域内寻找是否有改变量，依此类推，一直寻找到全局变量。如果全局变量中依旧没有定义该变量，就会返回undefined。**

我们来看下下面这个例子：

```javascript
var milk = '外面的特仑苏'
function wrapper1() {
    var milk = '里面的特仑苏'
    console.log('我要喝' + milk)   //我要喝里面的特仑苏
}
function wrapper2() {
    console.log('我要喝' + milk)   //我要喝外面的特仑苏
}
wrapper1()
wrapper2()
```

在上述例子中，我们在*wrapper1*函数体内定义了变量*milk*，因此*wrapper1*在寻找完当前作用域即可以得到**里面的特仑苏**，而在*wrapper2*函数体内没有定义变量*milk*，它会沿着作用域链去寻找全局变量，然后得到了*外面的特仑苏*。所以，有另一种说法认为闭包是由函数和与其相关的引用环境组合而成的实体。


## 5. 闭包

#### 什么是闭包

>**维基百科：**在计算机科学中，**闭包**（Closure），是引用了自由变量的函数。这个被引用的自由变量将和这个函数一同存在，即使已经离开了创造它的环境也不例外。所以，有另一种说法认为闭包是由函数和与其相关的引用环境组合而成的实体。

按照作用域链的规则，我们无法在某函数体外访问到该函数内的局部变量。但是出于某些目的我们想在函数体外访问到函数体内的局部变量，我们该怎么做呢？请看下面例子：

```javascript
function wrapper1() {
    var milk = '里面的特仑苏'
    function drink() {
        console.log('我喝了' + milk)
    }
    return drink
}
var result = wrapper1()
result()  //我喝了里面的特仑苏
```

我们在函数体内再创建一个函数，并且把这个内部函数*drink*作为外部函数*wrapper1*的返回值。我们通过执行函数*wrapper1*获得了它的返回值*drink*，并且执行它，就成功的访问到了它的内部变量*milk*（里面的特仑苏）。
回想维基百科中闭包的定义，再结合上述例子：*drink*函数就是一个闭包，因为它引用了处于它外部的变量*milk*。这个被引用的外部变量*milk*和函数*drink*一同存在，即使已经离开了创造它的环境也不例外。所以，有另一种说法认为闭包是由函数和与其相关的引用环境组合而成的实体。

#### 闭包的用途

从上述例子中不难看出，闭包的一个用途就是可以访问函数的内部变量。从而可以实现一些面向对象的功能，例如设置类的隐私变量。闭包的另一个用途就是可以使变量一直保存在内存之中，不被垃圾回收机制所回收。看下面这个例子：

```javascript
var change;
function wrapper() {
    var milk = '特仑苏'
    function drink() {
        console.log('我喝了' + milk)
    }
    change = function () {
        milk =  'AD钙奶'
    }
    return drink
}
var result = wrapper()
result()  //我喝了特仑苏
change()
result()  //我喝了AD钙奶
```

可以看到，*wrapper*执行之后，*milk*变量一直能被访问到，原因就是*result*引用了*wrapper*内部的*drink*函数，*drink*函数又引用了milk变量，因此它一直不会被垃圾回收机制所回收。

#### 慎用闭包
因为闭包会使得函数中的变量都被保存在内存中，内存消耗很大，所以不能滥用闭包，否则可能会造成内存泄露。解决方法是，在使用完闭包函数之后，将变量设置为undefined。比如在上例中，在使用完result之后，将result设置为null或者undefined。


## 6. 异步机制

>Javascript作为一种单线程语言，是如何实现异步编程的呢？

相信不少人对Javascript单线程表示怀疑：为何单线程可以实现异步操作呢？其实Javascript确实是单线程的（我们不妨把这个线程称作主线程），但它实现异步操作的方式确实借助了浏览器的其他线程的帮助。那其他线程是怎么帮助Javascript主线程来实现异步的呢？答案就是任务队列（task queue）和事件循环（event loop）。

#### 任务队列

首先，作为单线程语言，在Javascript中定义的任务都会在主线程中执行。但是并不是每个任务都会立刻执行，而这种不立刻执行的任务我们称作异步任务。相反，那些立刻执行的任务我们把它们称作同步任务。而这些异步任务都会交给浏览器的其他线程去执行，但是主线程需要了解这些异步任务执行的状态，才方便进行下一步操作。

>打个比方，主线程准备做饭，所以下达一个异步任务去买菜，异步任务买完菜之后得告诉主线程：“我买完菜啦”，这个时候主线程才好开始做饭。

而我们知道因为Javascript是单线程，所以上述的“下一步操作”没法直接定义在主函数里（不然就被当做同步任务直接执行了），那这些应该定义在哪里呢？答案就是**异步任务的回调函数中**。在Javascript异步机制中，任务队列就是用来维护异步任务回调函数的队列。这样一个队列用来存放这些回调函数，它们会等到主线程执行完所有的同步函数之后按照先进先出的方式挨个执行。那么执行完任务队列之后呢？Javascript主线程就执行完毕了吗？当然不是，不然网页加载完毕之后，谁来处理后续与用户的交互事件（比如点击事件）呢？

#### 事件循环

![javascript_asyc.jpg](https://upload-images.jianshu.io/upload_images/12275140-2962d9dbb54f1cc6.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

我们通过上图来更加形象的了解Javascript的异步机制。
执行同步任务 -> 检查任务队列中是否有任务 -> [有如果则执行] -> 检查任务队列中是否有任务 -> [有如果则执行] -> ......
可见主线程在执行完同步任务之后，会无限循环地去检查任务队列中是否有新的“任务”，如果有则执行。而这些任务包括我们在异步任务中定义的回调函数，也包括用户交互事件的回调函数。通过事件循环，Javascript不仅很好的处理了异步任务，也很好的完成了与用户交互事件的处理。因为在完成异步任务的回调函数之后，任务队列中的任务都是由事件所产生的，因此我们也把上述的循环过程叫做**事件循环**。

#### 异步机制实践

```
console.log('定时器去买菜吧')
setTimeout(function(){
    console.log('菜买完了，主线程去做菜吧')
}, 0)
console.log('你先去买菜，我先看个世界杯')
```
在浏览器中执行上述代码，兴许能更好地理解Javascript的异步机制。

#### 宏任务与微任务

宏任务：script、setTimeout、setInterval、setImmediate、I/O、UI rendering
微任务：Promise(原生)、process.nextTick

##### 执行顺序：

事件循环的顺序，决定js代码的执行顺序。进入整体代码(宏任务)后，开始第一次循环。接着执行所有的微任务。然后再次从宏任务开始，找到其中一个任务队列执行完毕，再执行所有的微任务。

##### 观察者优先级：

idle观察者 > I/O观察者 > check观察者。
idle观察者：process.nextTick
I/O观察者：一般性的I/O回调，如网络，文件，数据库I/O等
check观察者：setImmediate，setTimeout
```
setTimeout(() => {
	console.log('timeout')
})
new Promise(function(resolve) {
	console.log('promise')
	resolve()
}).then(() => {
	console.log('then')
})
console.log('main')
```
执行顺序是：
promise、main、then、timeout

#### 总结

总而言之，Javascript单线程的背后有浏览器的其他线程为其完成异步服务，这些异步任务为了和主线程通信，通过将回调函数推入到任务队列等待执行。主线程所做的就是执行完同步任务后，通过事件循环，不断地检查并执行任务队列中回调函数。




