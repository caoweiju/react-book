# 一步一步的基础使用React
这里将会讲述怎么在我们的项目中使用react，包括了几种方式：
1. 已有的传统无打包前端项目
2. 新项目
3. 已有的webpack等项目

## 直接添加React到website
> React 从一开始就被设计为逐步采用，并且你可以根据需要选择性地使用 React。可能你只想在现有页面中“局部地添加交互性”。

我们的有一些项目可能已经有一些年头了，但是同样想使用react，我们也有途径可以做到，根据下面的步骤：
1. 引入react
2. 使用react渲染

### 使用实践
1. 对于传统的前端项目，我们通过`<script>`引入react，后面会再详细讲解各个版本的变化。
    ````html
    <body>
        <!-- ... 其它 HTML ... -->

        <!-- 加载 React -> react组件、ReactDOM -> 关于dom部分 -->
        <!-- 注意: 部署时，将 "development.js" 替换为 "production.min.js"。-->
        <script src="https://unpkg.com/react@16/umd/react.development.js" crossorigin></script>
        <script src="https://unpkg.com/react-dom@16/umd/react-dom.development.js" crossorigin></script>
        <!-- 引入书写的使用react的js代码 -->
        <script src="react_use.js"></script>
    </body>
    ````
2. 在HTML文档中定义一个容器元素，可以body下的唯一根元素[如果整个页面都想使用react渲染]，也可以按需选择需要的使用react渲染的节点，也可以定义多个react容器节点，通过使用querySelector+id来找到容器节点，节点内的内容将全部交由react来渲染替换；
    ````html
    <!-- ... 其它 HTML ... -->
    <div id="react_container"></div>
    <!-- ... 其它 HTML ... -->

    <!-- ... 或者整个页面有react控制 ... -->
    <body>
    <div id="root_react_container"></div>
    </body>
    ````
3. 编写react的渲染代码，使用上面提到的新建js文件`react_use.js`，
    ````js
    const reactComponent = React.createElement(
        'div', // 元素/组价 html元素或者大写字母开头的React节点
        { onClick: () => this.setState({ liked: true }) }, // 属性对象 props
        'Like' // 子元素/子组件
        );
    const domContainer = document.querySelector('#react_container'); // root_react_container 或者使用整夜页面的id
    ReactDOM.render(e(LikeButton), domContainer);   // 把react生成的节点塞入dom容器节点中
    ````
4. 完成了。

## 使用官方的构建工具Create React App

### 安装

### 使用


## 已有的webpack项目的引用


## 各种方式的区别
1. jsx
2. 版本
3. 渲染