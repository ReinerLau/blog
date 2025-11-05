---
tags:
  - 前端
---
项目需求: 页面上手动选择文件, 导入文件后, 下次进来页面不需再手动选择同一个文件就能获取文件信息

要实现这个需求, 需要将文件信息存储下来, 或是存储到服务器上的数据库, 或是存储到 [[What - IndexedDB]]

存储也要考虑文件大小, 如果文件过大, 硬盘空间容易不足, 转换成 [[Blob URL]] 存储也不行, 刷新或关闭页面都会失效

为了以上问题, 使用 File System API, 目前只有 Chrome 和 Edge 支持这个 API, 该 API 有一个 showOpenFilePicker 方法, 调用该方法可以像 `<input type="file" />` 那样弹窗选择文件, 不同的是选择文件返回的不是 File 对象, 而是 FileSystemFileHandle 对象, 是一个句柄, 指向文件系统的某个文件, 可以通过句柄访问文件, 可以将该句柄存储起来

假设已经句柄已经存储到 IndexedDB, 下次进入页面从中取出句柄, 虽然仍能通过句柄访问文件, 但没有权限, 需要调用 requestPermission 方法重新请求权限, 最开始调用 showOpenFilePicker 访问到文件的权限是 granted, 表示已授权, 还有 denied 和 prompt 分别表示已拒绝和需要请求权限

查看[示例](https://gist.github.com/e102f9bb68dadb6c4e2dbafe5cfb11a7.git)