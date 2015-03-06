---
id: self-closing-tag
title: 自闭合标签
layout: tips
permalink: self-closing-tag.html
prev: if-else-in-JSX.html
next: maximum-number-of-jsx-root-nodes.html
---

In JSX, `<MyComponent />` alone is valid while `<MyComponent>` isn't. All tags must be closed, either with the self-closing format or with a corresponding closing tag (`</MyComponent>`).

在 JSX 中， `<MyComponent />` 是合法的，而 `<MyComponent>` 就不合法。 所有的标签都必须闭合，可以是自闭和的形式，也可以是常规的闭合。

> Note:
>
> Every React component can be self-closing: `<div />`. `<div></div>` is also an equivalent.

> 注意：
>
> 所有 React component 都可以采用自闭和的形式：`<div />`. `<div></div>` 它们是等价的。
