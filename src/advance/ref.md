# refs
>Refs 提供了一种方式，允许我们访问 DOM 节点或在 render 方法中创建的 React 元素。

在典型的 React 数据流中，props 是父组件与子组件交互的唯一方式。要修改一个子组件，你需要使用新的 props 来重新渲染它。但是，在某些情况下，你需要在典型数据流之外强制修改子组件。被修改的子组件可能是一个 React 组件的实例，也可能是一个 DOM 元素。对于这两种情况，React 都提供了解决办法。

下面是几个适合使用 refs 的情况：
* 管理焦点，文本选择或媒体播放。
* 触发强制动画。
* 集成第三方 DOM 库。

避免使用 refs 来做任何可以通过声明式实现来完成的事情: 避免在 Dialog 组件里暴露 open() 和 close() 方法，最好传递 isOpen 属性。

## 基础使用refs
在React的历史版本中，有三种使用Refs的方法；
1. 早起版本的string方式【已过时】
    ````jsx
    class CustomTextInput extends React.Component {
        focusTextInput = () => {
            // 使用原生 DOM API 使 text 输入框获得焦点
            if (this.refs.textInput) this.refs.textInput.focus();
        }
        render() {
            // 使用 `ref` 的回调函数将 text 输入框 DOM 节点的引用存储到 React
            // 实例上（比如 this.textInput）
            return (
                <div>
                    <input
                        type="text"
                        ref="textInput" // 可以在this.refs这个内置属性上获取到
                    />
                    <input
                        type="button"
                        value="Focus the text input"
                        onClick={this.focusTextInput}
                    />
                </div>
            );
        }
    }
    ````
2. 回调式的方法
    ````jsx
    class CustomTextInput extends React.Component {
        constructor(props) {
            super(props);
        }
        focusTextInput = () => {
            // 使用原生 DOM API 使 text 输入框获得焦点
            if (this.textInput) this.textInput.focus();
        }
        render() {
            // 使用 `ref` 的回调函数将 text 输入框 DOM 节点的引用存储到 React
            // 实例上（比如 this.textInput）
            return (
                <div>
                    <input
                        type="text"
                        ref={(e) => this.textInput = e; } // 回调函数的参数就是该元素/组件，直接赋值给一个属性即可
                    />
                    <input
                        type="button"
                        value="Focus the text input"
                        onClick={this.focusTextInput}
                    />
                </div>
            );
        }
    }
    ````
3. 16.3及后面版本React.createRef()方式
    ````jsx
    class MyComponent extends React.Component {
        constructor(props) {
            super(props);
            this.myRef = React.createRef();
        }
        render() {
            return <div ref={this.myRef} />;
        }
    }
    ````
    当 ref 被传递给 render 中的元素时，对该节点的引用可以在 ref 的 current 属性中被访问。`const node = this.myRef.current`; 
    ref 的值根据节点的类型而有所不同：
        * 当 ref 属性用于 HTML 元素时，构造函数中使用 React.createRef() 创建的 ref 接收底层 DOM 元素作为其 current 属性。
        * 当 ref 属性用于自定义 class 组件时，ref 对象接收组件的挂载实例作为其 current 属性。
        * 你不能在函数组件上使用 ref 属性，因为他们没有实例。
        * React 会在组件挂载时给 current 属性传入 DOM 元素，并在组件卸载时传入 null 值。ref 会在 componentDidMount 或 componentDidUpdate 生命周期钩子触发前更新。
        * 如果 ref 回调函数是以内联函数的方式定义的，在更新过程中它会被执行两次，第一次传入参数 null，然后第二次会传入参数 DOM 元素。这是因为在每次渲染时会创建一个新的函数实例，所以 React 清空旧的 ref 并且设置新的。通过将 ref 的回调函数定义成 class 的绑定函数的方式可以避免上述问题，但是大多数情况下它是无关紧要的。

## 深度实践



