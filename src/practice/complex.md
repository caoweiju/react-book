# react中的一些细节
我们在开发过程中经常会遇到一些通用型的问题，这里会记录一些在开发过程的注意细节和解析：
- jsx的部分书写技巧
- class组件的构造函数
- 合成事件和原生事件混用
- React.PureComponent使用注意
- props的不变性
- list的key非唯一性时存在问题

## jsx的一些技巧
JSX 仅仅只是 `React.createElement(component, props, ...children)` 函数的语法糖。如下 JSX 代码：
````jsx
<MyButton color="blue" shadowSize={2}>
  Click Me
</MyButton>

// 会编译为：

React.createElement(
  MyButton,
  {color: 'blue', shadowSize: 2},
  'Click Me'
)
````

在 JSX 中，你也可以使用点语法来引用一个 React 组件。当你在一个模块中导出许多 React 组件时(UI组件库等)，这会非常方便。例如，如果 `MyComponents.DatePicker` 是一个组件，你可以在 JSX 中直接使用：
````jsx
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
你不能将通用表达式作为 React 元素类型。如果你想通过通用表达式来（动态）决定元素类型，你需要首先将它赋值给大写字母开头的变量。
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
如果你已经有了一个 props 对象，你可以使用展开运算符 ... 来在 JSX 中传递整个 props 对象。以下两个组件是等价的：
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
## class类的constructor
在使用class组件的时候，我们常见的构造函数和state声明以及方法this的绑定一般会在这里进行；
````js
    constructor(props) {
        super(props);
        this.state = {date: new Date()};
        this.clickHander = this.clickHander.bind(this);
    }
````
可以看到，JavaScript的class的构造方法，这样书写和es6引入的class的方式有关，首先是class组件都需要继承基类React.Component/React.PureComponent，子类构造函数需要限制性super方法，来实例化子类this，同时需要传递props作为super参数，因为这样props才会被传入到this对象上，当然如果构造函数中没有使用`this.props`的话，就没有影响，其他生命周期中使用可以正常访问`this.props`，不受super传参与否的影响，因为react后面手动将props加入子类实例this中。

>ES5 的继承，实质是先创造子类的实例对象this，然后再将父类的方法添加到this上面（Parent.apply(this)）。ES6 的继承机制完全不同，实质是先将父类实例对象的属性和方法，加到this上面（所以必须先调用super方法），然后再用子类的构造函数修改this。


## 合成事件和原生事件混用
>谨慎对待 JSX 回调函数中的 this，在 JavaScript 中，class 的方法默认不会绑定 this。如果你忘记绑定 this.handleClick 并把它传入了 onClick，当你调用这个函数的时候 this 的值为 undefined。onClick的默认第一个参数是 event，当然如果通过bind传入了其他参数，那么event参数将会被后置；

### 简单了解合成事件原理
>如果DOM上绑定了过多的事件处理函数，整个页面响应以及内存占用可能都会受到影响。React为了避免这类DOM事件滥用，同时屏蔽底层不同浏览器之间的事件系统差异，实现了一个中间层——SyntheticEvent。

React并不是将click事件绑在该div的真实DOM上，而是在document处监听所有支持的事件，当事件发生并冒泡至document处时，React将事件内容封装并交由真正的处理函数运行。

其中，由于event对象是复用的【因为react使用了事件池机制来管理事件】，事件处理函数执行完后，属性会被清空，所以event的属性无法被异步访问，详情请查阅event-pooling。

合成事件是通过点击事件的冒泡来完成的，整体过程如下：
1. 绑定onClick事件
2. react-dom的render/hydrate方法将会有document代理所有绑定了onClick事件的元素/组件；
3. 发生点击时，事件冒泡到了document上，在事件池中找到空闲合成事件，设置为本次合成事件，绑定对应的属性和方法；
4. 顺序执行整个冒泡过程中的所有事件回调

### 合成事件和原生事件混用注意顺序
鉴于react的事件机制，在和DOM event同时使用的时候，需要注意事件的顺序；大致的顺序如下：
1. 父元素的dom event的捕获事件
2. 子组件的dom event的捕获事件
3. 子组件的dom event的冒泡事件
4. 父组件的dom event的冒泡事件
5. 父元素的react event的捕获事件
6. 子组件的react event的捕获事件
7. 子组件的react event的冒泡事件
8. 父组件的react event的冒泡事件

可以看到合成事件的捕获事件也是在原生事件的冒泡之后才执行，所以不是严格的捕获/冒泡时期，但是顺序是正常的。

## React.PureComponent


## props的不变性


## key使用index可能存在的问题
主要是而非受控组件和key：index一起使用时，可能存在展示的错误。
https://medium.com/@robinpokorny/index-as-a-key-is-an-anti-pattern-e0349aece318




