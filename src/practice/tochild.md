# 父子组件的通信问题
在react的开发过程中，引起组件进行更新的就是props/state的变更，而react的设计模式是自上而下的，所以在自上而下的过程中，上层组件和下层组件的通信(父组件与后代组件通信)的方式就要求开发者能够熟练掌握了。

目前常见的包括以下：
1. 基础props传递
2. render props复用方式
3. context上下文传递
4. ref获取后代组件实例或者DOM节点
5. findDOMNode获取后代组件DOM节点

## props方法
props是react中一个很重要的概念，目前主要的用于是将组件的属性传入，

当然有一些含有特殊意义的props我们应该注意：
- key 用于标记list的唯一
- ref 用于记录对应组件的实例或者DOM节点
- onClick等事件属性 作用于html元素的事件监听（是react自身的合成事件的封装实现）
- className 就是对于html元素而言的class属性
- style/表单type/等原生html元素属性 对应html元素的同名属性
- children 默认属性 组件包含的所有子组件

当我们使用自定义的react组件(组件名首字母要求大写)时，我们需要定义些属性，组件通过props获取(函数组件通过第一个参数 props获取，class组件通过this.props获取)，这样子组件就可以获取到父组件传入的属性/方法，来完成通信，当然我们还可以通过子组件传递给更深层级的后代组件，这样后代组件就可以获取到最上层的组件传递的属性/方法。

关于跨层级的传递props的不便，可以尝试：
- 通过context的方式支持跨层级传递；
- 通过提升后代节点的层级避免多层传递；
- slot方式，react的props可以传递组件，类似其他库的slot形式，通过形如`<FatherComponent leftSlot={<LeftComponent value="test" className="m-test" clickHander={() => {console.log('click')}} >} />`这种方式来把依赖组件提升，交给子组件处理展示，示例中FatherComponent自定义组件需要将props中的LeftComponent在render中进行处理展示。

## render props复用



## context深层级传递



## ref获取后代组件实例


## findDOMNode获取后代组件

