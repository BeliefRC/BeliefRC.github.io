---
title: 重读JavaScript高级程序设计总结(变量，内存问题)

date: 2017-07-29 13:05:37

tags:
 JavaScript
---


# js中容易被忽视的知识点


------------

## 一.基本类型和引用类型的值

- ECMAScript变量可能包含两种不同数据类型的值：**基本类型值（Undefined、Null、Boolean、Number 和String）**和**引用类型值（Object）**。基本类型值指的是简单的数据段，而引用类型值指那些可能由多个值构成的对象。

- 5 种基本数据类型是**按值访问**的，因为可以操作保存在变量中的实际的值。

- 引用类型的值是保存在内存中的对象。~~与其他语言不同，JavaScript 不允许直接访问内存中的位置，也就是说不能直接操作对象的内存空间~~**(这段话如果不理解就先不要理会)**。在操作对象时，实际上是在操作对象的引用而不是实际的对象。为此，引用类型的值是**按引用访问**的

举两个例子：

按值访问：
```javascript
var a=1;
var b=a;
console.log(b);//1
a=2;
console.log(b);//还是1
```
复制前的变量

|   |   |
| ------------ | ------------ |
|   |   |
|  **a** |  ** 1**(Number类型)|

复制后的变量

|   |   |
| ------------ | ------------ |
| **b**  |  ** 1**(Number类型) |
|  **a** |  ** 1**(Number类型)|

在此，a 中保存的值是1。当使用a 的值来初始化b 时，b 中也保存了值1。但a中的1 与b中的1 是**完全独立**的，该值只是a 中1 的一个**副本**。此后，这**两个变量可以参与任何操作而不会相互影响**

------------


按引用访问：
```javascript
var obj1 = new Object();
var obj2 = obj1;
obj1.name = "Nicholas";
alert(obj2.name); //"Nicholas"
```
> 首先，变量obj1 保存了一个对象的新实例。然后，这个值被复制到了obj2 中；换句话说，o**bj1和obj2 都指向同一个对象**。这样，当为obj1 添加name 属性后，可以通过obj2 来访问这个属性，因为这**两个变量引用的都是同一个对象**。下图展示了保存在变量对象中的变量和保存在堆中的对象之间的这种关系。
[![按引用访问](http://wx4.sinaimg.cn/large/85eda507gy1fi0jsykwjvj20fj09ejsn.jpg "按引用访问")](http://wx4.sinaimg.cn/large/85eda507gy1fi0jsykwjvj20fj09ejsn.jpg "按引用访问")


## 二.传递参数
- ECMAScript 中**所有函数的参数都是按值传递**的。也就是说，把函数外部的值复制给函数内部的参数，就和把值从一个变量复制到另一个变量一样。基本类型值的传递如同基本类型变量的复制一样，而引用类型值的传递，则如同引用类型变量的复制一样。有不少开发人员在这一点上可能会感到困惑，因为访问变量有按值和按引用两种方式，而**参数只能按值传递**。

例子：
```javascript
function addTen(num) {
	num += 10;
	return num;
}
var count = 20;
var result = addTen(count);
alert(count); //20，没有变化
alert(result); //30
```
这里的函数addTen()有一个参数num，而**参数实际上是函数的局部变量**。在调用这个函数时，变量count 作为参数被传递给函数，这个变量的值是20。于是，数值20 被复制给参数num 以便在addTen()中使用。在函数内部，参数num 的值被加上了10，但这一**变化不会影响函数外部的count 变量**。参数num 与变量count 互不相识，它们仅仅是具有相同的值。假如num 是按引用传递的话，那么变量count的值也将变成30，从而反映函数内部的修改。


```javascript
function setName(obj) {
	obj.name = "Nicholas";//给参数person设置一个属性
	obj = new Object();//重新定义了一个对象（不再是person了！！！）
	obj.name = "Greg";//新对象的name属性
}
var person = new Object();//person是一个对象
setName(person);//传递参数（对象person）
alert(person.name); //"Nicholas"
```
在把person 传递给setName()后，其name 属性被设置为"Nicholas"。然后，又将一个新对象赋给变量obj，同时将其name属性设置为"Greg"。如果person 是按引用传递的，那么person 就会自动被修改为指向其name 属性值"Greg"的新对象。但是，当接下来再访问person.name 时，显示的值仍然是"Nicholas"。这说明即使在**函数内部修改了参数的值，但原始的引用仍然保持未变。**实际上，当在函数内部重写obj 时，这个**变量引用的就是一个局部对象了。而这个局部对象会在函数执行完毕后立即被销毁**。