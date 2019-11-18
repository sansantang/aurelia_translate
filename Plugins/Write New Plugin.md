原文：https://aurelia.io/docs/plugins/write-new-plugin

>A tutorial on how to write a new Aurelia plugin.

* [1 Introduction \- 简介](#1-introduction---%E7%AE%80%E4%BB%8B)
* [2 Setup \- 安装](#2-setup---%E5%AE%89%E8%A3%85)
* [3 Structure of Plugin \- 插件结构](#3-structure-of-plugin---%E6%8F%92%E4%BB%B6%E7%BB%93%E6%9E%84)
  * [3\.1 Plugin Entry \- 插件入口](#31-plugin-entry---%E6%8F%92%E4%BB%B6%E5%85%A5%E5%8F%A3)
  * [3\.2 Create New Resources \- 创建新的资源](#32-create-new-resources---%E5%88%9B%E5%BB%BA%E6%96%B0%E7%9A%84%E8%B5%84%E6%BA%90)
  * [3\.2 Resource import within the dev app \- 在开发应用程序内导入资源](#32-resource-import-within-the-dev-app---%E5%9C%A8%E5%BC%80%E5%8F%91%E5%BA%94%E7%94%A8%E7%A8%8B%E5%BA%8F%E5%86%85%E5%AF%BC%E5%85%A5%E8%B5%84%E6%BA%90)
* [4\.Develop Plugin \- 开发插件](#4develop-plugin---%E5%BC%80%E5%8F%91%E6%8F%92%E4%BB%B6)
  * [4\.1 Run Dev App \- 运行开发程序](#41-run-dev-app---%E8%BF%90%E8%A1%8C%E5%BC%80%E5%8F%91%E7%A8%8B%E5%BA%8F)
  * [4\.2 Tests \- 测试](#42-tests---%E6%B5%8B%E8%AF%95)
  * [4\.3 Manage dependencies \- 管理依赖关系](#43-manage-dependencies---%E7%AE%A1%E7%90%86%E4%BE%9D%E8%B5%96%E5%85%B3%E7%B3%BB)
  * [4\.4 Build Plugin \- 生成插件](#44-build-plugin---%E7%94%9F%E6%88%90%E6%8F%92%E4%BB%B6)
  * [4\.5 Consume Plugin \- 自定义插件](#45-consume-plugin---%E8%87%AA%E5%AE%9A%E4%B9%89%E6%8F%92%E4%BB%B6)
  * [4\.6 Publish npm package \- 发布npm包](#46-publish-npm-package---%E5%8F%91%E5%B8%83npm%E5%8C%85)
  * [4\.7 Automate changelog, git push, and npm publish \- 自动化变更日志，git push和npm发布](#47-automate-changelog-git-push-and-npm-publish---%E8%87%AA%E5%8A%A8%E5%8C%96%E5%8F%98%E6%9B%B4%E6%97%A5%E5%BF%97git-push%E5%92%8Cnpm%E5%8F%91%E5%B8%83)
* [5\.Advanced Usage \- 高级用法](#5advanced-usage---%E9%AB%98%E7%BA%A7%E7%94%A8%E6%B3%95)
  * [5\.1 Wrap other plugins \- 包装其他插件](#51-wrap-other-plugins---%E5%8C%85%E8%A3%85%E5%85%B6%E4%BB%96%E6%8F%92%E4%BB%B6)
  * [5\.2 Change Aurelia DI Behavior \- 改变Aurelia DI行为](#52-change-aurelia-di-behavior---%E6%94%B9%E5%8F%98aurelia-di%E8%A1%8C%E4%B8%BA)


## 1 Introduction - 简介

由于Aurelia编码约定，编写一个新的Aurelia插件并不困难。然而，建立一个新的插件项目是困难的，但现在你可以毫不费力地生成一个新的插件项目使用Aurelia-CLI。

## 2 Setup - 安装

在本教程中，我们将使用[Aurelia-CLI](/docs/cli)创建插件项目。

First, make sure you installed the latest Aurelia-CLI.

``` shell
npm i -g aurelia-cli
```

  Then run command `au new --plugin` or `au new project-name --plugin`. 
  您将被要求提供一个项目名称，后面是一些选项。如果你不确定你想要什么，你可以在第一个问题中选择一个默认的ESNext或TypeScript设置。否则，您可以创建自定义项目。只需按照提示操作即可。

## 3 Structure of Plugin - 插件结构


由Aurelia-CLI创建的插件项目不仅提供了插件源本身，而且还提供了一个开发应用程序(带有CLI内置的bundler和RequireJS)来简化插件的开发。

>提示：我们尚未在Webpack设置中提供带有dev-app的插件框架。我们使用CLI内置捆绑程序是因为它允许我们共享一些设置来转换插件资源（js / html / css）。

1.  本地 `src/` folder, 是插件的源代码.
2.  The local `dev-app/` folder,是开发应用程序的代码，就像一个由aurelia-cli引导的普通应用程序。
3.  你可以像开发应用程序一样在开发中使用普通的`au run` and `au test` 。
4.  你可以使用 aurelia-testing 来测试你的插件，就像开发应用程序一样。
5.  为确保与其他应用程序兼容，请始终在`src/`目录下的文件中使用`PLATFORM.moduleName()`捆绑包装器。您不需要在 `dev-app/` 文件夹中使用包装器，因为CLI内置捆绑程序支持不带包装器的模块名称。

### 3.1 Plugin Entry - 插件入口

插件入口文件是 `src/index.js` (or `src/index.ts` if using TypeScript). 它只导出一个名为 `configure` 的函数。

**index.js**

``` javascript
import {PLATFORM} from 'aurelia-pal';
  
  export function configure(config) {
    config.globalResources([
      PLATFORM.moduleName('./elements/hello-world')
    ]);
  }
```
当最终用户像这样在他们的应用程序中使用你的插件时，Aurelia会调用`configure`函数：

**main.js**

``` javascript
aurelia.use.plugin('your-plugin-name');
```

  
可以在`configure`函数中使用的方法列在 [FrameworkConfiguration](/docs/api/framework/class/FrameworkConfiguration). `globalResources` 可以注册自定义元素、自定义属性、值转换器和绑定行为，以便在最终用户的应用程序中全局可用。

>提示：注意，`globalResources`不是您可以使用的唯一方法。您可以使用其他方法来引入额外的插件，将对象注册到Aurelia DI容器，等等。我们将在本教程的后面展示其中的一些用法。

### 3.2 Create New Resources - 创建新的资源

您可以手动创建一个新的custom element, custom attribute, value converter or binding behavior（定制元素、定制属性、值转换器和绑定行）自定义元素、自定义属性、值转换器或绑定行为，或者使用命令 `au generate`来提供帮助。

``` shell
  au generate element some-name
  au generate attribute some-name
  au generate value-converter some-name
  au generate binding-behavior some-name
```

默认情况下，cli generate命令生成以下文件夹中的文件：

``` shell
  src/elements
  src/attributes
  src/value-converters
  src/binding-behaviors
```

请注意，文件夹结构仅是为了帮助您组织文件，不是Aurelia的要求。您可以在`src/`中的任何位置手动创建新元素（或其他东西）。

添加一些新文件后，需要将其注册在 `src/index.js` (or `src/index.ts`. Like this:

   
``` javascript
 config.globalResources([
    // ...
    PLATFORM.moduleName('./path/to/new-file-without-ext')
  ]);
```
### 3.2 Resource import within the dev app - 在开发应用程序内导入资源
  
在dev应用程序中，当你需要从内部插件中导入一些东西(例如，为依赖注入导入一个类)时，使用特殊的名称`"resources"`来引用内部插件。

**app.js**

``` javascript
  import {inject} from 'aurelia-framework';
  // "resources" refers the inner plugin src/index.js
  import {MyService} from 'resources';
  
  @inject(MyService)
  export class App {
    constructor(myService) {
      this.myService = myService;
    }
  }
```

  
## 4.Develop Plugin - 开发插件

### 4.1 Run Dev App - 运行开发程序

使用 `au run --open`命令运行内置的开发应用程序，它将自动打开浏览器，向您展示示例自定义元素`hello-world`。

>提示：如果你在运行`au new --plugin`时选择了“Custom Aurelia Plugin”，那么最后一个问题将允许你选择“Basic”scaffolding 而不是“None”。“Basic”将为您提供 custom attribute, value converter, and binding behavior（定制属性、值转换器和绑定行）为方面的其他示例。

### 4.2 Tests - 测试


在运行测试之前终止正在运行的开发应用程序。运行 `au test` 以运行单元测试。根据您对单元测试框架（karma/jest）的选择，编写单元测试的方法略有不同，请遵循 `test/unit/`中的现有示例。

对于你的插件的质量，我们建议使用karma，因为我们真的想在真正的浏览器上测试。Jest使用模拟的浏览器环境在NodeJS中运行测试。Jest要快得多，但它并没有真正在浏览器内测试你的插件。


### 4.3 Manage dependencies - 管理依赖关系


默认情况下，此插件在package.json中没有“依赖项”。从理论上讲，此插件至少依赖 `aurelia-pal`，因为`src/index.js` (or `src/index.ts`)会导入它。如果您构建引用它们的高级组件，它还可能依赖于更多的核心Aurelia软件包，例如 `aurelia-binding` or `aurelia-templating`。

理想情况下，您需要小心地将这些 `aurelia-pal` (`aurelia-binding`...) 添加到package.json中的“依赖项”中。但实际上，你不必这么做。因为每个使用这个插件的应用程序都会安装完整的Aurelia核心包。

此外，将这些依赖项排除在 plugin.json 之外有两个好处。

1.  确保这个插件不会给消费者的应用程序带来重复的Aurelia核心包。这主要是针对使用 webpack 构建的应用程序。由于第三方插件要求使用`aurelia-binding` v1，我们遇到了`aurelia-binding` v1和v2冲突。
2.  减少安装此插件时npm/yarn的负担。

如果您是一个完美主义者，无法忍受遗漏依赖项，我建议您在package.json的“`peerDependencies`”中添加`aurelia-pal` (`aurelia-binding`...)。所以至少它不会导致一个重复的Aurelia核心包。

如果你的插件依赖于其他npm包，比如lodash或jquery，**你必须将它们添加到package.json的“依赖项”中**。

### 4.4 Build Plugin - 生成插件

Run `au build-plugin`. 这会将所有文件从 `src/` folder to `dist/native-modules/` and `dist/commonjs/`.

For example, `src/index.js` (or `src/index.ts`) 会变成 `dist/native-modules/index.js` 和 `dist/commonjs/index.js`.

请注意， `dev-app/`文件夹中的所有其他文件都是针对dev应用的，它们不会出现在已发布的npm包中。

### 4.5 Consume Plugin - 自定义插件

默认情况下，`dist/`文件夹不提交给git。（我们在`.gitignore`中有 `/dist`）。但这不会阻止您通过直接git引用使用此插件。

你可以直接使用这个插件：

``` shell
npm i github:your_github_username/your-plugin-name
  # or if you use bitbucket
  npm i bitbucket:your_github_username/your-plugin-name
  # or if you use gitlab
  npm i gitlab:your_github_username/your-plugin-name
  # or plain url
  npm i https:/github.com/your_github_username/your-plugin-name.git
```

然后像这样在应用程序的`main.js` 或 `main.ts`中加载插件。

``` shell
  aurelia.use.plugin('your-plugin-name');
  // for webpack user, use PLATFORM.moduleName wrapper
  aurelia.use.plugin(PLATFORM.moduleName('your-plugin-name'));
```
丢失的`dist/`文件将由npm通过以下方式填充`"prepare": "npm run build"`（在package.json的`"scripts"`部分中）。

Yarn 有一个忽略`"prepare"`脚本的[bug](https://github.com/yarnpkg/yarn/issues/5235)。如果要使用yarn通过直接git引用使用插件，请从`.gitignore`中删除`/dist`并提交所有文件。请注意，如果您仅使用yarn通过已发布的npm软件包（`npm i your-plugin-name`）使用此插件，则无需提交`dist/`文件。

### 4.6 Publish npm package - 发布npm包

默认情况下，包中的`"private"`字段在package.json已经打开，这可以防止您意外地将私有插件发布到npm。

将插件发布到 npm 以供公众参考:

1.  Remove `"private": true,` from package.json.

2.  增加项目版本。这将首先运行 `au test` (在package.json中的“preversion预版本”中)。
  
``` shell
npm version patch # or minor or major
```

 3.  将更改推入git服务器


``` shell
git push && git push --tags
```
  
4.  然后发布到npm，您需要有您的npm帐户登录。

``` shell
   npm publish
```

  
### 4.7 Automate changelog, git push, and npm publish - 自动化变更日志，git push和npm发布

您可以启用npm版本补丁#或次要版本或主要版本（`npm version patch # or minor or major`）自动更新changelog，将提交和版本标记推到git服务器，并发布到npm。

Here is one simple setup.

1.  `npm i -D standard-changelog`. 我们使用标准变更日志（[standard-changelog](https://github.com/conventional-changelog/conventional-changelog)）作为支持常规变更日志的最小示例。

*   或者，您也可以使用高级[标准版本]([standard-version](https://github.com/conventional-changelog/standard-version) )。

2.  将两个命令添加到package.json的`"scripts"`部分。
  
  
``` JavaScript
"scripts": {
    // ...
    "version": "standard-changelog && git add CHANGELOG.md",
    "postversion": "git push && git push --tags && npm publish"
  },
```

3. 如果您的项目是私有的，你可以删除`&& npm publish`。
  
## 5.Advanced Usage - 高级用法

### 5.1 Wrap other plugins - 包装其他插件

您可以使用您的插件来包装其他插件，而不提供任何额外的功能。这对于组织中重用的公共插件列表的分组是非常有用的。

**index.js**

``` javascript
export function configure(config) {
    config.plugin(PLATFORM.moduleName('aurelia-animator-css'));
    config.plugin(PLATFORM.moduleName('aurelia-dialog'), config => {
      config.useDefaults();
      config.settings.lock = true;
      config.settings.ignoreTransitions = true;
    });
  }
```

  
对于上面的例子，您需要确保在package.json中将`aurelia-animator-css` and `aurelia-dialog`添加到插件的“依赖项”中。

### 5.2 Change Aurelia DI Behavior - 改变Aurelia DI行为

默认的Aurelia DI对任何JavaScript类使用单例，您可以覆盖类的行为。

**index.js**
   
``` javascript
  import {MyAwesomeService} from './my-awesome-service';
  
  export function configure(config) {
    // new instance for every injection of MyAwesomeService
    config.transient(MyAwesomeService, MyAwesomeService);
  }
  
  export {MyAwesomeService};
```

  
