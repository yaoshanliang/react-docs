---
id: props-in-getInitialState-as-anti-pattern
title: getInitialState 里的 Props 是一个反模式
layout: tips
permalink: props-in-getInitialState-as-anti-pattern.html
prev: componentWillReceiveProps-not-triggered-after-mounting.html
next: dom-event-listeners.html
---

> Note:
>
> This isn't really a React-specific tip, as such anti-patterns often occur in code in general; in this case, React simply points them out more clearly.

> 注意：
> 
> 这实际上不是一篇单独的 React 提示，因为类似的反模式设计也经常会在平时的编码中出现；这里，React 只是简单清晰地指出来这个问题

Using props, passed down from parent, to generate state in `getInitialState` often leads to duplication of "source of truth", i.e. where the real data is. Whenever possible, compute values on-the-fly to ensure that they don't get out of sync later on and cause maintenance trouble.

使用 props, 自父级向下级传递，在使用 `getInitialState` 生成 state 的时候，经常会导致重复的"来源信任"，i.e. 如果有可能，请尽量在使用的时候计算值，以此来确保不会出现同步延迟的问题和状态保持的问题。

**糟糕的例子**

**Bad example:**

```js
var MessageBox = React.createClass({
  getInitialState: function() {
    return {nameWithQualifier: 'Mr. ' + this.props.name};
  },

  render: function() {
    return <div>{this.state.nameWithQualifier}</div>;
  }
});

React.render(<MessageBox name="Rogers"/>, mountNode);
```

Better:

更好的写法:

```js
var MessageBox = React.createClass({
  render: function() {
    return <div>{'Mr. ' + this.props.name}</div>;
  }
});

React.render(<MessageBox name="Rogers"/>, mountNode);
```

(For more complex logic, simply isolate the computation in a method.)

(对于更复杂的逻辑，最好通过方法将数据处理分离开来)

However, it's **not** an anti-pattern if you make it clear that synchronization's not the goal here:

然而，如果你理清了这些，那么它也就 **不是** 反模式了。两者兼得不是我们的目标：

```js
var Counter = React.createClass({
  getInitialState: function() {
    // naming it initialX clearly indicates that the only purpose
    // of the passed down prop is to initialize something internally
    return {count: this.props.initialCount};
  },

  handleClick: function() {
    this.setState({count: this.state.count + 1});
  },

  render: function() {
    return <div onClick={this.handleClick}>{this.state.count}</div>;
  }
});

React.render(<Counter initialCount={7}/>, mountNode);
```
