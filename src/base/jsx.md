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
>jsx的自定义组件名字必须是大写字母开头, 由于 JSX 会编译为 React.createElement 调用形式，所以 React 库也必须包含在 JSX 代码作用域内。

就像我们看到的，JSX包括了UI部分的HTML元素，那么就可以添加各种属性，而JSX支持两种引入属性的方式，
* 字符串字面量,仅支持字符串：`const element = <div tabIndex="0"></div>;`
* JavaScript表达式【赋值、条件if、for代码块都不算表达式】：`const element = <img src={user.avatarUrl}></img>;`

> 因为 JSX 语法上更接近 JavaScript 而不是 HTML，所以 React DOM 使用 camelCase（小驼峰命名）来定义属性的名称，而不使用 HTML 属性名称的命名约定。例如，JSX 里的 class 变成了 className，而 tabindex 则变为 tabIndex。

JSX还可以防止注入攻击，React DOM 在渲染所有输入内容之前，默认会进行转义。它可以确保在你的应用中，永远不会注入那些并非自己明确编写的内容。所有的内容在渲染之前都被转换成了字符串。这样可以有效地防止 XSS（cross-site-scripting, 跨站脚本）攻击。

jsx还支持点语法：
````jsx
  // 由于 JSX 会编译为 React.createElement 调用形式，所以 React 库也必须包含在 JSX 代码作用域内。
  import React from 'react';

  const MyComponents = {
    DatePicker: function DatePicker(props) {
      return <div>Imagine a {props.color} datepicker here.</div>;
    }
  }
  function BlueDatePicker() {
    return <MyComponents.DatePicker color="blue" />;
  }
````

jsx通用支持运行时来选择渲染的组件写法
````jsx
import React from 'react';
import { PhotoStory, VideoStory } from './stories';

const components = {
  photo: PhotoStory,
  video: VideoStory
};

function Story(props) {
  // 正确！JSX 类型可以是大写字母开头的变量。
  const SpecificStory = components[props.storyType];
  return <SpecificStory story={props.story} />;
}
````

jsx允许开发者把包裹在 {} 中的 JavaScript 表达式作为一个 prop 传递给 JSX 元素。if 语句以及 for 循环不是 JavaScript 表达式，所以不能在 JSX 中直接使用。但是，你可以用在 JSX 以外的代码中；Props 默认值为 “True”

````jsx
  function NumberDescriber(props) {
    let description;
    if (props.number % 2 == 0) {
      description = <strong>even</strong>;
    } else {
      description = <i>odd</i>;
    }
    return <div>{props.number} is an {description} number</div>;
  }
  // 一下两行等价
  <MyTextBox autocomplete />

  <MyTextBox autocomplete={true} />
````

jsx同样支持属性展开
````jsx
  const Button = props => {
    const { kind, ...other } = props;
    const className = kind === "primary" ? "PrimaryButton" : "SecondaryButton";
    return <button className={className} {...other} />;
  };

  const App = () => {
    return (
      <div>
        <Button kind="primary" onClick={() => console.log("clicked!")}>
          Hello World!
        </Button>
      </div>
    );
  };
````

jsx子元素的类型可以使很多种：
1. 字符串字面量：JSX 会移除行首尾的空格以及空行。与标签相邻的空行均会被删除，文本字符串之间的新行会被压缩为一个空格。
    ````jsx
    // 一下几行是一样的
    <div>Hello World</div>

    <div>
      Hello World
    </div>

    <div>
      Hello
      World
    </div>

    <div>

      Hello World
    </div>
    ````
2. jsx元素：单个元素，混合元素，或者存储在数组中的一组元素
    ````jsx
    <MyContainer>
      <MyFirstComponent />
      <MySecondComponent />
    </MyContainer>

    <div>
      Here is a list:
      <ul>
        <li>Item 1</li>
        <li>Item 2</li>
      </ul>
    </div>

    render() {
      // 不需要用额外的元素包裹列表元素！
      return [
        // 不要忘记设置 key :)
        <li key="A">First item</li>,
        <li key="B">Second item</li>,
        <li key="C">Third item</li>,
      ];
    }
    ````
3. javascript表达式/函数: `{}`大把括号包围的，可以使开发者结合使用的长比较多。


jsx对于一下额子元素：false, null, undefined, and true处理，虽然都是合法的子元素。但它们并不会被渲染。但是其他的伪真数值会被渲染: `0`


## JSX的本质
从本质上来讲，JSX最终是要表示对象，JSX的语法是名为 `React.createElement() `函数的语法糖，以下两种示例代码完全等效：
````js
const element = (
  <h1 className="greeting">
    Hello, world!
  </h1>
);
````
````js
const element = React.createElement(
  'h1', // tagname
  {className: 'greeting'},  // props
  'Hello, world!' // 子元素 children
);
````

`React.createElement()` 会预先执行一些检查，以帮助你编写无错代码，但实际上它创建了一个这样的对象：
````js
// 注意：这是简化过的结构
const element = {
  type: 'h1',
  props: {
    className: 'greeting',
    children: 'Hello, world!'
  }
};
````
这些对象被称为 “React 元素”。它们描述了你希望在屏幕上看到的内容。React 通过读取这些对象，然后使用它们来构建 DOM 以及保持随时更新。


## React渲染
使用JSX就是能够方便开发者书写想要的界面，而这些内容最好是要渲染成真正的HTML展示的，元素是构成 React 应用的最小砖块。元素描述了你在屏幕上想看到的内容。`const element = <h1>Hello, world</h1>;`
> 与浏览器的 DOM 元素不同，React 元素是创建开销极小的普通对象【就是上文提到的JSX对象本质】。React DOM 会负责更新 DOM 来与 React 元素保持一致。

之前有提到，使用React就是把HTML中的某个节点完全交个React来管理和渲染更新，想要将一个 React 元素渲染到根 DOM 节点中，只需把它们一起传入 ReactDOM.render()，
````js
// HTML中交给React管理的跟节点 一般是body元素下的唯一节点
<div id="root"></div>
// react 部分
const element = <h1>Hello, world</h1>;
ReactDOM.render(element, document.getElementById('root'));
````

渲染完成之后，我们知道很多时候通过一些交互，我们需要完成更新，我们可以通过修改JSX元素，重新执行ReactDOM.render, ReactDOM会将元素和它的子元素与它们之前的状态进行比较，并只会进行必要的更新来使 DOM 达到预期的状态。这样就可以提高性能，避免不必要的渲染。

那么除了频繁的调用ReactDOM.render外，其实React还提供了方法让开发者自己定位需要修改的内容，只要更新对于元素/组件的props和state就可以，后面将会讲解props和state的具体内容。
