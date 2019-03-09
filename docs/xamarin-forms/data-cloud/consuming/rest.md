---
title: 使用 RESTful Web 服务
description: 将 web 服务集成到应用程序是一种常见方案。 本文演示如何使用 Xamarin.Forms 应用程序中的 RESTful web 服务。
ms.prod: xamarin
ms.assetid: B540910C-9C51-416A-AAB9-057BF76489C3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/22/2017
ms.openlocfilehash: 840dee3213bc117cff82fe52b094dc71f343dcd1
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57668187"
---
# <a name="consuming-a-restful-web-service"></a>使用 RESTful Web 服务

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoREST/)

_将 web 服务集成到应用程序是一种常见方案。本文演示如何使用 Xamarin.Forms 应用程序中的 RESTful web 服务。_

具象状态传输 (REST) 是用于构建 web 服务的体系结构样式。 REST 请求是通过发出 HTTP 使用 web 浏览器使用检索 web 页面，并将数据发送到服务器的同一 HTTP 谓词。 谓词是：

- **获取**– 此操作用于从 web 服务中检索数据。
- **开机自检**– 此操作用于 web 服务上创建新项的数据。
- **放置**– 此操作用于更新 web 服务上的数据的项。
- **修补程序**– 此操作用于通过一组指令描述有关应如何修改此项更新 web 服务上的数据的项。 在示例应用程序不使用此谓词。
- **删除**– 此操作用于删除 web 服务上的数据的项。

遵守 REST 的 Api 被称为 RESTful Api，并使用定义的 web 服务：

- 一个基 URI。
- HTTP 方法，如 GET、 POST、 PUT、 PATCH 或 DELETE。
- 数据，如 JavaScript 对象表示法 (JSON) 媒体类型。

