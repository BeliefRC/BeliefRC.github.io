---
title: '''重读JavaScript高级程序设计总结-DOM'''
date: 2017-08-31 13:26:02
tags: JavaScript DOM
---

# 一.Node类型
**JavaScript中的所有节点类型都继承自Node类型，因此所有节点类型都共享着相同的基本属性和方法。**
首先了解一个知识点：
> 文档节点是每个文档的根节点，在HTML页面中，文档元素始终都是`<html>`元素


总共有12种节点类型，一下是常见节点属性的总结（剩余的大家可以自己总结）

### 一、nodeName 属性: 节点的名称，是只读的。
1. 元素节点的 nodeName 与标签名相同
2. 属性节点的 nodeName 是属性的名称
3. 文本节点的 nodeName 永远是 #text
4. 文档节点的 nodeName 永远是 #document

### 二、nodeValue 属性：节点的值
1. 元素节点的 nodeValue 是 undefined 或 null
2. 文本节点的 nodeValue 是文本自身
3. 属性节点的 nodeValue 是属性的值

### 三、nodeType 属性: 节点的类型，是只读的。以下常用的几种结点类型:

| 元素类型  |  节点类型 |
| ------------ | ------------ |
| 元素  | 1  |
| 属性  | 2  |
| 文本  | 3  |
|  注释 |  8 |
| 文档  |  9 |


# 二.创建节点
### 1.createElement
```javascript
document.createElement('tagname')
```
此方法会创建一个元素节点，tagname为标签名 如p,span,div。仅仅是创建一个节点，**不会被插入到dom中**

### 2.createTextNode
```javascript
document.createTextNode('str ')
```
此方法会创建一个文本节点，str是文本的内容，如 hello world。仅仅是创建一个节点，**不会被插入到dom中**

### 2.cloneNode
```javascript
let newNode=oldNode.cloneNode(bool)
```
此方法会克隆一个节点，接受一个布尔值值作为参数，ture或者false。
true代表还会克隆其所有的子节点（包括文本节点）
false代表仅仅克隆其本身，不包括子节点
该方法会**还会复制该节点的属性**，例如，class，id，title，如果有id属性，记得手动去更改其中一个节点的id
仅仅是创建一个节点，**不会被插入到dom中**

# 三.删除节点
### 1.removeChild
移除节点，返回被移除的节点
```javascript
//删除第一个子节点
let returnNode=parentNode.removeChild(parentNode.firstChild)
```
```javascript
//删除最后一个子节点
let returnNode=parentNode.removeChild(parentNode.lastChild)
```
### 2.replaceChild
replaceChild()方法接受的两个参数是：要插入的节点和要替换的节点。要替换的节点将由这个方法返回并从文档树中被移除，同时由**要插入的节点占据其位置**。
```javascript
//替换第一个子节点
let returnNode=parentNode.replaceChild(newNode，parentNode.firstChild)
```
```javascript
//替换最后一个子节点
let returnNode=parentNode.replaceChild(newNode，parentNode.lastChild)
```

# 四.插入节点
### 1.appendChild
用于向childNodes列表的末尾添加一个节点,appendChild()会返回新增的节点
```javascript
let returnNode=parentNode.appendChild(newNode)
returnNode==newNode		//true
returnNode==parentNode.lastChild		//true
```
如果传入到appendChild()中的节点已经是文档的一部分了，那结果就是将该节点从原来的位置**转移**到新位置。
```javascript
let returnNode=parentNode.appendChild(parentNode.firstNode)
returnNode==parentNode.firstNode		//false
returnNode==parentNode.lastChild		//true
```


### 2.insertBefore
被插入的节点会变成参照节点的**前一个同胞节点**（previousSibling），同时被方法返回。如果参照节点是null，则insertBefore()与appendChild()执行相同的操作
```javascript
let returnNode=parentNode.appendChild(newNode,parentNode.firstNode)
returnNode==parentNode.firstNode		//ture
```


