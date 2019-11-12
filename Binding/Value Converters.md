原文：https://aurelia.io/docs/binding/class-and-style

* [Class](#class)
* [Style](#style)


## Class

可以使用字符串插值或`.bind`/`.to-view`绑定元素的`class`属性。

**Class Binding**
```
  <template>
    <div class="foo ${isActive ? 'active' : ''} bar"></div>
    <div class.bind="isActive ? 'active' : ''"></div>
    <div class.one-time="isActive ? 'active' : ''"></div>
  </template>
```
<section>

为了确保与其他JavaScript库的最大互操作性，绑定系统将只添加或删除绑定表达式中指定的类。这确保了由其他代码添加的类(如通过`classList.add(...)`)被保留。这种“默认安全”行为的代价很小，但在基准测试或其他性能关键的情况下(比如带有大量元素的repeat)，这种行为是显而易见的。通过使用`class-name.bind="...."` 或`class-name.one-time="..."`直接绑定到元素的[className](https://developer.mozilla.org/en-US/docs/Web/API/Element/className)属性，您可以选择退出默认行为。这将稍微快一些，但是会增加很多绑定。

</section>

<section>

## Style

可以将css字符串或对象绑定到元素的`style`属性。在视图中执行字符串插值时使用 `css`自定义属性，以确保应用程序与Internet Explorer和Edge兼容。如果 `css`中你不使用插值 - 它不会得到处理，所以如果你只是使用 inline 样式-使用适当的 HTMLElement 样式属性。

**Style Binding Data**
```javascript
  export class StyleData {
    constructor() {
      this.styleString = 'color: red; background-color: blue';
  
      this.styleObject = {
        color: 'red',
        'background-color': 'blue'
      };
    }
  }
```
**Style Binding View**
```html
  <template>
    <div style.bind="styleString"></div>
    <div style.bind="styleObject"></div>
  </template>
```

**Illegal Style Interpolation 非法语体插值**
```html
  <template>
    <div style="width: ${width}px; height: ${height}px;"></div>
  </template>
```

**Legal Style Interpolation 合法语体插值**
```html
  <template>
    <div css="width: ${width}px; height: ${height}px;"></div>
  </template>  
```
**Won't Work Without Interpolation 没有插值就无法工作**
```html
  <template>
    <div css="width: 100px; height: 100px;"></div>
  </template>
```
</section>