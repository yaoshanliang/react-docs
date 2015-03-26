---
id: thinking-in-react
title: 深入理解React
prev: tutorial.html
next: videos.html
---

这是一篇源自[官方博客](/react/blog) 的[文章](/react/blog/2013/11/05/thinking-in-react.html)。

在我看来， React 是较早使用 JavaScript 构建大型、快速的 Web 应用程序的技术方案。它已经被我们广泛应用于 Facebook 和 Instagram 。

React 众多优秀特征中的其中一部分就是，教会你去重新思考如何构建应用程序。

本文中，我将跟你一起使用 React 构建一个具备搜索功能的产品列表。

> **注意：**
>
> 如果你无法看到本页内嵌的代码片段，请确认你不是用 `https` 协议加载本页的。

## 从原型（ mock ）开始

假设我们已经拥有了一个 JSON API 和设计师设计的原型。我们的设计师显然不够好，因为原型看起来如下：

![Mockup](/react/img/blog/thinking-in-react-mock.png)

JSON接口返回数据如下：

```
[
  {category: "Sporting Goods", price: "$49.99", stocked: true, name: "Football"},
  {category: "Sporting Goods", price: "$9.99", stocked: true, name: "Baseball"},
  {category: "Sporting Goods", price: "$29.99", stocked: false, name: "Basketball"},
  {category: "Electronics", price: "$99.99", stocked: true, name: "iPod Touch"},
  {category: "Electronics", price: "$399.99", stocked: false, name: "iPhone 5"},
  {category: "Electronics", price: "$199.99", stocked: true, name: "Nexus 7"}
];
```

## 第一步：拆分用户界面为一个组件树

你要做的第一件事是，为所有组件（及子组件）命名并画上线框图。假如你和设计师一起工作，也许他们已经完成了这项工作，所以赶紧去跟他们沟通！他们的 Photoshop 图层名也许最终可以直接用于你的 React 组件名。

