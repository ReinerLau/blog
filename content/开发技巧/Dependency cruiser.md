---
tags:
  - 开发技巧
---
# 作用

- 校验依赖关系
- 生成依赖图

# 安装

```sh
npm install --save-dev dependency-cruiser
# 或者使用 yarn
yarn add -D dependency-cruiser
# 或者使用 pnpm
pnpm add -D dependency-cruiser
```

# 生成配置文件

```sh
npx depcruise --init
```

一定要, 不然无法执行命令

# 校验依赖关系

```sh
npx depcruise src
```

配置参考[教程](https://github.com/sverweij/dependency-cruiser/blob/main/doc/rules-tutorial.md)和[文档](https://github.com/sverweij/dependency-cruiser/blob/main/doc/rules-reference.md)

# 生成依赖图

```sh
npx dependency-cruiser --output-type mermaid src --output-to dependency-graph.mmd
```

- npx: 在 `node_modules` 中查找安装的包并执行
- dependency-cruiser: 要执行的包名称
- --output-type mermaid: 制定格式为 `mermaid` , [参考](https://github.com/sverweij/dependency-cruiser/blob/main/doc/cli.md#--output-type-specify-the-output-format)
- src: 对 `src` 文件夹中的文件进行依赖关系分析
- --output-to dependency-graph.mmd: 指定输出文件的名称为 `dependency-graph.mmd`

生成 svg:

```sh
brew install graphviz
```

```sh
npx depcruise src --output-type dot | dot -T svg > dependency-graph.svg
```