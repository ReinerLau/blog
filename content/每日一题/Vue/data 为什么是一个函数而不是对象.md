---
noteId: 1727839876475
---
## data 为什么是一个函数而不是对象
---
- 复用: vue 组件通常是可以复用的, 每次使用组件都会创建新的组件实例
- 对象: 如果 `data` 是对象, 每个组件实例共享同一个 `data` 对象, 一个组件实例修改 `data` 会影响其他组件实例
- 函数: 如果 `data` 是函数, 每个组件实例返回一个全新的对象, 每个组件实例互不影响

