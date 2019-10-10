## Hooks 规则
>Hook 是 React 16.8 的新增特性。它可以让你在不编写 class 的情况下使用 state 以及其他的 React 特性。

Hook 本质就是 JavaScript 函数，但是在使用它时需要遵循两条规则:
1. 只在最顶层使用 Hook
    不要在循环，条件或嵌套函数中调用 Hook， 确保总是在你的 React 函数的最顶层调用他们。遵守这条规则，你就能确保 Hook 在每一次渲染中都按照同样的顺序被调用。这让 React 能够在多次的 useState 和 useEffect 调用之间保持 hook 状态的正确。因为React 靠的是 Hook 调用的顺序来管理Hook的状态的，如果在条件语句中就存在顺序出现不一致的渲染情况，这会导致Hook状态出现混乱。
2. 只在 React 函数中调用 Hook
    不要在普通的 JavaScript 函数中调用 Hook。可以在以下环境使用Hook：
    - react函数组件顶层中
    - 自定义Hook中可以调用其他Hook

## 自定义Hook
>Hook 是 React 16.8 的新增特性。它可以让你在不编写 class 的情况下使用 state 以及其他的 React 特性。

通过自定义 Hook，可以将组件逻辑提取到可重用的函数中。


