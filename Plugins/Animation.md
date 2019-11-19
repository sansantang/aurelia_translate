原文：https://aurelia.io/docs/plugins/animation

## 1 Introduction - 简介

动画是我们将应用程序带入生活的方式。动画让一个元素从一种风格逐渐转变为另一种风格，使它能够随着时间的推移平滑地改变其大小、颜色等。

为Aurelia构建动画支持的一个关键目标是提供一个灵活的解决方案，允许您选择自己喜欢的动画库。因此，对于如何实现动画，您既不局限于专用API，也不局限于特定的样式。为了实现这种灵活性，Aurelia的动画系统围绕[一个简单的动画接口](https://github.com/aurelia/templating/blob/master/src/animator.js)构建，该接口是其模板库的一部分。

在这篇文章中，我们将介绍官方的CSS动画插件Aurelia, `aurelia-animator-css`。这个插件是上述接口的一个具体实现。然而，如果你喜欢Velocity库，你也可以使用我们官方的`aurelia-animator-velocity`插件，或者你可以基于上面的接口编写自己的插件，例如，如果你喜欢使用 Greensock 之类的东西。

任何类型的动画都可以应用到元素中，但在本文中，我们将通过使用简单的CSS动画演示常见的情况;主要是使用路由器在视图之间导航时的动画，以及对中继器（repeater）的传入和传出元素进行动画。


## Installing The Plugin

If you are using the CLI or Webpack, you can install the plugin from NPM:

``` Shell
  npm install aurelia-animator-css
```
or

``` Shell
  yarn add aurelia-animator-css
```

  
If you are using JSPM for client dependencies, then you can use this command:


``` Shell
  jspm install aurelia-animator-css
```

>提示：If you created your app with the **Aurelia CLI**, chances are you already have the plugin installed as a dependency.
  
>警告：If you are using an older version of **Aurelia CLI**, prior to 1.0, with RequireJS/SystemJS loaders, you should add `aurelia-animator-css` in the dependencies part of the bundle in your `aurelia.json` file.

## Configuring The Plugin

In your `main.js` within your `src` folder, simply call the plugin API with the animation plugin's name:

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

>警告：`PLATFORM.moduleName` should _not_ be omitted if you are using _Webpack_.  

## Demo

Before we get started setting up the animations themselves, take a look at a demo of what we'll build out.

[示例](https://codesandbox.io/s/x93zy0m8mp?from-embed)

  ## Using The Plugin

First we need to declare some `keyframes` animations that we can later hook on our elements.

>警告：Don't forget to add the appropriate vendor prefixes if you target old browsers.

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

  These are pretty typical, standard CSS animations. There's nothing really special to note about them. You may not need them all, or you may add new ones according to your needs, but they should give you a solid start.

Now we also need CSS classes that use those animations, so we can later add those classes on our elements to activate the animations.

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

  
Essentially, all that is needed to make an animation work is to define CSS classes with special predefined suffixes. You get the chance to use preparation classes, added before the actual animation starts, as well as activation classes, used to trigger the actual animation. Take a look at the following table for all available options.

<table>

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

## Working with Default Animation Triggers

We need to give our elements the class `au-animate` to tell Aurelia that those elements can be animated. Additionally, we should apply a specific animation from the ones we have created above (i.e `animate-fade-in`). Once that's done, every time the element enters or leaves the DOM, the animation will kick-in.

As an example, we may have multiple `li` elements rendering in a repeater and we would like them to animate in and out using the fading effect we declared above. We can declare that like this:

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

  Notice the `au-stagger` class on the `ul` container. It is used to delay the animation between each one of the `li`s so they don't animate in simultaneously.

**animations.css**

``` css
  .au-stagger {
    animation-delay: 500ms;
  }
```

  If we would like to animate the views that are rendered in a `router-view`, we can use the same method. We need to add the `au-animate` class on the first child of the view and add the desired entering/exiting animations.

**todos.html**

``` HTML
 <template>
    <div class="au-animate animate-slide-in-right animate-slide-out-left">
      ...
    </div>
  </template>
```
We'll also want to set the `swap-order` attribute of the `router-view` element. This controls how the animations between the old view and the new view are ordered in time. More information on the available options can be read about [here](docs/routing/configuration#view-swapping-and-animation) and you can play with their effects in the demo above.
  
  ## Summary

By adopting the `aurelia-animator-css` plugin, adding animations to your app is as simple as including a few standard CSS animations and applying a few classes to selected HTML elements. However, this just scratches the surface of animation in Aurelia. If you need more power, you can implement your own animation system by creating a class that implements the standard Aurelia interface and plugging it in, opening up endless possibilities.

  