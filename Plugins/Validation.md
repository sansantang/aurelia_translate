原文：https://aurelia.io/docs/plugins/validation

* [1 Introduction \- 简介](#1-introduction---%E7%AE%80%E4%BB%8B)
* [2 Defining Rules \- 定义规则](#2-defining-rules---%E5%AE%9A%E4%B9%89%E8%A7%84%E5%88%99)
  * [2\.1 ensure \- 确保](#21-ensure---%E7%A1%AE%E4%BF%9D)
  * [2\.2 displayName \- 显示名称](#22-displayname---%E6%98%BE%E7%A4%BA%E5%90%8D%E7%A7%B0)
  * [2\.3 Applying Rules \- 应用规则](#23-applying-rules---%E5%BA%94%E7%94%A8%E8%A7%84%E5%88%99)
  * [2\.4 withMessage \- 重写消息](#24-withmessage---%E9%87%8D%E5%86%99%E6%B6%88%E6%81%AF)
  * [2\.5 withMessageKey](#25-withmessagekey)
  * [2\.6 Conditional Validation \- 条件式验证](#26-conditional-validation---%E6%9D%A1%E4%BB%B6%E5%BC%8F%E9%AA%8C%E8%AF%81)
  * [2\.7 Sequencing Rule Evaluation \- 排序规则评估](#27-sequencing-rule-evaluation---%E6%8E%92%E5%BA%8F%E8%A7%84%E5%88%99%E8%AF%84%E4%BC%B0)
  * [2\.8 Tagging Rules \- 标注规则](#28-tagging-rules---%E6%A0%87%E6%B3%A8%E8%A7%84%E5%88%99)
  * [2\.9 on](#29-on)
* [3 Customizing Messages \- 自定义消息](#3-customizing-messages---%E8%87%AA%E5%AE%9A%E4%B9%89%E6%B6%88%E6%81%AF)
* [4 Validation Controller \- 验证控制器](#4-validation-controller---%E9%AA%8C%E8%AF%81%E6%8E%A7%E5%88%B6%E5%99%A8)
  * [4\.1 Creating a Controller](#41-creating-a-controller)
  * [4\.2 Setting the Validate Trigger \- 设置验证触发器](#42-setting-the-validate-trigger---%E8%AE%BE%E7%BD%AE%E9%AA%8C%E8%AF%81%E8%A7%A6%E5%8F%91%E5%99%A8)
  * [4\.3 validate &amp; reset \- 验证和重置](#43-validate--reset---%E9%AA%8C%E8%AF%81%E5%92%8C%E9%87%8D%E7%BD%AE)
  * [4\.4 addError &amp; removeError \- 添加和移除错误](#44-adderror--removeerror---%E6%B7%BB%E5%8A%A0%E5%92%8C%E7%A7%BB%E9%99%A4%E9%94%99%E8%AF%AF)
  * [4\.5 addRenderer &amp; removeRenderer \- 添加和删除渲染器](#45-addrenderer--removerenderer---%E6%B7%BB%E5%8A%A0%E5%92%8C%E5%88%A0%E9%99%A4%E6%B8%B2%E6%9F%93%E5%99%A8)
  * [4\.6 Event \- 事件](#46-event---%E4%BA%8B%E4%BB%B6)
* [5 Validator \- 验证器](#5-validator---%E9%AA%8C%E8%AF%81%E5%99%A8)
  * [5\.1 Creating a Validator \- 创建验证器](#51-creating-a-validator---%E5%88%9B%E5%BB%BA%E9%AA%8C%E8%AF%81%E5%99%A8)
* [6 Validate Binding Behavior \- 验证绑定行为](#6-validate-binding-behavior---%E9%AA%8C%E8%AF%81%E7%BB%91%E5%AE%9A%E8%A1%8C%E4%B8%BA)
* [7 Displaying Errors \- 显示错误](#7-displaying-errors---%E6%98%BE%E7%A4%BA%E9%94%99%E8%AF%AF)
* [8 Custom Renderers \- 定义渲染器](#8-custom-renderers---%E5%AE%9A%E4%B9%89%E6%B8%B2%E6%9F%93%E5%99%A8)
* [9 Entity Validation \- 实体验证](#9-entity-validation---%E5%AE%9E%E4%BD%93%E9%AA%8C%E8%AF%81)
* [10 Custom Rules \- 自定义规则](#10-custom-rules---%E8%87%AA%E5%AE%9A%E4%B9%89%E8%A7%84%E5%88%99)
* [11 Multiple Validation Controllers \- 多个验证控制器](#11-multiple-validation-controllers---%E5%A4%9A%E4%B8%AA%E9%AA%8C%E8%AF%81%E6%8E%A7%E5%88%B6%E5%99%A8)
* [12 Integration With Other Libraries \- 与其他库的集成](#12-integration-with-other-libraries---%E4%B8%8E%E5%85%B6%E4%BB%96%E5%BA%93%E7%9A%84%E9%9B%86%E6%88%90)
* [13 Integrating with Aurelia\-I18N \- 与Aurelia\-I18N集成](#13-integrating-with-aurelia-i18n---%E4%B8%8Eaurelia-i18n%E9%9B%86%E6%88%90)
  * [13\.1 Creating the view and view\-model \- 创建视图和视图模型](#131-creating-the-view-and-view-model---%E5%88%9B%E5%BB%BA%E8%A7%86%E5%9B%BE%E5%92%8C%E8%A7%86%E5%9B%BE%E6%A8%A1%E5%9E%8B)
  * [13\.2 Translation Files \- 翻译文件](#132-translation-files---%E7%BF%BB%E8%AF%91%E6%96%87%E4%BB%B6)
* [14 Server\-Side Validation \- 服务端验证](#14-server-side-validation---%E6%9C%8D%E5%8A%A1%E7%AB%AF%E9%AA%8C%E8%AF%81)


## 1 Introduction - 简介

本文介绍了使用Aurelia的验证插件进行验证的基础知识。您将了解如何使用连贯规则API向应用程序添加验证，并对模板进行最小程度的更改。

首先，你需要使用`npm install aurelia-validation`或者`jspm install aurelia-validation`来安装`aurelia-validation`。然后，将`.plugin(PLATFORM.moduleName('aurelia-validation'))`添加到`main.js`的配置中，以确保在应用程序启动时加载插件。

## 2 Defining Rules - 定义规则

Aurelia验证的标准规则引擎使用流畅的语法来定义一组规则。语法有五个部分：

1.  选择一个属性使用 `.ensure`
2.  使用`.required`, `.matches`等将规则与属性关联起来
3.  使用`.withMessage`, `.when`等自定义属性规则
4.  排序规则使用 `.then`
5.  将规则集应用于使用的类或实例 `.on`

### 2.1 ensure - 确保

要开始定义规则集，请使用`ValidationRules`类。首先使用`ValidationRules.ensure(...)`锁定一个属性。`ensure`方法接受一个表示属性名的参数。参数可以是字符串或简单的属性访问表达式。如果你正在使用TypeScript，你可能会想要使用一个属性访问表达式，因为你可以得到智能感知、重构的好处，并且避免使用可能会带来维护问题的“魔法字符串”。

**Ensure**

``` javascript
  ValidationRules
    .ensure('firstName')...
  
  ValidationRules
    .ensure(p => p.firstName)...
```

### 2.2 displayName - 显示名称

Once you've targetted a property using `ensure` you can define the property's display name using `.displayName(name: string|ValidationDisplayNameAccessor)`. Display names are used in validation messages. Specifying a display name is optional. If you do not explicitly set the display name the validation engine will attempt to compute the display name for you by splitting the property name on upper-case letters. A `firstName` property's display name would be `First Name`.
  
  一旦你使用确保来指定一个属性，你就可以使用`.displayName(name: string|ValidationDisplayNameAccessor)`来定义属性的显示名。显示名称用于验证消息。指定显示名称是可选的。如果您没有显式地设置显示名称，验证引擎将尝试通过将属性名称拆分为大写字母来为您计算显示名称。 `firstName`属性的显示名应该是 `First Name`。
  
**displayName**

``` javascript
 ValidationRules
    .ensure('ssn').displayName('SSN')...
```

  ### 2.3 Applying Rules - 应用规则

在使用 `ensure` 锁定属性并可选地设置其显示名称之后，可以开始使用内置规则方法将规则与属性关联起来:

*   `required()` validates the property is not null, undefined or whitespace.
*   `matches(regex)`验证属性是否与指定的正则表达式匹配。
*   `email()` validates an email address.
*   `minLength(length)` and `maxLength(length)` validate the length of string properties.
*   `minItems(count)` and `maxItems(count)` 验证数组中的项数。
*   `min(constaint)`, `max(constraint)`, `range(min, max)`, and `between(min, max)` 验证number属性的值.
*   `equals(expectedValue)` validates the property equals the expected value.
*   `satisfies((value: any, object?: any) => boolean|Promise<boolean>)` validates the supplied function returns `true` or a `Promise` that resolves to `true`. 验证提供的函数返回`true`或解析为 `true`的承诺`Promise`。该函数将使用两个参数调用：
    *  属性的当前值。
    *  属性所属的对象。

### 2.4 withMessage - 重写消息

所有规则都有一个标准消息，可以使用`.withMessage(message)`对其逐个进行覆盖。`message`参数是一个字符串，它将被解释为插值绑定表达式，并在发生验证错误时根据经过验证的对象进行计算。插值绑定表达式可以访问对象的任何属性以及下面列出的上下文属性：

*   `$displayName`: 属性的显示名称。
*   `$propertyName`: 属性的名称。
*   `$value`: 属性值(在执行验证规则时)。
*   `$object`: 拥有该属性的对象。
*   `$config`: 包含规则配置的对象。例如，`minLength`规则的配置将有一个`length`属性。
*   `$getDisplayName`: 返回给定属性名的可显示名称(与属性的显示名称无关)，以大写字母分隔，确保首字母大写。

Here's an example:

  **withMessage**

``` javascript
  ValidationRules
    .ensure('ssn').displayName('SSN')
      .required().withMessage(`\${$displayName} cannot be blank.`);
      .matches(/\d{3}-\d{2}-\d{4}/).withMessage(`"\${$value}" is not a valid \${$displayName}.`);
  
```

  ### 2.5 withMessageKey 

另一种逐个重写消息的方法是使用`.withMessageKey(key)` fluent API。Key是一个字符串，表示`validationMessages`字典中的一个键。可以使用以下代码向字典添加新键：

**withMessageKey**

``` JavaScript
 import {validationMessages} from 'aurelia-validation';
  
  validationMessages['invalidAirportCode'] = `\${$displayName} is not a valid airport code.`;
  
  ValidationRules
    .ensure('airportCode')
      .matches(/^[A-Z]{3}$/).withMessageKey('invalidAirportCode')...
```

### 2.6 Conditional Validation - 条件式验证

您可能会遇到这样的情况，即只希望在满足某些条件时对规则进行评估。使用`.when(condition: (object) => boolean)` fluent API定义一个条件，该条件必须在计算规则之前满足。当接受一个参数时，函数接受该对象并返回一个布尔值，该布尔值指示是否应该(`true`)或不应该(`false`)计算规则。
  
**Conditional Validation**

``` JavaScript
 ValidationRules
    .ensure('email')
      .email()
      .required()
        .when(order => order.shipmentNotifications)
        .withMessage('Email is required when shipment notifications have been requested.');
```

### 2.7 Sequencing Rule Evaluation - 排序规则评估

规则并行计算。使用`.then()`方法将规则的评估推迟到`ensure`（确保）中的前一个规则被评估之后。

**Conditional Validation**

``` JavaScript
ValidationRules
    .ensure('email')
      .email()
      .required()
      .then()
      .satisfiesRule('emailNotAlreadyRegistered')
    .ensure('username')
      .required()
      .minLength(3)
      .maxLength(50)
      .then()
      .satisfiesRule('usernameNotInUse');
```


在上面的例子中，只有当email属性通过了`required()` and `email()`验证时，才会计算 `emailNotAlreadyRegistered`定制规则。同样，`usernameNotInUse`只有在 `required()`, `minLength(3)` and `maxLength(50)` 检查通过验证时才会被计算。

### 2.8 Tagging Rules - 标注规则

使用 `.tag(tag: string)`方法标记具有名称的特定属性规则。您可以使用 `let someRules = ValidationRules.taggedRules(rules, tag)`。要获得没有应用标记的规则，可以使用`let someRules = ValidationRules.untaggedRules(rules)`。当您要执行特定规则或规则子集时，这可能会派上用场。 ValidationController 的文档（如下）显示了如何验证特定的对象/属性/规则。您还可以将规则的子集与Validator API一起使用（也记录在下面）。

### 2.9 on

 一旦定义了规则集，就可以使用`.on`方法将它们应用到类中。这将确保验证引擎在评估特定对象时能够定位规则。
  
  **Applying Rules to a Class**

``` JavaScript
export class Person {
    firstName = '';
    lastName = '';
  }
  
  ValidationRules
    .ensure('firstName').required()
    .ensure('lastName').required()
    .on(Person);
```

  `.on` 也可以将规则应用到普通的JavaScript对象:

**Applying Rules to an Object**

``` JavaScript
 let patient = {
    firstName: '',
    lastName: ''
  };
  
  ValidationRules
    .ensure('firstName').required()
    .ensure('lastName').required()
    .on(patient);
```
不需要将规则直接应用于对象或类，可以使用 `.rules` 在变量或属性中捕获规则集：

  **Storing Rules in a Property**

``` JavaScript
  const personRules = ValidationRules
    .ensure('firstName').required()
    .ensure('lastName').required()
    .rules;
```

## 3 Customizing Messages - 自定义消息

前一节展示了如何定制单个属性规则的消息。您可以在`validationMessages`字典中替换规则的默认消息，从而在系统范围内覆盖消息：
  
**Overriding Messages**

``` JavaScript
import {validationMessages} from 'aurelia-validation';
  
  validationMessages['required'] = `\${$displayName} is missing!`;
```

You can override the `ValidationMessageProvider`'s `getMessage(key: string): Expression` 方法以启用更多动态消息逻辑：

  **Overriding getMessage**

``` JavaScript
 import {ValidationMessageProvider} from 'aurelia-validation';
  
  ValidationMessageProvider.prototype.standardGetMessage = ValidationMessageProvider.prototype.getMessage;
  ValidationMessageProvider.prototype.getMessage = function(key) {
    const translation = i18next.t(key);
    return this.parser.parse(translation);
  };
```

You can also override the `ValidationMessageProvider`'s `getDisplayName(propertyName: string, displayName: string): string` method:

  **Overriding getDisplayName**

``` javascript
 import {ValidationMessageProvider} from 'aurelia-validation';
  
  ValidationMessageProvider.prototype.getDisplayName = function(propertyName, displayName) {
    if (displayName !== null && displayName !== undefined) {
      return displayName;
    }
    return i18next.t(propertyName);
  };
```

  ## 4 Validation Controller - 验证控制器

`ValidationController`编排验证属性的UI过程，以响应各种触发器，并通过呈现器显示验证错误。通常，每个“form”视图模型都有一个验证控制器实例。根据用例，您可能有多个。

### 4.1 Creating a Controller 

验证控制器可以使用 `NewInstance` 解析器创建：

**Creating a Controller**

``` JavaScript
import {inject, NewInstance} from 'aurelia-dependency-injection';
  import {ValidationController} from 'aurelia-validation';
  
  @inject(NewInstance.of(ValidationController))
  export class RegistrationForm {
    controller = null;
  
    constructor(controller) {
      this.controller = controller;
    }
  }
```

  
或者使用 `ValidationControllerFactory`:


  **Creating a Controller**

``` JavaScript
 import {ValidationControllerFactory} from 'aurelia-validation';
  
  @inject(ValidationControllerFactory)
  export class RegistrationForm {
    controller = null;
  
    constructor(controllerFactory) {
      this.controller = controllerFactory.createForCurrentScope();
    }
  }
```
  
这两种技术都创建一个`ValidationController`的新实例，并在组件的容器中注册该实例，从而使验证库中的其他组件能够访问相应控制器实例，而不需要大量的样板代码或标记。

如果您希望在用视图模型和绑定连接控制器时完全显式，或者如果您需要在组件中使用多个控制器，您可以使用`Factory` 解析器或`ValidationControllerFactory`的 `create`方法来创建控制器实例。使用这些方法将不会在容器中自动注册控制器实例，这将防止控制器与绑定和呈现器的自动连接，并将迫使您在绑定中指定控制器实例，并手动向控制器添加呈现器。

### 4.2 Setting the Validate Trigger - 设置验证触发器

一旦你创建了一个控制器，你可以设置它的 `validationTrigger`要么`blur`, `change`, `changeOrBlur` or `manual`。默认值是`blur`，这意味着当绑定的关联元素“blur”(失去焦点)时，验证控制器将验证在绑定中访问的属性。

当触发器发生 `change`（更改）时，绑定对模型属性所做的每个更改都将触发对该属性的验证。使用 `throttle`, `debounce` and `updateTrigger`绑定行为，并结合更改验证触发器来定制行为。

使用`manual`触发器指示控制器不应自动验证绑定中使用的属性。只有在调用控制器的验证`validate`方法时才会显示错误，在调用控制器的重置 `reset`方法时才会清除错误。
  
  **Setting the validate trigger**

``` JavaScript
  import {ValidationControllerFactory, validateTrigger} from 'aurelia-validation';
  
  @inject(ValidationControllerFactory)
  export class RegistrationForm {
    controller = null;
  
    constructor(validationControllerFactory) {
      this.controller = validationControllerFactory.createForCurrentScope();
  
      this.controller.validateTrigger = validateTrigger.manual;
    }
  }
```

  
### 4.3 validate & reset - 验证和重置

您可以通过调用`validate()`方法强制验证控制器运行验证。Validate将运行验证，呈现结果并返回一个使用`ControllerValidateResult`实例解析的承诺 `Promise`。只有在出现意外的应用程序错误时，承诺才会拒绝。一定要像处理其他意外的应用程序错误一样处理这些拒绝。

调用不带参数的validate方法将验证向控制器注册的所有绑定和对象。您可以提供一条验证指令来将验证限制到特定对象、属性和规则集：

**validate**

``` JavaScript
controller.validate();
  controller.validate({ object: person });
  controller.validate({ object: person, rules: myRules });
  controller.validate({ object: person, propertyName: 'firstName' });
  controller.validate({ object: person, propertyName: 'firstName', rules: myRules });
  
  controller.validate()
    .then(result => {
      if (result.valid) {
        // validation succeeded
      } else {
        // validation failed
      }
    });
```

  
大多数情况下，您将使用 `ControllerValidateResult` 实例有自己的`rule`（规则） 和`valid`（有效）属性来确定验证是否通过或失败。对于由`controller.validate(...)`调用计算的每个规则，使用`results`属性访问`ValidateResult`。以及 `message`, `object` and `propertyName`属性。

与 `validate`方法相反的是`reset`。调用不带参数的重置将取消任何先前呈现的验证结果。您可以提供复位指令，将复位限制到特定的对象或属性。

**reset**

``` JavaScript
controller.reset();
  controller.reset({ object: person });
  controller.reset({ object: person, propertyName: 'firstName' });
```

  
### 4.4 addError & removeError - 添加和移除错误

您可能需要显示来自其他来源的验证错误。可能在试图保存更改时，服务器返回了一个业务规则错误。可以使用控制器的`addError(message: string, object: any, propertyName?: string): ValidateResult` 方法。该方法返回一个`ValidateResult`实例，可以使用`removeError(result: ValidateResult)`来反呈现错误。

### 4.5 addRenderer & removeRenderer - 添加和删除渲染器

验证控制器通过将错误发送到`ValidationRenderer`接口的实现来呈现错误。库附带了一个内置的呈现器，可以将错误“呈现”到一个数组属性中，以用于数据绑定/模板化。这将在下面的[显示错误](aurelia-doc://section/11/version/1.0.0)一节中讨论。您可以创建自己的自定义渲染器[custom renderer](aurelia-doc://section/12/version/1.0.0)，并使用`addRenderer(renderer)`方法将其添加到控制器的渲染器集合中。

### 4.6 Event - 事件

验证控制器有一个 `subscribe(callback: (event: ValidateEvent) => void)` 方法，您可以使用它来订阅验证和重置事件。无论何时调用控制器的验证和重置方法，都会调用回调。回调将传递一个实例`ValidateEvent`，其中包含可以用来确定总体有效性状态以及验证或重置调用结果的属性。更多信息请参考API文档。

## 5 Validator - 验证器

`Validator`是 `ValidationController`用来执行对象和属性验证的后台工作的接口。`aurelia-validation`插件附带一个名为 `StandardValidator`的接口实现，它知道如何评估由`aurelia-validation`的fluent API创建的规则。当您直接使用`Validator`来验证特定对象或属性时，不会有UI副作用—验证结果不会发送到验证呈现器。

### 5.1 Creating a Validator - 创建验证器

可以注入验证器：

**Creating a Validator**

``` JavaScript
 import {inject} from 'aurelia-dependency-injection';
  import {Validator} from 'aurelia-validation';
  
  @inject(Validator)
  export class RegistrationForm {
    validator = null;
  
    constructor(validator) {
      this.validator = validator;
    }
  }
```

使用Validator实例的 `validateObject` and `validateProperty` 方法来运行验证，而不会产生任何呈现副作用。这些方法返回一个使用 `ValidateResults`数组解析的 `Promise` 。
  

## 6 Validate Binding Behavior - 验证绑定行为

验证绑定行为可实现双向数据绑定的快速简便验证。

该行为向控制器注册绑定实例，从而使系统能够在发生验证触发器（blur / change）时验证绑定的关联属性。

绑定行为能够标识对象和属性名称，以在各种绑定表达式中进行验证：

**Automatic Binding Validation**

``` HTML
 <input type="text" value.bind="firstName & validate">
  
  <input type="text" value.bind="person.firstName & validate">
  
  <input type="text" value.bind="person['firstName'] | upperCase & validate">
  
  <input type="text" value.bind="currentEntity[p] & debounce & validate">
```
`validate`接受两个可选参数，使您能够显式地指定规则和控制器实例：
  

**Explicit Binding Validation**

``` HTML
  <input type="text" value.bind="firstName & validate:personController">
  
  <input type="text" value.bind="firstName & validate:personRules">
  
  <input type="text" value.bind="firstName & validate:personController:personRules">
```

`validate`绑定行为遵循关联控制器的行为的`validateTrigger` (`blur`, `change`, `changeOrBlur`, `manual`)。如果希望在特定绑定中使用不同的`validateTrigger`，请使用以下绑定行为之一来替代`& validate`：
  
*   `& validateOnBlur`: DOM blur事件触发验证。
*   `& validateOnChange`: 更改模型的数据输入项触发验证。
*   `& validateOnChangeOrBlur`: DOM blur 或数据输入项触发验证。
*   `& validateManually`: 当关联的元素被用户blurred（模糊）或更改时，绑定不会自动进行验证。

## 7 Displaying Errors - 显示错误

控制器公开了使用标准Aurelia模板技术创建错误用户界面时有用的属性:

*   `results`: 当前`ValidateResult`实例的数组。这些是验证单个规则的结果。
*   `errors`: 当前 `ValidateResult`实例的数组，其有效`valid`属性为 `false`。
*   `validating`: 指示控制器当前是否正在执行验证的布尔值。

假设您的视图模型有一个控制器属性，您可以使用repeat将一个简单的错误摘要添加到表单中:

**Simple Validation Summary**

``` HTML
 <form>
    <ul if.bind="controller.errors">
      <li repeat.for="error of controller.errors">
        ${error.message}
      </li>
    </ul>
    ...
    ...
  </form>
```

要构建更复杂的error uis，可能需要特定于特定绑定或绑定集的错误列表。`validation-errors`自定义属性创建一个数组，其中包含与出现在其上的元素及其后代元素相关的所有`validation-errors`。下面是一个使用引导样式表单标记的示例：

**validation-errors custom attribute**

``` HTML
  <form>
    <div class="form-group" validation-errors.bind="firstNameErrors"
          class.bind="firstNameErrors.length ? 'has-error' : ''">
      <label for="firstName">First Name</label>
      <input type="text" class="form-control" id="firstName"
              placeholder="First Name"
              value.bind="firstName & validate">
      <span class="help-block" repeat.for="errorInfo of firstNameErrors">
        ${errorInfo.error.message}
      </span>
    </div>
  
    <div class="form-group" validation-errors.bind="lastNameErrors"
          class.bind="lastNameErrors.length ? 'has-error' : ''">
      <label for="lastName">Last Name</label>
      <input type="text" class="form-control" id="lastName"
              placeholder="Last Name"
            value.bind="lastName & validate">
      <span class="help-block" repeat.for="errorInfo of lastNameErrors">
        ${errorInfo.error.message}
      </span>
    </div>
  </form>
```

  
第一个表单组div使用`validation-errors`自定义属性创建`firstNameErrors`属性。当数组中有项目时，bootstrap`has-error` 类被应用到form-group div中。每个错误消息都使用`help-block`（帮助块）范围显示在输入下方。使用相同的方法来显示lastName字段的错误。

`validation-errors` 自定义属性实现 `ValidationRenderer`接口。它并不直接通过DOM操作来显示错误，而是将错误“呈现”到一个数组属性中，以启用上面描述的数据绑定和模板场景。当“绑定”生命周期事件发生时，它还会使用`addRenderer`自动将自己添加到控制器中，当“取消绑定”复合生命周期事件发生时，它会使用`removeRenderer`方法将自己从控制器中移除。

## 8 Custom Renderers - 定义渲染器

上一节描述的模板方法可能需要比您希望在模板中包含的标记更多的标记。如果希望使用直接的DOM操作来呈现验证错误，可以实现自定义渲染器。

自定义渲染器实现一个方法接口: `render(instruction: RenderInstruction)`。 `RenderInstruction` 是一个具有两个属性的对象:要呈现的结果和要取消呈现的结果。下面是引导表单的示例实现：

**bootstrap-form-renderer.ts**

``` TypeScript
import {
    ValidationRenderer,
    RenderInstruction,
    ValidateResult
  } from 'aurelia-validation';
  
  export class BootstrapFormRenderer {
    render(instruction: RenderInstruction) {
      for (let { result, elements } of instruction.unrender) {
        for (let element of elements) {
          this.remove(element, result);
        }
      }
  
      for (let { result, elements } of instruction.render) {
        for (let element of elements) {
          this.add(element, result);
        }
      }
    }
  
    add(element: Element, result: ValidateResult) {
      if (result.valid) {
        return;
      }
  
      const formGroup = element.closest('.form-group');
      if (!formGroup) {
        return;
      }
  
      // add the has-error class to the enclosing form-group div
      formGroup.classList.add('has-error');
  
      // add help-block
      const message = document.createElement('span');
      message.className = 'help-block validation-message';
      message.textContent = result.message;
      message.id = `validation-message-${result.id}`;
      formGroup.appendChild(message);
    }
  
    remove(element: Element, result: ValidateResult) {
      if (result.valid) {
        return;
      }
  
      const formGroup = element.closest('.form-group');
      if (!formGroup) {
        return;
      }
  
      // remove help-block
      const message = formGroup.querySelector(`#validation-message-${result.id}`);
      if (message) {
        formGroup.removeChild(message);
  
        // remove the has-error class from the enclosing form-group div
        if (formGroup.querySelectorAll('.help-block.validation-message').length === 0) {
          formGroup.classList.remove('has-error');
        }
      }
    }
  }
```

要使用自定义渲染器，您需要实例化它并通过`addRenderer`方法将其传递给控制器。任何控制器的现有错误将立即渲染。您可以使用`removeRenderer`方法删除渲染程序。`removeRenderer`渲染器将取消渲染器之前渲染的任何错误。如果您选择在视图模型的 `activate` or `bind`方法中调用`addRenderer`，请确保在相应的`deactivate` or `unbind`方法中调用`removeRenderer`。
  
  
 >警告：渲染器示例使用`Element.closest`。您需要在Internet Explorer中[polyfill](https://github.com/jonathantneal/closest)此方法。

下面是演示“成功样式化”的引导窗体的另一个渲染程序。当属性从不确定有效性转换为有效或从无效转换为有效时，表单控件将以绿色突出显示。当属性从不确定的有效性转换为无效或从有效转换为无效时，表单控件将以红色突出显示，就像前面的引导表单渲染器示例一样。

**bootstrap-form-renderer.ts**

``` TypeScript
//TypeScript
export class BootstrapFormRenderer {
    render(instruction: RenderInstruction) {
      for (let { result, elements } of instruction.unrender) {
        for (let element of elements) {
          this.remove(element, result);
        }
      }
  
      for (let { result, elements } of instruction.render) {
        for (let element of elements) {
          this.add(element, result);
        }
      }
    }
  
    add(element: Element, result: ValidateResult) {
      const formGroup = element.closest('.form-group');
      if (!formGroup) {
        return;
      }
  
      if (result.valid) {
        if (!formGroup.classList.contains('has-error')) {
          formGroup.classList.add('has-success');
        }
      } else {
        // add the has-error class to the enclosing form-group div
        formGroup.classList.remove('has-success');
        formGroup.classList.add('has-error');
  
        // add help-block
        const message = document.createElement('span');
        message.className = 'help-block validation-message';
        message.textContent = result.message;
        message.id = `validation-message-${result.id}`;
        formGroup.appendChild(message);
      }
    }
  
    remove(element: Element, result: ValidateResult) {
      const formGroup = element.closest('.form-group');
      if (!formGroup) {
        return;
      }
  
      if (result.valid) {
        if (formGroup.classList.contains('has-success')) {
          formGroup.classList.remove('has-success');
        }
      } else {
        // remove help-block
        const message = formGroup.querySelector(`#validation-message-${result.id}`);
        if (message) {
          formGroup.removeChild(message);
  
          // remove the has-error class from the enclosing form-group div
          if (formGroup.querySelectorAll('.help-block.validation-message').length === 0) {
            formGroup.classList.remove('has-error');
          }
        }
      }
    }
  }
```

  
## 9 Entity Validation - 实体验证

到目前为止的示例显示了控制器验证`& validate`绑定中使用的特定属性。即使数据绑定中没有使用某些属性，控制器也可以验证整个实体。使用控制器的 `addObject(object, rules?)`方法选择这个“实体”样式验证。当控制器的`validate`方法被调用时，调用`addObject`将把指定的对象添加到控制器应该验证的对象集中。规则参数是可选的。当没有使用连贯语法的`.on` 方法指定对象的规则时，使用它。您可以从控制器的对象列表中删除对象，以使用 `removeObject(object)`进行验证。调用 `removeObject`将取消与该对象关联的任何错误。

您可能有与单个属性无关的规则。连贯规则语法有一个可以用来为整个对象定义规则的 `ensureObject()` 方法。  
  
  **ensureObject**

``` JavaScript
 export class Shipment {
    length = 0;
    width = 0;
    height = 0;
  }
  
  ValidationRules
    .ensureObject()
      .satisfies(obj => obj.length * obj.width * obj.height <= 50)
        .withMessage('Volume cannot be greater than 50 cubic centemeters.')
    .on(Shipment);
```

  

  ## 10 Custom Rules - 自定义规则

fluent API的`satisfies`（满足）方法支持快速自定义规则。如果您有一个需要多次使用的自定义规则，您可以使用`customRule`方法来定义它。定义之后，可以使用`satisfiesRule`应用规则。下面介绍如何定义和使用简单的日期验证规则：

**customRule**

``` JavaScript
ValidationRules.customRule(
    'date',
    (value, obj) => value === null || value === undefined || value instanceof Date,
    `\${$displayName} must be a Date.`
  );
  
  ValidationRules
    .ensure('startDate')
      .required()
      .satisfiesRule('date');
```

您经常需要将参数传递给自定义规则。下面是一个接受“min”和“max”参数的“整数范围”规则的示例。请注意`customRule`方法的最后一个参数，它将预期的参数打包到一个“config”对象中。当发生错误时，在计算验证消息时使用配置对象，使消息表达式能够访问规则的配置。
  

**customRule**

``` JavaScript
ValidationRules.customRule(
    'integerRange',
    (value, obj, min, max) => value === null || value === undefined
      || Number.isInteger(value) && value >= min && value <= max,
    `\${$displayName} must be an integer between \${$config.min} and \${$config.max}.`,
    (min, max) => ({ min, max })
  );
  
  ValidationRules
    .ensure('volume')
      .required()
      .satisfiesRule('integerRange', 1, 5000);
```
您可能已经注意到上面的自定义规则示例认为`null` and `undefined`是有效的。这是有意为之的 —— **规则应该遵循单一责任原则**，并且只验证一个约束。`.required()` 规则的作用是验证是否填写了数据，您不应该将必需的检查逻辑添加到自定义规则中，原因有两个：
  

1.  规则重用——如果我们的“integerRange”规则也执行“required”检查，我们就不能在可选字段上使用它。
2.  消息相关性——如果我们的“integerRange”规则也做了“required”检查，当用户应该得到“required”错误消息时，他们将得到“range”错误消息。

当您编写自定义规则时，该函数在规则“satisfied 满意” /“valid 有效”时应返回 `true`，而在规则“broken 无效” /“invalid 无效”时应返回`false`。您可以选择返回一个解析为`true` or `false`的`Promise`承诺。除非出现意外异常，否则不应拒绝承诺。承诺拒绝不用于控制流或表示“invalid 无效”状态。

自定义规则的一个常见应用程序是确认两个密码项是否匹配。下面是一个示例，演示如何做到这一点：

**confirm password entries match**

``` JavaScript
 ValidationRules.customRule(
    'matchesProperty',
    (value, obj, otherPropertyName) =>
      value === null
      || value === undefined
      || value === ''
      || obj[otherPropertyName] === null
      || obj[otherPropertyName] === undefined
      || obj[otherPropertyName] === ''
      || value === obj[otherPropertyName],
    '${$displayName} must match ${$getDisplayName($config.otherPropertyName)}', otherPropertyName => ({ otherPropertyName })
  );
  
  ValidationRules
    .ensure(a => a.password)
      .required()
    .ensure(a => a.confirmPassword)
      .required()
      .satisfiesRule('matchesProperty', 'password');
```

  
**confirm password entries match**

``` HTML
 <div class="form-group">
    <label class="control-label" for="password">Password</label>
    <input type="password" class="form-control" id="password" placeholder="Password"
            value.bind="password & validate"
            change.delegate="confirmPassword = ''">
  </div>
  
  <div class="form-group">
    <label class="control-label" for="confirmPassword">Confirm Password</label>
    <input type="password" class="form-control" id="confirmPassword" placeholder="Confirm Password"
            value.bind="confirmPassword & validate">
  </div>
```

  

  ## 11 Multiple Validation Controllers - 多个验证控制器

如果您有两个需要单独验证的表单，当然建议您在单独的组件中实现它们。但是，通过创建多个验证控制器，可以在同一个组件中执行两个或多个独立验证。

For instance: imagine you're building an order form for an Italian restaurant. The customer can order pizza or pasta. If the customer makes a mistake in ordering a pizza, they should be able to order a pasta regardless. In that case your view model would look like this:

例如:假设您正在为一家意大利餐厅构建一个订单表单。顾客可以点披萨或意大利面。如果顾客在点披萨时出了错，他们应该可以点意大利面。在这种情况下，视图模型应该是这样的：

**ViewModel with multiple validation controllers**

``` JavaScript
  import {inject, NewInstance} from 'aurelia-framework';
  import {ValidationController, ValidationRules} from 'aurelia-validation';
  
  @inject(NewInstance.of(ValidationController), NewInstance.of(ValidationController))
  export class ItalianRestaurant {
    pizza;
    pasta;
  
    pizzaValidationController;
    pastaValidationController
  
    constructor(pizzaValidationController, pastaValidationController) {
      this.pizzaValidationController = pizzaValidationController;
      this.pastaValidationController = pastaValidationController;
  
      const pizzaRules = ValidationRules
        .ensure((res: ItalianRestaurant) => res.pizza).required()
        .rules;
      this.pizzaValidationController.addObject(this, pizzaRules);
  
      const pastaRules = ValidationRules
        .ensure((res: ItalianRestaurant) => res.pasta).required()
        .rules;
      this.pastaValidationController.addObject(this, pastaRules);
    }
  
    orderPizza() {
      this.pizzaValidationController.validate()
        .then(result => {
          if (result.valid) {
            alert("Ordering this pizza: " + this.pizza);
          }
        });
    }
  
    orderPasta() {
      this.pastaValidationController.validate()
        .then(result => {
          if (result.valid) {
            alert("Ordering this pasta: " + this.pasta);
          }
        });
    }
  }
  
```
In your view you need to take care to associate each input with the correct validation controller:

在视图中，您需要注意将每个输入与正确的验证控制器关联起来：  


**I18N View**

``` HTML
<template>
    <form validation-errors="errors.bind: pizzaErrors; controller.bind: pizzaValidationController"
          submit.delegate="orderPizza()">
      <label for="pizza">Choose a pizza:</label>
      <input id="pizza" value.bind="pizza & validateManually:pizzaValidationController">
      <span class="help-block" repeat.for="errorInfo of pizzaErrors">
        ${errorInfo.error.message}
      </span>
      <input type="submit" value="Order pizza!">
    </form>
  
    <form validation-errors="errors.bind: pastaErrors; controller.bind: pastaValidationController"
          submit.delegate="orderPasta()">
      <label for="pasta">Choose a pasta:</label>
      <input id="pasta" value.bind="pasta & validateManually:pastaValidationController">
      <span class="help-block" repeat.for="errorInfo of pastaErrors">
        ${errorInfo.error.message}
      </span>
      <input type="submit" value="Order pasta!">
    </form>
  </template>
```

  

  In the forms above you can see that each `validation-errors` attribute and each `validateManually` binding behavior is bound to the appropriate validation controller. This needs to be specified each time, since by default the attribute and the binding behavior will ask the container for a `ValidationController` instance not knowing which one it will get.
  
在上面的表单中，您可以看到每个`validation-errors`属性和每个`validateManually`绑定行为都绑定到适当的验证控制器。这需要每次都指定，因为在默认情况下，属性和绑定行为将向容器请求`ValidationController`实例，而不知道它将获得哪个实例。

  ## 12 Integration With Other Libraries - 与其他库的集成

In `aurelia-validation` the object and property validation work is handled by the `StandardValidator` class which is an implementation of the `Validator` interface. The `StandardValidator` is responsible for applying the rules created with aurelia-validation's fluent syntax. You may not need any of this machinery if you have your own custom validation engine or if you're using a client-side data management library like [Breeze](http://www.getbreezenow.com/breezejs) which has its own validation logic. You can replace the `StandardValidator` with your own implementation when the plugin is installed. Here's an example using breeze:

在`aurelia-validation`中，对象和属性验证工作由`StandardValidator`类处理，它是`Validator`接口的一个实现。`StandardValidator`负责应用用 aurelia-validation 的流畅语法创建的规则。如果您有自己的定制验证引擎，或者您使用的是像[Breeze](http://www.getbreezenow.com/breezejs)这样的客户端数据管理库，它有自己的验证逻辑，那么您可能不需要这些机制。您可以在安装插件时用自己的实现替换`StandardValidator`。这里有一个使用 breeze 的例子：

**breeze-validator**

``` JavaScript
 import {ValidateResult} from 'aurelia-validation';
  
  export class BreezeValidator {
    validateObject(object) {
      if (object.entityAspect.validateEntity()) {
        return [];
      }
      return object.entityAspect
        .getValidationErrors()
        .map(({ errorMessage, propertyName, key }) => new ValidateResult(key, object, propertyName, false, errorMessage));
    }
  
    validateProperty(object, propertyName) {
      if (object.entityAspect.validateProperty(propertyName)) {
        return [];
      }
      return object.entityAspect
        .getValidationErrors(propertyName)
        .map(({ errorMessage, propertyName, key }) => new ValidateResult(key, object, propertyName, false, errorMessage));
    }
  }
```

  

  
**main**

``` JavaScript
 import {BreezeValidator} from './breeze-validator';
  
  export function configure(aurelia) {
    aurelia.use
      .standardConfiguration()
      .plugin(PLATFORM.moduleName('aurelia-validation'), config => config.customValidator(BreezeValidator))
      ...
  
    ...
  }
```

  

  ## 13 Integrating with Aurelia-I18N - 与Aurelia-I18N集成

`aurelia-i18n`  是Aurelia的官方I18N插件。有关如何在应用程序中使用`aurelia-i18n`的信息，请参阅项目的[自述](https://github.com/aurelia/i18n/blob/master/README.md)。

用`aurelia-validation`验证`aurelia-i18n`是很容易的。所有标准验证消息都由`ValidationMessageProvider`类提供。要转换消息，请使用使用`aurelia-i18n`来获取messages/property-names 的翻译版本的实现重写`getMessage(key)` and `getDisplayName(propertyName, displayName)` 方法。

Here's how to override the methods, in your main.js, during application startup:

**main.js**

``` JavaScript
 import {I18N} from 'aurelia-i18n';
  import {ValidationMessageProvider} from 'aurelia-validation';
  
  export function configure(aurelia) {
    ...
    ...
  
    ValidationMessageProvider.prototype.getMessage = function(key) {
      const i18n = aurelia.container.get(I18N);
      const translation = i18n.tr(`errorMessages.${key}`);
      return this.parser.parse(translation);
    };
  
    ValidationMessageProvider.prototype.getDisplayName = function(propertyName, displayName) {
      if (displayName !== null && displayName !== undefined) {
        return displayName;
      }
      const i18n = aurelia.container.get(I18N);
      return i18n.tr(propertyName);
    };
  
    ...
    ...
  }
```

  
### 13.1 Creating the view and view-model - 创建视图和视图模型

一旦在 `ValidationMessageProvider`中覆盖了必要的方法，就可以创建视图和视图模型了。下面是一个简单的多语言表单的视图，其中包含姓名字段。所有静态文本都使用[T binding behavior](https://github.com/aurelia/i18n#translating-with-the-tbindingbehavior) 行为进行翻译。在表单提交时进行验证，开关语言按钮演示i18n功能。

**I18N View**

``` HTML
<template>
    <form>
      <label>${'firstName' & t}: <br>
        <input type="text" value.bind="firstName & validate">
      </label>
      <label>${'lastName' & t}: <br>
        <input type="text" value.bind="lastName & validate">
      </label>
      <button click.delegate="submit()">${'submit' & t}</button>
    </form>
  
    <div>
      <h3>${'latestValidationResult' & t}: ${message}</h3>
      <ul if.bind="controller.errors.length">
        <li repeat.for="error of controller.errors">
          ${error.message}
        </li>
      </ul>
    </div>
  
    <button click.trigger="switchLanguage()">${'switchLanguage' & t}</button>
  <template>
```

Here's the view model:

**I18N ViewModel**

``` JavaScript
 import {inject, NewInstance} from 'aurelia-framework';
  import {I18N} from 'aurelia-i18n';
  import {ValidationRules, ValidationController} from 'aurelia-validation';
  
  @inject(NewInstance.of(ValidationController), I18N)
  export class App {
    firstName;
    lastName;
  
    controller;
    i18n;
    message = '';
  
    constructor(controller, i18n) {
      this.controller = controller;
      this.i18n = i18n;
  
      ValidationRules
        .ensure((m: App) => m.firstName).required()
        .ensure((m: App) => m.lastName).required()
        .on(this);
    }
  
    submit() {
      this.executeValidation();
    }
  
    switchLanguage() {
      const currentLocale = this.i18n.getLocale();
      this.i18n.setLocale(currentLocale === 'en' ? 'de' : 'en')
        .then(() => this.executeValidation());
    }
  
    executeValidation() {
      this.controller.validate()
        .then(errors => {
          if (errors.length === 0) {
            this.message = this.i18n.tr('allGood');
          } else {
            this.message = this.i18n.tr('youHaveErrors');
          }
        });
    }
  }
```

  
### 13.2 Translation Files - 翻译文件

最后，创建翻译文件，其中包括每个propertyName和每个验证消息键的翻译。下面是上面示例的德语和英语文件。注意， `errorMessages`部分提供了`required`规则的翻译。实际上，您需要对使用的每个规则进行翻译。查看完整列表的[validationMessages](https://github.com/aurelia/validation/blob/master/src/implementation/validation-messages.ts)。

**Translation files**

``` JSON
 // file: locales/de/translation.json
  {
    "firstName": "Vorname",
    "lastName": "Nachname",
    "submit": "Abschicken",
    "switchLanguage": "Sprache wechseln",
    "youHaveErrors": "Es gibt Fehler!",
    "allGood": "Alles in Ordnung!",
    "latestValidationResult": "Aktuelles Validierungsergebnis",
    "errorMessages": {
      "required": "${$displayName} fehlt!"
    }
  }
  
  // file: locales/en/translation.json
  {
    "firstName": "First name",
    "lastName": "Last name",
    "submit": "Submit",
    "switchLanguage": "Switch language",
    "youHaveErrors": "You have errors!",
    "allGood": "All is good!",
    "latestValidationResult": "Latest validation result",
    "errorMessages": {
      "required": "${$displayName} is missing!"
    }
  }
```

  
## 14 Server-Side Validation - 服务端验证

fluent 规则API和验证器API可以在NodeJS应用程序的服务器端使用。