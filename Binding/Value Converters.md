原文：https://aurelia.io/docs/binding/value-converters

Aurelia绑定引擎的值转换器功能概述。值转换器用于在数据绑定过程中向视图和从视图转换数据。


## Introduction

在Aurelia中，用户界面元素由视图和视图-模型对组成。视图是用HTML编写的，并呈现到DOM中。视图模型是用JavaScript编写的，它为视图提供数据和行为。Aurelia强大的数据绑定将这两部分连接在一起，允许在视图中反映数据的更改，反之亦然。

Here's a simple data-binding example using the **bind** (`.bind="expression"`) and **interpolation** (`${expression}`) techniques:

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

Sometimes the raw data exposed by your view-model isn't in a format that's ideal for displaying in the UI. Rendering date and numeric values are common scenarios:

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

Ideally, the date would be in a more readable format and the amount would be formatted as currency. One solution to this problem would be to compute the formatted values and expose them as properties of the view-model. This is certainly a valid approach; however, defining extra properties and methods in your models can get messy, especially when you need to keep the formatted values in sync when the original property value change. Fortunately, Aurelia has a feature that makes solving this problem quite easy.


## Value Converters

> A value converter is a class whose responsibility is to convert view-model values into values that are appropriate to display in the view _and vice versa_.

Most commonly you'll be creating value converters that translate model data to a format suitable for the view; however, there are situations where you'll need to convert data from the view to a format expected by the view-model, typically when using two-way binding with input elements.

If you've used value converters in other languages such as Xaml, you'll find Aurelia value converters are quite similar, although with a few notable improvements:

1.  The Aurelia ValueConverter interface uses `toView` and `fromView` methods, which make it quite clear which direction the data is flowing. This is in contrast to Xaml's `IValueConverter`, which uses `Convert` and `ConvertBack`.
2.  In Aurelia, converter parameters can be data-bound. This is something that was missing in Xaml and enables more advanced binding scenarios.
3.  Aurelia value converter methods can accept multiple parameters.
4.  Multiple value converters can be composed using pipes (`|`).
5.  Aurelia value converter can have a class field named `signals`, which accepts an array of string that will be used to manually trigger updating the view. This is to handle the situations where the value converter relies on variables that are defined outside of Aurelia application, such as language, locale etc.



## Simple Converters

Before we get too far into the details, let's rework the previous example to use a couple of basic value converters. Aurelia and the popular [Moment](http://momentjs.com/) and [Numeral](http://numeraljs.com/) libraries will take care of the heavy lifting, we just need to wire things up...

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

OK, the result looks much better, but how did this all work?

Well, first we created a couple of value converters: `DateFormatValueConverter` and `CurrencyFormatValueConverter`. Each has a `toView` method that the Aurelia framework will apply to model values before displaying them in the view. Our converters use the MomentJS and NumeralJS libraries to format the data.

Next, we updated the view to `require` the converters so they can be used in the view. When requiring a resource such as a value converter, you supply the path to the resource in the require element's `from` attribute.

**Requiring Resources**
```
  <require from="./date-format"></require>
  <require from="./currency-format"></require>
```

When Aurelia processes the resource, it examines the class's metadata to determine the resource type (custom element, custom attribute, value converter, etc). Metadata isn't required, and in fact our value converters didn't expose any. Instead, we relied on one of Aurelia's simple conventions: export names ending with _ValueConverter_ are assumed to be value converters. **The convention registers the converter using the export name, camel-cased, with the _ValueConverter_ portion stripped from the end.**

*   `DateFormatValueConverter` registers as `dateFormat`
*   `CurrencyFormatValueConverter` registers as `currencyFormat`

Finally, we applied the converter in the binding using the pipe `|` syntax:

**Converter Syntax**
```
  ${currentDate | dateFormat} <br>
  ${netWorth | currencyFormat}
```

#### Conventional Names

>The name that a resource is referenced by in a view derives from its export name. For Value Converters and Binding Behaviors, the export name is converted to camel case (think of it as a variable name). For Custom Elements and Custom Attributes the export name is lower-cased and hyphenated (to comply with HTML element and attribute specifications).

## Converter Parameters

The converters in the previous example worked great, but what if we needed to display dates and numbers in multiple formats? It would be quite repetitive to define a converter for each format we needed to display. A better approach would be to modify the converters to accept a `format` parameter. Then we'd be able to specify the format in the binding and get maximum reuse out of our format converters.

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

With the `format` parameter added to the `toView` methods, we are able to specify the format in the binding using the `[expression] | [converterName]:[parameterExpression]` syntax:

**Converter Parameter Syntax**
```
  ${currentDate | dateFormat:'MMMM Mo YYYY'} <br>
  ${netWorth | numberFormat:'$0.0a'} <br>
```

## Binding Converter Parameters

Converter parameters needn't be literal values. You can bind parameter values to achieve dynamic results:

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

## Multiple Parameters / Composing Converters

Value converters can accept multiple parameters and multiple converters can be composed in the same binding expression, providing a lot of flexibility and opportunity for reuse.

In the following example, we have a view-model exposing an array of Aurelia repos. The view uses a repeat binding to list the repos in a table. A `SortValueConverter` is used to sort the array based on two arguments: `propertyName` and `direction`. A second converter, `TakeValueConverter` accepting a `count` argument is applied to limit the number of repositories listed:

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

## Object Parameters

Aurelia supports object converter parameters. An alternate implementation of the `SortValueConverter` using a single `config` parameter would look like this:

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

There are a couple of advantages to this approach: you don't need to remember the order of the converter parameter arguments, and anyone reading the markup can easily tell what each converter parameter represents.


## Bi-directional Value Converters

So far we've been using converters with to-view bindings. The data flows in a single direction, from the model to the view. When using a converter in an input element's `value` binding, we need a way to convert the user's data entry to the format expected by the view-model. This is where the value converter's `fromView` method comes into play, taking the element's value and converting it to the format expected by the view-model.

In the example below, we have a view-model that exposes colors in an object format, with properties for the red, green and blue components. In the view, we want to bind this color object to an HTML5 color input. The color input expects hex format text, so we'll use an `RgbToHexValueConverter` to facilitate the binding.

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

## Globally Accessible Value Converters

In all of our examples, we've been using the `require` element to import converters we need into our view. There's an easier way. If you have some commonly used value converters that you'd like to make globally available, use Aurelia's `globalResources` function to register them. This will eliminate the need for `require` elements at the top of every view.


## Signalable Value Converters

In some scenarios, a global parameter that is unobservable by Aurelia is used inside a value converter, such as timezone, or a new USB was connected to the device etc. In some other scenarios, we need to update all bindings that use a certain value converter at once, for example: language translation value converters when application language has changed. Aurelia value converters have an API to trigger the bindings, with value converters that have signals property declared on it, to update.

In the example below, we have a view-model that exposes a list of flights with information of each flight. In the view, we want to bind display each of those flights, as a `clock`, with correct date format based on global variable name `currentLocale`. We can trigger all of the flight display to change based on `signals` of the value converter. We do this via export `signalBindings` of the framework.

</section>

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


