# react的事件
React 元素的事件处理和 DOM 元素的很相似，但是有一点语法上的不同:

React 事件的命名采用小驼峰式（camelCase），而不是纯小写。
使用 JSX 语法时你需要传入一个函数作为事件处理函数，而不是一个字符串。
在 React 中另一个不同点是你不能通过返回 false 的方式阻止默认行为。你必须显式的使用 preventDefault 。

## 基础使用
常见的`react`的事件使用如下：
````js
class Toggle extends React.Component {
  constructor(props) {
    super(props);
    this.state = {isToggleOn: true};
    // 为了在回调中使用 `this`，这个绑定是必不可少的
    this.handleClick = this.handleClick.bind(this);
  }

  handleClick() {
    this.setState(state => ({
      isToggleOn: !state.isToggleOn
    }));
  }

  render() {
    return (
      <button onClick={this.handleClick}>
        {this.state.isToggleOn ? 'ON' : 'OFF'}
      </button>
    );
  }
}
````
可以看到react的事件就是直接绑定在jsx的组件元素上面即可，不过有一点需要注意，你必须谨慎对待 JSX 回调函数中的 this，在 JavaScript 中，class 的方法默认不会绑定 this。如果你忘记绑定 this.handleClick 并把它传入了 onClick，当你调用这个函数的时候 this 的值为 undefined。

可以使用以下三种方式：
1. 在constructor使用bind来传入this，this.handleClick = this.handleClick.bind(this);
2. 使用新的 class fields 语法；
    ````
    class LoggingButton extends React.Component {
    // 此语法确保 `handleClick` 内的 `this` 已被绑定。
    // 注意: 这是 *实验性* 语法。
    handleClick = () => {
        console.log('this is:', this);
    }

    render() {
        return (
        <button onClick={this.handleClick}>
            Click me
        </button>
        );
    }
    }
    ````
3. onClick回调中使用箭头函数：
    ````
    render() {
        // 此语法确保 `handleClick` 内的 `this` 已被绑定。
        return (
        <button onClick={(e) => this.handleClick(e)}>
            Click me
        </button>
        );
    }
    ````

以上三种方法都是比较好的，不过我们应该避免在组件/元素的onClick这个属性上传递，那么还是建议不要在onClick属性中传递匿名函数，

**这个并不是针对onClick，而是针对于组件的某个属性是函数的时候，最好不要直接传递一个匿名的箭头函数，避免不必要的重新render，因为匿名函数每次渲染的时候都会被认为是一个新的属性，这样这个组件就会重新渲染**

### 事件参数传递
在循环中，通常我们会为事件处理函数传递额外的参数。例如，若 id 是你要删除那一行的 ID，以下两种方式都可以向事件处理函数传递参数：
````html
<button onClick={(e) => this.deleteRow(id, e)}>Delete Row</button>
<button onClick={this.deleteRow.bind(this, id)}>Delete Row</button>
````
上述两种方式是等价的，分别通过箭头函数和 Function.prototype.bind 来实现。

在这两种情况下，React 的事件对象 e 会被作为第二个参数传递。如果通过箭头函数的方式，事件对象必须显式的进行传递，而通过 bind 的方式，事件对象以及更多的参数将会被隐式的进行传递。

## 常见事件列表及原生事件的区别
>React 事件的命名采用小驼峰式（camelCase），而不是纯小写。
使用 JSX 语法时你需要传入一个函数作为事件处理函数，而不是一个字符串。
在 React 中另一个不同点是你不能通过返回 false 的方式阻止默认行为。你必须显式的使用 preventDefault 。



## 简单了解原理


### 合成事件的基本实现

