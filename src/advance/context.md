# context的简述
>Context 提供了一个无需为每层组件手动添加 props，就能在组件树间进行数据传递的方法。

在一个典型的 React 应用中，数据是通过 props 属性自上而下（由父及子）进行传递的，但这种做法对于某些类型的属性而言是极其繁琐的（例如：地区偏好，UI 主题），这些属性是应用程序中许多组件都需要的。Context 提供了一种在组件之间共享此类值的方式，而不必显式地通过组件树的逐层传递 props。


## context的学习
Context 主要应用场景在于很多不同层级的组件需要访问同样一些的数据。请谨慎使用，因为这会使得组件的复用性变差。

如果你只是想避免层层传递一些属性，[组件组合（component composition）](https://reactjs.org/docs/composition-vs-inheritance.html)有时候是一个比 context 更好的解决方案。可以将需要使用对应的props属性的组件提升到父组件中，用props来实现类似插槽的概念，因为props可以传递各种各样类型的数据，包括react组件和dom元素。

在正常使用context的时候，需要先来了解React提供的context相关的API【16.x以后】：
1. React.createContext 创建context
2. Context.Provider 将context引入组件树顶层
3. Class.contextType 通过静态属性contextType获取context
4. Context.Consumer 通过生成的context来消费 主要是函数组件，和Class.contextType功能类似

### React.createContext创建context
    const MyContext = React.createContext(defaultValue);

创建一个 Context 对象。当 React 渲染一个订阅了这个 Context 对象的组件，这个组件会从组件树中离自身最近的那个匹配的 Provider 中读取到当前的 context 值。

只有当组件所处的树中没有匹配到 Provider 时，其 defaultValue 参数才会生效。这有助于在不使用 Provider 包装组件的情况下对组件进行测试。注意：将 undefined 传递给 Provider 的 value 时，消费组件的 defaultValue 不会生效。

defaultValue可以使context需要存储的内容，常见的就是一个对象，包括了页面相关数据data属性、交互函数等内容；
````js
export const themes = {
  light: {
    foreground: '#000000',
    background: '#eeeeee',
  },
  dark: {
    foreground: '#ffffff',
    background: '#222222',
  },
};
// 确保传递给 createContext 的默认值数据结构是调用的组件（consumers）所能匹配的！
export const ThemeContext = React.createContext({
  theme: themes.dark,
  toggleTheme: () => {},
});
````

### Context.Provider引入context
    const MyContext = React.createContext(defaultValue);
    <MyContext.Provider value={/* 某个值 */}>

每个 Context 对象都会返回一个 Provider React 组件，它允许消费组件订阅 context 的变化。

Provider 接收一个 value 属性，传递给消费组件。一个 Provider 可以和多个消费组件有对应关系。多个 Provider 也可以嵌套使用，里层的会覆盖外层的数据。

当 Provider 的 value 值发生变化时，它内部的所有消费组件都会重新渲染。Provider 及其内部 consumer 组件都不受制于 shouldComponentUpdate 函数，因此当 consumer 组件在其祖先组件退出更新的情况下也能更新。

通过新旧值检测来确定变化，使用了与 Object.is 相同的算法。但是切记value这个不是要直接传入字面量的对象，因为这样每次渲染都被认为是context的值改变了，因为字面量的对象即使内容相等，但是被认为是两个不同的值。


### Class.contextType使用context
class组件消费context：
````jsx
class MyClass extends React.Component {
  componentDidMount() {
    let value = this.context;
    /* 在组件挂载完成后，使用 MyContext 组件的值来执行一些有副作用的操作 */
  }
  componentDidUpdate() {
    let value = this.context;
    /* ... */
  }
  componentWillUnmount() {
    let value = this.context;
    /* ... */
  }
  render() {
    let value = this.context;
    /* 基于 MyContext 组件的值进行渲染 */
  }
}
MyClass.contextType = MyContext;

// 如果支持static的语法
class MyClass extends React.Component {
  static contextType = MyContext;
  render() {
    let value = this.context;
    /* 基于这个值进行渲染工作 */
  }
}
````
或和函数组件的使用【推荐使用Context.Consumer或者useContext hook】：
````jsx
// 第二个参数作为context传入
function App(props, context) {
    return <div />
}
MyClass.contextType = MyContext;
````

挂载在 class 上的 contextType 属性会被重赋值为一个由 React.createContext() 创建的 Context 对象。这能让你使用 this.context 来消费最近 Context 上的那个值。你可以在任何生命周期中访问到它，包括 render 函数中。

### Context.Consumer函数组件使用context
````jsx
<MyContext.Consumer>
  {value => /* 基于 context 值进行渲染*/}
</MyContext.Consumer>
````jsx

这里，React 组件也可以订阅到 context 变更。这能让你在函数式组件中完成订阅 context。
这需要函数作为子元素这种做法。这个函数接收当前的 context 值，返回一个 React 节点。传递给函数的 value 值等同于往上组件树离这个 context 最近的 Provider 提供的 value 值。如果没有对应的 Provider，value 参数等同于传递给 createContext() 的 defaultValue。

当然useContext这个hooks也可以，后面再hooks章节会提起。


## context的实践使用






