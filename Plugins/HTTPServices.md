原文：https://aurelia.io/docs/plugins/http-services

通常JavaScript应用程序需要从各种web服务获取数据或与之通信。在本文中，我们将看看Aurelia为您提供的选项。

* [1.Options 选项](#options-%E9%80%89%E9%A1%B9)
* [2.aurelia\-fetch\-client](#aurelia-fetch-client)
  * [Learn About The Fetch Spec](#learn-about-the-fetch-spec)
  * [Bring Your Own Polyfill 带上你自己的填充工具](#bring-your-own-polyfill-%E5%B8%A6%E4%B8%8A%E4%BD%A0%E8%87%AA%E5%B7%B1%E7%9A%84%E5%A1%AB%E5%85%85%E5%B7%A5%E5%85%B7)
   	* [封装HttpClient使用](#%E5%B0%81%E8%A3%85httpclient%E4%BD%BF%E7%94%A8)
  * [Basic Use 基本使用](#basic-use-%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8)
  * [Configuration 配置](#configuration-%E9%85%8D%E7%BD%AE)
  * [Helpers](#helpers)
  * [警告说明](#%E8%AD%A6%E5%91%8A%E8%AF%B4%E6%98%8E)
  * [A Complete Example](#a-complete-example)
  * [Limitations 限制](#limitations-%E9%99%90%E5%88%B6)
* [3.aurelia\-http\-client](#aurelia-http-client)
  * [Basic Use 基本使用](#basic-use-%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8-1)
    * [JSON By Default](#json-by-default)
  * [Configuration 配置](#configuration-%E9%85%8D%E7%BD%AE-1)


## Options 选项

在构建前端应用程序时，常常需要利用HTTP服务来获取数据或持久化状态。有很多方法可以做到这一点。您可以使用实现对象关系映射的高级数据库、自以为是的restful客户机或简单的HTTP库。所有这些选项都是由全球web社区提供的，并且都可以在Aurelia应用程序中使用。

也就是说，Aurelia团队觉得有必要提供一个开箱即用的简单解决方案。我们希望我们的社区有一个直接支持的选项，同时仍然保持Aurelia的开放，这样我们自己的社区就可以创新或利用其他地方开发的任何其他库。

沿着这些思路，Aurelia项目提供了两个选项：

*   `aurelia-http-client` - 一个基于`XMLHttpRequest`的基本`HttpClient`。它支持所有HTTP谓词、JSONP和请求取消。
*   `aurelia-fetch-client` - 基于Fetch规范的更具前瞻性的HttpClient。 它支持所有HTTP谓词并与Service Workers集成，包括 Request/Response 缓存。

你应该如何在这两个选项中做出选择?如果可能，我们建议使用`aurelia-fetch-client`。它基于Fetch规范，这将是处理未来所有AJAX的首选方法。但是，如果您需要请求取消或下载过程，Fetch规范目前不支持这些特性。虽然将来会考虑对规范进行这些改进，但目前还不能使用。因此，如果需要这些功能，可能需要使用`aurelia-http-client`。

## aurelia-fetch-client

如前所述，`aurelia-fetch-client`库旨在包含和公开新的[Fetch API](https://developer.mozilla.org/zh-cn/docs/Web/API/Fetch_API)，同时提供web应用程序中重要的特性:请求参数的默认配置、拦截器和集中的请求跟踪。主方法`HttpClient#fetch()`具有与`window.fetch()`相同的签名。不同之处在于，我们的HttpClient将应用缺省配置值，执行任何已注册的拦截器，并跟踪活动请求的数量。

>#### Learn About The Fetch Spec
> 如果您正在寻找有关Fetch API规范的一些信息，我们建议您从[MDN文档](https://developer.mozilla.org/zh-CN/docs/Web/API/Fetch_API)开始。 您可能还会发现 [Jake Archibald](http://jakearchibald.com/2015/thats-so-fetch/) 很有用。

### Bring Your Own Polyfill 带上你自己的填充工具

这个库依赖于Fetch API，但并不是所有流行的浏览器都支持这个API。这个库不包含用于获取的polyfill。如果需要支持[尚未实现Fetch的浏览器](http://caniuse.com/#feat=fetch)，则需要安装像[GitHub](https://github.com/github/fetch)这样的polyfill。

首先，使用包管理器安装polyfill。其次，确保将polyfill导入应用程序代码，以便在使用fetch客户机之前正确初始化它。加载polyfill的最佳位置通常在应用程序的`main`模块中。大概是这样的：

**Initializing the Fetch Polyfill**

``` javascript
  import 'whatwg-fetch';
  
  export function configure(aurelia) {
    aurelia.use
      .standardConfiguration()
      .developmentLogging();
  
    aurelia.start().then(() => aurelia.setRoot());
  }
```
>#### 封装HttpClient使用
>一般来说，我们建议您不要在使用HttpClient时乱放应用程序代码。相反，我们建议您创建一个或多个服务类，将所有HTTP访问封装在友好的、特定于应用程序的API后面。如果您这样做，我们还建议您在这些服务模块中导入`fetch` polyfill，而不是在应用程序的主模块中导入。这有助于保持封装。
  
### Basic Use 基本使用

数据请求是通过调用`HttpClient`实例上的 `fetch`方法来完成的。默认情况下， `fetch`发出`GET`请求。所有获取的调用都返回一个承诺`Promise`，该承诺将解析为响应`Response`对象。使用此响应`Response`对象，您可以轻松地解析内容、读取标题和检查状态代码。有关更多信息，请参见Fetch API规范。

下面是一个简单的示例，演示了对JSON文件的基本`GET`请求，包括解析响应内容和向控制台写入数据值。

**GET JSON with Fetch**

``` javascript
  import {HttpClient} from 'aurelia-fetch-client';
  
  let httpClient = new HttpClient();
  
  httpClient.fetch('package.json')
    .then(response => response.json())
    .then(data => {
      console.log(data.description);
    });
```

  
### Configuration 配置

`HttpClient`实例可以配置多个选项，比如在请求或响应上运行默认头文件和拦截器。

**Configuring Fetch Client**
    

``` javascript
  httpClient.configure(config => {
    config
      .withBaseUrl('api/')
      .withDefaults({
        credentials: 'same-origin',
        headers: {
          'Accept': 'application/json',
          'X-Requested-With': 'Fetch'
        }
      })
      .withInterceptor({
        request(request) {
          console.log(`Requesting ${request.method} ${request.url}`);
          return request;
        },
        response(response) {
          console.log(`Received ${response.status} ${response.url}`);
          return response;
        }
      });
  });
 
```

  
在上面的例子中，使用`withBaseUrl()`指定一个基本url，所有的获取都是相对的。

`withDefaults()`允许将一个对象传递给 [Request constructor](https://developer.mozilla.org/zh-cn/docs/Web/API/Request/Request)构造函数，该对象可以包含可选`init`参数中描述的任何属性，并在将其传递给第一个Request拦截器之前将其合并到新请求中。

`withInterceptor()`允许传递一个对象，该对象可以提供这四种可选方法中的任何一种:`request`, `requestError`, `response`和`responseError`。下面解释一下这些方法的工作原理：

*   `request`获取拦截器运行后将传递给`window.fetch()`的[Request](https://developer.mozilla.org/zh-cn/docs/Web/API/Request) 。它应该返回相同的请求，或者创建一个新的请求。它还可以返回对短路调用`fetch()`的[Response](https://developer.mozilla.org/zh-cn/docs/Web/API/Response) ，并立即完成请求。在请求拦截器中抛出的错误将由`requestError`拦截器处理。
*    `requestError`在请求创建和请求拦截器执行期间充当承诺拒绝处理程序。它将接收拒绝原因，并可以通过返回一个有效的请求重新抛出或恢复。
*   `response`将在`fetch()`完成后运行，并接收结果响应。与`request`一样，它可以传递响应、返回修改后的响应或抛出。
*   `responseError`类似于requestError，充当响应拒绝的承诺拒绝处理程序。

拦截器对象上的这些方法还可以返回各自返回值的`Promise`s。

### Helpers

> ### 警告说明
> 
> * `fetch()`返回的承诺在**HTTP错误状态下不会拒绝**，即使响应是HTTP 404或500。相反，它将正常地解析，并且只会在网络故障或任何阻止请求完成的情况下拒绝。
> * 在发送和接收cookie时，为了获得最大的浏览器兼容性，请始终提供`credentials: 'same-origin'`(凭据:“同源”)选项，而不是依赖于默认值。看到发送cookie。
> 

Fetch API有两个陷阱，由[GitHub Fetch polyfill](https://github.com/github/fetch#caveats) 文档记录(上面2条)。`aurelia-fetch-client`提供配置帮助程序来应用polyfill文档中建议的更改。

*   `config.rejectErrorResponses()` 将添加一个响应拦截器，该拦截器将导致状态代码不成功的响应导致被拒绝的承诺。
*   `config.useStandardConfiguration()` 将应用`rejectErrorResponses()`，并将配置`credentials: 'same-origin'` 作为所有请求的默认值。
*   Fetch API没有在请求体中发送JSON的方便方法。对象必须手动序列化为JSON，并适当设置 `Content-Type` header。`aurelia-fetch-client`为此包含一个名为`json`的助手。

**POST JSON with Fetch**


``` JavaScript
  import {HttpClient, json} from 'aurelia-fetch-client';
  
  let comment = {
    title: 'Awesome!',
    content: 'This Fetch client is pretty rad.'
  };
  
  httpClient.fetch('comments', {
    method: 'post',
    body: json(comment)
  });
```

  
### A Complete Example

这个例子创建了一个新的HttpClient，并将其配置为一个假想的JSON API，用于在`api/`上管理注释。然后使用客户机向API发布一个新注释，并显示一个带有指定注释ID的警告对话框。

**POST JSON with Fetch**
   

``` javascript
 import {HttpClient, json} from 'aurelia-fetch-client';
  
  let httpClient = new HttpClient();
  
  httpClient.configure(config => {
    config
      .useStandardConfiguration()
      .withBaseUrl('api/')
      .withDefaults({
        credentials: 'same-origin',
        headers: {
          'X-Requested-With': 'Fetch'
        }
      })
      .withInterceptor({
        request(request) {
          let authHeader = fakeAuthService.getAuthHeaderValue(request.url);
          request.headers.append('Authorization', authHeader);
          return request;
        }
      });
  });
  
  let comment = {
    title: 'Awesome!',
    content: 'This Fetch client is pretty rad.'
  };
  
  httpClient
    .fetch('comments', {
      method: 'post',
      body: json(comment)
    })
    .then(response => response.json())
    .then(savedComment => {
      alert(`Saved comment! ID: ${savedComment.id}`);
    })
    .catch(error => {
      alert('Error saving comment!');
    });
```

  



### Limitations 限制

*   这个库不包含用于Fetch的polyfill。如果需要支持尚未实现Fetch的浏览器，则需要安装像 [GitHub](https://github.com/github/fetch) 这样的polyfill。
*   这个库不处理Fetch API中的任何现有限制，包括:
    *   Fetch目前不支持中止请求或指定请求超时。
    *  Fetch目前不支持进度报告。.
*   这个库目前不提供JSONP支持。
*    [Request constructor](https://developer.mozilla.org/zh-cn/docs/Web/API/Request/Request) 提供自己的默认值，因此，如果在调用 `HttpClient#fetch` 之前创建了一个请求（使用 `HttpClient#fetch(request)`签名，而不是 `HttpClient#fetch(url, params)`签名），客户端无法知道将哪些默认值合并到请求中。可以合并基本URL和标头，但目前在本例中不应用其他缺省值。

如果您的应用程序有不符合这些限制的需求，那么您可能希望转而使用 `aurelia-http-client` 。



## aurelia-http-client

除了 `aurelia-fetch-client`之外，Aurelia还包含一个基本的`HttpClient`，为浏览器的`XMLHttpRequest`对象提供一个舒适的接口。和 `aurelia-fetch-client`一样， `aurelia-fetch-client`也不包含在Aurelia的引导程序安装的模块中，因为它是完全可选的，而且许多应用程序可能会选择使用不同的策略来进行数据检索。因此，如果您想使用它，首先必须使用首选的包管理器安装它。

### Basic Use 基本使用

Once installed, you can use it like this:

**GET JSON with Basic HTTP**

    

``` javascript
  import {HttpClient} from 'aurelia-http-client';
  
  let client = new HttpClient();
  
  client.get('package.json')
    .then(data => {
      console.log(data.description)
    });
```

  `HttpClient`有多种方法。下面是可用api的描述。
  
  **HTTPClient API**

``` TypeScript
  export class HttpClient {
    isRequesting: boolean;
  
    constructor();
  
    configure(fn: ((builder: RequestBuilder) => void)): HttpClient;
    createRequest(url: string): RequestBuilder;
    send(requestMessage: RequestMessage, transformers: Array<RequestTransformer>): Promise<HttpResponseMessage>;
  
    delete(url: string): Promise<HttpResponseMessage>;
    get(url: string): Promise<HttpResponseMessage>;
    head(url: string): Promise<HttpResponseMessage>;
    jsonp(url: string, callbackParameterName?: string): Promise<HttpResponseMessage>;
    options(url: string): Promise<HttpResponseMessage>;
    put(url: string, content: any): Promise<HttpResponseMessage>;
    patch(url: string, content: any): Promise<HttpResponseMessage>;
    post(url: string, content: any): Promise<HttpResponseMessage>;
  }
```

  
如您所见，该API为所有标准谓词以及`jsonp`提供了方便的方法。除了`jsonp`发送`JSONPRequestMessage`之外，这些方法都发送`HttpRequestMessage`。发送消息的结果是`HttpResponseMessage`的`Promise`承诺。

The `HttpResponseMessage` has the following properties:

*   `response` - 返回从服务器发送的原始内容
*   `responseType` - 预期响应类型.
*   `content` - 根据`responseType`格式化原始响应`response` 内容并返回它。
*   `headers` - Returns a `Headers` object with the parsed header data.
*   `statusCode` - The server's response status code.
*   `statusText` - The server's textual status message.
*   `isSuccess` - 指示状态码是否位于成功范围内.
*   `reviver` - 用于转换原始响应`response` 内容的函数.
*   `requestMessage` - 对原始请求消息的引用。
*   
>#### JSON By Default
> 默认情况下，`HttpClient`假定您期望使用JSON responseType。

### Configuration 配置

您可以使用`configure`访问一个连贯api来配置客户机发送的所有请求。您还可以使用`createRequest`自定义配置单个请求。下面是一个配置的例子

**Configuring Basic HTTP**

    

``` JavaScript
  import {HttpClient} from 'aurelia-http-client';
  
  let httpClient = new HttpClient()
    .configure(x => {
      x.withBaseUrl('http://aurelia.io');
      x.withHeader('Authorization', 'bearer 123');
    });
  
  httpClient.get('some/cool/path');
```
 在这种情况下，来自客户端的所有请求都将具有“http://aurelia.io” 的基本URL，并具有指定的`Authorization`标头。 `RequestBuilder`提供相同的API。 因此，您可以在单个请求上完成相同的操作，如下所示：

**RequestBuilder API**

    

``` JavaScript
  import {HttpClient} from 'aurelia-http-client';
  
  let httpClient = new HttpClient();
  
  httpClient.createRequest('some/cool/path')
    .asGet()
    .withBaseUrl('http://aurelia.io')
    .withHeader('Authorization', 'bearer 123')
    .withParams({ abc: '123' })
    .send();
```

  The fluent API has the following chainable methods: `asDelete()`, `asGet()`, `asHead()`, `asOptions()`, `asPatch()`, `asPost()`, `asPut()`, `asJsonp()`, `withUrl()`, `withBaseUrl()`, `withContent()`, `withParams()`, `withResponseType()`, `withTimeout()`, `withHeader()`, `withCredentials()`, `withReviver()`, `withReplacer()`, `withProgressCallback()`, and `withCallbackParameterName()`.
  
  也可以使用拦截器连接到请求和响应。这里有一个例子:

**Basic HTTP Interceptors**

    

``` JavaScript
  import {HttpClient} from 'aurelia-http-client';
  
  let httpClient = new HttpClient();
  
  httpClient.configure(x => {
      x.withInterceptor({
        request(message) {
          return message;
        },
  
        requestError(error) {
          throw error;
        },
  
        response(message) {
          return message;
        },
  
        responseError(error) {
          throw error;
        }
      });
    });
  

  
```

与客户端一起使用的所有拦截器都形成一个链。拦截方法的返回值作为参数传递给下一个方法。拦截器是按照它们被添加的顺序调用的。有关拦截器的更多信息，请参阅`aurelia-fetch-client`拦截器文档。