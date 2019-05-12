与Aurelia数据绑定的基础知识。

##  介绍

本文介绍了使用Aurelia进行数据绑定的基础知识。您将学习如何绑定到HTML属性、DOM事件和元素内容。您还将看到如何将视图模型引用给DOM元素，从而使直接使用元素变得很容易。

## HTML和SVG属性

Aurelia支持将HTML和SVG属性绑定到JavaScript表达式。属性绑定声明有三个部分:`attribute.command="expression"`。

*   `attribute`: HTML或SVG属性名。
*   `command`: Aurelia的属性绑定命令之一:
    *   `one-time`: 单向流动数据:从`view-model到view`，**一次性**.
    *   `to-view` / `one-way`:单向流动数据: 从`view-model到view`.
    *   `from-view`:单向流动数据: 从`view到view-model`.
    *   `two-way`: 双向流动数据: from view-model to view and from view to view-model.
    *   `bind`: 自动选择绑定模式。对表单控件使用双向绑定，对几乎所有其他内容使用`to-view`绑定。
*   `expression`: a JavaScript expression.

通常，您将使用`bind`命令，因为它在大多数情况下执行您想要的操作。考虑在性能关键的情况下使用一次性`one-time`方法，在这种情况下，数据永远不会更改，因为它跳过了观察视图模型进行更改的开销。下面是一些例子。

**HTML Attribute Binding Examples**

``` html
<input type="text" value.bind="firstName">
  <input type="text" value.two-way="lastName">
  <input type="text" value.from-view="middleName">
  
  <a class="external-link" href.bind="profile.blogUrl">Blog</a>
  <a class="external-link" href.to-view="profile.twitterUrl">Twitter</a>
  <a class="external-link" href.one-time="profile.linkedInUrl">LinkedIn</a>
```


第一个输入使用`bind`命令，该命令将自动为输入值属性绑定创建双向绑定`two-way`。第二个和第三个输入`two-way` / `from-view`（双向/从视图）命令，这些命令明确设置绑定模式。对于第一个和第二个输入，只要绑定视图模型的 `firstName` / `lastName`属性被更新，它们的值就会被更新，这些属性也会随着输入的改变而更新。对于第三个输入，绑定视图模型`middleName`属性中的更改不会更新输入值，但是，输入中的更改会更新视图模型。第一个锚元素使用`bind`命令，该命令将自动为锚href属性创建一个`to-view`绑定。另外两个锚元素使用`to-view`和`one-time`(一次性)命令显式地设置绑定的模式。


##  DOM 事件

绑定系统支持绑定到DOM事件。当指定的DOM事件发生时，DOM事件绑定将执行JavaScript表达式。事件绑定声明有三个部分: `event.command="expression"`。

*   `event`: DOM事件的名称，没有“on”前缀。
*   `command`: Aurelia的属性绑定命令之一:
    *   `trigger`: 将事件处理程序直接附加到元素。当事件触发时，将调用表达式。
    *   `delegate`:  将单个事件处理程序附加到文档(或最近的影子DOM边界)，该文档在**冒泡bubbling**阶段处理指定类型的所有事件，并将它们正确地分派回原始目标，以便调用关联的表达式。
    *   `capture`:将单个事件处理程序附加到文档(或最近的影子DOM边界)，该文档在**捕获capturing**阶段处理指定类型的所有事件，并将它们正确地分派回原始目标，以便调用关联的表达式。
*   `expression`:一个JavaScript表达式。使用特殊的`$event`属性访问绑定表达式中的DOM事件。

下面是几个例子。

**DOM Event Binding Examples**

``` HTML
  <button type="button" click.trigger="cancel()">Cancel</button>
  
  <button type="button" click.delegate="select('yes')">Yes</button>
  <button type="button" click.delegate="select('no')">No</button>
  
  <input type="text" blur.trigger="elementBlurred($event.target)">
  <input type="text" change.delegate="lastName = $event.target.value">
```


