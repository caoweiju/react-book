# useEffect简介
>Effect Hook 可以让你在函数组件中执行副作用操作，而数据获取，设置订阅以及手动更改 React 组件中的 DOM 都属于副作用  
**如果你熟悉 React class 的生命周期函数，你可以把 useEffect Hook 看做 componentDidMount，componentDidUpdate 和 componentWillUnmount 这三个函数的组合。**


## useEffect简单使用
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
上面的代码使用了useState和useEffect，其中useState创建了一个state叫count，以及修改count的方法；而useEffect的使用是提供一个函数作为参数，该函数会在componentDidMount和componentDidUpdate的时候调用；所以上面代码的就是最开始初始化和每次点击button的时候document.title都会被重新设置为新的值。经验丰富的 JavaScript 开发人员可能会注意到，传递给 useEffect 的函数在每次渲染中都会有所不同，这是刻意为之的。事实上这正是我们可以在 effect 中获取最新的 count 的值，而不用担心其过期的原因。每次我们重新渲染，都会生成新的 effect，替换掉之前的。某种意义上讲，effect 更像是渲染结果的一部分 —— 每个 effect “属于”一次特定的渲染。

>与 componentDidMount 或 componentDidUpdate 不同，使用 useEffect 调度的 effect 不会阻塞浏览器更新屏幕【componentDid* 中如果操作了setState，那么将会合并多次的浏览器更新屏幕，不呈现中间状态】，这让你的应用看起来响应更快。大多数情况下，effect 不需要同步地执行。在个别情况下（例如测量布局），有单独的 useLayoutEffect Hook 供你使用，其 API 与 useEffect 相同。


- useEffect 做了什么？ 通过使用这个 Hook，你可以告诉 React 组件需要在渲染后执行某些操作。React 会保存你传递的函数（我们将它称之为 “effect”），并且在执行 DOM 更新之后调用它。在这个 effect 中，我们设置了 document 的 title 属性，不过我们也可以执行数据获取或调用其他命令式的 API。
- 为什么在组件内部调用 useEffect？ 将 useEffect 放在组件内部让我们可以在 effect 中直接访问 count state 变量（或其他 props）。我们不需要特殊的 API 来读取它 —— 它已经保存在函数作用域中。Hook 使用了 JavaScript 的闭包机制，而不用在 JavaScript 已经提供了解决方案的情况下，还引入特定的 React API。
- useEffect 会在每次渲染后都执行吗？ 是的，默认情况下，它在第一次渲染之后和每次更新之后都会执行。（我们稍后会谈到如何控制它。）你可能会更容易接受 effect 发生在“渲染之后”这种概念，不用再去考虑“挂载”还是“更新”。React 保证了每次运行 effect 的同时，DOM 都已经更新完毕。

**在 React 组件中有两种常见副作用操作：需要清除的和不需要清除的**

1. 不需要清除：有时候我们只想在 React 更新 DOM 之后运行一些额外的代码。比如发送网络请求，手动变更 DOM，记录日志，这些都是常见的无需清除的操作。因为我们在执行完这些操作之后，就可以忽略他们了。上面提到的例子就是一个不需要清楚的effect。

2. 需要清除的：有一些副作用是需要清除的。例如订阅外部数据源。这种情况下，清除工作是非常重要的，可以防止引起内存泄露！一般需要 componentDidMount 和 componentWillUnmount 之间相互对应完成订阅和解除订阅操作。使用生命周期函数迫使我们拆分这些逻辑代码，即使这两部分代码都作用于相同的副作用。而在hook中可以更好的完成编写：
    ````js
    useEffect(() => {
        function handleStatusChange(status) {
            setIsOnline(status.isOnline);
        }
        // 先订阅
        ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
        // Specify how to clean up after this effect: 返回一个函数来解除订阅 函数在
        return function cleanup() {
            ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
        };
    });
    ````
    - 为什么要在 effect 中返回一个函数？ 这是 effect 可选的清除机制。每个 effect 都可以返回一个清除函数。如此可以将添加和移除订阅的逻辑放在一起。它们都属于 effect 的一部分。
    - React 何时清除 effect？ React 会在组件卸载的时候执行清除操作。但是正如之前学到的，effect 在每次渲染的时候都会执行。这就是为什么 React 会在执行当前 effect 之前会对上一个 effect 进行清除，每次传递给 useEffect 的函数在每次渲染中都会有所不同，每次我们重新渲染，都会生成新的 effect，替换掉之前的effect，而如果存在return function清除函数，那么每次执行render都会运行清除函数来清除上一次的effect【除了第一次执行render】。


