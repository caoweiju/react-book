# 组件
之前说的React是声明式渲染，以及JSX很方便的帮助我们完成渲染元素的编写，这些都离不开一个核心理念：组件；组件允许你将 UI 拆分为独立可复用的代码片段，并对每个片段进行独立构思。
>组件，从概念上类似于 JavaScript 函数。它接受任意的入参（即 “props”），并返回用于描述页面展示内容的 React 元素。

元素：是一个普通的js对象，是react的最小单元。
组件：可以是单个元素，也可以是若干元素的集合，描述UI的可以分块和复用的模块。

## 函数组件和class组件
目前常见的组件包括：
1. 函数组件
2. class组件

### 函数组件
定义组件最简单的方式就是编写 JavaScript 函数：
````js
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}
````
该函数是一个有效的 React 组件，因为它接收唯一带有数据的 “props”（代表属性）对象与并返回一个 React 元素。这类组件被称为“函数组件”，因为它本质上就是 JavaScript 函数。

### class组件
你同时还可以使用 ES6 的 class 来定义组件：
````js
class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
````

## 组件的props
从上面的实例可以知道，props是组件接收的唯一数据源，是调用该组件的地方传入的数据；需要注意：**组件无论是使用函数声明还是通过 class 声明，都决不能修改自身的 props。**

所以在没有使用state的时候，组件就好比纯函数，props传入的数据不变时，那么输出的元素就是不变的。
````js
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

const element = <Welcome name="Sara" />;
ReactDOM.render(
  element,
  document.getElementById('root')
);
````js

代码中，Welcome是一个函数组件，调用时，组件上的所有属性都将会收敛到Welcome组件的参数props中，也就是`props.name='Sara'`，

## 组件的state
除了props的修改可以是的UI自动更新，还有一个就是组件自身的state的使用，state描述的是自身的状态，应该尽可能的和其他的无关数据隔离。
**注意：在react hooks之前，只有class组件具有state，函数组建不能使用state，不过react16.8版本引入hooks，使的函数组建更加灵活了**

````js
class Clock extends React.Component {
  // state = {date: new Date()}  // 根据class fields proposal 也可以这样书写 这是推荐的写法
  constructor(props) {
    super(props); // 这里一定要调用 另外需要传入props 否则constructor内的super后面的内容不能使用this.props
    this.state = {date: new Date()};
  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
````

使用state需要注意的：
1. 不要直接修改 State，而应该`this.setState({comment: 'Hello'});`，或者`this.setState((preState) => ({comment: 'Hello'}));`
2. State 的更新可能是异步的【目前，在事件处理函数内部的 setState 是异步的】，出于性能考虑，React 可能会把多个 setState() 调用合并成一个调用。因为 this.props 和 this.state 可能会异步更新，所以你不要依赖他们的值来更新下一个状态。
    ````js
    // Correct
    this.setState((state, props) => ({
    counter: state.counter + props.increment
    }), () => {
        // do something after steState exected
    });
    ````
3. State 的更新会被合并，执行setState的时候，state值将会和已有的部分浅合并：`{...preState, ...newState}`

### 关于setState的异步问题
调用 setState 其实是可能是异步的【目前，在事件处理函数内部的 setState 是异步的】 —— 不要指望在调用 setState 之后，this.state 会立即映射为新的值。看下面的例子：
````jsx
incrementCount() {
  // 注意：这样 *不会* 像预期的那样工作。
  this.setState({count: this.state.count + 1});
}

handleSomething() {
  // 假设 `this.state.count` 从 0 开始。
  this.incrementCount();
  this.incrementCount();
  this.incrementCount();
  // 当 React 重新渲染该组件时，`this.state.count` 会变为 1，而不是你期望的 3。

  // 这是因为上面的 `incrementCount()` 函数是从 `this.state.count` 中读取数据的，
  // 但是 React 不会更新 `this.state.count`，直到该组件被重新渲染。
  // 所以最终 `incrementCount()` 每次读取 `this.state.count` 的值都是 0，并将它设为 1。

  // 问题的修复参见下面的说明。
}

````
执行三次incrementCount来setState，但是由于异步的原因，呆滞每次获取的state并不是上一次调用incrementCount设置的值，而是当前的初始值0；所以最后的结果是调用了三次，但是结果是1；

解决方法：给 setState 传递一个函数，

传递一个函数可以让你在函数内访问到当前的 state 的值。因为 setState 的调用是分批的，所以你可以链式地进行更新，并确保它们是一个建立在另一个之上的，这样才不会发生冲突：
````jsx
incrementCount() {
  this.setState((state) => {
    // 重要：在更新的时候读取 `state`，而不是 `this.state`。
    return {count: state.count + 1}
  });
}

handleSomething() {
  // 假设 `this.state.count` 从 0 开始。
  this.incrementCount();
  this.incrementCount();
  this.incrementCount();

  // 如果你现在在这里读取 `this.state.count`，它还是会为 0。
  // 但是，当 React 重新渲染该组件时，它会变为 3。
}
````

## 数据流概念
不管是父组件或是子组件都无法知道某个组件是有状态的还是无状态的，并且它们也并不关心它是函数组件还是 class 组件。，通常会被叫做“自上而下”或是“单向”的数据流。组件可以选择把它的 state 作为 props 向下传递到它的子组件中，任何的 state 总是所属于特定的组件，而且从该 state 派生的任何数据或 UI 只能影响树中“低于”它们的组件。

## 组件生命周期和版本变迁
react让我们可以为 class 组件声明一些特殊的方法，当组件挂载或卸载时就会去执行这些方法；

目前可以集中分为以下几个版本：
1. 16.2及以前
2. 16.3版本
3. 16.4及以后

先看看16.2以前版本：
![16.2的生命周期](http://pzdgkztjy.bkt.clouddn.com/book/react/img/reactlifecircle16-2.png)

再看看16.3的生命周期
![16.2的生命周期](http://pzdgkztjy.bkt.clouddn.com/book/react/img/reactlifecircle16-3.png)

再看看16.4及以后的
![16.2的生命周期](http://pzdgkztjy.bkt.clouddn.com/book/react/img/reactlifecircle16-4.png)



