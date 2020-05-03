---
title: Ternary Operator
date: 2020-05-03 22:03:25
thumbnail: 1068790.jpg
categories: Development
tags:
- JavaScript
- Ternary Operator
---

## 基础语法

三元运算符的基础语法十分的简单，看看就懂了

```javascript
condition ? exprIfTrue : exprIfFalse

// Equivalent to:

if(condition) {
    return exprIfTrue
}else {
    return exprIfFalse
}

```

### 条件链

这个三元操作符是右结合的，也就是说你可以像这样把它链接起来(格式：`?:?:?:....`)

```javascript
return condition1 ? value1
     : condition2 ? value2
     : condition3 ? value3
     : value4

// Equivalent to:

if (condition1) { return value1; }
else if (condition2) { return value2; }
else if (condition3) { return value3; }
else { return value4; }

```

## 拓展

偶然之间我看见了一段代码

```javascript
slice.call(
    isSimple && !maybeID && element.getElementsByClassName ?
        maybeClass ? element.getElementsByClassName(nameOnly) :
        element.getElementsByTagName(selector) :
        element.querySelectorAll(selector)
 )
```

其中的三元运算符的格式是 ? ? : :，这和教科书的完全不一样啊，所以就写个例子搞懂他

代码：

```javascript
function example() {
    var condition1 = true, condition2 = true
    var value1 = 'value1', value2 = 'value2', value3 = 'value3';
    console.log(condition1 ? condition2 ? value1 : value2 : value3);
}
```

返回值：

condition1    |condition2     |echo
------------- | ------------- | -------------
true          |true           | value1
true          |false          | value2
false         |true           | value3
false         |false          | value3

从返回值可以得出上面的代码等价于

```javascript
console.log(condition1 ? ( condition2 ? value1 : value2 ) : value3);
```

这样看来就明了很多

### 解读

[MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Conditional_Operator)的文档中提到过一个关键字【右结合】,在查阅了一些资料和案例中大概的理解了其表达的意思。

**从最右边取":", 然后看它左边相邻的符号，如果是"?",那么它和这个"?"结合起来，可以用一个（）把它俩包住；如果左边相邻的符号是":"，那么取左边的":"，再重复这个判断.**

示例：

```javascript
?:?:?:?:

// Equivalent to:

?:(?:(?:(?:)))
```

```javascript
???:::

// Equivalent to:

?(?(?:):):
```

## Done

三元运算符在开发中很常用，可以大程度的减少一些不必要的`if else`也可以让代码看起来美观。但如果写了过多的`?:?:?:` `???:::`的情况下，自己用用还是可以的，在团队合作开发下还是尽量少用，不然很容易绕弯子🐍。

### 参考文章

[Conditional (ternary) operator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Conditional_Operator)
