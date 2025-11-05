---
tags:
  - css
---
下面这段代码在 PC 端下不会出现滚动条, 但在移动端下会出现滚动条

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>playground</title>
    <style>
      body {
        margin: 0;
      }
      .container {
        height: 100vh;
        background-color: red;
      }
    </style>
  </head>
  <body>
    <div class="container"></div>
  </body>
</html>
```

按理说 vh 表示[[视口]]高度的百分比, 但视口又分为小视口和大视口

某些浏览器会在用户滚动页面时动态隐藏和显示浏览器 UI 以便展示更多页面区域

小视口是指浏览器 UI 显示时剩下的区域

大视口是指浏览器 UI 隐藏时剩下的区域

小视口高度和大视口高度的百分比可以分别用 svh 和 lvh 表示

vh 等同于 lvh 

实测, 在本人的移动端上, 浏览器的 UI 不会出现动态隐藏和显示的情况, 也就是说页面的可见区域是小视口, 设置成 100vh, 相当于页面铺满包括浏览器 UI 的高度, 所以被浏览器 UI 遮挡, 需要向下滚动显示完整内容

如果设置成 svh, 在一些浏览器上, 隐藏 UI 后可能会出现空白区域

svh 和 lvh 都有利弊, 所以还有一种单位 dvh, 可以跟随浏览器 UI 动态调整视口高度, 但也有缺点, 可能会导致用户滚动页面过程不断[[重排]]

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>playground</title>
    <style>
      body {
        margin: 0;
      }
      .container {
        height: 100dvh;
        background-color: red;
      }
    </style>
  </head>
  <body>
    <div class="container"></div>
  </body>
</html>
```

