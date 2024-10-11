---
noteId: 1727840007779
---
# 路由中 hash 和 history 模式的区别
---
- URL
	- hash: 带 `#` 号, `#` 后面的部分不会发送服务器
	- history: 不带 `#` 号
- 原理
	- hash: 监听 `onhashchange` 事件进行导航, 不会刷新页面, 也不会向服务器发起请求
	- history: 利用 HTML5 的 history API (即 `pushState` 和 `replaceState`) 改变历史记录和 URL, 不会刷新页面