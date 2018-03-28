---
title: Redux原理分析
date: 2018-03-20 10:12:29
tags:
- Redux
- React
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
```JavaScript
function add() {
    return {type: 'add'}
}
```
### 2.reducer
reducer其实就是一个函数，它接收两个参数，第一个参数是需要管理的状态state，第二个是action。**reducer会根据传入的action的type值对state进行不同的操作，然后返回一个新的state，而不是在原有state的基础上进行修改,但是如果遇到了未知的（不匹配的）action，就会返回原有的state，不进行任何改变**。
```JavaScript
function reducer(state = {money: 0}, action) {
    //返回一个新的state可以使用es6提供的Object.assign()方法，或扩展运算符（此方法需要babel-preset-state-3支持）
    switch (action.type) {
        case '+':
            return Object.assign({}, state, {money: state.money + 1});
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
```JavaScript
import {createStore} from 'redux';
const store = createStore(reducer);
```
store提供了几个方法供我们使用，下面是我们常用的3个：
```JavaScript
store.getState();//获取整个状态树
store.dispatch();//改变状态，改变state的唯一方法
store.subscribe();//订阅一个函数，每当state改变时，都会去调用这个函数
```

接下来演示一个redux的完整应用，并且说明这三个方法该怎么用
```JavaScript
import {createStore} from 'redux';

