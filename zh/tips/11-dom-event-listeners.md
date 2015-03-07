---
id: dom-event-listeners
title: 组件的 DOM 事件监听
layout: tips
permalink: dom-event-listeners.html
prev: props-in-getInitialState-as-anti-pattern.html
next: initial-ajax.html
---

> Note:
>
> This entry shows how to attach DOM events not provided by React ([check here for more info](/react/docs/events.html)). This is good for integrations with other libraries such as jQuery.

> 注意：
>
> 这篇文章是讲如何给 DOM 元素绑定 React 没有提供的事件 ([check here for more info](/react/docs/events.html))。 当你想和其他类库比如 jQuery 一起使用的时候，需要知道这些。

Try to resize the window:

```js
var Box = React.createClass({
  getInitialState: function() {
    return {windowWidth: window.innerWidth};
  },

  handleResize: function(e) {
    this.setState({windowWidth: window.innerWidth});
  },

  componentDidMount: function() {
    window.addEventListener('resize', this.handleResize);
  },

  componentWillUnmount: function() {
    window.removeEventListener('resize', this.handleResize);
  },

  render: function() {
    return <div>Current window width: {this.state.windowWidth}</div>;
  }
});

React.render(<Box />, mountNode);
```

`componentDidMount` is called after the component is mounted and has a DOM representation. This is often a place where you would attach generic DOM events.

`componentDidMount` 会在 component 渲染完成且已经有了 DOM 结构的时候被调用。通常情况下，你可以在这绑定普通的 DOM 事件。

Notice that the event callback is bound to the react component and not the original element. React automatically binds methods to the current component instance for you through a process of [autobinding](/react/docs/interactivity-and-dynamic-uis.html#under-the-hood-autobinding-and-event-delegation).

注意，事件的回调被绑定在了 react 组件上，而不是原始的元素上。React 通过一个 [autobinding](/react/docs/interactivity-and-dynamic-uis.html#under-the-hood-autobinding-and-event-delegation) 过程自动将方法绑定到当前的组件实例上。
