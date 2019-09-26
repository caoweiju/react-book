# React Hooks的引入
>Hook 是 React 16.8 的新增特性。它可以让你在不编写 class 的情况下使用 state 以及其他的 React 特性。而且fackbook准备让 Hook 覆盖所有 class 组件的使用场景。

对于开发者而言需要了解Hooks，而尽快熟悉Hooks也是facebook所期待的，这里简单了解一下Hooks的基本概念。

## Hooks简析
可以先看一个最简单的例子，让函数组件也具备使用state来保持组件状态，这样函数组件将不会再被称为无状态组件了：
````jsx
import React, { useState } from 'react';

function Example() {
  // 声明一个叫 “count” 的 state 变量，并声明了修改count这个state的方法setCount。
  const [count, setCount] = useState(0); // 数组解构 使得返回的一对值被分别赋值给count、setCount
  // 这和组件state看起来不大一样，不需要定义属性名state并初始化，直接通过useState来返回一个state。

  // 声明多个 state 变量！
  const [age, setAge] = useState(42);
  const [fruit, setFruit] = useState('banana');
  const [todos, setTodos] = useState([{ text: 'Learn Hooks' }]);

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}
````

在上面的代码中，`useState` 就是一个 Hook。通过在函数组件里调用它来给组件添加一些内部 state。React 会在重复渲染时保留这个 state。`useState `会返回一对值：当前状态和一个让你更新它的函数，你可以在事件处理函数中或其他一些地方调用这个函数。它类似 class 组件的 `this.setState`，但是它不会把新的 state 和旧的 state 进行合并。

**Hook定义**：Hook 是一些可以让你在函数组件里“钩入” React state 及生命周期等特性的函数。Hook 不能在 class 组件中使用 —— 这使得你不使用 class 也能使用 React。

React 内置了一些像 useState 这样的 Hook。开发者也可以创建你自己的 Hook 来复用不同组件之间的状态逻辑。

**Hook规则**：Hook 就是 JavaScript 函数，但是使用它们会有两个额外的规则：
- 只能在函数最外层调用 Hook。不要在循环、条件判断或者子函数中调用。
- 只能在 React 的函数组件或者自定义Hook中调用 Hook。不要在其他 JavaScript 函数中调用。