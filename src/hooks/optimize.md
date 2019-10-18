## Hook的正确使用和注意事项

参考[Hook的正确使用](https://zhuanlan.zhihu.com/p/85969406)

### useState的使用建议

多个还是单个：相关联/更新时依赖上一次的值的情况使用单个Object或者配合useReducer；不相关的分开到不同的useState

**注意：state中保留的最好是会影响渲染的数据，如果只是想要一个类似实例数据持久化，那么请使用useRef**

