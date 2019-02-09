---
title: React as a UI Runtime
date: '2019-02-02'
spoiler: An in-depth description of the React programming model.
---

许多教程中提到 React 是一个 UI 库，这说得没什么问题。就像字面上一样，它确实是一个 UI 库！
![React homepage screenshot: "A JavaScript library for building user interfaces"](./react.png)

我之前写过关于创建 [UI](/the-elements-of-ui-engineering/) 所遇到的挑战，但是这篇博客将采用一个不同的方式的来讨论 React—更接近 [programming runtime](https://en.wikipedia.org/wiki/Runtime_system)

**这篇文章不会教你怎么使用 React 来创建 UI，**如果你对 React 的编程方式有了更为深入的理解后，这篇文章可能帮到你。

---

**注意：如果你正在学习 React，最好先看[文档](https://reactjs.org/docs/getting-started.html#learn-react)**

<font size="60">⚠️</font>

*这是一篇深度文章，而不是一篇新人向教程！*在这里，我会从首要原则来描述大部分的 React 细节。我不会解释怎么使用它们—只说明它们是如何工作的。

这文章面向有经验的开发和那些还在权衡是否使用 React 的其他 UI 库的使用者。我希望它能帮助到你们！

**很多开发者即使没有考虑过这方面的问题，也舒舒服服地使用了 React 很长一段时间，** This is definitely a programmer-centric view of React rather than, say, a [designer-centric one](http://mrmrs.cc/writing/2016/04/21/developing-ui/). But I don’t think it hurts to have resources for both.

看完了免责声明，起飞🛫️吧！

「译者注」：现在开始进入正题，标题和一些通用术语不翻译。

---

## Host Tree

一部分程序吐出(output)数字，一些程序会写诗。不同的语言和它们的 runtimes 通常会针对一些特定的用例进行优化，React 也一样。

React 程序通常会得到**一颗随时会改变的树**。它可能是 [DOM 树](https://www.npmjs.com/package/react-dom)、[iOS 层级图(iOS hierarchy)](https://developer.apple.com/library/archive/documentation/General/Conceptual/Devpedia-CocoaApp/View%20Hierarchy.html)，[PDF primitives](https://react-pdf.org/)，甚至一个 [JSON](https://reactjs.org/docs/test-renderer.html) 对象。大多是时候我们都希望它为我们展示 UI。我们之所以称它为 ”*host* tree”是因为它属于 **宿主环境（host environment）** 的一部分—就像 DOM 和 iOS。Host tree 通常会有它们[自身](https://developer.mozilla.org/en-US/docs/Web/API/Node/appendChild)[特有](https://developer.apple.com/documentation/uikit/uiview/1622616-addsubview)的命令式（imperative） API。React is a layer on top of it.

所以 React 是用来做什么的？抽象地说，它帮助你在响应一些类似于交互，网络响应，计时器等外部事件时，也能够可预测地管理这复杂的 Host tree。

当一个专用工具可以通过施加特定的约束并从中获益的时候，它表现得会比通用的工具要好。React 在两个原则上做了一个赌注：
* **稳定性。** Host tree 相对稳定，大多数的更新都不会从根本上改变它的整体结构。一个 app 每秒都会重新排列它所有的交互元素的工具显然很难使用，我的 button 去哪了？为什么我的显示屏在乱动？
* **规律性。** Host tree 可以分解成外观和行为一致的 UI 模式（比如按钮 🔘，列表，头像👤）而不是随机的形状。

**对于大多数 UI 来说这些行为是正确的。**但是如果你需要的 UI 没有稳定的输出模式时，React 就不适合了。比如，React 可以帮助你写一个类似 Twitter 客户端的玩意，但是写不了 [3D pipes screensaver](https://www.youtube.com/watch?v=Uzx9ArZ7MUU)
