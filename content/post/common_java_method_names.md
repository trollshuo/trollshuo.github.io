---
title: "Java方法通用名最佳实践"
date: 2020-12-22T18:20:59+08:00
showDate: true
draft: false
tags: [Java, Translate, BestPractice]
---

在讨论了[一些](http://blog.joda.org/2011/08/implementations-of-interfaces-prefixes.html)[常见](http://blog.joda.org/2011/08/more-java-prefixs-and-suffixes.html)的类名前缀和后缀之后，本文想讨论一些方法命名的惯例。同样，这一切都与Java有关---其他语言则完全不同。
<!--more--> 

## 通用方法命名模式

不论对错与否，Java都是一种基于模式的语言。我的意思是，在Java开发中，很多被认为是最佳实践的东西都是通过遵循标准模式来实现的。当然，这里指的不仅仅是 [Gang of Four](http://en.wikipedia.org/wiki/Design_Patterns_%28book%29) 这本书，还包括无数的其他模式。这些模式中通常都是 "视觉模式 "和代码模式一样多。举个例子，通过以某种一致的方式布局我们的代码，这帮助了无数使用我们代码的开发者。

应当注意的是，我并不是说基于模式/命名的方法比新的语言特性更好。只是恰好在Java开发中，对于我们要处理的很多问题，这是最佳实践。 （剪切和粘贴是Java开发中的一种生活方式，这种方式也有好的一面)

在无数模式中，其中一种模式是方法命名模式。Java中的这种方法受两方面的影响很大---JDK中命名选择的规模和主导地位，以及Java-Beans的设计。早在2006年，我就为不可变类[提出](http://blog.joda.org/2006/05/immutable-pojos-improving-on_6406.html)了一些额外的变体，之后便通过[Joda-Time](https://www.joda.org/joda-time/)推广开来了。下面这个列表其实包括了这两方面的内容，再加上我从ThreeTen/JSR-310中添加的一些新内容。当然，并不是要列出所有可能的方法命名惯例，只是列出了一些比较流行的方法，以及那些具有特定语义的方法。

Name | Represent | 
---------|----------|
get | 从一个对象中获取一些东西，比如 bean.getBar()。这也可以和键一起使用，通过索引查找一个 list 或通过键查找一个 map，如 list.get(index) 或 map.get(key)。
is | 检查一个对象或一个对象的属性是否为真或假。例如foo.isValid()。 
check | 检查是否为真，如果不为真则抛出异常。例如foo.checkValid()。
contains | 检查一个集合是否包含被查询的对象，例如coll.contains(bar)。这可以用在那些封装类或者其他作为集合的类上。
remove | 从一个集合中移除一个元素，比如 coll.remove(bar)。这可以用在那些封装类或者其他作为集合的类上。
clear | 清除对象，通常但不一定是集合，使其为 "空"。
put | 可变的putter。这将使目标对象发生改变，通过某种键值对的形式替换或添加。例如map.put(key,bar)和bean.putFoo(key,bar)。
set | 可变设置器。这将使目标对象设置一个属性，例如bean.setBar(bar)。
with | 不可变的设置器。返回一个改变了一个或多个属性的原始副本，如 result = original.withBar(bar)。
to | 将此对象转换为一个独立的对象，通常是另一种类型。一般不接受任何参数，但在某些特殊情况下，可以使用。
as | 将此对象转换为另一个对象，对原始对象的更改会在结果中显示出来，如Arrays.asList()。
build | 基于指定的参数、目标对象的状态，或者两者都有，建立另一个对象。
add/subtract | 给数量添加/减去一个值。这将使目标数量（数字、日期、时间、距离......）加/减 "foo "属性。这个命名也单独用于添加元素到一个集合。
plus/minus | 不可变版本的add/subract。返回一个添加/减去值的原始副本。这个名称似乎并不能很好地用于将元素添加到一个不可变的集合中。
append | 有时被添加到列表末尾的方法使用，比如在StringBuilder中。
reset | 将对象重置回一个合适的初始状态，准备好被重新使用。
past tense | 用在不可变类上，以帮助提示调用者，该方法不会改变目标，而是必须分配给另一个变量。返回一个应用了方法命名的目标对象的副本。例如有immutable.normalized()、immutable.multipliedBy(bar)、immutable.dividedBy(bar)和immutable.negated()。

接下来还有一些静态方法命名。

Name | Represent
------- | -------
of | 静态工厂方法。通常用于不可变的类，构造函数是私有的（允许缓存）。这是EnumSet和ThreeTen/JSR-310使用的方法。
valueOf | JDK使用的较长形式的 of。
from | 较长形式的of。JSR-310在类型之间进行 "松散“转换时使用'from'，即有合理的失败机会。相比之下，'of'是在转换几乎肯定会成功时使用。
parse | 静态工厂方法，通过解析一个字符串来创建一个类的实例。这可能只是另一个of方法，但我认为用一个特殊意义的名字，语义会更清晰。

## 精彩问答

Q: 在list中增加 "append"如何？

A: 我对 "append "加入到list中没意见。该list建议使用过去分词 "appendedWith"，但这有点笨拙。在JSR-310中，我们有以 "at "为前缀的方法--date.atTime(...)。这在时间领域是有意义的。

Q: 你对什么时候用 "of"，什么时候用 "from"，有哪些好的建议吗？Java 8 DateTime也把它们混在一起（甚至在同一个类中），这让我觉得，一定有区别：from(TemporalAccessor)，为什么不是of()？所有of()方法的JavaDoc甚至都说是 "获取自"。
JDK中也有很多valueOf(String)，但另一方面也有UUID.fromString(String)。

A: JSR-310在进行类型间的 "松散 "转换时使用 "from"，即有合理机会失败的转换。相反，当转换几乎肯定会成功时，则使用 "of"。
例如：
- LocalDate.of(year, month, day) is bascially taking the three things that a LocalDate is _formed_ _of_.
- LocalDate.ofYearDay(year, dayOfYear) is being explicit about the input arguments, and performing a very simple conversion that, providing the values are in range, will succeed.
- LocalDate.from(temporal) is a slower and more complicated attempt to extract meaning _from_ the input temporal. The temporal could be a ZonedDateTime, in which case it succeeds, or it could be a YearMonth, in which case it fails. ie. the type system isn't helping you with "fr".

Q: 如果java.net.URL遵循这样的命名惯例，除了String（"spec"）构造函数外，每个构造函数都是 "of"，那么 "from "或 "parse "更好？

A: 
- UUID：是的，我会用 "parse"，而不是 "fromString"。"parse "是 "from "方法的一种特殊类型。
- java.net.URL: 基本上，包含 "protocol "在内的部分都符合 "of "的概念。 取 "spec "的可能是 "of"，也可能是 "parse"。我想往往使用 "from "的情况比较少。

> 本文翻译自[Common Java method names - Stephen Colebourne's blog](https://blog.joda.org/2011/08/common-java-method-names.html)