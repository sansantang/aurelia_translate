原文：https://aurelia.io/docs/binding/binding-engine

* [1\.Introduction 简介](#1introduction-%E7%AE%80%E4%BB%8B)
* [2\.How to install 怎么安装](#2how-to-install-%E6%80%8E%E4%B9%88%E5%AE%89%E8%A3%85)
* [3\.Observing a property on an object 观察object上的属性](#3observing-a-property-on-an-object-%E8%A7%82%E5%AF%9Fobject%E4%B8%8A%E7%9A%84%E5%B1%9E%E6%80%A7)
* [4\.Observing a collection mutation 观察一个集合的突变](#4observing-a-collection-mutation-%E8%A7%82%E5%AF%9F%E4%B8%80%E4%B8%AA%E9%9B%86%E5%90%88%E7%9A%84%E7%AA%81%E5%8F%98)
* [5\.Observing an expression on an object 观察对象上的表达式](#5observing-an-expression-on-an-object-%E8%A7%82%E5%AF%9F%E5%AF%B9%E8%B1%A1%E4%B8%8A%E7%9A%84%E8%A1%A8%E8%BE%BE%E5%BC%8F)

## 1.Introduction 简介

绑定引擎是`aurelia-binding`模块的一个实用程序导出，它提供了一些用于处理观察的高级api，这些api利用了底层的aurelia绑定系统原语。


## 2.How to install 怎么安装

通过将`BindingEngine`注入到Aurelia应用程序中的任何类中来检索它的实例：
```
import { BindingEngine } from 'aurelia-framework'; // or 'aurelia-binding'
  
  export class MyViewModel {
  
    static inject() {
      return [BindingEngine]
    }
  
    constructor(bindingEngine) {
      //
    }
  }
```

> 注意:您也可以直接构造 BindingEngine 实例，或者从容器中获取它。该示例使用最简单的代码。


现在让我们用它来观察对象上的变化(数组也是对象)。通常，我们将使用`propertyObserver(obj, propertyName)`来处理对象属性值更改，使用`collectionObserver(collection)`来处理集合突变。


## 3.Observing a property on an object 观察object上的属性

*   `propertyObserver(obj, propertyName)` 返回一个 `PropertyObserver` 对象，带有一个可以像下面的示例一样使用的公共方法`subscribe`:

```
bindingEngine
      .propertyObserver(myObject, 'myObjectPropertyName')
      .subscribe((newValue, oldValue) => {
        // handle value change here
      });
```
## 4.Observing a collection mutation 观察一个集合的突变

*    `collectionObserver(collection)` 返回带有公共方法 `subscribe` 的 `CollectionObserver` 对象，可以像下面的示例一样使用该方法：

```
bindingEngine
      .collectionObserver(myCollection)
      .subscribe((splice) => {
        // do something with the mutated collection
      })
```


`splice` 是一个对象与 `ICollectionObserverSplice` 接口，源代码在 [here](https://github.com/aurelia/binding/blob/b42630b9ef94f84f39e450d959ddaa721d82e5d5/src/aurelia-binding.d.ts#L148)

## 5.Observing an expression on an object 观察对象上的表达式


通常，您希望观察一个属性访问链(或路径)，binding engine 也有一个API来支持这一点。您可以使用 `expressionObserver(obj, expressionString)` 来创建一个observer，它会在 `expressionString` 路径中的任何属性被更改时通知您，如下面的例子所示：

```
  bindingEngine
  .expressionObserver(this, 'project.name')
  .subscribe((newProjectName, oldProjectName) => {
    // do something with the new project name value
  });
```
  
