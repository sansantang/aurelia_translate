原文：https://aurelia.io/docs/cli/basics

* [1 Introduction \- 简介](#1-introduction---%E7%AE%80%E4%BB%8B)
* [2 Machine Setup \- 安装](#2-machine-setup---%E5%AE%89%E8%A3%85)
* [3 Creating A New Aurelia Project \- 创建一个新Aurelia项目](#3-creating-a-new-aurelia-project---%E5%88%9B%E5%BB%BA%E4%B8%80%E4%B8%AA%E6%96%B0aurelia%E9%A1%B9%E7%9B%AE)
    * [Windows上的Git BASH](#windows%E4%B8%8A%E7%9A%84git-bash)
* [4 Creating A New Aurelia Plugin Project \- 创建一个新的Aurelia插件项目](#4-creating-a-new-aurelia-plugin-project---%E5%88%9B%E5%BB%BA%E4%B8%80%E4%B8%AA%E6%96%B0%E7%9A%84aurelia%E6%8F%92%E4%BB%B6%E9%A1%B9%E7%9B%AE)
* [5 Running Your Aurelia App \- 运行你的Aurelia应用程序](#5-running-your-aurelia-app---%E8%BF%90%E8%A1%8C%E4%BD%A0%E7%9A%84aurelia%E5%BA%94%E7%94%A8%E7%A8%8B%E5%BA%8F)
* [6 Environments \- 环境设置](#6-environments---%E7%8E%AF%E5%A2%83%E8%AE%BE%E7%BD%AE)
* [7 Building Your App \- 构建您的应用程序](#7-building-your-app---%E6%9E%84%E5%BB%BA%E6%82%A8%E7%9A%84%E5%BA%94%E7%94%A8%E7%A8%8B%E5%BA%8F)
* [8 Generators \- 生成器](#8-generators---%E7%94%9F%E6%88%90%E5%99%A8)
    * [name\-in\-kebab\-case](#name-in-kebab-case)
* [9 Aurelia\.json](#9-aureliajson)
* [10 Webpack vs Built\-in Bundler \- Webpack vs 内置打包](#10-webpack-vs-built-in-bundler---webpack-vs-%E5%86%85%E7%BD%AE%E6%89%93%E5%8C%85)
  * [10\.1 Webpack](#101-webpack)
  * [10\.2 CLI's Built\-in Bundler](#102-clis-built-in-bundler)
* [11 What if I need help? \- 如果我需要帮助？](#11-what-if-i-need-help---%E5%A6%82%E6%9E%9C%E6%88%91%E9%9C%80%E8%A6%81%E5%B8%AE%E5%8A%A9)


## 1 Introduction - 简介

Aurelia CLI是Aurelia的官方命令行工具。它可以用于创建新项目、脚手架组件和将应用程序打包发布。这是开始一个新的Aurelia项目的最好方法。

## 2 Machine Setup - 安装

CLI本身有两个必须首先安装的先决条件:

*   Install Node.js version 10 or above.
    *   You can [download it here](https://nodejs.org/en/) .
*   Install a Git Client
    *   Here's [a nice GUI client](https://desktop.github.com) .
    *   Here's [a standard client](https://git-scm.com) .

安装了先决条件之后，就可以安装Aurelia CLI本身了。在命令行中，使用npm全局安装CLI：

``` javascript
npm install aurelia-cli -g
```

 > 根据环境的不同，在执行npm全局安装时可能需要使用`sudo`。

## 3 Creating A New Aurelia Project - 创建一个新Aurelia项目

要创建新项目，可以运行`au new`。你将会看到许多选项。如果不确定想要什么，可以选择一个默认值。否则，您可以创建自定义项目。只需按照提示操作即可。

一旦您做出了选择，CLI将显示您的选择并询问是否要创建文件结构。之后，系统会询问您是否愿意安装新项目的依赖项。

一旦安装了依赖项，您的项目就可以开始了。

#### Windows上的Git BASH
>警告：Aurelia CLI命令在Windows上的Git BASH模拟器中不能正常工作。Windows用户请使用CMD或PowerShell运行Aurelia CLI命令。

## 4 Creating A New Aurelia Plugin Project - 创建一个新的Aurelia插件项目

要创建一个新的插件项目，运行 `au new --plugin`. More details in tutorial on [write new plugin](https://github.com/sansantang/aurelia_translate/blob/master/Plugins/Write%20New%20Plugin.md).

## 5 Running Your Aurelia App - 运行你的Aurelia应用程序

从您的项目文件夹中，只需执行`au run`。这将构建您的应用程序，在此过程中创建所有包。它将启动一个最小的web服务器并为您的应用程序提供服务。默认情况下，当源代码更改时，dev web服务器会自动刷新浏览器。如果你选择使用**ASP.NET Core**和**Webpack**，您将希望使用`dotnet run`命令而不是`au run`。

## 6 Environments - 环境设置

CLI构建系统知道您可能在不同的环境中运行代码。默认情况下，你有三种配置: `dev`, `stage` and `prod`。您可以使用`--env` 标志来指定要在哪个环境下运行。 例如:`au run --env prod`。

当您构建 `src/environment.js` or `src/environment.ts`时。ts文件将被来自 `aurelia_project/environments` 目录的`dev.js`, `stage.js` or `prod.js`文件覆盖。

## 7 Building Your App - 构建您的应用程序

Aurelia CLI应用程序总是以绑定模式运行，甚至在开发期间也是如此。要构建应用程序，只需运行`au build`。您还可以指定要为其构建的环境。例如:`au build --env stage`。

## 8 Generators - 生成器

执行`au generate <resource>` 会运行一个生成器来支持典型的Aurelia构造。*资源*选项有：`element`, `attribute`, `value-converter`, `binding-behavior`, `task` and `generator`.

For example `au generate element my-awesome-element` generates `src/resources/my-awesome-element.js` or `my-awesome-element.ts`.

#### name-in-kebab-case

>根据Aurelia约定，我们使用[kebab case](https://en.wikipedia.org/wiki/Letter_case#Special_case_styles)来命名任何新的 `element`, `attribute`, `value-converter`, or `binding-behavior`.（“元素”、“属性”、“值转换器”或“绑定行为”）。

还有一个`generator`生成器，因此您可以创建自己的`au generate generator`。

## 9 Aurelia.json 

在`aurelia_project`目录中有一个名为`aurelia.json`的文件。这个文件是所有 gulp 任务(如构建和测试)的集中设置。在讨论各种自定义时，我们将展示这个文件的更多细节。

## 10 Webpack vs Built-in Bundler - Webpack vs 内置打包

在使用Aurelia CLI创建新项目时，您将看到一个向导，用于选择bundler、模块加载器、CSS预处理器等。

在所有选择中，您首先需要选择一个bundler:要么是Webpack(默认的bundler)，要么是CLI的内置bundler(备选的bundler)。

### 10.1 Webpack

Webpack是ESNext和TypeScript应用程序的默认选择。

Webpack是一个内置模块加载器的bundler。如果选择使用Webpack，则不需要单独的模块加载器。Webpack功能强大，很受欢迎，但是从零开始建立Webpack可能是一项艰巨的任务。Aurelia CLI为您的应用程序生成了一个经过实战测试的Webpack配置文件，为您进一步的定制提供了坚实的基础。

>警告：为了确保您的代码能很好地与Webpack一起工作，您需要使用`PLATFORM.moduleName('someModule')`来调用模块引用。请阅读[Webpack部分](https://aurelia.io/docs/cli/webpack/)的更多内容。

### 10.2 CLI's Built-in Bundler

Aurelia CLI附带了一个内部制造的bundler，它提供了与Webpack类似的功能，但是配置更简单。如果您没有使用Webpack的经验，我们建议您使用内置的bundler。

内置的bundler与模块加载器RequireJS或SystemJS配对。

*   RequireJS已经存在很长时间了，它是AMD模块格式的参考模块加载器。与SystemJS相比，它被认为更成熟、更稳定，但功能更少。
*  SystemJS是一个“动态ES模块加载器”，是JavaScript世界中最通用的模块加载器，支持AMD/CommonJS/UMD或本机ESNext模块格式。这在运行时给了你最大的自由。

如果您不需要或不确定SystemJS的功能，请选择RequireJS。

>内置的bundler支持CommonJS (Node.js默认)、AMD、UMD或本机ES模块格式的任何npm包。

以前版本的内置bundler要求用户手动维护`aurelia.json`中的依赖项配置。但是现在我们有了一个全新的bundler，我们称之为自动跟踪。顾名思义，它无需显式配置即可自动跟踪依赖项。你很少需要使用`aurelia.json`用于依赖项管理。

如果您将一个应用程序从旧的CLI bundler迁移到最新的CLI bundler，大多数应用程序仍然可以在不修改 `aurelia.json`的情况下工作。如果您的应用程序无法使用最新的CLI bundler，请阅读[迁移指南](https://aurelia.io/docs/cli/migrating)。如果您仍然有问题，请在[Aurelia话语论坛](https://discourse.aurelia.io/)上提问或在我们的[GitHub repo](https://github.com/aurelia/cli/issues)上创建问题，我们将帮助您解决。

Read [CLI bundler chapter](/docs/cli/cli-bundler) for more details.

有关Webpack、RequireJS和SystemJS的更多信息，请参考以下网站。

*   https://webpack.github.io/
*   https://requirejs.org/
*   https://github.com/systemjs/systemjs/

## 11 What if I need help? - 如果我需要帮助？

Run `au help`, or you can reach us on [Aurelia Discourse forum](https://discourse.aurelia.io/) .如果您不确定正在运行的CLI版本，可以运行 `au -v`;