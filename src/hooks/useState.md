## useState简介
经过对hooks的简单了解，我们可以继续看下面这个例子：
````jsx
class Example extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      count: 0
    };
  }

  render() {
    return (
      <div>
        <p>You clicked {this.state.count} times</p>
        <button onClick={() => this.setState({ count: this.state.count + 1 })}>
          Click me
        </button>
      </div>
    );
  }
}
```
上面是一个经典的class组件，设置了state，并通过onClick来修改state，是组件完成更新；在`16.8`之前的版本里面，函数组件被称为无状态组件，想要使用state就需要使用class组件，但是在 `16.8`及以后的版本中，我们可以在函数组件中使用state，就是使用 `useState`来完成，下面的例子就是实现和class组件一样的功能：
````jsx
import React, { useState } from 'react';

function Example() {
  // 声明一个叫 "count" 的 state 变量
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}
````

>Hook 是什么？ Hook 是一个特殊的函数，它可以让你“钩入” React 的特性。例如，useState 是允许你在 React 函数组件中添加 state 的 Hook。

上面的示例函数组件，由于没有this所以不能使用this.state，故而引入了useState这个hooks，将state引入到函数组件。

## useState使用实践
````jsx
import React, { useState } from 'react';

function Example() {
  // 声明一个叫 “count” 的 state 变量
  const [count, setCount] = useState(0);
  // ...
}
````
示例中声明了一个叫 count 的 state 变量，然后把它设为 0。React 会在重复渲染时记住它当前的值，并且提供最新的值给我们的函数。我们可以通过调用 setCount 来更新当前的 count。

而useState整体的效果就是：
1. 所有的hooks都在react包中，我们需要引入并使用；
2. 调用 useState 方法的时候，需要传入一个参数，也就是这个state的初始值（这个初始值也可以是函数），state可以任何JavaScript支持的数据类型；
3. 调用 useState 方法的返回值为：当前 state 以及更新 state 的函数。这就是我们写 `const [count, setCount] = useState() `的原因，通过数组解构来获取返回值。这与 class 里面 this.state.count 和 this.setState 类似，唯一区别就是你需要成对的获取它们以及useState返回的设置函数setCount调用时是覆盖式的设置state，而this.setState则是merge合并式修改。
4. 调用 useState 方法的时候具体就是：定义一个 “state 变量”。我们的变量叫 count， 但是我们可以叫他任何名字，并且定义一个修改该“state 变量”count的方法setCount，正常而言在函数组件执行完成之后变量count的方法setCount就就会”消失”，但是react保存了这些变量，从而达到count和setCount在函数组件中一直被保存的效果，这样就和class组件的state可以起一样的作用了。
5. 因为hook是函数组件中直接调用的方法，所以在每次mount或者update时运行函数组件就会顺序运行hook，不过useState这个hook有一个特性，就是第一次执行useState的时候会创建一个state变量和设置变量的方法，但是之后的update引起的执行useState只会返回之前最开始创建的state变量和设置变量的方法，就是前面提到的useState的返回值在第一次执行的时候创建并一直被保存，后面执行的时候直接返回即可。

**需要注意：和class组件的setState一样，Hook的useState返回的setState也可能是异步的，并不是在执行setState之后就可以获取到最新的state值得**
````jsx
import React, { useReducer, useState, useEffect } from "react";
import ReactDOM from "react-dom";
import "./styles.css";

function Counter() {
  // First render will create the state, and it will
  // persist through future renders
  const [count, setCount] = useState(0);
  const [sum, dispatch] = useReducer((state, action) => {
    return state + action;
  }, 0);
  useEffect(() => {
    setCount(count + 1);
    setCount(count + 1);
  }, [sum])
  return (
    <>
      {sum}

      <button onClick={() => dispatch(1)}>Add 1</button>
    </>
  );
}

ReactDOM.render(<Counter />, document.querySelector("#root"));
````

上面的示例中的useEffect中连续调用了两次setCount，但是在调用间隔中查看结果还是一样都为上次一的结果0；因为异步的原因，setCount之后获取到的还是当前的count值，所以执行两次后count只是+1，可以改成使用函数来更新count；
````jsx
useEffect(() => {
    setCount(count => count + 1);
    setCount(count => count + 1);
  }, [sum])
````
这样，执行完两次setCount，更新后的数据就是+2了。

### 惰性初始 state
useState的第一个参数initialState 只会在组件的初始渲染中起作用，后续渲染执行useState的时候会被忽略而返回之前保存的内容。

如果初始 state 需要通过复杂计算获得，则可以传入一个函数，在函数中计算并返回初始的 state，同样此函数只在初始渲染时被调用：
````js
const [state, setState] = useState(() => {
  const initialState = someExpensiveComputation(props);
  return initialState;
});
````

### 函数式更新state
在Hook返回的setState是通过替换来完成state更新，而不是合并，那么如果新的 state 需要通过使用先前的 state 计算得出，开发者可以将函数传递给 setState。该函数将接收先前的 state，并返回一个更新后的值。下面的计数器组件示例展示了 setState 的两种用法：
````js
function Counter({initialCount}) {
  const [count, setCount] = useState(initialCount);
  return (
    <>
      Count: {count}
      <button onClick={() => setCount(initialCount)}>Reset</button>
      <button onClick={() => setCount(prevCount => prevCount - 1)}>-</button>
      <button onClick={() => setCount(prevCount => prevCount + 1)}>+</button>
    </>
  );
}
````

### 使用多个 state 变量

因为useState的返回值中设置state的方法是通过直接替换的方式来完成state的更新，那么我们的state不应该是层级太深，不然设置的时候也比较的曲折，所以我们可以考虑将state拆分在不同的多个state变量中，也就是多次调用useState来声明多个state，
````jsx
function ExampleWithManyStates() {
  // 声明多个 state 变量
  const [age, setAge] = useState(42);
  const [fruit, setFruit] = useState('banana');
  const [todos, setTodos] = useState([{ text: '学习 Hook' }]);
````

通过声明多个变量还可以完成对不同state的分别设置。


