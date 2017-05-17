---
layout: post
title:  "ES5中reduce的用法"
date:   2017-03-08
categories: JavaScript
tags: reduce
excerpt: 快速理解 Javascript Array Reduce
---

#### JavaScript Reducing 与 for 循环

假如要计算一个数组中所有元素的和，用一个for循环很容易就可以写出计算步骤。如下：

```javascript

var total = 0;
var numbers = [1, 5, 7, 3, 8, 9];
for ( var i = 0; i < numbers.length; i++ ){
    total += numbers[i];
}

```
#### JavaScript Array.prototype.reduce

但是，同样的问题，用reduce如何解决呢？

```javascript

var sum = [1, 5, 7, 3, 8, 9].reduce(
  function(total, num){ return total + num }
  , 0);

```

#### Map Reduce

```javascript

let numbers = [1, 5, 10, 15];
let roots = numbers.map(function(x) {
    return x * 2;
});
// roots is now [2, 10, 20, 30]
// numbers is still [1, 5, 10, 15]

```
map 和 reduce 非常相似，只是map返回的仍是array，reduce可以返回任何格式。


#### 好处

经常用map和reduce可以使代码更紧凑简洁，便于维护。

> 参考：[Understand Javascript Array Reduce in 1 Minute](https://www.airpair.com/javascript/javascript-array-reduce)
