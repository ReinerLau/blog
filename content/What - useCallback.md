---
tags:
  - React
---
### 📝 代码示例：性能优化

假设我们有一个父组件 `ParentComponent` 和一个展示计数的子组件 `ButtonComponent`。

#### 1. 定义子组件（使用 `React.memo` 优化）

这个子组件只关心它接收到的 `handleClick` 函数。我们用 `React.memo` 包裹它，告诉 React 只有当 `props` 改变时才重新渲染。

```ts
import React from 'react';

// 使用 React.memo 优化子组件
const ButtonComponent = React.memo(({ handleClick }) => {
  // 仅在 props 真正变化时，这段代码才会运行
  console.log('ButtonComponent 渲染了'); 

  return (
    <button onClick={handleClick}>
      点击我执行父组件的函数
    </button>
  );
});

export default ButtonComponent;
```

#### 2. 定义父组件（使用 `useCallback`）

父组件有一个自己的 `state` (`count`)。每次 `count` 变化时，`ParentComponent` 都会重新渲染。

**✅ 使用 `useCallback` 优化：**

```ts
import React, { useState, useCallback } from 'react';
import ButtonComponent from './ButtonComponent'; // 引入优化的子组件

function ParentComponent() {
  const [count, setCount] = useState(0);
  const [otherState, setOtherState] = useState(0); // 用于触发 ParentComponent 重新渲染

  // 1. 定义并记忆化一个处理函数
  // 依赖项: [count]
  const incrementCount = useCallback(() => {
    // 这个函数使用了 count 状态
    setCount(c => c + 1);
  }, [count]); // 👈 只有 count 变化时，才会返回新的函数实例

  // 2. 一个不使用 useCallback 的函数 (每次渲染都会重建)
  const changeOtherState = () => {
    setOtherState(o => o + 1);
  };

  return (
    <div>
      <h3>Count: {count}</h3>
      <h3>Other State (会触发父组件重新渲染): {otherState}</h3>

      {/* 传递记忆化的函数 */}
      <ButtonComponent handleClick={incrementCount} /> 

      <button onClick={changeOtherState}>
        改变 Other State
      </button>
    </div>
  );
}
// export default ParentComponent;
```

### 🧠 效果演示

| **操作**                  | **ParentComponent 重新渲染？**        | **ButtonComponent 重新渲染？**                                         | **原因**                                                                                                                                  |
| ----------------------- | -------------------------------- | ----------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------- |
| 点击 **"点击我执行父组件的函数"**    | **是** (因为 `setCount` 改变了状态)      | **是** (因为 `count` 变了，`useCallback` 返回了**新的** `incrementCount` 函数) |                                                                                                                                         |
| 点击 **"改变 Other State"** | **是** (因为 `setOtherState` 改变了状态) | **否**                                                             | 父组件重新渲染了，但 `incrementCount` 函数依赖的 `count` **没有变**，`useCallback` **返回了缓存的函数**。`React.memo` 比较发现 `handleClick` prop **没有变**，所以阻止了子组件重新渲染。 |

**总结：** `useCallback` 成功地在 `ParentComponent` 仅因为 `otherState` 变化而重新渲染时，**保护了** `ButtonComponent` 不被无意义地重新渲染，从而实现了性能优化