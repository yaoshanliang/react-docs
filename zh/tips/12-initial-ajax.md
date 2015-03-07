---
id: initial-ajax
title: 通过 AJAX 加载初始数据
layout: tips
permalink: initial-ajax.html
prev: dom-event-listeners.html
next: false-in-jsx.html
---

Fetch data in `componentDidMount`. When the response arrives, store the data in state, triggering a render to update your UI.

在 `componentDidMount` 时加载数据。当加载成功，将数据存储在 state 中，触发 render 来更新你的 UI。

When processing the response of an asynchronous request, be sure to check that the component is still mounted before updating its state by using `this.isMounted()`.

当执行同步请求的响应时，在更新 state 前， 一定要先通过 `this.isMounted()` 来检测组件的状态是否还是 mounted。

This example fetches the desired Github user's latest gist:

下面这个例子请求了一个 Github 用户最近的 gist:

```js
var UserGist = React.createClass({
  getInitialState: function() {
    return {
      username: '',
      lastGistUrl: ''
    };
  },

  componentDidMount: function() {
    $.get(this.props.source, function(result) {
      var lastGist = result[0];
      if (this.isMounted()) {
        this.setState({
          username: lastGist.owner.login,
          lastGistUrl: lastGist.html_url
        });
      }
    }.bind(this));
  },

  render: function() {
    return (
      <div>
        {this.state.username}'s last gist is
        <a href={this.state.lastGistUrl}>here</a>.
      </div>
    );
  }
});

React.render(
  <UserGist source="https://api.github.com/users/octocat/gists" />,
  mountNode
);
```
