---
id: getting-started
title: 入门教程
next: tutorial.html
redirect_from: "docs/index.html"
---

## JSFiddle

开始学习 React 最简单的方式是使用如下JSFiddle的 Hello World例子：

 * **[React JSFiddle](http://jsfiddle.net/reactjs/69z2wepo/)**
 * [React JSFiddle without JSX](http://jsfiddle.net/reactjs/5vjqabv3/)

## 初学者教程包 (Starter Kit)

开始先下载初学者教程包。

<div class="buttons-unit downloads">
  <a href="/react/downloads/react-{{site.react_version}}.zip" class="button">
    下载初学者教程包 {{site.react_version}}
  </a>
</div>

In the root directory of the starter kit, create a `helloworld.html` with the following contents.

```html
<!DOCTYPE html>
<html>
  <head>
    <script src="build/react.js"></script>
    <script src="build/JSXTransformer.js"></script>
  </head>
  <body>
    <div id="example"></div>
    <script type="text/jsx">
      React.render(
        <h1>Hello, world!</h1>,
        document.getElementById('example')
      );
    </script>
  </body>
</html>
```

在 JavaScript 代码里写着 XML 格式的代码称为 JSX；可以去 [JSX 语法](/react/docs/jsx-in-depth.html) 里学习更多 JSX 相关的知识。为了把 JSX 转成标准的 JavaScript，我们用 `<script type="text/jsx">` 标签包裹着含有 JSX 的代码，然后引入 `JSXTransformer.js` 库来实现在浏览器里的代码转换。

### 分离文件

你的 React JSX 代码文件可以写在单独的文件里。创建 `src/helloworld.js` 文件，内容如下： 

```javascript
React.render(
  <h1>Hello, world!</h1>,
  document.getElementById('example')
);
```

然后在 `helloworld.html` 引用它：

```html{10}
<script type="text/jsx" src="src/helloworld.js"></script>
```

### 离线转换

先安装命令行工具（依赖 [npm](http://npmjs.org/)）：

```
npm install -g react-tools
```

然后将你的 `src/helloworld.js` 文件转成标准的 JavaScript:

```
jsx --watch src/ build/

```

一旦你修改了， `build/helloworld.js` 文件会自动生成。

```javascript{2}
React.render(
  React.createElement('h1', null, 'Hello, world!'),
  document.getElementById('example')
);
```


对照以下内容更新你的 HTML 代码

```html{6,10}
<!DOCTYPE html>
<html>
  <head>
    <title>Hello React!</title>
    <script src="build/react.js"></script>
    <!-- No need for JSXTransformer! -->
  </head>
  <body>
    <div id="example"></div>
    <script src="build/helloworld.js"></script>
  </body>
</html>
```

## 想要遵循 CommonJS?

If you want to use React with [browserify](http://browserify.org/), [webpack](http://webpack.github.io/), or another CommonJS-compatible module system, just use the [`react` npm package](https://www.npmjs.org/package/react). In addition, the `jsx` build tool can be integrated into most packaging systems (not just CommonJS) quite easily.

## Next Steps

Check out [the tutorial](/react/docs/tutorial.html) and the other examples in the starter kit's `examples` directory to learn more. 

We also have a wiki where the community contributes with [workflows, UI-components, routing, data management etc.](https://github.com/facebook/react/wiki/Complementary-Tools)

Good luck, and welcome!
