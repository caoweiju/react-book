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


#### 类方法getDerivedStateFromProps


#### 渲染方法render


#### 完成挂载componentDidMount


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

#### shouldComponentUpdate()

#### 渲染方法render


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

