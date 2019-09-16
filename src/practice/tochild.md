# 父子组件的通信问题
在react的开发过程中，引起组件进行更新的就是props/state的变更，而react的设计模式是自上而下的，所以在自上而下的过程中，上层组件和下层组件的通信(父组件与后代组件通信)的方式就要求开发者能够熟练掌握了。

目前常见的包括以下：
1. 基础props传递
2. render props复用方式
3. context上下文传递
4. ref获取后代组件实例或者DOM节点
5. findDOMNode获取后代组件DOM节点

## props方法
props是react中一个很重要的概念，目前主要的用于是将组件的属性传入，

当然有一些含有特殊意义的props我们应该注意：
- key 用于标记list的唯一
- ref 用于记录对应组件的实例或者DOM节点
- onClick等事件属性 作用于html元素的事件监听（是react自身的合成事件的封装实现）
- className 就是对于html元素而言的class属性
- style/表单type/等原生html元素属性 对应html元素的同名属性
- children 默认属性 组件包含的所有子组件

当我们使用自定义的react组件(组件名首字母要求大写)时，我们需要定义些属性，组件通过props获取(函数组件通过第一个参数 props获取，class组件通过this.props获取)，这样子组件就可以获取到父组件传入的属性/方法，来完成通信，当然我们还可以通过子组件传递给更深层级的后代组件，这样后代组件就可以获取到最上层的组件传递的属性/方法。

关于跨层级的传递props的不便，可以尝试：
- 通过context的方式支持跨层级传递；
- 通过提升后代节点的层级避免多层传递；
- slot方式，react的props可以传递组件，类似其他库的slot形式，通过形如`<FatherComponent leftSlot={<LeftComponent value="test" className="m-test" clickHander={() => {console.log('click')}} >} />`这种方式来把依赖组件提升，交给子组件处理展示，示例中FatherComponent自定义组件需要将props中的LeftComponent在render中进行处理展示。

## render props复用
render props是一种思想，是提高复用组件的一种方法，render prop 是一个用于告知组件需要渲染什么内容的函数 prop。 任何被用于告知组件需要渲染什么内容的函数 prop 在技术上都可以被称为 “render prop”；

这种方法同样可以是的后代组件完成提升，实现props的传递，只不过props对应的方法需要组件处理，并展示返回的结果。

[参见render props详析](./../advance/error.md) 或者官方文档 [render props详析](https://zh-hans.reactjs.org/docs/render-props.html#use-render-props-for-cross-cutting-concerns)

## context深层级传递



## ref获取后代组件实例
在典型的 React 数据流中，props 是父组件与子组件交互的唯一方式。要修改一个子组件，你需要使用新的 props 来重新渲染它。但是，在某些情况下，你需要在典型数据流之外强制修改子组件。被修改的子组件可能是一个 React 组件的实例，也可能是一个 DOM 元素。对于这两种情况，React 都提供了解决办法---refs。

下面是几个适合使用 refs 的情况：
* 管理焦点，文本选择或媒体播放。
* 触发强制动画。
* 集成第三方 DOM 库。

[参见render props详析](./../advance/ref.md)

## findDOMNode获取后代组件
>注意:findDOMNode 是一个访问底层 DOM 节点的应急方案（escape hatch）。在大多数情况下，不推荐使用该方法，因为它会破坏组件的抽象结构。严格模式下该方法已弃用。

`ReactDOM.findDOMNode(component)`, 参数component通常是通过refs获取的自定义react组件实例，如果组件已经被挂载到 DOM 上，此方法会返回浏览器中相应的原生 DOM 元素。此方法对于从 DOM 中读取值很有用，例如获取表单字段的值或者执行 DOM 检测（performing DOM measurements）。大多数情况下，你可以绑定一个 ref 到 DOM 节点上，可以完全避免使用 findDOMNode。

当组件渲染的内容为 null 或 false 时，findDOMNode 也会返回 null。当组件渲染的是字符串时，findDOMNode 返回的是字符串对应的 DOM 节点。从 React 16 开始，组件可能会返回有多个子节点的 fragment，在这种情况下，findDOMNode 会返回第一个非空子节点对应的 DOM 节点。

**另外findDOMNode不能用于函数组件。而且一般用于获取自定义组件对应的DOM节点，ref获取自定义组件的实例，通过ref作为findDOMNode的参数来获取自定义组件的DOM节点**
````jsx
import { findDomNode } from 'react-dom';
var MyComponent = React.createClass({
  handleClick: function() {
    React.findDOMNode(this.refs.myTextInput).focus();
  },
  render: function() {
    return (
      <div>
        <input type="text" ref="myTextInput" />
        <input type="button" value="Focus the text input" onClick={this.handleClick} />
      </div>
    );
  }
});

React.render(
  <MyComponent />,
  document.getElementById('example')
);
````


