原文：https://aurelia.io/docs/plugins/virtualization


* [1 Introduction \- 简介](#1-introduction---%E7%AE%80%E4%BB%8B)
* [2 Installing The Plugin \- 安装插件](#2-installing-the-plugin---%E5%AE%89%E8%A3%85%E6%8F%92%E4%BB%B6)
* [3 Configuring The Plugin \- 配置插件](#3-configuring-the-plugin---%E9%85%8D%E7%BD%AE%E6%8F%92%E4%BB%B6)
* [4 Using The Plugin \- 使用插件](#4-using-the-plugin---%E4%BD%BF%E7%94%A8%E6%8F%92%E4%BB%B6)
  * [Basic repeat](#basic-repeat)
  * [Unordered list repeat \- 无序列表重复](#unordered-list-repeat---%E6%97%A0%E5%BA%8F%E5%88%97%E8%A1%A8%E9%87%8D%E5%A4%8D)
  * [Table row repeat](#table-row-repeat)
* [5 Infinite Scroll \- 无限滚动](#5-infinite-scroll---%E6%97%A0%E9%99%90%E6%BB%9A%E5%8A%A8)
* [6 Caveats \- 注意事项](#6-caveats---%E6%B3%A8%E6%84%8F%E4%BA%8B%E9%A1%B9)

## 1 Introduction - 简介

在处理大量的项目集合(数千个，甚至数万个)时，无论是数组还是映射，由于DOM的限制，在以高性能的方式显示这些项目时会遇到一些挑战。这就是UI虚拟化插件非常方便的地方。

这个插件通过一个新的虚拟重复属性来实现列表的“虚拟化”。使用时，该列表“几乎”有数万或数十万行，但DOM只有可见的行。这允许以惊人的性能呈现大量的数据列表。它像重复一样工作。因为，它只创建一个滚动区域并使用UI虚拟化技术管理列表。

## 2 Installing The Plugin - 安装插件

如果您正在使用CLI或Webpack，您可以从NPM安装插件：

``` Shell
npm install aurelia-ui-virtualization
```
  
or

``` Shell
yarn add aurelia-ui-virtualization
```

如果您将JSPM用于客户端依赖项，则可以使用此命令:

``` Shell
jspm install aurelia-ui-virtualization
```

  ## 3 Configuring The Plugin - 配置插件

在`src`文件夹中的`main.js` 中，只需使用动画插件的名称调用插件API：

  **main.js**

``` javascript
 import { PLATFORM } from 'aurelia-pal';
  
  export function configure(aurelia) {
    aurelia.use
      .standardConfiguration()
      .developmentLogging()
      .plugin(PLATFORM.moduleName('aurelia-ui-virtualization')); //<-- add this
  
    aurelia.start().then(a => a.setRoot());
  }
```

>警告：仅当您使用**Webpack**时才需要`PLATFORM.moduleName`。
  

  ## 4 Using The Plugin - 使用插件

如果您已经使用了Aurelia的现成的`repeat.for`属性，那么您可能会注意到用户界面虚拟化库提供了它自己类似的名为`virtual-repeat.for` 属性使用了完全相同的语法。

就像标准的`repeat.for`属性一样。`virtual-repeat.for` 属性允许您在视图中重复任何类型的内容。

>警告：重复的行**需要**固定的高度，并且每行只需要一个项目。虚拟化要求所有元素的高度完全相同

### Basic repeat
**app.html**

``` Html
  <template>
    <div virtual-repeat.for="item of items">
      ${$index} ${item}
    </div>
  </template>
```

### Unordered list repeat - 无序列表重复

**app.html**

``` Html
  <template>
    <ul>
      <li virtual-repeat.for="item of items">
        ${$index} ${item}
      </li>
    </ul>
  </template>
```

### Table row repeat
**app.html**

``` Html
  <template>
    <table>
      <tr virtual-repeat.for="item of items">
        <td>${$index}</td>
        <td>${item}</td>
      </tr>
    </table>
  </template>
```

  ## 5 Infinite Scroll - 无限滚动

UI虚拟化插件允许您实际滚动包含许多项的列表，它还提供了无限滚动属性，允许您在用户滚动容器时获取更多的项。`infinite-scroll-next`属性在视图中接受一个回调函数，该函数在触发时接收三个参数。

*   Argument #1 一个整数值，表示当前存在于DOM中呈现的项的顶部。
*   Argument #2 一个布尔值，指示列表是否已滚动到项列表的底部。
*   Argument #3 一个布尔值，指示列表是否已滚动到项列表的顶部。

  **app.html**

``` Html
  <template>
    <div virtual-repeat.for="item of items" infinite-scroll-next="getMore">
      ${$index} ${item}
    </div>
  </template>
```
 
**app.js**

``` javascript
  export class App {
      items = ['Foo', 'Bar', 'Baz'];
  
      getMore(topIndex, isAtBottom, isAtTop) {
          for(let i = 0; i < 100; ++i) {
              this.items.push('item' + i);
          }
      }
  }
```
类似地，`infinite-scroll-next`属性也支持通过`.call`使用表达式

**app.html**

``` Html
  <template>
    <div virtual-repeat.for="item of items" infinite-scroll-next.call="getMore($scrollContext)">
      ${$index} ${item}
    </div>
  </template>
```

**app.js**

``` javascript
  export class App {
      items = ['Foo', 'Bar', 'Baz'];
  
      getMore(scrollContext) {
          for(let i = 0; i < 100; ++i) {
              this.items.push('item' + i);
          }
      }
  }
```

  infinite-scroll-next 属性可以接受函数、承诺或返回承诺的函数。它是相当灵活的方式，它允许您实现无限加载功能到您的Aurelia应用程序。
  
  ## 6 Caveats - 注意事项

1.  `<template></template>`不支持作为虚拟重复模板的根元素，因为虚拟化要求项目高度是可计算的。对于`<template></template>`，没有简单而有效的方法来获取这个值。

2. 与(1)类似，不同于重复，其他模板控制器不能与 `virtual-repeat`一起使用。例如:内置模板控制器：`with`, `if`, `replaceable`不能与`virtual-repeat`一起使用。您可以通过在带有`<template></template>`元素的重复元素中嵌套其他模板控制器来轻松解决此约束，例如：

**app.html**

``` Html
  <template>
    <h1>${message}</h1>
    <div virtual-repeat.for="person of persons">
      <template with.bind="person">
        ${Name}
      </template>
    </div>
  </template>
```
3.  注意CSS选择器`:nth-child`和类似的选择器。虚拟化需要根据滚动位置适当地删除和插入可见项。这意味着DOM元素的顺序将不会保持不变，因此会创建意想不到的`:nth-child`CSS选择器行为。为了解决这个问题，你可以使用上下文属性`$index`, `$odd`, `$even`等…来确定一个项目的位置，并应用CSS类/样式对它，像下面的例子：

``` Html
<template>
    <div virtual-repeat.for="person of persons" class="${$odd ? 'odd' : 'even'}-row">
      ${person.name}
    </div>
  </template>
```

4.   与(3)类似，虚拟化需要适当地删除和插入可见项，在插入(`bind`, `attached`)或删除(`unbind`, `detached`)时也会触发相应的项视图生命周期。

  