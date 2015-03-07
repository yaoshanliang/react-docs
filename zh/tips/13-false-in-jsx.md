---
id: false-in-jsx
title: JSX 的 false 处理
layout: tips
permalink: false-in-jsx.html
prev: initial-ajax.html
next: communicate-between-components.html
---

Here's how `false` renders in different contexts:

Renders as `id="false"`:

这篇文章展示了在不同上下文中 `false` 的渲染:

被渲染成 `id="false"`:

```js
React.render(<div id={false} />, mountNode);
```

String `"false"` as input value:

input value 的值将会是 `"false"` 字符串

```js
React.render(<input value={false} />, mountNode);
```

No child:

没有子节点

```js
React.render(<div>{false}</div>, mountNode);
```

The reason why this one doesn't render as the string `"false"` as a `div` child is to allow the more common use-case: `<div>{x > 1 && 'You have more than one item'}</div>`.

上面这个没有被渲染成 `"false"`  字符串是应为要考虑到这种常见的情况：`<div>{x > 1 && 'You have more than one item'}</div>`.
