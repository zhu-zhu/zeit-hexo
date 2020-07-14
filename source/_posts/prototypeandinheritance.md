---
title: Prototype & Inheritance
date: 2020-07-01 00:15:01
thumbnail: 931367.jpg
categories: Development
tags:
- JavaScript
- Prototype
- Inheritance
---

## 构造函数与实例化对象

```javascript
function Person() {
    this.name = 'Kangna'
}

var person = new Person()
console.log(person.name) // Kangna
```

这段代码中`Person`是构造函数，而`person`是通过构造函数`Person`创建出来的实例化对象

## prototype

每个函数中都有个`prototype`属性，通过`prototype`定义的方法或属性都可以供该构造函数的实例化对象所共享

```javascript
function Person() {

}

Person.prototype.name = 'Kangna'

var person1 = new Person()
var person2 = new Person()

console.log(person1.name) // Kangna
console.log(person2.name) // Kangna
```

函数中的```prototype```指向一个对象，指向了函数的原型对象，也就是上面代码中```person1``` ```person2```的原型，每个对象（null除外）都会从原型中继承属性

用一张图表示构造函数与原型对象的关系：

![构造函数与原型对象关系](20200715-013019.png)

// TODO