## useEffect注意事项
1. 使用多个 Effect 实现关注点分离：使用 Hook 其中一个目的就是要解决 class 中生命周期函数经常包含不相关的逻辑，但又把相关逻辑分离到了几个不同方法中的问题。以前经常出现相关联的逻辑被分离到各生命周期中，而Hook 允许我们按照代码的用途分离他们， 并不是像生命周期函数那样。React 将按照 effect 声明的顺序依次调用组件中的每一个 effect。

2. 关于为什么每次更新的时候都要运行 Effect，而且传递给 useEffect 的函数在每次渲染中都会有所不同。每次我们重新渲染，都会生成新的 effect，替换掉之前的effect：
    ````jsx
    function FriendStatus(props) {
    // ...
    useEffect(() => {
        // ...
        ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
        return () => {
        ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
        };
    });

    // 每次执行render的时候发生的过程类似如下：
    // Mount with { friend: { id: 100 } } props
    ChatAPI.subscribeToFriendStatus(100, handleStatusChange);     // 运行第一个 effect

    // Update with { friend: { id: 200 } } props
    ChatAPI.unsubscribeFromFriendStatus(100, handleStatusChange); // 清除上一个 effect
    ChatAPI.subscribeToFriendStatus(200, handleStatusChange);     // 运行下一个 effect

    // Update with { friend: { id: 300 } } props
    ChatAPI.unsubscribeFromFriendStatus(200, handleStatusChange); // 清除上一个 effect
    ChatAPI.subscribeToFriendStatus(300, handleStatusChange);     // 运行下一个 effect

    // Unmount
    ChatAPI.unsubscribeFromFriendStatus(300, handleStatusChange); // 清除最后一个 effect
    ````
3. 通过跳过 Effect 进行性能优化，某些情况每次渲染后都执行清理或者执行 effect 可能会导致性能问题。它被内置到了 useEffect 的 Hook API 中。如果某些特定值在两次重渲染之间没有发生变化，你可以通知 React 跳过对 effect 的调用，只要传递数组作为 useEffect 的第二个可选参数即可：
    ````js
    useEffect(() => {
        document.title = `You clicked ${count} times`;
    }, [count]); // 仅在 count 更改时更新
    ````
    >如果你要使用此优化方式，请确保数组中包含了所有外部作用域中会随时间变化并且在 effect 中使用的变量，否则你的代码会引用到先前渲染中的旧变量。  
    如果想执行只运行一次的 effect（仅在组件挂载和卸载时执行），可以传递一个空数组（[]）作为第二个参数。这就告诉 React 你的 effect 不依赖于 props 或 state 中的任何值，所以它永远都不需要重复执行。这并不属于特殊情况 —— 它依然遵循依赖数组的工作方式。  
    如果你传入了一个空数组（[]），effect 内部的 props 和 state 就会一直拥有其初始值。尽管传入 [] 作为第二个参数更接近大家更熟悉的 componentDidMount 和 componentWillUnmount 思维模式，但我们有更好的方式来避免过于频繁的重复调用 effect。除此之外，**请记得 React 会等待浏览器完成画面渲染之后才会延迟调用 useEffect，**因此会使得额外操作很方便。**需要注意这个和componentDidMount 或 componentDidUpdate不太一样，在componentDidMount 或 componentDidUpdate调用setState的话引起的重新渲染会合并后一起展示到屏幕，不会展示中间状态，但是useEffect会展示中间状态，不过可以使用useLayoutEffect来达到这个效果**  
    ````jsx
    function Counter() {
      const [count, setCount] = useState(0);
      const [count1, setCount1] = useState(0);
      // useInterval(() => {
      //   // Your custom logic here
      //   setCount(count + 1);
      // }, 1000);
      useEffect(() => {
        if(count1 < 50) {
          setCount1(count1+1);
        }
      })
      useLayoutEffect(() => {
        if(count < 50) {
          setCount(count+1);
        }
      })

      return <h1>{count},{count1}</h1>;
    }
    ````
    上面的代码不会出现count和count1增加的闪烁状态；但是如果把useLayoutEffect注释的话，那么就会出现从0到50的快速闪烁增加效果，就是前面提到的React 会等待浏览器完成画面渲染之后才会延迟调用 useEffect；而useLayoutEffect可以像在componentDidMount 或 componentDidUpdate调用setState所引起的重新渲染会在合并后一起展示到屏幕，不会看到中间状态。