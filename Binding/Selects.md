原文：https://aurelia.io/docs/binding/selects

* [Introduction](#introduction)
* [Select Number](#select-number)
* [Select Object](#select-object)
* [Select Object with Matcher](#select-object-with-matcher)
* [Select Boolean](#select-boolean)
* [Select String](#select-string)
* [Multiple Select Numbers](#multiple-select-numbers)
* [Multiple Select Objects](#multiple-select-objects)

## Introduction


根据是否存在`multiple`属性，`<select>`元素可以作为单选或多选“选择器”。绑定系统支持这两个用例。下面的示例演示了各种场景，都使用了一系列常见的步骤来配置select元素:

1.  向模板中添加一个`<select>`元素，并决定是否应该应用`multiple`属性
2.  将select元素的`value`属性绑定到属性。在"`multiple`"模式下，属性应该是一个数组。在奇异（singular mode）模式下，它可以是任何类型。
3.  定义select元素的`<option>`元素。您可以使用`repeat`或手动添加每个选项元素。
4.  _通过`model`属性指定每个选项的值：`<option model.bind="product.id"></option>`您可以使用标准`value`属性而不是`model`，只记得-它会强制分配给字符串的任何内容。


## Select Number

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
    <label>
      Select product:<br>
      <select value.bind="selectedProductId">
        <option model.bind="null">Choose...</option>
        <option repeat.for="product of products"
                model.bind="product.id">
          ${product.id} - ${product.name}
        </option>
      </select>
    </label>
    Selected product ID: ${selectedProductId}
  </template>
```

![](https://github.com/sansantang/aurelia_translate/blob/master/Binding/IMG/Selects/1.gif)

## Select Object

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
    <label>
      Select product:<br>
      <select value.bind="selectedProduct">
        <option model.bind="null">Choose...</option>
        <option repeat.for="product of products"
                model.bind="product">
          ${product.id} - ${product.name}
        </option>
      </select>
    </label>
  
    Selected product: ${selectedProduct.id} - ${selectedProduct.name}
  </template>
```

![](https://github.com/sansantang/aurelia_translate/blob/master/Binding/IMG/Selects/2.gif)

## Select Object with Matcher

您可能会遇到这样的情况，即所绑定的select元素的值与所绑定的option元素模型属性的任何对象都没有引用相等性。select的值对象可能通过id“匹配”某个选项对象，但它们可能不是相同的对象实例。为了支持这个场景，您可以覆盖Aurelia的默认“`matcher`”，它是一个类似如下的等式比较函数:`(a, b) => a === b`。您可以替换您所选择的具有正确逻辑来比较对象的函数。

**app.js**
```javascript
  export class App {
    products = [
      { id: 0, name: 'Motherboard' },
      { id: 1, name: 'CPU' },
      { id: 2, name: 'Memory' },
    ];
  
    productMatcher = (a, b) => a.id === b.id;
  
    selectedProduct = { id: 1, name: 'CPU' };
  }
```
**app.html**
```html
  <template>
    <label>
      Select product:<br>
      <select value.bind="selectedProduct" matcher.bind="productMatcher">
        <option model.bind="null">Choose...</option>
        <option repeat.for="product of products"
                model.bind="product">
          ${product.id} - ${product.name}
        </option>
      </select>
    </label>
  
    Selected product: ${selectedProduct.id} - ${selectedProduct.name}
  </template>
```

![](https://github.com/sansantang/aurelia_translate/blob/master/Binding/IMG/Selects/3.gif)

## Select Boolean

**app.js**
```javascript
  export class App {
    likesTacos = null;
  }
```
**app.html**
```html
  <template>
    <label>
      Do you like tacos?:
      <select value.bind="likesTacos">
        <option model.bind="null">Choose...</option>
        <option model.bind="true">Yes</option>
        <option model.bind="false">No</option>
      </select>
    </label>
    likesTacos: ${likesTacos}
  </template>
```


![](https://github.com/sansantang/aurelia_translate/blob/master/Binding/IMG/Selects/4.gif)

## Select String

**app.js**
```javascript
  export class App {
    products = ['Motherboard', 'CPU', 'Memory'];
    selectedProduct = '';
  }
```
**app.html**
```html
  <template>
    <label>
      Select product:<br>
      <select value.bind="selectedProduct">
        <option value="">Choose...</option>
        <option repeat.for="product of products"
                value.bind="product">
          ${product}
        </option>
      </select>
    </label>
    Selected product: ${selectedProduct}
  </template>
```


![](https://github.com/sansantang/aurelia_translate/blob/master/Binding/IMG/Selects/5.gif)


## Multiple Select Numbers

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
    <label>
      Select products:
      <select multiple value.bind="selectedProductIds">
        <option repeat.for="product of products"
                model.bind="product.id">
          ${product.id} - ${product.name}
        </option>
      </select>
    </label>
    Selected product IDs: ${selectedProductIds}
  </template>
```


![](https://github.com/sansantang/aurelia_translate/blob/master/Binding/IMG/Selects/6.gif)


## Multiple Select Objects

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
    <label>
      Select products:
      <select multiple value.bind="selectedProducts">
        <option repeat.for="product of products"
                model.bind="product">
          ${product.id} - ${product.name}
        </option>
      </select>
    </label>
  
    Selected products:
    <ul>
      <li repeat.for="product of selectedProducts">${product.id} - ${product.name}</li>
    </ul>
  </template>
```

![](https://github.com/sansantang/aurelia_translate/blob/master/Binding/IMG/Selects/7.gif)