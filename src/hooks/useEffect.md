# useEffect
>Effect Hook 可以让你在函数组件中执行副作用操作，而数据获取，设置订阅以及手动更改 React 组件中的 DOM 都属于副作用  
**如果你熟悉 React class 的生命周期函数，你可以把 useEffect Hook 看做 componentDidMount，componentDidUpdate 和 componentWillUnmount 这三个函数的组合。**


## useEffect简介
先看一个基础的例子：
````jsx
import React, { useState, useEffect } from 'react';

function Example() {
  const [count, setCount] = useState(0);

  // Similar to componentDidMount and componentDidUpdate:
  useEffect(() => {
    // Update the document title using the browser API
    document.title = `You clicked ${count} times`;
  });

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
上面的代码使用了useState和useEffect，其中useState创建了一个state叫count，以及修改count的方法；而useEffect的使用是提供一个函数作为参数，该函数会在componentDidMount和componentDidUpdate的时候调用；所以上面代码的就是最开始初始化和每次点击button的时候document.title都会被重新设置为新的值。

- useEffect 做了什么？ 通过使用这个 Hook，你可以告诉 React 组件需要在渲染后执行某些操作。React 会保存你传递的函数（我们将它称之为 “effect”），并且在执行 DOM 更新之后调用它。在这个 effect 中，我们设置了 document 的 title 属性，不过我们也可以执行数据获取或调用其他命令式的 API。
- 为什么在组件内部调用 useEffect？ 将 useEffect 放在组件内部让我们可以在 effect 中直接访问 count state 变量（或其他 props）。我们不需要特殊的 API 来读取它 —— 它已经保存在函数作用域中。Hook 使用了 JavaScript 的闭包机制，而不用在 JavaScript 已经提供了解决方案的情况下，还引入特定的 React API。
- useEffect 会在每次渲染后都执行吗？ 是的，默认情况下，它在第一次渲染之后和每次更新之后都会执行。（我们稍后会谈到如何控制它。）你可能会更容易接受 effect 发生在“渲染之后”这种概念，不用再去考虑“挂载”还是“更新”。React 保证了每次运行 effect 的同时，DOM 都已经更新完毕。

**在 React 组件中有两种常见副作用操作：需要清除的和不需要清除的**

1. 不需要清除：有时候我们只想在 React 更新 DOM 之后运行一些额外的代码。比如发送网络请求，手动变更 DOM，记录日志，这些都是常见的无需清除的操作。因为我们在执行完这些操作之后，就可以忽略他们了。让

2. 需要清除的

## useEffect使用



## useEffect注意事项


