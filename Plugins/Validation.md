原文：https://aurelia.io/docs/plugins/validation


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

## 5 Validator

`Validator` is an interface used by the `ValidationController` to do the behind-the-scenes work of validating objects and properties. The `aurelia-validation` plugin ships with an implementation of this interface called the `StandardValidator`, which knows how to evaluate rules created by `aurelia-validation`'s fluent API. When you use a `Validator` directly to validate a particular object or property, there are no UI side-effects- the validation results are not sent to the the validation renderers.

### 5.1 Creating a Validator

Validators can be injected:

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

  
Use the Validator instance's `validateObject` and `validateProperty` methods to run validation without any render side-effects. These methods return a `Promise` that resolves with an array of `ValidateResults`.

## 6 Validate Binding Behavior

The `validate` binding behavior enables quick and easy validation for two-way data-bindings. The behavior registers the binding instance with a controller, enabling the system to validate the binding's associated property when the validate trigger occurs (blur / change). The binding behavior is able to identify the object and property name to validate in all sorts of binding expressions:

**Automatic Binding Validation**

``` HTML
 <input type="text" value.bind="firstName & validate">
  
  <input type="text" value.bind="person.firstName & validate">
  
  <input type="text" value.bind="person['firstName'] | upperCase & validate">
  
  <input type="text" value.bind="currentEntity[p] & debounce & validate">
```

  
`validate` accepts a couple of optional arguments enabling you to explicitly specify the rules and controller instance:

**Explicit Binding Validation**

``` HTML
  <input type="text" value.bind="firstName & validate:personController">
  
  <input type="text" value.bind="firstName & validate:personRules">
  
  <input type="text" value.bind="firstName & validate:personController:personRules">
```

  
The `validate` binding behavior obeys the associated controller's `validateTrigger` (`blur`, `change`, `changeOrBlur`, `manual`). If you'd like to use a different `validateTrigger` in a particular binding use one of the following binding behaviors in place of `& validate`:

*   `& validateOnBlur`: the DOM blur event triggers validation.
*   `& validateOnChange`: data entry that changes the model triggers validation.
*   `& validateOnChangeOrBlur`: DOM blur or data entry triggers validation.
*   `& validateManually`: the binding is not validated automatically when the associated element is blurred or changed by the user.

## 7 Displaying Errors

The controller exposes properties that are useful for creating error UIs using standard Aurelia templating techniques:

*   `results`: An array of the current `ValidateResult` instances. These are the results of validating individual rules.
*   `errors`: An array of the current `ValidateResult` instances whose `valid` property is false.
*   `validating`: a boolean that indicates whether the controller is currently executing validation.

Assuming your view-model had a controller property you could add a simple error summary to your form using a repeat:

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

  To build more sophisticated error UIs you might need a list of errors specific to a particular binding or set of bindings. The `validation-errors` custom attribute creates an array containing all validation errors relevant to the element the `validation-errors` attribute appears on and its descendent elements. Here's an example using bootstrap style form markup:

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

  
This first form-group div uses the `validation-errors` custom attribute to create a `firstNameErrors` property. When there are items in the array the bootstrap `has-error` class is applied to the form-group div. Each error message is displayed below the input using `help-block` spans. The same approach is used to display the lastName field's errors.

The `validation-errors` custom attribute implements the `ValidationRenderer` interface. Instead of doing direct DOM manipulation to display the errors it "renders" the errors to an array property to enable the data-binding and templating scenarios illustrated above. It also automatically adds itself to the controller using `addRenderer` when its "bind" lifecycle event occurs and removes itself from the controller using the `removeRenderer` method when its "unbind" composition lifecycle event occurs.

## 8 Custom Renderers

The templating approaches described in the previous section may require more markup than you wish to include in your templates. If you would prefer use direct DOM manipulation to render validation errors you can implement a custom renderer.

Custom renderers implement a one-method interface: `render(instruction: RenderInstruction)`. The `RenderInstruction` is an object with two properties: the results to render and the results to unrender. Below is an example implementation for bootstrap forms:

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

  
To use a custom renderer you'll need to instantiate it and pass it to your controller via the `addRenderer` method. Any of the controller's existing errors will be renderered immediately. You can remove a renderer using the `removeRenderer` method. Removing a renderer will unrender any errors that renderer had previously rendered. If you choose to call `addRenderer` in your view-model's `activate` or `bind` methods, make sure to call `removeRenderer` in the corresponding `deactivate` or `unbind` methods.
  
 >警告：The renderer example uses `Element.closest`. You'll need to [polyfill](https://github.com/jonathantneal/closest) this method in Internet Explorer.

