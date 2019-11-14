原文：https://aurelia.io/docs/binding/binding-behaviors

>对Aurelia绑定引擎的绑定行为功能的概述。绑定行为用于插入绑定实例的生命周期并更改其操作方式。

## 1.Introduction

**绑定行为**是view资源的一个类别，就像**值转换器**、**自定义属性**和**自定义元素**一样。绑定行为最类似于值转换器[value converters](https://github.com/sansantang/aurelia_translate/blob/master/Binding/Value%20Converters.md) ，因为您可以在绑定表达式中声明性地使用它们来影响绑定。

绑定行为和值转换器之间的主要区别是，绑定行为在绑定实例的整个*生命周期*中都可以完全访问它。与此相比，值转换器只能拦截从 model 传递到 view 的值，反之亦然。

绑定行为提供的额外“访问”使它们能够更改绑定的行为，从而支持许多有趣的场景，您将在下面看到这些场景。

## 2.throttle 节流

Aurelia附带了一些开箱即用的行为来支持常见场景。第一种是节流绑定行为，它限制了在双向绑定中视图模型更新的速度，或者在 `to-view` 绑定场景中视图更新的速度。

默认情况下，`throttle` 将只允许每200毫秒更新一次。当然，您可以自定义费率。这里有几个例子。


**Updating a property, at most, every 200ms**
```
<input type="text" value.bind="query & throttle">
```

在上面的示例中，您可能首先注意到的是 `&` 符号，它用于声明绑定行为表达式。绑定行为表达式使用与值转换器表达式相同的语法模式:
*   绑定行为可以接受参数: `firstName & myBehavior:arg1:arg2:arg3`
*   一个绑定表达式可以包含多个绑定行为: `firstName & behavior1 & behavior2:arg1`.
*   绑定表达式还可以包含值转换器和绑定行为的组合: `${foo | upperCase | truncate:3 & throttle & anotherBehavior:arg1:arg2}`.

下面是另一个使用`throttle`的例子，演示了将参数传递给绑定行为的能力

**Updating a property, at most, every 850ms**
```
<input type="text" value.bind="query & throttle:850">
```

当将事件绑定到视图模型上的方法时，节流行为特别有用。下面是`mousemove`事件的一个例子：

**Handling an event, at most, every 200ms**
```
<div mousemove.delegate="mouseMove($event) & throttle"></div>
```
## 3.debounce 去抖动

debounce（去抖动） 绑定行为是另一种速率限制绑定行为。Debounce防止绑定更新，直到指定的时间间隔已经过去而没有任何变化。

一个常见的用例是自动触发搜索的搜索输入。您不会希望对每个更改(每个击键)都使用搜索API。等待用户暂停输入以调用搜索逻辑会更有效。

**Update after typing stopped for 200ms**
```
 <input type="text" value.bind="query & debounce">
```

**Update after typing stopped for 850ms**
```
<input type="text" value.bind="query & debounce:850">
```

与节流一样，`debounce`绑定行为在事件绑定中确实很出色。下面是`mousemove`事件的另一个例子

**Call mouseMove after mouse stopped moving for 500ms**
```
<div mousemove.delegate="mouseMove($event) & debounce:500"></div>
```
## 4.updateTrigger 更新触发器


更新触发器允许您覆盖导致将元素的值写入视图模型的输入事件。默认事件是更改`change`和输入`input`。

下面是如何告诉绑定只在`blur`上更新模型：

**Update on blur**
```
<input value.bind="firstName & updateTrigger:'blur'>
```
支持多个事件：

**Update with multiple events**
```
<input value.bind="firstName & updateTrigger:'blur':'paste'>
```

## 5.signal 信号

信号绑定行为允许您对绑定进行“信号”刷新。当绑定结果受到观察路径之外的全局更改的影响时，这一点特别有用。


例如，如果您有一个“translate”值转换器，它可以将一个键转换为一个本地化的字符串—例如 `${'greeting-key' | translate}` ，并且您的站点允许用户更改当前语言，那么当这种情况发生时，您将如何刷新绑定呢？

Another example is a value converter that uses the current time to convert a record's datetime to a "time ago" value: `posted ${postDateTime | timeAgo}`. The moment this binding expression is evaluated it will correctly result in `posted a minute ago`. As time passes, it will eventually become inaccurate. How can we refresh this binding periodically so that it correctly displays `5 minutes ago`, then `15 minutes ago`, `an hour ago`, etc?

另一个例子是值转换器，它使用当前时间将记录的datetime转换为“time ago”值：`posted ${postDateTime | timeAgo}`。当这个绑定表达式被求值时，它将在一分钟前 （`posted a minute ago`一分钟前发布）正确地得到结果。随着时间的推移，它最终会变得不准确。随着时间的推移，它最终会变得不准确。我们如何定期刷新这个绑定，使它正确地显示`5 minutes ago`、`15 minutes ago`、`an hour ago`等等？

下面介绍如何使用信号`signal`绑定行为来完成此任务:

**Using a Signal**
```
posted ${postDateTime | timeAgo & signal:'my-signal'}
```

在上面的绑定表达式中，我们使用信号`signal`绑定行为来为绑定分配`my-signal`的“信号名”。信号名称是任意的，如果希望同时向多个绑定发送信号，可以向多个绑定发送相同的信号名称。

下面介绍如何使用 `BindingSignaler` 定期对绑定发出信号：

**Signaling Bindings**
```
  import {BindingSignaler} from 'aurelia-templating-resources';
  import {inject} from 'aurelia-framework';
  
  @inject(BindingSignaler)
  export class App {
    constructor(signaler) {
      setInterval(() => signaler.signal('my-signal'), 5000);
    }
  }
```

## 6.oneTime 一次性


使用一次性`oneTime`绑定行为，您可以指定字符串内插绑定应该发生一次。简单的写：

**One-time String Interpolation**
```
 <span>${foo & oneTime}</span>
```

这是一个需要公开的重要特性。一次性绑定是最有效的绑定类型，因为它们不会引起任何属性观察开销。

还有 `toView` 和 `twoWay` 的绑定行为，可以像这样使用：

**To-view and two-way binding behaviours**
```
 <input value.bind="foo & toView">
  <input value.to-view="foo">
  
  <input value.bind="foo & twoWay">
  <input value.two-way="foo">
```

#### Binding Mode Casing 绑定模式包装

>绑定模式的包装是不同的，这取决于它们是作为绑定命令 **binding command**出现还是作为绑定行为**binding behavior**出现。由于HTML不区分大小写，绑定命令不能使用大写字母。因此，在此处指定绑定模式时，将使用小写、虚线名称。但是，在绑定表达式中作为绑定行为使用时，不能使用破折号，因为在JavaScript中，破折号不是变量名的有效符号。所以，在这种情况下，使用骆驼大小写。

## 7.self 自绑定

使用`self`绑定行为，您可以指定事件处理程序将只响应附加到侦听器的目标，而不是其后代。

例如，在下面的标记中

**Self binding behavior**
```
  <panel>
    <header mousedown.delegate='onMouseDown($event)' ref='header'>
      <button>Settings</button>
      <button>Close</button>
    </header>
  </panel>
```

`onMouseDown`是你的事件处理程序，它将被调用不仅当用户`mousedown`的标题元素，而且它的所有元素，在这种情况下是按钮`settings` 和 `close`。然而，这并不总是期望的行为。有时，您希望组件只在用户单击标题本身而不是按钮时响应。为了实现这一点，`onMouseDown`方法需要进行一些修改：

**Handler without self binding behavior**
```
 // inside component's view model class
  onMouseDown(event) {
    // if mousedown on the header's descendants. Do nothing
    if (event.target !== header) return;
    // mousedown on header, start listening for mousemove to drag the panel
    // ...
  }
```

这是可行的，但现在业务/组件逻辑与DOM事件处理混在一起，这是不必要的。使用`self`绑定行为可以帮助你实现相同的目标，而不用填充你的方法与不必要的代码:

**Using self binding behavior**
```
  <panel>
    <header mousedown.delegate='onMouseDown($event) & self'>
      <button class='settings'></button>
      <button class='close'></button>
    </header>
  </panel>
```
**Using self binding behavior**
```
  // inside component's view model class
  onMouseDown(event) {
    // No need to perform check, as the binding behavior will ensure check
    // if (event.target !== header) return;
    // mousedown on header, start listening for mousemove to drag the panel
    // ...
  }
```

## 8.Custom binding behaviors 自定义绑定行为

您可以构建自定义绑定行为，就像您可以构建值转换器一样。您将创建`bind(binding, scope, [...args])` 和 `unbind(binding, scope)`方法，而不是`toView` 和 `fromView`方法。在绑定方法中，您将向绑定添加您的行为，而在unbind方法中，您应该清除您在绑定方法中所做的一切，以将绑定实例恢复到其原始状态。 `binding`参数是您希望更改其行为的绑定实例。它是`Binding`接口的实现。`scope`参数是绑定的数据上下文。它通过绑定的`bindingContext` 和 `overrideContext`属性提供对模型的访问。

下面是一个自定义绑定行为，它在每次调用绑定的`updateSource` / `updateTarget` 和 `callSource`方法时调用视图模型上的方法。

**Creating a Custom Binding Behavior**
```
  const interceptMethods = ['updateTarget', 'updateSource', 'callSource'];
  
  export class InterceptBindingBehavior {
    bind(binding, scope, interceptor) {
      let i = interceptMethods.length;
      while (i--) {
        let method = interceptMethods[i];
        if (!binding[method]) {
          continue;
        }
        binding[`intercepted-${method}`] = binding[method];
        let update = binding[method].bind(binding);
        binding[method] = interceptor.bind(binding, method, update);
      }
    }
  
    unbind(binding, scope) {
      let i = interceptMethods.length;
      while (i--) {
        let method = interceptMethods[i];
        if (!binding[method]) {
          continue;
        }
        binding[method] = binding[`intercepted-${method}`];
        binding[`intercepted-${method}`] = null;
      }
    }
  }
```

**Using a Custom Binding Behavior**
```
 <template>
    <require from="./intercept-binding-behavior"></require>
  
    <div mousemove.delegate="mouseMove($event) & intercept:myFunc"></div>
  
    <input value.bind="foo & intercept:myFunc">
  </template>
```

