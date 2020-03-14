---
title: Event loop & Macrotask & Microtask
date: 2020-03-13 17:03:45
thumbnail: 690236.jpg
categories: Development
tags:
- Development
- JavaScript
---

## Macrotask（宏任务）

主要有：`script(整体代码)` `setTimeout` `setTimeout` `I/O` `UI Rendering` `postMessage` `MessageChannel` `setImmediate(Node.js)`

## Microtask（微任务）

主要有：`Promise.then` `MutationObserver` `Process.nextTick(Node.js)` `MutationObserver`

## Event loop

> Event loop 即事件循环，是浏览器或node帮助解决javascript单线程运行时放置阻塞的解决方案，也就是异步处理。

```javascript
setTimeout(() => {
    console.log(1)
}, 1000)

console.log(2)
```

如果没有事件循环，则会先等待一秒执行了`console.log(1)`后再执行`console.log(2)`，看似没什么关系，但是如果计时器事件多了起来，页面就会出现假死状态（一直等待计时器的执行完毕）。

而Event loop则是解决页面假死的方法，当执行到了`setTimeout`函数时就会告知浏览器有异步任务执行执行完毕后再进行回调，在`setTimeout`计时期间就可以执行别的任务。从而解决javascript的同步问题。

但内部如何处理执行这些任务呢？

1. 从宏任务最前面取出一个任务执行。
2. 执行过程中如果遇到微任务则将其加入微任务队列。
3. 宏任务执行结束后，查看微任务队列中是否有任务。
4. 有则顺序执行完微任务再执行下一个宏任务，若没有则直接进行下一个宏任务。

![](16b2a9d6f846e48e.png)

## 一些例子

### 例子1

```javascript
console.log('script start')    
setTimeout(function() {    
  console.log('setTimeout')    
}, 0)    
Promise.resolve().then(() => {    
  console.log('promise1')    
}).then(() => {    
  console.log('promise2')    
})    
console.log('script end')   

// script start | script end | promise1 | promise2 | setTimeout
```

解析

1. 执行到`console.log('script start')`时输出`script start` 
2. 遇到到`setTimeout`时，将代码回调`console.log('setTimeout')`放入宏任务队列中等待执行
3. 遇到`Promise.resolve().then`时将`console.log('promise1')` `console.log('promise2')`按顺序放入微任务队列
4. 执行到`console.log('script end')`时输出`script end`
5. 第一个宏任务执行完毕，检查是否有微任务。存在`console.log('promise1')` `console.log('promise2')`。输出 `promise1` `promise2`
6. 此时已经没有微任务了，则执行下一个宏任务`console.log('setTimeout')`输出`setTimeout`

### 例子2

```javascript
console.log(1);
setTimeout(function() {
    console.log(2);
}, 0);
new Promise(function(resolve) {
    console.log(3);
    resolve();
}).then(function() {
    console.log(4);
});
console.log(5);
setTimeout(function() {
    new Promise(function(resolve) {
        console.log(6);
        resolve();
    }).then(function() {
        console.log(7);
    });
}, 0);

// 1 3 5 4 2 6 7
```

解析

1. 执行到`console.log(1)`时输出`1`
2. 遇到第一个`setTimeout`时，将代码回调`console.log(2)`放入宏任务队列中等待执行
3. 执行到了`console.log(3)`时输出`3`，将then中的`console.log(4)`放入微任务队列
4. 执行`console.log(5)`时输出`5`
5. 遇到第二个`setTimeout`时，将里面代码回调放入宏任务队列中
6. 第一个宏任务执行完毕，发现有微任务`Promise.then`,执行`console.log(4)`输出`4`
7. 微任务队列执行完毕，执行下一个宏任务，第一个`setTimeout`输出`2`，第二个宏任务执行完成，发现没有微任务，执行下一个宏任务第二个`setTimeout`
8. 执行到`console.log(6)`，将then中的`console.log(7)`放入微任务队列
9. 宏任务执行完毕，存在一个微任务`console.log(7)`执行输出`7`

### async & await

```javascript
async function async1 () {
    console.log('async1 start');
    await async2();
    console.log('async1 end');
}

async function async2 () {
    console.log('async2');
}

console.log('script start');

setTimeout(function () {
    console.log('setTimeout');
}, 0);

async1();

new Promise(function (resolve) {
    console.log('promise1');
    resolve();
}).then(function () {
    console.log('promise2');
});

console.log('script end');
// script start | async1 start | async2 | promise1 | script end | async1 end | promise2 | setTimeout
```

这个例子中包含了`async` `await`，这是`Promise+generator`的语法糖

```javascript
async function async1 () {
    console.log('async1 start');
    await async2();
    console.log('async1 end');
}

// 等价于

function async1 () {
    console.log('async1 start');
    Promise.resolve(async2()).then(() => {
        console.log('async1 end');
    })
}

//这样看就可以容易理解很多
```

这样再来解析

1. 第一轮宏任务执行完后输出结果为 `script start` `async1 start` `async2` `promise1` `script end`
2. 此时宏任务队列有['setTimeout'], 微任务队列有['console.log('async1 end')', 'console.log('promise2')']
3. 要执行宏任务`setTimeout`得先执行完微任务输出为`async1 end` `promise2`
4. 再执行宏任务输出为`setTimeout`

## Done

最近到了学校的实习期间，也开始陆续招工作了，面试几轮下来把我虐的体无完肤，一度的自我怀疑。后来我也总结了一下原因，我在学校时各种学习的一些框架只学习怎么使用而忽略了本质问题框架实现的原理导致只有~广度~没有深度。虽然现在已经找到了工作，但是感觉不是我想要的那种feel，希望能通过这一年的努力让自己提升一下能力找到一份好工作，加油奥利给！

### 参考文章

[一次弄懂Event Loop（彻底解决此类面试问题）](https://juejin.im/post/5c3d8956e51d4511dc72c200?utm_source=gold_browser_extension)
[前端中的事件循环eventloop机制](https://juejin.im/post/5e0adffbe51d4541013f0bf4)
[JS引擎的执行机制（event loop），宏任务和微任务](https://juejin.im/post/5cf879846fb9a07ee27b0584)