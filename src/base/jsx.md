# React的渲染
在react的官网上，最开始说的就是关于react的声明式渲染，而这一部分主要体现在react的渲染方式上面，react很灵活的把渲染内容交由开发者来管理，就像正常书写HTML一样，
````js
const name = 'Josh Perez';
const element = <h1>Hello, {name}</h1>;

ReactDOM.render(
  element,
  document.getElementById('root')
);
````
上面这段代码，大部分是js的语法，但是多了一些js以外的内容，这就是JSX【JavaScript XML】，是一个 JavaScript 的语法扩展。我们建议在 React 中配合使用 JSX，JSX 可以很好地描述 UI 应该呈现出它应有交互的本质形式。JSX 可能会使人联想到模版语言，但它具有 JavaScript 的全部功能。

React 认为渲染逻辑本质上与其他 UI 逻辑内在耦合，比如，在 UI 中需要绑定处理事件、在某些时刻状态发生变化时需要通知到 UI，以及需要在 UI 中展示准备好的数据。React 并没有采用将标记与逻辑进行分离到不同文件这种人为地分离方式，而是通过将二者共同存放在称之为“组件”的松散耦合单元之中，来实现关注点分离。

**综上：这种混合UI[html元素]和js语法的部分就是JSX**

## JSX的学习使用
我们先来看一段简单的代码，这是一个很简单的JSX表达式：
````js
const element = <h1>Hello, {name}</h1>;
````
JSX的变量可以是UI和JS语法的结合体，并使用在任何JSX代码中：
````js
function getGreeting(user) {
  if (user) {
    return <h1>Hello, {formatName(user)}!</h1>;
  }
  return <h1>Hello, Stranger.</h1>;
}
// 复杂一点的使用
function formatName(user) {
  return user.firstName + ' ' + user.lastName;
}

const user = {
  firstName: 'Harper',
  lastName: 'Perez'
};

const element = (
  <h1>
    Hello, {formatName(user)}!
  </h1>
);
````
### JSX的特性

就像我们看到的，JSX包括了UI部分的HTML元素，那么就可以添加各种属性，而JSX支持两种引入属性的方式，
* 字符串字面量,仅支持字符串：`const element = <div tabIndex="0"></div>;`
* JavaScript表达式【赋值、条件if、for代码块都不算表达式】：`const element = <img src={user.avatarUrl}></img>;`

> 因为 JSX 语法上更接近 JavaScript 而不是 HTML，所以 React DOM 使用 camelCase（小驼峰命名）来定义属性的名称，而不使用 HTML 属性名称的命名约定。例如，JSX 里的 class 变成了 className，而 tabindex 则变为 tabIndex。

JSX还可以防止注入攻击，React DOM 在渲染所有输入内容之前，默认会进行转义。它可以确保在你的应用中，永远不会注入那些并非自己明确编写的内容。所有的内容在渲染之前都被转换成了字符串。这样可以有效地防止 XSS（cross-site-scripting, 跨站脚本）攻击。


## JSX的深度使用


## JSX的本质
从本质上来讲，JSX最终是要表示对象，JSX的语法是名为 `React.createElement() `函数的语法糖，