//给初始状态一个默认值：{money: 0}
function reducer(state = {money: 0}, action) {
    //返回一个新的state可以使用es6提供的Object.assign()方法，或扩展运算符（此方法需要babel-preset-state-3支持）
    switch (action.type) {
        case '+':
            return Object.assign({}, state, {money: state.money + 1});
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
```JavaScript
function listen() {
    console.log(store.getState());
}

store.subscribe(listen);
```
将type维护成常量，这样我们在日后的维护过程中只需要对常量进行维护就可以了，我们目前这个demo使用到type的地方太少可能感觉不到，可是在实际项目中这个方法却非常的实用
```JavaScript
const ADD = '+', SUBTRACTION = '-';
```
我们优化后的代码如下：
```JavaScript
import {createStore} from 'redux';

//定义常量方便维护
const ADD = '+', SUBTRACTION = '-';

//给初始状态一个默认值：{money: 0}
function reducer(state = {money: 0}, action) {
    //返回一个新的state可以使用es6提供的Object.assign()方法，或扩展运算符（此方法需要babel-preset-state-3支持）
    switch (action.type) {
        case ADD:
            return Object.assign({}, state, {money: state.money + 1});
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
```JavaScript
import {combineReducers} from 'redux';

const reducerFamily=combineReducers({
    reduceSon,
    reduceDaughter,
    reducerFather,
	reducerMother
})
const store = createStore(reducerFamily);
```

## 二.在React中使用redux
如果会react，那么也一定知道creact-react-app这个官方脚手架工具，首先使用creact-react-app创建一个项目，然后删除src目录下所有文件，接下来就可以愉快的敲代码了。

在src下创建三个文件
**index.js**
```JavaScript
import React from 'react'
import ReactDOM from 'react-dom'
import {createStore} from 'redux'
//引入我们的reducer和action创建函数
import {reducer, add, subtraction} from './index.redux'
import App from './App'

//创建store
const store = createStore(reducer);

//store.subscribe方法接受的参数是一个函数，
// 所以将ReactDOM.render方法写在一个函数内
function listen() {
    //将store，action创建函数分别以属性的方式传递给子组件App
    ReactDOM.render(<App store={store} add={add} subtraction={subtraction}/>,
        document.querySelector('#root'));
}

//因为刚进入页面没有dispatch操作改变store，
// 所以listen不会执行，我们需要手动调用一次
listen();

//重点，改变了store，页面就会重新渲染，
// 可以试试不写这行代码会是怎样的效果
store.subscribe(listen);
```

**App.js**
```JavaScript
import React from 'react'

export default class App extends React.Component {
    render() {
        //从属性中获取store，action创建函数
        const {store, add, subtraction} = this.props;
        //获取state
        let state = store.getState();
        return <div>
            <h1>我有{state.money}元</h1>

            {/*通过store.dispatch方法改变store，从而页面也会改变*/}
            <button onClick={() => {store.dispatch(add())}}>
                捡了一块钱
            </button>

            <button onClick={() => {store.dispatch(subtraction())}}>
                掉了一块钱
            </button>
        </div>
    }
}
```
**index.redux.js**
```JavaScript
//定义常量方便维护
const ADD = '+', SUBTRACTION = '-';

//给初始状态一个默认值：{money: 0}
export function reducer(state = {money: 0}, action) {
    //返回一个新的state可以使用es6提供的Object.assign()方法，或扩展运算符（此方法需要babel-preset-state-3支持）
    switch (action.type) {
        case ADD:
            return Object.assign({}, state, {money: state.money + 1});
        case SUBTRACTION:
            return {...state, ...{money: state.money - 1}};
        default:
            return state;
    }
}

//action创建函数，返回了一个action
export function add() {
    return {type: ADD}
}

export function subtraction() {
    return {type: SUBTRACTION}
}

```
效果图
[![效果图](http://wx4.sinaimg.cn/mw690/85eda507gy1fplbvikkn8g20bc06qdgy.gif "效果图")](http://wx4.sinaimg.cn/mw690/85eda507gy1fplbvikkn8g20bc06qdgy.gif "效果图")

这样我们就将redux和react结合了起来但是这样我们可能会觉得麻烦，因为我们要将store和action创建函数传给子组件，当我们的action比较多时，子组件比较多时，就需要将store和大量的action创建函数一层层的多次传递下去。这样就会十分麻烦，因此我们就可以使用`react-redux`这个库来帮助我们实现这个麻烦的过程

## 三.react-redux的使用
### 1.Provider
`react-redux`给我们提供了一个`Provider`组件，我们可以把这个组件写在最外层，这样被`Provider`包裹的所有组件都可以通过`props`来获取`state`，无论组个组件藏得多么深。
而`Provider`组件只接受一个属性，那就是`store`

那么我们index.js的代码就变成下面这样了：
```javascript
import React from 'react'
import ReactDOM from 'react-dom'
import {createStore} from 'redux'
import {Provider} from 'react-redux'
import {reducer} from './index.redux'
import App from './App'

//创建store
const store = createStore(reducer);

ReactDOM.render(
    <Provider store={store}>
        <App/>
    </Provider>,
    document.querySelector('#root'));
```
### 2.connect
当然，只有`Provider`组件是不够的，我们还需要`connect`来帮助我们获取state和action，没错，**`connect`就是帮助我们获取state和action的**

那么问题就来了，我们的组件可不是需要项目中所有的state和action，只需要其中的一部分就可以了，所以`connect`会接受两个参数，**第一个参数它可以帮我们筛选state，第二个参数可以帮我们筛选action。**
我们可以把这两个参数写成函数的形式，
参数1，
```javascript
function mapStateToProps(state) {
    return {
        money: state.money
    }
}
```
参数2，
```javascript
function actionCreators() {
    return {
        subtraction,
        add
    }
}
```
我们可以发现这两个函数都是返回了一个对象，第一个函数返回了我们需要的state，第二个函数返回了我们需要的action创建函数

那么app.js 的代码就变成这样了：
```javascript
import React from 'react'
import {connect} from 'react-redux'
import {add, subtraction} from './index.redux'

class App extends React.Component {
    render() {
        //因为connect的原因，state和action我们已经可以从属性中获取了
        const {money, add, subtraction} = this.props;

        return <div>
            <h1>我有{money}元</h1>

            {/*这个时候不需要我们dispatch了*/}
            <button onClick={add}>
                捡了一块钱
            </button>

            <button onClick={subtraction}>
                掉了一块钱
            </button>
        </div>
    }
}

//connect所需要的参数
//函数返回的我们需要的状态，我们需要money，就从state中取出money
//假如我们还需要house，就增加一个house:state.house
function mapStateToProps(state) {
    return {
        money: state.money
    }
}

//connect需要的第二参数
//返回我们需要的action创建函数
function actionCreators() {
    return {
        subtraction,
        add
    }
}

//上面两个函数返回的都是对象

//通过connect将state和action创建函数当做属性传递给组件
export default App = connect(mapStateToProps, actionCreators())(App);

```

如果熟悉es6装饰器的语法那就更好了，可以使我们的代码变得更优雅
app.js
```javascript
import React from 'react'
import {connect} from 'react-redux'
import {add, subtraction} from './index.redux'

@connect(
    state => ({money: state.money}),
    {
        subtraction,
        add
    })
export default class App extends React.Component {
    render() {
        //因为connect的原因，state和action我们已经可以从属性中获取了
        const {money, add, subtraction} = this.props;

        return <div>
            <h1>我有{money}元</h1>

            {/*这个时候不需要我们dispatch了*/}
            <button onClick={add}>
                捡了一块钱
            </button>

            <button onClick={subtraction}>
                掉了一块钱
            </button>
        </div>
    }
}
```
**看到这里再回头看看最开始图片，就能搞清楚redux的工作流程究竟是怎样的。**











