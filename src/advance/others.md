## 代码分割
打包：大多数 React 应用都会使用 Webpack，Rollup 或 Browserify 这类的构建工具来打包文件。 打包是一个将文件引入并合并到一个单独文件的过程，最终形成一个 “bundle”。 接着在页面上引入该 bundle，整个应用即可一次性加载。

打包是个非常棒的技术，但随着你的应用增长，你的代码包也将随之增长。尤其是在整合了体积巨大的第三方库的情况下。你需要关注你代码包中所包含的代码，以避免因体积过大而导致加载时间过长。

为了避免搞出大体积的代码包，在前期就思考该问题并对代码包进行分割是个不错的选择。代码分割是由诸如 Webpack（代码分割）和 Browserify（factor-bundle）这类打包器支持的一项技术，能够创建多个包并在运行时动态加载。

对你的应用进行代码分割能够帮助你“懒加载”当前用户所需要的内容，能够显著地提高你的应用性能。尽管并没有减少应用整体的代码体积，但你可以避免加载用户永远不需要的代码，并在初始加载的时候减少所需加载的代码量
````jsx
import("./math").then(math => {
  console.log(math.add(16, 26));
});
````
当 Webpack 解析到该语法时，它会自动地开始进行代码分割;

### React.lazy
React.lazy 函数能让你像渲染常规组件一样处理动态引入（的组件）。
>React.lazy 和 Suspense 技术还不支持服务端渲染。如果你想要在使用服务端渲染的应用中使用，我们推荐 Loadable Components 这个库。它有一个很棒的服务端渲染打包指南。

````jsx
const OtherComponent = React.lazy(() => import('./OtherComponent'));

function MyComponent() {
  return (
    <div>
      <OtherComponent />
    </div>
  );
}
````
这个代码将会在渲染组件时，自动导入包含 OtherComponent 组件的包。

React.lazy 接受一个函数，这个函数需要动态调用 import()。它必须返回一个 Promise，该 Promise 需要 resolve 一个 defalut export 的 React 组件。

**React.lazy 目前只支持默认导出（default exports）**。如果你想被引入的模块使用命名导出（named exports），你可以创建一个中间模块，来重新导出为默认模块。这能保证 tree shaking 不会出错，并且不必引入不需要的组件。
````jsx
// ManyComponents.js
export const MyComponent = /* ... */;
export const MyUnusedComponent = /* ... */;
// MyComponent.js
export { MyComponent as default } from "./ManyComponents.js";
// MyApp.js
import React, { lazy } from 'react';
const MyComponent = lazy(() => import("./MyComponent.js"));
````
### Suspense
如果在 MyComponent 渲染完成后，包含 OtherComponent 的模块还没有被加载完成，我们可以使用加载指示器为此组件做优雅降级。这里我们使用 Suspense 组件来解决。
````jsx
const OtherComponent = React.lazy(() => import('./OtherComponent'));

function MyComponent() {
  return (
    <div>
      <Suspense fallback={<div>Loading...</div>}>
        <OtherComponent />
      </Suspense>
    </div>
  );
}
````jsx

代码分割一个不错的选择是从路由开始。大多数网络用户习惯于页面之间能有个加载切换过程。你也可以选择重新渲染整个页面，这样您的用户就不必在渲染的同时再和页面上的其他元素进行交互。

这里是一个例子，展示如何在你的应用中使用 React.lazy 和 React Router 这类的第三方库，来配置基于路由的代码分割。
````jsx
import { BrowserRouter as Router, Route, Switch } from 'react-router-dom';
import React, { Suspense, lazy } from 'react';

const Home = lazy(() => import('./routes/Home'));
const About = lazy(() => import('./routes/About'));

const App = () => (
  <Router>
    <Suspense fallback={<div>Loading...</div>}>
      <Switch>
        <Route exact path="/" component={Home}/>
        <Route path="/about" component={About}/>
      </Switch>
    </Suspense>
  </Router>
);
````


## 性能优化
>UI 更新需要昂贵的 DOM 操作，而 React 内部使用几种巧妙的技术以便最小化 DOM 操作次数。对于大部分应用而言，使用 React 时无需专门优化就已拥有高性能的用户界面。尽管如此，你仍然有办法来加速你的 React 应用。


性能优化的建议：
1. 使用production版本的react/react-dom;
2. 使用webpack等打包工具使需要配置uglifyify简化、terser压缩等操作；
3. 如果存在性能问题：[使用 Chrome Performance 标签分析组件](https://calibreapp.com/blog/react-performance-profiling-optimization/)
4. 虚拟化长列表
5. 使用shouldComponentUpdate可以优化性能，也可以继承 React.PureComponent 以代替手写 shouldComponentUpdate()。它用当前与之前 props 和 state 的浅比较覆写了 shouldComponentUpdate() 的实现。
