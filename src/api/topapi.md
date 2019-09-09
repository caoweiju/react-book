# React的顶层API
>React 是 React 库的入口。如果你通过使用 `<script>` 标签的方式来加载 React，则可以通过 React 全局变量对象来获得 React 的顶层 API。当你使用 ES6 与 npm 时，可以通过编写 `import React from 'react'` 来引入它们。当你使用 ES5 与 npm 时，则可以通过编写 `var React = require('react')` 来引入它们。

目前可以大致分为以下几类：
1. 组件类： React.Component 、 React.PureComponent、React.memo；
2. 创建React元素：React.createElement()、React.createFactory()；
3. 转换元素：React.cloneElement()、React.isValidElement()、React.Children；
4. Fragments：React.Fragment 片段；
5. 引用Refs：React.createRef、React.forwardRef；
6. 动态加载：React.lazy、React.Suspense；
7. Hook 是 React 16.8 的新增特性，后面会单独讲解。


## 组件类详析
1. React.Component 是使用 ES6 classes 方式定义 React 组件的基类：
    ````jsx
    class Greeting extends React.Component {
    render() {
        return <h1>Hello, {this.props.name}</h1>;
    }
    }
    ````
2. React.PureComponent
    `React.PureComponent` 与 `React.Component` 很相似。两者的区别在于 `React.Component` 并未实现 `shouldComponentUpdate()`，而 `React.PureComponent` 中以浅层对比 prop 和 state 的方式来实现了该函数。`React.PureComponent` 中的 `shouldComponentUpdate()` 仅作对象的浅层比较。如果对象中包含复杂的数据结构，则有可能因为无法检查深层的差别，产生错误的比对结果。
3. React.memo
    ````jsx
    const MyComponent = React.memo(function MyComponent(props) {
    /* 使用 props 渲染 */
    });
    ````
    React.memo 为高阶组件。它与 React.PureComponent 非常相似，但它适用于函数组件，但不适用于 class 组件。
    默认情况下其只会对复杂对象做浅层对比，如果你想要控制对比过程，那么请将自定义的比较函数通过第二个参数传入来实现。
    ````jsx
    function MyComponent(props) {
    /* 使用 props 渲染 */
    }
    function areEqual(prevProps, nextProps) {
    /*
    如果把 nextProps 传入 render 方法的返回结果与
    将 prevProps 传入 render 方法的返回结果一致则返回 true，
    否则返回 false
    */
    }
    export default React.memo(MyComponent, areEqual);
    ````
    此方法仅作为性能优化的方式而存在。但请不要依赖它来“阻止”渲染，因为这会产生 bug。

## 创建元素
1. JSX的语法内在：createElement()
    ````jsx
    React.createElement(
    type,
    [props],
    [...children]
    )
    ````
    创建并返回指定类型的新 React 元素。其中的类型参数既可以是标签名字符串（如 'div' 或 'span'），也可以是 React 组件 类型 （class 组件或函数组件），或是 React fragment 类型。
2. createFactory()
    `React.createFactory(type)`返回用于生成指定类型 React 元素的函数。与 React.createElement() 相似的是，类型参数既可以是标签名字符串（像是 'div' 或 'span'），也可以是 React 组件 类型 （class 组件或函数组件），或是 React fragment 类型。
    **此辅助函数已废弃，建议使用 JSX 或直接调用 React.createElement() 来替代它。**

## 转变元素
1. 克隆元素：cloneElement()
    ````jsx
    React.cloneElement(
        element,
        [props],
        [...children]
    )
    ````
    以 element 元素为样板克隆并返回新的 React 元素。返回元素的 props 是将新的 props 与原始元素的 props 浅层合并后的结果。新的子元素将取代现有的子元素，而来自原始元素的 key 和 ref 将被保留。
    React.cloneElement() 几乎等同于：`<element.type {...element.props} {...props}>{children}</element.type>`但是，这也保留了组件的 ref。这意味着当通过 ref 获取子节点时，你将不会意外地从你祖先节点上窃取它。相同的 ref 将添加到克隆后的新元素中。
    最为常见的场景就是：
    1. 克隆某个组件的所有子元素，并添加新的props到各组件子元素中；
        ````jsx
        const pageCmp = React.Children.map(
            children, (child: any, index) => React.cloneElement(
                child, {
                    index,
                    activeIndex: this.state.activeIndex,
                }
            )
        );
        ````
    2. 实现sticky的功能;
