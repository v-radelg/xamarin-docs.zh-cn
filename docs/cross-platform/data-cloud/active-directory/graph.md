---
title: 访问图形 API
description: 本文档介绍如何将 Azure Active Directory 身份验证添加到使用 Xamarin 生成移动应用程序。
ms.prod: xamarin
ms.assetid: F94A9FF4-068E-4B71-81FE-46920745380D
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: c43dfa79831f22e55490b27c3c360602ae717627
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61189932"
---
# <a name="accessing-the-graph-api"></a>访问图形 API

请按照下列步骤以使用 Graph API 从 Xamarin 应用程序中：

1. [与 Azure Active Directory 注册](~/cross-platform/data-cloud/active-directory/get-started/register.md)上*windowsazure.com*门户，然后
2. [配置服务](~/cross-platform/data-cloud/active-directory/get-started/configure.md)。

## <a name="step-3-adding-active-directory-authentication-to-an-app"></a>步骤 3。 向应用添加 Active Directory 身份验证

在应用程序中，添加对引用**Azure Active Directory 身份验证库 (Azure ADAL)** 使用 NuGet 包管理器在 Visual Studio 或 Visual Studio for mac。
请确保选择**显示预发行包**以包含此包，因为它仍处于预览状态。

> [!IMPORTANT]
> 注意:Azure ADAL 3.0 当前为预览版，最终版本发布之前可能是重大更改。 


![](graph-images/06.-adal-nuget-package.jpg "添加对 Azure Active Directory 身份验证库 (Azure ADAL) 的引用")

应用程序中，现在需要添加以下类级变量所需的身份验证流。

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

一件事要注意的一点是`commonAuthority`。 身份验证终结点何时`common`，您的应用程序变得**多租户**，这意味着任何用户使用其 Active Directory 凭据可以使用登录名。 身份验证后，该用户将在他们自己的 Active Directory 的上下文上工作 – 即，他们将看到其 Active Directory 与相关详细信息。

### <a name="write-method-to-acquire-access-token"></a>编写方法来获取访问令牌

下面的代码 （适用于 Android) 将启动身份验证并完成后将分配中的结果`authResult`。 IOS 和 Windows Phone 实现稍有不同： 第二个参数 (`Activity`) 是在 iOS 上不同的不存在 Windows Phone 上。

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

在上面的代码，`AuthenticationContext`负责 commonAuthority 进行身份验证。 它具有`AcquireTokenAsync`方法，将参数作为资源，需要对其进行访问，在这种情况下`graphResourceUri`， `clientId`，和`returnUri`。 应用程序将返回到`returnUri`身份验证完成时。 此代码将保持不变，则对于所有平台，但是，最后一个参数， `AuthorizationParameters`、 可能在不同平台上有所不同，并且负责管理身份验证流。

对于 Android 或 iOS，我们将传递给`this`参数`AuthorizationParameters(this)`共享上下文，而在 Windows 中它传递不带任何参数为新`AuthorizationParameters()`。

### <a name="handle-continuation-for-android"></a>适用于 Android 的句柄延续

完成身份验证后，流应返回到应用。 在下面的代码处理的 Android 的情况下应将其添加到**MainActivity.cs**:


```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
  base.OnActivityResult(requestCode, resultCode, data);
  AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);

    
}
```

### <a name="handle-continuation-for-windows-phone"></a>Windows Phone 的句柄延续

对于 Windows Phone 修改`OnActivated`中的方法**App.xaml.cs**文件具有以下代码：

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

现在如果运行应用程序，您应看到身份验证对话框。
成功进行身份验证，系统会询问你的权限来访问资源 （在我们的示例 Graph API）：

![](graph-images/08.-authentication-flow.jpg "成功进行身份验证，系统会询问你访问我们的示例 Graph API 中的资源的权限")

如果身份验证会成功，并且您已授权应用访问的资源，应获取`AccessToken`并`RefreshToken`中的组合`authResult`。 这些令牌所需进一步的 API 调用以及在后台使用 Azure Active Directory 进行授权。

![](graph-images/07.-access-token-for-authentication.jpg "这些标记包括进一步 API 调用以及在后台使用 Azure Active Directory 进行授权必须")

例如，下面的代码可以从 Active Directory 获取用户列表。 Web API URL 可以替换由 Azure AD 保护 Web API。

```csharp
var client = new HttpClient();
var request = new HttpRequestMessage(HttpMethod.Get,
    "https://graph.windows.net/tendulkar.onmicrosoft.com/users?api-version=2013-04-05");
request.Headers.Authorization =
  new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
var response = await client.SendAsync(request);
var content = await response.Content.ReadAsStringAsync();
```

