原文：https://aurelia.io/docs/binding/checkboxes

* [Introduction 简介](#introduction-%E7%AE%80%E4%BB%8B)
* [Booleans 布尔值](#booleans-%E5%B8%83%E5%B0%94%E5%80%BC)
* [Array of Numbers 数字数组](#array-of-numbers-%E6%95%B0%E5%AD%97%E6%95%B0%E7%BB%84)
* [Array of Objects 对象数组](#array-of-objects-%E5%AF%B9%E8%B1%A1%E6%95%B0%E7%BB%84)
* [Array of Objects with Matcher 与匹配对象的数组](#array-of-objects-with-matcher-%E4%B8%8E%E5%8C%B9%E9%85%8D%E5%AF%B9%E8%B1%A1%E7%9A%84%E6%95%B0%E7%BB%84)
* [Array of Strings 字符串数据](#array-of-strings-%E5%AD%97%E7%AC%A6%E4%B8%B2%E6%95%B0%E6%8D%AE)


<section>

## Introduction 简介

Aurelia支持将各种数据类型双向绑定到复选框输入元素。

</section>

<section>

## Booleans 布尔值


使用`checked.bind="myBooleanProperty"`将布尔属性绑定到输入元素的`checked`属性。

**app.js**
```javascript
 export class App {
    motherboard = false;
    cpu = false;
    memory = false;
  }
```
**app.html**
```html
 <template>
    <form>
      <h4>Products</h4>
      <label><input type="checkbox" checked.bind="motherboard">  Motherboard</label>
      <label><input type="checkbox" checked.bind="cpu"> CPU</label>
      <label><input type="checkbox" checked.bind="memory"> Memory</label>
  
      motherboard = ${motherboard}<br>
      cpu = ${cpu}<br>
      memory = ${memory}<br>
    </form>
  </template>
```
![](https://github.com/sansantang/aurelia_translate/blob/master/Binding/IMG/Checkboxes/1.gif)
## Array of Numbers 数字数组

一组复选框元素是一个多重选择接口。如果您有一个充当“选定项”列表的数组，则可以将该数组绑定到每个输入的 `checked`属性。绑定系统将跟踪输入的检查状态，在检查输入时将输入的值添加到数组中，在检查输入时将输入的值从数组中删除。

要定义输入的“value”，绑定输入的`model`属性:`model.bind="product.id"`。


**app.js**
```javascript
export class App {
    products = [
      { id: 0, name: 'Motherboard' },
      { id: 1, name: 'CPU' },
      { id: 2, name: 'Memory' },
    ];
  
    selectedProductIds = [];
  }
```
**app.html**
```html
  <template>
    <form>
      <h4>Products</h4>
      <label repeat.for="product of products">
        <input type="checkbox" model.bind="product.id" checked.bind="selectedProductIds">
        ${product.id} - ${product.name}
      </label>
      <br>
      Selected product IDs: ${selectedProductIds}
    </form>
  </template>
```
![](https://github.com/sansantang/aurelia_translate/blob/master/Binding/IMG/Checkboxes/2.gif)

## Array of Objects 对象数组

数字不是“选择项”数组中惟一可以存储的值类型。绑定系统支持所有类型，包括对象。下面是一个使用复选框数据绑定从 `selectedProducts` 数组中添加和删除“product”对象的示例。

**app.js**
```javascript
  export class App {
    products = [
      { id: 0, name: 'Motherboard' },
      { id: 1, name: 'CPU' },
      { id: 2, name: 'Memory' },
    ];
  
    selectedProducts = [];
  }
```
**app.html**
```html
 <template>
    <form>
      <h4>Products</h4>
      <label repeat.for="product of products">
        <input type="checkbox" model.bind="product" checked.bind="selectedProducts">
        ${product.id} - ${product.name}
      </label>
  
      Selected products:
      <ul>
        <li repeat.for="product of selectedProducts">${product.id} - ${product.name}</li>
      </ul>
    </form>
  </template>
```
![](https://github.com/sansantang/aurelia_translate/blob/master/Binding/IMG/Checkboxes/3.gif)

## Array of Objects with Matcher 与匹配对象的数组

您可能会遇到这样的情况，即您的输入元素的模型所绑定的对象不具有对已检查数组中任何对象的引用相等性。对象可能通过id匹配，但它们可能不是相同的对象实例。为了支持这个场景，您可以覆盖Aurelia的默认“matcher”，它是一个类似如下的等式比较函数:`(a, b) => a === b`。您可以替换您所选择的具有正确逻辑来比较对象的函数。

**app.js**
```javascript
 export class App {
    selectedProducts = [
      { id: 1, name: 'CPU' },
      { id: 2, name: 'Memory' }
    ];
  
    productMatcher = (a, b) => a.id === b.id;
  }
```
**app.html**
```html
  <template>
    <form>
      <h4>Products</h4>
      <label>
        <input type="checkbox" model.bind="{ id: 0, name: 'Motherboard' }"
                matcher.bind="productMatcher"
                checked.bind="selectedProducts">
        Motherboard
      </label>
      <label>
        <input type="checkbox" model.bind="{ id: 1, name: 'CPU' }"
                matcher.bind="productMatcher"
                checked.bind="selectedProducts">
        CPU
      </label>
      <label>
        <input type="checkbox" model.bind="{ id: 2, name: 'Memory' }"
                matcher.bind="productMatcher"
                checked.bind="selectedProducts">
        Memory
      </label>
  
      Selected products:
      <ul>
        <li repeat.for="product of selectedProducts">${product.id} - ${product.name}</li>
      </ul>
    </form>
  </template>
```
![](https://github.com/sansantang/aurelia_translate/blob/master/Binding/IMG/Checkboxes/4.gif)
## Array of Strings 字符串数据

最后，下面是一个使用复选框数据绑定从`selectedProducts`数组中添加和删除字符串的示例。这个例子是唯一的，因为它不使用`model.bind`来分配每个复选框的值。而是使用输入的标准 `value`属性。通常，我们不能将标准 `value`属性与检查绑定一起使用，因为它会强制赋值给字符串的任何内容。这个例子使用了一个字符串数组，所以一切正常。

**app.js**
```javascript
 export class App {
    products = ['Motherboard', 'CPU', 'Memory'];
    selectedProducts = [];
  }
```
**app.html**
```html
  <template>
    <form>
      <h4>Products</h4>
      <label repeat.for="product of products">
        <input type="checkbox" value.bind="product" checked.bind="selectedProducts">
        ${product}
      </label>
      <br>
      Selected products: ${selectedProducts}
    </form>
  </template>
```
![](https://github.com/sansantang/aurelia_translate/blob/master/Binding/IMG/Checkboxes/5.gif)
</section>