Here's another renderer for bootstrap forms that demonstrates "success styling". When a property transitions from indeterminate validity to valid or from invalid to valid, the form control will highlight in green. When a property transitions from indeterminate validity to invalid or from valid to invalid, the form control will highlight in red, just like in the previous bootstrap form renderer example.

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

  
## 9 Entity Validation

The examples so far show the controller validating specific properties used in `& validate` bindings. The controller can validate whole entities even if some of the properties aren't used in data bindings. Opt in to this "entity" style validation using the controller's `addObject(object, rules?)` method. Calling `addObject` will add the specified object to the set of objects the controller should validate when its `validate` method is called. The `rules` parameter is optional. Use it when the rules for the object haven't been specified using the fluent syntax's `.on` method. You can remove objects from the controller's list of objects to validate using `removeObject(object)`. Calling `removeObject` will unrender any errors associated with the object.

You may have rules that are not associated with a single property. The fluent rule syntax has an `ensureObject()` method you can use to define rules for the whole object.
  
  
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

  

  ## 10 Custom Rules

The fluent API's `satisfies` method enables quick custom rules. If you have a custom rule that you need to use multiple times you can define it using the `customRule` method. Once defined, you can apply the rule using `satisfiesRule`. Here's how you could define and use a simple date validation rule:

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

  
You will often need to pass arguments to your custom rule. Below is an example of an "integer range" rule that accepts "min" and "max" arguments. Notice the last parameter to the `customRule` method packages up the expected parameters into a "config" object. The config object is used when computing the validation message when an error occurs, enabling the message expression to access the rule's configuration.

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

  
You may have noticed the custom rule examples above consider `null` and `undefined` to be valid. This is intentional- **a rule should follow the single responsibility principle** and validate only one constraint. It's the `.required()` rule's job to validate whether the data is filled in, you shouldn't add required checking logic to your custom rule for two reasons:

1.  Rule reuse- if our "integerRange" rule also does "required" checks, we can't use it on optional fields.
2.  Messages Relevance- if our "integerRange" rule also does "required" checks the user will get "range" error messages when we they should have gotten "required" error messages.

When you write a custom rule, the function should return `true` when the rule is "satisfied" / "valid" and `false` when the rule is "broken" / "invalid". Optionally you can return a `Promise` that resolves to `true` or `false`. The promise should not reject unless there's an unexpected exception. Promise rejection is not used for control flow or to represent "invalid" status.

A common application of a custom rule is to confirm that two password entries match. Here is a example showing how you can do that:

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

  

  ## 11 Multiple Validation Controllers

If you have two forms that need to be independently validated, it is of course recommended you implement them in separate components. However, it is technically possible to do two or more independant validations in the same component by creating multiple validation controllers.

For instance: imagine you're building an order form for an Italian restaurant. The customer can order pizza or pasta. If the customer makes a mistake in ordering a pizza, they should be able to order a pasta regardless. In that case your view model would look like this:

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
  
  ## 12 Integration With Other Libraries

In `aurelia-validation` the object and property validation work is handled by the `StandardValidator` class which is an implementation of the `Validator` interface. The `StandardValidator` is responsible for applying the rules created with aurelia-validation's fluent syntax. You may not need any of this machinery if you have your own custom validation engine or if you're using a client-side data management library like [Breeze](http://www.getbreezenow.com/breezejs) which has its own validation logic. You can replace the `StandardValidator` with your own implementation when the plugin is installed. Here's an example using breeze:

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

  

  ## 13 Integrating with Aurelia-I18N

`aurelia-i18n` is Aurelia's official I18N plugin. Check out the project's [readme](https://github.com/aurelia/i18n/blob/master/README.md) for information on how to use `aurelia-i18n` in your application.

Integrating `aurelia-i18n` with `aurelia-validation` is easy. All standard validation messages are supplied by the `ValidationMessageProvider` class. To translate the messages, override the `getMessage(key)` and `getDisplayName(propertyName, displayName)` methods with implementations that use `aurelia-i18n` to fetch translated versions of the messages/property-names.

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

  
### 13.1 Creating the view and view-model

Once you've overriden the necessary methods in `ValidationMessageProvider` you're ready to create a view and view-model. Here's a view for a simple multi-language form with first and last name fields. All static text is translated using the [T binding behavior](https://github.com/aurelia/i18n#translating-with-the-tbindingbehavior) . Validation occurs on form submission and a switch language button demonstrates the i18n capabilities.

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

  
### 13.2 Translation Files

Last but not least, create translation files that include translations for each propertyName and each validation message key. Below are the German and English files for the example above. Notice the `errorMessages` section has the translation for the `required` rule. In practice, you would need translations for each rule that you use. Take a look at the [validationMessages](https://github.com/aurelia/validation/blob/master/src/implementation/validation-messages.ts) for the full list.

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

  
## 14 Server-Side Validation

The fluent rule API and Validator API can be used server-side in a NodeJS application.