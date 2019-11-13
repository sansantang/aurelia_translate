原文：https://aurelia.io/docs/templating/basics

* [1.介绍](#%E4%BB%8B%E7%BB%8D)
* [2.一个简单的模板](#%E4%B8%80%E4%B8%AA%E7%AE%80%E5%8D%95%E7%9A%84%E6%A8%A1%E6%9D%BF)
* [3.Binding](#binding)
  * [Binding Focus 绑定焦点](#binding-focus-%E7%BB%91%E5%AE%9A%E7%84%A6%E7%82%B9)
  * [使用with绑定范围](#%E4%BD%BF%E7%94%A8with%E7%BB%91%E5%AE%9A%E8%8C%83%E5%9B%B4)
* [4.Composition 任意组合](#composition-%E4%BB%BB%E6%84%8F%E7%BB%84%E5%90%88)
* [5.as\-element 属性](#as-element-%E5%B1%9E%E6%80%A7)
* [6.The View Resource Pipeline 查看资源管道](#the-view-resource-pipeline-%E6%9F%A5%E7%9C%8B%E8%B5%84%E6%BA%90%E7%AE%A1%E9%81%93)
* [7.View and Compilation Spies  调试资源管道新视图](#view-and-compilation-spies--%E8%B0%83%E8%AF%95%E8%B5%84%E6%BA%90%E7%AE%A1%E9%81%93%E6%96%B0%E8%A7%86%E5%9B%BE)
* [8.Conditionals 条件语](#conditionals-%E6%9D%A1%E4%BB%B6%E8%AF%AD)
  * [Caching the view instance when condition changes 在条件更改时缓存视图实例](#caching-the-view-instance-when-condition-changes-%E5%9C%A8%E6%9D%A1%E4%BB%B6%E6%9B%B4%E6%94%B9%E6%97%B6%E7%BC%93%E5%AD%98%E8%A7%86%E5%9B%BE%E5%AE%9E%E4%BE%8B) 
* [9.Repeaters 重复器](#repeaters-%E9%87%8D%E5%A4%8D%E5%99%A8)
  * [Arrays](#arrays)
  * [Range](#range)
  * [Sets](#sets)
  * [Map](#map)
  * [Objects](#objects)

## 介绍
Aurelia的模板系统简单易学，但功能强大，甚至可以构建最复杂的应用程序。本文将介绍静态模板的构造，将该模板导入父模板，通过视图模型绑定和操作数据，以及条件、重复器和事件的使用。

## 一个简单的模板
所有Aurelia模板必须用`<template>`元素包装。最基本的模板是打印“Hello World”的组件

**hello-world.html**

``` HTML
  <template>
    <p>
      Hello, World!
    </p>
  </template>
```

这个模板可以是Aurelia应用程序中的一个页面，也可以是自定义元素的视图。只包含样板文件的模板可以用作包含版权信息或免责声明的页脚，但是静态模板的乐趣在哪里呢?

所有的Aurelia模板都使用视图模型，所以让我们创建一个:

**hello.js**
   

``` javascript
  export class Hello {
    constructor() {
      this.name = 'John Doe';
    }
  }
```
让我们使用Aurelia的字符串插值将视图模型中的`name`属性绑定到模板中
  
  **hello.html**

``` HTML
  <template>
    <p>
      Hello, ${name}!
    </p>
  </template>
```

  Aurelia模板系统的一个关键特性是帮助减少javascript代码和模板标记之间的上下文切换。使用`${}`操作符进行字符串插值是ES2015中的一个新特性，它使向字符串插入值变得简单。因此，Aurelia在模板中使用这个标准语法。

  运行此模板时，Aurelia将把`name`属性的值插入出现`${name}`的模板中。很简单,对吧?但如果我们想在字符串插值中加入逻辑呢。我们可以添加自己的表达式吗?绝对!

**greeter.html**

``` HTML
  <template>
    <p>
      ${arriving ? 'Hello' : 'Goodbye'}, ${name}!
    </p>
  </template>
```

**greeter.js**
 

``` javascript
export class Greeter {
    constructor() {
      this.name = 'John Doe';
      this.arriving = true;
      setTimeout(
        () => this.arriving = false,
        5000);
    }
  }
```

  在我们的模板中，当`arriving` 为true时，三元运算符使我们的结果为'Hello'，但是当它为false时，它使我们的结果为`Goodbye`。我们的视图模型代码初始化`arriving`为`true`，并在5秒(5000毫秒)后将其更改为`false`。因此，当我们运行模板时，它会说“Hello, John Doe!”5秒后，它会说“Goodbye, John Doe!”当`arriving`值发生变化时，Aurelia重新计算字符串插值!

  但别担心，没有肮脏检查。Aurelia使用了一个基于可观察的绑定系统，该系统无需进行脏检查就可以对发生的更改做出反应。这意味着，当您向模板和视图模型添加更复杂的功能时，Aurelia不会变慢。
  
  ## Binding
  在Aurelia中绑定允许来自视图模型的数据驱动模板行为。绑定最基本的例子是使用`value.bind`将文本框链接到视图模型。如果我们让用户决定他们想要问候谁，以及是说你好还是说再见呢?
  
  **greeter.html**

``` HTML
  <template>
    <label for="nameField">
      Who to greet?
    </label>
    <input type="text" value.bind="name" id="nameField">
    <label for="arrivingBox">
      Arriving?
    </label>
    <input type="checkbox" checked.bind="arriving" id="arrivingBox">
    <p>
      ${arriving ? 'Hello' : 'Goodbye'}, ${name}!
    </p>
  </template>
  
```

  
**greeter.js**

``` javascript
  export class Greeter {
    constructor() {
      this.name = 'John Doe';
      this.arriving = true;
    }
  }
```
上面，我们有一个文本框，询问要打招呼的人的名字，以及一个复选框，指示他们是否到达。因为我们在视图模型中将`name`定义为“John Doe”，文本框的初始值将为“John Doe”，当`arriving`值设置为`true`时，复选框将开始选中。当我们改变名字时，我们打招呼的人也会随之改变:“你好，简·多伊!”如果我们不勾选复选框，问候语就会改变:"Goodbye, Jane Doe"。
  
注意，我们设置绑定的方法是使用`value.bind`和`checked.bind`。那`.`在属性中很重要:每当您看到`.`时，Aurelia将对该属性做一些事情。从本节中最重要的是要理解Aurelia将属性链接到`.`动作在`.`的右边。

您可以在我们的文档的绑定部分了解更多关于数据绑定的信息。


### Binding Focus 绑定焦点

我们还可以使用双向数据绑定来通信元素是否具有焦点

**bind-focus.html**

``` HTML
  <template>
    <input focus.bind="hasFocus">
    ${hasFocus}
  </template>
```
当我们点击输入字段，我们看到“true”打印出来。当我们点击其他地方时，它会变成“false”。

###   使用`with`绑定范围

我们还可以声明标记的某些部分将引用视图模型中对象的属性，如下所示:

**bind-with.html**


``` HTML
  <template>
    <p with.bind="first">
      <input type="text" value.bind="message">
    </p>
    <p with.bind="second">
      <input type="text" value.bind="message">
    </p>
  </template>
```

**bind-with.js**


``` javascript
  export class Hello {
    constructor() {
      this.first = {
        message : 'Hello'
      };
      this.second = {
        message : 'Goodbye'
      }
    }
  }
```
使用`with`基本上是“我正在处理这个对象的属性”的缩写，它允许您在必要时重用代码。
  
## Composition 任意组合

In order to live by the DRY (Don't Repeat Yourself) Principle, we don't necessarily want to rely on tight coupling between our view and view-model pairs. Wouldn't it be great if there was a custom element that would arbitrarily combine an HTML template, a view-model, and maybe even some initialization data for us? As it turns out, we're in luck:

为了遵循DRY(不要重复)原则，我们不必依赖view和view-model对之间的紧密耦合。如果有一个自定义元素可以任意组合HTML模板、视图模型，甚至为我们组合一些初始化数据，那不是很好吗?事实证明，我们很幸运:
  
**compose-template.html**
    
``` HTML
  <template>
    <compose view-model="hello"
              view="./hello.html"
              model.bind="{ target : 'World' }" ></compose>
  </template>
  
```
  
**hello.html**

``` HTML
  <template>
    Hello, ${friend}!
  </template>
```

**hello.js**

``` JavaScript
  export class Hello {
    activate(model) {
      this.friend = model.target;
    }
  }
```

注意，我们组合成的视图模型有一个`activate`方法。当我们使用`model.bind`时，将传递内容以`activate`。然后从传递的模型中提取所需的确切值并分配它。

## as-element 属性

在某些情况下，特别是在使用Aurelia自定义元素创建表行时，可能需要将自定义元素伪装成标准HTML元素。例如，如果要用数据填充表行，可能需要将自定义元素显示为`<tr>`行或`<td>`单元格。这就是`as-element`属性派上用场的地方:
  
**as-element.html**

``` HTML
  <template>
    <require from="./hello-row.html"></require>
    <table>
      <tr as-element="hello-row">
    </table>
  </template>
```

**hello-row.html**

``` HTML
  <template>
    <td>Hello</td>
    <td>World</td>
  </template>
```

`as-element`属性告诉Aurelia，我们希望表行内容恰好是`hello-row`模板包装的内容。不同浏览器呈现表的方式意味着有时可能需要这样做。

## The View Resource Pipeline 查看资源管道

View Resource Pipeline背后的基本思想是我们不仅限于HTML或JavaScript。 一个基本的例子就是拉入Bootstrap：

**hello.html**    

``` HTML
  <template>
    <require from="bootstrap/css/bootstrap.min.css"></require>
    <p class="lead">Hello, World!</p>
    <p>Lorem Ipsum...</p>
  </template>
```
这里，`<require>`标记使用的是CSS文件，而不是html或视图模型。视图资源管道是Aurelia的一部分，负责识别它是CSS，并适当地处理它。Aurelia最强大的特性之一是视图资源管道是完全可扩展的，允许您为任何类型的视图资源定义自己的处理程序.
  

## View and Compilation Spies  调试资源管道新视图

如果已经安装了 `aurelia-testing`插件，就可以访问两个特殊的模板行为:
**hello.html**

``` HTML
  <template>
    <p view-spy compile-spy>Hello!</p>
  </template>
```

  `view-spy`将视图对象的Aurelia副本放入控制台，而`compile-spy`发出编译器的目标指令。这对于调试使用视图资源管道创建的任何新视图资源尤其有用。

## Conditionals 条件语

Aurelia有两个主要的条件显示工具:`if`和`show`。区别在于，`if`从DOM中完全删除元素，并切`show`换控制元素可见性的`aurelia-hide` CSS类。

在速度和可用性方面，差别是细微的，但很重要。当 `if`中的状态发生变化时，模板及其所有子模板都将从DOM中删除，如果反复执行该操作，其计算开销将非常大。但是，如果`show`用于一个非常大的模板，例如一个包含数千个元素的仪表盘，其中包含它们自己绑定的数据，那么保持这些元素加载但隐藏可能最终不是一个有用的方法。

这是我们的基本“Hello World”，询问用户是否想先问候世界:

**if-template.html**

``` HTML
  <template>
    <label for="greet">Would you like me to greet the world?</label>
    <input type="checkbox" id="greet" checked.bind="greet">
    <div if.bind="greet">
      Hello, World!
    </div>
  </template>
```
但是，如果我们只想对视图隐藏元素而不是将其从DOM中完全删除，则应该使用`show`而不是`if`。
  
**show-template.html**

``` HTML
  <template>
    <label for="greet">Would you like me to greet the world?</label>
    <input type="checkbox" id="greet" checked.bind="greet">
    <div show.bind="greet">
      Hello, World!
    </div>
  </template>
```
未选中时，我们的“Hello World”div将有一个`aurelia -hide`类，它设置`display: none`(如果您正在使用Aurelia的CSS库)。但是，如果不希望这样做，还可以定义自己的CSS规则，以不同的方式处理`aurelia-hide`，比如设置`visibility: none`或`height: 0px`。
  
  条件句也可以是一次性绑定的，这样模板的某些部分在实例化时是固定的:
  
    
**conditional-one-time-template.html**

``` HTML
  <template>
    <div if.one-time="greet">
      Hello, World!
    </div>
    <div if.one-time="!greet">
      Some other time.
    </div>
  </template>
```

**bind-template.js**

``` javascript
  export class ConditionalOneTimeTemplate {
    greet = (Math.random() > 0.5);
  }
```
我们有一半的机会迎接这个世界，或者把它推迟到以后。一旦页面加载，这是固定的，因为数据是一次性绑定的。为什么不用`show.one-time`呢?如果我们思考一下`show`的作用，就会发现它并没有什么意义。我们想要应用一个CSS类来隐藏一个元素，并且它永远不会改变。在大多数情况下，我们希望`if`拒绝创建一个我们永远不会使用的元素。

  除了`if`，还有`else`的。如果与`if`一起使用，`else`将在`if`计算值不为true时呈现其内容。

**if-else-template.html**

``` HTML
  <template>
    <div if.bind="showMessage">
      <span>${message}</span>
    </div>
    <div else>
      <span>Nothing to see here</span>
    </div>
  </template>
```

使用`else`模板修饰符的元素必须跟随if绑定元素，以使上下文有意义并正常工作。

### Caching the view instance when condition changes 在条件更改时缓存视图实例

默认情况下，`if`缓存底层视图模型实例。尽管元素被从DOM中删除，组件经历`detached` 和 `unbind`绑定的lifecyle事件，但是它的实例被保存在内存中，以备进一步的条件更改。因此，当元素被隐藏并再次显示时，如果不需要再次初始化组件。

通过显式设置`if` 指令的 `cache`绑定，可以选择不执行此默认行为。当对自定义元素使用`if`并在每个外观上初始化它们时，这尤其有用。

  **if-template-without-cache.html**


``` HTML
  <template>
    <div if="condition.bind: showMessage; cache: false">
      <span>${message}</span>
    </div>
  </template>
```

## Repeaters 重复器

中继器可以用于任何元素，包括自定义元素和模板元素!下面是一些可以用中继器迭代的不同数据类型。

### Arrays

Aurelia还能够为数组中的每个元素重复元素。

**repeater-template.html**

``` HTML
  <template>
    <p repeat.for="friend of friends">Hello, ${friend}!</p>
  </template>
```
  
**repeater-template.js**

``` javascript
  export class RepeaterTemplate {
    constructor() {
      this.friends = [
        'Alice',
        'Bob',
        'Carol',
        'Dana'
      ];
    }
  }
```
这让我可以列出我的朋友，并一一问候他们，而不是试图一次问候世界上所有70亿居民。
如前所述，我们还可以使用template元素作为我们的中继器——但是我们必须将它包装在代理`<template>`元素中.
  
  **repeater-template.html**

``` HTML
  <template>
    <template repeat.for="friend of friends">
      <p>Hello, ${friend}!</p>
    </template>
  </template>
```

>Aurelia将无法使用`array[index] = value`语法观察数组的更改。为了确保Aurelia能够观察到数组的变化，可以使用数组方法:`Array.prototype.push`,`Array.prototype.pop`和`Array.prototype.splice`。

  由于javascript中`for-of`循环的性质，与数组的双向绑定需要特殊的语法。不要`repeat.for="item of dataArray"`；这样做只会导致单向绑定——输入的值不会被绑定回来。而是使用以下语法

**repeater-template-input.html**

    

``` HTML
  <template>
    <div repeat.for="i of dataArray.length">
    <input type="text" value.bind="$parent.dataArray[i]">
    </div>
  </template>
```
### Range

我们也可以遍历数值范围：
  
**repeater-template.html**

    

``` HTML
  <template>
    <p repeat.for="i of 10">${10-i}</p>
    <p>Blast off!</p>
  </template>
```
注意范围将从0开始，长度为10，所以我们的倒计时确实从10开始，在发射前结束于1。
  

  ### Sets

我也可以用同样的方法使用`ES6`集：

**repeater-template.html**

``` HTML
  <template>
    <p repeat.for="friend of friends">Hello, ${friend}!</p>
  </template>
```

**repeater-template.js**

    

``` JavaScript
  export class RepeaterTemplate {
    constructor() {
      this.friends = new Set();
      this.friends.add('Alice');
      this.friends.add('Bob');
      this.friends.add('Carol');
      this.friends.add('Dana');
    }
  }
```

  我们可以将中继器与数组一起使用，这很有用——但是我们也可以将中继器与其他可迭代数据类型(包括对象)以及新的ES6标准(如Map和Set)一起使用。

### Map

其中一个比较有用的迭代器是Map，因为您可以在repeater中直接将键和值分解为两个变量。虽然您可以直接对对象进行重复，但是映射可以是双向绑定，比对象要简单得多，所以您应该尽可能地使用映射。

**repeater-template.html**

    

``` HTML
  <template>
    <p repeat.for="[greeting, friend] of friends">${greeting}, ${friend.name}!</p>
  </template>
```

  

  
**repeater-template.js**

    

``` JavaScript
  export class RepeaterTemplate {
    constructor() {
      this.friends = new Map();
      this.friends.set('Hello',
        { name : 'Alice' });
      this.friends.set('Hola',
        { name : 'Bob' });
      this.friends.set('Ni Hao',
        { name : 'Carol' });
      this.friends.set('Molo',
        { name : 'Dana' });
    }
  }
```

在上面的示例中需要注意的一件事是`[greeting, friend]`中的取消引用操作符——它将映射的键-值对分解为`greeting`(键)和`friend`(值)。注意，因为我们所有的值都是具有`name`属性集的对象，所以可以使用`${friend.name}`获取朋友的名字，就像从JavaScript中获取一样
  

###  Objects
让我们做同样的事情，除了在view-model中使用传统的JavaScript对象:

**repeater-template.html**

    

``` HTML
  <template>
    <p repeat.for="greeting of friends | keys">${greeting}, ${friends[greeting].name}!</p>
  </template>
```

  

  
**repeater-template.js**

    

``` javascript
  export class RepeaterTemplate {
    constructor() {
      this.friends = {
        'Hello':
          { name : 'Alice' },
        'Hola':
          { name : 'Bob' },
        'Ni Hao':
          { name : 'Carol' },
        'Molo':
          { name : 'Dana' }
      }
    }
  }
  
  export class KeysValueConverter {
    toView(obj) {
      return Reflect.ownKeys(obj);
    }
  }
```

  我们刚刚介绍了一个叫做“值转换器”的东西。基本上，我们接受视图模型中的对象`friends`，并通过键`keys`值转换器运行它。Aurelia寻找一个名为`KeysValueConverter`的类，并试图用`friends`对象调用它的`toView()`方法。该方法返回一个键数组——我们可以迭代该数组。在紧要关头，我们可以使用它来遍历对象。
  
  一个常见的问题出现在这里:为什么我们不能像处理映射那样将对象解引用到`[key, value]`中呢?简而言之，JavaScript对象不能像数组、映射和集合那样进行迭代。因此，为了遍历JavaScript对象，我们必须将它们转换为可迭代的对象。您处理它的方式将根据您想要对该对象做什么而改变。还可以包含一个[plugin](https://github.com/martingust/aurelia-repeat-strategies)，它将对象转换为可迭代映射，可以使用`[key, value]`语法取消引用。

  