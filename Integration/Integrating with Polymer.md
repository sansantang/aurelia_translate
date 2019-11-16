原文：https://aurelia.io/docs/integration/polymer

>Polymer是一个库，用于声明性地创建可重复使用的Web组件，并具有诸如数据绑定和属性观察之类的额外功能。在许多方面，它类似于Aurelia的组件支持。但是，Polymer还提供了广泛的自定义元素目录，涵盖从材料设计到信用卡表单再到嵌入Google服务（例如Google Maps和YouTube）的所有内容。

* [1\.Setup](#1setup)
* [2\.Importing Elements 导入的元素](#2importing-elements-%E5%AF%BC%E5%85%A5%E7%9A%84%E5%85%83%E7%B4%A0)
* [3\.Data Binding](#3data-binding)
* [4\.Forms and Two\-Way Binding 表单和双向绑定](#4forms-and-two-way-binding-%E8%A1%A8%E5%8D%95%E5%92%8C%E5%8F%8C%E5%90%91%E7%BB%91%E5%AE%9A)
* [5\.How the Plugin Works](#5how-the-plugin-works)


## 1.Setup


第一步是获取Polymer，通常使用[Bower](http://bower.io/) 软件包管理器完成。以下`bower.json`将安装Polymer的基础和材料设计元素。

**Bower Config**

``` json
  {
    "name": "my-aurelia-polymer-project",
    "private": true,
    "dependencies": {
      "polymer": "Polymer/polymer#^1.2.0",
      "paper-elements": "PolymerElements/paper-elements#^1.0.6",
      "iron-elements": "PolymerElements/iron-elements#^1.0.4",
      "webcomponentsjs": "webcomponents/webcomponentsjs#^0.7.20"
    }
  }
```

  
确保已安装Bower，然后使用Bower为每个Polymer元素安装软件包。

**Bower Package Installation**

``` shell
 $ npm install -g bower
  $ bower install
```

 
Aurelia还必须配置为使用HTML导入模板加载器（`aurelia-html-import-template-loader`）和`aurelia-polymer`插件，两者都可以与JSPM一起安装。
  
  
  **Plugin Installation**

``` shell
 $ jspm install aurelia-html-import-template-loader
  $ jspm install aurelia-polymer@^1.0.0-beta
```


在`index.html`中，需要在Aurelia之前加载Polymer和`webcomponents.js` polyfills，以便`aurelia-polymer`插件可以挂接到Polymer的元素注册系统中。

**Loading Web Components and Polymer**

``` HTML
<!DOCTYPE html>
  <html>
    <head>
      <!-- ... -->
      <script src="bower_components/webcomponentsjs/webcomponents-lite.js"></script>
      <link rel="import" href="bower_components/polymer/polymer.html">
    </head>
    <!-- ... -->
  </html>
```

等待Web组件polyfill将Polymer加载到Bootstrap Aurelia上也是一个好主意。

与其直接导入`aurelia-bootstrapper`，不如等到在`index.html`中触发`WebComponentsReady`事件。

**Bootstrapping Aurelia**

``` HTML
<!DOCTYPE html>
  <script>
    document.addEventListener('WebComponentsReady', function() {
      System.import('aurelia-bootstrapper');
    });
  </script>
```

在`main.js`中，还需要加载之前安装的两个Aurelia插件。  

**Configuring Aurelia**

``` javascript
export function configure(aurelia) {
    aurelia.use
      .standardConfiguration()
      .developmentLogging();
  
    aurelia.use.plugin('aurelia-polymer');
    aurelia.use.plugin('aurelia-html-import-template-loader');
  
    aurelia.start().then(a => a.setRoot());
  }
```

此时，Aurelia和Polymer已准备就绪。下面的示例将各种Polymer元素合并到Aurelia骨架导航启动器中。  

## 2.Importing Elements 导入的元素


使用普通的Aurelia模板加载器，在根`<template>`元素之外不允许任何操作。但是，在使用HTML导入时，导入语句必须位于`<template>`之前。

**Using HTML Imports**

``` HTML
 <link rel="import" href="/bower_components/paper-drawer-panel/paper-drawer-panel.html">
  <link rel="import" href="/bower_components/paper-toolbar/paper-toolbar.html">
  <link rel="import" href="/bower_components/paper-menu/paper-menu.html">
  <link rel="import" href="/bower_components/paper-item/paper-item.html">
  <link rel="import" href="/bower_components/paper-scroll-header-panel/paper-scroll-header-panel.html">
  <link rel="import" href="/bower_components/paper-icon-button/paper-icon-button.html">
  <link rel="import" href="/bower_components/iron-icons/iron-icons.html">
  
  <template>
    <!-- ... -->
  </template>
```

  
## 3.Data Binding

根据上面的导入，这展示了如何在 `app.html`中使Polymer件实现基本布局。

**Using Polymer Elements**

``` HTML
<template>
    <paper-drawer-panel force-narrow>
      <div drawer>
        <paper-toolbar class="paper-header">
          <span>Menu</span>
        </paper-toolbar>
        <paper-menu>
          <paper-item repeat.for="row of router.navigation" active.bind="row.isActive">
            <a href.bind="row.href">${row.title}</a>
          </paper-item>
        </paper-menu>
      </div>
      <div main>
        <paper-toolbar class="paper-header">
          <paper-icon-button icon="menu" tabindex="1" paper-drawer-toggle></paper-icon-button>
          <div class="title">${router.title}</div>
          <span if.bind="router.isNavigating"><iron-icon icon="autorenew"></iron-icon></span>
        </paper-toolbar>
        <div>
          <router-view></router-view>
        </div>
      </div>
    </paper-drawer-panel>
  </template>
```

  
注意，Poylmer元素，如`<paper-drawer-panel>` 或 `<paper-menu>`，一旦导入，就可以像普通的HTML元素一样使用。


所有标准的Aurelia绑定语法都将继续起作用。例如，`repeat.for`用于从Aurelia路由器填充菜单项。
导航菜单中`<paper-item>`元素上的`active.bind`绑定显示Aurelia也支持Polymer定义的属性。

## 4.Forms and Two-Way Binding 表单和双向绑定

更新后的welcome.html使用Polymer输入元素来增强其形式。

**Using Polymer in a Form**

``` HTML
 <link rel="import" href="../bower_components/paper-input/paper-input.html">
  <link rel="import" href="../bower_components/iron-form/iron-form.html">
  
  <template>
    <section class="au-animate">
      <h2>${heading}</h2>
      <form is="iron-form" role="form" submit.delegate="submit()">
        <div class="form-group">
          <label for="fn">First Name</label>
          <paper-input id="fn" value.two-way="firstName" placeholder="first name"></paper-input>
        </div>
        <div class="form-group">
          <label for="ln">Last Name</label>
          <paper-input id="fn" value.two-way="lastName" placeholder="last name"></paper-input>
        </div>
        <div class="form-group">
          <label>Full Name</label>
          <p class="help-block">${fullName | upper}</p>
        </div>
        <button type="submit" class="btn btn-default">Submit</button>
      </form>
    </section>
  </template>
```

虽然在这种情况下并不是必须的，但是将表单设置为 `<iron-form>`可以确保Polymer输入元素与原生HTML元素一起提交。

注意， `<paper-input>` 元素有`.two-way`绑定，而不仅仅是 `.bind`。除了本地HTML表单元素外，Aurelia默认为单向绑定，因此必须显式地指定双向数据绑定。

普通的`<button>`元素与Aurelia的`submit.delegate`绑定一起使用，因为Polymer元素无法提交表单。

如果不需要表单提交语义，则另一个选择是改为在Polymer按钮上使用`click.delegate` 。

**Polymer Element with Event Delegation**

``` HTML
<paper-button raised click.delegate="submit()">Submit</paper-button>
```

  

  ## 5.How the Plugin Works

`aurelia-polymer`插件非常简单。对于每个Polymer元素，它使用Aurelia的绑定组件中的 `EventManager`注册该元素的属性以及它们支持的事件。这个插件的核心是 [registerElement](https://github.com/roguePanda/aurelia-polymer/blob/1.0.0-beta.1.0.1/src/index.js#L7)函数，它循环遍历元素的所有属性及其实现的任何行为，以便Aurelia绑定引擎能够正确地注册事件监听器。

**Element Registration**

``` javascript
  var propertyConfig = {'bind-value': ['change', 'input']}; // Not explicitly listed for all elements that use it
  
  function handleProp(propName, prop) {
    if (prop.notify) {
      propertyConfig[propName] = ['change', 'input'];
    }
  }
  
  Object.keys(prototype.properties).forEach(propName => handleProp(propName, prototype.properties[propName]));
  
  prototype.behaviors.forEach(behavior => {
    if (typeof behavior.properties != 'undefined') {
      Object.keys(behavior.properties).forEach(propName => handleProp(propName, behavior.properties[propName]));
    }
  });
  
  eventManager.registerElementConfig({
    tagName: prototype.is,
    properties: propertyConfig
  });
```

标记为`notify`的元素属性可用于Polymer的双向数据绑定系统，并触发`{property-name}-changed`事件。这对应于Aurelia中的输入`input`和更改`change`事件。

每当导入Polymer元素时，都会触发对`Polymer.telemetry._registrate`的调用，该调用会将元素原型添加到已注册的Polymer元素列表中。`aurelia-polymer`插件用一个也在原型上调用`registerElement`的注册函数替换了这个注册函数，因此它也配置了Aurelia。需要覆盖这个功能是为什么Polymer必须在插件之前加载。