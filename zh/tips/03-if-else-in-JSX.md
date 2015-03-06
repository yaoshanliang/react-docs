---
id: if-else-in-JSX
title: JSX 中的 If-Else
layout: tips
permalink: if-else-in-JSX.html
prev: inline-styles.html
next: self-closing-tag.html
---

`if-else` statements don't work inside JSX. This is because JSX is just syntactic sugar for function calls and object construction. Take this basic example:

你没法在JSX中使用 `if-else` 语句，因为 JSX 只是函数调用和对象创建的语法糖。看下面这个例子：

```js
// This JSX:
React.render(<div id="msg">Hello World!</div>, mountNode);

// Is transformed to this JS:
React.render(React.createElement("div", {id:"msg"}, "Hello World!"), mountNode);
```

```js
// JSX 代码:
React.render(<div id="msg">Hello World!</div>, mountNode);

// 编译成 JS 是这样的:
React.render(React.createElement("div", {id:"msg"}, "Hello World!"), mountNode);
```

This means that `if` statements don't fit in. Take this example:

这意味着 `if` 语句不合适。看下面这个栗子

```js
// This JSX:
<div id={if (condition) { 'msg' }}>Hello World!</div>

// Is transformed to this JS:
React.createElement("div", {id: if (condition) { 'msg' }}, "Hello World!");
```

That's not valid JS. You probably want to make use of a ternary expression:

这是不合语法的 JS 代码。不过你可以采用三元操作表达式：

```js
React.render(<div id={condition ? 'msg' : ''}>Hello World!</div>, mountNode);
```

If a ternary expression isn't robust enough, you can use `if` statements to determine which
components should be used.

如果三元操作表达式不够健壮，你也可以使用 `if` 语句来决定应该渲染那个组件。

```js
var loginButton;
if (loggedIn) {
  loginButton = <LogoutButton />;
} else {
  loginButton = <LoginButton />;
}

return (
  <nav>
    <Home />
    {loginButton}
  </nav>
)
```

Try using it today with the [JSX compiler](/react/jsx-compiler.html).

马上开始使用[JSX compiler](/react/jsx-compiler.html)吧。