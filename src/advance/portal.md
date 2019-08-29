## portal的学习



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



