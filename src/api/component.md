# React
本章节提供了 React class 组件的详细 API 参考。

React 的组件可以定义为 class 或函数的形式。class 组件目前提供了更多的功能，这些功能将在此章节中详细介绍。如需定义 class 组件，需要继承 `React.Component`：
````jsx
class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
````
在 `React.Component` 的子类中有个必须定义的 render() 函数。本章节介绍其他方法均为可选。
**我们强烈建议你不要创建自己的组件基类。 在 React 组件中，代码重用的主要方式是组合而不是继承。**

本文需要了解的是：
1. 生命周期
2. 错误处理
3. 其他API
4. class属性
5. 实例属性

## 生命周期
>每个组件都包含“生命周期方法”，你可以重写这些方法，以便于在运行过程中特定的阶段执行这些方法。你可以使用此[生命周期图谱](http://projects.wojtekmaj.pl/react-lifecycle-methods-diagram/)作为速查表。在下述列表中，常用的生命周期方法会被加粗。其余生命周期函数的使用则相对罕见。

整个生命周期可以分为三大类过程：
1. 挂载过程的生命周期调用函数顺序；
2. 更新过程的生命周期调用函数顺序；
3. 卸载过程的生命周期调用函数顺序。

### 组件挂载
组件挂载时的顺序：
* constructor()
* static getDerivedStateFromProps()
* render()
* componentDidMount()

还有一个已经过时的`UNSAFE_componentWillMount()`;是在`constructor`和`render`之间调用；

#### 构造函数constructor()
`constructor(props)`

**如果不初始化 state 或不进行方法绑定，则不需要为 React 组件实现构造函数。**
在 React 组件挂载之前，会调用它的构造函数。在为 React.Component 子类实现构造函数时，应在其他语句之前前调用 super(props)。否则，this.props 在构造函数中可能会出现未定义的 bug。

通常，在 React 中，构造函数仅用于以下两种情况：
* 通过给 this.state 赋值对象来初始化内部 state。
* 为事件处理函数绑定实例

在 constructor() 函数中不要调用 setState() 方法。如果你的组件需要使用内部 state，请直接在构造函数中为 this.state 赋值初始 state：
````jsx
constructor(props) {
  super(props);
  // 不要在这里调用 this.setState()
  this.state = { counter: 0 };
  this.handleClick = this.handleClick.bind(this);
}
````
只能在构造函数中直接为 this.state 赋值。如需在其他方法中赋值，你应使用 this.setState() 替代。

要避免在构造函数中引入任何副作用或订阅。如遇到此场景，请将对应的操作放置在 componentDidMount 中。

>避免将 props 的值复制给 state！这是一个常见的错误：
````jsx
constructor(props) {
 super(props);
 // 不要这样做
 this.state = { color: props.color };
}
````
>如此做毫无必要（你可以直接使用 this.props.color），同时还产生了 bug（更新 prop 中的 color 时，并不会影响 state）。只有在你刻意忽略 prop 更新的情况下使用。此时，应将 prop 重命名为 initialColor 或 defaultColor。必要时，你可以修改它的 key，以强制“重置”其内部 state。

#### 类方法getDerivedStateFromProps
`static getDerivedStateFromProps(props, state)`
getDerivedStateFromProps 会在调用 render 方法之前调用，并且在初始挂载及后续更新时都会被调用。它应返回一个对象来更新 state，如果返回 null 则不更新任何内容。此方法无权访问组件实例。

#### 渲染方法render
`render()` 方法是 class 组件中唯一必须实现的方法。

当 render 被调用时，它会检查 this.props 和 this.state 的变化并返回以下类型之一：

1. React 元素。通常通过 JSX 创建。例如，`<div />` 会被 React 渲染为 DOM 节点，`<MyComponent />` 会被 React 渲染为自定义组件，无论是 `<div />` 还是 `<MyComponent />` 均为 React 元素。
2. 数组或 fragments。 使得 render 方法可以返回多个元素。欲了解更多详细信息，请参阅 fragments 文档。
3. Portals。可以渲染子节点到不同的 DOM 子树中。欲了解更多详细信息，请参阅有关 portals 的文档
4. 字符串或数值类型。它们在 DOM 中会被渲染为文本节点
5. 布尔类型或 null。什么都不渲染。（主要用于支持返回 `test && <Child />` 的模式，其中 test 为布尔类型。)

`render()` 函数应该为纯函数，这意味着在不修改组件 state 的情况下，每次调用时都返回相同的结果，并且它不会直接与浏览器交互。

如需与浏览器进行交互，请在 `componentDidMount()` 或其他生命周期方法中执行你的操作。保持 `render()` 为纯函数，可以使组件更容易思考。

>注意:如果 shouldComponentUpdate() 返回 false，则不会调用 render()。

