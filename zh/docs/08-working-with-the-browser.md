---
id: working-with-the-browser
title: 浏览器中的工作原理
permalink: working-with-the-browser.html
prev: forms.html
next: more-about-refs.html
---

React提供了强大的抽象，让你在大多数应用场景中不再直接操作DOM，但是有时你需要简单地调用底层的API，或者借助于第三方库或已有的代码。


## 虚拟DOM

React是很快的，因为它从不直接操作DOM。React在内存中维护一个快速响应的DOM描述。`render()`方法返回一个DOM的*描述*，React能够利用内存中的描述来快速地计算出差异，然后更新浏览器中的DOM。

另外，React实现了一个完备的虚拟事件系统，尽管各个浏览器都有自己的怪异行为，React确保所有事件对象都符合W3C规范，并且持续冒泡，用一种高性能的方式跨浏览器（and everything bubbles consistently and in a performant way cross-browser）。你甚至可以在IE8中使用一些HTML5的事件！

大多数时候你应该呆在React的“虚拟浏览器”世界里面，因为它性能更加好，并且容易思考。但是，有时你简单地需要调用底层的API，或许借助于第三方的类似于jQuery插件这种库。React为你提供了直接使用底层DOM API的途径。


## Refs和findDOMNode()

为了和浏览器交互，你将需要对DOM节点的引用。React提供了`React.findDOMNode(component)`函数，你可以调用这个函数来获取该组件的DOM结点。

> 注意：
>
> `findDOMNode()`仅在挂载的组件上有效（也就是说，组件已经被放进了DOM中）。如果你尝试在一个未被挂载的组件上调用这个函数（例如在创建组件的`render()`函数中调用`findDOMNode()`），将会抛出异常。

为了获取一个到React组件的引用，你可以使用`this`来得到当前的React组件，或者你可以使用refs来指向一个你拥有的组件。它们像这样工作：

```javascript
var MyComponent = React.createClass({
  handleClick: function() {
    // Explicitly focus the text input using the raw DOM API.
    React.findDOMNode(this.refs.myTextInput).focus();
  },
  render: function() {
    // The ref attribute adds a reference to the component to
    // this.refs when the component is mounted.
    return (
      <div>
        <input type="text" ref="myTextInput" />
        <input
          type="button"
          value="Focus the text input"
          onClick={this.handleClick}
        />
      </div>
    );
  }
});

React.render(
  <MyComponent />,
  document.getElementById('example')
);
```


## 更多关于Refs

为了学习更多有关Refs的内容，包括如何有效地使用它们，参考我们的[更多关于Refs](/react/docs/more-about-refs.html)文档。


## 组件生命周期

组件的生命周期包含三个主要部分：

* **挂载：** 组件被插入到DOM中。
* **更新：** 组件被重新渲染，查明DOM是否应该刷新。
* **移除：** 组件从DOM中移除。

React提供生命周期方法，你可以在这些方法中放入自己的代码。我们提供**will**方法，会在某些行为发生之前调用，和**did**方法，会在某些行为发生之后调用。


### 挂载

* `getInitialState(): object`在组件被挂载之前调用。状态化的组件应该实现这个方法，返回初始的state数据。
* `componentWillMount()`在挂载发生之前立即被调用。
* `componentDidMount()`在挂载结束之后马上被调用。需要DOM节点的初始化操作应该放在这里。


### 更新

* `componentWillReceiveProps(object nextProps)`当一个挂载的组件接收到新的props的时候被调用。该方法应该用于比较`this.props`和`nextProps`，然后使用`this.setState()`来改变state。
* `shouldComponentUpdate(object nextProps, object nextState): boolean`当组件做出是否要更新DOM的决定的时候被调用。实现该函数，优化`this.props`和`nextProps`，以及`this.state`和`nextState`的比较，如果不需要React更新DOM，则返回false。
* `componentWillUpdate(object nextProps, object nextState)`在更新发生之前被调用。你可以在这里调用`this.setState()`。
* `componentDidUpdate(object prevProps, object prevState)`在更新发生之后调用。


### 移除

* `componentWillUnmount()`在组件移除和销毁之前被调用。清理工作应该放在这里。


### 挂载的方法（Mounted Methods）

_挂载的_复合组件也支持如下方法：

* `findDOMNode(): DOMElement`可以在任何挂载的组件上面调用，用于获取一个指向它的渲染DOM节点的引用。
* `forceUpdate()`当你知道一些很深的组件state已经改变了的时候，可以在该组件上面调用，而不是使用`this.setState()`。


## 跨浏览器支持和兼容代码（Browser Support and Polyfills）

在Facebook里，我们支持低版本的浏览器，包括IE8。我们提前很早就写好polyfills（填充工具，这里语意应为兼容工具或代码），这让我们可以去写些前瞻性思维的JS。这意味我们的代码库里没有一堆乱七八糟的hacks技巧，但我们的代码仍可按预期地正常工作。例如，我们可以直接写 `Date.now()`，而不用写`+new Date()`。从React的开源版本跟我们（Facebook）内部版本相同的时候开始，我们就已经延续了这一理念——运用前瞻性思维的JS 。

除了这一理念之外，站在一个JS库作者的立场，我们也不应该让polyfills成为我们代码库里的一部分。如果每个代码库都包含polyfills，那你项目中可能会多次包含相同的polyfills，这会产生一大堆无效代码。如果你项目中需要支持老版本的浏览器，你可以试试（或许你已经在用了）类似[es5-shim](https://github.com/kriskowal/es5-shim)这样的东西。

### 支持低版本浏览器的兼容代码

[kriskowal的es5-shim](https://github.com/kriskowal/es5-shim) `es5-shim.js` 提供了以下react需要的api：

* `Array.isArray`
* `Array.prototype.every`
* `Array.prototype.forEach`
* `Array.prototype.indexOf`
* `Array.prototype.map`
* `Date.now`
* `Function.prototype.bind`
* `Object.keys`
* `String.prototype.split`
* `String.prototype.trim`

[kriskowal的es5-shim](https://github.com/kriskowal/es5-shim) `es5-sham.js` 同样提供了以下react需要的api：

* `Object.create`
* `Object.freeze`

未压缩版本的React需要[paulmillr写的console-polyfill](https://github.com/paulmillr/console-polyfill)来提供以下api。

* `console.*`

当我们在IE8里面使用HTML5的标签 `<section>`、`<article>`、`<nav>`、`<header>`和`<footer>`时，项目中需要包含[html5shiv](https://github.com/aFarkas/html5shiv) 或类似的脚本。


### 跨浏览器问题（Cross-browser Issues）

尽管React在抽象浏览器行为差异方面做得不错，但仍有一些浏览器有限制，或存在一些我们也找不到解决办法的怪异行为。

#### IE8上的onScroll事件

在IE8的`onscroll`事件没有冒泡，IE8也没有API定义如何处理事件捕获，这意味着React没办法监听这些事件。目前，IE8上这一事件的处理程序是没有。

On IE8 the onScroll event doesn't bubble and IE8 doesn't have an API to define handlers to the capturing phase of an event, meaning there is no way for React to listen to these events. Currently a handler to this event is ignored on IE8.

阅读[onScroll doesn't work in IE8](https://github.com/facebook/react/issues/631) GitHub issue 了解更多信息。
