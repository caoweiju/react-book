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


