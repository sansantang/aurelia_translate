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
* [6\.Templating View Resources](#6templating-view-resources)
* [7\.Routing](#7routing)
* [8\.Custom Attributes](#8custom-attributes)
* [9\.Custom Elements](#9custom-elements)
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

  
**Using Resolvers使用解析器**
   
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

**Explicit Registration显示注入**

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
>Dynamic compose as used below does not work when using webpack. It is suggested to bind to a property on the viewmodel `template: PLATFORM.moduleName("./template_.html")` and then use it like so `<tr repeat.for="r of ['A','B','A','B']" as-element="compose" view.bind='template'>`
>
>使用webpack时，下面使用的动态组合不能工作。建议绑定到viewmodel上的属性`template: PLATFORM.moduleName("./template_.html")`然后像这样使用它`<tr repeat.for="r of ['A','B','A','B']" as-element="compose" view.bind='template'>`
  
使用`as-element`属性可以确保我们在加载时拥有一个有效的HTML表结构，但是我们告诉Aurelia将其内容视为不同的标记。

HTML
    
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

Use on any HTML attribute.

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

**String Interpolation Example **

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

  

  
**Select with Object Array**

    

``` HTML
  <template>
    <select value.bind="employeeOfTheMonth">
      <option>Select An Employee</option>
      <option repeat.for="employee of employees" model.bind="employee">${employee.fullName}</option>
    </select>
  </template>
```

  

  
**Select with Object Id Sync**
   

``` HTML
  <template>
    <select value.bind="employeeOfTheMonthId">
      <option>Select An Employee</option>
      <option repeat.for="employee of employees" model.bind="employee.id">${employee.fullName}</option>
    </select>
  </template>
```

  

  
**Basic Multi-Select**
   

``` HTML
  <template>
    <select value.bind="favoriteColors" multiple>
      <option repeat.for="color of colors" value.bind="color">${color}</option>
    </select>
  </template>
```

  

  
**Multi-Select with Object Array**
    

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

  

  


**Radios with Object Arrays**
    

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

>禁止：始终使用HTML清理。我们提供了一个简单的转换器，可以使用。建议您使用更完整的HTML杀毒工具，如[sanitize-html](https://www.npmjs.com/package/sanitize-html)。

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
```
  <template>
    <div style.bind="styleString"></div>
    <div style.bind="styleObject"></div>
  </template>
```
**Illegal Style Interpolation**
```
   <template>
    <div style="width: ${width}px; height: ${height}px;"></div>
  </template>
```
**Legal Style Interpolation**
```
  <template>
    <div css="width: ${width}px; height: ${height}px;"></div>
  </template>
```
### Declaring Computed Property Dependencies 计算属性声明依赖项
**Computed Properties **
```
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

## 6.Templating View Resources

## 7.Routing

## 8.Custom Attributes

## 9.Custom Elements

## 10.The Event Aggregator

  

  



  
  


  

