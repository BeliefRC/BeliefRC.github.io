---
title: Redux原理分析
date: 2018-03-20 10:12:29
tags: Redux React
---

# Redux原理分析


**Redux是什么**
	很多人认为redux必须要结合React使用，其实并不是的，Redux 是 JavaScript 状态容器，只要你的项目中使用到了状态，并且状态十分复杂，那么你就可以使用Redux管理你的项目状态，它可以使用在react中，也可以使用中在Vue中，当然也适用其他的框架。

## 一.redux的工作原理
先上图（图片源于网络）
[![redux](http://wx2.sinaimg.cn/mw690/85eda507gy1fpkak6pk3pj20p10e1abo.jpg "redux")](http://wx2.sinaimg.cn/mw690/85eda507gy1fpkak6pk3pj20p10e1abo.jpg "redux")
1. 首先我们找到最上面的state
2. 在react中state决定了视图（ui），state的变化就会调用React的`render（）`方法，从而改变视图
3. 用户通过一些事件（如点击按钮，移动鼠标）就会像reducer派发一个action
4. reducer接收到action后就会去更新state
5. store是包含了所有了state，可以把他看做所有状态的集合

**当然，现在可能看不懂这在瞎说啥，但是等把这篇文章看完再来这个图，和这段话，就会有恍然大明白的感觉**


### 1.action
action本质上就是一个对象，它一定有一个名为`type`的key 如`{type: 'add'}`,`{type: 'add'}`就是一个action
但是我们只实际工作中并不是直接用action ，而是使用**action创建函数**，（千万别弄混淆），
顾名思义action创建函数就是一个函数，它的作用就是返回一个action，如：
```
function add() {
    return {type: 'add'}
}
```
### 2.reducer
reducer其实就是一个函数，它接收两个参数，第一个参数是需要管理的状态state，第二个是action。**reducer会根据传入的action的type值对state进行不同的操作，然后返回一个新的state，而不是在原有state的基础上进行修改,但是如果遇到了未知的（不匹配的）action，就会返回原有的state，不进行任何改变**。
```
function reducer(state = {money: 0}, action) {
    //返回一个新的state可以使用es6提供的Object.assign()方法，或扩展运算符（此方法需要babel-preset-state-3支持）
    switch (action.type) {
        case '+':
            return Object.assign(state, {money: state.money + 1});
        case '-':
            return {...state, ...{money: state.money - 1}};
        default:
            return state;
    }
}
```

### 3.store
你可以把store想成一个状态树，它包含了整个redeux应用的所有状态。
我们使用redux提供的`createStore`方法生成store
```
import {createStore} from 'redux';
const store = createStore(reducer);
```
store提供了几个方法供我们使用，下面是我们常用的3个：
```
store.getState();//获取整个状态树
store.dispatch();//改变状态，改变state的唯一方法
store.subscribe();//订阅一个函数，每当state改变时，都会去调用这个函数
```

接下来演示一个redux的完整应用，并且说明这三个方法该怎么用
```
import {createStore} from 'redux';

//给初始状态一个默认值：{money: 0}
function reducer(state = {money: 0}, action) {
    //返回一个新的state可以使用es6提供的Object.assign()方法，或扩展运算符（此方法需要babel-preset-state-3支持）
    switch (action.type) {
        case '+':
            return Object.assign(state, {money: state.money + 1});
        case '-':
            return {...state, ...{money: state.money - 1}};
        default:
            return state;
    }
}

//action创建函数，返回了一个action
function add() {
    return {type: '+'}
}

function subtraction() {
    return {type: '-'}
}

//创建单一状态树
const store = createStore(reducer);

console.log(store.getState());//{money: 0},初始的状态，没有任何改变（通过getState来获取目前的状态）

//store通过dispatch这个方法，并且传入action作为参数，对store进行了改变
store.dispatch(add());
console.log(store.getState());//{money: 1}，reducer接受到了 '+' 这个命令，就捡到了一块钱

store.dispatch(subtraction());
console.log(store.getState());//{money: 0}，reducer接受到了 '-' 这个命令，又掉了一块钱

store.dispatch({type:'我是来捣乱的'});
console.log(store.getState());//{money: 0}，reducer接受到了一个不识别命令，返回原有的state
```

这个时候我们就会发现几个问题：
1. 每次状态改变的时候我们都要`console.log()`才能知道改变后的状态，
2. action的type实际上就是一个字符串，如果我们需要进行项目维护，更改type的值，就需要在多处进行修改，变得十分麻烦。

这个时候我们就可以使用`store.subscribe()`来订阅一个事件，代替我们在每次`dispatch`后都要`console.log()`后才能知道改变后的状态
```
function listen() {
    console.log(store.getState());
}

store.subscribe(listen);
```
将type维护成常量，这样我们在日后的维护过程中只需要对常量进行维护就可以了，我们目前这个demo使用到type的地方太少可能感觉不到，可是在实际项目中这个方法却非常的实用
```
const ADD = '+', SUBTRACTION = '-';
```
我们优化后的代码如下：
```
import {createStore} from 'redux';

//定义常量方便维护
const ADD = '+', SUBTRACTION = '-';

//给初始状态一个默认值：{money: 0}
function reducer(state = {money: 0}, action) {
    //返回一个新的state可以使用es6提供的Object.assign()方法，或扩展运算符（此方法需要babel-preset-state-3支持）
    switch (action.type) {
        case ADD:
            return Object.assign(state, {money: state.money + 1});
        case SUBTRACTION:
            return {...state, ...{money: state.money - 1}};
        default:
            return state;
    }
}

//action创建函数，返回了一个action
function add() {
    return {type: ADD}
}

function subtraction() {
    return {type: SUBTRACTION}
}

//打印改变后的状态
function listen() {
    console.log(store.getState());
}

//创建单一状态树
const store = createStore(reducer);

//订阅listen，每次dispatch后都会执行listen，从而打印状态（只有在执行dispatch后才会执行，状态初始化的时候并不会执行）
store.subscribe(listen);

console.log(store.getState());//初始的状态，没有任何改变

//store通过dispatch这个方法，并且传入action作为参数，对store进行了改变
store.dispatch(add());
store.dispatch(subtraction());
store.dispatch({type: '我是来捣乱的'});

/*控制台的打印结果如下：
{money: 0}
{money: 1}
{money: 0}
{money: 0}*/

```

**补充：**
**一个应用只能有一个store**，这个时候就会有一个问题 ，如果有多个reducer分别来处理不同的状态，而createStore是能接受一个reducer，这个时候我们就需要redux提供的`combineReducers`方法来将多个reducer结合成一个reducer
```
import {combineReducers} from 'redux';

const reducerFamily=combineReducers({
    reduceSon,
    reduceDaughter,
    reducerFather,
	reducerMother
})
const store = createStore(reducerFamily);
```



