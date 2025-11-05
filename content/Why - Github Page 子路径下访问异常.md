---
tags:
  - 工程化
  - 前端
---
项目打包后得到  dist  目录, 目录下有一个  index.html  文件, 我想将该目录托管到 github page 下访问

在仓库的  .github/workflow  目录下生成  static.yml   文件, 配置中默认托管项目根目录, 但我需要托管  dist  目录, 于是修改该配置文件, 改为托管  dist  目录, 保存推送后会自动执行 github action 流程

```yml
# Simple workflow for deploying static content to GitHub Pages
name: Deploy static content to Pages


on:
  # Runs on pushes targeting the default branch
  push:
    branches: ["main"]


  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:


# Sets permissions of the GITHUB_TOKEN to allow deployment to GitHub Pages
permissions:
  contents: read
  pages: write
  id-token: write


# Allow only one concurrent deployment, skipping runs queued between the run in-progress and latest queued.
# However, do NOT cancel in-progress runs as we want to allow these production deployments to complete.
concurrency:
  group: "pages"
  cancel-in-progress: false


jobs:
  # Single deploy job since we're just deploying
  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4
      - name: Setup Pages
        uses: actions/configure-pages@v5
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          # Upload entire repository
          path: "./dist"
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

因为项目仓库不是  用户名.github.io  , 所以 dist 目录下的资源会托管到  https://<用户名>.g  ithub.io/<仓库名>/  下, 第一次访问出现白屏, 打开 Devtools 查看 network 发现 index.html 引用的资源报 404, 我查看发现这些资源的请求路径是 https://<用户名>.github.io/<文件名>, 但资源都托管在 github page 根域名的子路径下, 所以才 404, 查看 index.html 中引用 JS 资源的路径是绝对路径, 所以才直接请求到根域名下

```html
<script type="module" crossorigin src="/assets/index-HHTfNE0t.js"></script>
<link rel="stylesheet" crossorigin href="/assets/index-BunLEPEv.css">
```

而 dist 目录的结构是这样的

```
.
└── dist/
    ├── assets/
    │   ├── index-HHTfNE0t.js
    │   └── index-BunLEPEv.css
    └── index.html
```

所以我需要将绝对路径改成相对路径, 因为我是 vite 构建的项目, 我查看 vite.config.ts 配置文件并没有发现相关配置, 说明有默认配置, 需要查看官方文档是否存在相关配置

```ts
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react-swc";
import UnoCSS from "unocss/vite";


// https://vite.dev/config/
export default defineConfig({
  plugins: [react(), UnoCSS()]
});
```

通过查看官方文档我发现  [https://vite.dev/guide/build.html#public-base-path](https://vite.dev/guide/build.html#public-base-path)  中说到资源基础路径的配置,  [https://vite.dev/config/shared-options.html#base](https://vite.dev/config/shared-options.html#base)  也提到默认资源基础路径是 / , 只要将 base 设置成 ./ 或空字符串就可以改为相对路径

```ts
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react-swc";
import UnoCSS from "unocss/vite";


// https://vite.dev/config/
export default defineConfig({
  plugins: [react(), UnoCSS()],
  base: "./",
});
```

配置完成, 重新打包并推送, github action 自动识别到分支出现更新开始部署流程, 部署完成后再次访问 https://<用户名>.github.io/<仓库名> 发现还是白屏, 但资源加载正常, Console 面板出现了无法匹配路由的黄色警告, 也就是说, 当前项目的路由配置无法兼容子路径的访问形式, 路由配置如下:

```ts
import { StrictMode } from "react";
import { createRoot } from "react-dom/client";
import "./index.css";
import "virtual:uno.css";
import App from "./App.tsx";
import { BrowserRouter } from "react-router";
import { ConfigProvider } from "antd";
import zhCN from "antd/locale/zh_CN";


createRoot(document.getElementById("root")!).render(
  <StrictMode>
    <BrowserRouter>
      <ConfigProvider locale={zhCN}>
        <App />
      </ConfigProvider>
    </BrowserRouter>
  </StrictMode>
);
```

```ts
import { Routes, Route } from "react-router";
import HomePage from "./pages/HomePage";
import GamePage from "./pages/GamePage";


/**
 * 主应用组件
 * 管理应用的路由结构
 */
function App() {
  return (
    <Routes>
      {/* 首页路由 */}
      <Route path="/" element={<HomePage />} />


      {/* 对局页面路由 - 编辑模式 */}
      <Route path="/edit-game/:gameId" element={<GamePage />} />


      {/* 对局页面路由 - 正常模式和新增模式 */}
      <Route path="/game/:gameId" element={<GamePage />} />
    </Routes>
  );
}


export default App;
```

对这个路由配置来说, 根域名才能匹配首页路由, 通过子路径访问的方式不存在于当前路由配置中, 其中一种解决方案就是修改      basename  , 将路由的根路径改成和 github page 子路径一样

```ts
import { StrictMode } from "react";
import { createRoot } from "react-dom/client";
import "./index.css";
import "virtual:uno.css";
import App from "./App.tsx";
import { ConfigProvider } from "antd";
import zhCN from "antd/locale/zh_CN";
import { BrowserRouter } from "react-router";


createRoot(document.getElementById("root")!).render(
  <StrictMode>
    <BrowserRouter basename="doudizhu-simulator">
      <ConfigProvider locale={zhCN}>
        <App />
      </ConfigProvider>
    </BrowserRouter>
  </StrictMode>
);
```

这样配置的话, 在 github page 下能正常访问了, 但不足的是, 我在本地开发环境下访问首页也必须带上这个根路径, 试过一些重定向方案都不太好使, 最后只好转成 hash 模式, 丑是丑点, 但不用考虑子路径和重定向的问题还是挺方便的

```ts
import { StrictMode } from "react";
import { createRoot } from "react-dom/client";
import "./index.css";
import "virtual:uno.css";
import App from "./App.tsx";
import { HashRouter } from "react-router";
import { ConfigProvider } from "antd";
import zhCN from "antd/locale/zh_CN";


createRoot(document.getElementById("root")!).render(
  <StrictMode>
    <HashRouter>
      <ConfigProvider locale={zhCN}>
        <App />
      </ConfigProvider>
    </HashRouter>
  </StrictMode>
);
```