# 前言

[Vue 官方文档](https://cn.vuejs.org/guide/scaling-up/tooling#browser-devtools)提到过一个提升开发体验的工具叫 [Vue DevTools](https://devtools.vuejs.org/), 这个插件即提供了浏览器拓展的版本, 也提供了 Vite 插件的版本, 如果你是 Vite 项目, [官方](https://devtools.vuejs.org/guide/vite-plugin#vite-plugin)更推荐使用 Vite 插件的版本 (说是提供了更多功能, 不过我没有一一比对)

其中有一个功能非常吸引我, 就是 [inspector](https://devtools.vuejs.org/getting-started/features#inspector-vite-only) 功能, 这个功能只在 Vite 项目上有效,  这个功能和浏览器的 DevTools 类似, 只要点击一个按钮进入检查模式并将鼠标移动到页面上的任意位置就能看到对应 DOM 结构, 点击就能定位到 DOM 结构所在位置, 区别在于 Vue DevTools 是以 Vue 组件为单位进行检查的, 比如我移动鼠标到想要查看的页面元素上, 页面就会显示元素所在 Vue 组件对应的行数和列数, 点击之后还能自动打开 IDE 对应 Vue 组件文件并将光标指向所在行列数, 这个功能大大减少了查找组件的时间, 尤其是在接手别人的代码后, 第一次改动时不太确定文件名和位置的情况下

inspector 功能点击页面元素, 默认配置下是打开 VSCode 的, 当然也支持配置成[其他 IDE](https://devtools.vuejs.org/getting-started/open-in-editor#used-in-devtools-vite-plugin), 可惜的是还没有原生支持 [Cursor](https://www.cursor.com/), Cursor 最近作为 AI 辅助开发工具在程序员圈子里挺火爆, 我也趁热试用了两周, 使用下来和 VSCode 的体验一致, 毕竟就是 Cursor 本身就是基于 VSCode 开发的, 学习成本和迁移成本几乎没有, 配合 AI 智能补全和聊天功能, 体验上甚至已经超越了 VSCode, 当然费用上是偏贵的, 但是对于生产力的提升、时间成本的节省来说, 我认为这钱是值得投资的, 反正我现在是离不开它了, 因此需要想办法解决一下这个问题

# launchEditor

Vue DevTools 通过 `launchEditor` 这个配置切换 IDE, 通过测试发现这个配置要接收的值其实是脚本命令, 安装 IDE 后, 一般会注入一个全局命令到系统中, 方便通过命令行快速打开项目文件, 比如 VSCode 就会自动注入 `code` 命令, 如果没有可以打开 IDE 后键盘输入 `cmd + shift + p` 并搜索 `shell command` 就会看到 `Shell Command: Install 'code' command` 的命令, 安装全局命令后就能命令行任意目录中使用这个命令打开项目文件

知道 inspector 功能是通过命令打开 IDE 之后, 我尝试将配置改成 `cursor`, 因为 Cursor 安装后得到的就是这个全局命令

```ts
import VueDevTools from 'vite-plugin-vue-devtools'
export default defineConfig({
  plugins: [
    VueDevTools({
      launchEditor: 'cursor',
    })
  ],
})
```

然后我尝试运行项目并点击元素打开文件, 然后我发现虽然能打开对应文件, 但是光标没有聚焦到对应行列, 而且还额外打开了两个文件名为数字的未保存的新文件, 我在想这样不行呀, 要是每次打开创建两个新文件, 那 tab 的数量还有每次关掉 IDE 时处理新文件保不保存的弹窗就会很麻烦

通过搜索 [issue](https://github.com/yyx990803/launch-editor/issues?q=is:issue+is:open+cursor) 找到了解决方案, 新建脚本 `cursor-launcher.sh`

```sh
#!/bin/bash

# Gets called with 3 args: filename, line, column
filename=$1
line=$2
column=$3

# Call Cursor with the expected arguments
cursor -g "${filename}:${line}:${column}"
```

`-g` 这个参数沿用了 VSCode 的参数, 作用是打开指定文件的同时跳转到指定行和列

又因为 inspector 功能支持[自定义 IDE](https://github.com/yyx990803/launch-editor?tab=readme-ov-file#custom-editor-support), 其实是指定要接收文件名和行列信息的命令或脚本, 这里指定成 `cursor-launcher.sh` 脚本存放的路径

```ts
import VueDevTools from 'vite-plugin-vue-devtools'
export default defineConfig({
  plugins: [
    VueDevTools({
      launchEditor: '/Users/reinerlau/Code/cursor-launcher.sh',
    })
  ],
})
```

当点击元素打开文件时, 相当于执行了 `cursor-launcher.sh <filename> <line> <column>` 命令, 比如我要打开 `src/packages/workflow/views/agent/layout.vue` 文件的第 4 行, 第 7 列, 就会执行 `cursor-launcher.sh <filename> <line> <column>`, 相当于执行 `cursor -g "src/packages/workflow/views/agent/layout.vue:4:7"`

如果没有这个脚本相当于没有 `-g` 参数, 会变成执行 `cursor src/packages/workflow/views/agent/layout.vue 4 7`, 会同时打开 3 个文件, 而且不会自己定位到对应行列

# 总结

希望官方尽快跟随时代步伐, 快点兼容 Cursor 吧