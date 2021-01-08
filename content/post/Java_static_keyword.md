---
title: "Java static关键字完整指南"
date: 2021-01-06T20:19:48+08:00
showDate: true
draft: false
tags: [Java, Keyword]
---

最近整理了一部分资料，尝试通过探索static关键字的历史作为开端，接着阐述并结合它的自身特点来对实际运用场景作一些总结。

<!--more-->

说起来，static这个关键字的发展历程很有趣。
起初，在C语言中引入了static关键字，用来表示当一个代码块退出时不会消失的局部变量。在这种情况下，static关键字是有意义的。这个变量会一直存在，当再次输入代码块时，它仍然存在。
接下来，static关键字在C语言中又有了第二个含义，用来表示不能从其他文件中访问的全局变量和函数。为了避免引入一个新的关键字，static这个关键字被简单地重复使用。
最后，C++又重复使用了这个关键字来作第三个不相关的解释--表示属于一个类的变量和函数，但不属于该类的任何特定对象。

这正是我们将要介绍的在Java中的含义。

在Java中，关键字是不能作为标识符的保留字。而Java总共有57个关键字，其中一个便是 "static"关键字。
static关键字主要用于内存管理。它可以与变量、方法、代码块和嵌套类一起使用。下面主要围绕这四种场景来阐述它们的自身特点与实际运用场景。

## 静态字段
### 静态变量
静态变量是类的所有实例(或对象)所共有的，因为它是一个类级变量。因此，我们可以在不创建类实例的情况下访问静态成员。

如果我们把一个类中的字段定义为static，那么这个类仅且只有一个这样的字段。相反，每个对象都有自己的非静态实例字段的副本。

例如，我们假设要给每个员工分配一个唯一的Id号。我们在Employee类中添加一个实例字段id和一个静态字段nextId。
```
class Employee
{
   private static int nextId = 1;
   private int id;
   . . .
}
```
那么现在每个Employee对象都有自己的id字段，但只有nextId字段是该类的所有实例共享的。

让我们一起来实现一个简单的set方法。
```
public void setId()
{
   id = nextId;
   nextId++;
}
```
假设我们想要为harry设置员工Id号： 
`harry.setId();`

这其实就是将harry的id字段设置为静态字段nextId的当前值，并对静态字段的值进行递增。

因此，实际上执行的代码如下：
```
*harry.*id = *Employee.*nextId;
*Employee.*nextId++;
```

### 静态常量
一般来说，静态变量在Java中也是很少使用的。但是，静态常量却是比较常见。这些静态常量通常由static final关键字定义，并以大写字母表示。这其实也是为什么有些人喜欢用大写字母来表示静态变量的原因。

例如，Math类中定义了一个静态常量：
```
public class Math
{
   . . .
   public static final double PI = 3.14159265358979323846;
   . . .
}
```
我们可以在程序中以Math.PI的形式访问这个常量。
如果省略了static这个关键字，那么PI将是Math类的一个实例字段。也就是说，我们需要这个类的一个对象来访问PI，而每个Math对象都会有自己的PI副本。

另一个我们经常使用的静态常量场景是System.out。它在System类中的声明如下
```
public class System
{
   . . .
   public static final PrintStream out = . . .;
   . . .
}
```
由于out已被声明为final，所以我们不能再为其分配其他输出流。

`System.out = new PrintStream(. . . ); // ERROR-out is final`

### 静态变量该如何初始化？
* 静态变量在类被加载时被初始化。
* 静态变量在该类的任何对象被创建之前被初始化。
* 静态变量在该类的任何静态方法执行之前被初始化。

## 静态方法
静态方法可以在不使用类的对象(实例)的情况下访问类变量(静态变量)，但是非静态方法和非静态变量只能使用对象来访问。

通常，我们会在下面两种场景下使用静态方法：
* 当一个方法不需要访问对象状态时，因为所有需要的参数都是作为显式参数提供的（例如：Math.pow）。
* 当一个方法只需要访问类的静态字段时（例如：Employee.getNextId）。

### 工厂方法
这是静态方法的一个常见用法。像LocalDate和NumberFormat这样的类使用静态**工厂方法**来构造对象。在实际开发中，我们经常会使用到工厂方法LocalDate.now和LocalDate.of。

我们来看一下NumberFormat类如何创建各种风格的formatter对象：
```
NumberFormat currencyFormatter = NumberFormat.getCurrencyInstance();
NumberFormat percentFormatter = NumberFormat.getPercentInstance();
double x = 0.1;
System.out.println(currencyFormatter.format(x)); // prints $0.10
System.out.println(percentFormatter.format(x)); // prints 10%
```

#### 为什么NumberFormat类不直接使用构造函数呢？
主要有两个原因：
* 我们不能给构造函数命名。构造函数的名称总是和类的名称一样的，但我们希望用两个不同的名称来获得货币实例和百分比实例。
* 当我们使用构造函数时，我们不能改变构造对象的类型。但是工厂方法实际上返回的是类DecimalFormat的对象，这个子类继承自NumberFormat。

