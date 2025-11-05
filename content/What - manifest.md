---
tags:
  - 前端
---
本质: JSON 文件
作用: 提供浏览器安装 PWA 时需要的信息
文件后缀: 可以是 .json (MIME 为 application/json), 也可以是 .webmanifest (MIME 为 application/manifest+json)
引用方式:
```html
<link rel="manifest" href="manifest.json" />
```

- name 和 short_name: 两个至少定义一个, 两个都有则优先使用 name, short_name 主要用于用户设备空间有限时显示
- icons: 安装 PWA 后显示的一组图标, 为什么是一组而不是一个, 因为这一组图标中包含多种尺寸和用途, 浏览器需要根据设备类型自动选择合适的图标, 如果没提供合适尺寸的图标, 浏览器还会自动帮你缩放图标, 建议至少要提供 192*192、512*512 尺寸的图标, 还可以指定 maskable 图标, 尺寸按照 48px 的幅度递增递减
- id: 让浏览器知道这是同一个应用, 避免重复安装
- start_url: 打开应用后看到的第一个页面
- background_color: 启动页的背景色
- display: 显示模式
- display_override: 自定义显示模式的回退优先级
- scope: PWA 的路径范围, 导航时超出范围的路径在浏览器上打开
- theme_color: 顶部工具栏的颜色
- shortcuts: 右键应用显示的快捷方式
- description: 描述应用用途
- screenshots: 安装时显示的截图
