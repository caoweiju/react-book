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


