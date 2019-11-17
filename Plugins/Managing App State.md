原文：https://aurelia.io/docs/plugins/store

>使用Aurelia Store插件进行可预测的状态管理。

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

  ## 7 What are actions?

Actions are the primary way to create a new state. They are essentially functions which take the current state and optionally one or more arguments. Their job is to create the next state and return it. By doing so they should not mutate the passed-in current state but instead use immutable functions to create a proper clone. The reason for that is that each state represents a unique snapshot of your app in time. By modifying it, you'd alter the state and wouldn't be able to properly compare the old and new state. It would also mean that advanced features such as time-traveling through states wouldn't work properly anymore. So keep in mind ... don't mutate your state.

>提醒：In case you're not a fan of functional approaches take a look at libraries like [Immer.js](https://github.com/mweststrate/immer) , and the [Aurelia store example](https://github.com/zewa666/aurelia-store-examples#immer) using it: this lets you act like you're mutating the state object but secretly you're getting a proper clone.

Continuing with above framework example, let's say we want to make an action to add an additional framework to the list. You can create a "shallow" clone of the state by using `Object.assign`. By saying shallow we mean that the actual `frameworks` array in the new state object will just be a reference to the original one. So in order to fix that we can use the array spread syntax with the name of the new framework to create a fresh array.

**A simple action**

``` javascript
  const demoAction = (state, frameworkName) => {
    const newState = Object.assign({}, state);
    newState.frameworks = [...newState.frameworks, frameworkName];
  
    return newState;
  }
```
Next, we need to register the created action with the store. That is done by calling the store's `registerAction` method. By doing so we can provide a name which will be used for all kinds of error-handlers, logs, and even Redux DevTools. As a second argument, we pass the action itself.
  
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
You can unregister actions whenever needed by using the store's `unregisterAction` method
  
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

  ## 8 Async actions

Previously we mentioned that an action should return a state. What we didn't mention is that actions can also return a promise which will eventually resolve with the new state.

From the above example, imagine we'd have to validate the given name, which happens in an async manner.

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
  
## 9 Dispatching actions

