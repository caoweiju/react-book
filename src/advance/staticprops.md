# 元素的静态属性

## displayName
`displayName` 字符串多用于调试消息。通常，你不需要设置它，因为它可以根据函数组件或 `class` 组件的名称推断出来。如果调试时需要显示不同的名称或创建高阶组件，请参阅使用 `displayname` 轻松进行调试了解更多。

````jsx
    class WithSubscription extends React.Component {/* ... */}
    WithSubscription.displayName = `WithSubscription(${getDisplayName(WrappedComponent)})`;
    function Subscription(WrappedComponent) {
        return <div />;
    }
    Subscription.displayName='Subscription'
````
## PropTypes 
>自 React v15.5 起，React.PropTypes 已移入另一个包中。请使用 prop-types 库 代替。

React 也内置了一些类型检查的功能。要在组件的 props 上进行类型检查，你只需配置特定的 propTypes 属性：
````jsx
import PropTypes from 'prop-types';

class Greeting extends React.Component {
  render() {
    return (
      <h1>Hello, {this.props.name}</h1>
    );
  }
}

Greeting.propTypes = {
  name: PropTypes.string
};
````

PropTypes 提供一系列验证器，可用于确保组件接收到的数据类型是有效的。在本例中, 我们使用了 PropTypes.string。当传入的 prop 值类型不正确时，JavaScript 控制台将会显示警告。出于性能方面的考虑，propTypes 仅在开发模式下进行检查。
````jsx
import PropTypes from 'prop-types';

MyComponent.propTypes = {
  // 你可以将属性声明为 JS 原生类型，默认情况下
  // 这些属性都是可选的。
  optionalArray: PropTypes.array,
  optionalBool: PropTypes.bool,
  optionalFunc: PropTypes.func,
  optionalNumber: PropTypes.number,
  optionalObject: PropTypes.object,
  optionalString: PropTypes.string,
  optionalSymbol: PropTypes.symbol,

  // 任何可被渲染的元素（包括数字、字符串、元素或数组）
  // (或 Fragment) 也包含这些类型。
  optionalNode: PropTypes.node,

  // 一个 React 元素。
  optionalElement: PropTypes.element,

  // 一个 React 元素类型（即，MyComponent）。
  optionalElementType: PropTypes.elementType,

  // 你也可以声明 prop 为类的实例，这里使用
  // JS 的 instanceof 操作符。
  optionalMessage: PropTypes.instanceOf(Message),

  // 你可以让你的 prop 只能是特定的值，指定它为
  // 枚举类型。
  optionalEnum: PropTypes.oneOf(['News', 'Photos']),

  // 一个对象可以是几种类型中的任意一个类型
  optionalUnion: PropTypes.oneOfType([
    PropTypes.string,
    PropTypes.number,
    PropTypes.instanceOf(Message)
  ]),

  // 可以指定一个数组由某一类型的元素组成
  optionalArrayOf: PropTypes.arrayOf(PropTypes.number),

  // 可以指定一个对象由某一类型的值组成
  optionalObjectOf: PropTypes.objectOf(PropTypes.number),

  // 可以指定一个对象由特定的类型值组成
  optionalObjectWithShape: PropTypes.shape({
    color: PropTypes.string,
    fontSize: PropTypes.number
  }),
  
  // An object with warnings on extra properties
  optionalObjectWithStrictShape: PropTypes.exact({
    name: PropTypes.string,
    quantity: PropTypes.number
  }),   

  // 你可以在任何 PropTypes 属性后面加上 `isRequired` ，确保
  // 这个 prop 没有被提供时，会打印警告信息。
  requiredFunc: PropTypes.func.isRequired,

  // 任意类型的数据
  requiredAny: PropTypes.any.isRequired,

  // 你可以指定一个自定义验证器。它在验证失败时应返回一个 Error 对象。
  // 请不要使用 `console.warn` 或抛出异常，因为这在 `onOfType` 中不会起作用。
  customProp: function(props, propName, componentName) {
    if (!/matchme/.test(props[propName])) {
      return new Error(
        'Invalid prop `' + propName + '` supplied to' +
        ' `' + componentName + '`. Validation failed.'
      );
    }
  },

  // 你也可以提供一个自定义的 `arrayOf` 或 `objectOf` 验证器。
  // 它应该在验证失败时返回一个 Error 对象。
  // 验证器将验证数组或对象中的每个值。验证器的前两个参数
  // 第一个是数组或对象本身
  // 第二个是他们当前的键。
  customArrayProp: PropTypes.arrayOf(function(propValue, key, componentName, location, propFullName) {
    if (!/matchme/.test(propValue[key])) {
      return new Error(
        'Invalid prop `' + propFullName + '` supplied to' +
        ' `' + componentName + '`. Validation failed.'
      );
    }
  })
};

````

这其中有一部分比较实用的需要注意：
* PropTypes.bool PropTypes.func两个使用了缩写
* PropTypes.node、PropTypes.element、PropTypes.elementType可以使dom元素
* PropTypes.oneOfType、PropTypes.oneOf 可以使枚举具体的值，也可以约定具体的类型之一
* PropTypes.arrayOf、、PropTypes.objectOf 规定数组元素 对象类型
* PropTypes.shape 置顶属性的格式
* 还可以通过函数来自定义判断方式


你可以通过 PropTypes.element 来确保传递给组件的 children 中只包含一个元素。
````jsx
import PropTypes from 'prop-types';

class MyComponent extends React.Component {
  render() {
    // 这必须只有一个元素，否则控制台会打印警告。
    const children = this.props.children;
    return (
      <div>
        {children}
      </div>
    );
  }
}

MyComponent.propTypes = {
  children: PropTypes.element.isRequired
};
````

## defaultProps 
defaultProps 可以为 Class 组件添加默认 props。这一般用于 props 未赋值，但又不能为 null 的情况。例如：
````jsx
class CustomButton extends React.Component {
  // ...
}

CustomButton.defaultProps = {
  color: 'blue'
};
````
如果未提供 props.color，则默认设置为 'blue'
````jsx
render() {
    return <CustomButton /> ; // props.color 将设置为 'blue'
  }
````
如果 props.color 被设置为 null，则它将保持为 null
````jsx
  render() {
    return <CustomButton color={null} /> ; // props.color 将保持是 null
  }
````

**需要注意的是`defaultProps`最好不要 `PropTypes.any.isRequired`一起使用在同一个属性上,**

## 其他的static属性和特殊属性
static属性：
1. getDerivedStateFromProps 生命周期，返回新的state状态

特殊属性：
1. children，是一个特殊的 prop，通常由 JSX 表达式中的子组件组成，而非组件本身定义。
2. calssName 对于htmlElement而已就是class属性，对于自定义属性就是正常使用
3. style 属性对于htmlElement而已就是style属性，对于自定义属性就是正常使用
4. ref 属性，是用于标记当前元素/组件的实例/节点
5. key 属性，一般而言是列表子项的唯一标识，用提提高diff性能，其他场景正常使用
