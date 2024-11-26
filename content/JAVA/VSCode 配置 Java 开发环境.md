# 功能

- 调试
- 测试 (插件)
- 代码补全 (插件和 vscode 支持)
- 代码片段
- 代码导航 (插件和 vscode 支持)

# 快速入门

安装 [Extension Pack for Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack) 后, `cmd + shift + p` 输入  `Java: Tips for Beginners`, 提供了有关代码编辑和调试的快速入门指南和提示, 还有常见问题解答

`cmd + shift + p` 输入 `Java: Extensions Guide` 查看不同场景下推荐的插件

# 最基本的插件

- [Language Support for Java™ by Red Hat](https://marketplace.visualstudio.com/items?itemName=redhat.java)
- [Debugger for Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug)

# 安装 JDK

[Extension Pack for Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack) 支持 Java 版本为 1.8 及更高

[安装地址](https://www.oracle.com/java/technologies/downloads/?er=221886#jdk23-mac)

# 创建项目

打开一个文件夹并创建一个 Java 文件后, Java Language Server 会自动开始加载

也可以使用 `Java： Create Java Project` 命令创建项目

# 编写

使用代码片段快速生成类和方法的雏形, 还有各种补全和重构方法

# 调试

在代码数字栏左侧具体某行点击鼠标左键添加断点, 按 `F5` 进入调试模式

# 代码导航

在左侧 outline 视图里, 可以在当前文件中快速跳转到对应位置

在左侧 java project 视图里, 可以看到整个项目的组成

# 搜索符号

`cmd + T` 在当前项目搜索符号并跳转

`cmd + p` 输入 `@` 在当前文件搜索符号并跳转 

# 查看定义

右键符号选择 `Peek -> Peek Definition` 在当前文件查看符号定义

右键符号选择 `Go to Definition` 跳转到符号定义位置

鼠标悬浮到重写的方法上快速跳转到父类方法的定义

# 查看调用层次

右键符号选择 `Show Call Hierarchy` 显示调用层次

# 查看继承关系

右键符号选择 `Show Type Hierarchy` 显示继承关系

