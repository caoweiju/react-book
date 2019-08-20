# 表单
在 React 里，HTML 表单元素的工作方式和其他的 DOM 元素有些不同，这是因为在DOM中表单元素通常会保持一些内部的 state；
比如：
* input的value；
* checkbox的cheched；
* radio的checked

## 受控组建和非受控组件

### 受控组件
在 HTML 中，表单元素（如`<input>、 <textarea> 和 <select>`）之类的表单元素通常自己维护 state，并根据用户输入进行更新。而在 React 中，可变状态（mutable state）通常保存在组件的 state 属性中，并且只能通过使用 setState()来更新。

我们可以把两者结合起来，使 React 的 state 成为“唯一数据源”。渲染表单的 React 组件还控制着用户输入过程中表单发生的操作。被 React 以这种方式控制取值的表单输入元素就叫做“受控组件”。

**需要注意的是：react的Component.onChange事件 和 DOM的 element.oninput表现一致，不需要表单失焦就会触发**
````jsx
class NameForm extends React.Component {
  constructor(props) {
    super(props);
    this.state = {value: ''};

    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleChange(event) {
    this.setState({value: event.target.value});
  }

  handleSubmit(event) {
    alert('提交的名字: ' + this.state.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          名字:
          <input type="text" value={this.state.value} onChange={this.handleChange} />
        </label>
        <input type="submit" value="提交" />
      </form>
    );
  }
}
````
由于在表单元素上设置了 value 属性，因此显示的值将始终为 this.state.value，这使得 React 的 state 成为唯一数据源。由于 handlechange 在每次按键时都会执行并更新 React 的 state，因此显示的值将随着用户输入而更新。

对于受控组件来说，每个 state 突变都有一个相关的处理函数。这使得修改或验证用户输入变得简单。

总的来说，这使得 `<input type="text">, <textarea> 和 <select>` 之类的标签都非常相似—它们都接受一个 value 属性，你可以使用它来实现受控组件。

>注意: 你可以将数组传递到 value 属性中，以支持在 select 标签中选择多个选项：`<select multiple={true} value={['B', 'C']}>`

### 非受控组件
在 HTML 中，`<input type=“file”> `允许用户从存储设备中选择一个或多个文件，将其上传到服务器，或通过使用 JavaScript 的 File API 进行控制。
`<input type="file" />`
因为它的 value 只读，所以它是 React 中的一个非受控组件。

除此之外，要编写一个非受控组件，而不是为每个状态更新都编写数据处理函数，你可以 使用 ref 来从 DOM 节点中获取表单数据。
>在 React 渲染生命周期时，表单元素上的 value 将会覆盖 DOM 节点中的值，在非受控组件中，你经常希望 React 能赋予组件一个初始值，但是不去控制后续的更新。 在这种情况下, 你可以指定一个 defaultValue 属性，而不是 value。

````jsx
class NameForm extends React.Component {
  constructor(props) {
    super(props);
    this.handleSubmit = this.handleSubmit.bind(this);
    this.input = React.createRef();
  }

  handleSubmit(event) {
    alert('A name was submitted: ' + this.input.current.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Name:
          <input defaultValue="Bob" type="text" ref={this.input} />
        </label>
        <input type="submit" value="Submit" />
      </form>
    );
  }
}
````


## 表单的常见使用示例

需要注意的是：平时处理表单提交监听enter的事件的时候，不要像使用pc的监听按键keycode一样，而应该使用form表单的onsubmit来完成。



