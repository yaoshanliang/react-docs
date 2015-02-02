---
id: addons
title: 插件
permalink: addons.html
prev: tooling-integration.html
next: animation.html
---

`React.addons` is where we park some useful utilities for building React apps. **These should be considered experimental** but will eventually be rolled into core or a blessed utilities library:

- [`TransitionGroup` and `CSSTransitionGroup`](animation.html), for dealing with animations and transitions that are usually not simple to implement, such as before a component's removal.
- [`LinkedStateMixin`](two-way-binding-helpers.html), to simplify the coordination between user's form input data and the component's state.
- [`classSet`](class-name-manipulation.html), for manipulating the DOM `class` string a bit more cleanly.
- [`cloneWithProps`](clone-with-props.html), to make shallow copies of React components and change their props.
- [`update`](update.html), a helper function that makes dealing with immutable data in JavaScript easier.
- [`PureRenderMixin`](pure-render-mixin.html), a performance booster under certain situations.

以下插件只存在于 React 开发版（未压缩）：

- [`测试工具集`](test-utils.html), 帮助你编写测试用例（仅存在于未压缩版）.
- [`性能分析工具`](perf.html), 用于性能测评，并帮助你检查出可以优化的点。

To get the add-ons, use `react-with-addons.js` (and its minified counterpart) rather than the common `react.js`.

When using the react package from npm, just simply `require('react/addons')` instead of `require('react')` to get React with all of the addons.
