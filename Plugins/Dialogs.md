原文：https://aurelia.io/docs/plugins/dialog

>The basics of the dialog plugin for Aurelia.

* [1\.Introduction \- 简介](#1introduction---%E7%AE%80%E4%BB%8B)
* [2\.Installing The Plugin \- 安装插件](#2installing-the-plugin---%E5%AE%89%E8%A3%85%E6%8F%92%E4%BB%B6)
* [3\.Configuring The Plugin \- 配置插件](#3configuring-the-plugin---%E9%85%8D%E7%BD%AE%E6%8F%92%E4%BB%B6)
* [4\.Using The Plugin \- 使用插件](#4using-the-plugin---%E4%BD%BF%E7%94%A8%E6%8F%92%E4%BB%B6)
* [5\.Default Resources(custom elements/attributes) \- 默认资源（自定义元素/属性）](#5default-resourcescustom-elementsattributes---%E9%BB%98%E8%AE%A4%E8%B5%84%E6%BA%90%E8%87%AA%E5%AE%9A%E4%B9%89%E5%85%83%E7%B4%A0%E5%B1%9E%E6%80%A7)
  * [attach\-focus Custom Attribute \- attach\-focus 自定义特性](#attach-focus-custom-attribute---attach-focus-%E8%87%AA%E5%AE%9A%E4%B9%89%E7%89%B9%E6%80%A7)
* [6\.Settings \- 设定](#6settings---%E8%AE%BE%E5%AE%9A)
  * [Global Settings \- 全局设定](#global-settings---%E5%85%A8%E5%B1%80%E8%AE%BE%E5%AE%9A)
  * [Dialog Settings \- 对话框设定](#dialog-settings---%E5%AF%B9%E8%AF%9D%E6%A1%86%E8%AE%BE%E5%AE%9A)
* [7\.Accessing The DialogController API \- DialogController API访问](#7accessing-the-dialogcontroller-api---dialogcontroller-api%E8%AE%BF%E9%97%AE)
* [8\.Styling The Dialog \- 样式对话框](#8styling-the-dialog---%E6%A0%B7%E5%BC%8F%E5%AF%B9%E8%AF%9D%E6%A1%86)
  * [Overriding The Defaults \- 重写默认值](#overriding-the-defaults---%E9%87%8D%E5%86%99%E9%BB%98%E8%AE%A4%E5%80%BC)
  * [Overlay With 50% Opacity \- 具有50％不透明度的覆盖](#overlay-with-50-opacity---%E5%85%B7%E6%9C%8950%E4%B8%8D%E9%80%8F%E6%98%8E%E5%BA%A6%E7%9A%84%E8%A6%86%E7%9B%96)
* [9\.Lifecycle Hooks 生命周期钩子](#9lifecycle-hooks-%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F%E9%92%A9%E5%AD%90)
  * [\.canActivate()](#canactivate)
  * [\.activate()](#activate)
  * [\.canDeactivate(result: DialogCloseResult)](#candeactivateresult-dialogcloseresult)
  * [\.deactivate(result: DialogCloseResult | DialogCloseError)](#deactivateresult-dialogcloseresult--dialogcloseerror)
  * [Order of Invocation \- 调用的顺序](#order-of-invocation---%E8%B0%83%E7%94%A8%E7%9A%84%E9%A1%BA%E5%BA%8F)


## 1.Introduction - 简介

本文介绍了Aurelia的对话框插件。

创建此插件是为了在您的应用程序中显示对话框（有时称为模式）。

该插件支持在所有方面使用动态内容，并且可以轻松配置/覆盖。

## 2.Installing The Plugin - 安装插件

``` dockerfile
  npm install aurelia-dialog
```
or
``` dockerfile
  yarn add aurelia-dialog
```
or

``` dockerfile
  jspm install aurelia-dialog
```

>提醒：如果您使用TypeScript并只安装jspm包，您可以使用以下命令安装插件的类型：

``` nginx
  typings install github:aurelia/dialog
```

## 3.Configuring The Plugin - 配置插件


1.  确保使用手动引导[manual bootstrapping](http://aurelia.io/docs/fundamentals/app-configuration-and-startup#manual-bootstrapping) 。为此，请打开`index.html`并找到具有aurelia-app属性的元素。将其更改为如下所示：

**index.html**

``` xml
<body aurelia-app="main">...</body>
```

2.  在`src`文件夹中创建（如果尚未创建）`main.js`文件，其内容如下：

**main.js**

``` javascript
import {PLATFORM} from 'aurelia-pal';
  
  export function configure(aurelia) {
    aurelia.use
      .standardConfiguration()
      .developmentLogging()
      .plugin(PLATFORM.moduleName('aurelia-dialog'));
  
    aurelia.start().then(a => a.setRoot());
  }
```

>警告1：
>当`<body>` 标记有 `aurelia-app`属性时，在将应用附加到DOM之前打开的任何对话框（在`Aurelia.prototype.setRoot`完成之前）都将从DOM中删除。在任何情况下，如果您在继续之前先关闭对话框，则可以在 `canActivate` 或 `activate`钩子中打开对话框。如果您只想打开一个对话框而不等待它关闭，请改为执行`attached`的钩子。在任何情况下，如果您在继续之前先关闭对话框，则可以在canActivate或activate挂钩中打开对话框。

 >警告2：
 >如果您使用的是*Webpack*，则不应忽略`PLATFORM.moduleName`。

## 4.Using The Plugin - 使用插件

您可以通过几种方式利用Aurelia对话框。

1.  您可以使用对话框服务打开提示 -

**welcome.js**

``` javascript
 import {DialogService} from 'aurelia-dialog';
  import {Prompt} from './prompt';
  
  export class Welcome {
    static inject = [DialogService];
    constructor(dialogService) {
      this.dialogService = dialogService;
    }
    submit(){
      this.dialogService.open({ viewModel: Prompt, model: 'Good or Bad?', lock: false }).whenClosed(response => {
        if (!response.wasCancelled) {
          console.log('good');
        } else {
          console.log('bad');
        }
        console.log(response.output);
      });
    }
  }
```


这将打开一个提示，并返回一个在关闭时会解决的承诺。如果用户单击退出，单击取消或单击右上角的“x”，它仍将解决`resolve`诺言，但响应 `wasCancelled`上将具有属性，以允许开发人员处理的对话框。

还有一个`output`属性，如果采取了用户操作，它将返回用户操作的结果。

2.  您可以创建自己的 view / view-model，并使用对话框服务从应用程序的 view-model 中调用它 -

  
**welcome.js**
    

``` javascript
  import {EditPerson} from './edit-person';
  import {DialogService} from 'aurelia-dialog';
  
  export class Welcome {
    static inject = [DialogService];
    constructor(dialogService) {
      this.dialogService = dialogService;
    }
    person = { firstName: 'Wade', middleName: 'Owen', lastName: 'Watts' };
    submit(){
      this.dialogService.open({ viewModel: EditPerson, model: this.person, lock: false }).whenClosed(response => {
        if (!response.wasCancelled) {
          console.log('good - ', response.output);
        } else {
          console.log('bad');
        }
        console.log(response.output);
      });
    }
  }
```

这将打开一个对话框，并以与提示相同的方式对其进行控制。要记住的重要一点是，您需要遵循与在`EditPerson`视图模型中利用`DialogController` 以及在 activate 方法中接受模型相同的方法 -


  **edit-person.js**

``` javascript
import {DialogController} from 'aurelia-dialog';
  
  export class EditPerson {
    static inject = [DialogController];
    person = { firstName: '' };
    constructor(controller){
      this.controller = controller;
    }
    activate(person){
      this.person = person;
    }
  }
```

对应的视图 - 
  
**edit-person.html**

``` html
   <template>
    <ux-dialog>
      <ux-dialog-body>
        <h2>Edit first name</h2>
        <input value.bind="person.firstName">
      </ux-dialog-body>
  
      <ux-dialog-footer>
        <button click.trigger="controller.cancel()">Cancel</button>
        <button click.trigger="controller.ok(person)">Ok</button>
      </ux-dialog-footer>
    </ux-dialog>
  </template>
```

  ## 5.Default Resources(custom elements/attributes) - 默认资源（自定义元素/属性）



可用的资源有:`<ux-dialog>`, `<ux-dialog-header>`, `<ux-dialog-body>`, `<ux-dialog-footer>` 和 `attach-focus`。它们是默认注册的。如果您不使用它们，则提供一个配置回调，这样它们就不会被默认注册：

  **main.js**

``` javascript
   import {PLATFORM} from 'aurelia-pal';
  
  export function configure(aurelia) {
    aurelia.use
      // ...
      .plugin(PLATFORM.moduleName('aurelia-dialog'), (configuration) => {
        // custom configuration
      });
    // ...
  }
```
或者你只想用其中的一部分：

  **main.js**

``` javascript
import {PLATFORM} from 'aurelia-pal';
  
  export function configure(aurelia) {
    aurelia.use
      // ...
      .plugin(PLATFORM.moduleName('aurelia-dialog'), (configuration) => {
        // use only attach-focus
        configuration.useResource('attach-focus');
      });
    // ...
  }
```

### `attach-focus` Custom Attribute - `attach-focus` 自定义特性

该库公开一个`attach-focus`自定义属性，该属性允许在加载模态时集中于模式中的元素。你可以用它来聚焦按钮，输入，等等…示例使用：

  **edit-person.html**

``` html
  <template>
    <ux-dialog>
      <ux-dialog-body>
        <h2>Edit first name</h2>
        <input attach-focus value.bind="person.firstName">
      </ux-dialog-body>
    </ux-dialog>
  </template>
```

如果您希望根据视图模型属性更改将焦点放在哪个元素上，还可以绑定`attach-focus`属性的值。

**edit-person.html**

``` HTML
 <input attach-focus.bind="isNewPerson" value.bind="person.email">
  <input attach-focus.bind="!isNewPerson" value.bind="person.firstName">
```

 >提示：逻辑是在附加期间执行的——因此属性名是附加期间执行的。在此之后对值的任何更改都不会反映出来，因为这样的场景使用了 `focus`定制属性。

 ## 6.Settings - 设定

### Global Settings - 全局设定

您还可以通过`configure`方法为安装插件时使用的所有对话框指定全局设置。如果提供自定义配置，则必须调用`useDefaults()`方法来应用基本配置。

  **main.js**

``` JavaScript
  import {PLATFORM} from 'aurelia-pal';
  
  export function configure(aurelia) {
    aurelia.use
      .standardConfiguration()
      .developmentLogging()
      .plugin(PLATFORM.moduleName('aurelia-dialog'), config => {
        config.useDefaults();
        config.settings.lock = true;
        config.settings.centerHorizontalOnly = false;
        config.settings.startingZIndex = 5;
        config.settings.keyboard = true;
      });
  
    aurelia.start().then(a => a.setRoot());
  }
```

### Dialog Settings - 对话框设定

对话框可用的设置是在每个对话框的基础上在对话框控制器上设置的。

*   `viewModel` 可以是url，类引用或实例。

    1. url - 相对于应用程序根目录的路径。

  **System**
``` dsconfig?linenums
  +-- app.js
  +-- forms
      +-- consent-form.js
      +-- consent-form.html
  +-- prompts
      +-- prompt.js
      +-- prompt.html
```

如果要从`consent-form`打开`prompt`，则路径为`prompts/prompt`。

	 >警告：
	 >_Webpack_ 用户应该始终使用`PLATFORM.moduleName`标记动态加载的依赖项。要了解更多细节，请查看`aurelia-webpack-plugin` [wiki](https://github.com/aurelia/webpack-plugin/wiki) 。


   2. object - 它将被用作视图模型。在这种情况下，还必须指定视图 `view` 。

   3. class - 视图模型类或构造函数。

*   `view` 可以是url或视图策略来覆盖默认的视图位置约定。

*   `model` 如果实现，要传递给视图模型的 `canActivate` 和 `activate`方法的数据。

*   `host` 允许提供作为对话框父级的元素-如果未提供，将使用正文。

*   `childContainer` 允许为对话框指定DI容器实例。如果没有提供，将从根容器创建新的子容器。

*   `lock` 使对话框成为模态，并从右上角移除关闭按钮。(默认值为true)

*   `keyboard` 允许配置键盘键关闭对话框。禁用设置为`false`。要取消关闭对话框时，ESC键被按下设置为 `true`，`'Escape'`或数组包含`'Escape'` - `['Escape']`。当按下回车键设置为`'Enter'`或包含`'Enter'` - `['Enter']`的数组时，以确认结束。结合ESC和输入键设置为`['Enter', 'Escape']` -顺序是不相关的。(优先于锁`lock`)

*   `overlayDismiss` 如果设置为`true`则在对话框外部单击时，关闭对话框。(优先于锁`lock`)

*   `centerHorizontalOnly` 意味着对话框将水平居中，垂直对齐由您决定。(默认值为false)

*   `position` 在显示带有签名的模态之前立即调用的回调：`(modalContainer: Element, modalOverlay: Element) => void`。这使您可以设置特殊的类，使用位置进行播放，等等。如果指定，则会忽略`centerHorizontalOnly` 。
（可选的）

*   `ignoreTransitions` 是一个布尔值，如果禁用对话框的CSS动画，则必须将其设置为`true`。（可选，默认为 false）

*   `rejectOnCancel` 是一个布尔值，如果要将取消作为拒绝处理，则必须将其设置为 `true`。原因将是`DialogCancelError`——属性`wasCancelled`将设置为`true`，如果提供了取消数据，则将其设置为`output`属性。

>警告：建议插件作者明确改变行为的设置(`rejectOnCancel`)。

**prompt.js**

``` javascript
  export class Prompt {
    static inject = [DialogController];
  
    constructor(controller){
      this.controller = controller;
      this.answer = null;
  
      controller.settings.lock = false;
      controller.settings.centerHorizontalOnly = true;
    }
  }
```

## 7.Accessing The DialogController API - DialogController API访问

在打开对话框的同一上下文中可以解决和关闭（使用cancel / ok / error方法）对话框。
  
**welcome.js**
 
``` javascript
  import {EditPerson} from './edit-person';
  import {DialogService} from 'aurelia-dialog';
  
  export class Welcome {
    static inject = [DialogService];
    constructor(dialogService) {
      this.dialogService = dialogService;
    }
    person = { firstName: 'Wade', middleName: 'Owen', lastName: 'Watts' };
    submit(){
      this.dialogService.open({viewModel: EditPerson, model: this.person}).then(openDialogResult => {
        // Note you get here when the dialog is opened, and you are able to close dialog
        setTimeout(() => {
          openDialogResult.controller.cancel('canceled outside after 3 sec')
        }, 3000);
  
        // Promise for the result is stored in openDialogResult.closeResult property
        return openDialogResult.closeResult;
      }).then((response) => {
        if (!response.wasCancelled) {
          console.log('good');
        } else {
          console.log('bad');
        }
        console.log(response);
      });
    }
  }
```

## 8.Styling The Dialog - 样式对话框

### Overriding The Defaults - 重写默认值

对话框的CSS类被硬编码在`dialog-configuration.ts`中。通过`config.useDefaults()`配置对话框插件时，将执行以下代码。
  

``` javascript
public useDefaults(): this {
    return this.useRenderer(defaultRenderer)
      .useCSS(defaultCSSText)
      .useStandardResources();
  }
```

如果你想覆盖默认的样式，可以通过调用`main.ts`中的`useCSS('')`来配置插件。

  **main.js**

``` JavaScript
  import {PLATFORM} from 'aurelia-pal';
  
  export function configure(aurelia) {
    aurelia.use
      .standardConfiguration()
      .developmentLogging()
      .plugin(PLATFORM.moduleName('aurelia-dialog'), config => {
        config.useDefaults();
        config.useCSS('');
      });
  
    aurelia.start().then(a => a.setRoot());
  }
```

将CSS从`dialog-configuration.ts`复制到应用程序的CSS文件是一个很好的起点。

### Overlay With 50% Opacity - 具有50％不透明度的覆盖

Bootstrap增加了50%的不透明度和一个黑色的背景颜色的模式。要在对话框中实现这一点，您可以简单地添加以下CSS。

``` css
ux-dialog-overlay.active {
    background-color: black;
    opacity: .5;
  }
```

  ## 9.Lifecycle Hooks 生命周期钩子

除了 `aurelia-templating`定义的生命周期钩子之外， `aurelia-dialog`还定义了其他钩子。所有对话框特定的钩子都可以返回一个`Promise`，该承诺将解析为钩子的适当值，并将被等待。

### `.canActivate()`


使用此钩子可以取消对话框的打开。它由一个参数调用——传递给`.open()`的 `model`设置的值。取消对话框的打开，返回`false` - `null` 和 `undefined`将被强制为`true`。

### `.activate()`


这个钩子可以用来做任何必要的初始化工作。通过一个参数 — 传递给的`model`设置的值`.open()` 来调用钩子。

### `.canDeactivate(result: DialogCloseResult)`

使用此钩子可以取消对话框的关闭。这样做将返回`false` - `null` 和 `undefined`将被强制为`true`。传递的结果参数有一个属性`wasCancelled`，表示对话框是否关闭或取消，以及一个输出`output` 属性，其中包含对话框结果，可以在对话框停用之前进行操作。

>警告：调用`DialogController.prototype.error()`时，将跳过这个钩子。

### `.deactivate(result: DialogCloseResult | DialogCloseError)`

这个挂钩可以用来做任何清理工作。钩子是通过一个结果参数调用的，该结果参数具有一个属性`wasCancelled`，它指示对话框是关闭还是取消，以及一个输出`output`属性和对话框结果。

### Order of Invocation - 调用的顺序

每个对话框实例经历一次完整的生命周期。

1.  constructor call 构造函数调用
2.  `.canActivate()` - `aurelia-dialog` _specific_
3.  `.activate()` - `aurelia-dialog` _specific_
4.  `.created()` - as defined by `aurelia-templating`
5.  `.bind()` - as defined by `aurelia-templating`
6.  `.attached()` - as defined by `aurelia-templating`
7.  `.canDeactivate()` - `aurelia-dialog` _specific_
8.  `.deactivate()` - `aurelia-dialog` _specific_
9.  `.detached()` - as defined by `aurelia-templating`
10.  `.unbind()` - as defined by `aurelia-templating`