RESTful web 服务通常使用 JSON 消息来将数据返回到客户端。 JSON 是一种基于文本的数据交换格式生成 compact 负载，这会导致降低带宽需求，发送数据时。 示例应用程序使用开放源代码[NewtonSoft JSON.NET 库](http://www.newtonsoft.com/json)进行序列化和反序列化消息。

REST 的简单性已帮助使其用于访问移动应用程序中的 web 服务的主要方法。

设置 REST 服务的说明可以找到示例应用程序附带的自述文件中。 但是，当运行示例应用程序时，它将连接到一个 Xamarin 托管 REST 服务，允许只读访问的数据，如以下屏幕截图中所示：

![](rest-images/portal.png "示例应用程序")

> [!NOTE]
> 在 iOS 9 及更高版本中，应用传输安全 (ATS) 强制执行安全连接之间 （例如应用程序的后端服务器） 的 internet 资源和应用程序中，从而防止意外泄露敏感信息。 由于默认情况下构建适用于 iOS 9 应用程序中启用了 ATS，所有连接都将遵循 ATS 安全要求。 如果连接不能满足这些要求，它们将失败并出现异常。
>
>如果不能使用 ATS 可以选择退出的**HTTPS**协议并确保对 internet 资源的通信安全。 这可以通过更新应用程序的实现**Info.plist**文件。 有关详细信息请参阅[应用程序传输安全](~/ios/app-fundamentals/ats.md)。

## <a name="consuming-the-web-service"></a>使用 Web 服务

REST 服务 ASP.NET Core 中编写，并提供以下操作：

|操作|HTTP 方法|相对 URI|参数|
|--- |--- |--- |--- |
|获取待办事项的列表|GET|/ api/todoitems /|
|创建新的待办事项|发布|/ api/todoitems /|JSON 格式的 TodoItem|
|更新待办事项|PUT|/ api/todoitems /|JSON 格式的 TodoItem|
|删除待办事项|DELETE|/ api/todoitems / {id}|

大多数的 uri 包含`TodoItem`路径中的 ID。 例如，若要删除`TodoItem`其 ID 是`6bb8a868-dba1-4f1a-93b7-24ebce87e243`，客户端发送 DELETE 请求到`http://hostname/api/todoitems/6bb8a868-dba1-4f1a-93b7-24ebce87e243`。 有关示例应用程序中使用的数据模型的详细信息，请参阅[为数据建模](~/xamarin-forms/data-cloud/walkthrough.md)。

当收到请求时的 Web API 框架将请求路由到某个操作。 这些操作是中的只是公共方法`TodoItemsController`类。 该框架使用路由表来确定要调用以响应请求，下面的代码示例中所示的操作：

```csharp
config.Routes.MapHttpRoute(
    name: "TodoItemsApi",
    routeTemplate: "api/{controller}/{id}",
    defaults: new { controller="todoitems", id = RouteParameter.Optional }
);
```

路由表包含的路由模板和 Web API 框架接收 HTTP 请求时，它尝试匹配对路由表中的路由模板的 URI。 如果匹配不能发现路由客户端收到 404 （未找到） 错误。 如果找到匹配的路由，则 Web API 选择控制器和操作，如下所示：

- 若要查找控制器，Web API 向"controller"的值 *{controller}* 变量。
- 若要查找操作，Web API 的 HTTP 方法查看，并探讨使用相同的 HTTP 方法作为特性修饰的控制器操作。
- *{Id}* 占位符变量映射到操作参数。

REST 服务使用基本身份验证。 有关详细信息请参阅[RESTful web 服务进行身份验证](~/xamarin-forms/data-cloud/authentication/rest.md)。 有关 ASP.NET Web API 路由的详细信息，请参阅[ASP.NET Web API 中的路由](http://www.asp.net/web-api/overview/web-api-routing-and-actions/routing-in-aspnet-web-api)ASP.NET 网站上。 有关生成使用 ASP.NET Core 的 REST 服务的详细信息，请参阅[创建本机移动应用程序的后端服务](/aspnet/core/mobile/native-mobile-backend/)。

> [!NOTE]
> 示例应用程序使用 Xamarin 托管 REST 服务，提供对 web 服务的只读访问。 因此，创建、 更新和删除数据的操作不会更改应用程序中使用的数据。 但是，REST 服务的可承载版本现已推出**TodoRESTService**文件夹中附带[示例代码](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoREST/)。
> 如果您自己托管 REST 服务，它允许完全创建、 更新、 读取和删除对数据的访问。

`HttpClient`类用于发送和接收通过 HTTP 请求。 它提供的功能用于发送 HTTP 请求和接收 HTTP 响应从 URI 标识的资源。 每个请求将作为异步操作发送。 有关异步操作的详细信息，请参阅[异步支持概述](~/cross-platform/platform/async.md)。

`HttpResponseMessage`类表示 HTTP 响应消息进行 HTTP 请求之后收到来自 web 服务。 它包含有关响应，包括状态代码、 标头和任何主体的信息。 `HttpContent`类表示的 HTTP 正文和内容标头，如`Content-Type`和`Content-Encoding`。 可以读取内容，使用任一`ReadAs`方法，如`ReadAsStringAsync`和`ReadAsByteArrayAsync`根据数据的格式。

### <a name="creating-the-httpclient-object"></a>创建 HTTPClient 对象

`HttpClient`实例在类级别声明的以便在对象存在的只要该应用程序需要发出 HTTP 请求，如下面的代码示例中所示：

```csharp
public class RestService : IRestService
{
  HttpClient client;
  ...

  public RestService ()
  {
    client = new HttpClient ();
    client.MaxResponseContentBufferSize = 256000;
  }
  ...
}
```

`HttpClient.MaxResponseContentBufferSize`属性用于指定最大读取的 HTTP 响应消息中的内容时要缓冲的字节数。 此属性的默认大小是一个整数的最大大小。 因此，该属性设置为较小的值，作为安全措施，以限制的应用程序将接受作为 web 服务的响应的数据量。

### <a name="retrieving-data"></a>检索数据

`HttpClient.GetAsync`方法用于将 GET 请求发送到由 URI，指定的 web 服务，然后接收响应来自 web 服务，如下面的代码示例中所示：

```csharp
public async Task<List<TodoItem>> RefreshDataAsync ()
{
  ...
  // RestUrl = https://developer.xamarin.com:8081/api/todoitems/
  var uri = new Uri (string.Format (Constants.RestUrl, string.Empty));
  ...
  var response = await client.GetAsync (uri);
  if (response.IsSuccessStatusCode) {
      var content = await response.Content.ReadAsStringAsync ();
      Items = JsonConvert.DeserializeObject <List<TodoItem>> (content);
  }
  ...
}
```

REST 服务中发送的 HTTP 状态代码`HttpResponseMessage.IsSuccessStatusCode`属性，以指示 HTTP 请求是成功还是失败。 此操作的 REST 服务在响应中，它指示请求成功，且请求的信息包含在响应中发送 HTTP 状态代码 200 （正常）。

如果 HTTP 操作是否成功，读取将响应的内容时，用于显示。 `HttpResponseMessage.Content`属性表示 HTTP 响应的内容和`HttpContent.ReadAsStringAsync`方法以异步方式将 HTTP 内容写入字符串。 此内容然后从 JSON 到转换`List`的`TodoItem`实例。

### <a name="creating-data"></a>创建数据

`HttpClient.PostAsync`方法用于将 POST 请求发送到由 URI，指定的 web 服务，然后接收来自 web 服务的响应，如下面的代码示例中所示：

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  // RestUrl = https://developer.xamarin.com:8081/api/todoitems/
  var uri = new Uri (string.Format (Constants.RestUrl, string.Empty));

  ...
  var json = JsonConvert.SerializeObject (item);
  var content = new StringContent (json, Encoding.UTF8, "application/json");

  HttpResponseMessage response = null;
  if (isNewItem) {
    response = await client.PostAsync (uri, content);
  }
  ...

  if (response.IsSuccessStatusCode) {
    Debug.WriteLine (@"                TodoItem successfully saved.");

  }
  ...
}
```

`TodoItem`实例转换为发送到 web 服务的 JSON 有效负载。 此有效负载然后嵌入在正文中将发送到 web 服务中之前与发出请求, 的 HTTP 内容`PostAsync`方法。

REST 服务中发送的 HTTP 状态代码`HttpResponseMessage.IsSuccessStatusCode`属性，以指示 HTTP 请求是成功还是失败。 此操作通常的反应是：

- **201 （已创建）** – 该请求导致已发送响应之前正在创建新资源。
- **400 （错误请求）** – 请求不理解的服务器。
- **409 （冲突）** – 请求可能不会执行由于在服务器上的冲突。

### <a name="updating-data"></a>更新数据

`HttpClient.PutAsync`方法用于将 PUT 请求发送到由 URI，指定的 web 服务，然后接收响应来自 web 服务，如下面的代码示例中所示：

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  ...
  response = await client.PutAsync (uri, content);
  ...
}
```
操作`PutAsync`方法等同于`PostAsync`用于在 web 服务中创建数据的方法。 但是，从 web 服务发送的可能响应不同。

REST 服务中发送的 HTTP 状态代码`HttpResponseMessage.IsSuccessStatusCode`属性，以指示 HTTP 请求是成功还是失败。 此操作通常的反应是：

- **204 （无内容）** – 成功处理该请求，响应是有意留为空白。
- **400 （错误请求）** – 请求不理解的服务器。
- **404 （找不到）** – 服务器上不存在请求的资源。

### <a name="deleting-data"></a>删除数据

`HttpClient.DeleteAsync`方法用于将 DELETE 请求发送到由 URI，指定的 web 服务，然后接收响应来自 web 服务，如下面的代码示例中所示：

```csharp
public async Task DeleteTodoItemAsync (string id)
{
  // RestUrl = https://developer.xamarin.com:8081/api/todoitems/{0}
  var uri = new Uri (string.Format (Constants.RestUrl, id));
  ...
  var response = await client.DeleteAsync (uri);
  if (response.IsSuccessStatusCode) {
    Debug.WriteLine (@"                TodoItem successfully deleted.");
  }
  ...
}
```

REST 服务中发送的 HTTP 状态代码`HttpResponseMessage.IsSuccessStatusCode`属性，以指示 HTTP 请求是成功还是失败。 此操作通常的反应是：

- **204 （无内容）** – 成功处理该请求，响应是有意留为空白。
- **400 （错误请求）** – 请求不理解的服务器。
- **404 （找不到）** – 服务器上不存在请求的资源。

## <a name="summary"></a>总结

本文介绍了如何使用 Xamarin.Forms 应用程序中的 RESTful web 服务使用`HttpClient`类。 REST 的简单性已帮助使其用于访问移动应用程序中的 web 服务的主要方法。


## <a name="related-links"></a>相关链接

- [为本机移动应用程序创建后端服务](/aspnet/core/mobile/native-mobile-backend/)
- [TodoREST （示例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoREST/)
- [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx)
