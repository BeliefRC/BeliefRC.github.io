---
title: 重读JavaScript高级程序设计总结-Function类型
date: 2017-08-14 09:58:29
tags: JavaScript
---


# 一.函数的内部属性
### 1.arguments
###### 1.arguments是一个**类数组对象**，他代表函数参数的数组
```javascript
function length(){
	console.log(c.length)
}
say()//0
say('hello'')//1
say('hello','world')//2
```
```javascript
function say(){
	console.log(arguments[0],arguments[0])
}
say('hello','world')//helloworld
```
###### 2.arguments.callee,
arguments对象还有一个名叫callee的属性，该属性是一个**指针**，**指向拥有这个arguments对象的函数**。
此方法可以消除函数之间的强耦合，例如阶乘函数的递归运算。
**该属性在严格模式下不可用**

```javascript
function factorial(num){
	if(num<=1){
		return 1
	}else{
		return num*arguments.callee(num-1)
	}
} 
```


### 2.caller
这个属性中保存着调用当前函数的函数的引用，如果是在全局作用域中调用当前函数，它的值为null。
```javascript
function inner(){
	console.log(inner.caller)
	//console.log(arguments.callee.caller)
}
function outer(){
	inner()
}
outer()//function outer(){inner() }
```

### 3.this
this指向当**前执行环境**

### 3.call(),apply()
这两个方法能**劫持**另外一个**对象的方法**，**继承**另外一个**对象的属性.**

```javascript
Function.apply(obj,arr)
Function.call(obj,param1,param2,param3)
```

call和apply的意思一样,只不过是**参数列表不一样.**
方法接受第的一个参数将**代替**Function类里的this对象


