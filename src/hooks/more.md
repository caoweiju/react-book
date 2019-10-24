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
- **注意：state中保留的最好是会影响渲染的数据，如果只是想要一个类似实例数据持久化，那么请使用useRef**

更多详细内容参考：[useEffect的使用简析](./useState.md)

## useEffect
`useEffect(didUpdate, deps)` 该 Hook 接收一个包含命令式、且可能有副作用代码的函数。默认情况下，effect 将在每轮渲染结束后执行，但你可以通过第二个参数deps来控制让它 在只有某些值改变的时候 才执行。

注意点：
- 清除Effect：组件卸载时需要清除 effect 创建的诸如订阅或计时器 ID 等资源。要实现这一点，useEffect 函数需返回一个清除函数。但是实际情况中，每次useEffect执行都会清除上一次的effect，而创造新的effect，也就是如果组件多次渲染（通常如此），则在执行下一个 effect 之前，上一个 effect 就已被清除。所以为了减少不必要的操作，我们应该考虑减少useEffect的执行，也就是通过控制useEffect的第二个参数deps；
- 传给 useEffect 的函数会延迟调用，而且总是在浏览器屏幕完成渲染后延迟调用，而不是像class组件的didimount、didiupdate周期一样会合并然后渲染到屏幕中避免中间状态渲染出来【useLayoutEffect Hook 可以达到这种效果】；因此不应在 useEffect 的第一个参数对应的函数中执行阻塞浏览器更新屏幕的操作；
- 依赖可以使得useEffect仅仅在某些条件下才会被执行，不过需要确保数组中包含了所有外部作用域中会发生变化且在 effect 中使用的变量，否则你的代码会引用到先前渲染中的旧变量，旧变量会保存就得值，而不是最新的值导致出现bug。而且依赖项数组不会作为参数传给 effect 函数。虽然从概念上来说它表现为：所有 effect 函数中引用的值都应该出现在依赖项数组中。未来编译器会更加智能，届时自动创建数组将成为可能。

更多详细内容参考：[useEffect的使用简析](./useEffect.md)

## useContext
使用：`const value = useContext(MyContext)`  
接收一个 context 对象（React.createContext 的返回值）并返回该 context 的当前值。当前的 context 值由上层组件中距离当前组件最近的 <MyContext.Provider> 的 value prop 决定。

当组件上层最近的 <MyContext.Provider> 更新时，该 Hook 会触发重渲染，并使用最新传递给 MyContext provider 的 context value 值。

别忘记 useContext 的参数必须是 context 对象本身：
- 正确： useContext(MyContext)
- 错误： useContext(MyContext.Consumer)
- 错误： useContext(MyContext.Provider)

>如果你在接触 Hook 前已经对 context API 比较熟悉，那应该可以理解，useContext(MyContext) 相当于 class 组件中的 static contextType = MyContext 或者 <MyContext.Consumer>。

````jsx
const themes = {
  light: {
    foreground: "#000000",
    background: "#eeeeee"
  },
  dark: {
    foreground: "#ffffff",
    background: "#222222"
  }
};

const ThemeContext = React.createContext(themes.light);

function App() {
  return (
    <ThemeContext.Provider value={themes.dark}>
      <Toolbar />
    </ThemeContext.Provider>
  );
}

function Toolbar(props) {
  return (
    <div>
      <ThemedButton />
    </div>
  );
}

function ThemedButton() {
  const theme = useContext(ThemeContext);

  return (
    <button style={{ background: theme.background, color: theme.foreground }}>
      I am styled by theme context!
    </button>
  );
}
````

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
使用：`const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b])`  
返回一个 memoized 值。

把“创建”函数和依赖项数组作为参数传入 useMemo，它仅会在某个依赖项改变时才重新计算 memoized 值。这种优化有助于避免在每次渲染时都进行高开销的计算。

>记住，传入 useMemo 的函数会在渲染期间执行。请不要在这个函数内部执行与渲染无关的操作，诸如副作用这类的操作属于 useEffect 的适用范畴，而不是 useMemo。

**如果没有提供依赖项数组，useMemo 在每次渲染时都会计算新的值。**

