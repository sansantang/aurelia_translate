原文：https://aurelia.io/docs/plugins/store

>使用Aurelia Store插件进行可预测的状态管理。

* [1 Introduction \- 简介](#1-introduction---%E7%AE%80%E4%BB%8B)
  * [1\.1 Reasons for state management \- 状态管理的原因](#11-reasons-for-state-management---%E7%8A%B6%E6%80%81%E7%AE%A1%E7%90%86%E7%9A%84%E5%8E%9F%E5%9B%A0)
  * [1\.2 Why is RxJS used for this plugin? \- 为什么RxJS用于此插件？](#12-why-is-rxjs-used-for-this-plugin---%E4%B8%BA%E4%BB%80%E4%B9%88rxjs%E7%94%A8%E4%BA%8E%E6%AD%A4%E6%8F%92%E4%BB%B6)
* [2 Getting Started \- 开始](#2-getting-started---%E5%BC%80%E5%A7%8B)
* [3 What is the State? \- 什么是状态？](#3-what-is-the-state---%E4%BB%80%E4%B9%88%E6%98%AF%E7%8A%B6%E6%80%81)
* [4 Configuring your app \- 配置您的APP](#4-configuring-your-app---%E9%85%8D%E7%BD%AE%E6%82%A8%E7%9A%84app)
* [5 Subscribing to the stream of states \- 订阅状态流](#5-subscribing-to-the-stream-of-states---%E8%AE%A2%E9%98%85%E7%8A%B6%E6%80%81%E6%B5%81)
* [6 Subscribing with the connectTo decorator \- 订阅 connectTo 装饰模式](#6-subscribing-with-the-connectto-decorator---%E8%AE%A2%E9%98%85-connectto-%E8%A3%85%E9%A5%B0%E6%A8%A1%E5%BC%8F)
* [7 What are actions? \- 什么操作？](#7-what-are-actions---%E4%BB%80%E4%B9%88%E6%93%8D%E4%BD%9C)
* [8 Async actions \- 异步操作](#8-async-actions---%E5%BC%82%E6%AD%A5%E6%93%8D%E4%BD%9C)
* [9 Dispatching actions 分发操作](#9-dispatching-actions-%E5%88%86%E5%8F%91%E6%93%8D%E4%BD%9C)
* [10 Piping multiple actions as one 将多个操作管道化为一个操作](#10-piping-multiple-actions-as-one-%E5%B0%86%E5%A4%9A%E4%B8%AA%E6%93%8D%E4%BD%9C%E7%AE%A1%E9%81%93%E5%8C%96%E4%B8%BA%E4%B8%80%E4%B8%AA%E6%93%8D%E4%BD%9C)
* [11 Using the dispatchify higher order function \- 使用dispatchify高阶函数](#11-using-the-dispatchify-higher-order-function---%E4%BD%BF%E7%94%A8dispatchify%E9%AB%98%E9%98%B6%E5%87%BD%E6%95%B0)
* [12 Resetting the store to a specific state \- 将store重置为特定的状态](#12-resetting-the-store-to-a-specific-state---%E5%B0%86store%E9%87%8D%E7%BD%AE%E4%B8%BA%E7%89%B9%E5%AE%9A%E7%9A%84%E7%8A%B6%E6%80%81)
* [13 Two\-way binding and state management \- 双向绑定和状态管理](#13-two-way-binding-and-state-management---%E5%8F%8C%E5%90%91%E7%BB%91%E5%AE%9A%E5%92%8C%E7%8A%B6%E6%80%81%E7%AE%A1%E7%90%86)
* [14 Recording a navigable history of the stream of states \- 记录状态流的导航历史](#14-recording-a-navigable-history-of-the-stream-of-states---%E8%AE%B0%E5%BD%95%E7%8A%B6%E6%80%81%E6%B5%81%E7%9A%84%E5%AF%BC%E8%88%AA%E5%8E%86%E5%8F%B2)
* [15 Making our app history\-aware \- 对我们的应用程序历史了解](#15-making-our-app-history-aware---%E5%AF%B9%E6%88%91%E4%BB%AC%E7%9A%84%E5%BA%94%E7%94%A8%E7%A8%8B%E5%BA%8F%E5%8E%86%E5%8F%B2%E4%BA%86%E8%A7%A3)
  * [15\.1 Navigating through history \- 浏览历史](#151-navigating-through-history---%E6%B5%8F%E8%A7%88%E5%8E%86%E5%8F%B2)
* [16 Limiting the number of history items \- 限制历史记录项的数量](#16-limiting-the-number-of-history-items---%E9%99%90%E5%88%B6%E5%8E%86%E5%8F%B2%E8%AE%B0%E5%BD%95%E9%A1%B9%E7%9A%84%E6%95%B0%E9%87%8F)
* [17 Handling side\-effects with middleware \- 处理副作用与中间件](#17-handling-side-effects-with-middleware---%E5%A4%84%E7%90%86%E5%89%AF%E4%BD%9C%E7%94%A8%E4%B8%8E%E4%B8%AD%E9%97%B4%E4%BB%B6)
* [18 Accessing the original (unmodified) state in a middleware \- 在中间件中访问原始(未修改的)状态](#18-accessing-the-original-unmodified-state-in-a-middleware---%E5%9C%A8%E4%B8%AD%E9%97%B4%E4%BB%B6%E4%B8%AD%E8%AE%BF%E9%97%AE%E5%8E%9F%E5%A7%8B%E6%9C%AA%E4%BF%AE%E6%94%B9%E7%9A%84%E7%8A%B6%E6%80%81)
* [19 Defining settings for middlewares \- 为middlewares定义设置](#19-defining-settings-for-middlewares---%E4%B8%BAmiddlewares%E5%AE%9A%E4%B9%89%E8%AE%BE%E7%BD%AE)
* [20 Reference to the calling action for middlewares \- 参考中间件的调用操作](#20-reference-to-the-calling-action-for-middlewares---%E5%8F%82%E8%80%83%E4%B8%AD%E9%97%B4%E4%BB%B6%E7%9A%84%E8%B0%83%E7%94%A8%E6%93%8D%E4%BD%9C)
* [21 Error propagation with middlewares \- 使用中间件进行错误传播](#21-error-propagation-with-middlewares---%E4%BD%BF%E7%94%A8%E4%B8%AD%E9%97%B4%E4%BB%B6%E8%BF%9B%E8%A1%8C%E9%94%99%E8%AF%AF%E4%BC%A0%E6%92%AD)
* [22 Default middlewares \- 默认中间件](#22-default-middlewares---%E9%BB%98%E8%AE%A4%E4%B8%AD%E9%97%B4%E4%BB%B6)
  * [22\.1 The Logging Middleware \- 日志中间件](#221-the-logging-middleware---%E6%97%A5%E5%BF%97%E4%B8%AD%E9%97%B4%E4%BB%B6)
  * [22\.2 The Local Storage middleware \- 本地存储中间件](#222-the-local-storage-middleware---%E6%9C%AC%E5%9C%B0%E5%AD%98%E5%82%A8%E4%B8%AD%E9%97%B4%E4%BB%B6)
* [23 Execution order \- 执行顺序](#23-execution-order---%E6%89%A7%E8%A1%8C%E9%A1%BA%E5%BA%8F)
* [24 Tracking overall performance \- 跟踪整体性能](#24-tracking-overall-performance---%E8%B7%9F%E8%B8%AA%E6%95%B4%E4%BD%93%E6%80%A7%E8%83%BD)
* [25 Debugging with the Redux DevTools extension \- 使用Redux DevTools扩展进行调试](#25-debugging-with-the-redux-devtools-extension---%E4%BD%BF%E7%94%A8redux-devtools%E6%89%A9%E5%B1%95%E8%BF%9B%E8%A1%8C%E8%B0%83%E8%AF%95)
* [26 Defining custom devToolsOptions \- 定义自定义devToolsOptions](#26-defining-custom-devtoolsoptions---%E5%AE%9A%E4%B9%89%E8%87%AA%E5%AE%9A%E4%B9%89devtoolsoptions)
* [27 Defining custom LogLevels \- 定义自定义日志级别](#27-defining-custom-loglevels---%E5%AE%9A%E4%B9%89%E8%87%AA%E5%AE%9A%E4%B9%89%E6%97%A5%E5%BF%97%E7%BA%A7%E5%88%AB)
* [28 Comparison to other state management libraries \- 与其他状态管理库的比较](#28-comparison-to-other-state-management-libraries---%E4%B8%8E%E5%85%B6%E4%BB%96%E7%8A%B6%E6%80%81%E7%AE%A1%E7%90%86%E5%BA%93%E7%9A%84%E6%AF%94%E8%BE%83)
  * [28\.1 Differences to Redux](#281-differences-to-redux)
  * [28\.2 Differences to MobX](#282-differences-to-mobx)
  * [28\.3 Differences to VueX](#283-differences-to-vuex)

## 1 Introduction - 简介

本文介绍了Aurelia的Store插件。它建立在[RxJS](http://reactivex.io/rxjs/)的两个核心特性之上，即`Observables`和`BehaviorSubject`。你不必钻研反应性领域 —— 事实上，你几乎不会在一开始就注意到它。但是，为了明智地使用它，您当然可以从了解 RxJS 中受益。

可以在样例存储库[samples repository](https://github.com/zewa666/aurelia-store-examples)中找到各种示例，这些示例演示了插件的各个部分。

### 1.1 Reasons for state management - 状态管理的原因


许多现代开发方法都利用了一个单独的存储，它是应用程序的中心基础。其思想是，它保存构成应用程序的所有数据。存储的内容是应用程序的状态。如果愿意，应用程序状态是特定时刻所有数据的快照。通过使用操作(操作是操作全局状态的唯一方法)和创建下一个应用程序状态来修改它。


将此与传统的面向服务的方法进行对比，在传统的方法中，数据在几个服务实体之间进行分割。一开始，一种更简单的方法，尤其是与强大的IoC容器相结合，一旦应用程序的大小变大，就会成为一个问题。您不仅开始增加服务的复杂性和相互依赖性，而且跟踪谁修改了什么以及如何通知每个组件有关更改的信息也变得很棘手。

利用单一的存储方法，您的数据只有一个真实的来源，所有的修改都以可预测的方式发生，这可能导致更无副作用的整体应用程序。

### 1.2 Why is RxJS used for this plugin? - 为什么RxJS用于此插件？

As mentioned in the intro this plugin uses RxJS as a foundation. The main idea is having a [BehaviorSubject](http://reactivex.io/rxjs/manual/overview.html#behaviorsubject) `store._state` which will store the current state. It starts with some initial state and emits new states as they come. The BehaviorSubject cannot be accessed directly - it is defined as private - but instead a public Observable named `state` should be consumed. This way consumers only have reading access to streamed values but cannot directly manipulate the streaming queue through the Subjects `next` method.

正如在介绍中提到的，这个插件使用RxJS作为基础。主要的想法是建立一个行为主题[BehaviorSubject](http://reactivex.io/rxjs/manual/overview.html#behaviorsubject)`store._state`  将存储当前状态的状态。它从一些初始状态开始，并随着它们的出现发出新状态。无法直接访问 BehaviorSubject - 它被定义为私有 - 但是应该使用公共的 Observable 命名状态。通过这种方式，使用者只能读取流值，但不能通过下一个主体方法直接操作流队列。

But besides these core features, RxJS itself can be described as _[Lodash/Underscore for events](http://reactivex.io/rxjs/manual/overview.html#introduction)_ . As such all of the operators and objects can be used to manipulate the state in whatever way necessary. As an example [pluck](http://reactivex.io/rxjs/class/es6/Observable.js~Observable.html#instance-method-pluck) can be used to pierce into a sub-section of the state, whereas methods like [skip](http://reactivex.io/rxjs/class/es6/Observable.js~Observable.html#instance-method-skip) and [take](http://reactivex.io/rxjs/class/es6/Observable.js~Observable.html#instance-method-take) are great ways to unit test the stream of states over time.

但是除了这些核心功能之外，RxJS本身可以描述为事件的_[Lodash/Underscore for events](http://reactivex.io/rxjs/manual/overview.html#introduction)_。这样，所有运算符和对象都可以以任何必要的方式用于操纵状态。例如，可以使用[pluck](http://reactivex.io/rxjs/class/es6/Observable.js~Observable.html#instance-method-pluck)来投入状态的子部分，而像[skip](http://reactivex.io/rxjs/class/es6/Observable.js~Observable.html#instance-method-skip) 和 [take](http://reactivex.io/rxjs/class/es6/Observable.js~Observable.html#instance-method-take)这样的方法是对随时间变化的状态流进行单元测试的好方法。


不过，使用 RxJS 的主要原因是，可观察数据是随时间交付的。这促进了对如何设计应用程序的反应性方法。我们不采用一种命令式的方法，比如**单击按钮A**，会**触发组件B的重新渲染**，而是采用一种观察者模式（[Observer Pattern](https://en.wikipedia.org/wiki/Observer_pattern)），其中**组件B观察全局状态**并对**更改进行操作**，这些更改是**通过按钮A的操作触发**的。


如下图所示，分解为Aurelia的概念，这意味着ViewModel订阅单个存储（store）并设置状态订阅（state subscription）。视图直接绑定到状态的属性（binds to properties of the state）。可以分发操作（Actions can be dispatched）并触发下一个状态发出。

现在，初始订阅接收下一个状态并更改绑定变量，Aurelia自动找出更改的内容并触发重新渲染。下一个分发（dispatch）将触发下一个循环，以此类推。通过这种方式，系统以一种循环的、反应性的方式运行，并将状态更改视为重新呈现的请求。

![Chart workflow](https://github.com/sansantang/aurelia_translate/blob/master/Plugins/IMG/chart_store_workflow.png)


这种方法的一个基本好处是，作为开发人员，您不需要考虑向各个组件发送有关更改的信号，而是在状态的各个部分被修改时，它们都将自己侦听和响应更改。可以将其视为一个事件分发，其中多个接收者可以侦听并执行更改，但是可以使用正式的全局状态。因此，您只需要关注状态，其余的将自动处理。

另一个好处是订阅的异步性。无论操作是同步操作，比如更改页面标题、获取最新产品的Ajax请求，还是下一个聊天应用程序的长时间运行的web套接字:无论何时分发下一个操作，侦听器都将对这些更改作出反应。

## 2 Getting Started - 开始

通过以下途径安装npm依赖项

``` shell
  npm install aurelia-store
```

or using [Yarn](https://yarnpkg.com)

 

``` shell
 yarn add aurelia-store
```

>提醒：如果您目前还没有使用RxJS，建议运行`npm install rxjs`，它应该安装最新的可用RxJS(目前是6.x.x)版本作为项目依赖。

>提醒：与最近发布的RxJS v.6，这个插件所依赖的东西发生了很大的变化。有一些新的方法可以导入依赖项，也有一些方法可以保持与以前的API版本的兼容性。查看下面的[升级说明](https://github.com/ReactiveX/rxjs/blob/master/docs_app/content/guide/v6/migration.md)以了解更多细节。


## 3 What is the State? - 什么是状态？

典型的应用程序由多个组件组成，这些组件呈现各种数据。除了实际数据之外，您的组件还包含各种状态。例如，切换按钮的“active”状态，但也包括高级状态，如所选主题或当前页面。组件中包含的状态是一件好事，只要该组件实例只关心它。但是当您从另一个组件引用那个状态时，您将需要一种关于那个状态的通信方式，比如服务类。像 EventAggregator 这样的发布-订阅（Pub-sub）机制是组件间通信（inter-component communication）的另一个例子。

与之相反，Store插件只在单一的应用程序状态下运行。可以将它看作一个大型对象，其中包含在特定时间点反映应用程序条件的所有子状态。这个状态对象只需要包含可序列化的属性。有了它，您将获得拥有应用程序快照的好处，该快照允许使用各种很酷的功能，例如时间旅行（time-traveling），保存/重新加载（save/reload）等。


您在状态中投入多少取决于您自己，但是一个好的经验法则是，一旦应用程序的两个不同区域使用相同的数据或影响相同的组件状态，您就应该存储它们。

您的应用程序通常从初始状态开始，然后在整个应用程序生命周期中对其进行操作。如前所述，这种状态几乎可以是任何状态，如下面的示例所示。您喜欢TypeScript还是纯JavaScript取决于您自己，但是有了类型化的状态就可以更容易地重构和更好的自动补全支持。

**Defining the State entity and initialState**

``` javascript
  // there is no need for a dedicated entity in JavaScript
  
  // state.js
  export const initialState = {
    frameworks: ['Aurelia', 'React', 'Angular']
  };
```

## 4 Configuring your app - 配置您的APP

为了告诉Aurelia如何使用这个插件，我们需要注册它。这是在应用程序的主文件 `main`中完成的，特别是`configure`方法。我们必须使用前面定义的状态实体注册存储：
  
**Registering the plugin**
``` javascript
  // main.js
  import { Aurelia } from 'aurelia-framework';
  import { initialState } from './state';
  
  export function configure(aurelia) {
    aurelia.use
      .standardConfiguration()
      .feature('resources');
  
    ...
  
    aurelia.use.plugin('aurelia-store', { initialState });  // <----- REGISTER THE PLUGIN
  
    aurelia.start().then(() => aurelia.setRoot());
  }
```

完成此操作后，我们就可以使用我们的应用程序状态，并深入研究状态管理领域。
  
## 5 Subscribing to the stream of states - 订阅状态流

正如一开始所解释的，Aurelia Store插件提供了一个名为`state`的公共可见状态，它会随着时间的推移不断地传输应用程序的状态。因此，为了使用它，我们首先需要通过依赖注入将存储注入到构造函数中。接下来，在绑定（ `bind`）生命周期方法中，我们订阅`Store`的状态（ `state`）属性。在订阅的处理程序中，我们将拥有实际的流状态，并可以将其分配给组件的本地状态属性。不要忘记在组件解除绑定后释放订阅。这是通过调用订阅的 `unsubscribe`方法实现的。
  
  **Injecting the store and creating a subscription**

``` javascript
// app.js
  import { inject } from 'aurelia-dependency-injection';
  import { Store } from 'aurelia-store';
  
  @inject(Store)
  export class App {
    constructor(store) { this.store = store; }
  
    bind() {
      this.subscription = this.store.state.subscribe(
        (state) => this.state = state
      );
    }
  
    unbind() {
      this.subscription.unsubscribe();
    }
  }
```

 >提醒：请注意，在TypeScript版本中，我们不必对提供给订阅处理程序的状态变量进行类型转换，因为初始存储是使用状态（`State`）实体作为泛型提供者注入的。

有了这些，就可以像往常一样直接从模板中使用状态：

**Subscribing to the state property**

``` html
  <template>
    <h1>Frameworks</h1>
  
    <ul>
      <li repeat.for="framework of state.frameworks">${framework}</li>
    </ul>
  </template>
```

由于您已经订阅了状态，所以到达的每个状态的新版本将再次分配给组件的状态（ `state`）属性，并且根据更改的细节自动重新呈现UI。

## 6 Subscribing with the connectTo decorator - 订阅 connectTo 装饰模式

在前一节中，您已经看到了如何手动绑定到状态观察对象以实现完全控制。但是，您可能更喜欢使用`connectTo` 装饰器模式，而不是自己处理订阅和处理。它的作用是将Store的状态自动连接到一个名为`state`的类属性。它通过覆盖`bind` 和 `unbind`生命周期方法来正确设置和销毁状态订阅，状态订阅将存储在一个名为`_stateSubscription`的属性中。

上面的 ViewModel 示例可以使用 `connectTo`装饰器看起来像这样：

**Using the connectTo decorator**

``` javascript
  // app.js
  import { connectTo } from 'aurelia-store';
  
  @connectTo()
  export class App {
  }
```

>提醒：注意我们如何在TS版本中声明 `State`类型的public state属性。这样做的惟一原因是在编译期间需要适当的类型提示。

如果您希望提供一个自定义选择器而不是订阅整个状态，您可以提供一个函数，它将接收存储并返回一个被使用的可观察对象，而不是默认的`store.state`。为了实现更好的TypeScript工作流，装饰器接受一个与您的状态匹配的通用接口。
  
**Sub-state selection**

``` javascript
 // app.js
  import { pluck } from 'rxjs/operators';
  ...
  
  @connectTo((store) => store.state.pipe(pluck('frameworks')))
  export class App {
    ...
  }
```

如果您需要更多的控制，并且例如想要覆盖默认的目标属性`state`，您可以传递一个settings对象，而不是一个函数，其中子状态选择器（`selector`）匹配上面的函数，而`target`指定持有接收状态的新目标。
  
**Defining the selector and target**

``` javascript
 // app.js
  import { pluck } from 'rxjs/operators';
  ...
  
  @connectTo({
    selector: (store) => store.state.pipe(pluck('frameworks')), // same as above
    target: 'currentState' // link to currentState instead of state property
  })
  export class App {
    ...
  }
```

如果希望有多个选择器，可以将一个对象传递给选择器（ `selector`），选择器对象中的每个属性定义一个接收状态片的新目标。每个属性的值应该是一个函数，如上面所示。

  **Defining multiple selectors**

``` javascript
  // app.js
  import { pluck } from 'rxjs/operators';
  ...
  
  @connectTo({
    selector: {
      currentState: (store) => store.state.pipe(pluck('frameworks')), // same as above
      databases: (store) => store.state.pipe(pluck('databases'))
    }
  })
  export class App {
    ...
  }
```

您仍然可以提供具有多个选择器的目标（`target`），这将更改多个选择器的行为。与在视图模型中为每个选择器名称提供多个目标（`target`）属性不同，目标将是视图模型中的一个对象，而多个选择器名称将是该对象的属性。

**Defining multiple selectors with a target**

``` javascript
  // app.js
  import { pluck } from 'rxjs/operators';
  ...
  
  @connectTo({
    target: 'currentState',
    selector: {
      currentState: (store) => store.state.pipe(pluck('frameworks')), // same as above
      databases: (store) => store.state.pipe(pluck('databases'))
    }
  })
  export class App {
    activate() {
      console.log(this.currentState.frameworks);
      console.log(this.currentState.databases);
    }
  }
```

不仅可以指定目标，还可以指定默认的`setup` 和 `teardown`方法，可以指定一个，也可以指定两个。钩子 `bind` 和 `unbind`充当默认值。
  
**Overriding the default setup and teardown methods**

``` javascript
 // app.js
  import { pluck } from 'rxjs/operators';
  ...
  
  @connectTo({
    selector: (store) => store.state.pipe(pluck('frameworks')), // same as above
    setup: 'create'        // create the subscription inside the create life-cycle hook
    teardown: 'deactivate' // do the disposal in deactivate
  })
  export class App {
    ...
  }
```

>提醒：为 setup 和 teardown 提供的操作名不一定是正式的[生命周期方法](https://github.com/sansantang/aurelia_translate/blob/master/Fundamentals/%E5%88%9B%E5%BB%BA%E7%BB%84%E4%BB%B6.md#the-component-lifecycle-%E7%BB%84%E4%BB%B6%E7%9A%84%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F)，但是应该被Aurelia在适当的时候自动调用。

最后但并非最不重要的一点是，您还可以通过多种方式处理状态变化

当没有传递任何设置，使用函数或选择器使用对象设置时，装饰器将尝试调用`stateChanged(newState, oldState)`。
  
**Default change stateChanged handling**

``` javascript
  // app.js
  ...
  
  // All of these have the same change handling
  // @connectTo({
  //   selector: (store) => store.state.pipe(pluck('frameworks')),
  // })
  // OR
  // @connectTo((store) => store.state.pipe(pluck('frameworks')))
  // OR
  @connectTo()
  export class App {
    ...
  
    stateChanged(newState, oldState) {
      console.log('The state has changed', newState);
    }
  }
```

提供一个没有多个选择器的目标名会调用 `target` Changed 方法。

  **Default change handling with target**

``` javascript
// app.js
  ...
  
  @connectTo({
    target: 'currentState'
  })
  export class App {
    ...
  
    currentStateChanged(newState, oldState) {
      console.log('The state has changed', newState);
    }
  }
```

使用多个选择器，每个选择器都可以获得相同的更改处理。

**Default change handling with multiple selectors**

``` javascript
 // app.js
  import { pluck } from 'rxjs/operators';
  ...
  
  @connectTo({
    selector: {
      frameworks: (store) => store.state.pipe(pluck('frameworks')),
      databases: (store) => store.state.pipe(pluck('databases'))
    }
  })
  export class App {
    ...
  
    frameworksChanged(newState, oldState) {
      console.log('The state has changed', newState);
    }
  
    databasesChanged(newState, oldState) {
      console.log('The state has changed', newState);
    }
  }
```

但是，提供一个具有多个选择器的目标（`target`），通过使用3个参数调用`target` Changed方法，而不是使用2个参数调用每个单独的选择器，可以更改先前的行为。您可以访问从第一个参数更改后的单个状态。
  
**Default change handling with multiple selectors and target**

``` javascript
// app.js
  import { pluck } from 'rxjs/operators';
  ...
  
  @connectTo({
    target: 'currentState',
    selector: {
      frameworks: (store) => store.state.pipe(pluck('frameworks')),
      databases: (store) => store.state.pipe(pluck('databases'))
    }
  })
  export class App {
    ...
  
    currentStateChanged(stateName, newState, oldState) {
      // this will be called twice:
      //   once for stateName='frameworks'
      //   once for stateName='databases'
  
      console.log('The state has changed', newState);
    }
  }
```

最后但并非最不重要的一点是，一旦发生状态更改，您还可以定义要用下一个状态调用的回调。

  **Define an onChanged handler**

  

``` coffeescript
// app.js
  import { pluck } from 'rxjs/operators';
  ...
  
  @connectTo({
    selector: (store) => store.state.pipe(pluck('frameworks')), // same as above
    onChanged: 'changeHandler'
  })
  export class App {
    ...
  
    changeHandler(newState, oldState) {
      console.log('The state has changed', newState);
    }
  }
```

对于这些配置中的任何一个，您都可以添加一个称为`propertyChanged`的附加更改处理函数，该函数将最后调用。它有3个参数，状态名被更改，新状态和旧状态。
  
**Default change handling with propertyChanged**
 
``` javascript
// app.js
  ...
  
  @connectTo()
  export class App {
    ...
  
    propertyChanged(stateName, newState, oldState) {
      // stateName will be "state"
      console.log('The state has changed', newState);
    }
  }
```

 >提醒：更改处理程序函数将在更改目标属性之前调用。通过这种方式，您可以访问当前状态和以前的状态。如果您提供一个`onChanged`处理程序，那么您的视图模型中最多可以有3个更改处理方法(按顺序调用): `onChanged`方法、`target`Changed方法和`propertyChanged`方法。

接下来，让我们找出如何产生状态变化。

  ## 7 What are actions? - 什么操作？

Actions are the primary way to create a new state. They are essentially functions which take the current state and optionally one or more arguments. Their job is to create the next state and return it. By doing so they should not mutate the passed-in current state but instead use immutable functions to create a proper clone. The reason for that is that each state represents a unique snapshot of your app in time. By modifying it, you'd alter the state and wouldn't be able to properly compare the old and new state. It would also mean that advanced features such as time-traveling through states wouldn't work properly anymore. So keep in mind ... don't mutate your state.

操作是创建新状态的主要方法。它们本质上是接受当前状态和一个或多个可选参数的函数。他们的工作是创建下一个状态并返回它。通过这样做，它们不应该改变传入的当前状态，而是使用不可变的函数来创建正确的克隆。原因是每个状态及时地表示应用程序的唯一快照。通过修改它，您将改变状态，并不能正确地比较新旧状态。这也意味着像穿越状态的时间旅行这样的高级功能再也不能正常工作了。所以记住……不要改变你的状态。

>提醒：如果你不喜欢函数式方法，可以看看像[Immer.js](https://github.com/mweststrate/immer) 这样的库，使用它的[Aurelia商店示例](https://github.com/zewa666/aurelia-store-examples#immer):这使您可以像修改状态对象一样操作，但实际上您得到的是一个正确的克隆。


继续上面的框架示例，假设我们希望执行一个操作来向列表添加一个额外的框架。您可以使用`Object.assign`创建状态的“浅”拷贝。这里说浅层表示在新的状态对象中的实际的框架（`frameworks`）数组将只是一个对原始对象的引用。为了修正这个问题，我们可以使用数组扩展语法来创建一个新的数组。

**A simple action**

``` javascript
  const demoAction = (state, frameworkName) => {
    const newState = Object.assign({}, state);
    newState.frameworks = [...newState.frameworks, frameworkName];
  
    return newState;
  }
```

  接下来，我们需要将创建的操作注册到存储中。这是通过调用商店的`registerAction`方法实现的。通过这样做，我们可以提供一个用于各种错误处理程序、日志甚至Redux DevTools的名称。作为第二个参数，我们传递动作本身。
**Registering an action**

``` javascript
 // app.js
  import { inject } from 'aurelia-dependency-injection';
  import { Store } from 'aurelia-store';
  
  @inject(Store)
  export class App {
    constructor(store) {
      this.store = store;
      this.store.registerAction('DemoAction', demoAction);
    }
  
    ...
  }
```
  您可以随时使用存储的注销操作（(`unregisterAction`）方法注销操作
  
  **Unregistering an action**

``` javascript
 // app.js
  ...
  
  @inject(Store)
  export class App {
    constructor(store) {
      this.store = store;
      this.store.registerAction('DemoAction', demoAction);
      this.store.unregisterAction(demoAction);
    }
  
    ...
  }
```

  ## 8 Async actions - 异步操作

前面我们提到过，一个动作应该返回一个状态。我们没有提到的是，行为也可以返回一个承诺，这将最终解决与新状态。

从上面的例子中，假设我们必须验证给定的名称，这是以异步的方式发生的。

**An async action**

``` javascript
  function validateAsync(name) {
    return Promise((resolve, reject) => {
      setTimeout(() => {
        if (name === 'Angular') {
          reject(new Error('Try using a different framework'))
        } else {
          resolve(name);
        }
      }, 1000);
    })
  }
  
  const demoAction = async (state, frameworkName) => {
    const newState = Object.assign({}, state);
    const validatedName = await validateAsync(frameworkName);
  
    newState.frameworks = [...newState.frameworks, validatedName];
  
    return newState;
  }
```

>You don't have to use async/await but it's highly recommended to use it for better readability whenever you can.
  
## 9 Dispatching actions 分发操作
  
  到目前为止，我们只是创建了一个操作并通过几种方式注册了它。现在让我们看看如何实际执行其中一个来触发下一个状态更改。我们可以使用store方法`dispatch`来完成这一任务。在下面的示例中，可以使用参数`nextFramework`调用函数`dispatchDemo`。在里面我们叫`store.dispatch`，将操作本身和所需的所有后续参数传递给它。或者，我们也可以提供以前注册的名称。
  
**Dispatching an action**

``` javascript
// app.js
  import { inject } from 'aurelia-dependency-injection';
  import { Store, connectTo } from 'aurelia-store';
  
  @inject(Store)
  @connectTo()
  export class App {
    constructor(store) {
      this.store = store;
      this.store.registerAction('DemoAction', demoAction);
    }
  
    public dispatchDemo(nextFramework) {
      this.store.dispatch(demoAction, nextFramework);
  
      // or
      // this.store.dispatch('DemoAction', nextFramework);
    }
  }
```

  现在请记住，一个操作可能是异步的(实际上任何中间件都可能是异步的——稍后您将了解更多关于中间件的信息)，因此，如果您依赖于在发送操作之后立即更新状态，那么请确保等待（`await`）调用来分发（`dispatch`）。

您可以选择使用实际的操作函数进行分派，还是使用它之前注册的名称进行分派。只转发一个字符串的工作量可能会更少。这样您就不需要从任何您想要调度的地方导入动作。另一方面，使用实际的函数是一种很有帮助的机制，可以确保您的应用程序能够经受住重构过程。假设您要重命名操作的名称，但是忘记更新所有调度它的位置。漫长的调试之夜可能即将来临;)

>提醒：Dispatching non-registered actions will result in an error.
> 
> 分派未注册的操作将导致错误。

## 10 Piping multiple actions as one 将多个操作管道化为一个操作

能够调度一个操作是很好的，但是如果你想一次调度多个操作呢?当然，您可以多次调用`dispatch`，但这可能会导致不必要的往返，而且可能会歪曲您的历史记录或填充您的DevTools条目。在这种情况下，通过管道发送的调度可以很方便地派上用场。

你用store调用它。`store.pipe(reducer, params)` ，返回一个`PipedDispatch`对象。它允许将更多的步骤链接到自身，并最终调度聚合的操作。

**Dispatching multiple piped actions as one**
    
``` javascript
  // app.js
  import { inject } from 'aurelia-dependency-injection';
  import { Store, connectTo } from 'aurelia-store';
  
  @inject(Store)
  @connectTo()
  export class App {
    constructor(store) {
      this.store = store;
      this.store.registerAction('ActionA', actionA);
      this.store.registerAction('ActionB', actionB);
    }
  
    public pipedDispatchDemo() {
      store
        .pipe(actionA, "foo", 42)
        .pipe(actionB, "bar", 1337)
        .dispatch();
    }
  }
```

  >`pipe()`仅接受注册的减速器，与分派`dispatch`相同，否则将抛出错误。

管道分派将只触发单个状态转换。当仅通过管道立即发送单个减速器时，其行为与使用分派方法相同。

查看DevTools，您会注意到管道操作以`actionA->actionB`格式出现。

## 11 Using the dispatchify higher order function - 使用dispatchify高阶函数

也许您不想或无法获得对store的引用，但您仍然希望调度您的操作。如果您不希望子元素了解实际的逻辑，而只是通过属性接收操作，那么这一点尤其有用。然后，孩子们可以在模板中直接调用这个方法。为了做到这一点，您可以利用高阶函数`dispatchify`。它所做的是返回一个包装好的新函数，该函数将自己获取存储并将提供的参数直接转发给 `store.dispatch`。

**Forwarding a dispatchable function as argument to child-elements**

``` javascript
 // actions.js
  export const addFramework = (state, frameworkName) => {
    const newState = Object.assign({}, state);
    newState.frameworks = [...newState.frameworks, frameworkName];
    
    return newState;
  }
  
  // app.html
  <template>
    <require from="./framework-list" ></require>
  
    <framework-list></framework-list>
  </template>
  
  // app.js
  import { inject } from 'aurelia-dependency-injection';
  import { Store } from 'aurelia-store';
  
  import * as actions from './actions';
  
  @inject(Store)
  export class App {
    constructor(store) {
      this.store = store;
  
      store.registerAction('Add framework', actions.addFramework);
    }
  
  }
  
  // framework-list.js
  import { bindable, inlineView } from 'aurelia-framework';
  import { connectTo, dispatchify } from 'aurelia-store';
  
  import * as actions from './actions';
  
  @connectTo()
  @inlineView(`
    <template>
      <require from="./framework-item"></require>
      <framework-item add.bind="addFramework"></framework-item>
      <ul>
        <li repeat.for="framework of state.frameworks">\${framework}</li>
      </ul>
    </template>
  `)
  export class FrameworkList {
    @bindable state;
  
    constructor() {
      this.addFramework = dispatchify(actions.addFramework);
    }
  }
  
  // framework-item.js
  import { bindable, inlineView } from 'aurelia-framework';
  
  @inlineView(`
    <template>
      New framework name:
      <input value.bind="newFrameworkName">
      <button click.trigger="add(newFrameworkName)" >Add</button>
    </template>
  `)
  export class FrameworkItem {
    @bindable add;
  }
```

With this approach, you can design your custom elements to act either as presentational or container components. For further information take a look at [this article](http://pragmatic-coder.net/using-a-state-container-with-aurelia/) .

使用此方法，可以将自定义元素设计为表示组件或容器组件。更多信息请看[this article](http://pragmatic-coder.net/using-a-state-container-with-aurelia/)。
  
## 12 Resetting the store to a specific state - 将store重置为特定的状态

有时可能需要将存储重置为特定的状态，而不需要运行内置队列，因此不需要通知中间件。使用redu - devtools扩展可以恢复初始状态或时间旅行。为此，请使用`resetToState`方法并传递所需的状态。结果将发出一个新状态，您的订阅将接收它。
  
 > 请记住，应该谨慎使用此功能，因为它可能会带来意想不到的副作用，特别是在与Redux DevTools协作时，因为它不会跟踪重置状态。

## 13 Two-way binding and state management - 双向绑定和状态管理

当涉及到轻松开发应用程序时，双向绑定肯定有其优点。通过让Aurelia负责视图和视图模型的同步，可以免费完成许多工作。但是，在使用中央状态管理时，这种行为会变得很麻烦。请记住，主要的设想是仅通过分发操作进行状态修改。这样我们就保证了一个单一的真相来源和没有副作用。然而，如果您将ViewModel直接绑定到状态对象，则双向绑定所引入的正是后者。因此，应该使用双向绑定而不是状态对象：

*   创建一个新属性，反映您希望修改和绑定的状态部分。
*   在进行更改时，通过调度使用绑定模型值的操作来持久化更改
*  在分派之后，确保更新绑定模型以匹配在分派期间发生的对对象的任何更新

**Using binding models instead of the state**

``` javascript
 /*
   * state: {
   *  firstName: string,
   *  lastName: string
   * }
   */
  import { inlineView } from 'aurelia-framework';
  import { inject } from 'aurelia-dependency-injection';
  import { Store } from 'aurelia-store';
  
  function updateUser(state, model) {
    return Object.assign({}, state, model);
  }
  
  @inject(Store)
  @inlineView(`
    <template>
      Firstname: <input type="text" value.bind="model.firstName">
      Lastname: <input type="text" value.bind="model.lastName">
      <button click.delegate="updateUser()">Update</button>
      <div>
        <b>Model</b> Firstname = '\${model.firstName}', Lastname = '\${model.lastName}'
      </div>
      <div>
        <b>State</b> Firstname = '\${state.firstName}', Lastname = '\${state.lastName}'
      </div>
    </template>
  `)
  export class App {
    constructor(store) {
      this.store = store;
      this.store.registerAction('updateUser', updateUser);
    }
  
    bind() {
      this.store.state.subscribe((newState) => {
        this.state = newState;
        this.model = Object.assign({}, newState); // take care of shallow cloning
      });
    }
  
    updateUser() {
      this.store.dispatch(updateUser, this.model);
    }
  }
```

  或者，您可以只使用 `one-time`, `to-view`, `one-way`并且自己处理更新。

## 14 Recording a navigable history of the stream of states - 记录状态流的导航历史

因为这个插件的整个概念是随时间流状态，所以跟踪历史变化也是有意义的。Aurelia Store通过在插件注册期间打开历史记录支持来支持这个特性。

**Registering the plugin with history support**

``` javascript
  // main.js
  import {Aurelia} from 'aurelia-framework';
  import {initialState} from './state';
  
  export function configure(aurelia) {
    aurelia.use
      .standardConfiguration()
      .feature('resources');
  
    ...
  
    aurelia.use.plugin('aurelia-store', {
      initialState,
      history: {
        undoable: true <----- REGISTER THE PLUGIN WITH THE HISTORY FEATURE
      }
    });
  
    aurelia.start().then(() => aurelia.setRoot());
  }
```

  Now when you subscribe to new state changes, instead of a simple State you'll get a `StateHistory<State>` object returned, which looks like the following:
现在，当您订阅新的状态更改时，您将获得一个`StateHistory<State>`对象，而不是简单的状态，如下所示：

``` typescript
//TypeScript

// aurelia-store -> history.ts
  export interface StateHistory<T> {
    past: T[];
    present: T;
    future: T[];
  }
```

  **Subscribing to the state history**

``` javascript
  // app.js
  import { inject } from 'aurelia-framework';
  import { Store } from 'aurelia-store';
  
  @inject(Store)
  export class App {
    constructor(store) {
      this.store = store;
      this.store.registerAction('DemoAction', demoAction);
    }
  
    attached() {
      this.store.state.subscribe(
        (state) => this.state = state
      );
    }
  }
```
## 15 Making our app history-aware - 对我们的应用程序历史了解

现在请记住，每个操作都将接收一个州`StateHistory<T>`作为输入，并且应该返回一个新的`StateHistory<T>`。你可以使用 `nextStateHistory` 帮助函数来轻松地推动您的新状态，并创建一个合适的状态历史表示，它将简单地将当前状态移动到过去，将您提供的状态作为当前状态并删除未来状态。
  
**A StateHistory-aware action**

``` javascript
 // app.js
  import { nextStateHistory, StateHistory } from 'aurelia-store';
  
  const demoAction = (currentState, frameworkName) => {
    return nextStateHistory(currentState, {
      frameworks: [...frameworks, frameworkName]
    });
  }
  
  // The same as returning a handcrafted object like
  
  const demoAction = (currentState, frameworkName) => {
    return Object.assign(
      {},
      currentState,
      {
        past: [...currentState.past, currentState.present],
        present: { frameworks: [...frameworks, frameworkName] },
        future: []
      }
    );
  }
```

  ### 15.1 Navigating through history - 浏览历史

拥有国家历史很适合进行国家时间旅行。这意味着将过去或未来的状态定义为新的现在。你可以手动如上所描述的成熟的例子和开关状态属性之间的`past`, `present` and `future`,您可以导入预注册的动作跳转（`jump`），然后将其传递给正数以供将来使用，或传递负数供用于过去的状态。

**Time-travelling states**

``` javascript
  // app.js
  import { inject } from 'aurelia-framework';
  import { Store, jump } from 'aurelia-store';
  
  @inject(Store)
  export class App {
    constructor(store) {
      this.store = store;
      this.store.registerAction('DemoAction', demoAction);
    }
  
    attached() {
      this.store.state.subscribe(
        (state) => this.state = state
      );
    }
  
    backToDinosaurs() {
      // Go back one step in time
      this.store.dispatch(jump, -1);
    }
  
    backToTheFuture() {
      // Go forward one step to the future
      this.store.dispatch(jump, 1);
    }
  }
```

跳转（`jump`）操作将处理任何潜在的溢出并返回当前历史记录对象。

## 16 Limiting the number of history items - 限制历史记录项的数量

项目太多可能会导致内存不足。因此，您可以指定历史记录过去和未来何时开始溢出的限制（`limit`）。这意味着您的过去和未来数组只能容纳所提供的最大限制，并且新的条目将开始删除，分别是历史堆栈的第一项或最后一项。

**Registering the plugin with history overflow limits**

``` javascript
 // main.js
  import {Aurelia} from 'aurelia-framework';
  import {initialState} from './state';
  
  export function configure(aurelia) {
    aurelia.use
      .standardConfiguration()
      .feature('resources');
  
    ...
  
    aurelia.use.plugin('aurelia-store', {
      initialState,
      history: {
        undoable: true,
        limit: 10       <----- LIMIT THE HISTORY TO 10 ENTRIES
      }
    });
  
    aurelia.start().then(() => aurelia.setRoot());
  }
```

## 17 Handling side-effects with middleware - 处理副作用与中间件

Aurelia Store使用中间件的概念来处理副作用。从概念上讲，它们与[Express.js](https://expressjs.com/) 中间件类似，因为它们允许执行副作用或操作请求数据。因此，它们是注册函数，在每次分派操作之前或之后执行。

中间件类似于操作，但不同之处在于它也可能返回void。中间件可以在调度操作之前执行，因此可能会操纵将传递给操作的当前状态，或者在之后修改操作返回的值。如果它们不返回任何内容，则将以前的值作为输出传递。无论哪种方式，中间件都可以是同步的，也可以是异步的。
  
 >警告：只要注册了一个异步中间件，基本上所有动作分派都将是异步的。

![Chart workflow](https://github.com/sansantang/aurelia_translate/blob/master/Plugins/IMG/middlewares.png)

中间层注册使用 `store.registerMiddleware`与中间件的功能和放置前后（`before` or `after`）。可以使用`store.unregisterMiddleware`中间件取消注册

  **Registering a middleware**

``` javascript
  // app.js
  
  const customLogMiddleware = (currentState) => console.log(currentState);
  
  // in JavaScript just provide the string 'before' or 'after'
  store.registerMiddleware(customLogMiddleware, 'after');
```

>提醒：你可以给随时使用 `store.unregisterMiddleware`功能。这意味着中间件不必在应用程序配置时预先定义，而是在需要的时候定义。这同样适用于`store.unregisterMiddleware`中间件。
  
## 18 Accessing the original (unmodified) state in a middleware - 在中间件中访问原始(未修改的)状态

在执行时，中间件可能会接受第二个参数，该参数反映当前未修改的状态，即任何其他中间件之前的状态，或者在after定位的情况下，是分派操作的结果。这对于确定中间件链中发生的状态差异或在某些条件下重置下一个状态非常有用。

**Accessing the original state**

``` javascript
  // app.js
  
  const blacklister = (currentState, originalState) => {
    if ( currentState.newValue.indexOf('f**k') > -1 ) {
      return originalState;
    }
  }
```

## 19 Defining settings for middlewares - 为middlewares定义设置

一些中间件需要额外的配置才能正常工作。上面我们介绍了 `customLogMiddleware` 中间件，它将新创建的状态记录到控制台。现在，如果我们希望将日志类型控制为输出到 `console.debug` ，我们可以使用中间件设置。这些参数作为中间件函数的第三个参数传递，并在 `registerMiddlware`中注册。
  
**Passing settings to middlewares**

``` javascript
 // app.js
  
  const customLogMiddleware = (currentState, originalState, settings) => console[settings.logType](currentState);
  
  store.registerMiddleware(customLogMiddleware, 'after', { logType: 'debug' });
```

  ## 20 Reference to the calling action for middlewares - 参考中间件的调用操作

最后但并非最不重要的是传递给中间件的可选的第四个参数是调用操作，这意味着正在分派的操作。在这里，您将获得一个包含操作 `name` 和提供的`params`的对象。例如，当您仅希望某些操作在某些情况下通过或取消时，这是非常有用的。

**Reference to the calling action in middlewares**

``` javascript
  // app.js
  
  const gateKeeperMiddleware = (currentState, originalState, _, action) => {
    // imagine a lockActive property on the state indicating that certain actions may not be executed
    if (currentState.lockActive === true && action.name === 'trespasser') {
      return originalState;
    }
  };
  
  store.registerMiddleware(gateKeeperMiddleware, 'after');
```

对于管道操作，`action.name`将以`actionA->actionB`格式显示这些内容，就像它们在Redux Devtools中显示一样。`action.params`的行动。另一方面，params将在单个数组中包含所有管道参数。另外，您将能够通过`action.pipedActions`从管道中访问所有单独操作的详细信息。


## 21 Error propagation with middlewares - 使用中间件进行错误传播

  默认情况下，由中间件抛出的错误将被吞噬，以保证状态的连续性。如果您想要停止状态传播，您需要将插件的`propagateError`选项设置为`true`：
  
**Registering the plugin with active error propagation**

``` javascript
  // main.js
  import {Aurelia} from 'aurelia-framework';
  import {initialState} from './state';
  
  export function configure(aurelia) {
    aurelia.use
      .standardConfiguration()
      .feature('resources');
  
    ...
  
    aurelia.use.plugin('aurelia-store', {
      initialState,
      propagateError: true
    });
  
    aurelia.start().then(() => aurelia.setRoot());
  }
```

## 22 Default middlewares - 默认中间件

Aurelia Store附带一些自制的中间件。其他应作为自定义依赖项添加，而不要污染整个程序包的大小。

### 22.1 The Logging Middleware - 日志中间件

从前面对中间件内部工作原理的解释中，您已经了解了日志中间件`loggingMiddleware`。您可以简单地从Aurelia存储导入它并注册它，而不必重新构建它。请记住，您可以传递设置对象来定义`logType`，它也在TypeScript中定义为字符串 enum。

  **Registering the Logging middleware**

``` javascript
  // app.js
  
  import { logMiddleware } from 'aurelia-store';
  
  ...
  
  store.registerMiddleware(logMiddleware, 'after', { logType: 'log' });
```

### 22.2 The Local Storage middleware - 本地存储中间件

`localStorageMiddleware`将您最近发出的状态存储在 [window.localStorage](https://developer.mozilla.org/zh-cn/docs/Web/API/Window/localStorage)中。这在创建应用程序时是很有用的，它应该在整个页面刷新后仍然有效。通常，最合理的做法是将中间件放在队列的末尾，以获得存储在`localStorage`中的最新可用值。

为了充分利用它，您所需要做的就是像往常一样注册中间件。默认情况下，存储密钥是`aurelia-store-state`。您还可以通过要使用的设置提供存储键。

  **Registering the LocalStorage middleware**

``` javascript
  // app.js
  
  import { localStorageMiddleware } from 'aurelia-store';
  
  ...
  
  store.registerMiddleware(localStorageMiddleware, MiddlewarePlacement.After, { key: 'my-storage-key' });
  
```

现在，为了重新补充存储状态，您所需要做的就是调度提供的`rehydrateFromLocalStorage`操作，您可以像往常一样导入和注册该操作。如果您使用的键与默认键不同，那么只需将其作为第二个参数传递给分派调用。

**Dispatching the localStorage rehydration action**

``` javascript
 // app.js
  
  import { rehydrateFromLocalStorage } from 'aurelia-store';
  
  ...
  
  store.registerAction('Rehydrate', rehydrateFromLocalStorage);
  store.dispatch(rehydrateFromLocalStorage, 'my-storage-key');
  
```

>提示：  请记住，存储store从`initialState`开始。如果`localStorage`中间件在应用程序的启动时注册，那么下一次刷新很可能会立即覆盖`localStorage`并抵消恢复前一次运行的数据的效果。为了避免这种情况，请确保在初始状态加载后立即注册中间件。


## 23 Execution order - 执行顺序

如果分派了多个操作，它们将排队并依次执行，以确保每个分派都以最新的状态开始。

如果您的操作或中间件返回一个同步或异步值`false`，它将导致Aurelia Store插件中断执行，而不发出下一个状态。使用此行为可避免不必要的状态更新。

## 24 Tracking overall performance - 跟踪整体性能

为了深入了解总的运行时间，从而有效地计算调度下一个状态所需的时间，您可以在插件配置部分传递 `measurePerformance`选项。

**Tracking performance data**

``` javascript
// main.js
  
  aurelia.use.plugin('aurelia-store', { initialState, measurePerformance: 'all' });
```

您可以在`startEnd`和`all`之间进行选择，前者为您提供整个调度队列的持续时间，后者将在每个中间件和实际调度之后记录除总持续时间之外的所有单个标记。

衡量将只记录成功的状态更新，因此如果一个操作或中间件由于返回`false`或抛出错误而中止，则不会记录任何内容。
  

## 25 Debugging with the Redux DevTools extension - 使用Redux DevTools扩展进行调试

如果您曾经使用过Redux，那么您肯定知道[Redux Devtools 浏览器扩展](https://github.com/zalmoxisus/redux-devtools-extension)。这是一种记录和回放应用程序演练状态的极好方法。对于每个步骤，您将获得当时状态的详细信息。这可以极大地帮助调试状态和更容易地复制问题。

有很多 [很好的文章](https://codeburst.io/redux-devtools-for-dummies-74566c597d7) 可以帮助你开始。前往[DevTools浏览器扩展页面](https://github.com/zalmoxisus/redux-devtools-extension)了解如何安装扩展，启动你的Aurelia Store插件项目，看看它是如何工作的。

## 26 Defining custom devToolsOptions - 定义自定义devToolsOptions

如果您使用Redux DevTools扩展，您可以将选项传递给Aurelia-Store，以使用[首选配置](https://github.com/zalmoxisus/redux-devtools-extension/blob/master/docs/API/Arguments.md)设置扩展。

在下面的示例中，我们将 `serialize` 选项设置为 false。这样，我们的状态在发送到扩展时就不会被序列化。

**Initializing the DevTools extension with custom options**

``` javascript
 // main.js
  
  ...
  aurelia.use.plugin('aurelia-store', {
    initialState,
    devToolsOptions: {
      serialize: false
    },
  });
```

## 27 Defining custom LogLevels - 定义自定义日志级别
  
  对于各种特性，如果打开日志记录，Aurelia存储可以记录信息。例如，当前被调度操作的调度信息将默认登录到info级别。在您的控制台窗口中，组合这些特性的日志可能会让您分心。因此，可以在插件的设置选项中定义每个特性使用的日志级别。在下面的示例中，我们希望将 `dispatchAction` 信息的 `logLevel` 设置为`debug`，而不是默认的`info`级别。
  
**Dispatch logs to console.debug**

``` javascript
  // main.js
  
  aurelia.use.plugin('aurelia-store', {
    initialState,
    logDispatchedActions: true,
    logDefinitions: {
      dispatchedActions: 'debug'
    }
  });
```
  
  除了`dispatchedActions`控件之外，您还可以为 `performanceLog` 和 `devToolsStatus`通知设置日志类型。
  
## 28 Comparison to other state management libraries - 与其他状态管理库的比较

还有许多其他的状态管理库，所以您可能会问自己为什么应该选择Aurelia Store。和往常一样，Aurelia不想强迫你朝某个方向走。有很好的理由坚持你已经熟悉的东西或在其他项目中使用的东西。让我们看看与一些众所周知的替代方案的区别。

### 28.1 Differences to Redux

毫无疑问，[Redux](https://redux.js.org/)是生态系统中最受欢迎的状态管理库之一。作为一个可预测的状态容器，它有着坚实的原则，因此致力于开发行为一致的应用程序，这是React开发者的普遍选择。这很大程度上是由对不可变状态的关注和由此带来的可预测性所驱动的。然而，Redux并不是唯一会发生反应的，它可以和其他任何东西一起使用，[包括Aurelia](https://www.sitepoint.com/managing-state-aurelia-with-redux/)。甚至还有一些插件可以帮助你[入门](https://github.com/steelsojka/aurelia-redux-plugin)。

Aurelia Store与Redux共享了许多基本的设计选择，但在两点上又有很大的不同。首先是样板代码的减少。没有必要将操作和简化器以及单独的操作常量分开。只需要简单的函数。其次，通过将应用程序状态作为状态流处理，可以简化异步状态计算。RxJS本身就是一个主要的区别，它也慢慢地在Redux生态系统（ [Redux eco-system](https://github.com/redux-observable/redux-observable)）中找到了自己的位置。

### 28.2 Differences to MobX

[MobX](https://github.com/mobxjs/mobx) came up as a more lightweight alternative to Redux. With its focus on observing properties for changes and that way manipulating the app's state, it addresses the issue of reducing boilerplate and not forcing the user into a strict functional programming style. MobX, similar to Redux, is not tied specifically to a framework - although they offer React bindings out of the box - yet it is not really a great fit for Aurelia. The primary reason for this is that observing property changes is actually one of the main selling points of Aurelia. Same applies to computed values resembling Aurelia's `computedFrom` and reactions, being pretty much the same as `propertyChanged` handlers.

Essentially all that MobX brings to the table might be implemented with vanilla Aurelia plus a global state service.

### 28.3 Differences to VueX

The last well-known alternative is [VueX](https://github.com/vuejs/vuex) , a state management library designed specifically for use with the [Vue framework](https://vuejs.org/v2/guide/) . On the surface, VueX is relatively similar to MobX with some specific twists to how it handles internal changes, being `mutations`, although developers [seem to disagree](https://twitter.com/youyuxi/status/736939734900047874) about that. Mutations very much translate function-wise to Redux reducers, with the difference that they make use of Vue's change tracking and thus nicely fit into the framework itself. Modules, on the other hand, are another way to group your actions.

Aurelia Store is pretty similar to VueX in that regard. It makes use of Aurelia's dependency injection, logging and platform abstractions, but aside from that is still a plain simple TypeScript class and could be re-used for any other purpose. One of the biggest differentiators is that Aurelia Store does not force any specific style. Whether you prefer a class-based approach, using the `connectTo` decorator, or heavy function based composition, the underlying architecture of a private `BehaviorSubject` and a public `Observable` is flexible enough to adapt to your needs.
  