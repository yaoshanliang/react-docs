---
id: tutorial
title: 教程
prev: getting-started.html
next: thinking-in-react.html
---
我们将构建一个简单却真实的评论框，你可以将它放入你的博客，类似于 disqus 、 livefyre 、 facebook 中实时评论框，包含一些基础功能。

我们将提供以下功能：

* 展示所有评论的界面
* 发布评论的表单
* 后端 API 接口地址，该接口需要自己实现

同时也包含一些简洁的特性：

* **发布评论的体验优化：** 评论在保存到服务器之前就展现在评论列表，因此感觉起来很快。
* **实时更新：** 其他用户的评论将会实时展示在评论框中。
* **Markdown 格式：** 用户可以使用 Markdown 语法来编辑评论文本。

### 想要跳过后续所有的内容，只看源代码？

[所有代码都在 GitHub 。](https://github.com/reactjs/react-tutorial)

### <a class="anchor" name="running-a-server"></a>运行一个服务器

为了开始本教程，我们将需要一个运行的服务器，提供一个 API 接口，仅用于获取和保存评论数据。为了尽量简便，我们已经准备好了几种脚本语言开发的简单服务器程序，完成了我们需要的功能。你可以[查看源码](https://github.com/reactjs/react-tutorial/)或者[下载 zip 文件](https://github.com/reactjs/react-tutorial/archive/master.zip)，里面包含了开始学习教程所需的所有东西。

为了简单，服务器将会使用一个 `JSON` 文件作为数据库。不能在生产环境中采用这种做法，但是这样做确实简化了模拟后端 API 的工作。一旦启动了这个服务器，它就能提供我们需要的 API 接口，同时也能生成并发送我们需要的页面。

### 开始学习

本教程会尽量简单。上述讨论提及的页面是一个 HTML 文件，它包含在服务器源码包中，我们先看看这个文件。使用你最喜欢的编辑器打开 `public/index.html` ，然后会看见这些内容（可能会有一些小的差异，后面我们会添加一个额外的 `<script>` 标签）：

```html
<!-- index.html -->
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />
    <title>React Tutorial</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/react/0.14.0/react.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/react/0.14.0/react-dom.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/babel-core/5.8.23/browser.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jquery/2.1.1/jquery.min.js"></script>
  </head>
  <body>
    <div id="content"></div>
    <script type="text/babel" src="scripts/example.js"></script>
    <script type="text/babel">
      // To get started with this tutorial running your own code, simply remove
      // the script tag loading scripts/example.js and start writing code here.
    </script>
  </body>
</html>
```

在本教程的剩下部分，我们会在这个 script 标签中编写我们的 JavaScript 代码。我们没有任何高端的页面自动加载刷新工具，所以在修改了代码之后，需要手动刷新浏览器来查看变化。下面，启动服务器，在浏览器中打开 `http://localhost:3000` 。在没对源码进行修改的前提下，第一次打开这个 URL 会看到我们计划构建的应用的一个最终样子。当你准备好开始往下学习的时候，只需要删掉上述代码中的 `<script>` 标签就可以了。

> 注意：
>
> 因为我们想简化 ajax 请求代码，所以在这里引入 jQuery，但是 React 并不依赖于 jQuery 。

### 你的第一个组件

React 中全是模块化、可组装的组件。对于我们的评论框例子，我们将有如下的组件结构：

```
- CommentBox
  - CommentList
    - Comment
  - CommentForm
```

让我们构造 `CommentBox` 组件，它只是一个简单的 `<div>` ：

```javascript
// tutorial1.js
var CommentBox = React.createClass({
  render: function() {
    return (
      <div className="commentBox">
        Hello, world! I am a CommentBox.
      </div>
    );
  }
});
ReactDOM.render(
  <CommentBox />,
  document.getElementById('content')
);
```

注意，原生 HTML 元素名以小写字母开头，而自定义的 React 类名以大写字母开头。

#### JSX语法

首先你注意到 JavaScript 代码中 XML 式的语法语句。我们有一个简单的预编译器，用于将这种语法糖转换成纯 JavaScript 代码：

```javascript
// tutorial1-raw.js
var CommentBox = React.createClass({displayName: 'CommentBox',
  render: function() {
    return (
      React.createElement('div', {className: "commentBox"},
        "Hello, world! I am a CommentBox."
      )
    );
  }
});
ReactDOM.render(
  React.createElement(CommentBox, null),
  document.getElementById('content')
);
```

JSX 语法是可选的，但是我们发现 JSX 语句比纯 JavaScript 用起来更容易。更多内容请阅读[《 JSX 语法介绍》](/react/docs/jsx-in-depth.html)。

#### 发生了什么

我们通过 JavaScript 对象传递一些方法到 `React.createClass()` 来创建一个新的 React 组件。其中最重要的方法是 `render` ，该方法返回一棵 React 组件树，这棵树最终将会渲染成 HTML 。

这里的 `<div>` 标签不是真正的 DOM 节点；他们是 React `div` 组件的实例。你可以认为这些标签就是一些标记或者数据， React 知道如何处理它们。React 是**安全的**。我们不生成 HTML 字符串，因此默认阻止了 XSS 攻击。

没必要返回基本的 HTML 结构，可以返回一个你（或者其他人）创建的组件树。而这就是让 React 变得**组件化** 的特性：一个前端可维护性的关键原则。

`ReactDOM.render()` 实例化根组件，启动框架，把标记注入到第二个参数指定的原生的 DOM 元素中。

`ReactDOM` 模块提供了一些 DOM 相关的方法，而 `React` 模块包含了 React 团队分享的不同平台上的核心工具（例如， [React Native](http://facebook.github.io/react-native/) ）。

## 制作组件

让我们为 `CommentList` 和 `CommentForm` 搭建骨架，它们也是由一些简单的 `<div>` 组成。将这两个组件的代码添加到你的源码文件中，保留已有的 `CommentBox` 声明和 `ReactDOM.render` 调用：

```javascript
// tutorial2.js
var CommentList = React.createClass({
  render: function() {
    return (
      <div className="commentList">
        Hello, world! I am a CommentList.
      </div>
    );
  }
});

var CommentForm = React.createClass({
  render: function() {
    return (
      <div className="commentForm">
        Hello, world! I am a CommentForm.
      </div>
    );
  }
});
```

接下来，更新 `CommentBox` 组件代码，使用新创建的组件：

```javascript{6-8}
// tutorial3.js
var CommentBox = React.createClass({
  render: function() {
    return (
      <div className="commentBox">
        <h1>Comments</h1>
        <CommentList />
        <CommentForm />
      </div>
    );
  }
});
```

注意我们是如何整合 HTML 标签和我们所创建的组件的。 HTML 组件就是普通的 React 组件，就和你定义的组件一样，只不过有一处不同。 JSX 编译器会自动重写 HTML 标签为 `React.createElement(tagName)` 表达式，其它什么都不做。这是为了避免全局命名空间污染。

### 使用属性（ props ）

让我们创建 `Comment` 组件，该组件依赖于从父级传入的数据。从父组件传入的数据会做为子组件的 `属性（ property ）` ，这些 `属性（ properties ）` 可以通过 `this.props` 访问到。使用属性（ props ），我们就可以读到从 `CommentList` 传到 `Comment` 的数据，然后渲染一些标记：

```javascript{6-7}
// tutorial4.js
var Comment = React.createClass({
  render: function() {
    return (
      <div className="comment">
        <h2 className="commentAuthor">
          {this.props.author}
        </h2>
        {this.props.children}
      </div>
    );
  }
});
```

在 JSX 中，通过使用大括号包住一个 JavaScript 表达式（例如作为属性或者儿子节点），你可以在树结构中生成文本或者 React 组件。我们通过 `this.props` 来访问传入组件的数据，键名就是对应的命名属性，也可以通过 `this.props.children` 访问组件内嵌的任何元素。

### 组件属性

现在我们定义了 `Comment` 组件，我们想传递给它作者名字和评论文本，以便于我们能够对每一个独立的评论重用相同的代码。首先让我们添加一些评论到 CommentList ：

```js
// tutorial5.js
var CommentList = React.createClass({
  render: function() {
    return (
      <div className="commentList">
        <Comment author="Pete Hunt">This is one comment</Comment>
        <Comment author="Jordan Walke">This is *another* comment</Comment>
      </div>
    );
  }
});
```

请注意，我们从父 CommentList 组件传递给子 Comment 组件一些数据。例如，我们传递了 *Pete Hunt* （通过属性）和 *This is one comment* （通过类似于 XML 的子节点）给第一个 Comment 组件。正如前面说的那样， `Comment` 组件通过 `this.props.author` 和 `this.props.children` 来访问这些“属性”。

### 添加 Markdown 语法格式的评论

Markdown 是一种简单的格式化内联文本的方式。例如，用星号包裹文本将会使其强调突出。

首先，引入第三方的 **marked** 库到你的应用。这是一个将 Markdown 文本转换成原生 HTML 的 JavaScript 库。在头部加一个 script 标签（我们已经在 React 操练场上包含了这个标签）：

```html{9}
<!-- index.html -->
<head>
  <meta charset="utf-8" />
  <title>React Tutorial</title>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/react/0.14.0/react.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/react/0.14.0/react-dom.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/babel-core/5.8.23/browser.min.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/jquery/2.1.1/jquery.min.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/marked/0.3.2/marked.min.js"></script>
</head>
```

接下来，让我们使用 Markdown 格式的评论过文本，然后转换输出：

```javascript
// tutorial6.js
var Comment = React.createClass({
  render: function() {
    return (
      <div className="comment">
        <h2 className="commentAuthor">
          {this.props.author}
        </h2>
        {marked(this.props.children.toString())}
      </div>
    );
  }
});
```

这里我们唯一需要做的就是调用 marked 库。我们需要把 `this.props.children` 从 React 的包裹文本转换成 marked 能处理的原始的字符串，所以我们显示地调用了 `toString()` 。

但是这里有一个问题！我们渲染的评论内容在浏览器里面看起来像这样：“`<p>`This is `<em>`another`</em>` comment`</p>`”。我们希望这些标签能够真正地渲染成 HTML 。

那是 React 在保护你免受 [XSS 攻击](https://en.wikipedia.org/wiki/Cross-site_scripting)。这里有一种方法解决这个问题，但是框架会警告你别使用这种方法：

```javascript{3-6,14}
// tutorial7.js
var Comment = React.createClass({
  rawMarkup: function() {
    var rawMarkup = marked(this.props.children.toString(), {sanitize: true});
    return { __html: rawMarkup };
  },

  render: function() {
    return (
      <div className="comment">
        <h2 className="commentAuthor">
          {this.props.author}
        </h2>
        <span dangerouslySetInnerHTML={this.rawMarkup()} />
      </div>
    );
  }
});
```

这是一个特殊的 API，故意让插入原始的 HTML 变得困难，但是对于 marked ，我们将利用这个后门。

**记住：** 使用这个功能，你的代码就要依赖于 marked 的安全性。在本场景中，我们传入 `sanitize: true` ，告诉 marked 转义掉评论文本中的 HTML 标签而不是直接原封不动地返回这些标签。

### 接入数据模型

到目前为止，我们已经在源代码里面直接插入了评论数据。让我们将这些评论数据抽出来，放在一个 JSON 格式的变量中，然后将这个 JSON 数据渲染到评论列表。到最后，数据将会来自服务器，但是现在，直接写在源代码中：

```javascript
// tutorial8.js
var data = [
  {author: "Pete Hunt", text: "This is one comment"},
  {author: "Jordan Walke", text: "This is *another* comment"}
];
```

我们需要用一种模块化的方式将数据传入到 `CommentList` 。修改 `CommentBox` 和 `React.render()` 方法，通过 props 传递数据到 `CommentList` ：

```javascript{7,15}
// tutorial9.js
var CommentBox = React.createClass({
  render: function() {
    return (
      <div className="commentBox">
        <h1>Comments</h1>
        <CommentList data={this.props.data} />
        <CommentForm />
      </div>
    );
  }
});

ReactDOM.render(
  <CommentBox data={data} />,
  document.getElementById('content')
);
```

现在数据在 `CommentList` 中可用了，让我们动态地渲染评论：

```javascript{4-10,13}
// tutorial10.js
var CommentList = React.createClass({
  render: function() {
    var commentNodes = this.props.data.map(function (comment) {
      return (
        <Comment author={comment.author}>
          {comment.text}
        </Comment>
      );
    });
    return (
      <div className="commentList">
        {commentNodes}
      </div>
    );
  }
});
```

就是这样！

### 从服务器获取数据

让我们用从服务器动态获取的数据替换硬编码的数据。我们会删掉 `data` 属性，使用一个 URL 来获取数据：

```javascript{3}
// tutorial11.js
ReactDOM.render(
  <CommentBox url="/api/comments" />,
  document.getElementById('content')
);
```

这个组件和前面的组件是不一样的，因为它必须重新渲染自己。在服务器请求返回之前，该组件将不会有任何数据，请求返回之后，该组件或许要渲染一些新的评论。

### 响应状态变化（ Reactive state ）

到目前为止，每一个组件都根据自己的 props 渲染了自己一次。 `props` 是不可变的：它们从父组件传递过来，“属于”父组件。为了实现交互，我们给组件引入了可变的 **state** 。`this.state` 是组件私有的，可以通过调用 `this.setState()` 来改变它。当 state 更新之后，组件就会重新渲染自己。

`render()` 方法依赖于 `this.props` 和 `this.state` ，框架会确保渲染出来的 UI 界面总是与输入（ `this.props` 和 `this.state` ）保持一致。

当服务器拿到评论数据的时候，我们将会用已知的评论数据改变评论。让我们给 `CommentBox` 组件添加一个评论数组作为它的 state ：

```javascript{3-5,10}
// tutorial12.js
var CommentBox = React.createClass({
  getInitialState: function() {
    return {data: []};
  },
  render: function() {
    return (
      <div className="commentBox">
        <h1>Comments</h1>
        <CommentList data={this.state.data} />
        <CommentForm />
      </div>
    );
  }
});
```

`getInitialState()` 在组件的生命周期中仅执行一次，用于设置组件的初始化 state 。

#### 更新 state

当组件第一次创建的时候，我们想从服务器获取（使用 GET 方法）一些 JSON 数据来更新状态，以便展示最新的数据。我们将会使用 jQuery 发送一个异步请求到我们之前启动好的服务器，获取我们需要的数据。数据格式和相应代码看起来会是这个样子：

```json
[
  {"author": "Pete Hunt", "text": "This is one comment"},
  {"author": "Jordan Walke", "text": "This is *another* comment"}
]
```

```javascript{6-18}
// tutorial13.js
var CommentBox = React.createClass({
  getInitialState: function() {
    return {data: []};
  },
  componentDidMount: function() {
    $.ajax({
      url: this.props.url,
      dataType: 'json',
      cache: false,
      success: function(data) {
        this.setState({data: data});
      }.bind(this),
      error: function(xhr, status, err) {
        console.error(this.props.url, status, err.toString());
      }.bind(this)
    });
  },
  render: function() {
    return (
      <div className="commentBox">
        <h1>Comments</h1>
        <CommentList data={this.state.data} />
        <CommentForm />
      </div>
    );
  }
});
```

这里， `componentDidMount` 是一个组件渲染的时候被 React 自动调用的方法。动态更新界面的关键点就是调用 `this.setState()` 。我们用新的从服务器拿到的评论数组来替换掉老的评论数组，然后 UI 自动更新。有了这种反应机制，实现实时更新就仅需要一小点改动。在这里我们使用简单的轮询，但是你也可以很容易地改为使用 WebSockets 或者其他技术。

```javascript{3,15,20-21,35}
// tutorial14.js
var CommentBox = React.createClass({
  loadCommentsFromServer: function() {
    $.ajax({
      url: this.props.url,
      dataType: 'json',
      cache: false,
      success: function(data) {
        this.setState({data: data});
      }.bind(this),
      error: function(xhr, status, err) {
        console.error(this.props.url, status, err.toString());
      }.bind(this)
    });
  },
  getInitialState: function() {
    return {data: []};
  },
  componentDidMount: function() {
    this.loadCommentsFromServer();
    setInterval(this.loadCommentsFromServer, this.props.pollInterval);
  },
  render: function() {
    return (
      <div className="commentBox">
        <h1>Comments</h1>
        <CommentList data={this.state.data} />
        <CommentForm />
      </div>
    );
  }
});

ReactDOM.render(
  <CommentBox url="/api/comments" pollInterval={2000} />,
  document.getElementById('content')
);

```

这里，我们只需把 AJAX 调用移到一个分离的方法中去，在组件第一次加载的时候，以及之后每隔两秒钟，调用一下这个方法。尝试在你的浏览器中运行这些代码，然后改变 `comments.json` 文件（在 server 源码文件夹中）；在两秒钟之内，改动将会显示出来！

### 添加新的评论

现在是时候构造表单了。我们的 `CommentForm` 组件应该询问用户的名字和评论内容，然后发送一个请求到服务器，保存这条评论。

```javascript{5-9}
// tutorial15.js
var CommentForm = React.createClass({
  render: function() {
    return (
      <form className="commentForm">
        <input type="text" placeholder="Your name" />
        <input type="text" placeholder="Say something..." />
        <input type="submit" value="Post" />
      </form>
    );
  }
});
```

让我们使表单可交互。当用户提交表单的时候，我们应该清空表单，发送一个请求到服务器，然后刷新评论列表。首先，让我们监听表单的提交事件，然后清空表单。

```javascript{3-14,16-19}
// tutorial16.js
var CommentForm = React.createClass({
  handleSubmit: function(e) {
    e.preventDefault();
    var author = this.refs.author.value.trim();
    var text = this.refs.text.value.trim();
    if (!text || !author) {
      return;
    }
    // TODO: send request to the server
    this.refs.author.value = '';
    this.refs.text.value = '';
    return;
  },
  render: function() {
    return (
      <form className="commentForm" onSubmit={this.handleSubmit}>
        <input type="text" placeholder="Your name" ref="author" />
        <input type="text" placeholder="Say something..." ref="text" />
        <input type="submit" value="Post" />
      </form>
    );
  }
});
```

##### 事件

React 使用驼峰命名规范的方式给组件绑定事件处理器。我们给表单绑定一个`onSubmit`处理器，用于当表单提交了合法的输入后清空表单字段。

在事件回调中调用 `preventDefault()` 来避免浏览器默认地提交表单。

##### Refs

我们利用 `ref` 属性给子组件命名，通过 `this.refs` 引用 DOM 节点。

##### 用回调函数作为属性（ props ）

当用户提交评论的时候，我们需要刷新评论列表来加进这条新评论。在 `CommentBox` 中完成所有逻辑是合适的，因为 `CommentBox` 拥有代表评论列表的状态（ state ）。

我们需要从子组件传数据到它的父组件。我们在父组件的 `render` 方法中这样做：传递一个新的回调函数（ `handleCommentSubmit` ）到子组件，绑定它到子组件的 `onCommentSubmit` 事件上。无论事件什么时候触发，回调函数都会被调用：

```javascript{16-18,31}
// tutorial17.js
var CommentBox = React.createClass({
  loadCommentsFromServer: function() {
    $.ajax({
      url: this.props.url,
      dataType: 'json',
      cache: false,
      success: function(data) {
        this.setState({data: data});
      }.bind(this),
      error: function(xhr, status, err) {
        console.error(this.props.url, status, err.toString());
      }.bind(this)
    });
  },
  handleCommentSubmit: function(comment) {
    // TODO: submit to the server and refresh the list
  },
  getInitialState: function() {
    return {data: []};
  },
  componentDidMount: function() {
    this.loadCommentsFromServer();
    setInterval(this.loadCommentsFromServer, this.props.pollInterval);
  },
  render: function() {
    return (
      <div className="commentBox">
        <h1>Comments</h1>
        <CommentList data={this.state.data} />
        <CommentForm onCommentSubmit={this.handleCommentSubmit} />
      </div>
    );
  }
});
```

当用户提交表单的时候，在 `CommentForm` 中调用这个回调函数：

```javascript{10}
// tutorial18.js
var CommentForm = React.createClass({
  handleSubmit: function(e) {
    e.preventDefault();
    var author = this.refs.author.value.trim();
    var text = this.refs.text.value.trim();
    if (!text || !author) {
      return;
    }
    this.props.onCommentSubmit({author: author, text: text});
    this.refs.author.value = '';
    this.refs.text.value = '';
    return;
  },
  render: function() {
    return (
      <form className="commentForm" onSubmit={this.handleSubmit}>
        <input type="text" placeholder="Your name" ref="author" />
        <input type="text" placeholder="Say something..." ref="text" />
        <input type="submit" value="Post" />
      </form>
    );
  }
});
```

现在回调函数已经就绪，我们唯一需要做的就是提交到服务器，然后刷新评论列表：

```javascript{17-28}
// tutorial19.js
var CommentBox = React.createClass({
  loadCommentsFromServer: function() {
    $.ajax({
      url: this.props.url,
      dataType: 'json',
      cache: false,
      success: function(data) {
        this.setState({data: data});
      }.bind(this),
      error: function(xhr, status, err) {
        console.error(this.props.url, status, err.toString());
      }.bind(this)
    });
  },
  handleCommentSubmit: function(comment) {
    $.ajax({
      url: this.props.url,
      dataType: 'json',
      type: 'POST',
      data: comment,
      success: function(data) {
        this.setState({data: data});
      }.bind(this),
      error: function(xhr, status, err) {
        console.error(this.props.url, status, err.toString());
      }.bind(this)
    });
  },
  getInitialState: function() {
    return {data: []};
  },
  componentDidMount: function() {
    this.loadCommentsFromServer();
    setInterval(this.loadCommentsFromServer, this.props.pollInterval);
  },
  render: function() {
    return (
      <div className="commentBox">
        <h1>Comments</h1>
        <CommentList data={this.state.data} />
        <CommentForm onCommentSubmit={this.handleCommentSubmit} />
      </div>
    );
  }
});
```

###  优化：提前更新

我们的应用现在已经完成了所有功能，但是在新添加的评论出现在列表之前，必须等待请求完成，所以感觉很慢。我们可以提前添加这条评论到列表中，从而使应用感觉更快。

```javascript{17-19}
// tutorial20.js
var CommentBox = React.createClass({
  loadCommentsFromServer: function() {
    $.ajax({
      url: this.props.url,
      dataType: 'json',
      cache: false,
      success: function(data) {
        this.setState({data: data});
      }.bind(this),
      error: function(xhr, status, err) {
        console.error(this.props.url, status, err.toString());
      }.bind(this)
    });
  },
  handleCommentSubmit: function(comment) {
    var comments = this.state.data;
    var newComments = comments.concat([comment]);
    this.setState({data: newComments});
    $.ajax({
      url: this.props.url,
      dataType: 'json',
      type: 'POST',
      data: comment,
      success: function(data) {
        this.setState({data: data});
      }.bind(this),
      error: function(xhr, status, err) {
        console.error(this.props.url, status, err.toString());
      }.bind(this)
    });
  },
  getInitialState: function() {
    return {data: []};
  },
  componentDidMount: function() {
    this.loadCommentsFromServer();
    setInterval(this.loadCommentsFromServer, this.props.pollInterval);
  },
  render: function() {
    return (
      <div className="commentBox">
        <h1>Comments</h1>
        <CommentList data={this.state.data} />
        <CommentForm onCommentSubmit={this.handleCommentSubmit} />
      </div>
    );
  }
});
```

### 祝贺你!

你刚刚通过一些简单步骤构造了一个评论框。了解更多关于[为什么使用 React](/react/docs/why-react.html) 的内容，或者深入学习[ API 参考](/react/docs/top-level-api.html)，开始专研！祝你好运！
