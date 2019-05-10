原文链接：https://aurelia.io/docs/fundamentals/cheat-sheet

忘了绑定的语法？ 需要知道如何创建自定义属性？ 本文包含诸如此类问题的答案以及一系列常见任务的快速代码段。

# Configuration and Startup
Bootstrapping Older Browsers

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

**Standard Startup Configuration**

``` typescript
import {Aurelia} from 'aurelia-framework';
  
  export function configure(aurelia: Aurelia): void {
    aurelia.use
      .standardConfiguration()
      .developmentLogging();
  
    aurelia.start().then(() => aurelia.setRoot());
  }
```
**Explicit Startup Configuration**
  

``` typescript
import {LogManager, Aurelia} from 'aurelia-framework';
  import {ConsoleAppender} from 'aurelia-logging-console';
  
  LogManager.addAppender(new ConsoleAppender());
  LogManager.setLevel(LogManager.logLevel.debug);
  
  export function configure(aurelia: Aurelia): void {
    aurelia.use
      .defaultBindingLanguage()
      .defaultResources()
      .history()
      .router()
      .eventAggregator();
  
    aurelia.start().then(() => aurelia.setRoot('app', document.body));
  }
  
```
**Configuring A Feature**
 

``` typescript
 import {Aurelia} from 'aurelia-framework';
  
  export function configure(aurelia: Aurelia): void {
    aurelia.use
      .standardConfiguration()
      .developmentLogging()
      .feature('feature-name', featureConfiguration);
  
    aurelia.start().then(() => aurelia.setRoot());
  }
  
```
**Installing a Plugin**

 

``` typescript
import {Aurelia} from 'aurelia-framework';
  
  export function configure(aurelia: Aurelia): void {
    aurelia.use
      .standardConfiguration()
      .developmentLogging()
      .plugin('plugin-name', pluginConfiguration);
  
    aurelia.start().then(() => aurelia.setRoot());
  }
```
## Creating Components

UI components consist of two parts: a view-model and a view. Simply create each part in its own file. Use the same file name but different file extensions for the two parts. For example: _hello.ts_ and _hello.html_.

**Explicit Configuration**

``` typescript
  import {useView} from 'aurelia-framework';
  
  @useView('./hello.html')
  export class Hello {
    ...
  }
  
```

  #### The Component Lifecycle

Components have a well-defined lifecycle:

1.  `constructor()` - The view-model's constructor is called first.
2.  `created(owningView: View, myView: View)` - If the view-model implements the `created` callback it is invoked next. At this point in time, the view has also been created and both the view-model and the view are connected to their controller. The created callback will receive the instance of the "owningView". This is the view that the component is declared inside of. If the component itself has a view, this will be passed second.
3.  `bind(bindingContext: Object, overrideContext: Object)` - Databinding is then activated on the view and view-model. If the view-model has a `bind` callback, it will be invoked at this time. The "binding context" to which the component is being bound will be passed first. An "override context" will be passed second. The override context contains information used to traverse the parent hierarchy and can also be used to add any contextual properties that the component wants to add. It should be noted that when the view-model has implemented the `bind` callback, the databinding framework will not invoke the changed handlers for the view-model's bindable properties until the "next" time those properties are updated. If you need to perform specific post-processing on your bindable properties, when implementing the `bind` callback, you should do so manually within the callback itself.
4.  `attached()` - Next, the component is attached to the DOM (in document). If the view-model has an `attached` callback, it will be invoked at this time.
5.  `detached()` - At some point in the future, the component may be removed from the DOM. If/When this happens, and if the view-model has a `detached` callback, this is when it will be invoked.
6.  `unbind()` - After a component is detached, it's usually unbound. If your view-model has the `unbind` callback, it will be invoked during this process.

## Dependency Injection

**Declaring Dependencies**
    

``` typescript
  import {autoinject} from 'aurelia-framework';
  import {Dep1} from 'dep1';
  import {Dep2} from 'dep2';
  
  @autoinject
  export class CustomerDetail {
    constructor(private dep1: Dep1, private dep2: Dep2){ }
  }
```

  

  
**Using Resolvers**
   

``` typescript
  import {Lazy, inject} from 'aurelia-framework';
  import {HttpClient} from 'aurelia-fetch-client';
  
  @inject(Lazy.of(HttpClient))
  export class CustomerDetail {
    constructor(private getHTTP: () => HttpClient){ }
  }
```
#### Available Resolvers

*   `Lazy` - Injects a function for lazily evaluating the dependency.
    *   ex. `Lazy.of(HttpClient)`
*   `All` - Injects an array of all services registered with the provided key.
    *   ex. `All.of(Plugin)`
*   `Optional` - Injects an instance of a class only if it already exists in the container; null otherwise.
    *   ex. `Optional.of(LoggedInUser)`
*   `Parent` - Skips starting dependency resolution from the current container and instead begins the lookup process on the parent container.
    *   ex. `Parent.of(MyCustomElement)`
*   `Factory` - Used to allow injecting dependencies, but also passing data to the constructor.
    *   ex. `Factory.of(CustomClass)`
*   `NewInstance` - Used to inject a new instance of a dependency, without regard for existing instances in the container.
    *   ex. `NewInstance.of(CustomClass).as(Another)`

**Explicit Registration**
   