#  五.节点关系
> 每个节点都有一个childNodes属性，其中保存着一个NodeList对象。**NodeList是一种类数组对象**，用于保存一组有序的节点，可以通过位置来访问这些节点。请注意，虽然可以通过方括号语法来访问NodeList的值，而且这个对象也有length属性，但它并不是Array的实例。

```javascript
childNodes 		//所有子节点
parentNode		//父节点
parentElement		//与parentNode的区别在于，其父节点必须是一个元素节点，如果不是，则返回null
firstChild		//第一个子节点
lastChild		//最后一个子节点
previousSibling		//前一个兄弟节点
previousElementSibling		//与previousSibling的区别在于，其前一个节点必须是一个元素节点，如果不是，则返回null
nextSibling		//后一个兄弟节点
nextElementSibling		//与nextSibling的区别在于，其后一个节点必须是一个元素节点，如果不是，则返回null
hasChildNodes()		//在节点包含一或多个子节点的情况下返回true
ownerDocument 		//属性指向表示整个文档的文档节点
```

列表中第一个节点的previousSibling属性值为null，而列表中最后一个节点的nextSibling属性的值同样也为null，如果列表中只有一个节点，那么该节点的nextSibling和previousSibling都为null。

[![关系图](http://wx3.sinaimg.cn/mw690/85eda507gy1fj2nkeo37sj20e107e3zt.jpg "关系图")](http://wx3.sinaimg.cn/mw690/85eda507gy1fj2nkeo37sj20e107e3zt.jpg "关系图")



# 六.元素属性

### 1.setAttribute()
这个方法接受两个参数：要设置的特性名和值。如果特性已经存在，setAttribute()会以指定的值替换现有的值；如果特性不存在，setAttribute()则创建该属性并设置相应的值。
```javascript
 someNode.setAttribute('id','value')
 someNode.setAttribute('class','value')
```
也可以这样直接设置属性
```javascript
 someNode.id='value'
 someNode.class='value'
 //不可以设置自定义属性
```

### 2.getAttribute()
getAttribute()的特性名与实际的特性名相同,如果给定名称的特性不存在，getAttribute()返回null。getAttribute()方法也可以取得自定义特性
```javascript
someNode.getAttribute("id")
someNode.getAttribute("class")
someNode.getAttribute("title")
```

### 3.removeAttribute()
这个方法用于彻底删除元素的特性。调用这个方法不仅会清除特性的值，而且也会从元素中完全删除特性，ie6前不支持。
```javascript
someNode.removeAttribute("id")
```
### 4.attributes属性
这个方法不常用，它返回的是一个包含该节点所有属性的类数组对象

# 七.获取节点
这个是最基础的就不再赘述
```javascript
document.getElementById()
document.getElementsByClassName()		//返回HTMLCollection对象（类数组对象）
document.getElementsByTagName()		//返回HTMLCollection对象（类数组对象）
document.getElementsByName()		//返回HTMLCollection对象（类数组对象）
querySelector()		//方法接收一个CSS选择符
querySelectorAll()		//接收的参数与querySelector()方法一样，都是一个CSS选择符，但返回的是所有匹配的元素而不仅仅是一个元素。这个方法返回的是一个NodeList的实例。
```
# 八.document对象的特殊集合

>-  　document.anchors，包含文档中所有带name特性的`<a>`元素；
- 　document.forms，包含文档中所有的`<form>`元素，与 document.getElementsByTagName（"form"）得到的结果相同；
- 　document.images，包含文档中所有的`<img>`元素，与document.getElementsByTagName（"img"）得到的结果相同；
- 　document.links，包含文档中所有带href特性的`<a>`元素。
~~- 　document.applets，包含文档中所有的`<applet>`元素，因为不再推荐使用`<applet>`元素，所以这个集合已经不建议使用了~~；