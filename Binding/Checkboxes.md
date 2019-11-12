原文：https://aurelia.io/docs/binding/checkboxes

* [Class](#class)
* [Style](#style)


<section>

## Introduction

Aurelia supports two-way binding a variety of data-types to checkbox input elements.

</section>

<section>

## Booleans

Bind a boolean property to an input element's `checked` attribute using `checked.bind="myBooleanProperty"`.

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
## Array of Numbers

A set of checkbox elements is a multiple selection interface. If you have an array that serves as the "selected items" list, you can bind the array to each input's `checked` attribute. The binding system will track the input's checked status, adding the input's value to the array when the input is checked and removing the input's value from the array when the input is unchecked.

To define the input's "value", bind the input's `model` attribute: `model.bind="product.id"`.
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
## Array of Objects

Numbers aren't the only type of value you can store in a "selected items" array. The binding system supports all types, including objects. Here's an example that adds and removes "product" objects from a `selectedProducts` array using the checkbox data-binding.
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
## Array of Objects with Matcher

You may run into situations where the object your input element's model is bound to does not have reference equality to any of the objects in your checked array. The objects might match by id, but they may not be the same object instance. To support this scenario you can override Aurelia's default "matcher" which is a equality comparison function that looks like this: `(a, b) => a === b`. You can substitute a function of your choosing that has the right logic to compare your objects.
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
## Array of Strings

Finally, here's an example that adds and removes strings from a `selectedProducts` array using the checkbox data-binding. This is example is unique because it does not use `model.bind` to assign each checkbox's value. Instead the input's standard `value` attribute is used. Normally we cannot use the standard `value` attribute in conjunction with checked binding because it coerces anything it's assigned to a string. This example uses an array of strings so everything works just fine.
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