---
tags:
  - 前端
---
在各种设备上, PWA 应用的图标会被截取成合适的形状

为了图标被截取后保留核心的部分而不影响视觉效果, 图标外围需要至少预留 10% 的空间用于截取, 进入 Devtools 的 Application 面板查看 Manifest 可以看到图标被截取剩下的最小区域, 看看截取后图标是否合适

 [https://maskable.app/](https://maskable.app/)  可以测试图标是否合适或者编辑生成图标