然而你如何知道哪些才能成为组件？想象一下，当你创建一些函数或对象时，用到一些类似的技术。其中一项技术就是[单一功能原则](http://en.wikipedia.org/wiki/Single_responsibility_principle)，指的是，理想状态下一个组件应该只做一件事，假如它功能逐渐变大就需要被拆分成更小的子组件。

由于你经常需要将一个JSON数据模型展示给用户，因此你需要检查这个模型结构是否正确以便你的 UI （在这里指组件结构）是否能够正确的映射到这个模型上。这是因为用户界面和数据模型在 *信息构造* 方面都要一直，这意味着将你可以省下很多将 UI 分割成组件的麻烦事。你需要做的仅仅只是将数据模型分隔成一小块一小块的组件，以便它们都能够表示成组件。

![Component diagram](/react/img/blog/thinking-in-react-components.png)

由此可见，我们的 app 中包含五个组件。下面我已经用斜体标示出每个组件对应的数据。

  1. **`FilterableProductTable` （橘色）：** 包含整个例子的容器
  2. **`SearchBar` （蓝色）：** 接受所有 *用户输入（ user input ）*
  3. **`ProductTable` （绿色）：** 根据 *用户输入（ user input ）* 过滤和展示 *数据集合（ data collection ）*
  4. **`ProductCategoryRow` （青色）：** 为每个 *分类（ category ）* 展示一列表头
  5. **`ProductRow` （红色）：** 为每个 *产品（ product ）* 展示一列

如果你仔细观察 `ProductTable` ，你会发现表头（包含“ Name ”和“ Price ”标签）并不是单独的组件。这只是一种个人偏好，也有一定的争论。在这个例子当中，我把表头当做 `ProductTable` 的一部分，因为它是渲染“数据集合”的一份子，这也是 `ProductTable` 的职责。但是，当这个表头变得复杂起来的时候（例如，添加排序功能），就应该单独地写一个 `ProductTableHeader` 组件。

既然我们在原型当中定义了这个组件，让我们把这些元素组成一棵树形结构。这很简单。被包含在其它组件中的组件在属性机构中应该是子级：

  * `FilterableProductTable`
    * `SearchBar`
    * `ProductTable`
      * `ProductCategoryRow`
      * `ProductRow`

## 第二步： 利用 React ，创建应用的一个静态版本

<iframe width="100%" height="300" src="http://jsfiddle.net/reactjs/yun1vgqb/embedded/" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

既然已经拥有了组件树，是时候开始实现应用了。最简单的方式就是创建一个应用，这个应用将数据模型渲染到 UI 上，但是没有交互功能。拆分这两个过程是最简单的，因为构建一个静态的版本仅需要大量的输入，而不需要思考；但是添加交互功能却需要大量的思考和少量的输入。我们将会知道这是为什么。

为了创建一个渲染数据模型的应用的静态版本，你将会构造一些组件，这些组件重用其它组件，并且通过 *props* 传递数据。 *props* 是一种从父级向子级传递数据的方式。如果你对 *state* 概念熟悉，那么**不要使用 state **来构建这个静态版本。 state 仅用于实现交互功能，也就是说，数据随着时间变化。因为这是一个静态的应用版本，所以你并不需要 state 。

你可以从上至下或者从下至上来构建应用。也就是说，你可以从属性结构的顶部开始构建这些组件（例如，从 `FilterableProductTable` 开始），或者从底部开始（ `ProductRow` ）。在简单的应用中，通常情况下从上至下的方式更加简单；在大型的项目中，从下至上的方式更加简单，这样也可以在构建的同时写测试代码。

在这步结束的时候，将会有一个可重用的组件库来渲染数据模型。这些组件将会仅有 `render()` 方法，因为这是应用的一个静态版本。位于树形结构顶部的组件（ `FilterableProductTable` ）将会使用数据模型作为 prop 。如果你改变底层数据模型，然后再次调用 `React.render()` ， UI 将会更新。查看 UI 如何被更新和什么地方改变都是很容易的，因为 React 的**单向数据流**（也被称作“单向绑定”）保持了一切东西模块化，很容易查错，并且速度很快，没有什么复杂的。

如果你在这步中需要帮助，请查看[ React 文档](http://facebook.github.io/react/docs/)。

### 穿插一小段内容： props 与 state 比较

在 React 中有两种类型的数据“模型”： props 和 state 。理解两者的区别是很重要的；如果你不太确定两者有什么区别，请大致浏览一下[官方的 React 文档](http://facebook.github.io/react/docs/interactivity-and-dynamic-uis.html)。

##  第三步：识别出最小的（但是完整的）代表 UI 的 state

为了使 UI 可交互，需要能够触发底层数据模型的变化。 React 通过 **state** 使这变得简单。

为了正确构建应用，首先需要考虑应用需要的最小的可变 state 数据模型集合。此处关键点在于精简：*不要存储重复的数据*。
To build your app correctly you first need to think of the minimal set of mutable state that your app needs. The key here is DRY: *Don't Repeat Yourself*. Figure out what the absolute minimal representation of the state of your application needs to be and compute everything else you need on-demand. For example, if you're building a TODO list, just keep an array of the TODO items around; don't keep a separate state variable for the count. Instead, when you want to render the TODO count simply take the length of the TODO items array.

Think of all of the pieces of data in our example application. We have:

  * The original list of products
  * The search text the user has entered
  * The value of the checkbox
  * The filtered list of products

Let's go through each one and figure out which one is state. Simply ask three questions about each piece of data:

  1. Is it passed in from a parent via props? If so, it probably isn't state.
  2. Does it change over time? If not, it probably isn't state.
  3. Can you compute it based on any other state or props in your component? If so, it's not state.

The original list of products is passed in as props, so that's not state. The search text and the checkbox seem to be state since they change over time and can't be computed from anything. And finally, the filtered list of products isn't state because it can be computed by combining the original list of products with the search text and value of the checkbox.

So finally, our state is:

  * The search text the user has entered
  * The value of the checkbox

## Step 4: Identify where your state should live

<iframe width="100%" height="300" src="http://jsfiddle.net/reactjs/zafjbw1e/embedded/" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

OK, so we've identified what the minimal set of app state is. Next we need to identify which component mutates, or *owns*, this state.

Remember: React is all about one-way data flow down the component hierarchy. It may not be immediately clear which component should own what state. **This is often the most challenging part for newcomers to understand,** so follow these steps to figure it out:

For each piece of state in your application:

  * Identify every component that renders something based on that state.
  * Find a common owner component (a single component above all the components that need the state in the hierarchy).
  * Either the common owner or another component higher up in the hierarchy should own the state.
  * If you can't find a component where it makes sense to own the state, create a new component simply for holding the state and add it somewhere in the hierarchy above the common owner component.

Let's run through this strategy for our application:

  * `ProductTable` needs to filter the product list based on state and `SearchBar` needs to display the search text and checked state.
  * The common owner component is `FilterableProductTable`.
  * It conceptually makes sense for the filter text and checked value to live in `FilterableProductTable`

Cool, so we've decided that our state lives in `FilterableProductTable`. First, add a `getInitialState()` method to `FilterableProductTable` that returns `{filterText: '', inStockOnly: false}` to reflect the initial state of your application. Then pass `filterText` and `inStockOnly` to `ProductTable` and `SearchBar` as a prop. Finally, use these props to filter the rows in `ProductTable` and set the values of the form fields in `SearchBar`.

You can start seeing how your application will behave: set `filterText` to `"ball"` and refresh your app. You'll see the data table is updated correctly.

## Step 5: Add inverse data flow

<iframe width="100%" height="300" src="http://jsfiddle.net/reactjs/n47gckhr/embedded/" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

So far we've built an app that renders correctly as a function of props and state flowing down the hierarchy. Now it's time to support data flowing the other way: the form components deep in the hierarchy need to update the state in `FilterableProductTable`.

React makes this data flow explicit to make it easy to understand how your program works, but it does require a little more typing than traditional two-way data binding. React provides an add-on called `ReactLink` to make this pattern as convenient as two-way binding, but for the purpose of this post we'll keep everything explicit.

If you try to type or check the box in the current version of the example you'll see that React ignores your input. This is intentional, as we've set the `value` prop of the `input` to always be equal to the `state` passed in from `FilterableProductTable`.

Let's think about what we want to happen. We want to make sure that whenever the user changes the form we update the state to reflect the user input. Since components should only update their own state, `FilterableProductTable` will pass a callback to `SearchBar` that will fire whenever the state should be updated. We can use the `onChange` event on the inputs to be notified of it. And the callback passed by `FilterableProductTable` will call `setState()` and the app will be updated.

Though this sounds like a lot it's really just a few lines of code. And it's really explicit how your data is flowing throughout the app.

## And that's it

Hopefully this gives you an idea of how to think about building components and applications with React. While it may be a little more typing than you're used to, remember that code is read far more than it's written, and it's extremely easy to read this modular, explicit code. As you start to build large libraries of components you'll appreciate this explicitness and modularity, and with code reuse your lines of code will start to shrink :)
