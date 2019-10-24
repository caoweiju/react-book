# Hook的实践和疑惑
>Hook 是 React 16.8 的新增特性。它可以让你在不编写 class 的情况下使用 state 以及其他的 React 特性。

开发者刚开始接触Hook的时候，可能会遇到一些问题，可以查看一下对应的解决方法和设计思路：[react Hook's FAQ](https://reactjs.org/docs/hooks-faq.html);


## Hook引入的策略
- 16.8版本的react已经引入了Hook，记得同时升级react、reactDOM等相关package；
- 虽然Hook可以替换大部分class组件的功能，但是class并不会被取消，会一直保持；
- 虽然应用可以同时支持class组件和包含Hook的函数组件，但是长远来看，我们期望 Hook 能够成为人们编写 React 组件的主要方式。
- 我们给 Hook 设定的目标是尽早覆盖 class 的所有使用场景。目前暂时还没有对应不常用的 getSnapshotBeforeUpdate 和 componentDidCatch 生命周期的 Hook 等价写法，但我们计划尽早把它们加进来。目前 Hook 还处于早期阶段，一些第三方的库可能还暂时无法兼容 Hook。
- React Redux 从 v7.1.0 开始支持 Hook API 并暴露了 useDispatch 和 useSelector 等 hook。React Router 从 v5.1 开始支持 hook。
- 规范：对 Hook 的调用要么在一个大驼峰法命名的函数（视作一个组件）内部，要么在另一个 useSomething 函数（视作一个自定义 Hook）中。Hook 在每次渲染时都按照相同的顺序被调用。

## class迁移到Hook
1. 生命周期方法要如何对应到 Hook？
    - constructor：函数组件不需要构造函数。你可以通过调用 useState 来初始化 state。如果计算的代价比较昂贵，你可以传一个函数给 useState。
    - getDerivedStateFromProps：改为 在渲染时 安排一次更新。
    - shouldComponentUpdate：详见 下方 React.memo.
    - render：这是函数组件体本身。
    - componentDidMount, componentDidUpdate, componentWillUnmount：useEffect Hook 可以表达所有这些(包括 不那么 常见 的场景)的组合。
    - componentDidCatch and getDerivedStateFromError：目前还没有这些方法的 Hook 等价写法，但很快会加上。

2. 异步获取数据在componentDidMount调用：
    ````jsx
    useEffect(() => {
        let ignore = false;

        async function fetchData() {
        const result = await axios('https://hn.algolia.com/api/v1/search?query=' + query);
        if (!ignore) setData(result.data);
        }

        fetchData();
        return () => { ignore = true; }
    }, [query]);
    ````
3. class的实例变量模拟：useRef() Hook 不仅可以用于 DOM refs。「ref」 对象是一个 current 属性可变且可以容纳任意值的通用容器，类似于一个 class 的实例属性。
4. state拆分：对于相关联的state或者更新时依赖上一次的值的情况建议使用单个Object作为state或者配合useReducer；不相关的分开到不同的state中；
5. componentDidUpdate模拟：你可以 使用一个可变的 ref 手动存储一个布尔值来表示是首次渲染还是后续渲染，然后在你的 effect 中检查这个标识；
6. 如果想获取上一轮的 props 或 state可以 通过 ref 来手动实现：
    ````jsx
    function Counter() {
    const [count, setCount] = useState(0);

    const prevCountRef = useRef();
    useEffect(() => {
        prevCountRef.current = count;
    });
    const prevCount = prevCountRef.current;

    return <h1>Now: {count}, before: {prevCount}</h1>;
    }
    ````
7. getDerivedStateFromProps模拟：可以在渲染过程中更新 state 。React 会立即退出第一次渲染并用更新后的 state 重新运行组件以避免耗费太多性能。
    ````jsx
    function ScrollView({row}) {
    let [isScrollingDown, setIsScrollingDown] = useState(false);
    let [prevRow, setPrevRow] = useState(null);

    if (row !== prevRow) {
        // Row 自上次渲染以来发生过改变。更新 isScrollingDown。
        setIsScrollingDown(prevRow !== null && row > prevRow);
        setPrevRow(row);
    }

    return `Scrolling down: ${isScrollingDown}`;
    }
    ````
8. forceUpdate模拟：可以用一个增长的计数器来在 state 没变的时候依然强制一次重新渲染
    ````jsx
    const [ignored, forceUpdate] = useReducer(x => x + 1, 0);

    function handleClick() {
        forceUpdate();
    }
    ````
9. 引用一个函数组件ref和部分方法: 可以通过 useImperativeHandle Hook 暴露一些命令式的方法给父组件。


## Hook的使用优化
在大型的组件树中，我们推荐的替代方案是通过 context 用 useReducer 往下传一个 dispatch 函数：
````jsx
const TodosDispatch = React.createContext(null);

function TodosApp() {
  // 提示：`dispatch` 不会在重新渲染之间变化
  const [todos, dispatch] = useReducer(todosReducer);

  return (
    <TodosDispatch.Provider value={dispatch}>
      <DeepTree todos={todos} />
    </TodosDispatch.Provider>
  );
}
// TodosApp 内部组件树里的任何子节点都可以使用 dispatch 函数来向上传递 actions 到 TodosApp：

function DeepChild(props) {
  // 如果我们想要执行一个 action，我们可以从 context 中获取 dispatch。
  const dispatch = useContext(TodosDispatch);

  function handleClick() {
    dispatch({ type: 'add', text: 'hello' });
  }

  return (
    <button onClick={handleClick}>Add todo</button>
  );
}
````



## Hook底层原理

1. React 是如何把对 Hook 的调用和组件联系起来的？
    React 保持对当先渲染中的组件的追踪。多亏了 Hook 规范，我们得知 Hook 只会在 React 组件中被调用（或自定义 Hook —— 同样只会在 React 组件中被调用）。

    每个组件内部都有一个「记忆单元格」列表。它们只不过是我们用来存储一些数据的 JavaScript 对象。当你用 useState() 调用一个 Hook 的时候，它会读取当前的单元格（或在首次渲染时将其初始化），然后把指针移动到下一个。这就是多个 useState() 调用会得到各自独立的本地 state 的原因。

2. Hook 使用了哪些现有技术？
    Hook 由不同的来源的多个想法构成：
    - react-future 这个仓库中包含我们对函数式 API 的老旧实验。
    - React 社区对 render prop API 的实验，其中包括 Ryan Florence 的 Reactions Component 。
    - Dominic Gannaway 的用 adopt 关键字 作为 render props 的语法糖的提案。
    - DisplayScript 中的 state 变量和 state 单元格。
    - ReasonReact 中的 Reducer components。
    - Rx 中的 Subscriptions。
    - Multicore OCaml 提到的 Algebraic effects。
    - Sebastian Markbåge 想到了 Hook 最初的设计，后来经过 Andrew Clark，Sophie Alpert，Dominic Gannaway，和React 团队的其它成员的提炼。


