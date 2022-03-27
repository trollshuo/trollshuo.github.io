---
title: "代码注释最佳实践"
date: 2022-03-12T15:10:14+08:00
showDate: true
draft: false
tags: [Comments, BestPractice, Translate]
---

为什么要翻译这篇文章？

工作中遇到一些代码，添加了注释但是表达出来的效果往往不甚理想。同时发现网上很多资源都是教你如何写代码，却很少讲述怎么写注释。

因此试着翻译一下，给自己长点见识。当然也希望给更多的人带来帮助。

<!--more-->

## TL;DR

**规则一: 注释不应与代码语义重复**

**规则二: 代码表达信息而不是使用注释**

**规则三: 如果写出不清不楚的注释，那么往往说明代码不够清晰**

**规则四: 注释应当减少困惑而不是成为其来源**

**规则五: 在注释中解释不规范的代码**

**规则六: 复制的代码应当提供链接来源注释**

**规则七: 在需要的位置为外部参考资料添加链接注释**

**规则八: 注释修复 BUG 的原因**

**规则九: 使用注释来标记功能实现 TODO**

那么每一条规则背后都是怎么总结出来的呢？以及如何在日常开发中应用这些规则？它的使用场景又是什么呢？

接下来的文章内容主要就是解释上述的问题。

## 规则一: 注释不应与代码语义重复

许多初学者会在代码中添加很多详细的注释。原因多种多样，注释的方式也令人眼花缭乱。

例如，在每个大括号后面添加表明结束标志的注释：

```java
if(x > 3) {
    ...
} // if
```

在每一行语句后面添加注释：

```java
i = i + 1;   // Add one to i
```

更有甚者，在每行代码都写上注释：

```java
// create a for loop // <-- comment
for // start for loop
( // round bracket
  // newline
int // type for declaration
i   // name for declatation
=   // assignment operator for declaration
0   // start value for i
)   // end for loop
```

以上这些案例的注释往往带来负面效果，它们会：

- 造成视觉上的干扰

- 花费更多时间读写

- 变得过时

而且这些注释都是与代码语义重复，在没有增加任何有用信息的情况下，产生了更多的维护成本。

## 规则二: 代码表达信息而不是使用注释

注释的另一个误用是本应使用代码表达的信息，却使用了注释。

这是一个会经常犯错的例子，那就是使用单个字母命名变量，然后添加注释来描述其用途。

```java
private static Node getBestChildNode(Node node) {
    Node n; // best child node candidate
    for(Node node: node.getChildren()) {
        // update n if the current state is better
        if(n == null || utility(node) > utility(n)) {
            n = node;
        }
    }
    return n;
} 
```

这种情况下，通过更好的变量命名，可以避免添加注释：

```java
private static Node getBestChildNode(Node node) {
    Node bestNode;
    for(Node currentNode: node.getChildren()) {
        if(bestNode == null || utility(currentNode) > utility(bestNode)) {
            bestNode = currentNode;
        }
    }
    return bestNode;
} 
```

正如 Kernighan 和 Plauger 在《编程风格的要素》中写道：

> 不要为烂代码添加注释---直接重写它。

## 规则三: 如果写出不清不楚的注释，那么往往说明代码不够清晰

Unix 源码中有一个最臭名昭著的注释是，“你不应该理解这个”，它出现在一些上下文切换代码之前。Dennis Ritchie 后来解释说，它的目的是“本着‘这不会出现在考试中’的想法，而不是作为一个厚颜无耻的挑战“。不幸的是，事实证明，他和合著者 Ken Thompson 自己也不明白，后来不得不重写。

这就要提一下 Kernighan 定律了：

> 调试的难度是当初写代码时的两倍。因此，如果你把代码写得越高深莫测，根据定义，你就越没有聪明到可以调试它。

这种直接警告代码阅读者远离代码的行为像极了你打开了汽车的危险灯：相当于承认你知道正在做非法的事情。我们需要取而代之的是，把代码重写成足够容易理解，方便更好解释，最好是一看就懂的。

## 规则四: 注释应当减少困惑而不是成为其来源

如果我们讨论烂注释，却不提 Steven Levy 的《黑客》中的这个故事，那这篇文章就索然无味了。

计算机革命的英雄：

> [ Peter Samson ] 拒绝在他的源码中添加注释，特别隐晦地解释他在某一特定时间正在做什么。在 Samson 写的一个分布广泛的程序有数百条汇编语言指令，只有一条包含数字1750的指令旁边有注释。这个注释是 RIPJSB，人们对它的含义绞尽脑汁，直到有人发现，1750年是巴赫去世的年份，而 Samson 写的是 Rest In Peace Johann Sebastian Bach 的简写。

虽然我和其他人一样也欣赏一个好的黑客，但这并不具有示范意义。如果你的注释造成了困惑而不是消除困惑，那最好把它删掉。

## 规则五: 在注释中解释不规范的代码

