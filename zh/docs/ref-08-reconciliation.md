---
id: reconciliation
title: Reconciliation
permalink: reconciliation.html
prev: special-non-dom-attributes.html
next: glossary.html
---

React 的关键设计目标是使 API 看起来就像每一次有数据更新的时候，整个应用重新渲染了一样。这就极大地简化了应用的编写，但是同时使 React 易于驾驭，也是一个很大的挑战。这篇文章解释了我们如何使用强大的试探法来将 O(n<sup>3</sup>) 复杂度的问题转换成 O(n) 复杂度的问题。


## 动机（Motivation）

生成最少的将一颗树形结构转换成另一颗树形结构的操作，是一个复杂的，并且值得研究的问题。[最优算法](http://grfia.dlsi.ua.es/ml/algorithms/references/editsurvey_bille.pdf)的复杂度是 O(n<sup>3</sup>)，n 是树中节点的总数。

这意味着要展示1000个节点，就要依次执行上十亿次的比较。这对我们的使用场景来说太昂贵了。准确地感受下这个数字：现今的 CPU 每秒钟能执行大约三十亿条指令。因此即便是最高效的实现，也不可能在一秒内计算出差异情况。

既然最优的算法都不好处理这个问题，我们实现一个非最优的 O(n) 算法，使用试探法，基于如下两个假设：

1、拥有相同类的两个组件将会生成相似的树形结构，拥有不同类的两个组件将会生成不同的树形结构。
2、可以为元素提供一个唯一的标志，该元素在不同的渲染过程中保持不变。

实际上，这些假设会使在几乎所有的应用场景下，应用变得出奇地快。


## 两个节点的差异检查（Pair-wise diff）

为了进行一次树结构的差异检查，首先需要能够检查两个节点的差异。此处有三种不同的情况需要处理：


### 不同的节点类型

如果节点的类型不同，React 将会把它们当做两个不同的子树，移除之前的那棵子树，然后创建并插入第二棵子树。

```xml
renderA: <div />
renderB: <span />
=> [removeNode <div />], [insertNode <span />]
```

该方法也同样应用于传统的组件。如果它们不是相同的类型，React 甚至将不会尝试计算出该渲染什么，仅会从 DOM 中移除之前的节点，然后插入新的节点。

```xml
renderA: <Header />
renderB: <Content />
=> [removeNode <Header />], [insertNode <Content />]
```

具备这种高级的知识点对于理解为什么 React 的差异检测逻辑既快又精确是很重要的。它对于避开树形结构大部分的检测，然后聚焦于似乎相同的部分，提供了启发。

一个 `<Header>` 元素与一个 `<Content>` 元素生成的 DOM 结构不太可能一样。React 将重新创建树形结构，而不是耗费时间去尝试匹配这两个树形结构。

如果在两个连续的渲染过程中的相同位置都有一个 `<Header>` 元素，将会希望生成一个非常相似的 DOM 结构，因此值得去做一做匹配。


### DOM 节点

当比较两个 DOM 节点的时候，我们查看两者的属性，然后能够找出哪一个属性随着时间产生了变化。

```xml
renderA: <div id="before" />
renderB: <div id="after" />
=> [replaceAttribute id "after"]
```

React 不会把 style 当做难以操作的字符串，而是使用键值对对象。这就很容易地仅更新改变了的样式属性。

```xml
renderA: <div style={{'{{'}}color: 'red'}} />
renderB: <div style={{'{{'}}fontWeight: 'bold'}} />
=> [removeStyle color], [addStyle font-weight 'bold']
```

在属性更新完毕之后，递归检测所有的子级的属性。


### 自定义组件

我们决定两个自定义组件是相同的。因为组件是状态化的，不可能每次状态改变都要创建一个新的组件实例。React 利用新组件上的所有属性，然后在之前的组件实例上调用 `component[Will/Did]ReceiveProps()`。

现在，之前的组件就是可操作了的。它的 `render()` 方法被调用，然后差异算法重新比较新的状态和上一次的状态。


## 子级优化差异算法（List-wise diff）

### 问题点（Problematic Case）

为了完成子级更新，React 选用了一种很原始的方法。React 同时遍历两个子级列表，当发现差异的时候，就产生一次 DOM 修改。

例如在末尾添加一个元素：

```xml
renderA: <div><span>first</span></div>
renderB: <div><span>first</span><span>second</span></div>
=> [insertNode <span>second</span>]
```

在开始处插入元素是比较困难的。React 将会发现，两个节点都是 span，因此直接修改第一个 span 元素，然后插入一个新的 span 元素：
（Inserting an element at the beginning is problematic. React is going to see that both nodes are spans and therefore run into a mutation mode.）

```xml
renderA: <div><span>first</span></div>
renderB: <div><span>second</span><span>first</span></div>
=> [replaceAttribute textContent 'second'], [insertNode <span>first</span>]
```

有很多的算法尝试找出转换一组元素的最小操作集合。[Levenshtein 距离](http://en.wikipedia.org/wiki/Levenshtein_distance)能够使用单一元素插入、删除和替换在 O(n<sup>2</sup>) 的复杂度下找到最小操作集合。即使使用 Levenshtein，当一个节点移到了另外一个位置，Levenshtein 不会发现，并且算法做这件事有很糟糕的复杂度。
（There are many algorithms that attempt to find the minimum sets of operations to transform a list of elements. [Levenshtein distance](http://en.wikipedia.org/wiki/Levenshtein_distance) can find the minimum using single element insertion, deletion and substitution in O(n<sup>2</sup>). Even if we were to use Levenshtein, this doesn't find when a node has moved into another position and algorithms to do that have much worse complexity.）

### 键（Keys）

为了解决这个表面上很棘手的问题，一个可选的 HTML 属性被引入了。可以给每个子级一个键值，用于将来的匹配比较。如果指定了一个键值，React 就能够知道节点插入、节点删除和节点替换，并且使用哈希表使节点移动复杂度为 O(n)。
（In order to solve this seemingly intractable issue, an optional attribute has been introduced. You can provide for each child a key that is going to be used to do the matching. If you specify a key, React is now able to find insertion, deletion, substitution and moves in O(n) using a hash table.）


```xml
renderA: <div><span key="first">first</span></div>
renderB: <div><span key="second">second</span><span key="first">first</span></div>
=> [insertNode <span>second</span>]
```

实际上，找到一个键值不是很困难。大多数时候，要展示的元素已经有一个唯一的标识了。当没有唯一标识的时候，可以给组件添加一个新的 ID 属性，或者计算内容的哈希值，然后生成一个键值。记住，键值仅需要在兄弟节点中唯一，而不是全局唯一。
（In practice, finding a key is not really hard. Most of the time, the element you are going to display already has a unique id. When that's not the case, you can add a new ID property to your model or hash some parts of the content to generate a key. Remember that the key only has to be unique among its siblings, not globally unique.）


## Trade-offs

记住同步更新算法是一种实现细节是很重要的。React 能够在每次操作中重新渲染整个应用；结果将会是一样的。我们经常优化这个预判算法来使常规的使用场景更加快速。
（It is important to remember that the reconciliation algorithm is an implementation detail. React could re-render the whole app on every action; the end result would be the same. We are regularly refining the heuristics in order to make common use cases faster.）

在当前的实现中，你能够知道某个子级树已经从它的兄弟节点中移除，但是你不能辨别出它是否移到了其它某个地方。该算法将会重新渲染整个子树。
（In the current implementation, you can express the fact that a sub-tree has been moved amongst its siblings, but you cannot tell that it has moved somewhere else. The algorithm will re-render that full sub-tree.）

由于依赖于两个启发，如果在它们之后的假设条件没有满足，性能将会大打折扣。
（Because we rely on two heuristics, if the assumptions behind them are not met, performance will suffer.）

1、算法将不会尝试匹配不同组件类的子树。如果发现交换两个输出非常相似的两个组件类，你可能想使它们变成同一个组件类。在实际操作中，这不是一个问题。
（1. The algorithm will not try to match sub-trees of different components classes. If you see yourself alternating between two components classes with very similar output, you may want to make it the same class. In practice, we haven't found this to be an issue.）

2、如果没有提供稳定的键值（例如通过 Math.random() 生成），所有子树将会在每次数据更新中被重新渲染。通过给用户选择键值的机会，他们能够给特定场景写出更优化的代码。
（2. If you don't provide stable keys (by using Math.random() for example), all the sub-trees are going to be re-rendered every single time. By giving the users the choice to choose the key, they have the ability to shoot themselves in the foot.）