2. isValidElement()
    `React.isValidElement(object)`，验证对象是否为 React 元素，返回值为 true 或 false。
3. React.Children
    React.Children 提供了用于处理 this.props.children 不透明数据结构的实用方法。
    1. React.Children.map 遍历
    `React.Children.map(children, function[(thisArg)])`在 children 里的每个直接子节点上调用一个函数，并将 this 设置为 thisArg。如果 children 是一个数组，它将被遍历并为数组中的每个子节点调用该函数。如果子节点为 null 或是 undefined，则此方法将返回 null 或是 undefined，而不会返回数组。**如果 children 是一个 Fragment 对象，它将被视为单一子节点的情况处理，而不会被遍历。**
    2. React.Children.forEach
    `React.Children.forEach(children, function[(thisArg)])`，与 React.Children.map() 类似，但它不会返回一个数组。
    3. React.Children.count
    `React.Children.count(children),` 返回 children 中的组件总数量，等同于通过 map 或 forEach 调用回调函数的次数。
    4. React.Children.only
    `React.Children.only(children)`, 验证 children 是否只有一个子节点（一个 React 元素），如果有则返回它，否则此方法会抛出错误。
    5. React.Children.toArray
    `React.Children.toArray(children)`,将 children 这个复杂的数据结构以数组的方式扁平展开并返回，并为每个子节点分配一个 key。当你想要在渲染函数中操作子节点的集合时，它会非常实用，特别是当你想要在向下传递 this.props.children 之前对内容重新排序或获取子集时。

## 片段
React的16.3开始，`React.Fragment` 组件能够在不额外创建 DOM 元素的情况下，让 render() 方法中返回多个元素。
````jsx
render() {
  return (
    <React.Fragment>
      Some text.
      <h2>A heading</h2>
    </React.Fragment>
  );
}
````
你也可以使用其简写语法 <></>，但是不支持添加属性。

## ref相关
1. React.createRef
    React.createRef 创建一个能够通过 ref 属性附加到 React 元素的 ref。
    ````jsx
    class MyComponent extends React.Component {
    constructor(props) {
        super(props);

        this.inputRef = React.createRef();
    }

    render() {
        return <input type="text" ref={this.inputRef} />;
    }

    componentDidMount() {
        this.inputRef.current.focus();
    }
    }
    ````
2. React.forwardRef
    React.forwardRef 会创建一个React组件，这个组件能够将其接受的 ref 属性转发到其组件树下的另一个组件中。这种技术并不常见，但在以下两种场景中特别有用：
    * 转发 refs 到 DOM 组件
    * 在高阶组件中转发 refs
    React.forwardRef 接受渲染函数作为参数。React 将使用 props 和 ref 作为参数来调用此函数。此函数应返回 React 节点。
    ````jsx
    const FancyButton = React.forwardRef((props, ref) => (
    <button ref={ref} className="FancyButton">
        {props.children}
    </button>
    ));

    // You can now get a ref directly to the DOM button:
    const ref = React.createRef();
    <FancyButton ref={ref}>Click me!</FancyButton>;
    ````

## 异步加载
1. React.lazy
    React.lazy() 允许你定义一个动态加载的组件。这有助于缩减 bundle 的体积，并延迟加载在初次渲染时未用到的组件。
    `const SomeComponent = React.lazy(() => import('./SomeComponent'));`, 请注意，渲染 lazy 组件依赖该组件渲染树上层的 <React.Suspense> 组件。这是指定加载指示器（loading indicator）的方式。
2. React.Suspense
    React.Suspense 可以指定加载指示器（loading indicator），以防其组件树中的某些子组件尚未具备渲染条件。目前，懒加载组件是 <React.Suspense> 支持的唯一用例：
    ````jsx
    // 该组件是动态加载的
    const OtherComponent = React.lazy(() => import('./OtherComponent'));

    function MyComponent() {
    return (
        // 显示 <Spinner> 组件直至 OtherComponent 加载完成
        <React.Suspense fallback={<Spinner />}>
        <div>
            <OtherComponent />
        </div>
        </React.Suspense>
    );
    }
    ````