对别人可能会认为不需要或多余的代码进行注释是个好主意。例如 [App Inventor](https://github.com/mit-cml/appinventor-sources)的这段代码（所有正面例子的出处）：

```java
final Object value = (new JSONTokener(jsonString)).nextValue();
// Note that JSONTokener.nextValue() may return
// a value equals() to null.
if(value == null || value.equals(null)) {
    return null;
}
```

如果没有注释，有人可能会想要 "优化"这段代码，或者把它看作是一个神秘但必不可少的咒语。一般来说，解释为什么需要这些代码会节省未来代码阅读者的时间和焦虑。

对于代码是否需要解释，往往需要做出判断。在学习 Kotlin 时，我在一个 Android 教程中遇到了这样的代码：

```kotlin
if(b == true)
```

我立即想，是否可以用以下方式代替：

```kotlin
if(b)
```

就像在 Java 中那样。经过一番[研究](https://kotlinlang.org/docs/idioms.html#nullable-boolean)，我了解到，为了避免丑陋的空值检查，可空的布尔变量会被明确地比作"真"。

```kotlin
if (b != null && b)
```

所以强烈建议，除非是专门为新手写的教程，否则不要把常见习语的注释写进去。

## 规则六: 复制的代码应当提供链接来源注释

如果你像大多数程序员那样，有时会使用在网上找到的代码。那么最好为其提供源码的链接出处，这会方便未来的代码阅读者获得完整的背景信息。例如：

- 解决了什么问题

- 谁提供的代码

- 解决方案被推荐的原因

- 评论者对它的看法

- 它是否仍然有效

- 如何改进它

一个典型的参考例子：

```java
/** Converts a Drawable to Bitmap. via https://stackoverflow.com/a/46018816/2219998. */
```

根据答案的链接可以看出：

- 该代码的作者是Tomáš Procházka，他在 StackOverflow 上排名前3%。

- 一个评论者提供了一个优化方法，已经被纳入到了 repo 中。

- 另一个评论者提出了一个避免边界情况的方法。

与这个注释形成鲜明对比的注释反例（为保护隐私，稍作改动）：

```java
// Magical formula taken from a stackoverflow post, reputedly related to
// human vision perception.
return (int) (0.3 * red + 0.59 * green + 0.11 * blue);
```

任何想了解这段代码的人都必须去搜索这个公式。粘贴 URL 的做法明显要比后来找到参考资料快得多。

一些程序员可能不愿意表明这不是他们自己写的代码，但复用代码是一个明智的选择，不仅可以节省时间，而且让你得到更多关注的好处。当然，你不应该[粘贴你不理解的代码](https://stackoverflow.blog/2019/11/26/copying-code-from-stack-overflow-you-might-be-spreading-security-vulnerabilities/)。

人们从 Stack Overflow 的问题和答案中[复制了大量](https://stackoverflow.blog/2021/04/19/how-often-do-people-actually-copy-and-paste-from-stack-overflow-now-we-know/)的代码。这些代码属于知识共享（Creative Commons）许可范围，需要注明出处。参考链接注释就可以很好的满足这一要求。

同样地，你应该注释那些有帮助的参考教程链接，这样就可以再次找到它们，也是对作者的感谢：

```java
// Many thanks to Chris Veness at http://www.movable-type.co.uk/scripts/latlong.html
// for a great reference and examples.
```

## 规则七: 在需要的位置为外部参考资料添加链接注释

当然，不是所有的参考资料都是源自 Stack Overflow。

参考例子：

```java
// http://tools.ietf.org/html/rfc4180 suggests that CSV lines
// should be terminated by CRLF, hence the \r\n.
csvStringBuilder.append("\r\n");
```

添加标准文档和其他文档的链接注释可以帮助代码阅读者理解代码所要解决的问题。虽然这些信息可能就在设计文档的某个地方，但一个位置良好的注释可以在最需要的时候给代码阅读者提供方向。在这个例子中，跟随链接表明 RFC 4180 已经被 RFC 7111 更新了，这是一个很有用的信息。

## 规则八: 注释修复 BUG 的原因

不仅仅要在最初写代码的时候，而且要在修改代码的时候，特别是在修复 BUG 的时候，都应该添加注释。

参考注释：

```java
  // NOTE: At least in Firefox 2, if the user drags outside of the browser window,
  // mouse-move (and even mouse-down) events will not be received until
  // the user drags back inside the window. A workaround for this issue
  // exists in the implementation for onMouseLeave().
  @Override
  public void onMouseMove(Widget sender, int x, int y) { .. }
```

这些注释不仅可以帮助代码阅读者理解当前方法和引用方法中的代码，而且对于判断是否还需要这些代码以及如何测试这些代码很有帮助。

注释对引用问题跟踪器也有帮助：

```java
// Use the name as the title if the properties did not include one (issue #1425)
```

虽然```git blame``` 可以用来查找添加或修改某一行的提交，但 commit 的信息往往很简短，而且最重要的修改（如修复问题#1425）可能不是最近 commit 的一部分（例如，将一个方法从一个文件移到另一个文件）。

## 规则九: 使用注释来标记功能实现 TODO

有时，即使代码有已知的局限性，也有必要对其进行检查。虽然不分享自己代码中已知的缺陷是很诱人的，但最好是把这些缺陷明确地表达出来。

例如使用 TODO 注释：

```java
// TODO(hal): We are making the decimal separator be a period, 
// regardless of the locale of the phone. We need to think about 
// how to allow comma as decimal separator, which will require 
// updating number parsing and other places that transform numbers 
// to strings, such as FormatAsDecimal
```

使用这种注释的标准格式有助于衡量和解决技术债务。更好的办法是，在你的跟踪器中添加一个问题，并在注释中引用该问题。

## 结论

希望上面的例子已经表明，注释并不是为烂代码提供借口或修复。它们通过提供不同类型的信息来补充优秀的代码。正如 Stack Overflow 的联合创始人 Jeff Atwood 所写的那样：

> [代码告诉你如何实现，注释告诉你实现的原因](https://blog.codinghorror.com/code-tells-you-how-comments-tell-you-why/)

遵循这些规则应该可以为你和你的伙伴们节省时间和减少挫败感。

期待在评论中可以集思广益，再度完善这些规则。

> 本文翻译自 [Best practices for writing code comments - Stack Overflow Blog](https://stackoverflow.blog/2021/12/23/best-practices-for-writing-code-comments/)
