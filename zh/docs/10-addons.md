---
id: addons
title: 插件
permalink: addons.html
prev: tooling-integration.html
next: animation.html
---

`React.addons`是我们为了构建React应用而放置的一些有用工具的地方。**这应当被视为实验性的**，但是最终将会成为核心的或者有用的工具库：

- [`TransitionGroup`和`CSSTransitionGroup`](animation.html)，为了处理动画和过渡，这些通常实现起来都不简单，比如说在一个组件移除之前。
- [`LinkedStateMixin`](two-way-binding-helpers.html)，用于使协调用户的表单数据输入和组件的state变得简化。
- [`classSet`](class-name-manipulation.html)，为了更加干净简洁地操作DOM`类`字符串。
- [`cloneWithProps`](clone-with-props.html)，用于实现React组件浅复制，同时改变它们的props。
- [`update`](update.html)，一个辅助方法，使在JavaScript中处理不可变数据更加容易。
- [`PureRednerMixin`](pure-render-mixin.html)，在某些情景下的性能检测器。

以下插件只存在于 React 开发版（未压缩）：

- [`测试工具集`](test-utils.html), 帮助你编写测试用例（仅存在于未压缩版）.
- [`性能分析工具`](perf.html), 用于性能测评，并帮助你检查出可以优化的点。

要下载这些插件，使用`react-with-addons.js`（和它的最小化副本）替换通常的`React.js`。

当通过npm使用react包的时候，只要简单地`require('react/addons')`替换`require('react')`来获取带有所有插件的React。
