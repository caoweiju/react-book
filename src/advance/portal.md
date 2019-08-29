
## Fragment的学习
在16.3之前的版本的react中，jsx里面返回的组件/元素都必须被一个父元素包括，如下：
````jsx
function MyComponent(props) {
    const { propsA } = props;
    /*
    * 错误示范 没有根节点的平铺多个元素
    return (
        <h1>propsA</h1>
        <p>1</p>
        <p>2</p>
        <p>3</p>
    )
    */
   // 需要包括在一个根节点之下 这样会引起很多不必要的层级问题
    return (
        <div>
            <h1>propsA</h1>
            <p>1</p>
            <p>2</p>
            <p>3</p>
        <div>
    )
}
````
当然，我们之前说过jsx的子元素是支持存储在数组中的一组元素，如下修改：
````jsx
function MyComponent(props) {
    const { propsA } = props;
    const arrayData = [1, 2, 3, 4, propsA];
    const elements = arrayData.map( (item, index) => {
        return <p key={index}>{item}</p>
    })
    // 或者收到放进一个数组内
    // arrayData.forEach( (item, index) => {
    //     elements.push(<p key={index}>{item}</p>);
    // })

   // 需要包括在一个根节点之下 这样会引起很多不必要的层级问题
    return elements;
}
````

而在16.3之后，重新设计了这块，可以使用新的API来完成上面的功能，
>React 中的一个常见模式是一个组件返回多个元素。Fragments 允许你将子列表分组，而无需向 DOM 添加额外节点。

````jsx
render() {
  return (
    <React.Fragment>
      <ChildA />
      <ChildB />
      <ChildC />
    </React.Fragment>
  );
}
// 空标签的段语法 不传递属性时可以使用
render() {
  return (
    <>
      <ChildA />
      <ChildB />
      <ChildC />
    </>
  );
}
````
目前key 是唯一可以传递给 Fragment 的属性。也就是在使用map这样的渲染列表的时候，如果使用了Fragment，那么就不能使用段语法，因为需要传递key这个唯一的属性。

## children的了解
children，是一个特殊的 prop，通常由 JSX 表达式中的子组件组成，而非组件本身定义。

同时也提供了一些顶级API[React.Children 提供了用于处理 this.props.children 不透明数据结构的实用方法。]来处理这个属性：
1. `React.Children.map(children, function[(thisArg)])`
    在 children 里的每个直接子节点上调用一个函数，并将 this 设置为 thisArg。如果 children 是一个数组，它将被遍历并为数组中的每个子节点调用该函数。如果子节点为 null 或是 undefined，则此方法将返回 null 或是 undefined，而不会返回数组。**注意: 如果 children 是一个 Fragment 对象，它将被视为单一子节点的情况处理，而不会被遍历。**
2. `React.Children.forEach(children, function[(thisArg)])`和map类似，就是不会返回数组
3. `React.Children.count(children)`
    返回 children 中的组件总数量，等同于通过 map 或 forEach 调用回调函数的次数。
4. `React.Children.only(children)`
    验证 children 是否只有一个子节点（一个 React 元素），如果有则返回它，否则此方法会抛出错误。
5. `React.Children.toArray`
    将 children 这个复杂的数据结构以数组的方式扁平展开并返回，并为每个子节点分配一个 key。


## portal的学习
Portal 提供了一种将子节点渲染到存在于父组件以外的 DOM 节点的优秀的方案。
`ReactDOM.createPortal(child, container)`
第一个参数（child）是任何可渲染的 React 子元素，例如一个元素，字符串或 fragment。第二个参数（container）是一个 DOM 元素。
````jsx
render() {
  // React 并*没有*创建一个新的 div。它只是把子元素渲染到 `domNode` 中。
  // `domNode` 是一个可以在任何位置的有效 DOM 节点。
  return ReactDOM.createPortal(
    this.props.children,
    domNode
  );
}
````
尽管 portal 可以被放置在 DOM 树中的任何地方，但在任何其他方面，其行为和普通的 React 子节点行为一致。由于 portal 仍存在于 React 树， 且与 DOM 树 中的位置无关，那么无论其子节点是否是 portal，像 context 这样的功能特性都是不变的。

这包含事件冒泡。一个从 portal 内部触发的事件会一直冒泡至包含 React 树的祖先，即便这些元素并不是 DOM 树 中的祖先

#### cloneElement的学习和使用
````jsx
React.cloneElement(
  element,
  [props],
  [...children]
)
````
以 element 元素为样板克隆并返回新的 React 元素。返回元素的 props 是将新的 props 与原始元素的 props 浅层合并后的结果。新的子元素将取代现有的子元素，而来自原始元素的 key 和 ref 将被保留。

`React.cloneElement()` 几乎等同于：
`<element.type {...element.props} {...props}>{children}</element.type>`

但是，这也保留了组件的 ref。这意味着当通过 ref 获取子节点时，你将不会意外地从你祖先节点上窃取它。相同的 ref 将添加到克隆后的新元素中。



