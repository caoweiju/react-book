# React
>如果你使用一个 `<script>` 标签引入 React，所有的顶层 API 都能在全局 ReactDOM 上调用

`react-dom` 的 `package` 提供了可在应用顶层使用的 `DOM（DOM-specific）`方法，如果有需要，你可以把这些方法用于 React 模型以外的地方。不过一般情况下，大部分组件都不需要使用这个模块。

`react-dom`API如下：
1. render()
2. hydrate()
3. unmountComponentAtNode()
4. findDOMNode()
5. createPortal()

