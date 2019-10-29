---
title: 使用 RESTful Web 服务
description: 将 web 服务集成到应用程序是一种常见方案。 本文演示如何从 Xamarin 应用程序使用 RESTful web 服务。
ms.prod: xamarin
ms.assetid: B540910C-9C51-416A-AAB9-057BF76489C3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/22/2018
ms.openlocfilehash: 6ccbb24be8c03d634c884853fb0ec339d49cf49d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029550"
---
# <a name="consume-a-restful-web-service"></a>使用 RESTful Web 服务

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todorest)

_将 web 服务集成到应用程序是一种常见方案。本文演示如何从 Xamarin 应用程序使用 RESTful web 服务。_

具象状态传输（REST）是用于构建 web 服务的体系结构样式。 REST 请求是通过 HTTP 使用 web 浏览器用于检索网页并将数据发送到服务器的 HTTP 谓词来完成的。 谓词包括：

- **GET** –此操作用于从 web 服务检索数据。
- **POST** –此操作用于在 web 服务中创建新的数据项。
- **PUT** –此操作用于更新 web 服务上的数据项。
- **修补程序**–此操作用于通过描述有关应如何修改项的一组说明来更新 web 服务上的数据项。 此谓词不在示例应用程序中使用。
- **删除**–此操作用于删除 web 服务上的数据项。

遵循 REST 的 Web 服务 Api 称为 RESTful Api，使用定义：

- 基 URI。
- HTTP 方法，例如 GET、POST、PUT、PATCH 或 DELETE。
- 数据的媒体类型，如 JavaScript 对象表示法（JSON）。

