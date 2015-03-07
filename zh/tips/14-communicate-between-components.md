---
id: communicate-between-components
title: 组件间的通信
layout: tips
permalink: communicate-between-components.html
prev: false-in-jsx.html
next: expose-component-functions.html
---

For parent-child communication, simply [pass props](/react/docs/multiple-components.html).

对于 父-子 通信，直接 [pass props](/react/docs/multiple-components.html).

For child-parent communication:
Say your `GroceryList` component has a list of items generated through an array. When a list item is clicked, you want to display its name:

对于 子-父 通信：
例如： `GroceryList` 组件有一些通过数组生成的子节点。当这些节点被点击的时候，你想要展示这个节点的名字：

```js
var GroceryList = React.createClass({
  handleClick: function(i) {
    console.log('You clicked: ' + this.props.items[i]);
  },

  render: function() {
    return (
      <div>
        {this.props.items.map(function(item, i) {
          return (
            <div onClick={this.handleClick.bind(this, i)} key={i}>{item}</div>
          );
        }, this)}
      </div>
    );
  }
});

React.render(
  <GroceryList items={['Apple', 'Banana', 'Cranberry']} />, mountNode
);
```

Notice the use of `bind(this, arg1, arg2, ...)`: we're simply passing more arguments to `handleClick`. This is not a new React concept; it's just JavaScript.

注意 `bind(this, arg1, arg2, ...)` 的使用： 我们通过它向 `handleClick` 传递参数。 这不是 React 的新概念，而是 JavaScript 的。

For communication between two components that don't have a parent-child relationship, you can set up your own global event system. Subscribe to events in `componentDidMount()`, unsubscribe in `componentWillUnmount()`, and call `setState()` when you receive an event.

对于没有 父-子 关系的组件间的通信，你可以设置你自己的全局时间系统。 在 `componentDidMount()` 里订阅事件，在 `componentWillUnmount()` 里退订，然后在事件回调里调用 `setState()`。
