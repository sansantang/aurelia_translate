原文：https://aurelia.io/docs/binding/radios

* [Introduction 简介](#introduction-%E7%AE%80%E4%BB%8B)
* [Numbers](#numbers)
* [Objects](#objects)
* [Objects with Matcher](#objects-with-matcher)
* [Booleans](#booleans)
* [Strings](#strings)
* 
## Introduction 简介

一组单选框输入是一种“单选择”接口。Aurelia支持将任何类型的属性双向绑定到一组单选框输入。下面的示例演示如何将数字、对象、字符串和布尔属性绑定到单选框输入集。在每个示例中都有一组常见的步骤：

1.  通过名`name`属性将单选框分组。具有相同name属性值的单选按钮位于相同的“单选按钮组”中;一次只能选择组中的一个单选按钮。
2.  使用`model`属性定义每个radio的值。
3.  双向绑定每个radio的`checked`属性到视图模型上的“selected item”属性。


## Numbers

让我们从一个使用数字“selected item”属性的示例开始。在本例中，将通过model属性为每个无线电输入分配一个数值。选择一个radio将导致它的模型值被分配给`selectedProductId`属性。

**app.js**
```javascript
  export class App {
    products = [
      { id: 0, name: 'Motherboard' },
      { id: 1, name: 'CPU' },
      { id: 2, name: 'Memory' },
    ];
  
    selectedProductId = null;
  }
```
**app.html**
```html
  <template>
    <form>
      <h4>Products</h4>
      <label repeat.for="product of products">
        <input type="radio" name="group1"
                model.bind="product.id" checked.bind="selectedProductId">
        ${product.id} - ${product.name}
      </label>
      <br>
      Selected product ID: ${selectedProductId}
    </form>
  </template>
```
![](https://github.com/sansantang/aurelia_translate/blob/master/Binding/IMG/Radios/1.gif)

## Objects

绑定系统支持绑定所有类型的单选框，包括对象。下面是一个将一组单选框绑定到`selectedProduct`对象属性的示例。

**app.js**
```javascript
  export class App {
    products = [
      { id: 0, name: 'Motherboard' },
      { id: 1, name: 'CPU' },
      { id: 2, name: 'Memory' },
    ];
  
    selectedProduct = null;
  }
```
**app.html**
```html
  <template>
    <form>
      <h4>Products</h4>
      <label repeat.for="product of products">
        <input type="radio" name="group2"
                model.bind="product" checked.bind="selectedProduct">
        ${product.id} - ${product.name}
      </label>
  
      Selected product: ${selectedProduct.id} - ${selectedProduct.name}
    </form>
  </template>
```
![](https://github.com/sansantang/aurelia_translate/blob/master/Binding/IMG/Radios/2.gif)

## Objects with Matcher

您可能会遇到这样的情况，即您的输入元素的模型绑定到的对象不具有对您所绑定的已检查属性中的任何对象的引用相等性。对象可能通过id匹配，但它们可能不是相同的对象实例。为了支持这个场景，您可以覆盖Aurelia的默认“`matcher`”，它是一个类似如下的等式比较函数:`(a, b) => a === b`。您可以替换您所选择的具有正确逻辑来比较对象的函数。

**app.js**
```javascript
export class App {
    selectedProduct = { id: 1, name: 'CPU' };
  
    productMatcher = (a, b) => a.id === b.id;
  }
```
**app.html**
```html
  <template>
    <form>
      <h4>Products</h4>
      <label>
        <input type="radio" name="group3"
                model.bind="{ id: 0, name: 'Motherboard' }"
                matcher.bind="productMatcher"
                checked.bind="selectedProduct">
        Motherboard
      </label>
      <label>
        <input type="radio" name="group3"
                model.bind="{ id: 1, name: 'CPU' }"
                matcher.bind="productMatcher"
                checked.bind="selectedProduct">
        CPU
      </label>
      <label>
        <input type="radio" name="group3"
                model.bind="{ id: 2, name: 'Memory' }"
                matcher.bind="productMatcher"
                checked.bind="selectedProduct">
        Memory
      </label>
  
      Selected product: ${selectedProduct.id} - ${selectedProduct.name}
    </form>
  </template>
```
![](https://github.com/sansantang/aurelia_translate/blob/master/Binding/IMG/Radios/3.gif)

## Booleans

在本例中，为每个radio输入分配三个文字值之一:`null`, `true` 和 `false`。选择其中一个单选框将赋予`likesCake`属性的值。

**app.js**
```javascript
  export class App {
    likesCake = null;
  }
```
**app.html**
```html
  <template>
    <form>
      <h4>Do you like cake?</h4>
      <label>
        <input type="radio" name="group3"
                model.bind="null" checked.bind="likesCake">
        Don't Know
      </label>
      <label>
        <input type="radio" name="group3"
                model.bind="true" checked.bind="likesCake">
        Yes
      </label>
      <label>
        <input type="radio" name="group3"
                model.bind="false" checked.bind="likesCake">
        No
      </label>
  
      likesCake = ${likesCake}
    </form>
  </template>
```
![](https://github.com/sansantang/aurelia_translate/blob/master/Binding/IMG/Radios/4.gif)

## Strings

最后，这里是一个使用字符串的例子。这个例子是唯一的，因为它不使用 `model.bind` 来分配每个收音机的值。而是使用输入的标准`value` 属性。通常，我们不能将标准`value` 属性与检查绑定一起使用，因为它会强制赋值给字符串的任何内容。

**app.js**
```javascript
    

  export class App {
    products = ['Motherboard', 'CPU', 'Memory'];
    selectedProduct = null;
  }
```
**app.html**
```html
 <template>
    <form>
      <h4>Products</h4>
      <label repeat.for="product of products">
        <input type="radio" name="group4"
                value.bind="product" checked.bind="selectedProduct">
        ${product}
      </label>
      <br>
      Selected product: ${selectedProduct}
    </form>
  </template>
```
![](https://github.com/sansantang/aurelia_translate/blob/master/Binding/IMG/Radios/5.gif)