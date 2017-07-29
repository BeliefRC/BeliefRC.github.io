---
title: css中常用但是容易忘记的知识点

date: 2017-07-29 12:55:11

tags: css
---

# css中那些常用但是容易忘记的知识点

**目录：**




------------
##一. CSS实现文字过长显示省略号
1.前提是父级容器宽度固定,只能实现单行省略
```css
overflow: hidden;/**超出边框的文字消失**/
text-overflow:ellipsis; /**超出则在文字最后现实省略号**/
white-space: nowrap;/**文字不换行**/
```
2.多行文本溢出显示省略号
```css
display: -webkit-box;/**对象作为伸缩盒子模型显示**/
-webkit-box-orient: vertical;/** 设置或检索伸缩盒对象的子元素的排列方式**/
-webkit-line-clamp: 3;/**显示的行数**/
overflow: hidden;
```
**这个方法不兼容ie，verflow: hidden在ie6、ie7无效**