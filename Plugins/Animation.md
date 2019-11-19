原文：https://aurelia.io/docs/plugins/animation

## 1 Introduction - 简介

动画是我们将应用程序带入生活的方式。动画让一个元素从一种风格逐渐转变为另一种风格，使它能够随着时间的推移平滑地改变其大小、颜色等。

为Aurelia构建动画支持的一个关键目标是提供一个灵活的解决方案，允许您选择自己喜欢的动画库。因此，对于如何实现动画，您既不局限于专用API，也不局限于特定的样式。为了实现这种灵活性，Aurelia的动画系统围绕[一个简单的动画接口](https://github.com/aurelia/templating/blob/master/src/animator.js)构建，该接口是其模板库的一部分。

在这篇文章中，我们将介绍官方的CSS动画插件Aurelia, `aurelia-animator-css`。这个插件是上述接口的一个具体实现。然而，如果你喜欢Velocity库，你也可以使用我们官方的`aurelia-animator-velocity`插件，或者你可以基于上面的接口编写自己的插件，例如，如果你喜欢使用 Greensock 之类的东西。

任何类型的动画都可以应用到元素中，但在本文中，我们将通过使用简单的CSS动画演示常见的情况;主要是使用路由器在视图之间导航时的动画，以及对中继器（repeater）的传入和传出元素进行动画。


## 2 Installing The Plugin - 安装插件

如果您正在使用CLI或Webpack，您可以从NPM安装插件：

``` Shell
  npm install aurelia-animator-css
```
or

``` Shell
  yarn add aurelia-animator-css
```
如果您将JSPM用于客户端依赖项，则可以使用此命令:

``` Shell
  jspm install aurelia-animator-css
```

>提示：如果你用**Aurelia CLI**创建了你的应用程序，那么很有可能你已经将插件作为依赖项安装了。
  
>警告：.如果你使用旧版本的**Aurelia CLI**，在1.0之前，带有RequireJS/SystemJS加载器，您应该在`aurelia.json`文件中捆绑包的依赖项部分中添加`aurelia-animator-css`。

## 3 Configuring The Plugin - 配置插件

在`src`文件夹中的 `main.js`中，只需使用动画插件的名称调用插件API：

**main.js** 

``` javascript
  import {PLATFORM} from 'aurelia-pal';
  
  export function configure(aurelia) {
    aurelia.use
      .standardConfiguration()
      .developmentLogging()
      .plugin(PLATFORM.moduleName("aurelia-animator-css")); //<-- add this
  
    aurelia.start().then(a => a.setRoot());
  }
```

>警告：仅当您使用 Webpack 时才需要`PLATFORM.moduleName`。

## 4 Demo

在我们开始设置动画之前，先来看看我们要构建的演示。

[示例](https://codesandbox.io/s/x93zy0m8mp?from-embed)

  ## 5 Using The Plugin - 使用插件

首先，我们需要声明一些`keyframes`（关键帧）动画，稍后可以将它们挂接到元素上。

>警告：如果您的目标是旧的浏览器，不要忘记添加适当的供应商前缀。

**animations.css**
 
``` css
@keyframes SlideInRight {
    0% {
      transform: translateX(100%);
    }
  
    100% {
      transform: translateX(0);
    }
  }
  
  @keyframes SlideOutRight {
    0% {
      transform: translateX(0);
    }
  
    100% {
      transform: translateX(100%);
    }
  }
  
  @keyframes SlideInLeft {
    0% {
      transform: translateX(-100%);
    }
  
    100% {
      transform: translateX(0);
    }
  }
  
  @keyframes SlideOutLeft {
    0% {
      transform: translateX(0);
    }
  
    100% {
      transform: translateX(-100%);
    }
  }
  
  @keyframes FadeIn {
    0% {
      opacity: 0;
    }
  
    100% {
      opacity: 1;
    }
  }
  
  @keyframes FadeOut {
    0% {
      opacity: 1;
    }
  
    100% {
      opacity: 0;
    }
  }
```

  这些都是非常典型的标准CSS动画。没有什么特别值得注意的。你可能不需要所有的，或者你可以根据你的需要添加新的，但它们应该给你一个坚实的开始。

现在我们还需要使用这些动画的CSS类，这样我们就可以在元素上添加这些类来激活动画。

  **animations.css**

``` css
 .animate-slide-in-right.au-enter {
    transform: translateX(100%);
  }
  
  .animate-slide-in-right.au-enter-active {
    animation: SlideInRight 1s;
  }
  
  .animate-slide-out-right.au-leave-active {
    animation: SlideOutRight 1s;
  }
  
  .animate-slide-in-left.au-enter {
    transform: translateX(-100%);
  }
  
  .animate-slide-in-left.au-enter-active {
    animation: SlideInLeft 1s;
  }
  
  .animate-slide-out-left.au-leave-active {
    animation: SlideOutLeft 1s;
  }
  
  .animate-fade-in.au-enter {
    opacity: 0;
  }
  
  .animate-fade-in.au-enter-active {
    animation: FadeIn 1s;
  }
  
  .animate-fade-out.au-leave-active {
    animation: FadeOut 1s;
  }
```

  

基本上，所有需要使动画工作是用特殊的预定义后缀定义CSS类。您有机会使用在实际动画开始之前添加的准备类，以及用于触发实际动画的激活类。查看下表中的所有可用选项。


<table border="1">

<tbody>

<tr>

<th>Method</th>

<th>Description</th>

<th>Preparation</th>

<th>Activation</th>

</tr>

<tr>

<td>Enter</td>

<td>Element enters the DOM</td>

<td>au-enter</td>

<td>au-enter-active</td>

</tr>

<tr>

<td>Leave</td>

<td>Element leaves the DOM</td>

<td>au-leave</td>

<td>au-leave-active</td>

</tr>

<tr>

<td>addClass</td>

<td>Adds a CSS class</td>

<td>n/a</td>

<td>[className]-add</td>

</tr>

<tr>

<td>removeClass</td>

<td>Removes a CSS class</td>

<td>n/a</td>

<td>[className]-remove</td>

</tr>

</tbody>

</table>

## 5 Working with Default Animation Triggers - 使用默认动画触发器

我们需要给我们的元素一个类 `au-animate`来告诉Aurelia这些元素可以被动画化。此外，我们应该应用一个特定的动画，从我们已经创建以上(i.e `animate-fade-in`)。完成之后，每当元素进入或离开DOM时，动画就会开始。

例如，我们可能在一个转发器中有多个`li`元素渲染，我们希望它们使用我们在上面声明的渐退效果来进行动画输入和输出。我们可以这样声明：

**todos.html**

``` HTML
<ul class="au-stagger">
    <li
      repeat.for="todo of todos"
      class="au-animate animate-fade-in animate-fade-out"
    >
      <input type="checkbox" checked.bind="todo.done">
      <span css="text-decoration: ${todo.done ? 'line-through' : 'none'}">
        ${todo.description}
      </span>
      <button click.trigger="removeTodo(todo)">Remove</button>
    </li>
  </ul>
```

注意 `ul`容器上的 `au-stagger`类。它用于延迟每个 `li`之间的动画，这样它们就不会同时进行动画。

**animations.css**

``` css
  .au-stagger {
    animation-delay: 500ms;
  }
```
如果我们想让在 `router-view`中呈现的视图动起来，我们可以使用相同的方法。我们需要在视图的第一个子节点上添加`au-animate`类，并添加所需的entering/exiting（输入/退出）动画。

**todos.html**

``` HTML
 <template>
    <div class="au-animate animate-slide-in-right animate-slide-out-left">
      ...
    </div>
  </template>
```
 
 我们还需要设置 `router-view`元素的 `swap-order` 属性。这控制了旧视图和新视图之间的动画如何及时排序。关于可用选项的更多信息可以在[这里](https://github.com/sansantang/aurelia_translate/blob/master/Route/%E8%B7%AF%E7%94%B1%E5%99%A8%E9%85%8D%E7%BD%AE.md#view-swapping-and-animation-%E6%9F%A5%E7%9C%8B%E4%BA%A4%E6%8D%A2%E5%92%8C%E5%8A%A8%E7%94%BB)阅读，你可以在上面的演示中使用它们的效果。
  
  ## 6 Summary 总结

  通过采用`aurelia-animator-css`插件，将动画添加到应用程序中非常简单，只需包含一些标准的CSS动画并将一些类应用于选定的HTML元素即可。然而，这只是触及了Aurelia动画的表面。如果您需要更多的功能，您可以通过创建一个实现标准Aurelia接口的类并将其插入来实现自己的动画系统，这将带来无限的可能性。