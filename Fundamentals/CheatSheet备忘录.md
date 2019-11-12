原文链接：https://aurelia.io/docs/fundamentals/cheat-sheet

* [1\.配置和启动](#1%E9%85%8D%E7%BD%AE%E5%92%8C%E5%90%AF%E5%8A%A8)
    * [Promises in Edge](#promises-in-edge)
* [2\.Creating Components 创建组件](#2creating-components-%E5%88%9B%E5%BB%BA%E7%BB%84%E4%BB%B6)
    * [组件的生命周期](#%E7%BB%84%E4%BB%B6%E7%9A%84%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F)
* [3\.Dependency Injection 依赖注入](#3dependency-injection-%E4%BE%9D%E8%B5%96%E6%B3%A8%E5%85%A5)
    * [Available Resolvers 可用的解析器](#available-resolvers-%E5%8F%AF%E7%94%A8%E7%9A%84%E8%A7%A3%E6%9E%90%E5%99%A8)
* [4\.Templating Basics 模板基础知识](#4templating-basics-%E6%A8%A1%E6%9D%BF%E5%9F%BA%E7%A1%80%E7%9F%A5%E8%AF%86)
    * [Invalid Table Structure When Dynamically Creating Tables 动态创建表时无效的表结构](#invalid-table-structure-when-dynamically-creating-tables-%E5%8A%A8%E6%80%81%E5%88%9B%E5%BB%BA%E8%A1%A8%E6%97%B6%E6%97%A0%E6%95%88%E7%9A%84%E8%A1%A8%E7%BB%93%E6%9E%84)
* [5\.Databinding](#5databinding)
  * [bind, one\-way, two\-way &amp; one\-time](#bind-one-way-two-way--one-time)
  * [delegate, trigger](#delegate-trigger)
  * [call](#call)
  * [ref](#ref)
  * [String Interpolation 字符串插入](#string-interpolation-%E5%AD%97%E7%AC%A6%E4%B8%B2%E6%8F%92%E5%85%A5)
  * [Binding to Select Elements 绑定到选择的元素](#binding-to-select-elements-%E7%BB%91%E5%AE%9A%E5%88%B0%E9%80%89%E6%8B%A9%E7%9A%84%E5%85%83%E7%B4%A0)
  * [Binding Radios](#binding-radios)
  * [Binding innerHTML and textContent](#binding-innerhtml-and-textcontent)
  * [Binding Style](#binding-style)
  * [Declaring Computed Property Dependencies 计算属性声明依赖项](#declaring-computed-property-dependencies-%E8%AE%A1%E7%AE%97%E5%B1%9E%E6%80%A7%E5%A3%B0%E6%98%8E%E4%BE%9D%E8%B5%96%E9%A1%B9)
* [6\.Templating View Resources 模板化视图资源](#6templating-view-resources-%E6%A8%A1%E6%9D%BF%E5%8C%96%E8%A7%86%E5%9B%BE%E8%B5%84%E6%BA%90)
* [7\.Routing](#7routing)
  * [Route Pattern Options 路由模式选项](#route-pattern-options-%E8%B7%AF%E7%94%B1%E6%A8%A1%E5%BC%8F%E9%80%89%E9%A1%B9)
  * [The Route Screen Activation Lifecycle 路由筛选激活生命周期](#the-route-screen-activation-lifecycle-%E8%B7%AF%E7%94%B1%E7%AD%9B%E9%80%89%E6%BF%80%E6%B4%BB%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F)
  * [Configuring PushState 配置推送状态](#configuring-pushstate-%E9%85%8D%E7%BD%AE%E6%8E%A8%E9%80%81%E7%8A%B6%E6%80%81)
  * [Reusing an Existing View Model](#reusing-an-existing-view-model)
* [8\.Custom Attributes](#8custom-attributes)
* [9\.Custom Elements](#9custom-elements)
  * [Custom Element Without View\-Model Declaration](#custom-element-without-view-model-declaration)
  * [Custom Element Variable Binding](#custom-element-variable-binding)
  * [Custom Element Options](#custom-element-options)
  * [SVG Elements](#svg-elements)
  * [Template Parts](#template-parts)
  * [Observable decorator](#observable-decorator)
* [10\.The Event Aggregator](#10the-event-aggregator)


忘了绑定的语法？ 需要知道如何创建自定义属性？ 本文包含诸如此类问题的答案以及一系列常见任务的快速代码段。

## 1.配置和启动
较旧的浏览器引导
``` html?linenums
<script src="jspm_packages/system.js"></script>
  <script src="config.js"></script>
  <script>
    SystemJS.import('aurelia-polyfills').then(function() {
      return SystemJS.import('webcomponents/webcomponentsjs/MutationObserver');
    }).then(function() {
      SystemJS.import('aurelia-bootstrapper');
    });
  </script>
  
```

>#### Promises in Edge
>Currently, the Edge browser has a serious performance problem with its Promise implementation. This deficiency can greatly increase startup time of your app. If you are targeting the Edge browser, it is highly recommended that you use the [bluebird promise](http://bluebirdjs.com/docs/getting-started.html) library to replace Edge's native implementation. You can do this by simply referencing the library prior to loading system.js.

**标准的启动配置**

``` javascript
export function configure(aurelia) {
    aurelia.use
      .standardConfiguration()
      .developmentLogging();
  
    aurelia.start().then(() => aurelia.setRoot());
  }
```
**显式启动配置**
  

``` javascript
  import {LogManager} from 'aurelia-framework';
  import {ConsoleAppender} from 'aurelia-logging-console';
  
  LogManager.addAppender(new ConsoleAppender());
  LogManager.setLevel(LogManager.logLevel.debug);
  
  export function configure(aurelia) {
    aurelia.use
      .defaultBindingLanguage()
      .defaultResources()
      .history()
      .router()
      .eventAggregator();
  
    aurelia.start().then(() => aurelia.setRoot('app', document.body));
  }
```
**配置功能**
 

``` javascript
  export function configure(aurelia) {
    aurelia.use
      .standardConfiguration()
      .developmentLogging()
      .feature('feature-name', featureConfiguration);
  
    aurelia.start().then(() => aurelia.setRoot());
  }

```
**安装一个插件**

``` javascript
export function configure(aurelia) {
    aurelia.use
      .standardConfiguration()
      .developmentLogging()
      .plugin('plugin-name', pluginConfiguration);
  
    aurelia.start().then(() => aurelia.setRoot());
  }
``` 
## 2.Creating Components 创建组件

UI components consist of two parts: a view-model and a view. Simply create each part in its own file. Use the same file name but different file extensions for the two parts. For example: hello.js and hello.html.

UI组件由两部分组成:视图模型和视图。只需在自己的文件中创建每个部分。对这两个部分使用相同的文件名，但是不同的文件扩展名。例如:hello.js和hello.html。

**显式配置**

``` javascript
  import {useView} from 'aurelia-framework';
  
  @useView('./hello.html')
  export class Hello {
    ...
  }
  
```

#### 组件的生命周期

Components have a well-defined lifecycle:

1.  `constructor()` - The view-model's constructor is called first.

    首先调用view-model的构造函数

2.  `created(owningView: View, myView: View)` - If the view-model implements the `created` callback it is invoked next. At this point in time, the view has also been created and both the view-model and the view are connected to their controller. The created callback will receive the instance of the "owningView". This is the view that the component is declared inside of. If the component itself has a view, this will be passed second.

    如果view-model实现了创建的回调，则接下来调用它。此时，视图也已经创建，视图模型和视图都连接到它们的控制器。创建的回调将接收“owningView”的实例。这是组件在其中声明的视图。如果组件本身有一个视图，它将被第二次传递。
3.  `bind(bindingContext: Object, overrideContext: Object)` - Databinding is then activated on the view and view-model. If the view-model has a `bind` callback, it will be invoked at this time. The "binding context" to which the component is being bound will be passed first. An "override context" will be passed second. The override context contains information used to traverse the parent hierarchy and can also be used to add any contextual properties that the component wants to add. It should be noted that when the view-model has implemented the `bind` callback, the databinding framework will not invoke the changed handlers for the view-model's bindable properties until the "next" time those properties are updated. If you need to perform specific post-processing on your bindable properties, when implementing the `bind` callback, you should do so manually within the callback itself.

    然后在view和view-model上激活数据绑定。如果view-model有一个bind回调，那么它将在这个时候被调用。组件被绑定到的“绑定上下文”将首先被传递。第二个将传递一个“覆盖上下文”。覆盖上下文包含用于遍历父的层次结构信息,还可以用来添加组件想要添加的任何上下文属性。需要注意的是,当视图模型绑定回调实现,数据绑定框架的改变处理程序不会调用视图模型的绑定属性,直到这些属性更新的“下一个”时间。如果需要对可绑定属性执行特定的后处理，在实现绑定回调时，应该在回调本身内手动执行。
4.  `attached()` - Next, the component is attached to the DOM (in document). If the view-model has an `attached` callback, it will be invoked at this time.

    接下来，将组件附加到DOM(在文档中)。如果视图模型有一个附加的回调，它将在这个时候被调用。
5.  `detached()` - At some point in the future, the component may be removed from the DOM. If/When this happens, and if the view-model has a `detached` callback, this is when it will be invoked.

    在将来的某个时候，组件可能会从DOM中删除。如果/当这种情况发生时，如果视图模型有一个`detached`的分离回调，这就是调用它的时候。
6.  `unbind()` - After a component is detached, it's usually unbound. If your view-model has the `unbind` callback, it will be invoked during this process.

    组件被分离后，通常会解除绑定。如果您的视图模型具有`unbind`回调，那么它将在此过程中被调用。

## 3.Dependency Injection 依赖注入

**声明依赖性**
    
``` javascript
  import {autoinject} from 'aurelia-framework';
  import {Dep1} from 'dep1';
  import {Dep2} from 'dep2';
  
  @autoinject
  export class CustomerDetail {
    constructor(private dep1: Dep1, private dep2: Dep2){ }
  }
```

  
**Using Resolvers  使用解析器**
   
``` javascript
  import {Lazy, inject} from 'aurelia-framework';
  import {HttpClient} from 'aurelia-fetch-client';
  
  @inject(Lazy.of(HttpClient))
  export class CustomerDetail {
    constructor(private getHTTP: () => HttpClient){ }
  }
```
#### Available Resolvers 可用的解析器

*   `Lazy` - 注入一个函数，用于`延迟lazily`评估依赖关系。
    *   ex. `Lazy.of(HttpClient)`
*   `All` - 注入用提供的键注册的所有服务的数组。
    *   ex. `All.of(Plugin)`
*   `Optional` - 仅当类的实例已经存在于容器中时，才注入该类的实例;否则无效。
    *   ex. `Optional.of(LoggedInUser)`
*   `Parent` - 跳过从当前容器启动依赖项解析，而是在父容器上启动查找过程。
    *   ex. `Parent.of(MyCustomElement)`
*   `Factory` - 用于允许注入依赖项，也可以将数据传递给构造函数。
    *   ex. `Factory.of(CustomClass)`
*   `NewInstance` - 用于注入依赖项的新实例，而不考虑容器中的现有实例。
    *   ex. `NewInstance.of(CustomClass).as(Another)`

**Explicit Registration 显示注入**

``` javascript
  import {transient, autoinject} from 'aurelia-framework';
  import {HttpClient} from 'aurelia-fetch-client';
  
  @transient()
  @autoinject
  export class CustomerDetail {
    constructor(private http: HttpClient) { }
  }
```

##  4.Templating Basics 模板基础知识

**A Simple Template**

``` HTML
  <template>
    <div>Hello World!</div>
  </template>
```

**Requiring Resources 需要的资源**

``` HTML
  <template>
    <require from='nav-bar'></require>
  
    <nav-bar router.bind="router"></nav-bar>
  
    <div class="page-host">
      <router-view></router-view>
    </div>
  </template>
```
>#### Invalid Table Structure When Dynamically Creating Tables 动态创建表时无效的表结构
>When the browser loads in the template it very helpfully validates the structure of the HTML, notices that you have an invalid tag inside your table definition, and very unhelpfully removes it for you before Aurelia even has a chance to examine your template.
>
>当浏览器加载模板时，它会非常有帮助地验证HTML的结构，注意到您的表定义中有一个无效的标记，在Aurelia有机会检查模板之前，它会非常没有帮助地为您删除它。

>警告：#### webpack
>
>Dynamic compose as used below does not work when using webpack. It is suggested to bind to a property on the viewmodel `template: PLATFORM.moduleName("./template_.html")` and then use it like so `<tr repeat.for="r of ['A','B','A','B']" as-element="compose" view.bind='template'>`
>
>使用webpack时，下面使用的动态组合不能工作。建议绑定到viewmodel上的属性`template: PLATFORM.moduleName("./template_.html")`然后像这样使用它`<tr repeat.for="r of ['A','B','A','B']" as-element="compose" view.bind='template'>`
  
使用`as-element`属性可以确保我们在加载时拥有一个有效的HTML表结构，但是我们告诉Aurelia将其内容视为不同的标记。

**Compose an existing object instance with a view. 用视图组合现有对象实例。**
    
```
  <template>
    <table>
      <tr repeat.for="r of ['A','B','A','B']" as-element="compose" view='./template_${r}.html'>
    </table>
  <template>
```  

  
对于上面的示例，我们可以根据数据的元素以编程方式选择嵌入的模板：

**template_A.html**
 
``` html
  <template>
    <td>I'm an A Row</td><td>Col 2A</td><td>Col 3A</td>
  </template>
```

**template_B.html**

``` HTML
  <template>
    <td>I'm an B Row</td><td>Col 2B</td><td>Col 3B</td>
  </template>
```

请注意，当使用了`containerless`属性时，容器将在浏览器加载DOM元素之后被剥离，因此不能使用此方法将非html兼容的结构转换为兼容的结构！
  
**Illegal Table Code 非法表代码**

``` HTML
  <template>
    <table>
      <template repeat.for="customer of customers">
        <tr>
          <td>${customer.fullName}</td>
        </tr>
      </template>
    </table>
  </template>
```

**Correct Table Code 正确的表代码**

``` HTML
  <template>
    <table>
      <tr repeat.for="customer of customers">
        <td>${customer.fullName}</td>
      </tr>
    </table>
  </template>
```

**Illegal Select Code 非法选择代码**

``` HTML
  <template>
    <select>
      <template repeat.for="customer of customers">
        <option>...</option>
      </template>
    </select>
  </template>
```

**Correct Select Code 正确选择代码**

``` HTML
  <template>
    <select>
      <option repeat.for="customer of customers">...</option>
    </select>
  </template>
```

## 5.Databinding

### bind, one-way, two-way & one-time

**Use on any HTML attribute.**

*   `.bind` - 使用默认绑定。除了使用双向绑定的表单控件外，其他都使用单向绑定。
*   `.one-way` - 向一个方向流动数据:从view-model到view。
*   `.two-way` - 数据以两种方式流动:从view-model到view，从view到view-model。
*   `.one-time` - 只呈现一次数据，但在初始呈现后不同步更改。

**Data Binding Examples**
``` HTML
  <template>
    <input type="text" value.bind="firstName">
    <input type="text" value.two-way="lastName">
  
    <a href.one-way="profileUrl">View Profile</a>
  </template>
```

>提醒：目前不支持可绑定对象的继承。对于`class B extends A` 和 `B`用作自定义元素/属性`@bindable`属性的用例，不能仅在`class A`上定义。如果使用继承，则应在实例化的类上定义`@bindable`属性。

### delegate, trigger

用于任何本机或自定义DOM事件。(不要在事件名称中包含“on”前缀。)

*   `.trigger` - 将事件处理程序直接附加到元素。当事件触发时，将调用表达式。
*   `.delegate` - 将单个事件处理程序附加到处理指定类型的所有事件的文档(或最接近的影子DOM边界)，将它们正确地发送回其原始目标，以便调用关联的表达式。

>提醒：The `$event` value can be passed as an argument to a `delegate` or `trigger` function call if you need to access the event object.
>提醒：如果需要访问事件对象，可以将`$event`值作为参数传递给`委托`或`触发器`函数调用。

**Event Binding Examples**

``` HTML
  <template>
    <button click.trigger="save()">Save</button>
    <button click.delegate="save($event)">Save</button>
  </template>
```

  
### call

Passes a function reference.

传递一个函数引用。  

**Call Example**
``` HTML
  <template>
    <button my-attribute.call="sayHello()">Say Hello</button>
  </template>
```

### ref

Creates a reference to an HTML element, a component or a component's parts.

创建对HTML元素、组件或组件部件的引用。

*   `ref="someIdentifier"` or `element.ref="someIdentifier"` - 在DOM中创建对HTML元素的引用。
*   `attribute-name.ref="someIdentifier"`-创建对自定义属性的view-model的引用。
*   `view-model.ref="someIdentifier"`- 创建对自定义元素的view-model的引用。
*   `view.ref="someIdentifier"`- 创建对自定义元素的view实例的引用(不是HTML元素)。
*   `controller.ref="someIdentifier"`- 创建对自定义元素的控制器实例的引用。

**Ref Example**

``` HTML
  <template>
    <input type="text" ref="name"> ${name.value}
  </template>
```

### String Interpolation 字符串插入

Used in an element's content. Can be used inside attributes, particularly useful in the `class` and `css` attributes.

用于元素的内容中。可以在属性中使用，在`class`和`css`属性中特别有用。

**String Interpolation Example**

``` HTML
  <template>
    <span>${fullName}</span>
    <div class="dot ${color} ${isHappy ? 'green' : 'red'}"></div>
  </template>
```
### Binding to Select Elements 绑定到选择的元素

A typical select element is rendered using a combination of `value.bind` and `repeat`. You can also bind to arrays of objects and synchronize based on an id (or similar) property.

典型的select元素是使用值的组合来呈现的。`value.bind`和 `repeat`。您还可以绑定到对象数组并基于id(或类似的)属性进行同步。

**Basic Select**
   
``` HTML
  <template>
    <select value.bind="favoriteColor">
      <option>Select A Color</option>
      <option repeat.for="color of colors" value.bind="color">${color}</option>
    </select>
  </template>
```

  
**Select with Object Array 使用对象数组选择**

``` HTML
  <template>
    <select value.bind="employeeOfTheMonth">
      <option>Select An Employee</option>
      <option repeat.for="employee of employees" model.bind="employee">${employee.fullName}</option>
    </select>
  </template>
```

**Select with Object Id Sync 使用同步对象Id选择**

``` HTML
  <template>
    <select value.bind="employeeOfTheMonthId">
      <option>Select An Employee</option>
      <option repeat.for="employee of employees" model.bind="employee.id">${employee.fullName}</option>
    </select>
  </template>
```

  

  
**Basic Multi-Select 基本多选**

``` HTML
  <template>
    <select value.bind="favoriteColors" multiple>
      <option repeat.for="color of colors" value.bind="color">${color}</option>
    </select>
  </template>
```

**Multi-Select with Object Array 使用对象数组多选**  

``` HTML
  <template>
    <select value.bind="favoriteEmployees" multiple>
      <option repeat.for="employee of employees" model.bind="employee">${employee.fullName}</option>
    </select>
  </template>
```

### Binding Radios
  
**Basic Radios**
   
``` HTML
  <template>
    <label repeat.for="color of colors">
      <input type="radio" name="clrs" value.bind="color" checked.bind="$parent.favoriteColor">
      ${color}
    </label>
  </template>
```

**Radios with Object Arrays 对象数组的radios**

``` HTML
  <template>
    <label repeat.for="employee of employees">
      <input type="radio" name="emps" model.bind="employee" checked.bind="$parent.employeeOfTheMonth">
      ${employee.fullName}
    </label>
  </template>
```

  
**Radios with a Boolean** 

``` HTML
  <template>
    <label><input type="radio" name="tacos" model.bind="null" checked.bind="likesTacos">Unanswered</label>
    <label><input type="radio" name="tacos" model.bind="true" checked.bind="likesTacos">Yes</label>
    <label><input type="radio" name="tacos" model.bind="false" checked.bind="likesTacos">No</label>
  </template>
```
>警告：如果要将方法附加到复选框上，则不能在复选框上使用`click.delegate`。你需要使用`change.delegate`。
  
**Checkboxes with an Array**

```html
<template>
    <label repeat.for="color of colors">
      <input type="checkbox" value.bind="color" checked.bind="$parent.favoriteColors" >
      ${color}
    </label>
  </template>
```

**Checkboxes with an Array of Objects**

```html
  <template>
    <label repeat.for="employee of employees">
      <input type="checkbox" model.bind="employee" checked.bind="$parent.favoriteEmployees">
      ${employee.fullName}
    </label>
  </template>
```

**Checkboxes with Booleans**

```html
  <template>
    <li><label><input type="checkbox" checked.bind="wantsFudge">Fudge</label></li>
    <li><label><input type="checkbox" checked.bind="wantsSprinkles">Sprinkles</label></li>
    <li><label><input type="checkbox" checked.bind="wantsCherry">Cherry</label></li>
  </template>
```
### Binding innerHTML and textContent

**Binding innerHTML**
```html
  <template>
    <div innerhtml.bind="htmlProperty | sanitizeHTML"></div>
    <div innerhtml="${htmlProperty | sanitizeHTML}"></div>
  </template>
```

>**禁止**：
>
>始终使用HTML清理。我们提供了一个简单的转换器，可以使用。建议您使用更完整的HTML杀毒工具，如[sanitize-html](https://www.npmjs.com/package/sanitize-html)。

>提醒：使用`innerhtml`属性绑定只是设置元素的`innerHTML`属性。标记不会通过Aurelia的模板系统。绑定表达式和require元素将不被计算。

**Binding textContent**

```html
  <template>
    <div textcontent.bind="stringProperty"></div>
    <div textcontent="${stringProperty}"></div>
  </template>
```
**Two-Way Editable textContent**

```html
  <template>
    <div textcontent.bind="stringProperty" contenteditable="true"></div>
  </template>
```

### Binding Style

可以将css字符串或对象绑定到元素的`style`属性。在执行字符串插值时使用`style`属性的别名`css`，以确保应用程序与Internet Explorer兼容。

**Style Binding Data**

```javascript
  export class StyleData {
    constructor() {
      this.styleString = 'color: red; background-color: blue';
  
      this.styleObject = {
        color: 'red',
        'background-color': 'blue'
      };
    }
  }
```

**Style Binding View**

```html
  <template>
    <div style.bind="styleString"></div>
    <div style.bind="styleObject"></div>
  </template>
```
**Illegal Style Interpolation 非法样式插值**

```html
   <template>
    <div style="width: ${width}px; height: ${height}px;"></div>
  </template>
```
**Legal Style Interpolation 合法样式插值**

```html
  <template>
    <div css="width: ${width}px; height: ${height}px;"></div>
  </template>
```
### Declaring Computed Property Dependencies 计算属性声明依赖项

**Computed Properties**
 
```Javascript
import {computedFrom} from 'aurelia-framework';
  
  export class Person {
    firstName = 'John';
    lastName = 'Doe';
  
    @computedFrom('firstName', 'lastName')
    get fullName(){
      return `${this.firstName} ${this.lastName}`;
    }
  }
  

```

## 6.Templating View Resources 模板化视图资源

**Conditionally displays an HTML element. 有条件地显示HTML元素。**
```html
<template>
	<div show.bind="isSaving" class="spinner"></div>
</template>
```

**Conditionally add/remove an HTML element 有条件地添加/删除HTML元素**
```html
<template>
    <div if.bind="isSaving" class="spinner"></div>
</template>
```

**Conditionally add/remove a group of elements 有条件地添加/删除一组元素**
```html
<template>
    <input value.bind="firstName">
  
    <template if.bind="hasErrors">
        <i class="icon error"></i>
        ${errorMessage}
    </template>
</template>
```
**creating new component instance every time condition changes 每当条件发生变化时创建新的组件实例**
```html
  <template>
    <my-custom-element if="condition.bind: showMessage; cache: false"></my-custom-element>
  </template>
```
**Render an array with a template 使用模板渲染数组**
```html
  <template>
    <ul>
      <li repeat.for="customer of customers">${customer.fullName}</li>
    </ul>
  </template>
```
**Render a map with a template 使用模板渲染地图**
```html
  <template>
    <ul>
      <li repeat.for="[id, customer] of customers">${id} ${customer.fullName}</li>
    </ul>
  </template>
  
```
**Render a template N times 渲染模板N次**
```html
  <template>
    <ul>
      <li repeat.for="i of rating">*</li>
    </ul>
  </template>
```

上下文项在重复模板中可用:
*   `$index` - 数组中该项的索引。
*   `$first` - 如果项是数组中的第一项，则为true.
*   `$last` - 如果该项是数组中的最后一项，则为true.
*   `$even` - 如果项目的索引是偶数，则为true。
*   `$odd` - 如果项目的索引是奇数，则为true.

>提醒：
>
>无容器模板控制器
>
>`if`和`repeat`属性通常放在它们所影响的HTML元素上。但是，您也可以使用 `template` 模板标记来对没有父元素的元素集合进行分组，并将`if`或`repeat`放在 `template` 模板元素上。

**Dynamically render UI into the DOM based on data  据数据动态地将UI呈现到DOM中**
```html
  <template repeat.for="item of items">
    <compose model.bind="item" view-model="widgets/${item.type}"></compose>
  </template>
```
**Composing a view only, inheriting the parent binding context 仅组合视图，继承父绑定上下文**
```html
  <template repeat.for="item of items">
    <compose view="my-view.html"></compose>
  </template>
```
**Compose an existing object instance with a view 用视图组合现有对象实例**
```html
  <template>
    <div repeat.for="item of items">
      <compose view="my-view.html" view-model.bind="item">
    </div>
  </template>
```


## 7.Routing

**Basic Route Configuration**
```javascript
export class App {
    configureRouter(config, router) {
      this.router = router;
      config.title = 'Aurelia';
      config.map([
        { route: ['', 'home'],       name: 'home',       moduleId: 'home/index' },
        { route: 'users',            name: 'users',      moduleId: 'users/index',   nav: true },
        { route: 'users/:id/detail', name: 'userDetail', moduleId: 'users/detail' },
        { route: 'files*path',       name: 'files',      moduleId: 'files/index',   href:'#files',   nav: true }
      ]);
    }
  }
```
### Route Pattern Options 路由模式选项

*   static routes
    *   ie 'home' - 字符串完全匹配。
*   parameterized（参数化） routes
    *   ie 'users/:id/detail' - 匹配字符串，然后解析`id`参数。视图模型的`activate`回调将通过一个对象调用，该对象的`id`属性设置为从url提取的值。
*   wildcard（通配符） routes
    *   ie 'files*path' - 匹配字符串和其后的任何内容。您的视图模型的`activate`回调将被一个对象调用，该对象的`path`属性设置为通配符的值.

### The Route Screen Activation Lifecycle 路由筛选激活生命周期

*   `canActivate(params, routeConfig, navigationInstruction)` - Implement this hook if you want to control whether or not your view-model _can be navigated to_. Return a boolean value, a promise for a boolean value, or a navigation command.

	如果你想控制你的视图模型是否可以被导航到，实现这个钩子。返回布尔值、布尔值的promise承诺或导航命令。
*   `activate(params, routeConfig, navigationInstruction)` - Implement this hook if you want to perform custom logic just before your view-model is displayed. You can optionally return a promise to tell the router to wait to bind and attach the view until after you finish your work.

	如果您想在视图模型显示之前执行自定义逻辑，请实现此钩子。您可以选择返回一个promise承诺，告诉路由器等待绑定和附加视图，直到您完成工作。
*   `canDeactivate()` - Implement this hook if you want to control whether or not the router _can navigate away_ from your view-model when moving to a new route. Return a boolean value, a promise for a boolean value, or a navigation command.

	实现这个钩子，如果你想控制路由器是否可以从你的视图模型导航到一个新的路由。返回布尔值、布尔值的promise承诺或导航命令。
*   `deactivate()` - Implement this hook if you want to perform custom logic when your view-model is being navigated away from. You can optionally return a promise to tell the router to wait until after you finish your work.

	如果你想在视图模型被导航时执行自定义逻辑，可以实现这个钩子。您可以选择返回一个promise承诺，告诉路由器等待，直到您完成您的工作。

>警告：Root筛选激活
>
>与映射的路由不同，根的视图模型只能访问`activate()`钩子。但是，这也可以用来实现逻辑，通过返回一个布尔值的promise来附加组件。

`params`对象将为解析的路由的每个参数提供一个属性，也为每个查询字符串值提供一个属性。`routeConfig`将是您设置的原始路由配置对象。`routeConfig`还将有一个新的`navModel`属性，可以用来更改视图模型加载的数据的文档标题。例如：

**Route Params and NavModel 路由参数和NavModel**
```javascript
  import {autoinject} from 'aurelia-framework';
  import {UserService} from './user-service';
  
  @inject(UserService)
  export class UserEditScreen {
    constructor(userService) {
      this.userService = userService;
    }
  
    activate(params, routeConfig) {
      return this.userService.getUser(params.id)
        .then(user => {
          routeConfig.navModel.setTitle(user.name);
        });
    }
  }
```
**Conventional Routing 传统路由**
```javascript
export class App {
    configureRouter(config){
      config.mapUnknownRoutes(instruction => {
        //check instruction.fragment
        //return moduleId
      });
    }
  }
```
**Customizing the Navigation Pipeline 自定义导航管道**
```javascript
  import {Redirect} from 'aurelia-router';
  
  export class App {
    configureRouter(config) {
      config.title = 'Aurelia';
      config.addPipelineStep('authorize', AuthorizeStep);
      config.map([
        { route: ['welcome'],    name: 'welcome',       moduleId: 'welcome',      nav: true, title:'Welcome' },
        { route: 'flickr',       name: 'flickr',        moduleId: 'flickr',       nav: true, auth: true },
        { route: 'child-router', name: 'childRouter',   moduleId: 'child-router', nav: true, title:'Child Router' },
        { route: '', redirect: 'welcome' }
      ]);
    }
  }
  
  class AuthorizeStep {
    run(navigationInstruction, next) {
      if (navigationInstruction.getAllInstructions().some(i => i.config.auth)) {
        var isLoggedIn = /* insert magic here */false;
        if (!isLoggedIn) {
          return next.cancel(new Redirect('login'));
        }
      }
  
      return next();
    }
  }
```

### Configuring PushState 配置推送状态

Add [a base tag](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/base) to the head of your html document. If you're using JSPM, you will also need to configure it with a `baseURL` corresponding to your base tag's `href`. Finally, be sure to set the `config.options.root` to match your base tag's setting.

在html文档的头部添加一个`基本标记`。如果使用JSPM，还需要使用与基本标记的`href`对应的`baseURL`来配置它。最后，一定要设置`config.options.root`以匹配基本标记的设置。

```javascript
export class App {
    configureRouter(config) {
      config.title = 'Aurelia';
      config.options.pushState = true;
      config.options.root = '/';
      config.map([
        { route: ['welcome'],    name: 'welcome',     moduleId: 'welcome',      nav: true, title:'Welcome' },
        { route: 'flickr',       name: 'flickr',      moduleId: 'flickr',       nav: true, auth: true },
        { route: 'child-router', name: 'childRouter', moduleId: 'child-router', nav: true, title:'Child Router' },
        { route: '',             redirect: 'welcome' }
      ]);
    }
  }
```

>PushState需要服务器端支持。不要忘记适当地配置您的服务器。

### Reusing an Existing View Model 重用现有的视图模型

Since the view model's navigation lifecycle is called only once, you may have problems recognizing that the user switched the route from `Product A` to `Product B` (see below). To work around this issue implement the method `determineActivationStrategy` in your view model and return hints for the router about what you'd like to happen. Available return values are `replace` and `invoke-lifecycle`. Remember, "lifecycle" refers to the navigation lifecycle.

**Router View Model Activation Control 路由器视图模型激活控制**

```javascript
  //app.js
  
  export class App {
    configureRouter(config) {
      config.title = 'Aurelia';
      config.map([
        { route: 'product/a',    moduleId: 'product',     nav: true },
        { route: 'product/b',    moduleId: 'product',     nav: true },
      ]);
    }
  }
  
  //product.js
  
  import {activationStrategy} from 'aurelia-router';
  
  export class Product {
    determineActivationStrategy() {
      return activationStrategy.replace;
    }
  }
  
```

>或者，如果策略总是相同的，并且您不希望它出现在您的视图模型代码中，那么您可以将`activationStrategy`属性添加到您的路由配置中。

### Rendering multiple ViewPorts

>如果你没有命名一个`router-view`，它会在`'default'`下可用。

**Multi-ViewPort View**
```html
 <template>
    <div class="page-host">
      <router-view name="left"></router-view>
    </div>
    <div class="page-host">
      <router-view name="right"></router-view>
    </div>
  </template>
  
```
**Multi-ViewPort View-Model**
```javascript
  export class App {
    configureRouter(config){
      config.map([{
        route: 'edit',
          viewPorts: {
            left: {
              moduleId: 'editor'
            },
            right: {
              moduleId: 'preview'
            }
          }
        }]);
    }
  }
```
### Generating Route URLs

**Generate Route URLs in Code**
```javascript
router.generate('routeName', { id: 123 });
  
```

**Navigating to a Generated Route**

```javascript
router.navigateToRoute('routeName', { id: 123 })

```
**Rendering an Anchor for a Route**
```html
<template>
    <a route-href="route: routeName; params.bind: { id: user.id }">${user.name}</a>
  </template>
```
**Bypassing the Router for a Link**
```
  <template>
    <a href="/my-page" router-ignore>Click to load my-page from server</a>
  </template>
 
```

## 8.Custom Attributes

**Simple Attribute Declaration**

```javascript
import {inject, customAttribute, DOM} from 'aurelia-framework';
  
  @customAttribute('my-attribute')
  @inject(DOM.Element)
  export class Show {
    constructor(element) {
      this.element = element;
    }
  
    valueChanged(newValue, oldValue) {
      ...
    }
  }
```
**Simple Attribute Use**

```html
<template>
    <div my-attribute="literal value"></div>
    <div my-attribute.bind="an.expression"></div>
  </template>
  

```
**Options Attribute Declaration**

```javascript
import {customAttribute, bindable} from 'aurelia-framework';
  
  @customAttribute('my-attribute')
  export class MyAttribute {
    @bindable foo;
    @bindable bar;
  
    fooChanged(newValue, oldValue) { ... }
  
    barChanged(newValue, oldValue) { ... }
  }
  

```
**Options Attribute Use**
```html
 <template>
    <div my-attribute="foo: literal value; bar.bind: an.expression"></div>
  </template>
  

  
```
**Dynamic Option Attribute Declaration**
```javascript
import {customAttribute, dynamicOptions} from 'aurelia-framework';
  
  @customAttribute('my-attribute')
  @dynamicOptions
  export class MyAttribute {
    propertyChanged(propertyName, newValue, oldValue) { ... }
  }
  

```

**Dynamic Option Attribute Use**
```html
<template>
    <div my-attribute="foo: literal value; bar.bind: an.expression"></div>
  </template>
  

```
**Bindable Signature (Showing Defaults)**
```javascript
bindable({
    name:'myProperty',
    attribute:'my-property',
    changeHandler:'myPropertyChanged',
    defaultBindingMode: bindingMode.oneWay,
    defaultValue: undefined
  })
  

```
**Template Controller Attribute Declaration**
```javascript
  import {BoundViewFactory, ViewSlot, customAttribute, templateController, inject} from 'aurelia-framework';
  
  @customAttribute('naive-if')
  @templateController
  @inject(BoundViewFactory, ViewSlot)
  export class NaiveIf {
    constructor(viewFactory, viewSlot) {
      this.viewFactory = viewFactory;
      this.viewSlot = viewSlot;
    }
  
    valueChanged(newValue) {
      if (newValue) {
        let view = this.viewFactory.create();
        this.viewSlot.add(view);
      } else {
        this.viewSlot.removeAll();
      }
    }
  }
```
**Template Controller Attribute Use**
```html
  <template>
    <div naive-if.bind="an.expression"></div>
  
    <template naive-if.bind="an.expression">
      <div></div>
      <div></div>
    </template>
  </template>
```

## 9.Custom Elements
**Custom Element View-Model Declaration**

```javascript
  import {customElement, bindable} from 'aurelia-framework';
  
  @customElement('say-hello')
  export class SayHello {
    @bindable to;
  
    speak(){
      alert(`Hello ${this.to}!`);
    }
  }

```
**Custom Element View Declaration**
```html
 <template>
      <button click.trigger="speak()">Say Hello To ${to}</button>
  </template>
```
**Custom Element Use**
```html
  <template>
    <require from="say-hello"></require>
  
    <input type="text" ref="name">
    <say-hello to.bind="name.value"></say-hello>
  </template>
```
### Custom Element Without View-Model Declaration

Aurelia will not search for a JavaScript file if you reference a component with an .html extension.

**Declare Custom Element Without View-Model With Binding**
```html
 <template bindable="greeting,name">
      Say ${greeting} To ${name}
  </template>
```
**Add Global Custom Element Without View-Model**
```javascript
aurelia.use.globalResources('./js-less-component.html');
  
```
**Use Custom Element Without View-Model**
```html
<require from="./js-less-component.html"></require>
  
<js-less-component greeting="Hello" name.bind="someProperty"></js-less-component>

```
### Custom Element Variable Binding

It's worth noting that when binding variables to custom elements, use camelCase inside the custom element's View-Model, and dash-case on the html element. See the following example:

**Custom Element View-Model Declaration**

```javascript
  import {bindable} from 'aurelia-framework';
  
  export class SayHello {
    @bindable to;
    @bindable greetingCallback;
  
    speak(){
      this.greetingCallback(`Hello ${this.to}!`);
    }
  }
```
**Custom Element Use**
```html
  <template>
    <require from="./say-hello"></require>
  
    <input type="text" ref="name">
    <say-hello to.bind="name.value" greeting-callback.call="doSomething($event)"></say-hello>
  </template>
  
```
### Custom Element Options

*   `@children(selector)` - Decorates a property to create an array on your class that has its items automatically synchronized based on a query selector against the element's immediate child content. Does not work with `@containerless()`, see below.
*   `@child(selector)` - Decorates a property to create a reference to a single immediate child content element. Does not work with `@containerless()`, see below.
*   `@processContent(false|Function)` - Tells the compiler that the element's content requires special processing. If you provide `false` to the decorator, the compiler will not process the content of your custom element. It is expected that you will do custom processing yourself. But, you can also supply a custom function that lets you process the content during the view's compilation. That function can then return true/false to indicate whether or not the compiler should also process the content. The function takes the following form `function(compiler, resources, node, instruction):boolean`
*   `@useView(path)` - Specifies a different view to use.
*   `@noView()` - Indicates that this custom element does not have a view and that the author intends for the element to handle its own rendering internally.
*   `@inlineView(markup, dependencies?)` - Allows the developer to provide a string that will be compiled into the view.
*   `@useShadowDOM(options?: { mode: 'open' | 'closed' })` - Causes the view to be rendered in the ShadowDOM. When an element is rendered to ShadowDOM, a special `DOMBoundary` instance can optionally be injected into the constructor. This represents the shadow root. Does not work with `@containerless()`, see below.
*   `@containerless()` - Causes the element's view to be rendered without the custom element container wrapping it. This cannot be used in conjunction with `@child`, `@children` or `@useShadowDOM` decorators. It also cannot be uses with surrogate behaviors. Use sparingly.

### SVG Elements

SVG (scalable vector graphic) tags can support Aurelia's custom element `<template>` tags by nesting the templated code inside a second `<svg>` tag. For example if you had a base `<svg>` element and wanted to add a templated `<rect>` inside it, you would first put your custom tag inside the main `<svg>` tag. Also, make sure the custom element class uses the `@containerless()` decorator.

**SVG Custom Element View-Model Declaration**
```javascript
import {containerless} from 'aurelia-framework';
  
  @containerless()
  export class MyCustomRect {
    ...
  }
```
**SVG Custom Element View**
```html
  <template>
    <svg>
      <rect width="10" height="10" fill="red" x="50" y="50"/>
    </svg>
  </template>
```
**SVG Custom Element Use**
```html
  <template>
    <require from="my-custom-rect"></require>
  
    <svg width="100" height="100" >
      <my-custom-rect></my-custom-rect>
    </svg>
  </template>
```

### Template Parts

**Custom Element View with Replaceable Parts**

```
  <template>
    <ul>
      <li class="foo" repeat.for="item of items">
        <template replaceable part="item-template">
          Original: ${item}
        </template>
      </li>
    <ul>
  </template>
```
**Custom Element Use with Replacement**
```html
  <template>
    <require from="my-element"></require>
  
    <my-element>
      <template replace-part="item-template">
        Replacement: ${item}
      </template>
    </my-element>
  </template>
```
**Surrogate Behavior Use**
```html
 <template role="progress-bar" aria-valuenow.bind="progress" aria-valuemin="0" aria-valuemax="100">
    <div class="bar">
      <div class="progress" css="width:${progress}%"></div>
    </div>
  </template>
```
### Observable decorator

Aurelia exposes a decorator named observable to allow watching for changes to a property and reacting to them. By convention it will look for a matching method name `Changed`

**Correct observable usage**
```javascript
  import {observable} from 'aurelia-framework';
  
  export class MyCustomViewModel {
    @observable name = 'Hello world';
    nameChanged(newValue, oldValue) {
      // react to change
    }
  }
```
The developer can also specify a different method name to use.

**Correct observable usage with configured change handler**
```javascript
  import {observable} from 'aurelia-framework';
  
  export class MyCustomViewModel {
    @observable({changeHandler: 'nameChanged'}) name = 'Hello world';
    nameChanged(newValue, oldValue) {
      // react to change
    }
  }

```

## 10.The Event Aggregator

If you include the `aurelia-event-aggregator` plugin using "basicConfiguration" or "standardConfiguration" then the singleton EventAggregator's API will be also present on the `Aurelia` object. You can also create additional instances of the EventAggregator, if needed, and "merge" them into any object. To do this, import `includeEventsIn` and invoke it with the object you wish to turn into an event aggregator. For example `includeEventsIn(myObject)`. Now my object has `publish` and `subscribe` methods and can be used in the same way as the global event aggregator, detailed below.

**Publishing on a Channel**
```javascript
  import {inject} from 'aurelia-framework';
  import {EventAggregator} from 'aurelia-event-aggregator';
  
  @inject(EventAggregator)
  export class APublisher {
    constructor(eventAggregator) {
      this.eventAggregator = eventAggregator;
    }
  
    publish(){
      var payload = {};
      this.eventAggregator.publish('channel name here', payload);
    }
  }
```
**Subscribing to a Channel**
```javascript
  import {inject} from 'aurelia-framework';
  import {EventAggregator} from 'aurelia-event-aggregator';
  
  @inject(EventAggregator)
  export class ASubscriber {
    constructor(eventAggregator) {
      this.eventAggregator = eventAggregator;
    }
  
    subscribe() {
      this.eventAggregator.subscribe('channel name here', payload => {
          ...
      });
    }
  }
```
**Publishing a Message**
```javascript
  //some-message.js
  export class SomeMessage{ }
  
  //a-publisher.js
  import {inject} from 'aurelia-framework';
  import {EventAggregator} from 'aurelia-event-aggregator';
  import {SomeMessage} from './some-message';
  
  @inject(EventAggregator)
  export class APublisher {
    constructor(eventAggregator) {
      this.eventAggregator = eventAggregator;
    }
  
    publish() {
      this.eventAggregator.publish(new SomeMessage());
    }
  }

```
** Subscribing to a Message Type**
```javascript
  import {inject} from 'aurelia-framework';
  import {EventAggregator} from 'aurelia-event-aggregator';
  import {SomeMessage} from './some-message';
  
  @inject(EventAggregator)
  export class ASubscriber {
    constructor(eventAggregator) {
      this.eventAggregator = eventAggregator;
    }
  
    subscribe(){
      this.eventAggregator.subscribe(SomeMessage, message => {
          ...
      });
    }
  }
``` 

  



  
  


  

