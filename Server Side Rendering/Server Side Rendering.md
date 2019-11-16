原文：https://aurelia.io/docs/ssr/introduction

>An introduction to server-side rendering with Aurelia.

* [1\.What is Server Side Rendering?](#1what-is-server-side-rendering)
* [2\.Caveats 注意事项](#2caveats-%E6%B3%A8%E6%84%8F%E4%BA%8B%E9%A1%B9)
* [3\.How It Works](#3how-it-works)
* [4\.Isomorphic Code 同构的代码](#4isomorphic-code-%E5%90%8C%E6%9E%84%E7%9A%84%E4%BB%A3%E7%A0%81)
* [5\.Memory Management 内存管理](#5memory-management-%E5%86%85%E5%AD%98%E7%AE%A1%E7%90%86)
* [6\.Different Main Entry Points 不同的主要入口点](#6different-main-entry-points-%E4%B8%8D%E5%90%8C%E7%9A%84%E4%B8%BB%E8%A6%81%E5%85%A5%E5%8F%A3%E7%82%B9)
* [7\.404 pages](#7404-pages)
* [8\.Skeleton with SSR Pre\-Configured 骨架与SSR预先配置](#8skeleton-with-ssr-pre-configured-%E9%AA%A8%E6%9E%B6%E4%B8%8Essr%E9%A2%84%E5%85%88%E9%85%8D%E7%BD%AE)


Aurelia框架是一个客户端JavaScript框架。其中一个特征是，在页面加载时，获取并执行一组JavaScript代码，启动框架和应用程序。除了提供客户机静态文件外，服务器不做其他的事情。


这有几个缺点。由于从服务器返回的HTML不包含任何内容(只有引用JavaScript包的样板HTML文件)，搜索引擎的爬虫无法有效地完成它们的工作。


另一个缺点是加载应用程序所需的时间。首先，浏览器必须从服务器获取HTML文件，加载所有包(可能比较大)，最后必须启动应用程序。只有这样用户才能看到他们请求的页面。


解决这些问题的一个方法是服务器端呈现(SSR)， Aurelia框架支持它。继续往下读，了解更多信息。


>提示：服务器端渲染，是Aurelia框架的一个新特性，打算供早期用户使用。
>



## 1.What is Server Side Rendering?

服务器端渲染是一个概念，其中服务器承担更多的职责，即在服务器上呈现应用程序并将完整呈现的页面发送回客户机。
通过这样做，用户将更快地看到呈现的页面，在加载包和应用程序启动客户端之前。因为服务器返回的页面已经完全呈现(页面内容已经在HTML中)，所以搜索引擎会给您的站点更高的排名。


服务器端渲染之所以可行，是因为有一种叫做同构（isomorphism）的东西。同构框架允许您在客户机和服务器上运行一个应用程序。Aurelia就是这样一个框架。



## 2.Caveats 注意事项


尽管服务器端呈现可以解决客户端应用程序的一些问题，但是有一些需要注意的地方。最重要的一点是，您的应用程序将在两个不同的环境中运行:浏览器和Node.js。这之所以成为可能，主要是因为Aurelia的平台抽象层（[Platform Abstraction Layer](https://github.com/aurelia/pal)）抽象了特定于平台的逻辑。使用这些抽象非常重要。


Node.js在某些方面与浏览器环境有很大的不同。不存在(实际的)DOM、`window` 或 `document`全局变量。任何依赖于全局变量的方法(例如jQuery)都将不起作用。要模拟DOM，需要使用一个名为jsdom的库。服务器环境中使用的平台抽象层使用jsdom，以便能够完成基本的DOM操作。只要使用平台抽象层对DOM进行任何修改，就不会有问题。

开发您的应用程序，使其能够同时在客户机和服务器上渲染，这需要时间和精力。我们的建议是仅在您可以花费时间使代码在客户端和服务器上都能工作时才使用服务器端渲染。


由于服务器不仅仅在服务器端渲染设置中提供静态文件，还可以做更多的事情，因此会使用更多的资源。
因此，必须有足够的可用内存和CPU资源。


## 3.How It Works

服务器端渲染使用三个库：

*   `aurelia-middleware-koa`
*   `aurelia-ssr-bootstrapper-webpack`
*   `aurelia-ssr-engine`


 `aurelia-middleware-koa`库是 [Koa](http://koajs.com/) 的中间件，它指示`aurelia-ssr-engine`渲染请求的页面并返回完全渲染的HTML。


`aurelia-ssr-bootstrapper-webpack`库提供三个功能：`initialize`初始化，`start`启动和`stop`停止。这些函数由`aurelia-ssr-engine`调用以启动和停止应用程序，并且必须是服务器捆绑包的默认导出。


当请求到达web服务器并且中间件启动了`aurelia-ssr-engine`时，该引擎将启动一个新的Aurelia实例。在启动应用程序之前，请求url用于设置服务器上的当前url，以便Aurelia呈现用户请求的页面。一旦页面被呈现，HTML就被发送回客户端。之后，应用程序将停止并从内存中释放。


当客户机从服务器接收到HTML时，它已经能够呈现整个页面，因此呈现初始内容要快得多。在初始渲染之后，浏览器将获取客户端包并在客户端启动Aurelia应用程序。


从浏览器呈现了从服务器接收到的HTML到浏览器完成加载包并启动应用程序之间有大量的时间。在这段时间内，用户已经可以单击按钮、编写文本输入等，但是由于Aurelia应用程序还没有运行，所以没有JavaScript来处理这些事件。


这就是所谓的 `preboot`库发挥作用的地方。Preboot捕获在呈现服务器视图和呈现客户机视图之间发生的所有事件。只要应用程序在客户机上运行， `preboot`就会回放它捕获的所有事件。这使您的应用程序可以像在没有服务器端呈现的设置中那样处理事件。例如，当用户单击服务器视图中的按钮时，将注册并存储click事件。应用程序在客户端启动后，将重播click事件，并且JavaScript能够处理click事件。


另一个有趣的方面是，有两个捆绑软件，一个用于客户端应用程序，一个用于服务器端应用程序。这使您可以为客户端和服务器设置不同的入口点。



## 4.Isomorphic Code 同构的代码

为了使用服务器端渲染，必须以某种方式编写代码，使其可以在客户端（浏览器环境）和服务器（Node.js环境）上运行。


在没有服务器端渲染的设置中，您可以执行以下操作而不会出现任何问题：

**DOM Manipulation in the Browser**

``` javascript
export class MyPage {
    activate() {
      document.body.appendChild(document.createElement('div'));
    }
  }
```

但是，由于服务器上没有全局文档`document`，因此您需要使用PAL：

**DOM Manipulation Using SSR**

``` javascript
 import {DOM} from 'aurelia-pal';
  
  export class MyPage {
    activate() {
      DOM.appendChild(DOM.createElement('div'));
    }
  }
```


请参考平台抽象层[Platform Abstraction Layer](http://aurelia.io/docs/api/pal)的API文档，了解有哪些功能可用。


## 5.Memory Management 内存管理


由于应用程序将在服务器上执行很多次，所以需要进行一些内存管理。有几件事要记住。


应该避免使用`setTimeout` 或 `setInterval`之类的计时器。如果您确实需要使用它们，请务必妥善处理，并尽快处理。否则Node.js将无法卸载应用程序，因为计时器是对应用程序代码的回调，只要计时器是活动的，应用程序就不能被垃圾收集。


另一件要避免的事情是覆盖全局原型。例如，覆盖数组`Array`中的任何函数(例如push、unshift等)都是不好的做法，因为Node.js将无法卸载应用程序，因为全局变量将在应用程序代码中有一个指向函数的指针。


## 6.Different Main Entry Points 不同的主要入口点

在非ssr应用程序中，可以使用一个主模块(`main.js` 或 `main.ts`)在启动期间配置应用程序。

当应用程序在客户端运行时，该配置可以工作，但是当应用程序在服务器端启动时，您可能需要不同的配置。

为此，您可以创建一个`server-main.js` 或 `server-main.ts`如下：

**Alternative Entry Point**

``` javascript
 import { Aurelia } from 'aurelia-framework';
  import { PLATFORM } from 'aurelia-pal';
  import bootstrapper from './ssr-bootstrapper-webpack';
  
  async function configure(aurelia: Aurelia) {
    aurelia.use
      .standardConfiguration();
  
    await aurelia.start();
    await aurelia.setRoot(PLATFORM.moduleName('app'));
  }
  
  module.exports = bootstrapper(configure);
```
Then, in `webpack.server.config.js` you can configure the server bundle to use the `server-main` as the main entry point of your application:
  
  **Alternative Entry Point in Webpack**

``` json
"entry": {
    "server": "./src/server-main"
  },
```

## 7.404 pages

使用服务器端渲染时，您可能希望处理对不存在的路由的请求。

这可以通过将未知路由映射到404模块来完成：

  **404 Page in Aurelia**

``` javascript
export class App {
    router: Router;
  
    configureRouter(config: RouterConfiguration, router: Router) {
      config.title = 'Aurelia';
      config.options.pushState = true;
      config.options.root = '/';
  
      // routes omitted
      config.map([]);
  
      config.mapUnknownRoutes(PLATFORM.moduleName('not-found'));
  
      this.router = router;
    }
  }
```

当用户请求一个不存在的路由时，服务器将像呈现其他页面一样呈现404页面。


## 8.Skeleton with SSR Pre-Configured 骨架与SSR预先配置


为了让你更容易开始与服务器端渲染，我们把一个骨架（skeleton）应用程序，已经配置了服务器端渲染。


预配置的SSR框架可以在这里 [here](https://github.com/aurelia/skeleton-navigation) 找到。按照自述文件中的说明开始。


  
