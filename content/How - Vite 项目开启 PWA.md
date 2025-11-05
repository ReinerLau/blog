---
tags:
  - 工程化
---
当项目需要在无网络的情况下也能正常访问并使用, 这时候转换成 PWA 应用安装到本地最适合不过, 恰巧有个项目部署在 github page 上, 恰巧 PWA 要求 https, 于是来实践一下, 首先浏览器判断网页是否为 PWA, 即是否支持安装到本地是需要满足一定条件的, 具体参考  [https://vite-pwa-org.netlify.app/guide/pwa-minimal-requirements.html](https://vite-pwa-org.netlify.app/guide/pwa-minimal-requirements.html)  , 大体要做三部分工作:

- manifest: JSON 文件, 里面包含浏览器如何识别网页的必要信息, 具体参考  [https://web.dev/learn/pwa/web-app-manifest](https://web.dev/learn/pwa/web-app-manifest) 

- icons: 作为应用安装到本地后显示的图标, 而且要求多种格式大小, 以便兼容不同的设备

- Service Worker: 相当于应用和云端服务器之间的代理服务器, 可以拦截资源的请求, 对资源进行缓存

即使知道这三点, 真实际手动配置起来还挺繁琐的, 恰巧我的项目是 vite 项目, 有 PWA 相关的成熟插件, 配置成 PWA 方便很多, 先安装插件

安装 vite-plugin-pwa 插件

```sh
pnpm add -D vite-plugin-pwa
```

配置插件

```ts
import { VitePWA } from 'vite-plugin-pwa'

export default defineConfig({
  plugins: [
    VitePWA({
      registerType: "autoUpdate",
      manifest: {
        id: "",
        name: "",
        short_name: "",
        theme_color: "#ffffff",
        icons: [],
      },
    })
  ]
})
```

现在打包后, index.html 就会自动引入一个 manifest 文件, 里面包含了一些最简单的配置, 按理说, 浏览器若识别网页是 PWA 会在地址栏弹出一个安装按钮, 但现在没有, 因为还没有配置 icons, 因为 icon 需要多种格式大小, 手动处理起来不方便, 可以使用 @vite-pwa/assets-generator 命令行工具快速生成需要的多种大小图标

https://vite-pwa-org.netlify.app/assets-generator/#example-using-minimal-preset
https://maskable.app/editor

```sh
pnpm add -D @vite-pwa/assets-generator
```

```sh
npx pwa-assets-generator --preset minimal-2023 public/logo.svg
```

执行后会看到 public 目录下多处个 png 文件, 在命令行还能看到以下输出:

```
<link rel="icon" href="/favicon.ico" sizes="48x48">
<link rel="icon" href="/vite.svg" sizes="any" type="image/svg+xml">
<link rel="apple-touch-icon" href="/apple-touch-icon-180x180.png">
```

```
{
  "icons": [
    {
      "src": "pwa-64x64.png",
      "sizes": "64x64",
      "type": "image/png"
    },
    {
      "src": "pwa-192x192.png",
      "sizes": "192x192",
      "type": "image/png"
    },
    {
      "src": "pwa-512x512.png",
      "sizes": "512x512",
      "type": "image/png"
    },
    {
      "src": "maskable-icon-512x512.png",
      "sizes": "512x512",
      "type": "image/png",
      "purpose": "maskable"
    }
  ]
}
```

前一个输出复制到 index.html 的 head 中, 后一个输出复制 vite 配置中, 这样重新打包后, 完整的 PWA 应用就完成了

```ts
import { VitePWA } from 'vite-plugin-pwa'

export default defineConfig({
  plugins: [
    VitePWA({
      registerType: "autoUpdate",
      manifest: {
        id: "",
        name: "",
        short_name: "",
        theme_color: "#ffffff",
        icons: [
          {
            src: "pwa-64x64.png",
            sizes: "64x64",
            type: "image/png",
          },
          {
            src: "pwa-192x192.png",
            sizes: "192x192",
            type: "image/png",
          },
          {
            src: "pwa-512x512.png",
            sizes: "512x512",
            type: "image/png",
            purpose: "any",
          },
          {
            src: "maskable-icon-512x512.png",
            sizes: "512x512",
            type: "image/png",
            purpose: "maskable",
          },
        ],
      },
    })
  ]
})
```

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <link rel="icon" href="/favicon.ico" sizes="48x48" />
    <link rel="icon" href="/logo.svg" sizes="any" type="image/svg+xml" />
    <link rel="apple-touch-icon" href="/apple-touch-icon-180x180.png" />
    <title>demo</title>
  </head>
  <body>
    <div id="root"></div>
    <script type="module" src="/src/main.tsx"></script>
  </body>
</html>
```

生成启动页

https://vite-pwa-org.netlify.app/assets-generator/cli.html#ios-ipad-splash-screens

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <link rel="icon" href="/favicon.ico" sizes="48x48" />
    <link rel="icon" href="/logo.svg" sizes="any" type="image/svg+xml" />
    <link rel="apple-touch-icon" href="/apple-touch-icon-180x180.png" />
    <meta name="apple-mobile-web-app-capable" content="yes" />
    <link
      rel="apple-touch-startup-image"
      media="(device-width: 375px) and (device-height: 812px) and (-webkit-device-pixel-ratio: 3) and (orientation: portrait)"
      href="/apple-splash-portrait-1125x2436.png"
    />
    <link
      rel="apple-touch-startup-image"
      media="(device-width: 375px) and (device-height: 812px) and (-webkit-device-pixel-ratio: 3) and (orientation: landscape)"
      href="/apple-splash-landscape-2436x1125.png"
    />
    <title>demo</title>
  </head>
  <body>
    <div id="root"></div>
    <script type="module" src="/src/main.tsx"></script>
  </body>
</html>
```

https://progressier.com/pwa-icons-and-ios-splash-screen-generator
https://developer.apple.com/design/human-interface-guidelines/layout
