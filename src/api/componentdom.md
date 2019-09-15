# React
>如果你使用一个 `<script>` 标签引入 React，所有的DOM相关顶层 API 都能在全局 ReactDOM 上调用

`react-dom` 的 `package` 提供了可在应用顶层使用的 `DOM（DOM-specific）`方法，如果有需要，你可以把这些方法用于 React 模型以外的地方。不过一般情况下，大部分组件都不需要使用这个模块。

`react-dom`API如下：
1. render()
2. hydrate()
3. unmountComponentAtNode()
4. findDOMNode()
5. createPortal()

## render
`ReactDOM.render(element, container[, callback])`, 
三个参数：
1. react元素，一般是jsx声明的根组件
2. 容器，是dom中的一个交给react管理的根节点，一般而言是一个body的子元素，一般是通过querySelector/querySelectorAll/getElementById等方法获取的DOM节点
3. 回调函数，在执行渲染或者更新的之后执行，

返回值：
* 是这个组件的引用，也就是组件实例（或者针对无状态组件返回 null

>在提供的 `container` 里渲染一个 `React` 元素，并返回对该组件的引用（或者针对无状态组件返回 null）。如果 React 元素之前已经在 container 里渲染过，这将会对其执行更新操作，并仅会在必要时改变 DOM 以映射最新的 React 元素。如果提供了可选的回调函数，该回调将在组件被渲染或更新之后被执行。

需要注意以下几点：
1. `ReactDOM.render()` 会控制你传入容器节点里的内容。当首次调用时，容器节点里的所有 DOM 元素都会被替换，后续的调用则会使用 React 的 DOM 差分算法（`DOM diffing algorithm`）进行高效的更新。
2. `ReactDOM.render()` 不会修改容器节点（只会修改容器的子节点）。也可以直接将react组件渲染到子节点中，这样就可以在不覆盖现有子节点的情况下，将组件插入已有的 DOM 节点中。
3. 使用 `ReactDOM.render()` 对服务端渲染容器进行 `hydrate` 操作的方式已经被废弃，并且会在 React 17 被移除。作为替代，请使用 hydrate()。

## hydrate
`ReactDOM.hydrate(element, container[, callback])`, 与 render() 相同，但它用于在 `ReactDOMServer` 渲染的容器中对 HTML 的内容进行 hydrate 操作。React 会尝试在已有标记上绑定事件监听器。

React 希望服务端与客户端渲染的内容完全一致。如果单个元素的属性或者文本内容，在服务端和客户端之间有无法避免差异（比如：时间戳），则可以为元素添加 suppressHydrationWarning={true} 来消除警告。这种方式只在一级深度上有效，应只作为一种应急方案（escape hatch）。请不要过度使用！除非它是文本内容。

如果你执意要在服务端与客户端渲染不同内容，你可以采用双重（two-pass）渲染。在客户端渲染不同内容的组件可以读取类似于 this.state.isClient 的 state 变量，你可以在 `componentDidMount()` 里将它设置为 true。这种方式在初始渲染过程中会与服务端渲染相同的内容，从而避免不匹配的情况出现，但在 hydration 操作之后，会同步进行额外的渲染操作。注意，因为进行了两次渲染，这种方式会使得组件渲染变慢，请小心使用。主要理解一下几点：
1. 服务端只会执行`ReactDOMServer.renderToString(component/element)`操作，将组件变成html字符串，并不会执行相应的生命周期回调；
2. 客户端执行`ReactDOM.hydrate(element, container[, callback])`会对服务端渲染的内容根据标记进行事件绑定，并完成hydrate和服务端renderToString的对比，验证是否一样；
3. 客服端第一次执行`ReactDOM.hydrate(element, container[, callback])`，会触发`componentDidMount()`，该生命周期内执行setState操作的话，会引起同步的渲染，也就是前面hydrate产生的UI只用来对比服务端，但不会直接渲染到界面，而是等`componentDidMount()`中的setState作用完成，
## unmountComponentAtNode
`ReactDOM.unmountComponentAtNode(container)`, 从 DOM 中卸载组件，会将其事件处理器（event handlers）和 state 一并清除。如果指定容器上没有对应已挂载的组件，这个函数什么也不会做。如果组件被移除将会返回 true，如果没有组件可被移除将会返回 false。

## findDOMNode
>注意:findDOMNode 是一个访问底层 DOM 节点的应急方案（escape hatch）。在大多数情况下，不推荐使用该方法，因为它会破坏组件的抽象结构。严格模式下该方法已弃用。

`ReactDOM.findDOMNode(component)`, 参数component通常是通过refs获取的自定义react组件实例，如果组件已经被挂载到 DOM 上，此方法会返回浏览器中相应的原生 DOM 元素。此方法对于从 DOM 中读取值很有用，例如获取表单字段的值或者执行 DOM 检测（performing DOM measurements）。大多数情况下，你可以绑定一个 ref 到 DOM 节点上，可以完全避免使用 findDOMNode。

当组件渲染的内容为 null 或 false 时，findDOMNode 也会返回 null。当组件渲染的是字符串时，findDOMNode 返回的是字符串对应的 DOM 节点。从 React 16 开始，组件可能会返回有多个子节点的 fragment，在这种情况下，findDOMNode 会返回第一个非空子节点对应的 DOM 节点。

**另外findDOMNode 不能用于函数组件。**

## createPortal
`ReactDOM.createPortal(child, container)`, 创建 portal。Portal 将提供一种将子节点渲染到 DOM 节点中的方式，该节点存在于 DOM 组件的层次结构之外。

可以在组件的render方法中，返回一个createPortal生产的portal，将组件渲染在指定的DOM节点中。

