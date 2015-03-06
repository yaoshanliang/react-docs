---
id: maximum-number-of-jsx-root-nodes
title: JSX 根节点的最大数量
layout: tips
permalink: maximum-number-of-jsx-root-nodes.html
prev: self-closing-tag.html
next: style-props-value-px.html
---

Currently, in a component's `render`, you can only return one node; if you have, say, a list of `div`s to return, you must wrap your components within a `div`, `span` or any other component.

Don't forget that JSX compiles into regular JS; returning two functions doesn't really make syntactic sense. Likewise, don't put more than one child in a ternary.

目前， 一个 component 的 `render`，只能返回一个节点。如果你需要返回一堆 `div` ， 那你必须将你的组件用 一个`div` 或 `span` 或任何其他的组件包裹。

切记，JSX 会被编译成常规的 JS； 因此返回两个函数也就没什么意义了，同样地，千万不要在三元操作符中放入超过一个子节点。
