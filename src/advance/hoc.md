# 高阶组件
>高阶组件（HOC）是 React 中用于复用组件逻辑的一种高级技巧。HOC 自身不是 React API 的一部分，它是一种基于 React 的组合特性而形成的设计模式。

具体而言，高阶组件是参数为组件，返回值为新组件的函数。

    const EnhancedComponent = higherOrderComponent(WrappedComponent);

组件是将 props 转换为 UI，而高阶组件是将组件转换为另一个组件。

HOC 在 React 的第三方库中很常见，例如 Redux 的 connect 和 Relay 的 createFragmentContainer。


很容易联想的就是高阶函数：
````js
// 当一个函数的返回值是一个函数的时候 这个函数就是高阶函数 是函数柯理化的基础理念
const hof = (a) => (b) => a + b;
````

## 使用实例
高阶组件是参数为组件，返回值为新组件的函数。而使用高阶组件就是希望通过高阶组件这个函数对传入的组件做一定的封装和统一的逻辑处理。从而达到复用的目的。

````jsx
// 此函数接收一个组件... 返回一个新的组件 就是高阶组件
function withSubscription(WrappedComponent, selectData) {
  // ...并返回另一个组件...
  return class extends React.Component {
    constructor(props) {
      super(props);
      this.handleChange = this.handleChange.bind(this);
      this.state = {
        data: selectData(DataSource, props)
      };
    }

    componentDidMount() {
      // ...负责订阅相关的操作...
      DataSource.addChangeListener(this.handleChange);
    }

    componentWillUnmount() {
      DataSource.removeChangeListener(this.handleChange);
    }

    handleChange() {
      this.setState({
        data: selectData(DataSource, this.props)
      });
    }

    render() {
      // ... 并使用新数据渲染被包装的组件!
      // 请注意，我们可能还会传递其他属性
      return <WrappedComponent data={this.state.data} {...this.props} />;
    }
  };
}

// 使用高阶组件
const CommentListWithSubscription = withSubscription(
  CommentList,
  (DataSource) => DataSource.getComments()
);

const BlogPostWithSubscription = withSubscription(
  BlogPost,
  (DataSource, props) => DataSource.getBlogPost(props.id)
);
````


## 注意事项

1. HOC 不会修改传入的组件，也不会使用继承来复制其行为。相反，HOC 通过将组件包装在容器组件中来组成新组件。HOC 是纯函数，没有副作用。不要试图在 HOC 中修改组件原型
2. 将不相关的 props 传递给被包裹的组件，HOC 为组件添加特性。自身不应该大幅改变约定。HOC 返回的组件与原组件应保持类似的接口。HOC 应该透传与自身无关的 props，这种约定保证了 HOC 的灵活性以及可复用性。。所以高阶组件一般都需要类似下面处理将其他的props透传给传入的组件：
    ````jsx
    render() {
        // 过滤掉非此 HOC 额外的 props，且不要进行透传
        const { extraProp, ...passThroughProps } = this.props;

        // 将 props 注入到被包装的组件中。
        // 通常为 state 的值或者实例方法。
        const injectedProp = someStateOrInstanceMethod;

        // 将 props 传递给被包装组件
        return (
            <WrappedComponent
            injectedProp={injectedProp}
            {...passThroughProps}
            />
        );
    }

    ````
3. 包装显示名称以便轻松调试，
    ````jsx
    function withSubscription(WrappedComponent) {
        class WithSubscription extends React.Component {/* ... */}
        WithSubscription.displayName = `WithSubscription(${getDisplayName(WrappedComponent)})`;
        return WithSubscription;
    }
    ````
4. 不要在 render 方法中使用 HOC,React 的 diff 算法（称为协调）使用组件标识来确定它是应该更新现有子树还是将其丢弃并挂载新子树。 如果从 render 返回的组件与前一个渲染中的组件相同（===），则 React 通过将子树与新子树进行区分来递归更新子树。 如果它们不相等，则完全卸载前一个子树。通常，你不需要考虑这点。但对 HOC 来说这一点很重要，因为这代表着你不应在组件的 render 方法中对一个组件应用 HOC：
    ````jsx
    render() {
    // 每次调用 render 函数都会创建一个新的 EnhancedComponent
    // EnhancedComponent1 !== EnhancedComponent2
    const EnhancedComponent = enhance(MyComponent);
    // 这将导致子树每次渲染都会进行卸载，和重新挂载的操作！
    return <EnhancedComponent />;
    }
    ````
5. 务必复制静态方法,有时在 React 组件上定义静态方法很有用，但是高阶组件返回的是新的组件，所以调用这写方法的时候可能会存在问题，所以需要copy这些方法。
    ````jsx
    import hoistNonReactStatic from 'hoist-non-react-statics';
    function enhance(WrappedComponent) {
    class Enhance extends React.Component {/*...*/}
    hoistNonReactStatic(Enhance, WrappedComponent);
    return Enhance;
    ````
6. Refs 不会被传递，虽然高阶组件的约定是将所有 props 传递给被包装组件，但这对于 refs 并不适用。那是因为 ref 实际上并不是一个 prop - 就像 key 一样，它是由 React 专门处理的。如果将 ref 添加到 HOC 的返回组件中，则 ref 引用指向容器组件，而不是被包装组件。这个问题的解决方案是通过使用 React.forwardRef API（React 16.3 中引入）
