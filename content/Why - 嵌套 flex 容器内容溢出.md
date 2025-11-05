---
tags:
  - css
---

## 问题描述

下面这段代码中, 按照原来设想应该是 sub-container 铺满 container 的剩余高度, 也就是 100vh, content 也是铺满 sub-container 的剩余高度, 也就是 100vh - 100px, 然后超出的部分通过滚动查看
```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
    <style>
      body {
        margin: 0;
      }
      #container {
        height: 100vh;
        display: flex;
        flex-direction: column;
      }
      #sub-container {
        flex: 1;
        display: flex;
        flex-direction: column;
      }
      #content {
        flex: 1;
        background-color: red;
        overflow: auto;
      }
      #footer {
        height: 100px;
        background-color: green;
      }
    </style>
  </head>
  <body>
    <div id="container">
      <div id="sub-container">
        <div id="content"></div>
        <div id="footer"></div>
      </div>
    </div>
    <script>
      const content = document.getElementById("content");
      for (let i = 0; i < 100; i++) {
        const div = document.createElement("div");
        div.innerHTML = i;
        content.appendChild(div);
      }
    </script>
  </body>
</html>]
```

但实际情况是 `<div id="content"></div>` 中的内容会撑开 `<div id="sub-container">` 导出溢出 `<div id="container">`, overflow: auto 没有生效 

## 原因

其原因是垂直排列的 flex 容器, 其子元素的 flex-shrink 默认为 1, 也就是说子元素在容器剩余空间不足时会自动收缩,  但同时子元素的 min-height 为 auto, 也就是说只能收缩到子元素的最小高度, 即子元素内容本身的高度 

代入到上面示例, 虽然 sub-container 的 flex 为 1, 但最终高度还是由最小高度决定, sub-container 的 min-height 为 auto, 计算顺序是 sub-container 先看 content 本身的高度, 假设 content 最终高度为 1000px, 那么 sub-container 的最小高度就是 content 的高度加上 footer 的高度, 即 1100px, 虽然 content 有 overflow: auto, 但在 content 看来, 其本身的高度并没有超出 sub-container 的 1100px, 所以不会出现滚动, 最终 content 撑开 sub-container, 进而溢出 container 

## 解决方案

### min-height: 0

既然子容器的默认 min-height 为 auto, 那可以强制设置 min-height 为 0, 这样子容器就可以自由收缩不受内容高度限制

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
    <style>
      body {
        margin: 0;
      }
      #container {
        height: 100vh;
        display: flex;
        flex-direction: column;
      }
      #sub-container {
        flex: 1;
        display: flex;
        flex-direction: column;
        min-height: 0;
      }
      #content {
        flex: 1;
        background-color: red;
        overflow: auto;
      }
      #footer {
        height: 100px;
        background-color: green;
      }
    </style>
  </head>
  <body>
    <div id="container">
      <div id="sub-container">
        <div id="content"></div>
        <div id="footer"></div>
      </div>
    </div>
    <script>
      const content = document.getElementById("content");
      for (let i = 0; i < 100; i++) {
        const div = document.createElement("div");
        div.innerHTML = i;
        content.appendChild(div);
      }
    </script>
  </body>
</html>
```

### overflow: hidden

给子容器设置 overflow: hidden 的原理和 min-height: 0 相同, 设置后子容器的 min-height 变为 0

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
    <style>
      body {
        margin: 0;
      }
      #container {
        height: 100vh;
        display: flex;
        flex-direction: column;
      }
      #sub-container {
        flex: 1;
        display: flex;
        flex-direction: column;
        overflow: hidden;
      }
      #content {
        flex: 1;
        background-color: red;
        overflow: auto;
      }
      #footer {
        height: 100px;
        background-color: green;
      }
    </style>
  </head>
  <body>
    <div id="container">
      <div id="sub-container">
        <div id="content"></div>
        <div id="footer"></div>
      </div>
    </div>
    <script>
      const content = document.getElementById("content");
      for (let i = 0; i < 100; i++) {
        const div = document.createElement("div");
        div.innerHTML = i;
        content.appendChild(div);
      }
    </script>
  </body>
</html>
```

### flex-basic: 0

设置内容的基础高度为 0, 这样子容器在计算最小高度时会认为 content 本身高度为 0, 所有子容器也能自由收缩

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
    <style>
      body {
        margin: 0;
      }
      #container {
        height: 100vh;
        display: flex;
        flex-direction: column;
      }
      #sub-container {
        flex: 1;
        display: flex;
        flex-direction: column;
      }
      #content {
        flex: 1;
        background-color: red;
        overflow: auto;
        flex-basis: 0;
      }
      #footer {
        height: 100px;
        background-color: green;
      }
    </style>
  </head>
  <body>
    <div id="container">
      <div id="sub-container">
        <div id="content"></div>
        <div id="footer"></div>
      </div>
    </div>
    <script>
      const content = document.getElementById("content");
      for (let i = 0; i < 100; i++) {
        const div = document.createElement("div");
        div.innerHTML = i;
        content.appendChild(div);
      }
    </script>
  </body>
</html>
```