``` typescript
  import {transient, autoinject} from 'aurelia-framework';
  import {HttpClient} from 'aurelia-fetch-client';
  
  @transient()
  @autoinject
  export class CustomerDetail {
    constructor(private http: HttpClient) { }
  }
```

##  Templating Basics
**A Simple Template**

    

``` HTML
  <template>
    <div>Hello World!</div>
  </template>
```

  

  
**Requiring Resources**

    

``` HTML
  <template>
    <require from='nav-bar'></require>
  
    <nav-bar router.bind="router"></nav-bar>
  
    <div class="page-host">
      <router-view></router-view>
    </div>
  </template>
```
>#### Invalid Table Structure When Dynamically Creating Tables
>When the browser loads in the template it very helpfully validates the structure of the HTML, notices that you have an invalid tag inside your table definition, and very unhelpfully removes it for you before Aurelia even has a chance to examine your template.

>警告：#### webpack
>Dynamic compose as used below does not work when using webpack. It is suggested to bind to a property on the viewmodel `template: PLATFORM.moduleName("./template_.html")` and then use it like so `<tr repeat.for="r of ['A','B','A','B']" as-element="compose" view.bind='template'>`
  
Use of the `as-element` attribute ensures we have a valid HTML table structure at load time, yet we tell Aurelia to treat its contents as though it were a different tag.

HTML
    

  <template>
    <table>
      <tr repeat.for="r of ['A','B','A','B']" as-element="compose" view='./template_${r}.html'>
    </table>
  <template>
  

  
For the above example we can then programmatically choose the embedded template based on an element of our data:

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

  Note that when a `containerless` attribute is used, the container is stripped _after_ the browser has loaded the DOM elements, and as such this method cannot be used to transform non-HTML compliant structures into compliant ones!
  
  **Illegal Table Code**


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

  

  
**Correct Table Code**

    

``` HTML
  <template>
    <table>
      <tr repeat.for="customer of customers">
        <td>${customer.fullName}</td>
      </tr>
    </table>
  </template>
```

  

  
**Illegal Select Code**

    

``` HTML
  <template>
    <select>
      <template repeat.for="customer of customers">
        <option>...</option>
      </template>
    </select>
  </template>
```

  

  
**Correct Select Code**
    

``` HTML
  <template>
    <select>
      <option repeat.for="customer of customers">...</option>
    </select>
  </template>
```

  ## Databinding

### bind, one-way, two-way & one-time

Use on any HTML attribute.

*   `.bind` - Uses the default binding. One-way binding for everything but form controls, which use two-way binding.
*   `.one-way` - Flows data one direction: from the view-model to the view.
*   `.two-way` - Flows data both ways: from view-model to view and from view to view-model.
*   `.one-time` - Renders data once, but does not synchronize changes after the initial render.

Data Binding Examples

    

``` HTML
  <template>
    <input type="text" value.bind="firstName">
    <input type="text" value.two-way="lastName">
  
    <a href.one-way="profileUrl">View Profile</a>
  </template>
```

  >At the moment inheritance of bindables is not supported. For use cases where `class B extends A` and `B` is used as custom Element/Attribute `@bindable` properties cannot be defined only on `class A`. If inheritance is used, `@bindable` properties should be defined on the instantiated class.

### delegate, trigger

Use on any native or custom DOM event. (Do not include the "on" prefix in the event name.)

*   `.trigger` - Attaches an event handler directly to the element. When the event fires, the expression will be invoked.
*   `.delegate` - Attaches a single event handler to the document (or nearest shadow DOM boundary) which handles all events of the specified type, properly dispatching them back to their original targets for invocation of the associated expression.

>The `$event` value can be passed as an argument to a `delegate` or `trigger` function call if you need to access the event object.

  **Event Binding Examples**

 
``` HTML
  <template>
    <button click.trigger="save()">Save</button>
    <button click.delegate="save($event)">Save</button>
  </template>
```

  
### call

Passes a function reference.
  

**Call Example**
``` HTML
  <template>
    <button my-attribute.call="sayHello()">Say Hello</button>
  </template>
```

  ### ref

Creates a reference to an HTML element, a component or a component's parts.

*   `ref="someIdentifier"` or `element.ref="someIdentifier"` - Create a reference to the HTMLElement in the DOM.
*   `attribute-name.ref="someIdentifier"`- Create a reference to a custom attribute's view-model.
*   `view-model.ref="someIdentifier"`- Create a reference to a custom element's view-model.
*   `view.ref="someIdentifier"`- Create a reference to a custom element's view instance (not an HTML Element).
*   `controller.ref="someIdentifier"`- Create a reference to a custom element's controller instance.

Ref Example

    

``` HTML
  <template>
    <input type="text" ref="name"> ${name.value}
  </template>
```

### String Interpolation

Used in an element's content. Can be used inside attributes, particularly useful in the `class` and `css` attributes.

**String Interpolation Example**

    

``` HTML
  <template>
    <span>${fullName}</span>
    <div class="dot ${color} ${isHappy ? 'green' : 'red'}"></div>
  </template>
```
### Binding to Select Elements

A typical select element is rendered using a combination of `value.bind` and `repeat`. You can also bind to arrays of objects and synchronize based on an id (or similar) property.

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

  

  ### Binding Checkboxes

  

  

  



  
  


  

