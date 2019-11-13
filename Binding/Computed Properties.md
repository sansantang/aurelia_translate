原文：https://aurelia.io/docs/binding/computed-properties

## Introduction

有时，在访问属性时需要返回一个动态计算的值，或者您可能希望反映内部变量的状态，而不需要使用显式方法调用。在JavaScript中，这可以通过使用getter来实现。

这是一个示例`Person`类，该类公开了`fullName`属性，该属性使用`firstName` 和 `lastName`属性计算其值。

**Computed Properties**
```javascript
export class Person {
    firstName = 'John';
    lastName = 'Doe';
  
    get fullName() {
      return `${this.firstName} ${this.lastName}`;
    }
  }
```

要绑定到计算属性(如`fullName`)，不需要执行任何特殊操作。绑定系统将检查属性的描述符[descriptor](https://developer.mozilla.org/zh-cn/docs/Web/JavaScript/Reference/Global_Objects/Object/getOwnPropertyDescriptor)，确定属性的值是由函数计算的，并选择脏检查观察策略。脏检查意味着绑定系统将定期检查属性值的变化，并根据需要更新视图。这意味着属性的getter函数将被多次执行，大约每120毫秒执行一次。但是，在大多数情况下，这不是问题，如果您正在使用大量的计算属性，或者如果您的getter函数足够复杂，您可能需要考虑向绑定系统提供关于观察什么的提示，这样它就不需要使用脏检查。这就是`@computedFrom`装饰器发挥作用的地方：

**Computed Properties**
```javascript
import {computedFrom} from 'aurelia-framework';
  
  export class Person {
    firstName = 'John';
    lastName = 'Doe';
  
    @computedFrom('firstName', 'lastName')
    get fullName() {
      return `${this.firstName} ${this.lastName}`;
    }
  }
```

`@computedFrom`告诉绑定系统要观察哪个表达式。当这些表达式更改时，绑定系统将重新评估属性(执行getter)。这消除了对脏检查的需要，可以提高性能。`@computedFrom`参数可以是上面显示的简单属性名，也可以是更复杂的表达式，比如`@computedFrom('event.startDate', 'event.endDate')`。