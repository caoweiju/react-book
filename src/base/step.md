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
    const ReactChildComponent = React.createElement(
        'p', // 元素/组价 html元素或者大写字母开头的React节点
        { style: { color: 'red', paddingTop: 10 } }, // 属性对象 props
        '子组件' // 子元素/子组件
        );
    const ReactComponent = React.createElement(
        'div', // 元素/组价 html元素或者大写字母开头的React节点
        { onClick: () => this.setState({ liked: true }) }, // 属性对象 props
        reactChildComponent // 子元素/子组件
        );
    
    const domContainer = document.querySelector('#react_container'); // root_react_container 或者使用整夜页面的id
    ReactDOM.render(ReactComponent, domContainer);   // 把react生成的节点塞入dom容器节点中
    ````
4. 已经完成了，但是上面的书写会随着节点层级的增加，嵌套深度会线性增加，可读性看起来不太友好，react在这方面提出了JSX*JavaScript XML*的概念来优化这一点*react不依赖jsx，但是可以提高效率和可读性*，上面的部分可以如此修改：
    ````js
    const ReactComponent = <div onClick={() => this.setState({ liked: true })}>
            <p>子组件</p>
        </div>;
    {/* root_react_container 或者使用整夜页面的id */}
    const domContainer = document.querySelector('#react_container'); 
    {/* 把react生成的节点塞入dom容器节点中 */}
    ReactDOM.render(ReactComponent, domContainer);   
    ````
    不过由于JSX并不是浏览器原生支持的，所以想使用和尝试的话，需要把JSX编译成常规的js，也就是把jsx还原成上面书写方式，很幸运，babel完成了这一项工作，我们可以通过引入babel来完成jsx的编译，
    ````html
    <script src="https://unpkg.com/babel-standalone@6/babel.min.js"></script>
    ````
    可以在任何 `<script>` 标签内使用 JSX，方法是在为其添加 `type="text/babel"`属性；这种方式适合于学习和创建简单的示例。然而，它会使你的网站变慢，并且不适用于生产环境。
5. 单独支持JSX的使用，安装node即可，本质上，添加 JSX 就像添加 CSS 预处理器一样。唯一的要求是你在计算机上安装了 Node.js。在终端上跳转到你的项目文件夹，然后粘贴这两个命令：
    * 步骤 1： 执行 npm init -y （如果失败，这是修复办法）
    * 步骤 2： 执行 npm install babel-cli@6 babel-preset-react-app@3
    * 执行：`npx babel --watch src/react_use.js --out-dir . --presets react-app/prod `
    会生监听路径下文件`src/react_use.js`，生成编译后的`react_use.js`, 这样，在旧浏览器上也能够使用现代 JavaScript 的语法特性，比如 class。


## 使用官方的构建工具Create React App

### 安装

### 使用


## 已有的webpack项目的引用


## 各种方式的区别
1. jsx
2. 版本
3. 渲染