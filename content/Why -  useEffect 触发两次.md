---
tags:
  - React
---
开发环境下使用了严格模式的现象

故意对组件执行挂载-卸载-挂载的流程

测试清理函数在组件多次挂载/卸载时仍能正常工作

生产环境不会有这个现象

https://react.dev/reference/react/useEffect#my-effect-runs-twice-when-the-component-mounts