### 为什么Java中main方法是静态的？
在每一个Java程序中，我们都声明main方法是静态的。这是因为要运行程序，JVM应该能够在内存中没有对象存在的初始阶段调用main方法。
如果是非静态方法，JVM先创建一个对象，然后再调用main()方法，会导致额外的内存分配问题。

### 注意事项
* 静态方法不能使用/this/或/super/关键字。
* 抽象方法不能是静态的。
* 静态方法不能被覆盖。
* 静态方法只能访问静态变量和其他静态方法。
* 静态方法属于类而不是类的对象。
* 静态方法可以被调用，而不需要创建一个类的实例。
* 静态方法可以访问静态数据成员，并可以改变其值。

## 代码块
代码块的使用场景是比较简单的，如果有些代码必须在项目启动的时候就执行的场景,我们可以使用静态代码块。这主要是由它的自身特点决定的：
* 用于初始化静态数据成员。
* 在类加载时，它在主方法之前执行。

但是，我们应当注意的是，当类的第一个对象被创建时，静态块仅且只有被执行一次。从下面例子中说明这种情况：
```
// filename: Main.java 
class Test { 
    static int I; 
    int j; 
    static{ 
        I = 10; 
        System.out.println(“static block called “); 
    } 
    Test(){ 
        System.out.println(“Constructor called”); 
    } 
} 
 
class Main { 
    public static void main(String args[]) { 
       // Although we have two objects, static block is executed only once. 
       Test t1 = new Test(); 
       Test t2 = new Test(); 
    } 
} 
```
**输出结果:**

static block called

Constructor called

Constructor called

## 嵌套类
嵌套类分为两类：静态和非静态。被声明为静态的嵌套类被称为**静态嵌套类**。非静态嵌套类被称为**内部类**。
```
class OuterClass {
    …
    // 静态嵌套类
    static class StaticNestedClass {
        …
    }
    // 内部类
    class InnerClass {
        …
    }
}
```
一个嵌套类是闭合类的成员。非静态嵌套类（内部类）可以访问闭合类的其他成员，即使它们被声明为私有。静态嵌套类不能访问闭合类的其他成员。

作为OuterClass的成员，嵌套类可以被声明为private、public、protected或package private。回想一下，外部类只能被声明为public或package private。

### 为什么使用嵌套类？
使用嵌套类的理由包括以下几点。

**它是一种将只在一个地方使用的类进行逻辑分组的方法**。如果一个类只对另外一个类有用，那么将它嵌入到那个类中，并将两个类放在一起，是符合逻辑的。嵌套这样的 "helper classes "可以使其封装更加精简。

**它增加了封装性**。考虑两个顶层类，A和B，B需要访问A的成员，否则会被声明为私有。通过将B类隐藏在A类中，A的成员可以被声明为私有，B可以访问它们。此外，B本身也可以对外界进行隐藏。

**可以使代码更具可读性和可维护性**。将小类嵌套在顶层类中，使代码更接近使用的地方。

### 静态嵌套类
与类方法和变量一样，静态嵌套类与其外部类相关联。而且和静态类方法一样，静态嵌套类不能直接引用其外部类中定义的实例变量或方法：它只能通过对象引用来使用它们。

静态嵌套类就像其他顶层类一样，与它的外部类（和其他类）的实例成员交互。实际上，静态嵌套类在行为上是一个顶层类，为了封装方便，它被嵌套在另一个顶层类中。

静态嵌套类是使用闭合类名来访问的。

`
OuterClass.StaticNestedClass
`

例如，要为静态嵌套类创建一个对象，则使用下面这种语法。

`
OuterClass.InnerClass innerObject = outerObject.new InnerClass();
`

### 内部类
与实例方法和变量一样，内部类与它的外层类的实例相关联，并且可以直接访问该对象的方法和字段。此外，由于内部类与实例相关联，它本身不能定义任何静态成员。

作为内部类实例的对象存在于外部类的实例之中。

举例说明一下：
```
class OuterClass {
    …
    class InnerClass {
        …
    }
}
```
一个InnerClass的实例只能存在于一个OuterClass的实例中，并且可以直接访问它的封装实例的方法和字段。

要实例化一个内部类，必须先实例化外部类。然后，用下面语法在外层对象中创建内层对象：

`OuterClass.InnerClass innerObject = outerObject.new InnerClass();
`

内部类有两种特殊的类:  [局部类](https://docs.oracle.com/javase/tutorial/java/javaOO/localclasses.html)  和  [匿名类](https://docs.oracle.com/javase/tutorial/java/javaOO/anonymousclasses.html)，就不一一展开讲了。

### 注意事项
* 只有当一个类是一个嵌套类时，它才能成为静态类。
* 静态嵌套类不需要引用外类的内容。
* 静态类不能访问外部类的非静态成员。
* 在Java中，外部类不能是静态的。
