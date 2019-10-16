# Hook列表
基础 Hook：
- useState
- useEffect
- useContext

额外的 Hook
- useReducer
- useCallback
- useMemo
- useRef
- useImperativeHandle
- useLayoutEffect
- useDebugValue

## useState
使用示例：
````js
const [state, setState] = useState(initialState);
````
注意点：
- useState 第一个参数可以是初始值或者是一个返回初始值得初始化函数；
- setState 的参数可以是一个新的state值，也可以是返回一个新的state的函数【这个函数的参数就是修改之前的state，这块看起来和class组件中state是相似的】；
- setState 这里的setState可以命名为其他的，而且是执行setState将会直接替换原有的state，而不是合并；
- setState 可能是异步的过程，所以如果更新state依赖之前的state，请使用函数作为setState的参数来更新state；
- useState 是在函数组件或者自定义Hook中使用的，所以无论在mount还是update的时候都会被执行，但是update过程的执行不会重新生成state，而是返回最新的state和setState方法；

更多详细内容参考：[useEffect的使用简析](./useState.md)

## useEffect
`useEffect(didUpdate, deps)` 该 Hook 接收一个包含命令式、且可能有副作用代码的函数。默认情况下，effect 将在每轮渲染结束后执行，但你可以通过第二个参数deps来控制让它 在只有某些值改变的时候 才执行。

注意点：
- 清除Effect：组件卸载时需要清除 effect 创建的诸如订阅或计时器 ID 等资源。要实现这一点，useEffect 函数需返回一个清除函数。但是实际情况中，每次useEffect执行都会清除上一次的effect，而创造新的effect，也就是如果组件多次渲染（通常如此），则在执行下一个 effect 之前，上一个 effect 就已被清除。所以为了减少不必要的操作，我们应该考虑减少useEffect的执行，也就是通过控制useEffect的第二个参数deps；
- 传给 useEffect 的函数会延迟调用，而且总是在浏览器屏幕完成渲染后延迟调用，而不是像class组件的didimount、didiupdate周期一样会合并然后渲染到屏幕中避免中间状态渲染出来【useLayoutEffect Hook 可以达到这种效果】；因此不应在 useEffect 的第一个参数对应的函数中执行阻塞浏览器更新屏幕的操作；
- 依赖可以使得useEffect仅仅在某些条件下才会被执行，不过需要确保数组中包含了所有外部作用域中会发生变化且在 effect 中使用的变量，否则你的代码会引用到先前渲染中的旧变量，旧变量会保存就得值，而不是最新的值导致出现bug。而且依赖项数组不会作为参数传给 effect 函数。虽然从概念上来说它表现为：所有 effect 函数中引用的值都应该出现在依赖项数组中。未来编译器会更加智能，届时自动创建数组将成为可能。

更多详细内容参考：[useEffect的使用简析](./useEffect.md)

## useContext



## useReducer
`const [state, dispatch] = useReducer(reducer, initialArg, init);`
- 第一个参数reducer是dispatch执行时需要调用的方法
- 第二个参数是：initialArg 看情况而定
    - 如果没有第三个参数init，那么第二个参数initialArg就是初始useReducer state值
    - 如果存在第三个参数init，且init是一个函数，那么第二个参数initialArg将作为第三个参数init的参数使用
- 第三个参数init 是一个函数，用来第一次运行useReducer获取初始useReducer state值,该函数以第二个参数initialArg为运行参数，返回初始state值；

useState 的替代方案。它接收一个形如 (state, action) => newState 的 reducer，并返回当前的 state 以及与其配套的 dispatch 方法【类似redux的使用】。

>在某些场景下，useReducer 会比 useState 更适用，例如 state 逻辑较复杂且包含多个子值，或者下一个 state 依赖于之前的 state 等。并且，使用 useReducer 还能给那些会触发深更新的组件做性能优化，因为你可以向子组件传递 dispatch 而不是回调函数 。

### 指定初始state
有两种不同初始化 useReducer state 的方式，你可以根据使用场景选择其中的一种。

1. 将初始 state 作为第二个参数传入 useReducer 是最简单的方法：
    ````js
    const [state, dispatch] = useReducer(
        reducer,
        {count: initialCount}
    );
    ````
2. 也可以像useState一样来惰性初始化
    ````js
    const [state, dispatch] = useReducer(
        reducer,
        {count: initialCount},
        (initArg) => {
            return {
                data: initArg
            }
        }
    );
    // 初始state将会是： {data: {count: initialCount}}
    ````

**如果 Reducer Hook 的返回值与当前 state 相同，React 将跳过子组件的渲染及副作用的执行【前提是当前更新只有dispatch引起了变化】。（React 使用 Object.is 比较算法 来比较 state。）**

## useCallback
````js
const memoizedCallback = useCallback(
  () => {
    doSomething(a, b);
  },
  [a, b],
);

````
参数：
- 第一个参数是函数，等待memoized【内存化】的函数；
- 第二参数是数组，函数中所使用的外部变量列表，以便在变量变化时来更新这个memoized【内存化】回调函数；
 
返回一个memoized【内存化】回调函数。类似于会一直被保存的函数，而不会在每次执行函数组件进行渲染的时候重新生成一个新的函数；把内联回调函数及依赖项数组作为参数传入 useCallback，它将返回该回调函数的 memoized 版本，该回调函数仅在某个依赖项改变时才会更新。这对于作为其他子组件props的函数而言来使用，配合shouldComponentUpdate或者meno来达到优化避免不必要的渲染；

**切记：一定要将useCallback中的函数所使用的外部变量都放入第二个参数deps数组中，不然你的函数中访问的外部变量可能会由于闭包的原因访问到上一次渲染时的值，**


## useMemo


## useRef
`const refContainer = useRef(initialValue);`

useRef 返回一个可变的 ref 对象，其 .current 属性被初始化为传入的参数（initialValue）。返回的 ref 对象在组件的整个生命周期内保持不变。

````jsx
function TextInputWithFocusButton() {
  const [inputValue, setInputValue] = useState('')
  const inputEl = useRef(null);
  const onButtonClick = () => {
    // `current` 指向已挂载到 DOM 上的文本输入元素
    inputEl.current.focus();
  };
  const changeInput = useCallback(
      () => {
          const value = inputEl.current.value;
          setInputValue(value.trim());
      },
      [inputEl]
  )
  return (
    <>
      <input ref={inputEl} type="text" onChange={changeInput} />
      <button onClick={onButtonClick}>Focus the input</button>
    </>
  );
}
````

上面是一个常见的react使用Ref的场景，只是使用了uesRef而不是 createRef；

然而，useRef() 比 ref 属性更有用。它可以很方便地保存任何可变值，其类似于在 class 中使用实例字段的方式。这是因为它创建的是一个普通 Javascript 对象。而且 useRef() 和自建一个 {current: ...} 对象的唯一区别是，useRef 会在每次渲染时返回同一个 ref 对象，而不是在每次更新执行函数组件的时候重新生成一个字面量对象。

**ref的内容发生变更时，并不会引起组件的重新渲染，就和class组件的实例对象一样，可以通过useCallback来配合setState来达到变更效果，类似上面的例子所示**

## useImperativeHandle


## useLayoutEffect


## useDebugValue


