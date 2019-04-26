---
title: 对 RESTful Web 服务进行身份验证
description: 基本身份验证提供给具有正确的凭据这些客户端对资源的访问。 本文介绍如何使用基本身份验证保护 RESTful web 服务资源的访问权限。
ms.prod: xamarin
ms.assetid: 7B5FFDC4-F2AA-4B12-A30A-1DACC7FECBF1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/22/2018
ms.openlocfilehash: d3f07a72ee26d6be4fafa72137dc9b6c3a724e00
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61330630"
---
# <a name="authenticating-a-restful-web-service"></a>对 RESTful Web 服务进行身份验证

_HTTP 支持使用多个身份验证机制来控制对资源的访问。基本身份验证提供给具有正确的凭据这些客户端对资源的访问。本文演示如何使用基本身份验证保护 RESTful web 服务资源的访问权限。_

> [!NOTE]
> 在 iOS 9 及更高版本中，应用传输安全 (ATS) 强制执行安全连接之间 （例如应用程序的后端服务器） 的 internet 资源和应用程序中，从而防止意外泄露敏感信息。 由于默认情况下构建适用于 iOS 9 应用程序中启用了 ATS，所有连接都将遵循 ATS 安全要求。 如果连接不能满足这些要求，它们将失败并出现异常。
> 如果不能使用 ATS 可以选择退出的`HTTPS`协议并确保对 internet 资源的通信安全。 这可以通过更新应用程序的实现**Info.plist**文件。 有关详细信息请参阅[应用程序传输安全](~/ios/app-fundamentals/ats.md)。

## <a name="authenticating-users-over-http"></a>用户通过 HTTP 进行身份验证

基本身份验证是 HTTP，支持的最简单的身份验证机制，并涉及到客户端发送的用户名和密码以未加密的 base64 编码文本。 其工作原理，如下所示：

- 如果 web 服务接收受保护资源的请求，它将拒绝该请求的 HTTP 状态代码 401 （拒绝访问），并设置 Www-authenticate 响应标头，如以下关系图中所示：

![](rest-images/basic-authentication-fail.png "基本身份验证失败")

- 如果 web 服务接收受保护资源的请求与`Authorization`正确设置标头，web 服务进行响应的 HTTP 状态代码 200，这表示请求成功和所需的信息是在响应中。 这种情况下在下图中所示：

![](rest-images/basic-authentication-success.png "基本身份验证成功")

> [!NOTE]
> 只应通过 HTTPS 连接使用基本身份验证。 当通过 HTTP 连接，使用<code>Authorization</code>标头很容易被解码，如果攻击者捕获的 HTTP 流量。

## <a name="specifying-basic-authentication-in-a-web-request"></a>在 Web 请求中指定基本身份验证

使用基本身份验证，如下所示指定：

1. 添加到"基本"的字符串`Authorization`的请求标头。
1. 用户名和密码将合并为一个字符串，格式为"用户名： 密码"，然后是 base64 编码，并且添加到`Authorization`的请求标头。

因此，使用 XamarinUser 的用户名和密码为 XamarinPassword，在标头变为：

```csharp
Authorization: Basic WGFtYXJpblVzZXI6WGFtYXJpblBhc3N3b3Jk
```

`HttpClient`该类可以设置`Authorization`标头值`HttpClient.DefaultRequestHeaders.Authorization`属性。 因为`HttpClient`实例存在多个请求间`Authorization`标头只需设置一次，而不是何时进行每个请求，如下面的代码示例中所示：

```csharp
public class RestService : IRestService
{
  HttpClient _client;
  ...

  public RestService ()
  {
    var authData = string.Format ("{0}:{1}", Constants.Username, Constants.Password);
    var authHeaderValue = Convert.ToBase64String (Encoding.UTF8.GetBytes (authData));

    _client = new HttpClient ();
    _client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue ("Basic", authHeaderValue);
  }
  ...
}
```

然后当请求时，web 服务操作使用签名请求`Authorization`标头，指示用户是否有权调用该操作。

> [!NOTE]
> 尽管此代码将凭据存储为常量，它们不应存储发布的应用程序中以不安全的格式。 [Xamarith.Auth](https://www.nuget.org/packages/Xamarin.Auth/) NuGet 提供了用于将凭据安全地存储功能。 有关详细信息请参阅[存储和检索设备上的帐户信息](~/xamarin-forms/data-cloud/authentication/oauth.md)。

## <a name="processing-the-authorization-header-server-side"></a>处理授权标头服务器端

REST 服务应修饰每个操作具有`[BasicAuthentication]`属性。 此属性用于分析`Authorization`标头，并确定 base64 编码凭据是否有效由将其与存储中的值进行比较*Web.config*。虽然这种方法适合于示例服务，它需要面向公众的 web 服务扩展。

使用 IIS 的基本身份验证模块，在用户进行身份验证针对其 Windows 凭据。 因此，用户必须在服务器的域上拥有帐户。 但是，可以配置基本身份验证模型，以允许自定义身份验证的用户帐户进行身份验证对外部源，例如数据库。 有关详细信息请参阅[ASP.NET Web API 中的基本身份验证](http://www.asp.net/web-api/overview/security/basic-authentication)ASP.NET 网站上。

> [!NOTE]
> 基本身份验证不用于管理日志记录。因此，注销的标准基本身份验证方法是以结束会话。

## <a name="related-links"></a>相关链接

- [使用 RESTful web 服务](~/xamarin-forms/data-cloud/consuming/rest.md)
- [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx)
