---
title: "大话ES6"
date: 2021-01-30T21:47:39+08:00
showDate: true
draft: false
tags: [ES6, Translate]
---

ES6、ES2015或者说Harmony给我们带来了很多有趣的特性，本文将对它所提供的这些新特性进行深入剖析。

<!--more-->

我赞同这种说法，ES6从2015年6月就出来了，从那以后已经写了很多关于新特性的文章，你最好不要再写一篇文章了...但这并不能阻止我写这篇文章。为什么这么说呢？因为，一是尚未完成的本文已经在我的磁盘里躺了相当长一段时间了，二是虽然我看了一些关于这个主题的文章，但自己写一篇文章有助于给这些知识点留下深刻印象。所以，这篇文章不仅是要写给你，同时也是要写给我自己看的。听起来似乎很公平？好吧，那就开始吧。

## 默认为严格模式
* ES5 - 使用 `use strict`来手动启用严格模式；
* ES6 - 默认在模块中启用严格模式。

ES5在Javascript中引入了严格模式，让我们的Javascript代码在一个“严格”的操作环境下执行。当你试图写一些脏代码时，会抛出更多的异常。
举个例子，在严格模式下，你不能使用没有声明的变量。例如`foo = "bar";`，其中`foo`没有声明会运行失败等等。当然，我不会详细介绍`strict`模式，而是向你推荐[这篇关于这个主题的精彩文章](http://ejohn.org/blog/ecmascript-5-strict-mode-json-and-more/)。

ES6在它的模块中默认启用了`strict`模式，也就是说，你不再需要显式地写`use strict`来启用它了。

## 块作用域
* ES5 - 使用`var`声明变量，并具有函数的作用域；
* ES6 - 使用`let/const`声明块作用域变量。

在ES5中，我们只能通过`var`来声明变量，这些变量具有函数的作用域，而另一种使用方式则是不添加`var`，这将使它处于全局作用域。

现在我们又多了两种构造方式，即`let`和`const`，我们可以用它们来声明具有块级作用域的变量，即在大括号`{}`内。`let`和`const`的唯一区别是`const`的值一旦初始化就不能改变，而且必须在声明时初始化`const`变量。

```
// 例子：
// `let` 和 `const` 声明的变量将不能在`if`块之外被访问
function scopeExample(){
    if(true){
        // `action` 和 `act` 只能在`if`块内被访问
        let action ="Some action";
        const act = "Some act";
        
        console.log("Doing: " + action);
        console.log("Now doing: " + act);
    }
    console.log(act); // 未捕获的引用错误："act"未定义。
    console.log(action); // 未捕获的引用错误："action"未定义
}
// 例子：
// `const`定义的变量一旦被赋值，就不能改变
function constExample(){
    const action = "something";
    action = "Updated action"; //未捕获的类型错误：给常量变量赋值
}
```

因此，我们不再需要使用[IIFEs](https://en.wikipedia.org/wiki/Immediately-invoked_function_expression)这种奇怪的语法了， 取而代之的是使用一个块`{}`，使用`let`和`const`定义的变量。

## 模板字符串
以前，当你有一个变量，并想把它的值拼接到一个字符串中时，你必须使用连词操作符来实现，即：

```
var name = 'John';
console.log('Hello ' + name)；
```
在ES6中，我们可以使用模板字符串，即可以这样做：

```
var name = 'John';
console.log(`Hello ${name}`)；
```
需要注意的点是；对于模板字符串，你必须使用反引号``，而且模板字符串中使用的变量必须在使用模板字符串之前声明。

## 箭头函数
箭头函数是匿名函数的一种简略语法，其中没有function关键字，并有隐含的`return`语句。有点绕哈？请看例子：

```
// ES5 例子
var sayHello = function(name){
    return 'Hello ' + name + '!';
};

// ES6 例子
// 箭头函数（一行表示）
var sayHello = name => `Hello ${name}!`;
sayHello('World'); // Hello World

// 在涉及多条语句的情况下，使用块{}
var sayHello = name => {
    // 设置name的默认值
    name = name || 'World';
    return `Hello ${name}!`;
};

sayHello(); // Hello World!
sayHello('Jim'); // Hello Jim!

// 不含参数
var sayHello = () => {
    return 'Hello World!';
};
sayHello(); // Hello World!

// 含有多个参数
var greet = (greeting, name) => `${greeting} ${name}!`;
// 或者使用块{}
var greet = (greeting, name) => {
    return `${greeting} ${name}!`;
};

greet('Hello', 'World'); // Hello World
```

## 解构赋值
如果你写过PHP，你可能知道一种叫做`list`的数据结构，现在在Javascript中也可以实现类似的功能。请看例子：

应用于解构数组：

```
var parts = '2010-11-11'.split('-'),
    year = parts[0],
    month = parts[1],
    day = parts[2];

console.log()
```
在ES6中一行表示：

```
var [year, month, day] = '2020-10-11.split('-');
console.log(year); // 2010
console.log(month); // 10
console.log(day); // 11
```
也可以忽略不感兴趣的值：

```
// 只取month和day的值
var [, month, day] = '2010-10-11'.split('-');
console.log(month); // 10
console.log(day); // 11

// 只取year和day的值
var [year, , day] = '2010-10-11'.split('-');
console.log(year); // 2010
console.log(day); // 11
```
为了防止从数组中取出一个值为undefined的对象，可以在表达式左边的数组中为任意对象预设默认值。例如：

```
// 假设日期是可选的，如果没有找到，那么我们使用`1`代替
var [year, month, date=1] = '2010-10'.split('-');
console.log(year); // 2010
console.log(month); // 10
console.log(date); // 1

var [year, month, date=1] = '2010-10-11'.split('-');
console.log(year); // 2010
console.log(month); // 10
console.log(date); // 11
```

应用于解构对象：

```
var person = {name: 'John Doe', age: 23, gender: 'male'};
var {name, age, gender} = person;
console.log(name); // John Doe
console.log(age); // 23
console.log(gender); // male
```
从上面的例子中，你一定注意到了我在`var {name, age, gender}`中为属性使用了相同的变量名，即`name`, `age`和`gender`。但是，如果我想把`person.name`存储在`tag`中，把`person.gender`存储在`sex`中呢？那么在这种情况下，自然而然会使用到别名：

```
var person = {name : 'John Doe', age: 23, gender: 'male'};
var {name:tag, age, gender:sex} = person;
console.log(tag); // John Doe
console.log(age); // 23
console.log(sex); // male
```
之前提到，对象也可以有默认值。例如，如果你想获得默认性别为`male`，即如果性别不存在，就使用`male`。可以通过以下方式实现：

```
var person = {name: 'John Doe', age: 23};
var {name, age, gender='male'} = person;
console.log(name); // John Doe
console.log(age); // 23
console.log(gender); // male

// 别名和默认值可以同时使用
var person = {name: 'John Doe', age: 23};
var {name, age, gender:sex = 'male'} = person;
console.log(name); // John Doe
console.log(age); // 23
console.log(sex); // male
```
这还有一些特殊的例子：

```
// 从函数调用中返回对象/多个值
function getLocation(){
    return {
    latitude: 23.4125,
    longitude: 45.128
    };
}

var {latitude:lat, longitude:long} = getLocation();
console.log(lat); // 23.4125
console.log(long); // 45.128
```
查看[这个要点有很多例子](https://gist.github.com/mikaelbr/9900818)。

## 强大的`for...of`
以前迭代数组，可能会使用`for`或`forEach`。`for`可以跳出循环，但不够简洁，而`forEach`不允许你跳出循环，但足够简洁。现在`for...of`既满足简洁又支持跳出循环。下面看看如何使用它来迭代数组：

```
var numbers = [10, 20, 30, 40, 50, 60, 70, 80, 90];
for(let number of numbers) {
    console.log(number);
}

// 跳出循环
var numbers = [10, 20, 30, 40, 50, 60, 70, 80, 90];
for(let number of numbers){
    if (number === 50) {
        break;
    }
    console.log(number);
}
```
举一个使用解构赋值和`for...of`的例子

```
var guests = [{name: 'John Doe', title: 'Mr'}, {name: 'Jane                 Doe', title: 'Ms'}];
for(let {name} of guests) {
    console.log('Hello ' + name + '!');
}

// Hello John Doe!
// Hello Jane Doe!
```

## 默认参数值
在ES6中，我们可以设置默认的参数值。例如之前得这样子实现：

```
function greet(greet, name){
    greet = greet || 'Hi';
    name = name || 'John Doe';
    
    console.log(greet + ' ' + name); 
}
```
现在，我们有更简单的方式：

```
function greet(greet='Hi', name='John Doe'){
    console.log(greet + ' ' + name);
}
```
另外，与第一个例子不同的是，在第一个例子中，默认值会被赋值给任何为false的值，而后一个例子则会给某些未定义参数赋值。此外，默认值也适用于对象参数。

## 展开操作符`...`
以前当你需要一个未知数量的参数时，往往会使用`arguments`这样的特殊的变量。
```
function populateBucket(){
    var bucket = [];
    for(var itemCounter = 0; itemCounter < arguments.length; itemCounter++){
        bucket.push(arguments[itemCounter]);
    }
}
```
你可能已经注意到，这已经开始变得混乱了。在ES6中，您可以使用展开操作符。即：

```
function populateBucket(...items){
    var bucket = [];
    for(item of items){
        bucket.push(item);
    }
}
```
展开操作符的另一用法是合并数组：

```
var a = [1, 2, 3],
    b = [4, 5, 6],
    merged = [];
    
// 以前合并数组的方法
merged = a.concat(b);

// 现在更简洁：
merged = [...a, ...b];
```

## 类
还记得我们以前是如何使用构造函数来创建类的吗？ES6引入了一些语法糖来让它变得更加愉快。现在创建类已经非常方便了：

```
class Person{
    // 当初始化person实例的时候会被调用
    constructor(name){
        this.name = name;
        console.log('A person named "' + name + '" is born');
    }
    
    // 不再需要 function 关键字
    sayHello(){
        console.log(this.name + ' says hello'};
}
```
你也可以继承类而不用接触`prototype`了：

```
class Employee extends Person{
    constructor(name){
        super(name);
        console.log(this.name + ' has been employed');
    }
}
```
## Maps
引入了一种新的数据结构，称为map，它使用键值对这样的方式存储数据。

```
var map = new Map();
map.set('spec', '2015');
map.set('year', '2015');

// 使用`.get()`来查询键值对
console.log(map.get('spec')); // 2015
```
当然，你也可以迭代maps：

```
for(var [key, value] of map) {
    console.log(key + ': ' + value);
}
```

此外，还有一些辅助函数来操作maps。

```
map.entries(); // 返回 map 中所有的键值对
map.keys(); // 返回 map 中所有的键
map.values(); // 获取存储在 map 中的值
map.has(keyName); // 检查 map 中是否有指定的键
map.delete(keyName); // 替换对应键的值
map.size; // 返回 map 的大小
map.clear(); // 清除所有的集合
```

## WeakMaps
和`Map`一样，`WeakMap`是一个键/值对的集合，其中键必须是对象，或者换句话说，它们必须是引用类型，而不是值类型，如numbers、symbols或strings等，值可以是任意值。如果`WeakMap`中没有存储键的其他引用，它们会触发垃圾回收。这意味着它们很适合在对象还在使用时为其保留元数据。让我们来看看我从[mozilla JS引用中偷来的例子](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/WeakMap)：

```
var wm1 = new WeakMap(),
    wm2 = new WeakMap(),
    wm3 = new WeakMap();
var o1 = {},
    o2 = function(){},
    o3 = window;

wm1.set(o1, 37);
wm1.set(o2, "azerty");
wm2.set(o1, o2); // 值可以是任何东西， 包括对象或者函数
wm2.set(o3, undefined);
wm2.set(wm1, wm2); // 键和值可以是任何对象，甚至是WeakMaps！

wm1.get(o2); // “azerty”
wm2.get(o2); // undefined, 因为wm2中的o2没有值
wm2.get(o3); // undefined， 因为这是设定的值

wm1.has(o2); // true
wm2.has(o2); // false
wm2.has(o3); // true (即使该值本身是 "undefined"。)

wm3.set(o1, 37);
wm3.get(o1); // 37

wm1.has(o1); // true
wm1.delete(o1);
wm1.has(o1); // false
```

## Sets
Sets是存储唯一值的集合。任何重复的值都将被忽略。这些值也可以是原始数据或对象引用。

```
let set = new Set();
set.add(1);
set.add('1');
set.add({key: 'value'});
console.log(set); // Set {1, '1', Object {key: 'value'}}

// 删除值
set.delete(2);

// 检查大小
console.log(set.size);
```

像map一样，set允许通过向其构造函数传递一个数组来创建集合:

```
let set = new Set([1, '1', {key: 'value' }]);
console.log(set); // Set {1, '1', Object {key: 'value'}}
```
为了迭代sets，我们有两个选项--内置的forEach函数或for..of结构。

```
// forEach
let set = new Set([1, '1', {key: 'value' }]);
set.forEach(function (value) {
    console.log(value);
    // 1
    // '1'
    // Object {key: 'value'}
});

// for...of
let set = new Set([1, '1', {key: 'value' }]);
for(let value of set){
    console.log(value);
    // 1
    // '1'
    // Object {key: 'value'}
};
```
## Weaksets
与`Set`可以是任何东西的集合不同，`WeakSet`只是对象的集合，而不是任何类型的任意值的集合。`WeakSet`是弱引用的：对集合中的对象的引用是弱引用的。如果`WeakSet`中存储的对象没有其他引用，它们就触发垃圾回收。另外，`WeakSets`不是可枚举的，所以不能使用枚举方法，比如`.forEach`，`.clear`等。

```
var ws = new weakSet();
var obj = {};
var foo = {};

ws.add(window);
ws.add(obj);

ws.has(window); // true
ws.has(foo); // false, set中没有foo变量

ws.delete(window); // 从set中删除window
ws.has(window); // false, window 已经被删除了
```

## 新的字符串函数
引入了一些新的字符串方法，让我们的生活变得更轻松。还记得我们以前是如何使用`indexOf`来检查一个字符串是否存在于另一个字符串中，或者检查一个字符串是否以一个特定的字符开始/结束的吗？

```
var message = 'Hello World';
message.startsWith('H'); // 而不是 message.indexOf('H') === 0
message.endsWith('d'); // 而不是其他什么 ¯\_(ツ)_/¯

message.includes('or'); //而不是 message.indexOf('or') >= 0
```
此外，还有一个用于重复字符`n`次的辅助函数。

```
// 之前得要这样做
new Array(3+1) * '*'

// 现在很简单就能实现
'*'.repeat(3); // 将会生成 ***
```

## 新的数组函数
引入了一些新的数组函数。`Array.from`可以让你从类似数组的对象中创建数组。比如说：

```
// 类似数组的对象（参数）转换成数组
function fooBar(){
    return Array.from(arguments);
}

fooBar(1, 2, 3, 4, 5); // [1, 2, 3, 4, 5];

// 任何可迭代的对象
// Set
var s = new Set(['foo', window]);
Array.from(s);
// ['foo', window]

// Map
var m = new Map([[1, 2], [2, 4], [4, 8]]);
Array.from(m);
// [[1, 2], [2, 4], [4, 8]]

// 使用一个箭头函数作为map函数来操作元素
Array.from([1, 2, 3], x => x + x);
// [2, 4, 6]
```
`Array.fill`允许你用传递的元素替换一个数组中的所有元素。

```
var randomNumbers = [1, 5, 7, 77, 12, 3];
randomNumbers.fill('*'); // ['*', '*', '*','*', '*', '*']
```
`Array.find`接收一个回调函数，并返回满足回调函数中条件的第一个元素。

```
var users = [{name: 'John Doe', age: 23}, {name: 'Jane Doe', age: 23}, {name: 'Kane Doe', age: 30}];
users.find(user => user.age > 25); // {name: 'Kane Doe', age:30}
```
还有一个类似的函数是Array.findIndex，它的工作原理是一样的，但返回匹配元素的索引。

## 模块
ES缺少原生模块支持已经有很长一段时间了。人们想出了AMD、CommonJS或模块化模式等类似的办法来解决。而ES6现在终于有了原生模块的支持。让我们先简单了解一下。

默认情况下，在文件中声明的任何东西都不会在文件外部可见，除非你使用`export`。

以下面的`User`模块为例

```
// user.js

var localVariable = 123; // 在文件外部不可见

export default function User(age){
    this.age = age;
}; // 可以被其他文件作为模块导入
```
现在我们希望使用User模块：

```
// user-details.js
import User from 'user';

var user = new User(24);
```
还有很多的内容。更多详情请看[这篇文章](https://ponyfoo.com/articles/es6-modules-in-depth)。

## 说在最后
虽然ES6已经发布了，但在浏览器中对它的支持仍然悬而未决。但是，现在有[大量的工具](https://github.com/addyosmani/es6-tools)可以帮助我们使用ES6。最流行的是[BabelJS](https://babeljs.io/)，它既可以作为一个独立的工具运行，也可以和你的构建系统一起使用。他们有针对Grunt、Gulp等的[插件](http://babeljs.io/docs/setup/)。

好了，伙计们，差不多就到此为止了。还有一些值得注意的、让人心动的特性，这里没有提到 Generators、Promises、Proxy 和 Symbols 等等。但是，本文所涉及的东西已经足够让你入门了。不过，如果可以的话，我会强烈推荐[这一系列的文章](https://ponyfoo.com/articles/tagged/es6-in-depth)和[这本书](https://leanpub.com/understandinges6)。而[规范](http://wiki.ecmascript.org/doku.php?id=harmony:specification_drafts)也许是为超前者准备的。

欢迎大家提出建议。

> 本文翻译自 [ES6 Succinctly](https://kamranahmed.info/blog/2016/04/04/es6-in-depth/)
