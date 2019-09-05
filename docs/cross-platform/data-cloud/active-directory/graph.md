---
title: 访问图形 API
description: 本文档介绍如何向使用 Xamarin 生成的移动应用程序添加 Azure Active Directory 身份验证。
ms.prod: xamarin
ms.assetid: F94A9FF4-068E-4B71-81FE-46920745380D
author: conceptdev
ms.author: crdun
ms.date: 03/23/2017
ms.openlocfilehash: 96e0991bb0805e61dfbf91e8479cbf1c9943f212
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70287750"
---
# <a name="accessing-the-graph-api"></a>访问图形 API

请按照以下步骤在 Xamarin 应用程序中使用图形 API：

1. 在*windowsazure.com*门户中[注册 Azure Active Directory](~/cross-platform/data-cloud/active-directory/get-started/register.md) ，然后
2. [配置服务](~/cross-platform/data-cloud/active-directory/get-started/configure.md)。

## <a name="step-3-adding-active-directory-authentication-to-an-app"></a>步骤 3。 向应用程序添加 Active Directory 身份验证

在应用程序中，使用 Visual Studio 中的 NuGet 包管理器或 Visual Studio for Mac 在**Azure Active Directory 身份验证库（AZURE ADAL）** 中添加引用。
请确保选择 "**显示预发行包**" 以包含此包，因为它仍处于预览阶段。

> [!IMPORTANT]
> 注意:Azure ADAL 3.0 当前为预览版，最终版本发布之前可能会有重大更改。 


![](graph-images/06.-adal-nuget-package.jpg "将引用添加到 Azure Active Directory 身份验证库（Azure ADAL）")

在您的应用程序中，您现在需要添加身份验证流所需的以下类级别的变量。

```csharp
//Client ID
public static string clientId = "25927d3c-.....-63f2304b90de";
public static string commonAuthority = "https://login.windows.net/common"
//Redirect URI
public static Uri returnUri = new Uri("http://xam-demo-redirect");
//Graph URI if you've given permission to Azure Active Directory
const string graphResourceUri = "https://graph.windows.net";
public static string graphApiVersion = "2013-11-08";
//AuthenticationResult will hold the result after authentication completes
AuthenticationResult authResult = null;
```

此处需要注意的一点是`commonAuthority`。 当身份验证终结点`common`为时，你的应用将成为**多租户**，这意味着任何用户都可以使用其 Active Directory 凭据的登录名。 进行身份验证后，该用户将在自己 Active Directory 的上下文中工作，即，他们将看到与他 Active Directory 相关的详细信息。

### <a name="write-method-to-acquire-access-token"></a>用于获取访问令牌的写入方法

以下代码（适用于 Android）将启动身份验证，完成后，将结果`authResult`分配到。 Ios 和 Windows Phone 实现略微不同：第二个参数（`Activity`）在 iOS 上不同，在 Windows Phone 上不存在。

```csharp
public static async Task<AuthenticationResult> GetAccessToken
            (string serviceResourceId, Activity activity)
{
    authContext = new AuthenticationContext(Authority);
    if (authContext.TokenCache.ReadItems().Count() > 0)
        authContext = new AuthenticationContext(authContext.TokenCache.ReadItems().First().Authority);
    var authResult = await authContext.AcquireTokenAsync(serviceResourceId, clientId, returnUri, new AuthorizationParameters(activity));
    return authResult;
}  
```

在上面的代码中， `AuthenticationContext`负责 commonAuthority 的身份验证。 它有一个`AcquireTokenAsync`方法，该方法将参数作为需要访问的资源，在本例中`graphResourceUri` `clientId`为、和`returnUri`。 身份验证完成`returnUri`后，应用将返回到。 对于所有平台，此代码将保持不变，但是，最后一个参数`AuthorizationParameters`将在不同的平台上不同，并负责管理身份验证流。

对于 Android 或 iOS，我们将`this` `AuthorizationParameters(this)`参数传递给以共享上下文，而在 Windows 中，不将任何参数作为新`AuthorizationParameters()`的传递。

### <a name="handle-continuation-for-android"></a>处理 Android 的继续符

身份验证完成后，流应返回到应用。 对于 Android，将通过以下代码来处理它：应将其添加到**MainActivity.cs**：


```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
  base.OnActivityResult(requestCode, resultCode, data);
  AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
}
```

### <a name="handle-continuation-for-windows-phone"></a>处理 Windows Phone 的继续符

对于 Windows Phone 用以下`OnActivated`代码修改**App.xaml.cs**文件中的方法：

```csharp
protected override void OnActivated(IActivatedEventArgs args)
{
#if WINDOWS_PHONE_APP
  if (args is IWebAuthenticationBrokerContinuationEventArgs)
  {
     WebAuthenticationBrokerContinuationHelper.SetWebAuthenticationBrokerContinuationEventArgs(args as IWebAuthenticationBrokerContinuationEventArgs);
  }
#endif
  base.OnActivated(args);
}
```

现在，如果你运行该应用程序，你应该会看到一个身份验证对话框。
身份验证成功后，它将要求你拥有访问资源的权限（在我们的示例中图形 API）：

![](graph-images/08.-authentication-flow.jpg "身份验证成功后，它将要求你的权限访问我们的示例中的资源图形 API")

如果身份验证成功，并且您已授权该应用程序访问资源，则应该获取`AccessToken`并`RefreshToken`组合入`authResult`。 这些令牌是进一步的 API 调用所必需的，并且是在幕后 Azure Active Directory 的授权。

![](graph-images/07.-access-token-for-authentication.jpg "这些令牌是进一步的 API 调用所必需的，用于在幕后 Azure Active Directory 进行授权")

例如，以下代码允许你从 Active Directory 获取用户列表。 你可以将 Web API URL 替换为受 Azure AD 保护的 Web API。

```csharp
var client = new HttpClient();
var request = new HttpRequestMessage(HttpMethod.Get,
    "https://graph.windows.net/tendulkar.onmicrosoft.com/users?api-version=2013-04-05");
request.Headers.Authorization =
  new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
var response = await client.SendAsync(request);
var content = await response.Content.ReadAsStringAsync();
```

