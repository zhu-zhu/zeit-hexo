---
title: Deep Copy & Shallow Copy
date: 2020-03-09 17:48:22
thumbnail: 705238.png
categories: Development
tags:
- Development
- JavaScript
---

## ECMAScript的数据类型

* 基本数据类型：String, Number, Boolean, Undefined, Null
* 引用数据类型：Object(Array, Date, RegExp, Function)

## 基本数据类型

基本数据类型是直接放在栈（stack）中，直接将值存放，直接访问值

## 引用数据类型

引用数据类型是放在堆（heap）中，变量相当与指针（js无指针"比喻"），指向堆中地址。

示例：

```javascript
var person1 = { name: 'kangna' }
var person2 = { name: 'Tom' }
var person3 = { name: 'jerry' }
```

栈区           |               | 堆区
------------- | ------------- | -------------
person1       |     ----->    | { name: 'kangna' }
person2       |     ----->    | { name: 'Tom' }
person2       |     ----->    | { name: 'jerry' }

## 传值 & 传址

### 传值

基本数据类型的赋值（=）是传值，在内存中新建一个栈内存，然后把赋值传入新栈中

示例

```javascript
var a = 10
var b = a

b ++ 

console.log(a, b) // 10 11
```

初始化b之前的栈区|               | 初始化b之后的栈区
------------- | ------------- | -------------
a -------> 10 |     ----->    | a -------> 10
              |               | b -------> 10

可以看出基本数据类型的两个变量都是互不影响的

### 传址

引用数据类型的赋值（=）是传址，意思是引用类型的赋值是指将栈中的地址赋值给对方，两个变量归根结底是指向了同一个对象，因此两者之间操作都会互相影响数据

示例

```javascript
var a = {}
var b = a

a.name = 'jojo'
console.log(a.name, b.name) // jojo jojo

b.age = 20
console.log(a.age, b.age) // 20 20

a === b // true
```

初始化b之前的栈区|               | 初始化b之后的栈区
------------- | ------------- | -------------
a -------> 地址|               | a -------> 地址
              |               | b -------> 地址

可以看出a与b都是指向同一个“地址”

## 浅拷贝与赋值（=）的区别

示例

```javascript
var obj1 = {
    name: 'kangna',
    tags: {
        tag: [1, [2]]
    }
}

var obj2 = obj1

obj2.name = 'miku'
obj2.tags.tag[1][0] = 3

console.log(obj1, obj2)
// obj1 = {
//     name: 'miku',
//     tags: {
//         tag: [1, [3]]
//     }
// }

// obj2 = {
//     name: 'miku',
//     tags: {
//         tag: [1, [3]]
//     }
// }

function shallowCopy(src) {
    var dst = {};
    for (var prop in src) {
        if (src.hasOwnProperty(prop)) {
            dst[prop] = src[prop];
        }
    }
    return dst;
}

var obj1 = {
    name: 'kangna',
    tags: {
        tag: [1, [2]]
    }
}

var obj3 = shallowCopy(obj1)

obj3.name = 'miku'
obj3.tags.tag[1][0] = 3

console.log(obj1, obj3)
// obj1 = {
//     name: 'kangna',
//     tags: {
//         tag: [1, [3]]
//     }
// }

// obj3 = {
//     name: 'miku',
//     tags: {
//         tag: [1, [3]]
//     }
// }

```

从示例中可以得到的结论

*             | 和原数据是否指向统一对象 | 第一层数据为基本数据类型 | 原数据中包含子对象
:-------------: | :------------------: | :--------------------: | :-------------:
赋值           |          是          | 修改会影响原数据一同修改 | 修改会影响原数据一同修改
浅拷贝         |          否          | 修改不会影响原数据一同修改| 修改会影响原数据一同修改
深拷贝         |          否          | 修改不会影响原数据一同修改| 修改不会影响原数据一同修改

## 深拷贝

上文已经解释深拷贝无论第一层数据为基本数据类型或者原数据中包含子对象都不会影响到修改数据而导致原数据一同修改

### 如何深拷贝呢

大致思路是通过递归一直循环赋给另一个对象

```javascript
const deepCopyFunction = (inObject) => {
    let outObject, value, key
    // outObject 赋值对象

    if (typeof inObject !== "object" || inObject === null) {
        // 判断入参为对象或者不为null
        return inObject
    }

    outObject = Array.isArray(inObject) ? [] : {}
    // 判断参数是否是数组是赋值为[]否为josn

    for (key in inObject) {
        value = inObject[key]

        outObject[key] = (typeof value === "object" && value !== null) ? deepCopyFunction(value) : value
        // 判断对象的value是否对象且不为null，如果成立则递归将value作为参数执行下去，否则直接赋值
    }

    return outObject
}

var obj1 = {
    name: 'kangna',
    tags: ['coding', 'overwatch'],
    expression: {
        arr: [1, 2, 3]
    }
}

var obj2 = deepCopyFunction(obj1)

obj2.name = 'miku',
obj2.tags[1] = 'work'
obj2.expression.arr[0] = 4

console.log(obj1, obj2)
// obj1 = {
//     name: 'kangna',
//     tags: ['coding', 'overwatch'],
//     expression: {
//         arr: [1, 2, 3]
//     }
// }

// obj2 = {
//     name: 'miku',
//     tags: ['coding', 'work'],
//     expression: {
//         arr: [4, 2, 3]
//     }
// }
```

输出的结果双方都互相不影响

## Done

参考文章
https://juejin.im/post/59ac1c4ef265da248e75892b#heading-3