#### 完成挂载componentDidMount
`componentDidMount()` 会在组件挂载后（插入 DOM 树中）立即调用。依赖于 DOM 节点的初始化应该放在这里。如需通过网络请求获取数据，此处是实例化请求的好地方。

这个方法是比较适合添加订阅的地方。如果添加了订阅，请不要忘记在 componentWillUnmount() 里取消订阅

>你可以在 componentDidMount() 里可以直接调用 setState()。它将触发额外渲染，但此渲染会发生在浏览器更新屏幕之前。如此保证了即使在 render() 两次调用的情况下，用户也不会看到中间状态。请谨慎使用该模式，因为它会导致性能问题。通常，你应该在 constructor() 中初始化 state。如果你的渲染依赖于 DOM 节点的大小或位置，比如实现 modals 和 tooltips 等情况下，你可以使用此方式处理

**componentDidMount() 里可以直接调用 setState()。它将触发额外渲染，但此渲染会发生在浏览器更新屏幕之前。如此保证了即使在 render() 两次调用的情况下，用户也不会看到中间状态**

### 组件更新
当组件的 props 或 state 发生变化时会触发更新。组件更新的生命周期调用顺序如下：
* static getDerivedStateFromProps()
* shouldComponentUpdate()
* render()
* getSnapshotBeforeUpdate()
* componentDidUpdate()

**其中不同版本会略有不同，16.3版本的set­State()和force­Update()之后不会调用static getDerivedStateFromProps()，但是16.4+就会调用，当然和force­Update后面不会再调用shouldComponentUpdate**

>注意:下述方法即将过时，在新代码中应该避免使用它们：`UNSAFE_componentWillUpdate()`、`UNSAFE_componentWillReceiveProps()`

