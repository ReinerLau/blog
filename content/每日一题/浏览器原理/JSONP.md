---
noteId: 1727841699054
---


# JSONP
---
利用 `script` 标签没有跨域限制的机制, 发送带有 `callback` 参数的 `GET` 请求, `callback` 参数值为当前域下声明的函数名, 该请求加载一个脚本, 该脚本加载后会自动调用 `callback` 参数对应的函数, 并将数据作为函数调用时的参数, 进而能在函数内部拿到不同源的数据

有一定局限性:

- 只能发起 `GET` 请求: `script` 标签不能用于 `POST` 请求
- 有安全隐患: 执行外部脚本容易收到 [[每日一题之浏览器原理#什么是 XSS 攻击| XSS 攻击]]