cancel按钮使用`trigger`命令将事件侦听器附加到button元素，该元素将调用视图模型的cancel方法。yes和no按钮使用委托`delegate`命令，该命令将使用事件委托模式。输入元素具有使用特殊`$event`属性访问[DOM event](https://developer.mozilla.org/en-US/docs/Web/API/Event) 的绑定表达式。

Aurelia将在使用`delegate` or `trigger`（委托或触发器 ）绑定处理的事件上自动调用[preventDefault()](https://developer.mozilla.org/en-US/docs/Web/API/Event/preventDefault)。大多数时候这是你想要的行为。要关闭此选项，请从事件处理函数返回`true`。


## 函数引用

在开发自定义元素或自定义属性时，您可能会遇到这样的情况:您有一个`@bindable`属性，它期望引用一个函数。使用`call` binding命令声明一个函数并将其传递给可绑定属性。对于这个用例，`call`命令优于`bind`命令，因为它将在正确的上下文中执行函数，确保`this` 这是您所期望的。

**Simple call binding-简单的调用绑定**

``` HTML
  <my-element go.call="doSomething()"></my-element>
  
  <input type="text" value.bind="taskName">
  <my-element go.call="doSomething(taskName)"></my-element>
```

  您的自定义元素或属性可以使用标准的调用语法调用传递给`@bindable`属性的函数:`this.go()`;。如果需要使用参数调用函数，请创建一个对象，该对象的键是参数名，值是参数值，然后使用这个“arguments对象”调用函数。对象的属性可用于`call`调用绑定表达式中的数据绑定。例如，用这个调用函数`this.go({ x: 5, y: -22, z: 11})`)将使`x`, `y`和`z`可用来绑定：

**Accessing the call argument properties-访问调用参数属性**


``` HTML
  <my-element execute.call="doSomething(x, y)"></my-element>
```

  ## Referencing Elements 引入元素

使用`ref`绑定命令创建对DOM元素的引用。ref命令最基本的语法是`ref="expression"`。当视图被数据绑定时，指定的表达式将被分配给DOM元素。

  **Simple ref example**
    
``` HTML
  <template>
    <input type="text" ref="nameInput"> ${nameInput.value}
  </template>
```


`ref`命令有几个限定符，您可以将它们与自定义元素和属性一起使用:

*   `element.ref="expression"`: 创建对DOM元素的引用 (same as `ref="expression"`).
*   `attribute-name.ref="expression"`: 创建对自定义属性的view-model的引用。
*   `view-model.ref="expression"`: 创建对自定义元素的view-model的引用。
*   `view.ref="expression"`: 创建对自定义元素view实例的引用(而不是HTML元素)。
*   `controller.ref="expression"`:创建对自定义元素的控制器实例的引用。



## String Interpolation 字符串插入

字符串插值表达式允许用文本插值表达式的结果。演示此功能的最佳方法是通过一个示例。下面是两个具有数据绑定textcontent的span元素：

**String interpolation example**

``` HTML
  <span textcontent.bind="'Hello' + firstName"></span>
  
  <span>Hello ${firstName}</span>
```

 第一个span使用`bind`命令。 第二个使用字符串插值。 内插版本更易于阅读和易于记忆，因为语法与ES2015 / ES6中标准化的[模板文字](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/template_strings)语法相匹配。
 
字符串内插可以在html属性中使用，作为`to-view`绑定的替代方法。默认情况下，插值绑定的模式是`to-view`，表达式的结果总是强制到字符串。`null` 或 `undefined`的结果将导致空字符串。

## 元素内容

前面的示例比较了字符串插值绑定和 `textcontent.bind`。插值更容易读，但当你需要双重绑定一个`contenteditable`元素，`textcontent.bind`可以派上用场，：

**textContent example**

``` HTML
  <div contenteditable textcontent.bind="firstName"></div>
  <div contenteditable textcontent.bind="lastName"></div>
```
  
您还可能需要将html文本绑定到元素的`innerHTML`属性：

**Binding innerHTML**

    

``` HTML
  <template>
    <div innerhtml.bind="htmlProperty | sanitizeHTML"></div>
    <div innerhtml="${htmlProperty | sanitizeHTML}"></div>
  </template>
```

 >重要：始终使用HTML清理。我们提供了一个简单的转换器作为占位符。但是，它不能提供针对各种复杂的XSS攻击的安全性，并且不应该依赖于对来自未知源的输入进行净化。您可以通过在启动时在应用程序中注册自己的[HTMLSanitizer](https://github.com/aurelia/templating-resources/blob/master/src/html-sanitizer.js)实现来替换内置的杀毒软件。例如`aurelia.use.singleton(HTMLSanitizer, BetterHTMLSanitizer);`我们建议使用DOMPurify或sanitize-html这样的库来实现。

>提示：使用`innerhtml`属性绑定只需要设置元素的`innerHTML`属性。标记不会通过Aurelia的模板系统。不计算 Binding expressions 和 require 元素。


## Contextual Properties 上下文属性

绑定系统根据上下文为模板中的绑定提供了几个属性。

*   `$this` - The binding context (the view-model).
*   `$parent` - 显式地从组合或循环模板中访问外部范围。 当当前作用域上的属性屏蔽外部作用域上的属性时，可能需要这样做。 可链式 - 例如`$parent.$parent.foo`受支持。
*   `$event` - The DOM Event in `delegate` or `trigger` bindings.
*   `$index` - In a repeat（循环） template, 集合中项的索引.
*   `$first` - In a repeat template, is true if the item is the first item in the array.
*   `$last` - In a repeat template, is true if the item is the last item in the array.
*   `$even` - In a repeat template, is true if the item has an even numbered index.
*   `$odd` - In a repeat template, is true if the item has an odd numbered index.



## 表达式语法

Aurelia的表达式解析器实现 [ECMAScript Expressions](https://tc39.github.io/ecma262/#sec-ecmascript-language-expressions) 的子集。对于所支持的特性，通常可以期望视图中的JavaScript与视图模型或浏览器控制台中的JavaScript以相同的方式工作。此外，还有两个调整：

*   符号`&` 表示一个 `BindingBehavior` (而不是按位AND)
*   这条 `|` 表示一个 `ValueConverter` (而不是按位 OR)

不支持非表达式语法(语句、声明、函数和类定义)。

作为对各种可能的表达式的概述，下面的列表是为了说明而列出的，并不是详尽的(也不一定推荐使用)，但是应该让您对可以做什么有一个相当好的了解：

### 基本表达式

#### 标识符

*   `foo` - l当前view-model中的`foo`变量
*   `ßɑṙ` - 当前view-model中的`ßɑṙ`变量

>提示：non-ASCII characters in the [Latin](https://en.wikipedia.org/wiki/Latin_script_in_Unicode#Table_of_characters) script are supported. This script contains 1,350 characters covering the vast majority of languages. Other [Non-BMP characters / Surrogate Pairs](https://en.wikipedia.org/wiki/Plane_(Unicode)) are not supported.
>提示：支持拉丁[Latin](https://en.wikipedia.org/wiki/Latin_script_in_Unicode#Table_of_characters)脚本中的非ASCII字符。这个脚本包含1350个字符，涵盖了绝大多数语言。不支持其他 [Non-BMP characters / Surrogate Pairs](https://en.wikipedia.org/wiki/Plane_(Unicode))。

#### 在Aurelia中具有特殊含义的标识符

*   `$this` - The current view-model
*   `$parent` - The parent view-model

####  原始文字

*   `true` - The literal value `true`
*   `false` - The literal value `false`
*   `null` - The literal value `null`
*   `undefined` - The literal value `undefined`

#### 字符串文字和转义序列

*   `'foo'` or `"foo"` - The literal string `foo`
*   `'\n'` - The literal string `[NEWLINE]`
*   `'\t'` - The literal string `[TAB]`
*   `'\''` - The literal string `'`
*   `'\\'` - The literal string `\`
*   `'\\n'` - The literal string `\n`
*   `'\u0061'` - The literal string `a`

>提示：支持的字符串文本包括`'\x61'` (2-point hex escape), `'\u{61}'` or `'\u{000061}'` (n-point braced unicode escape), 以及非bmp字符和代理对。
  
#### 模板文字

*   ``foo`` - Equivalent to `'foo'`
*   ``foo${bar}baz${qux}quux`` - Equivalent to `'foo'+bar+'baz'+qux+'quux'`

#### 数字文字

*   `42` - The literal number `42`
*   `42.` or `42.0` - The literal number `42.0`
*   `.42` or `0.42` - The literal number `0.42`
*   `42.3` - The literal number `42.3`
*   `10e3` or `10E3` - The literal number `1000`

>提示：Unsupported numeric literals include `0b01` (binary integer literal), `0o07` (octal integer literal), and `0x0F` (hex integer literal).

#### 数组文字

*   `[]` - An empty array
*   `[1,2,3]` - An array containing the literal numbers `1`, `2` and `3`
*   `[foo, bar]` - An array containing the variables `foo` and `bar`
*   `[[]]` - An array containing an empty array

>Unsupported array literals include `[,]` - [Elision](https://tc39.github.io/ecma262/#prod-Elision)

#### 对象文字

*   `{}` - An empty object
*   `{foo}` or `{foo,bar}` - ES6 shorthand notation, equivalent to `{'foo':foo}` or `{'foo':foo,'bar':bar}`
*   `{42:42}` - Equivalent to `{'42':42}`

>Unsupported object literals include `{[foo]: bar}` or `{['foo']: bar}` (computed property names).

### 一元表达式

**`foo` here represents any valid primary expression or unary expression.**

*   `+foo` or `+1` - Equivalent to `foo` or `1` (the `+` unary operator is always ignored)
*   `-foo` or `-1` - Equivalent to `0-foo` or `0-1`
*   `!foo` - Negates `foo`
*   `typeof foo` - Returns the primitive type name of `foo`
*   `void foo` - Evaluates `foo` and returns `undefined`

>提示：Unary increment (`++foo` or `foo++`), decrement (`--foo` or `foo--`), bitwise (`~`), `delete`, `await` and `yield` operators are not supported.

### 二进制表达式（从最高到最低优先级）

**这里的`a`和`b`表示任何有效的主、一元或二进制表达式。**

*   `a*b` or `a/b` or `a%b` - 乘法型
*   `a+b` or `a-b` - 加法型
*   `a<b` or `a>b` or `a<=b` or `a>=b` or `a in b` or `a instanceof b` - 关系型
*   `a==b` or `a!=b` or `a===b` or `a!==b` - 相等型
*   `a&&b` - 逻辑性 AND
*   `a||b` - 逻辑性l OR

>不支持指数（`a**b`）和按位运算符。


### 条件表达式

**这里的foo等表示任何有效的主、一元、二进制或条件表达式。**

*   `foo ? bar : baz`
*   `foo ? bar : baz ? qux : quux`

### 赋值表达式

**这里的`foo`必须是一个可分配的表达式(一个简单的访问器、一个成员访问器或一个索引成员访问器)。bar可以是任何有效的主、一元、二进制、条件或赋值表达式。**

*   `foo = bar`
*   `foo = bar = baz`

### 成员和调用表达式

在Aurelia中具有特殊含义的成员表达式：

*   `$parent.foo` - Access the `foo` variable in the parent view-model
*   `$parent.$parent.foo` - Access the `foo` variable in the parent's parent view-model
*   `$this` - Access the current view-model (equivalent to simply `this` inside the view-model if it's an ES class)

一般成员与调用表达式：

**`foo`在这里表示任何有效的成员、调用、赋值、条件表达式、二进制、一元或主表达式(假设表达式作为一个整体也是有效的JavaScript)。**

*   `foo.bar` - Member accessor
*   `foo['bar']` - Keyed member accessor
*   `foo()` - Function call
*   `foo.bar()` - Member function call
*   `foo['bar']()` - Keyed member function call

标记的模板文字:

**这里的`foo`应该是一个可以调用的函数。模板的字符串部分作为数组传递给第一个参数，表达式部分作为连续参数传递。**

*   `foo`bar`` - Equivalent to `foo(['bar'])`
*   `foo`bar${baz}qux`` - Equivalent to `foo(['bar','qux'], baz)`
*   `foo`bar${baz}qux${quux}corge`` - Equivalent to `foo(['bar','qux','corge'],baz,quux)`
*   `foo`${bar}${baz}${qux}`` - Equivalent to `foo(['','','',''],bar,baz,qux)`

### 绑定行为和值转换器

它们不被认为是普通表达式的一部分，并且必须始终位于表达式的末尾(尽管可以链接多个表达式)。此外，绑定行为必须紧跟在ValueConverters之后。(注意:为了可读性，BindingBehavior和ValueConverter被缩写为BB和VC)

Valid BB expressions:

*   `foo & bar & baz` - Applies the BB `bar` to the variable `foo`, and then applies the BB `baz` to the result of that.
*   `foo & bar:'baz'` - Applies the BB `bar` to the variable `foo`, and passes the literal string `'baz'` as an argument to the BB
*   `foo & bar:baz:qux` - Applies the BB `bar` to the variable `foo`, and passes the variables `baz` and `qux` as arguments to the BB
*   `'foo' & bar` - Applies the BB `bar` to the literal string `'foo'`

Valid VC expressions (likewise):

*   `foo | bar | baz`
*   `foo | bar:'baz'`
*   `foo | bar:baz:qux`
*   `'foo' | bar`

Combined BB and VC expressions:

*   `foo | bar & baz`
*   `foo | bar:42:43 & baz:'qux':'quux'`
*   `foo | bar | baz & qux & quux`

Invalid combined BB and VC expressions (BB must come at the end):

*   `foo & bar | baz`
*   `foo | bar & baz | qux`