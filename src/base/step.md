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
上面的方法的确可以快速的无污染的引入react，但是这并不是最完美的方案，我们可以使用一些工具链或者脚手架之类的完成更多的事情：
1. 大规模的组织组件
2. 使用第三方的开源包
3. 灵活的开发css和js
4. 优化编译后的产出文件

react团队针对不同的常见，建议大家使用不同的工具链来辅助开发：
1. 对于学习react或者创建一个单页应用SPA，可以使用Create React App
2. 如果是需要完成node服务端的渲染的话可以考虑使用Next.js
3. 如果是一个静态的内容导向站点，可以考虑使用[Gatsby](https://github.com/gatsbyjs/gatsby)
4. 当然如果是搭建组件库，需要更灵活的工具链，包括以下三部分组成：
    * node包管理器Yarn or npm. 可以使用第三方的库以及更新版本管理等操作
    * 打包工具webpack或者parcel等，可以根据需要打包成合适的产出
    * 编译器babel，可以使得书写的代码可以在陈旧版本的浏览器上正常运行

我们重点讲解如何使用官方的Create React App来构建react应用
### Create React App使用
这里是简单的描述Create React App使用和提供的功能，完整的Create React App内容还是需要去[Create React App官网](https://facebook.github.io/create-react-app/docs/getting-started)，Create React App本身使得你不用关注babel和webpack的配置【虽然是使用webpack+babel来实现的】，只需要关注代码本身，

快速开始：
````
npx create-react-app my-app
cd my-app
npm start
````
生成一个如下结构的文件夹：
````
my-app
├── README.md
├── node_modules
├── package.json
├── .gitignore
├── public
│   ├── favicon.ico
│   ├── index.html
│   └── manifest.json
└── src
    ├── App.css
    ├── App.js
    ├── App.test.js
    ├── index.css
    ├── index.js
    ├── logo.svg
    └── serviceWorker.js
````
就可以在 http://localhost:3000/ 访问到一个demo页面了；

同时create-react-app还有开启很多webpack的功能：
1. css-module的能力
2. code-split的能力

## 已有的webpack项目的引用
对于其他的webpack的项目，引入react很简单，就像正常的npm包一样使用，但是使用 babel需要处理jsx语法，

不过我们一个工程可能会存在很多目录，我们需要确定我们的react版本，同时也需要判断是不是各个使用当前最新版本的react，

1. 使用相同版本react，可以考虑使用当前最新的版本，然后整个工程使用这个版本，并使用npn-loack或者yarn-lock的方式锁定版本
2. 使用webpack的外部扩展externals字段来使用各页面引入的react和reactDOM，可以每个页面使用不同的版本react。
