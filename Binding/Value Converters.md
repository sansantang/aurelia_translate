原文：https://aurelia.io/docs/binding/value-converters

Aurelia绑定引擎的值转换器功能概述。值转换器用于在数据绑定过程中向视图和从视图转换数据。

* [1\.Introduction 简介](#1introduction-%E7%AE%80%E4%BB%8B)
* [2\.Value Converters 值转换器](#2value-converters-%E5%80%BC%E8%BD%AC%E6%8D%A2%E5%99%A8)
* [3\.Simple Converters 简单的转换器](#3simple-converters-%E7%AE%80%E5%8D%95%E7%9A%84%E8%BD%AC%E6%8D%A2%E5%99%A8)
    * [Conventional Names 惯用名称](#conventional-names-%E6%83%AF%E7%94%A8%E5%90%8D%E7%A7%B0)
* [4\.Converter Parameters 转换器参数](#4converter-parameters-%E8%BD%AC%E6%8D%A2%E5%99%A8%E5%8F%82%E6%95%B0)
* [5\.Binding Converter Parameters 绑定转换器参数](#5binding-converter-parameters-%E7%BB%91%E5%AE%9A%E8%BD%AC%E6%8D%A2%E5%99%A8%E5%8F%82%E6%95%B0)
* [6\.Multiple Parameters / Composing Converters 多个参数/转换器组成](#6multiple-parameters--composing-converters-%E5%A4%9A%E4%B8%AA%E5%8F%82%E6%95%B0%E8%BD%AC%E6%8D%A2%E5%99%A8%E7%BB%84%E6%88%90)
* [7\.Object Parameters 对象参数](#7object-parameters-%E5%AF%B9%E8%B1%A1%E5%8F%82%E6%95%B0)
* [8\.Bi\-directional Value Converters 双向值转换器](#8bi-directional-value-converters-%E5%8F%8C%E5%90%91%E5%80%BC%E8%BD%AC%E6%8D%A2%E5%99%A8)
* [9\.Globally Accessible Value Converters 全局可访问的值转换器](#9globally-accessible-value-converters-%E5%85%A8%E5%B1%80%E5%8F%AF%E8%AE%BF%E9%97%AE%E7%9A%84%E5%80%BC%E8%BD%AC%E6%8D%A2%E5%99%A8)
* [10\.Signalable Value Converters 信号值转换器](#10signalable-value-converters-%E4%BF%A1%E5%8F%B7%E5%80%BC%E8%BD%AC%E6%8D%A2%E5%99%A8)

## 1.Introduction 简介
 
在Aurelia中，用户界面元素由视图和视图-模型对组成。视图是用HTML编写的，并呈现到DOM中。视图模型是用JavaScript编写的，它为视图提供数据和行为。Aurelia强大的数据绑定将这两部分连接在一起，允许在视图中反映数据的更改，反之亦然。

下面是一个使用**bind** (`.bind="expression"`) 和**interpolation** (`${expression}`) 技术的简单数据绑定示例：

**simple-binding.js**
```javascript
export class Person {
    name = 'Donald Draper';
  }
```

**simple-binding.html**
```html
  <template>
    <label for="name">Enter Name:</label>
    <input id="name" type="text" value.bind="name">
    <p>Name is ${name}</p>
  </template>
```

![](https://github.com/sansantang/aurelia_translate/blob/master/Binding/IMG/Value%20Converters/1.gif)


有时view-model 公开的原始数据的格式并不适合在UI中显示。呈现日期和数值是常见的场景：

**date-and-number.js**
```javascript
export class NetWorth {
    constructor() {
      this.update();
      setInterval(() => this.update(), 1000);
    }
  
    update() {
      this.currentDate = new Date();
      this.netWorth = Math.random() * 1000000000;
    }
  }
```

**date-and-number.html**
```html
 <template>
    ${currentDate} <br>
    ${netWorth}
  </template>
```

![](https://github.com/sansantang/aurelia_translate/blob/master/Binding/IMG/Value%20Converters/2.gif)


理想情况下，日期应该是一种更易读的格式，金额应该被格式化为货币。这个问题的一个解决方案是计算格式化的值并将它们作为 view-model 的属性公开。这当然是一个有效的方法；然而，在您的模型中定义额外的属性和方法可能会变得混乱，特别是当您需要在原始属性值更改时保持格式化值的同步时。幸运的是，Aurelia 有一个特性可以很容易地解决这个问题。


## 2.Value Converters 值转换器

> A value converter is a class whose responsibility is to convert view-model values into values that are appropriate to display in the view _and vice versa_.
> 
> 值转换器是一个类，其职责是将view-model 值转换为适合在视图中显示的值，反之亦然。

最常见的情况是，您将创建将模型数据转换为适合视图的格式的值转换器；然而，在某些情况下，您需要将数据从视图转换为视图模型期望的格式，特别是在使用与输入元素的双向绑定时。

如果您在其他语言 (如 Xaml) 中使用过值转换器，您会发现Aurelia值转换器非常类似，尽管有一些显著的改进：

1.  The Aurelia ValueConverter interface uses `toView` and `fromView` methods, which make it quite clear which direction the data is flowing. This is in contrast to Xaml's `IValueConverter`, which uses `Convert` and `ConvertBack`.

	Aurelia ValueConverter 接口使用 `toView` 和 `fromView` 方法，这使得数据流向非常清晰。这与 Xaml 的`IValueConverter`不同，后者使用`Convert`和`ConvertBack`。

2.  In Aurelia, converter parameters can be data-bound. This is something that was missing in Xaml and enables more advanced binding scenarios.

	在Aurelia中，转换器参数可以是数据绑定的。这是 Xaml 中所缺少的，并支持更高级的绑定场景。
	
3.  Aurelia value converter methods can accept multiple parameters.

	Aurelia 值转换方法可以接受多个参数。
	
4.  Multiple value converters can be composed using pipes (`|`).

	可以使用管道 (`|`) 组合多个值转换器。
	
5.  Aurelia value converter can have a class field named `signals`, which accepts an array of string that will be used to manually trigger updating the view. This is to handle the situations where the value converter relies on variables that are defined outside of Aurelia application, such as language, locale etc.

	Aurelia 值转换器可以有一个名为 `signals` 的类字段，它接受一个字符串数组，该数组将用于手动触发更新 view 。这是为了处理的情况下，值转换器依赖的变量定义以外的Aurelia应用程序，如语言，地区等。



## 3.Simple Converters 简单的转换器

在深入讨论细节之前，让我们重新处理前面的示例，使用几个基本的值转换器。Aurelia 和流行的 [Moment](http://momentjs.com/) 和 [Numeral](http://numeraljs.com/) 库会处理繁重的工作，我们只需要把东西连接起来……

**currency-format.js**
```javascript
 import numeral from 'numeral';
  
  export class CurrencyFormatValueConverter {
    toView(value) {
      return numeral(value).format('($0,0.00)');
    }
  }
```

**date-format.js**
```javascript
 import moment from 'moment';
  
  export class DateFormatValueConverter {
    toView(value) {
      return moment(value).format('M/D/YYYY h:mm:ss a');
    }
  }
```

**simple-converter.js**
```javascript
  export class NetWorth {
    constructor() {
      this.update();
      setInterval(() => this.update(), 1000);
    }
  
    update() {
      this.currentDate = new Date();
      this.netWorth = Math.random() * 1000000000;
    }
  }
```

**simple-converter.html**
```html
  <template>
    <require from="./date-format"></require>
    <require from="./currency-format"></require>
  
    ${currentDate | dateFormat} <br>
    ${netWorth | currencyFormat}
  </template>
```

![](https://github.com/sansantang/aurelia_translate/blob/master/Binding/IMG/Value%20Converters/3.gif)

好的，结果看起来好多了，但是这些是怎么工作的呢？

首先，我们创建了两个值转换器: `DateFormatValueConverter` and `CurrencyFormatValueConverter`.。每个视图都有一个`toView`方法，Aurelia 框架将在 view 中显示模型值之前将其应用于模型值。我们的转换器使用 **MomentJS** 和 **NumeralJS** 库来格式化数据。

接下来，我们更新了视图，`require` 使用转换器，以便在视图中使用它们。当需要诸如值转换器之类的资源时，您可以在 require 元素的 `from`  属性中提供到资源的路径。

**Requiring Resources**
```
  <require from="./date-format"></require>
  <require from="./currency-format"></require>
```

当Aurelia处理资源时，它检查类的元数据以确定资源类型(自定义元素、自定义属性、值转换器等)。元数据不是必需的，而且实际上我们的值转换器没有公开任何元数据。相反，我们依赖于Aurelia的一个简单约定: 以 _ValueConverter_  结尾的导出名称被假定为值转换器。**约定使用 export name 注册转换器，使用大小写混合的形式，从末尾去掉 _ValueConverter_ 部分。**

*   `DateFormatValueConverter` registers as `dateFormat`
*   `CurrencyFormatValueConverter` registers as `currencyFormat`

Finally, we applied the converter in the binding using the pipe `|` syntax:

最后，我们使用管道 `|` 语法在绑定中应用了转换器：

**Converter Syntax**
```
  ${currentDate | dateFormat} <br>
  ${netWorth | currencyFormat}
```

#### Conventional Names 惯用名称

>在 view 中引用资源的名称派生自其导出名称。对于值转换器和绑定行为，导出名称被转换为驼峰大小写(将其视为变量名)。对于自定义元素和自定义属性，导出名称使用小写和连字符(以符合HTML元素和属性规范)。

## 4.Converter Parameters 转换器参数

前一个示例中的转换器工作得很好，但是如果我们需要以多种格式显示日期和数字该怎么办呢？为我们需要显示的每种格式定义一个转换器是相当重复的。更好的方法是修改转换器以接受`format` 参数。然后我们就可以在绑定中指定格式，并最大限度地重用格式转换器。

**number-format.js**
```javascript
  import numeral from 'numeral';
  
  export class NumberFormatValueConverter {
    toView(value, format) {
      return numeral(value).format(format);
    }
  }
```

**date-format.js**
```javascript
  import moment from 'moment';
  
  export class DateFormatValueConverter {
    toView(value, format) {
      return moment(value).format(format);
    }
  }
```

**converter-parameters.js**
```javascript
  export class NetWorth {
    constructor() {
      this.update();
      setInterval(() => this.update(), 1000);
    }
  
    update() {
      this.currentDate = new Date();
      this.netWorth = Math.random() * 1000000000;
    }
  }
```

**converter-parameters.html**
```html
  <template>
    <require from="./date-format"></require>
    <require from="./number-format"></require>
  
    ${currentDate | dateFormat:'M/D/YYYY h:mm:ss a'} <br>
    ${currentDate | dateFormat:'MMMM Mo YYYY'} <br>
    ${currentDate | dateFormat:'h:mm:ss a'} <br>
    ${netWorth | numberFormat:'$0,0.00'} <br>
    ${netWorth | numberFormat:'$0.0a'} <br>
    ${netWorth | numberFormat:'0.00000)'}
  </template>
```
![](https://github.com/sansantang/aurelia_translate/blob/master/Binding/IMG/Value%20Converters/4.gif)

通过将 `format` 参数添加到 `toView` 方法中，我们可以使用 `[expression] | [converterName]:[parameterExpression]` 语法在绑定中指定格式：

**Converter Parameter Syntax**
```
  ${currentDate | dateFormat:'MMMM Mo YYYY'} <br>
  ${netWorth | numberFormat:'$0.0a'} <br>
```

## 5.Binding Converter Parameters 绑定转换器参数

转换器参数不必是文字值。您可以绑定参数值来实现动态结果：

**number-format.js**
```javascript
 import numeral from 'numeral';
  
  export class NumberFormatValueConverter {
    toView(value, format) {
      return numeral(value).format(format);
    }
  }
```

**binding-converter-parameters.js**
```javascript
export class NetWorth {
    constructor() {
      this.update();
      setInterval(() => this.update(), 1000);
    }
  
    update() {
      this.netWorth = Math.random() * 1000000000;
    }
  }
```

**binding-converter-parameters.html**
```html
  <template>
    <require from="./number-format"></require>
  
    <label for="formatSelect">Select Format:</label>
    <select id="formatSelect" ref="formatSelect">
      <option value="$0,0.00">$0,0.00</option>
      <option value="$0.0a">$0.0a</option>
      <option value="0.00000">0.00000</option>
    </select>
  
    ${netWorth | numberFormat:formatSelect.value}
  </template>
```

![](https://github.com/sansantang/aurelia_translate/blob/master/Binding/IMG/Value%20Converters/5.gif)

## 6.Multiple Parameters / Composing Converters 多个参数/转换器组成

值转换器可以接受多个参数，并且可以在同一个绑定表达式中组合多个转换器，这为重用提供了很大的灵活性和机会。

在下面的例子中，我们有一个视图模型，它暴露了一个Aurelia repos数组。视图使用 repeat 绑定在表中列出repos。`SortValueConverter`用于根据两个参数对数组进行排序:`propertyName` and `direction`。第二个转换器`TakeValueConverter`接受 `count` 参数，用于限制列出的存储库的数量：

**Multiple Parameters and Converters**
```html
  <template>
    <tr repeat.for="repo of repos | sort:column.value:direction.value | take:10">
      ...
    </tr>
  </template>
```

Here's the full example:

**sort.js**
```javascript
  export class SortValueConverter {
    toView(array, propertyName, direction) {
      let factor = direction === 'ascending' ? 1 : -1;
      return array.slice(0).sort((a, b) => {
        return (a[propertyName] - b[propertyName]) * factor;
      });
    }
  }
```

**take.js**
```javascript
 export class TakeValueConverter {
    toView(array, count) {
      return array.slice(0, count);
    }
  }
```

**multiple-parameters-and-converters.js**
```javascript
  import {HttpClient} from 'aurelia-http-client';
  
  export class AureliaRepositories {
    repos = [];
  
    activate() {
      return new HttpClient()
        .get('https://api.github.com/orgs/aurelia/repos')
        .then(response => this.repos = response.content);
    }
  }
```

**multiple-parameters-and-converters.html**
```html
<template>
    <require from="./sort"></require>
    <require from="./take"></require>
  
    <label for="column">Sort By:</label>
    <select id="column" ref="column">
      <option value="stargazers_count">Stars</option>
      <option value="forks_count">Forks</option>
      <option value="open_issues">Issues</option>
    </select>
  
    <select ref="direction">
      <option value="descending">Descending</option>
      <option value="ascending">Ascending</option>
    </select>
  
    <table class="table table-striped">
      <thead>
        <tr>
          <th>Name</th>
          <th>Stars</th>
          <th>Forks</th>
          <th>Issues</th>
        </tr>
      </thead>
      <tbody>
        <tr repeat.for="repo of repos | sort:column.value:direction.value | take:10">
          <td>${repo.name}</td>
          <td>${repo.stargazers_count}</td>
          <td>${repo.forks_count}</td>
          <td>${repo.open_issues}</td>
        </tr>
      </tbody>
    </table>
  </template>
```
![](https://github.com/sansantang/aurelia_translate/blob/master/Binding/IMG/Value%20Converters/6.gif)

## 7.Object Parameters 对象参数

Aurelia 支持对象转换器参数。使用单个`config`参数实现 `SortValueConverter` 的另一种实现如下所示：

**sort.js**
```javascript
 export class SortValueConverter {
    toView(array, config) {
      let factor = (config.direction || 'ascending') === 'ascending' ? 1 : -1;
      return array.sort((a, b) => {
        return (a[config.propertyName] - b[config.propertyName]) * factor;
      });
    }
  }
```

**object-parameters.js**
```javascript
  import {HttpClient} from 'aurelia-http-client';
  
  export class AureliaRepositories {
    repos = [];
  
    activate() {
      return new HttpClient()
        .get('https://api.github.com/orgs/aurelia/repos')
        .then(response => this.repos = response.content);
    }
  }
```

**object-parameters.html**
```html
  <template>
    <require from="./sort"></require>
  
    <div class="row">
      <div class="col-sm-3"
            repeat.for="repo of repos | sort: { propertyName: 'open_issues', direction: 'descending' }">
        <a href="${repo.html_url}/issues" target="_blank">
          ${repo.name} (${repo.open_issues})
        </a>
      </div>
    </div>
  </template>
```

![](https://github.com/sansantang/aurelia_translate/blob/master/Binding/IMG/Value%20Converters/7.gif)

这种方法有两个优点：您不需要记住转换器参数参数的顺序，任何阅读标记的人都可以很容易地知道每个转换器参数代表什么。

## 8.Bi-directional Value Converters 双向值转换器

So far we've been using converters with to-view bindings. The data flows in a single direction, from the model to the view. When using a converter in an input element's `value` binding, we need a way to convert the user's data entry to the format expected by the view-model. This is where the value converter's `fromView` method comes into play, taking the element's value and converting it to the format expected by the view-model.

到目前为止，我们一直在使用带有to-view绑定的转换器。数据以单一方向流动，从模型到视图。在输入元素的`value` 绑定中使用转换器时，我们需要一种方法将用户的数据条目转换为视图模型所期望的格式。这就是值转换器的`fromView`方法发挥作用的地方，它获取元素的值并将其转换为视图模型所期望的格式。

In the example below, we have a view-model that exposes colors in an object format, with properties for the red, green and blue components. In the view, we want to bind this color object to an HTML5 color input. The color input expects hex format text, so we'll use an `RgbToHexValueConverter` to facilitate the binding.

在下面的示例中，我们有一个以对象格式公开颜色的视图模型，其中包含红色、绿色和蓝色组件的属性。在视图中，我们希望将这个color对象绑定到一个HTML5 color输入。颜色输入需要十六进制格式的文本，因此我们将使用`RgbToHexValueConverter`来方便绑定。

**rgb-to-hex.js**
```javascript
  export class RgbToHexValueConverter {
    toView(rgb) {
      return "#" + (
        (1 << 24) + (rgb.r << 16) + (rgb.g << 8) + rgb.b
      ).toString(16).slice(1);
    }
  
    fromView(hex) {
      let exp = /^#?([a-f\d]{2})([a-f\d]{2})([a-f\d]{2})$/i,
          result = exp.exec(hex);
      return {
        r: parseInt(result[1], 16),
        g: parseInt(result[2], 16),
        b: parseInt(result[3], 16)
      };
    }
  }
```

**bi-directional-value-converters.js**
```javascript
 export class Color {
    rgb = { r: 146, g: 39, b: 143 };
  }
```

**object-parameters.html**
```html
  <template>
    <require from="./rgb-to-hex"></require>
  
    <label for="color">Select Color:</label>
    <input id="color" type="color" value.bind="rgb | rgbToHex">
    <br> r: ${rgb.r}, g:${rgb.g}, b:${rgb.b}
  </template>
```

![](https://github.com/sansantang/aurelia_translate/blob/master/Binding/IMG/Value%20Converters/8.gif)

## 9.Globally Accessible Value Converters 全局可访问的值转换器

In all of our examples, we've been using the `require` element to import converters we need into our view. There's an easier way. If you have some commonly used value converters that you'd like to make globally available, use Aurelia's `globalResources` function to register them. This will eliminate the need for `require` elements at the top of every view.

在所有的示例中，我们都使用了 `require`元素来将需要的转换器导入视图。有一个更简单的方法。如果您有一些常用的值转换器，希望使其在全球可用，使用Aurelia的`globalResources`功能来注册它们。这将消除每个视图顶部对 `require`元素的需求。

## 10.Signalable Value Converters 信号值转换器

In some scenarios, a global parameter that is unobservable by Aurelia is used inside a value converter, such as timezone, or a new USB was connected to the device etc. In some other scenarios, we need to update all bindings that use a certain value converter at once, for example: language translation value converters when application language has changed. Aurelia value converters have an API to trigger the bindings, with value converters that have signals property declared on it, to update.

在某些情况下，一个全局参数是不可观察的 Aurelia 是使用在一个值转换器，如时区，或一个新的USB连接到设备等。在其他一些场景中，我们需要一次性更新使用某个值转换器的所有绑定，

例如：应用程序语言更改时的语言转换值转换器。Aurelia值转换器有一个API来触发绑定，值转换器上声明了signals属性来更新绑定。

In the example below, we have a view-model that exposes a list of flights with information of each flight. In the view, we want to bind display each of those flights, as a `clock`, with correct date format based on global variable name `currentLocale`. We can trigger all of the flight display to change based on `signals` of the value converter. We do this via export `signalBindings` of the framework.

在下面的示例中，我们有一个视图模型，它公开一个包含每个航班信息的航班列表。在视图中，我们希望将这些航班以时钟 `clock`的形式显示，并根据全局变量名`currentLocale`使用正确的日期格式。我们可以根据数值转换器的信号触发所有的飞行显示改变。我们通过导出框架的信号绑定（`signalBindings`） 来实现这一点。

**How To Signal Bindings**
```javascript
  import {signalBindings} from 'aurelia-framework';
  
  signalBindings('locale-changed');
```

Following is the example code

**flight-time-value-converter.js**
```javascript
  export class FlightTimeValueConverter {
    signals = ['locale-changed'];
  
    toView(date) {
      return date.toLocaleString(window.currentLocale);
    }
  }
```

**flight-dashboard.js**
```javascript
  export class FlightDashboard {
    constructor() {
      this.flights = [
        { from: 'Los Angeles', to: 'San Fran', depart: new Date('2017-10-09'), arrive: new Date('2017-10-10') },
        { from: 'Melbourne', to: 'Sydney', depart: new Date('2017-10-11'), arrive: new Date('2017-10-12') },
        { from: 'Hawaii', to: 'Crescent', depart: new Date('2017-10-13'), arrive: new Date('2017-10-14') }
      ];
    }
  }
```

**clock.html**
```html
  <template bindable='time'
    style='display: inline-block;
    width: 200px;
    padding: 4px 6px;
    border-radius: 10px;
    border: 2px solid #1e1e1e;
    font-size: 16px;
    text-align: center;'>
    <require from='./flight-time-value-converter'></require>
    ${time | flightTime}
  </template>
```

**flight-dashboard.html**
```html
<template>
    <require from="./clock.html"></require>
  
    <div>
      <h2>Flights</h2>
      <table repeat.for='flight of flights' style='margin-bottom: 15px'>
        <tr>
          <th>
          From ${flight.from}
          </th>
          <th style='width: 10px'></th>
          <th>
            To ${flight.to}
          </th>
        </tr>
        <tr>
          <td>
            <clock time.bind='flight.depart'></clock>
          </td>
          <td style='width: 10px'></td>
          <td>
            <clock time.bind='flight.arrive'></clock>
          </td>
        </tr>
      </table>
    </div>
  </template>
```

![](https://github.com/sansantang/aurelia_translate/blob/master/Binding/IMG/Value%20Converters/19.gif)


