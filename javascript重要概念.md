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

#### 1.类的prototype是什么？

在Javascript中，每当我们定义一个构造函数，Javascript引擎就会自动为这个类中添加一个prototype（也被称作原型）  

#### 2.对象的\_\_proto\_\_是什么？  

在Javascript中，每当我们使用new创建一个对象时，Javascript引擎就会自动为这个对象中添加一个**proto**属性，并让其指向其类的prototype

```

```

我们会发现Human.prototype是一个对象，Human类的实例化对象person\_test1、person\_test2下都有一个属性**proto**也是对象，并且它们都等于Human.prototype，我们知道在Javascript中引用类型的相等意味着他们所指向的是同一个对象。所以我们可以得到结论，任何一个实例化对象的**proto**属性都指向其类的prototype。  
**3.对象的proto**有什么作用？

```
// 代码3.3
var
 Pproto = {
    
name
:
'jack'

}

var
 person = {
    
__proto__
:Pproto
}

console
.log(person.name) 
// jack

person.name = 
'joker'
console
.log(person.name) 
// joker
```

我们发现最开始我们并没有给person定义name属性，为什么console出来jack呢？这就是Javascript著名的原型链的结果啦。话不多说，先上图：  


![](https://upload-images.jianshu.io/upload_images/12275140-ef84166de7582907.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 "prototype\_fig.png")

  


prototype\_fig.png

  


当我们访问person.name时，发生了什么呢？

  


首先它会访问person对象本身的属性，如果本身没有定义name属性的话，它会去寻找它的

**proto**

属性对象，在这个例子中person的

**proto**

属性对应的是Pproto对象，所以person的

**proto**

指向了Pproto，然后我们发现Pproto对象是具有name属性的，那么person.name就到此为止，返回了jack，但是如果我们又给person加上了一个自身的属性name呢？这时，再次person.name就不会再寻找

**proto**

了，因为person本身已经具有了name属性，而且其值为joker，所以这里会返回joker.



> 我们注意到上图中Pproto的**proto**指向了Object，这是因为每一个通过字面量的方式创建出来的对象它们都默认是Object类的对象，所以它们的**proto**自然指向Object.prototype。

**4.利用prototype实现静态【属性/方法】**

```

```

这里我们在构造函数外多写了一句：Human.prototype.eat = function\(\) {...} 这样以后每个通过Human实例化的对象的**proto**都会指向Human.prototype，并且根据上述原型链知识，我们可以知道只要构造函数中没有定义同名的非静态【属性/方法】，那么每个对象访问say方法时，访问的其实都是Human.prototype.say方法，这样我们就利用prototype实现了类的静态【属性/方法】，所有的对象实现了共有的特性，那就是eat

## 3. 继承

## 4. 作用域链

## 5. 闭包

## 6. 异步机制



