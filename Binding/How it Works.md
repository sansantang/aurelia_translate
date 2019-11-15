原文：https://aurelia.io/docs/binding/how-it-works

>数据绑定在Aurelia中的工作方式。


* [1\.End to End 端到端](#1end-to-end-%E7%AB%AF%E5%88%B0%E7%AB%AF)
* [2\.Abstract Syntax Tree 抽象语法树](#2abstract-syntax-tree-%E6%8A%BD%E8%B1%A1%E8%AF%AD%E6%B3%95%E6%A0%91)
* [3\.Binding Context / Scope 绑定上下文/作用域](#3binding-context--scope-%E7%BB%91%E5%AE%9A%E4%B8%8A%E4%B8%8B%E6%96%87%E4%BD%9C%E7%94%A8%E5%9F%9F)

## 1.End to End 端到端


本文解释了绑定系统的工作原理。这里有很多要介绍的内容，在开始之前，我们实际上要看看绑定系统之外的组件，这样您就可以全面了解这个过程。一个很好的起点是模板模块，其中有一个名为[ViewCompiler](https://github.com/aurelia/templating/blob/master/src/view-compiler.js)的组件。*ViewCompiler* 的工作是将视图编译成ViewFactory, `ViewFactory`将用于实例化模板的实例，称为视图。一些视图工厂将仅用于实例化特定模板的一个实例。编译`app.html`模板产生的ViewFactory通常属于这一类。与`repeat.for`一起使用的模板的其他视图工厂，可用于实例化数数十个、数百个甚至数千个视图实例。

*ViewFactory* 由模板(文档片段)、一组HTML行为指令(用于创建模板中出现的自定义元素或自定义属性的指令)、绑定表达式(用于创建模板中出现的绑定的工厂)和依赖项(您`<require from="...">`的内容)组成。


当Aurelia加载一个HTML模板时，浏览器会将标记解析为一个文档片段。文档片段是浏览器对HTML的对象表示。它是一个树结构，所有的节点名和属性名都是由浏览器的HTML解析器(除了SVG元素)小写的。ViewCompiler遍历树中的每个DOM节点，并检查节点的名称是否与自定义元素的名称匹配。

现在，我们不会详细讨论发现自定义元素时会发生什么，因为对于绑定系统来说，元素是自定义的还是内置的并不重要。除了检查自定义元素外，ViewCompiler还要求BindingLanguage实现检查每个节点的属性和内容。Aurelia附带的[standard BindingLanguage implementation](https://github.com/aurelia/templating-binding/blob/master/src/binding-language.js)会查找名称以`.bind`, `.to-view`, `.delegate`, `.trigger`结尾或以`ref`开头的属性，等等。这些属性后缀和前缀称为“绑定命令”。标准BindingLanguage还检查元素的内容，以查找字符串插值表达式。当发现绑定命令或字符串插值表达式时，属性的值(文本)或插值表达式的内容(也是文本)被发送到绑定系统的[Parser](https://github.com/aurelia/binding/blob/master/src/parser.js)(语法分析器)。

如果您正确地编写了绑定表达式，那么解析器将能够标记文本(将其分成一系列有趣的部分)并将标记转换为抽象语法树(AST)。AST是JavaScript绑定表达式的对象表示。我们马上要深入研究AST。我们马上要深入研究AST。现在需要了解的重要一点是，到目前为止，ViewCompiler已经收集了关于模板中每个绑定表达式的大量信息。它知道DOM元素的名称、要绑定到的元素属性的名称(如果适用)、绑定命令(`bind`/`trigger`/etc)，并且它具有JavaScript绑定表达式的AST表示。所有这些部分都用于构造`BindingExpression`，它是创建绑定实例的工厂。

一旦 *ViewCompiler* 完全遍历了文档片段，它将返回`ViewFactory`实例，该实例将被缓存，直到需要它为止。Aurelia根据需要编译模板，因此在编译后可能很快就会使用它。将上面描述的所有工作和后续步骤编排在一起的是CompositionEngine和一个`Controller`，后者是模板模块的一部分。`CompositionEngine`是一个高级组件，它创建控制器并执行组合指令。组合指令是应用程序引导、路由和`<compose>`元素的结果。

它们告诉Aurelia用视图模型构建一个视图 。控制器稍微低一些。它“拥有”一个`View`及其相应的视图模型，并带我们通过`created`, `bind`, `attached`, `detached` and `unbind`（创建、绑定、附加、分离和取消绑定）组合生命周期事件，这些事件在组合视图和视图模型时发生，将视图注入DOM中，然后在不再需要时删除它。让我们逐一介绍这些步骤，重点关注数据绑定方面的情况。


将调用 *ViewFactory* 的创建`create`方法来创建`View`实例。在这个意义上，视图是一个JavaScript类，它具有用于所有组合生命周期事件的方法。创建视图需要几个步骤。首先，复制 *ViewCompiler* 的文档片段来创建最终将被注入DOM和数据绑定的元素。这是在使用 `@inject(Element)` 时给视图模型构造函数的元素。接下来，ViewCompiler将执行它的所有指令:创建自定义元素实例、自定义属性实例和绑定实例。


创建绑定实例是通过对每个BindingExpression调用 `createBinding` 方法来完成的，方法是传入与BindingExpression相关的特定DOM元素、自定义元素或自定义属性。`createBinding` 方法使用ObserverLocator为DOM元素和属性的组合定位适当的属性观察者。属性监控公开一个`subscribe`, `unsubscribe`, `getValue` and `setValue`接口。每个属性观察者都有针对特定类型的对象或元素及其各种属性的特定观察策略。例如，如果绑定输入元素的值属性，则有一个观察者实现将订阅输入的 `change` 和 `input`DOM事件。有了属性观测器在手，`createBinding`将实例化绑定实例，将属性观测器传递给绑定的构造函数。这个观察者称为绑定的`targetObserver`。其他一些项被传递给绑定的构造函数：DOM元素(称为绑定的`target`)、属性名、绑定模式(`to-view`/`two-way`/`one-time`)和最后但并非最不重要的绑定表达式的AST(称为绑定的`sourceExpression`)。


一旦 *ViewFactory* 的`create`方法完成了所有指令的执行和所有绑定的创建，它将实例化`View`，其构造函数将接收DOM元素、绑定、控制器和其他一些项。使用创建的视图，控制器可以执行视图的`bind` 方法，传递绑定上下文和覆盖上下文。绑定上下文和覆盖上下文元组被称为 `scope`—— 稍后详细介绍……


视图的 `bind` 方法循环遍历所有绑定实例并调用它们的 `bind` 方法，传入绑定上下文并覆盖上下文。这就是绑定的 `sourceExpression` 中的AST变得重要的地方。抽象语法树是绑定系统的核心。它是绑定的JavaScript表达式的对象表示。树中的每个节点都是一个非常特定于绑定的接口的实现。大约有20种不同的AST节点类型，每一种都是为特定类型的JavaScript表达式定制的。可以通过调用根AST节点的 `evaluate` 方法并传入  `scope` 来评估AST。AST中的每个节点都知道如何使用作用域计算其表达式的一部分，最终结果将是JavaScript表达式的值。

绑定通过计算 `sourceExpression` 获得模型值后，它通过调用 `targetObserver`'的 `setValue` 方法将这个值赋值给视图。接下来，绑定将检查其绑定模式。如果是一次性  `one-time` 的，就没有什么可做的了。如果是 `to-view` or `two-way`，则绑定将使用AST的 `connect` 方法订阅视图模型中的更改。AST中的每个节点都知道要观察哪个视图模型属性，并将使用 *ObserverLocator* 创建属性观察者来订阅属性更改事件。最后，如果绑定模式是`two-way`，绑定将调用`targetObserver`的订阅方法。由于`two-way`, `one-way` and `to-view`都需要观察者，所以在绑定期间需要添加属性拦截器来检测更改。默认情况下，被拦截的属性将被添加到视图模型或视图模型属性中，如果对象上还没有定义，则使用 `undefined`值(例如`value.bind="foo"` 将在视图模型上创建`foo` 属性，如果它不存在，`value.bind="foo"`。如果' bar '属性还没有在`foo` 上定义，`bar`将在`foo` 对象上创建`bar`属性)。


此时，视图和视图模型是数据绑定的。当模型中发生更改时，在连接AST `connect`时创建的属性观察者将触发更改事件，通过调用`targetObserver.setValue`触发绑定更新目标。当视图中发生更改时，称为`targetObserver`的属性观察者将通过调用sourceExpression触发绑定`sourceExpression.assign(scope, value)`。剩下的就是控制器将视图附加`attach`到DOM上。稍后，当不再需要该视图时，它将与DOM分离，并调用`unbind`，取消`detached`所有视图的绑定，从而取消所有属性观察者的订阅。


## 2.Abstract Syntax Tree 抽象语法树

抽象语法树是绑定系统的关键部分，我们已经讨论了如何使用它，但是如果您能够可视化它，就更容易理解它。下面是一个演示，您可以在其中输入任何绑定表达式并查看解析表达式后得到的AST。点击按钮来查看我们放在一起的一些示例表达式，或者输入你自己的。

[示例点here](https://codesandbox.io/s/94wlo6v6lp?from-embed)

## 3.Binding Context / Scope 绑定上下文/作用域

aurelia中的“作用域 Scope”由两个对象组成:`bindingContext`(几乎总是一个视图模型实例)和 `overrideContext` (可以认为是 bindingContext 的“覆盖层”)。在 bindingContext 中，overrideContext的属性“覆盖”对应的属性。实际上，overrideContext上的一个属性“隐藏 hiding”了下面bindingContext上的一个属性。大多数情况下，overrideContext存储额外的上下文属性，如`$index`, `$first`, `$last`, `$odd`, `$even` (如果是repeat)、`$event`(当事件绑定触发时)等。

overrideContext的另一个目的是支持范围遍历。overrideContext还具有对父overrideContext及其对应的bindingContext的引用，这使绑定系统能够在计算绑定表达式时根据需要遍历范围。如果您已经使用了一段时间的Aurelia，您可能记得需要使用`$parent` 来访问外部范围。它不再需要了，因为绑定系统知道如何自动遍历范围(读:遍历bindingContext/overrideContext层次结构)。

什么时候创建bindingContexts ?不是经常。

重复是惟一能动态创建它们的东西。其余时间，bindingContext是您期望的视图模型实例。另一方面，overridecontext是在需要的基础上创建的，通常是在需要组合视图和视图模型的时候。