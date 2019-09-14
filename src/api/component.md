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

#### 渲染方法render
[参见：渲染方法render](#渲染方法render)

#### getSnapshotBeforeUpdate


#### 完成更新componentDidUpdate


### 组件卸载
当组件从 DOM 中移除时会调用如下方法：
* componentWillUnmount()

#### 准备卸载componentWillUnmount

## 错误处理
当渲染过程，生命周期，或子组件的构造函数中抛出错误时，会调用如下方法：
* static getDerivedStateFromError()
* componentDidCatch()


## 其他API
组件还提供了一些额外的 API：

setState()
forceUpdate()


## class属性
defaultProps
propTypes
displayName
contextType



## 实例属性
props
state

