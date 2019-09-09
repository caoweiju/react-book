# ReactDOMServer
ReactDOMServer允许开发者把组件渲染成静态的标签节点，这通常是在node端使用，
其中包括了常见的四个API，其中有两个在服务端和用户端都可以使用，还有两个只能在服务端使用，因为依赖一个stream包，该包仅支持服务端：
1. renderToString 把组建渲染成字符串节点
2. renderToStatickMarkup 把组建渲染成字符串节点，但是不会包含各节点的一些react内部使用的属性，可以减少部分size，但是可能影响交互
3. renderToNodeStream() 【仅在服务端可以使用】
4. renderToStaticNodeStream() 【仅在服务端可以使用】

## API简析
1. renderToString：`ReactDOMServer.renderToString(component/element)`
    将 React 元素渲染为初始 HTML。React 将返回一个 HTML 字符串。你可以使用此方法在服务端生成 HTML，并在首次请求时将标记下发，以加快页面加载速度，并允许搜索引擎爬取你的页面以达到 SEO 优化的目的。服务端渲染后，配合在已有服务端渲染标记的节点上调用 `ReactDOM.hydrate()` 方法，React 将会保留该节点且只进行事件处理绑定而不改动HTML元素，从而让你有一个非常高性能的首次加载体验。
    **虽然服务端和客户端都可以使用，但是常见用于服务端，同时配合客户端的`ReactDOM.hydrate()` 方法使用**
2. renderToStaticMarkup：`ReactDOMServer.renderToStaticMarkup(component/element)`
    此方法与 `renderToString` 相似，但此方法不会在 React 内部创建的额外 DOM 属性，例如 `data-reactroot`【等react内部使用的属性】。如果你希望把 React 当作静态页面生成器来使用，此方法会非常有用，因为去除额外的属性可以节省一些字节。如果你计划在前端使用 React 以使得标记可交互，请不要使用此方法。你可以在服务端上使用 renderToString 或在前端上使用 `ReactDOM.hydrate()` 来代替此方法。
3. renderToNodeStream()： `ReactDOMServer.renderToNodeStream(element)`【仅在服务端可以使用】
    将一个 React 元素渲染成其初始 HTML。返回一个可输出 HTML 字符串的可读流。通过可读流输出的 HTML 完全等同于 `ReactDOMServer.renderToString` 返回的 HTML。你可以使用本方法在服务器上生成 HTML，并在初始请求时将标记下发，以加快页面加载速度，并允许搜索引擎抓取你的页面以达到 SEO 优化的目的。如果你在已有服务端渲染标记的节点上调用 `ReactDOM.hydrate()` 方法，React 将会保留该节点且只进行事件处理绑定，从而让你有一个非常高性能的首次加载体验
4. renderToStaticNodeStream()：`ReactDOMServer.renderToStaticNodeStream(element)`【仅在服务端可以使用】
    此方法与 `renderToNodeStream` 相似，但此方法不会在 React 内部创建的额外 DOM 属性，例如 `data-reactroot`【等react内部使用的属性】。如果你希望把 React 当作静态页面生成器来使用，此方法会非常有用，因为去除额外的属性可以节省一些字节。通过可读流输出的 HTML，完全等同于 `ReactDOMServer.renderToStaticMarkup` 返回的 HTML。如果你计划在前端使用 React 以使得标记可交互，请不要使用此方法。你可以在服务端上使用 `renderToNodeStream` 或在前端上使用 `ReactDOM.hydrate()` 来代替此方法。



