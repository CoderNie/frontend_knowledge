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



## 3. 继承

## 4. 作用域链

## 5. 闭包

## 6. 异步机制



