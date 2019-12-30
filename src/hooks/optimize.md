## Hook的正确使用和注意事项

参考[Hook的正确使用](https://zhuanlan.zhihu.com/p/85969406)

### useState的使用建议
在使用 state 之前，我们需要考虑状态拆分的「粒度」问题。如果粒度过细，代码就会变得比较冗余。如果粒度过粗，代码的可复用性就会降低。
- 使用多个 state 变量可以让 state 的粒度更细，更易于逻辑的拆分和组合。
- 使用单个state会使得state的粒度过粗，代码的可复用性就会降低
多个还是单个：相关联/更新时依赖上一次的值的情况使用单个Object或者配合useReducer；不相关的分开到不同的useState

多个state的引起非问题：
- 当存在多个state的时候，在useEffect或者交互中，顺序调用多个setState的时候，多个setState可能会引起多次渲染，而不是合并渲染，需要注意是否存在依赖关系，特别是数据data和状态status被分离时需要注意；
- 需要结合场景来具体分析，避免过多的依赖的同时，也要避免过度的rerender；

建议：
1. 将完全不相关的 state 拆分为多组 state。比如 size 和 position。
2. 如果某些 state 是相互关联的，或者需要一起发生改变，就可以把它们合并为一组 state。比如

**注意：state中保留的最好是会影响渲染的数据，如果只是想要一个类似实例数据持久化，那么请使用useRef**

### deps过多难以维护【useEffect、useCall、useMemo】
使用 部分 hook 时【useEffect、useCall、useMemo】，为了避免每次 render 都去执行它的 callback，我们通常会传入第二个参数「dependency array」（下面统称为依赖数组）。这样，只有当依赖数组发生变化时，才会执行 useEffect 的回调函数。

1. 是否需要所有deps：对于那些使用不会变化的dep可以移除，比如用空数组[]作为deps的useCallback函数只会在第一次挂载时创建，然后不再变化，可以考虑移除这样的deps，但是hook-lint依然会提示你不要这么做。
2. 对于依赖过多的Hook可以考虑拆分为多个更加细粒度的Hook；
3. 通过 setState 回调函数来设置那些依赖上一次渲染时state值得处理；setState(preState => preState+1);
4. 还可以通过 ref 来保存可变变量，这些变量不需要添加到依赖中，因为他们的变化不会通知到开发者，但是每次访问都会是最新的值。


### 其他建议
1. 若 Hook 类型相同，且依赖数组一致时，应该合并成一个 Hook。否则会产生更多开销。
2. 参考原生 Hooks 的设计，自定义 Hooks 的返回值可以使用 数组 类型，更易于在外部重命名。但如果返回值的数量超过三个，还是建议返回一个对象。
3. ref 不要直接暴露给外部使用，而是提供一个修改值的方法。



