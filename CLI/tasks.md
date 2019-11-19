原文：https://aurelia.io/docs/cli/tasks

## 1 Introduction - 简介

Aurelia CLI允许您执行Gulp任务，并且提供了一些开箱即用的Gulp任务，可以帮助您启动。对于任何新项目，您都需要一个任务来构建、运行和测试应用程序，因此CLI为您提供了这些任务。这些任务可以在`aurelia_project/tasks`目录中找到。

CLI应用程序中的Gulp任务是普通的Gulp v4任务。v4带来了gulp.series和 gulp.parallel，使Gulp 任务更简单、更清晰。

Typescript完全支持在Gulp任务中使用 ES2017。Gulp任务中使用的语言与源代码中的语言相同，由`aurelia.json`中的`transpiler`对象确定。

## 2 Task execution - 任务执行

可以使用`au`命令执行任务。`au build`将执行从 `aurelia_project/tasks/build.js`导出的Gulp任务。和`au test`执行`aurelia_project/tasks/test.js`中的任务。

值得高兴的是，Aurelia CLI将执行导出为默认值的任务，这意味着您可以导出多个任务

**Export multiple tasks**

``` javascript
  let task1 = () => {};
  let task2 = () => {};
  
  export { task1 as default, task2 };
```

使用 `au`命令执行上述任务时，将执行 `task1`。

  ## 3 Creating a new task - 创建一个新的任务
CLI的任务运行器很酷的一点是，您也可以创建自己的任务，并使用`au` 命令运行它们。

只需在 `aurelia_project/tasks`目录中创建一个新的`.js` or `.ts`文件，并从该文件导出一个函数。

## 4 Task metadata - 任务的元数据

`au help`命令不仅显示了标准的Aurelia CLI命令，它还列出了任务，而且只列出了那些定义了我们称之为“task metadata（任务元数据）”的任务。可以在与任务同名的`.json`文件中找到此元数据。

例如，`build.js` 任务可以有一个`build.js` 文件，结构如下:

**Task metadata**

``` json
{
  	"name": "build",
  	"description": "Builds and processes all application assets.",
  	"flags": [
  		{
  			"name": "env",
  			"description": "Sets the build environment.",
  			"type": "string"
  		},
  		{
  			"name": "watch",
  			"description": "Watches source files for changes and refreshes the bundles automatically.",
  			"type": "boolean"
  		}
  	],
  	"parameters": [
  		{
  			"name": "some-parameter",
  			"description": "a description of this parameter"
  		}
  	]
  }
```
任何带有这种json文件的任务，都会在`au help`中显示出来。它将显示描述、任务支持的任何标志和任何参数。

如果一个标志的`type`没有设置为 `"boolean"`，那么 `au help`将显示该标志支持一个值。
  