So far we've just created an action and registered it by several means. Now let's look at how we can actually execute one of them to trigger the next state change. We can use the store method `dispatch` to do exactly that. In the example below, the function `dispatchDemo` can be called with an argument `nextFramework`. Inside we call `store.dispatch`, passing it the action itself and all subsequent parameters required. Alternatively we can also provide the previously registered name instead.
  
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

  Now keep in mind that an action might be async (really any middleware might be - you'll learn more about middleware later), and as such if you depend on the state being updated right after dispatching an action, make sure to `await` the call to `dispatch`.

The choice whether you dispatch using the actual action function or its previously registered name is up to you. It might be less work just forwarding a string. That way you don't need to import the action from wherever you want to dispatch it. On the other hand using the actual function is a helpful mechanism to make sure your app survives a refactoring session. Imagine you'd rename the action's name but forgot to update all the places that dispatch it. A long night of debugging might be just around the corner ;)

>提醒：Dispatching non-registered actions will result in an error.

## 10 Piping multiple actions as one

Being able to dispatch an action is great but what if you want to dispatch multiple actions at once? Of course you could call `dispatch` multiple times but that would result in perhaps unnecessary roundtrips and moreover either distort your history or fill up your DevTools entries. In such cases the piped dispatch might come in handly.

You call that using `store.pipe(reducer, params)` which returns a `PipedDispatch` object. That one allows to chain more steps into itself and to finally dispatch the aggregated action.

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

  >`pipe()` accepts only registered reducers, same as `dispatch`, and will otherwise throw an error.


A piped dispatch will only trigger a single state transition. When only a single reducer is piped and dispatched right away, the behavior is identical to using the `dispatch` method.

Looking at the DevTools, you'll notice that piped actions show up in the format `actionA->actionB`.


## 11 Using the dispatchify higher order function

Perhaps you don't want to or can't obtain a reference to the store, but you'd still like to dispatch your actions. This is especially useful if you don't want your child-elements to have any knowledge of the actual logic and just receive actions via attributes. Children can then call this method directly in the template. In order to do so, you can leverage the higher order function `dispatchify`. What it does is return a wrapped new function which will obtain the store by itself and forward the provided arguments directly to `store.dispatch`.

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
  
## 12 Resetting the store to a specific state
  Occasionally it might be necessary to _reset_ the store to a specific state, without running through the built-in queue and thus without notifying middlewares. A use case for this can be restoring the initial state or time-travelling with the Redux-DevTools extension. To do so use the method `resetToState` and pass in the desired state. The result will be that a new state is emitted and your subscriptions will receive it.
  
 > Keep in mind that this feature should be used with caution as it might introduce unintended side effects, especially in collaboration with the Redux DevTools, as the reset states are not tracked by it.

## 13 Two-way binding and state management

Two-way binding definitely has its merits when it comes to easily developing applications. By having Aurelia take care of synchronizing of the View and ViewModel a lot of work gets done for free. This behavior though becomes troublesome when working with a central state management. Remember, the primary vision is to do state modifications only via dispatching actions. This way we guarantee a single source of truth and no side-effects. Yet exactly the later is what a two-way binding introduces if you bind your ViewModel directly to state objects. So instead of two-way binding to state objects you should:

*   Create a new property, reflecting the state part you'd like to modify and bind to that.
*   When changes are made, persist the changes by dispatching an action which uses the binding model's values
*   After dispatching make sure to update the binding model to match any updates to the object happening during dispatch

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

  Alternatively you can resort to only using `one-time`, `to-view`, `one-way` and handle the updates yourself.

## 14 Recording a navigable history of the stream of states

Since the whole concept of this plugin is to stream states over time, it makes sense to also keep track of the historical changes. Aurelia Store supports this feature by turning on the history support during the plugin registration.

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
## 15 Making our app history-aware

Now keep in mind that every action will receive a `StateHistory<T>` as input and should return a new `StateHistory<T>`. You can use the `nextStateHistory` helper function to easily push your new state and create a proper StateHistory representation, which will simply move the currently present state to the past, place your provided one as the present and remove the future states.
  
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

  ### 15.1 Navigating through history

Having a history of states is great to do state time-travelling. That means defining either a past or future state as the new present. You can do it manually as described in the full-fledged example above and switching states between the properties `past`, `present` and `future`, or you can import the pre-registered action `jump` and pass it either a positive number for traveling into the future or a negative for travelling to past states.

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
The `jump` action will take care of any potential overflows and return the current history object.

## 16 Limiting the number of history items

Having too many items could result in a memory hit. Thus you can specify the `limit` for when the histories past and future start to overflow. That means your past and future arrays can hold only a maximum of the provided `limit` and new entries start to drop out, respectively the first or last item of the history stack.

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

## 17 Handling side-effects with middleware

Aurelia Store uses a concept of middleware to handle side-effects. Conceptually they are similar to [Express.js](https://expressjs.com/) middlewares in that they allow to perform side-effects or manipulate request data. As such they are registered functions, which execute before or after each dispatched action.

A middleware is similar to an action, with the difference that it may return void as well. Middlewares can be executed before the dispatched action, thus potentially manipulating the current state which will be passed to the action, or afterward, modifying the returned value from the action. If they don't return anything the previous value will be passed as output. Either way, the middleware can be sync as well as async.
  
 >提醒：As soon as you have one async middleware registered, essentially all action dispatches will be async as well.

![Chart workflow](https://github.com/sansantang/aurelia_translate/blob/master/Plugins/IMG/middlewares.png)

Middlewares are registered using `store.registerMiddleware` with the middleware's function and the placement `before` or `after`. Unregistration can be done using `store.unregisterMiddleware`

  **Registering a middleware**

``` javascript
  // app.js
  
  const customLogMiddleware = (currentState) => console.log(currentState);
  
  // in JavaScript just provide the string 'before' or 'after'
  store.registerMiddleware(customLogMiddleware, 'after');
```

>提醒：You can call the `store.registerMiddleware` function whenever you want. This means middlewares don't have to be defined upfront at the apps configuration time but whenever needed. The same applies to `store.unregisterMiddleware`.
  
## 18 Accessing the original (unmodified) state in a middleware

When executed, a middleware might accept a second argument which reflects the current unmodified state, the one before any other middlewares or, in case of an after positioning, the result of the dispatched action. This can be useful to determine the state diff that happened in the middleware chain or to reset the next state at certain conditions.

**Accessing the original state**

``` javascript
  // app.js
  
  const blacklister = (currentState, originalState) => {
    if ( currentState.newValue.indexOf('f**k') > -1 ) {
      return originalState;
    }
  }
```

## 19 Defining settings for middlewares

Some middlewares require additional configurations in order to work as expected. Above we've looked at a `customLogMiddleware` middleware, which logs the newly created state to the console. Now if we wanted to control the log type to, let's say, output to `console.debug` we can make use of middleware settings. These are passed in as the third argument to the middleware function and are registered with `registerMiddlware`.
  
**Passing settings to middlewares**

``` javascript
 // app.js
  
  const customLogMiddleware = (currentState, originalState, settings) => console[settings.logType](currentState);
  
  store.registerMiddleware(customLogMiddleware, 'after', { logType: 'debug' });
```

  ## 20 Reference to the calling action for middlewares

Last but not least the optional fourth argument passed into a middleware is the calling action, meaning the action that is being dispatched. In here you get an object containing the action's `name` and the provided `params`. This is useful when you, for instance, want only certain actions to pass or be canceled under certain circumstances.

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
In case of piped actions, the `action.name` will show these in the format `actionA->actionB`, just like they are displayed in the Redux Devtools. The `action.params` on the other hand, will contain all the piped parameters in a single array. Additionally, you will be able to access details of all individual actions from within the pipeline via `action.pipedActions`.

## 21 Error propagation with middlewares

By default errors thrown by middlewares will be swallowed in order to guarantee continued states. If you would like to stop state propagation you need to set the `propagateError` option of the plugin to `true`:
  
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

## 22 Default middlewares

Aurelia Store comes with a few home-baked middlewares. Others should be added as custom dependencies instead of polluting the overall package size.

### 22.1 The Logging Middleware

From the previous explanations of the inner workings of middlewares, you've come to learn about the `loggingMiddleware`. Instead of rebuilding it you can simply import it from Aurelia Store and register it. Remember that you can pass in a settings object to define the `logType`, which is also defined as a string enum in TypeScript.

  **Registering the Logging middleware**

``` javascript
  // app.js
  
  import { logMiddleware } from 'aurelia-store';
  
  ...
  
  store.registerMiddleware(logMiddleware, 'after', { logType: 'log' });
```

### 22.2 The Local Storage middleware

The `localStorageMiddleware` stores your most recent emitted state in the [window.localStorage](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage) . This is useful when creating apps which should survive a full page refresh. Generally, it makes the most sense to place the middleware at the end of the queue to get the latest available value stored in `localStorage`.

In order to make use of it all, all you need to do is to register the middleware as usual. By default, the storage key will be `aurelia-store-state`. You can additionally provide a storage-key via the settings to be used instead.

  **Registering the LocalStorage middleware**

``` javascript
  // app.js
  
  import { localStorageMiddleware } from 'aurelia-store';
  
  ...
  
  store.registerMiddleware(localStorageMiddleware, MiddlewarePlacement.After, { key: 'my-storage-key' });
  
```

Now in order to rehydrate the stored state, all you need to do is to dispatch the provided `rehydrateFromLocalStorage` action which you can import and register as usual. If you used a different key then the default one, just pass it as the second argument to the dispatch call.

**Dispatching the localStorage rehydration action**

``` javascript
 // app.js
  
  import { rehydrateFromLocalStorage } from 'aurelia-store';
  
  ...
  
  store.registerAction('Rehydrate', rehydrateFromLocalStorage);
  store.dispatch(rehydrateFromLocalStorage, 'my-storage-key');
  
```

>提示：  Keep in mind that the store starts with an `initialState`. If the `localStorage` middleware is registered at the app's start, most likely the next refresh will immediately overwrite your `localStorage` and negate the effect of restoring data from previous runs. In order to avoid that, make sure to register the middleware just after the initial state has loaded.


## 23 Execution order

If multiple actions are dispatched, they will get queued and executed one after another in order to make sure that each dispatch starts with an up-to-date state.

If either your actions or middlewares return a sync or async value of `false` it will cause the Aurelia Store plugin to interrupt the execution and not emit the next state. Use this behavior in order to avoid unnecessary state updates.


## 24 Tracking overall performance

In order to get insights into total run durations to effectively calculate how long it takes to dispatch the next state, you can pass in the `measurePerformance` option in the plugin configuration section.

**Tracking performance data**

``` javascript
// main.js
  
  aurelia.use.plugin('aurelia-store', { initialState, measurePerformance: 'all' });
```
You can choose between `startEnd` - which gets you a single measure with the duration of the whole dispatch queue - or `all`, which will log, besides the total duration, all single marks after every middleware and the actual dispatching.

Measures will only be logged for successful state updates, so if an action or middleware aborts due to returning `false` or throwing an error, nothing gets logged.
  

## 25 Debugging with the Redux DevTools extension

If you've ever worked with Redux then you know for sure about the [Redux Devtools browser extension](https://github.com/zalmoxisus/redux-devtools-extension) . It's a fantastic way to record and replay the states of your application's walkthrough. For each step, you get detailed information about your state at that time. This can help tremendously to debug states and replicate issues more easily.

There are tons of [great articles](https://codeburst.io/redux-devtools-for-dummies-74566c597d7) to get you started. Head over to [DevTools browser extension page](https://github.com/zalmoxisus/redux-devtools-extension) for instructions on how to install the extension, start your Aurelia Store plugin project and see how it works.


## 26 Defining custom devToolsOptions

if you use the Redux DevTools extension you can pass options to Aurelia-Store to setup the extension with your [preferred configuration](https://github.com/zalmoxisus/redux-devtools-extension/blob/master/docs/API/Arguments.md) .

In the following example, we set the serialize option to false. This way our state will not get serialized when sending it to the extension.

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

## 27 Defining custom LogLevels

For various features, Aurelia Store can log information if logging is turned on. E.g. the dispatch info of the currently dispatched action will log on info level by default. Combining the logging of many of these features might be very distracting in your console window. As such you can define the log level to be used per feature in the plugin's setup options. In the following example, we'd like to have the `logLevel` for the `dispatchAction` info set to `debug` instead of the default `info` level.
  
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
Besides the control for `dispatchedActions` you can also set the logType for the `performanceLog` and `devToolsStatus` notifications.
  
## 28 Comparison to other state management libraries

There are a lot of other state management libraries out there, so you might ask yourself why you should favor Aurelia Store instead. As always Aurelia doesn't want to force you into a certain direction. There are good reasons to stick with something you're already familiar or using in another project. Let's look at the differences with a few of the well-known alternatives.

### 28.1 Differences to Redux

Doubtlessly [Redux](https://redux.js.org/) is one of the most favored state management libraries out there in the ecosystem. With its solid principles of being a predictable state container and thus working towards consistently behaving apps, it's a common choice amongst React developers. A lot of that is motivated by the focus on immutable states and the predictability that this brings in itself. Yet Redux is not solely bound to React and can be used with everything else, [including Aurelia](https://www.sitepoint.com/managing-state-aurelia-with-redux/) . There are even plugins to help you [get started](https://github.com/steelsojka/aurelia-redux-plugin) .

Aurelia Store shares a lot of fundamental design choices from Redux, yet drastically differentiates itself in two points. For one it's the reduction of boilerplate code. There is no necessity to split Actions and Reducers, along with separate action constants. Plain functions are all that is needed. Secondly, handling async state calculations is simplified by treating the apps state as a stream of states. RxJS as such is a major differentiator, which is also slowly finding its place in the [Redux eco-system](https://github.com/redux-observable/redux-observable) .

### 28.2 Differences to MobX

[MobX](https://github.com/mobxjs/mobx) came up as a more lightweight alternative to Redux. With its focus on observing properties for changes and that way manipulating the app's state, it addresses the issue of reducing boilerplate and not forcing the user into a strict functional programming style. MobX, similar to Redux, is not tied specifically to a framework - although they offer React bindings out of the box - yet it is not really a great fit for Aurelia. The primary reason for this is that observing property changes is actually one of the main selling points of Aurelia. Same applies to computed values resembling Aurelia's `computedFrom` and reactions, being pretty much the same as `propertyChanged` handlers.

Essentially all that MobX brings to the table might be implemented with vanilla Aurelia plus a global state service.

### 28.3 Differences to VueX

The last well-known alternative is [VueX](https://github.com/vuejs/vuex) , a state management library designed specifically for use with the [Vue framework](https://vuejs.org/v2/guide/) . On the surface, VueX is relatively similar to MobX with some specific twists to how it handles internal changes, being `mutations`, although developers [seem to disagree](https://twitter.com/youyuxi/status/736939734900047874) about that. Mutations very much translate function-wise to Redux reducers, with the difference that they make use of Vue's change tracking and thus nicely fit into the framework itself. Modules, on the other hand, are another way to group your actions.

Aurelia Store is pretty similar to VueX in that regard. It makes use of Aurelia's dependency injection, logging and platform abstractions, but aside from that is still a plain simple TypeScript class and could be re-used for any other purpose. One of the biggest differentiators is that Aurelia Store does not force any specific style. Whether you prefer a class-based approach, using the `connectTo` decorator, or heavy function based composition, the underlying architecture of a private `BehaviorSubject` and a public `Observable` is flexible enough to adapt to your needs.
  