RESTful web 服务通常使用 JSON 消息将数据返回到客户端。 JSON 是一种基于文本的数据交换格式，可产生精简的负载，从而降低发送数据时的带宽需求。 示例应用程序使用开源[newtonsoft.json JSON.NET 库](https://www.newtonsoft.com/json)对消息进行序列化和反序列化。

REST 的简单性有助于使其成为在移动应用程序中访问 web 服务的主要方法。

运行示例应用程序时，它将连接到本地托管的 REST 服务，如以下屏幕截图所示：

![](rest-images/portal.png "Sample Application")

> [!NOTE]
> 在 iOS 9 及更高版本中，应用传输安全（ATS）在 internet 资源（如应用的后端服务器）和应用之间强制实施安全连接，从而防止意外泄漏敏感信息。 由于默认情况下在为 iOS 9 构建的应用中启用了 ATS，因此所有连接都将受到 ATS 的安全要求。 如果连接不满足这些要求，它们将失败并出现异常。
>
>如果无法对 internet 资源使用**HTTPS**协议和安全通信，则可以选择不使用 ATS。 这可以通过更新应用的**info.plist**文件来实现。 有关详细信息，请参阅[应用传输安全性](~/ios/app-fundamentals/ats.md)。

## <a name="consuming-the-web-service"></a>使用 Web 服务

REST 服务使用 ASP.NET Core 编写，并提供以下操作：

|操作|HTTP 方法|相对 URI|参数|
|--- |--- |--- |--- |
|获取待办事项的列表|GET|/api/todoitems/|
|创建新的待办事项|发布|/api/todoitems/|JSON 格式的 TodoItem|
|更新待办事项|准备|/api/todoitems/|JSON 格式的 TodoItem|
|删除待办事项|DELETE|/api/todoitems/{id}|

大多数 Uri 包含路径中的 `TodoItem` ID。 例如，若要删除 ID 为 `6bb8a868-dba1-4f1a-93b7-24ebce87e243`的 `TodoItem`，则客户端会将删除请求发送到 `http://hostname/api/todoitems/6bb8a868-dba1-4f1a-93b7-24ebce87e243`。 有关示例应用程序中使用的数据模型的详细信息，请参阅[对数据进行建模](~/xamarin-forms/data-cloud/web-services/introduction.md)。

当 Web API 框架收到请求时，它会将请求路由到某个操作。 这些操作只是 `TodoItemsController` 类中的公共方法。 框架使用路由表来确定要调用哪个操作来响应请求，如下面的代码示例所示：

```csharp
config.Routes.MapHttpRoute(
    name: "TodoItemsApi",
    routeTemplate: "api/{controller}/{id}",
    defaults: new { controller="todoitems", id = RouteParameter.Optional }
);
```

路由表包含路由模板，当 Web API 框架收到 HTTP 请求时，它会尝试将 URI 与路由表中的路由模板匹配。 如果找不到匹配的路由，则客户端收到404（未找到）错误。 如果找到匹配的路由，Web API 将按如下所示选择控制器和操作：

- 若要查找控制器，Web API 会将 "控制器" 添加到 *{controller}* 变量的值。
- 为了查找操作，Web API 查看 HTTP 方法，并查看使用与属性相同的 HTTP 方法修饰的控制器操作。
- *{Id}* 占位符变量映射到操作参数。

REST 服务使用基本身份验证。 有关详细信息，请参阅对[RESTful web 服务进行身份验证](~/xamarin-forms/data-cloud/authentication/rest.md)。 有关 ASP.NET Web API 路由的详细信息，请参阅 ASP.NET 网站上[ASP.NET Web API 中的路由](https://www.asp.net/web-api/overview/web-api-routing-and-actions/routing-in-aspnet-web-api)。 有关使用 ASP.NET Core 生成 REST 服务的详细信息，请参阅[为本机移动应用程序创建后端服务](/aspnet/core/mobile/native-mobile-backend/)。

`HttpClient` 类用于通过 HTTP 发送和接收请求。 它提供用于发送 HTTP 请求以及从 URI 标识的资源接收 HTTP 响应的功能。 每个请求都作为异步操作发送。 有关异步操作的详细信息，请参阅[异步支持概述](~/cross-platform/platform/async.md)。

`HttpResponseMessage` 类表示发出 HTTP 请求之后从 web 服务接收的 HTTP 响应消息。 它包含有关响应的信息，包括状态代码、标头和任何正文。 `HttpContent` 类表示 HTTP 正文和内容标头，如 `Content-Type` 和 `Content-Encoding`。 可以使用任何 `ReadAs` 方法读取内容，例如 `ReadAsStringAsync` 和 `ReadAsByteArrayAsync`，具体取决于数据的格式。

### <a name="creating-the-httpclient-object"></a>创建 HTTPClient 对象

`HttpClient` 实例在类级声明，以便在应用程序需要发出 HTTP 请求时，该对象的生存时间，如以下代码示例所示：

```csharp
public class RestService : IRestService
{
  HttpClient _client;
  ...

  public RestService ()
  {
    _client = new HttpClient ();
  }
  ...
}
```

### <a name="retrieving-data"></a>检索数据

`HttpClient.GetAsync` 方法用于将 GET 请求发送到由 URI 指定的 web 服务，然后接收来自 web 服务的响应，如下面的代码示例所示：

```csharp
public async Task<List<TodoItem>> RefreshDataAsync ()
{
  ...
  var uri = new Uri (string.Format (Constants.TodoItemsUrl, string.Empty));
  ...
  var response = await _client.GetAsync (uri);
  if (response.IsSuccessStatusCode)
  {
      var content = await response.Content.ReadAsStringAsync ();
      Items = JsonConvert.DeserializeObject <List<TodoItem>> (content);
  }
  ...
}
```

REST 服务发送 `HttpResponseMessage.IsSuccessStatusCode` 属性中的 HTTP 状态代码，以指示 HTTP 请求是成功还是失败。 对于此操作，REST 服务会在响应中发送 HTTP 状态代码200（正常），这表明请求已成功，并且请求的信息在响应中。

如果 HTTP 操作成功，将读取响应的内容以供显示。 `HttpResponseMessage.Content` 属性表示 HTTP 响应的内容，`HttpContent.ReadAsStringAsync` 方法将 HTTP 内容异步写入字符串。 然后，将此内容从 JSON 转换为 `TodoItem` 实例 `List`。

### <a name="creating-data"></a>创建数据

`HttpClient.PostAsync` 方法用于将 POST 请求发送到 URI 指定的 web 服务，然后接收来自 web 服务的响应，如下面的代码示例所示：

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  var uri = new Uri (string.Format (Constants.TodoItemsUrl, string.Empty));

  ...
  var json = JsonConvert.SerializeObject (item);
  var content = new StringContent (json, Encoding.UTF8, "application/json");

  HttpResponseMessage response = null;
  if (isNewItem)
  {
    response = await _client.PostAsync (uri, content);
  }
  ...

  if (response.IsSuccessStatusCode)
  {
    Debug.WriteLine (@"\tTodoItem successfully saved.");

  }
  ...
}
```

`TodoItem` 实例将转换为 JSON 有效负载以便发送到 web 服务。 然后，此负载将嵌入到 HTTP 内容的正文中，此负载将在用 `PostAsync` 方法发出请求之前发送到 web 服务。

REST 服务发送 `HttpResponseMessage.IsSuccessStatusCode` 属性中的 HTTP 状态代码，以指示 HTTP 请求是成功还是失败。 此操作的常见响应为：

- **201 （已创建）** -请求导致在发送响应之前创建新资源。
- **400 （错误的请求）** –服务器不理解该请求。
- **409 （冲突）** –由于服务器上的冲突而未能执行请求。

### <a name="updating-data"></a>更新数据

`HttpClient.PutAsync` 方法用于将 PUT 请求发送到由 URI 指定的 web 服务，然后接收来自 web 服务的响应，如下面的代码示例所示：

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  ...
  response = await _client.PutAsync (uri, content);
  ...
}
```

`PutAsync` 方法的操作与用于在 web 服务中创建数据的 `PostAsync` 方法相同。 但是，从 web 服务发送的可能响应会有所不同。

REST 服务发送 `HttpResponseMessage.IsSuccessStatusCode` 属性中的 HTTP 状态代码，以指示 HTTP 请求是成功还是失败。 此操作的常见响应为：

- **204 （无内容）** –请求已成功处理，并且响应有意为空。
- **400 （错误的请求）** –服务器不理解该请求。
- **404 （未找到）** –请求的资源在服务器上不存在。

### <a name="deleting-data"></a>删除数据

`HttpClient.DeleteAsync` 方法用于将删除请求发送到由 URI 指定的 web 服务，然后接收来自 web 服务的响应，如下面的代码示例所示：

```csharp
public async Task DeleteTodoItemAsync (string id)
{
  var uri = new Uri (string.Format (Constants.TodoItemsUrl, id));
  ...
  var response = await _client.DeleteAsync (uri);
  if (response.IsSuccessStatusCode)
  {
    Debug.WriteLine (@"\tTodoItem successfully deleted.");
  }
  ...
}
```

REST 服务发送 `HttpResponseMessage.IsSuccessStatusCode` 属性中的 HTTP 状态代码，以指示 HTTP 请求是成功还是失败。 此操作的常见响应为：

- **204 （无内容）** –请求已成功处理，并且响应有意为空。
- **400 （错误的请求）** –服务器不理解该请求。
- **404 （未找到）** –请求的资源在服务器上不存在。

## <a name="related-links"></a>相关链接

- [为本机移动应用程序创建后端服务](/aspnet/core/mobile/native-mobile-backend/)
- [TodoREST（示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todorest)
- [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx)
