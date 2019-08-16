## 条件渲染
>在 React 中，你可以创建不同的组件来封装各种你需要的行为。然后，依据应用的不同状态，你可以只渲染对应状态下的部分内容。

条件渲染有以下几种方式：
1. if/else的条件语句,不可以直接在jsx表达式/变量中使用
    ````jsx
    // 函数组件
    function Greeting(props) {
        const isLoggedIn = props.isLoggedIn;
        if (isLoggedIn) {
            return <UserGreeting />;
        }
        return <GuestGreeting />;
    }
    // class组件
    render() {
        const isLoggedIn = this.state.isLoggedIn;
        let button;

        if (isLoggedIn) {
        button = <LogoutButton onClick={this.handleLogoutClick} />;
        } else {
        button = <LoginButton onClick={this.handleLoginClick} />;
        }

        return (
        <div>
            <Greeting isLoggedIn={isLoggedIn} />
            {button}
        </div>
        );
    }
    ````
2. 逻辑运算符 &&
    ````jsx
    function Mailbox(props) {
        const unreadMessages = props.unreadMessages;
        return (
            <div>
            <h1>Hello!</h1>
            {unreadMessages.length > 0 &&
                <h2>
                You have {unreadMessages.length} unread messages.
                </h2>
            }
            </div>
        );
    }
    ````
3. 三目运算符 ？
    ````jsx
    render() {
        const isLoggedIn = this.state.isLoggedIn;
        return (
            <div>
            The user is <b>{isLoggedIn ? 'currently' : 'not'}</b> logged in.
            </div>
        );
    }
    ````

在极少数情况下，你可能希望能隐藏组件，即使它已经被其他组件渲染。若要完成此操作，你可以让 render 方法直接返回 null，而不进行任何渲染。但是在组件的 render 方法中返回 null 并不会影响组件的生命周期。
## 列表
react允许你可以通过使用 {} 在 JSX 内构建一个元素集合。

下面，我们使用 Javascript 中的 map() 方法来遍历 numbers 数组。将数组中的每个元素变成 <li> 标签，最后我们将得到的数组赋值给 listItems：
````jsx
function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    <li key={number.toString()}>
      {number}
    </li>
  );
  return (
    <ul>{listItems}</ul>
  );
}

const numbers = [1, 2, 3, 4, 5];
ReactDOM.render(
  <NumberList numbers={numbers} />,
  document.getElementById('root')
);
````
key 帮助 React 识别哪些元素改变了，比如被添加或删除。因此你应当给数组中的每一个元素赋予一个确定的标识。一个元素的 key 最好是这个元素在列表中拥有的一个独一无二的字符串。通常，我们使用来自数据 id 来作为元素的 key：当元素没有确定 id 的时候，万不得已你可以使用元素索引 index 作为 key：

### 列表渲染优化

## dom diff的过程