你可以把 useMemo 作为性能优化的手段，但不要把它当成语义上的保证。将来，React 可能会选择“遗忘”以前的一些 memoized 值，并在下次渲染时重新计算它们，比如为离屏组件释放内存。先编写在没有 useMemo 的情况下也可以执行的代码 —— 之后再在你的代码中添加 useMemo，以达到优化性能的目的。


>依赖项数组不会作为参数传给“创建”函数。虽然从概念上来说它表现为：所有“创建”函数中引用的值都应该出现在依赖项数组中。


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
使用：`useImperativeHandle(ref, createHandle, [deps])`  
useImperativeHandle 可以让你在使用 ref 时自定义暴露给父组件的实例值。在大多数情况下，应当避免使用 ref 这样的命令式代码。useImperativeHandle 应当与 forwardRef 一起使用：
````jsx
function FancyInput(props, ref) {
  const inputRef = useRef();
  useImperativeHandle(ref, () => ({
    focus: () => {
      inputRef.current.focus();
    }
  }));
  return <input ref={inputRef} ... />;
}
FancyInput = forwardRef(FancyInput);
````
在本例中，渲染 <FancyInput ref={fancyInputRef} /> 的父组件可以调用 fancyInputRef.current.focus()。



## useLayoutEffect
其函数签名与 useEffect 相同，但它会在所有的 DOM 变更之后同步调用 effect。可以使用它来读取 DOM 布局并同步触发重渲染。在浏览器执行绘制之前，useLayoutEffect 内部的更新计划将被同步刷新。

尽可能使用标准的 useEffect 以避免阻塞视觉更新。

>如果你正在将代码从 class 组件迁移到使用 Hook 的函数组件，则需要注意 useLayoutEffect 与 componentDidMount、componentDidUpdate 的调用阶段是一样的【特别是期间有引起update的操作会被合并后一起渲染出来，避免中间态的产生】。但是，我们推荐你一开始先用 useEffect，只有当它出问题的时候再尝试使用 useLayoutEffect。
- 如果你使用服务端渲染，请记住，无论 useLayoutEffect 还是 useEffect 都无法在 Javascript 代码加载完成之前执行。这就是为什么在服务端渲染组件中引入 useLayoutEffect 代码时会触发 React 告警。解决这个问题，需要将代码逻辑移至 useEffect 中（如果首次渲染不需要这段逻辑的情况下），或是将该组件延迟到客户端渲染完成后再显示（如果直到 useLayoutEffect 执行之前 HTML 都显示错乱的情况下）。
- 若要从服务端渲染的 HTML 中排除依赖布局 effect 的组件，可以通过使用 showChild && <Child /> 进行条件渲染，并使用 useEffect(() => { setShowChild(true); }, []) 延迟展示组件。这样，在客户端渲染完成之前，UI 就不会像之前那样显示错乱了。

## useDebugValue
使用：`useDebugValue(value)`  
useDebugValue 可用于在 React 开发者工具中显示自定义 hook 的标签。

例如，“自定义 Hook” 章节中描述的名为 useFriendStatus 的自定义 Hook：
````jsx
function useFriendStatus(friendID) {
  const [isOnline, setIsOnline] = useState(null);

  // ...

  // 在开发者工具中的这个 Hook 旁边显示标签
  // e.g. "FriendStatus: Online"
  useDebugValue(isOnline ? 'Online' : 'Offline');

  return isOnline;
}
````

我们不推荐你向每个自定义 Hook 添加 debug 值。当它作为共享库的一部分时才最有价值。

延迟格式化 debug 值
在某些情况下，格式化值的显示可能是一项开销很大的操作。除非需要检查 Hook，否则没有必要这么做。

因此，useDebugValue 接受一个格式化函数作为可选的第二个参数。该函数只有在 Hook 被检查时才会被调用。它接受 debug 值作为参数，并且会返回一个格式化的显示值。

例如，一个返回 Date 值的自定义 Hook 可以通过格式化函数来避免不必要的 toDateString 函数调用：

useDebugValue(date, date => date.toDateString());

