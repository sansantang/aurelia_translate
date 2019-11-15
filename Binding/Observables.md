原文：https://aurelia.io/docs/binding/observable-properties

* [1\.Observable Properties 监控属性](#1observable-properties-%E7%9B%91%E6%8E%A7%E5%B1%9E%E6%80%A7)
* [2\.Observing Collections 监控集合](#2observing-collections-%E7%9B%91%E6%8E%A7%E9%9B%86%E5%90%88)


## 1.Observable Properties 监控属性

您是否曾经需要在属性更改时执行某个操作?如果你有，这是可观察属性的一个很好的应用。

要观察一个属性，您需要用`@observable`装饰器装饰它，并定义一个方法作为变更处理程序。该方法可以接收两个参数:新值和旧值。您可以将任何业务逻辑放入此方法中。


按照惯例，更改处理程序是一个方法，其名称由属性名和文字值“Changed”组成。例如，如果您使用`@observable`修饰属性`color`，您必须定义一个名为 `colorChanged()`的方法作为更改处理程序。这里有一个例子：

**Observable Properties**

``` javascript
 import { observable } from 'aurelia-framework';
  
  export class Car {
    @observable color = 'blue';
  
    colorChanged(newValue, oldValue) {
      // this will fire whenever the 'color' property changes
    }
  }
```


>您不必检查`newValue` 和 `oldValue`是否不同。如果您指定了属性已经具有的值，则不会调用更改处理程序。


如果您不想使用约定，您可以通过设置`@observable`装饰器的`changeHandler`属性来定义变更处理程序的回调名称

**Observable Properties**

```javascript
 import { observable } from 'aurelia-framework';
  
  export class Car {
    @observable({ changeHandler: 'myChangeHandler' })
    color = 'blue';
  
    myChangeHandler(newValue, oldValue) {
      // this will fire whenever the 'color' property changes
    }
  }
```

如果你喜欢，也可以把`@observable`放在类上：

**Observable Properties**

```javascript
  import { observable } from 'aurelia-framework';
  
  @observable('color')
  @observable({ name: 'speed', changeHandler: 'speedChangeHandler' })
  export class Car {
    color = 'blue';
    speed = 300;
  
    colorChanged(newValue, oldValue) {
      // this will fire whenever the 'color' property changes
    }
  
    speedChangeHandler(newValue, oldValue) {
      // this will fire whenever the 'speed' property changes
    }
  }
```

>`@observable`*只*跟踪属性值的变化，而不跟踪值本身的变化。这意味着如果属性是一个数组，那么在添加、删除或编辑项时，change处理程序将不会触发。

  ## 2.Observing Collections 监控集合


使用集合观察器来观察对集合的更改。可以观察到的集合类型有[Array](https://developer.mozilla.org/zh-cn/docs/Web/JavaScript/Reference/Global_Objects/Array) , [Map](https://developer.mozilla.org/zh-cn/docs/Web/JavaScript/Reference/Global_Objects/Map) , 和 [Set](https://developer.mozilla.org/zh-cn/docs/Web/JavaScript/Reference/Global_Objects/Set) 。通过提供要观察的集合和回调函数来创建订阅。

  **Configuring a Collection Observer**
  

``` javascript?linenums
  import {BindingEngine} from 'aurelia-framework';
  
  @inject(BindingEngine)
  export class App {
  
    myCollection = ["foo"];
  
    constructor(bindingEngine) {
      let subscription = bindingEngine.collectionObserver(this.myCollection)
        .subscribe(this.collectionChanged.bind(this));
    }
  
    collectionChanged(splices) {
        // This will fire any time the collection is modified. 
    }
  }
```

example callback functions used to observe each of the different collection types.
回调将接收一个拼接数组，该数组提供有关已`detcted`已删除更改的信息。根据观察到的集合类型，拼接的属性可能会有所不同。在这里您可以看到用于观察每个不同集合类型的回调函数示例。

  **Array Splice**

``` javascript
 collectionChanged(splices) {
    for (var i = 0; i < splices.length; i++) {
      var splice = splices[i];
  
      var valuesAdded = this.myCollection.slice(splice.index, splice.index + splice.addedCount);
      if (valuesAdded.length > 0) {
        console.log(`The following values were inserted at ${splice.index}: ${JSON.stringify(valuesAdded)}`);
      }
  
      if (splice.removed.length > 0) {
        console.log(`The following values were removed from ${splice.index}: ${JSON.stringify(splice.removed)}`);
      }
    }
  }
```
**Map Splice**
  

``` javascript
collectionChanged(splices) {
    for (var i = 0; i < splices.length; i++) {
      var splice = splices[i];
  
      if(splice.type == "add"){
        var valuesAdded = this.myCollection.get(splice.key);
        console.log(`'${valuesAdded}' was added to position ${splice.key}`);
      }
      
      if(splice.type == "update"){
        var newValue = splice.object.get(splice.key);
        console.log(`Position ${splice.key} changed from '${splice.oldValue}' to '${newValue}'`);
      }
  
      if(splice.type == "delete"){
        console.log(`'${splice.oldValue}' was deleted from position ${splice.key}`);
      }
    
    }
  }
```
**Set Splice**
  

``` javascript
 collectionChanged(splices) {
    for (var i = 0; i < splices.length; i++) {
      var splice = splices[i];
  
      if(splice.type == "add"){
        console.log(`'${splice.value}' was added to the set`);
      }
  
      if(splice.type == "delete"){
        console.log(`'${splice.value}' was removed from the set`);
      }
    }
}
```

>警告：如果在创建订阅之后要覆盖集合的值，将不再检测到更改。例如，运行这个。在`this.bindingEngine.collectionObserver(this.myCollection)`之后，`this.myCollection = []`将无法观察到`myCollection`的更改。
  
  
  