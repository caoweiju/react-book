# 兄弟组件通信
在开发过程中，我们经常遇到一些兄弟组件之间的通信和关联操作，这种场景我们需要通过一些方式来完成这个功能，主要的方式还是依赖于父组件作为桥梁：
1. 直接将兄弟组件的通信提升到父组件；
2. 父组件分别获取兄弟子组件，并将通信方法分别传递到子组件中。

## 提升状态props
这种方式很好理解，就是讲通信的数据和方法都定义在父组件中，然后分别通过props传递到子组件中，兄弟子组件需要通信的时候分别调用对应方法即可：
- 父组件定义好子组件A、子组件B需要的数据放在state={a: 0, b: 1}中;
- 分别定义修改子组件A、子组件B数据的方法，`changeA= (a) => this.setState({a})、changeB = (b) => this.setState({b})`;
- 将state中的数据a 和 changeB方法传入子组件A，`<ComponentA a={this.state.a} changeB={this.changeB}  />`;
- 将state中的数据b 和 changeA方法传入子组件A，`<ComponentB a={this.state.b} changeB={this.changeA}  />`;
- 这样就可以在兄弟组件A/B中分别调用changeA/B方法，来改变兄弟组件，实现兄弟组件的联动。

````jsx
class ParentComp extends React.Component {
  state = {
      a: 0,
      b: 1,
  }
  changeA = (a) => {
      this.setState({a})
  }
  changeB = (b) => {
      this.setState({b})
  }
  render() {
    return (
        <div>
            <ChildCompA a={this.state.a} changeB={this.changeB} />
            <ChildCompB b={this.state.b} changeA={this.changeA} />
        </div>
    )
  }
}
function ChildCompA = ({a, changeB}) => {
    return (
        <div onClick={() => {
            changeB(Math.random());
        }}>
            {a}
        </div>
    )
}
function ChildCompB = ({b, changeA}) => {
    return (
        <div onClick={() => {
            changeA(Math.random());
        }}>
            {b}
        </div>
    )
}

````
这种方法对于较为底层的兄弟组件而已比较不适合，特别是两个组件的最近同一父级层级较高的时候，使用的时候需要看情况。

## 通过父级组件作为桥梁
上面的方法其实是把所有的状态提升到父组件，而当前这个方法不太一样，状态数据还是在对应的子组件中，但是需要子组件提供一个实例方法来修改自身，这样父组件获取子组件的对应实例，调用修改方法就可以作为桥梁来完成兄弟组件通信，

步骤如下：
- 父组件通过ref分别获取子组件的实例CompA_Ref和CompB_Ref；
- 父组件提供修改子组件的方法 changeCompA、changeCompB，通过调用子组件的实例CompA_Ref和CompB_Ref上对应的change方法；
- 子组件A/B分别在state存储各自的数据，同时提供change方法来改变自己的state值；
- 父组件将通过子组件实例提供的changeCompA、changeCompB分别传入给组件B、A;

````jsx
class ParentComp extends React.Component {
  state = {
      a: 0,
      b: 1,
  }
  changeCompA = () => {
      this.CompA_Ref && this.CompA_Ref.change();
  }
  changeCompB = () => {
      this.CompB_Ref && this.CompB_Ref.change();
  }
  render() {
    return (
        <div>
            <ChildCompA ref={(comp) => this.CompA_Ref=comp } changeB={this.changeCompB} />
            <ChildCompB ref={(comp) => this.CompB_Ref=comp } changeA={this.changeCompA} />
        </div>
    )
  }
}
class ChildCompA extends React.Component {
  state = {
      a: 0,
  }
  change = () => {
      this.setState({a: Math.random()})
  }
  render() {
    return (
        <div onClick={() => {
            this.props.changeB();
        }}>
            {this.state.a}
        </div>
    )
  }
}
class ChildCompB extends React.Component {
  state = {
      b: 1,
  }
  change = () => {
      this.setState({b: Math.random()})
  }
  render() {
    return (
        <div onClick={() => {
            this.props.changeA();
        }}>
            {this.state.b}
        </div>
    )
  }
}
````

**不建议直接将子组件的实例分别传入兄弟组件中直接使用，会有性能问题**



