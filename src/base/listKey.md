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
key 帮助 React 识别哪些元素改变了，比如被添加或删除。因此你应当给数组中的每一个元素赋予一个确定的标识。一个元素的 key 最好是这个元素在列表中拥有的一个独一无二的字符串。通常，我们使用来自数据 id 来作为元素的 key：当元素没有确定 id 的时候，万不得已你可以使用元素索引 index 作为 key。

> 可以看到一个有意思的内容，一个JSX的表达式或者函数返回的一般都是一个唯一的组件，但是使用map函数可以允许你返回多个并列的同级组件，当然16.3之后还可以使用segement来包裹多个组件。

## dom diff的过程和列表优化
在某一时间节点调用 React 的 render() 方法，会创建一棵由 React 元素组成的树。在下一次 state 或 props 更新时，相同的 render() 方法会返回一棵不同的树。React 需要基于这两棵树之间的差别来判断如何有效率的更新 UI 以保证当前 UI 与最新的树保持同步。

如果在 React 中使用了该算法，那么展示 1000 个元素所需要执行的计算量将在十亿的量级范围。这个开销实在是太过高昂。于是 React 在以下两个假设的基础之上提出了一套 O(n) 的启发式算法：两个不同类型的元素会产生出不同的树；
1. 开发者可以通过 key prop 来暗示哪些子元素在不同的渲染下能保持稳定；
2. 在实践中，我们发现以上假设在几乎所有实用的场景下都成立。

所以有三种情况：
1. 当根节点为不同类型的元素时，React 会拆卸原有的树并且建立起新的树。
2. 当比对两个相同类型的 React 元素时，React 会保留 DOM 节点，仅比对及更新有改变的属性。
3. 对比list元素的是时候根据key进行处理增删改操作。 

对于list的diff过程，完全是根据当前list的key来决定的，所以key是我们应该尽可能的保持唯一性和不变性的方式；
> 当list发生变化或者组件被重新渲染的时候，如果之前的key还存在的话，那么仅需要修改当前元素的变更部分，如果key对于元素 不存在，那么删除，如果有新的key只是重新构建组件

