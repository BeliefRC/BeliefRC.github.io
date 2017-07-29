---
title: 重读JavaScript高级程序设计总结(操作符)

date: 2017-07-29 12:54:25

tags:
 JavaScript
---




# js中容易被忽视的知识点



------------

## 一.操作符
逻辑与，逻辑或，这两个操作符都属于短路操作符。即如果第一操作数符能 决定结果，那么就不会对第二个操作数

```javascript
var a=true;
var c=(a&&b)    //报错
console.log(c)   //这行不会执行
```
在上面的代码中，当执行逻辑与操作时会发生错误，因为变量b 没有声明。由于变量a 的值是true，所以逻辑与操作符会继续对变量b 求值。但b 尚未定义，因此就会导致错误。这说明不能在逻辑与操作中使用未定义的值。

```javascript
var a=true;
var c=(a||b)    //不会报错
console.log(c)   //这行会执行
```
这个例子跟前面的例子一样，变量b 也没有定义。但是，由于变量a的值是true，而变量b 永远不会被求值，因此结果就会输出"true"。如果像下面的例子一样，把a 的值改为false，就会导致错误：

```javascript
var a=false;
var c=(a&&b)    //报错
console.log(c)   //这行不会执行
```