#### 类方法getDerivedStateFromProps
[参见：类方法getDerivedStateFromProps](#类方法getDerivedStateFromProps)
#### shouldComponentUpdate()
`shouldComponentUpdate(nextProps, nextState)`, 根据 shouldComponentUpdate() 的返回值，判断 React 组件的输出是否受当前 state 或 props 更改的影响。默认行为是 state 每次发生变化组件都会重新渲染。大部分情况下，你应该遵循默认行为。

当 props 或 state 发生变化时，shouldComponentUpdate() 会在渲染执行之前被调用。返回值默认为 true。首次渲染或使用 forceUpdate() 时不会调用该方法。

开发者在手动编写此函数时，可以将 this.props 与 nextProps 以及 this.state 与nextState 进行比较，并返回 false 以告知 React 可以跳过更新。
**请注意，返回 false 并不会阻止子组件在 state 更改时重新渲染。**

如果 `shouldComponentUpdate()` 返回 `false`，则不会调用 `UNSAFE_componentWillUpdate()`，`render()` 和 `componentDidUpdate()`。后续版本，React 可能会将 `shouldComponentUpdate` 视为提示而不是严格的指令，并且，当返回 `false` 时，仍可能导致组件重新渲染。

#### 渲染方法render
[参见：渲染方法render](#渲染方法render)

#### getSnapshotBeforeUpdate
`getSnapshotBeforeUpdate(prevProps, prevState)`, `getSnapshotBeforeUpdate()` 在最近一次渲染输出（提交到 DOM 节点）之前调用。它使得组件能在发生更改之前从 DOM 中捕获一些更新之前的信息（例如，滚动位置）可以传递到更新之后的`componentDidUpdate()`中使用。此生命周期的任何返回值将作为参数传递给 `componentDidUpdate()`。

此用法并不常见，但它可能出现在 UI 处理中，如需要以特殊方式处理滚动位置的聊天线程等。应返回 snapshot 的值（或 null）。

#### 完成更新componentDidUpdate
`componentDidUpdate(prevProps, prevState, snapshot)`，`componentDidUpdate()` 会在更新后会被立即调用。首次挂载渲染不会执行此方法。当组件更新后，可以在此处对 DOM 进行操作。

你也可以在 `componentDidUpdate()` 中直接调用 `setState()`，但请注意它必须被包裹在一个条件语件里，正如上述的例子那样进行处理，否则会导致死循环。
`componentDidUpdate()` 中调用 `setState()`还会导致额外的重新渲染，不过和`componentDidMount()`一样，不会把中间太渲染在屏幕中，而是等待最后一次的update变更再渲染，所以用户不可见，但还是会影响组件性能。不要将 props “镜像”给 state，请考虑直接使用 props。 欲了解更多有关内容，请参阅为什么 props 复制给 state 会产生 bug。

如果组件实现了 `getSnapshotBeforeUpdate()` 生命周期（不常用），则它的返回值将作为 `componentDidUpdate()` 的第三个参数 `“snapshot”` 参数传递。否则此参数将为 `undefined`。

**注意：如果 shouldComponentUpdate() 返回值为 false，则不会调用 componentDidUpdate()。**

### 组件卸载
当组件从 DOM 中移除时会调用如下方法：
* componentWillUnmount()

#### 准备卸载componentWillUnmount
`componentWillUnmount()` 会在组件卸载及销毁之前直接调用。在此方法中执行必要的清理操作，例如，清除 timer，取消网络请求或清除在 `componentDidMount()` 中创建的订阅等。

`componentWillUnmount()` 中不应调用 `setState()`，因为该组件将永远不会重新渲染。组件实例卸载后，将永远不会再挂载它。


## 错误处理
当渲染过程，生命周期，或子组件的构造函数中抛出错误时，会调用如下方法：
* static getDerivedStateFromError()
* componentDidCatch()

### static getDerivedStateFromError(error)
此生命周期会在后代组件抛出错误后被调用。 它将抛出的错误作为参数，并返回一个值以更新 state

### componentDidCatch()
`componentDidCatch(error, info)`, 此生命周期在后代组件抛出错误后被调用。 它接收两个参数：
1. error —— 抛出的错误。
2. info —— 带有 componentStack key 的对象，其中包含有关组件引发错误的栈信息。

### 错误处理的实例
* `getDerivedStateFromError(error)` 适合:处理UI降级展示
* `componentDidCatch(error, info)` 适合: 在UI降级展示后 对错误做一些处理记录等操作
````jsx
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError(error) {
    // 更新 state 使下一次渲染可以显示降级 UI
    return { hasError: true };
  }

  componentDidCatch(error, info) {
    // "组件堆栈" 例子:
    //   in ComponentThatThrows (created by App)
    //   in ErrorBoundary (created by App)
    //   in div (created by App)
    //   in App
    logComponentStackToMyService(info.componentStack);
  }

  render() {
    if (this.state.hasError) {
      // 你可以渲染任何自定义的降级 UI
      return <h1>Something went wrong.</h1>;
    }

    return this.props.children; 
  }
}
````
>注意：如果发生错误，你可以通过调用 setState 使用 componentDidCatch() 渲染降级 UI，但在未来的版本中将不推荐这样做。 可以使用静态 getDerivedStateFromError() 来处理降级渲染。

## 其他API
组件还提供了一些额外的 API：
* setState()
* forceUpdate()

### setState
`setState(updater[, callback])`,
`setState()` 将对组件 state 的更改排入队列，并通知 React 需要使用更新后的 state 重新渲染此组件及其子组件。这是用于更新用户界面以响应事件处理器和处理服务器数据的主要方式，将 setState() 视为请求而不是立即更新组件的命令。为了更好的感知性能，React 会延迟调用它，然后通过一次传递更新多个组件。React 并不会保证 state 的变更会立即生效。

setState() 并不总是立即更新组件。它会批量推迟更新。这使得在调用 setState() 后立即读取 this.state 成为了隐患。为了消除隐患，请使用 componentDidUpdate 或者 setState 的回调函数（setState(updater, callback)），

1. 第一个参数updater，
    * 可以是函数：`(state, props) => stateChange`, state 是对应用变化时组件状态的引用。当然，它不应直接被修改。你应该使用基于 state 和 props 构建的新对象来表示变化。例如，假设我们想根据 props.step 来增加 state：
    ````jsx
    this.setState((state, props) => {
      return {counter: state.counter + props.step};
    });
    ````
    updater 函数中接收的 state 和 props 都保证为最新。updater 的返回值会与 state 进行浅合并。
    * 可以是state对象，`this.setState({quantity: 2})`, 这种形式的 setState() 也是异步的，并且在同一周期内会对多个 setState 进行批处理。这会使得在同意周期内多次setState传递的对象会做合并处理，如果需要依赖使用生命周期内其他的setState的值，可以使用函数作为第一个参数，因为函数中获取的都是最新的。
2. 第二个参数：`setState()` 的第二个参数为可选的回调函数，它将在 setState 完成合并并重新渲染组件后执行。通常，我们建议使用 componentDidUpdate() 来代替此方式。

### forceUpdate
`component.forceUpdate(callback)`, 默认情况下，当组件的 state 或 props 发生变化时，组件将重新渲染。如果 render() 方法依赖于其他数据，则可以调用 `forceUpdate()` 强制让组件重新渲染。

调用 `forceUpdate()` 将致使组件调用 `render()` 方法，此操作会跳过该组件的 `shouldComponentUpdate()`。但其子组件会触发正常的生命周期方法，包括 `shouldComponentUpdate()` 方法。如果标记发生变化，React 仍将只更新 DOM。

通常你应该避免使用 `forceUpdate()`，尽量在 `render()` 中使用 `this.props` 和 `this.state`。

## class属性
* defaultProps
* propTypes
* displayName
* contextType

## 实例属性
props
state

