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
自定义 Hook 是一个函数，其名称以 “use” 开头，函数内部可以调用其他的 Hook。

通过自定义 Hook，可以将组件逻辑提取到可重用的函数中。在16.8版本以前，在 React 中有两种流行的方式来共享组件之间的状态逻辑: `render props 和高阶组件`，现在让我们来看看 Hook 是如何在让你不增加组件的情况下解决相同问题的。

````jsx
import React, { useState, useEffect } from 'react';

// 自定义Hook
function useFriendStatus(friendID) {
  const [isOnline, setIsOnline] = useState(null);

  useEffect(() => {
    function handleStatusChange(status) {
      setIsOnline(status.isOnline);
    }

    ChatAPI.subscribeToFriendStatus(friendID, handleStatusChange);
    return () => {
      ChatAPI.unsubscribeFromFriendStatus(friendID, handleStatusChange);
    };
  });

  return isOnline;
}
// 函数组件使用自定义Hook
function FriendStatus(props) {
  const isOnline = useFriendStatus(props.friend.id);

  if (isOnline === null) {
    return 'Loading...';
  }
  return isOnline ? 'Online' : 'Offline';
}

````

>自定义 Hook 不需要具有特殊的标识。我们可以自由的决定它的参数是什么，以及它应该返回什么（如果需要的话）。换句话说，它就像一个正常的函数。但是它的名字应该始终以 use 开头，这样可以一眼看出其符合 Hook 的规则。

- 自定义Hook必须以use开头，当然如果其他的自定义函数名也是以use开头，那么可能会被lint误认是Hook，自定义Hook必须是使用了use开头，同时内部使用了其他Hook，当然还要遵守在函数顶层调用Hook的规范；
- 在两个组件中使用相同的 Hook 不会共享 state，而且各组件的state是相互独立的；
- Hook使得函数组件能够做的更多，那么代码库中函数组件的代码行数可能会剧增，所以开发者最后通过自定义 Hook 寻找可能，以达到简化代码逻辑，解决组件杂乱无章的目的。

