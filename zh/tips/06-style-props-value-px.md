---
id: style-props-value-px
title: Shorthand for Specifying Pixel Values in style props
layout: tips
permalink: style-props-value-px.html
prev: maximum-number-of-jsx-root-nodes.html
next: children-props-type.html
---

When specifying a pixel value for your inline `style` prop, React automatically appends the string "px" for you after your number value, so this works:

当为内联样式指定一个像素值得时候， React 会在你的数字后面自动加上 "px" , 所以下面这样的写法是有效的：

```js
var divStyle = {height: 10}; // rendered as "height:10px"
React.render(<div style={divStyle}>Hello World!</div>, mountNode);
```

See [Inline Styles](/react/tips/inline-styles.html) for more info.

Sometimes you _do_ want to keep the CSS properties unitless. Here's a list of properties that won't get the automatic "px" suffix:

有时候你_的确需要_保持你的CSS属性的独立性。下面是不会自动加 "px" 后缀的 css 属性列表： 

- `columnCount`
- `fillOpacity`
- `flex`
- `flexGrow`
- `flexShrink`
- `fontWeight`
- `lineClamp`
- `lineHeight`
- `opacity`
- `order`
- `orphans`
- `strokeOpacity`
- `widows`
- `zIndex`
- `zoom`
