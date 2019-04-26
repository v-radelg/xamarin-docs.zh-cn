---
title: 使用 Azure 移动应用的用户进行身份验证
description: 本文介绍如何使用 Azure 移动应用管理 Xamarin.Forms 应用程序中的身份验证过程。
ms.prod: xamarin
ms.assetid: D50D6F56-8B19-44E7-81F3-E0E1C6E240
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/02/2017
ms.openlocfilehash: 428e536d6895ff16a928f8cc40a8a7976d087471
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61331311"
---
# <a name="authenticating-users-with-azure-mobile-apps"></a>使用 Azure 移动应用的用户进行身份验证

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuth/)

_Azure 移动应用使用各种外部标识提供程序来支持身份验证和授权应用程序用户，包括 Facebook、 Google、 Microsoft、 Twitter 和 Azure Active Directory。可以在表上设置权限，以限制对仅限经过身份验证用户的访问。本文介绍如何使用 Azure 移动应用管理 Xamarin.Forms 应用程序中的身份验证过程。_

## <a name="overview"></a>概述

具有 Azure 移动应用管理 Xamarin.Forms 应用程序中的身份验证过程的过程如下所示：

1. 注册标识提供者站点上的 Azure 移动应用，然后在移动应用后端设置提供程序生成的凭据。 有关详细信息，请参阅[注册你的应用进行身份验证并配置应用服务](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#register-your-app-for-authentication-and-configure-app-services)。
1. 定义用于在 Xamarin.Forms 应用程序，以允许身份验证系统的身份验证过程完成后重定向回 Xamarin.Forms 应用程序的新 URL 方案。 有关详细信息，请参阅[将应用添加到允许的外部重定向 Url](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#redirecturl)。
1. 限制对 Azure 移动应用后端到访问，仅经过身份验证的客户端。 有关详细信息，请参阅[将权限限制给已经过身份验证的用户](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#restrict-permissions-to-authenticated-users)。
1. 调用从 Xamarin.Forms 应用程序的身份验证。 有关详细信息，请参阅[向可移植类库添加身份验证](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#add-authentication-to-the-portable-class-library)，[向 iOS 应用添加身份验证](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#add-authentication-to-the-ios-app)，[向 Android 应用添加身份验证](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#add-authentication-to-the-android-app)，并且[将身份验证添加到 Windows 10 应用项目](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#add-authentication-to-windows-10-including-phone-app-projects)。

> [!NOTE]
> 在 iOS 9 及更高版本中，应用传输安全 (ATS) 强制执行安全连接之间 （例如应用程序的后端服务器） 的 internet 资源和应用程序中，从而防止意外泄露敏感信息。 由于默认情况下构建适用于 iOS 9 应用程序中启用了 ATS，所有连接都将遵循 ATS 安全要求。 如果连接不能满足这些要求，它们将失败并出现异常。
> 如果不能使用 ATS 可以选择退出的`HTTPS`协议并确保对 internet 资源的通信安全。 这可以通过更新应用程序的实现**Info.plist**文件。 有关详细信息请参阅[应用程序传输安全](~/ios/app-fundamentals/ats.md)。

从历史上看，移动应用程序已使用嵌入式的 web 视图执行与标识提供者的身份验证。 这不会再出于以下原因，建议：

- 承载 web 视图的应用程序可以访问用户的完全身份验证凭据，而不仅仅是适用于应用程序的授权授予。 这违反了最低特权原则，如应用程序有权访问更强大的凭据不是它需要有可能增加应用程序的受攻击面。
- 主机应用程序能捕获用户名和密码、 自动提交窗体和绕过用户同意，和复制会话 cookie 并使用它们的用户身份执行已经过身份验证的操作。
- 嵌入式的 web 视图不与其他应用程序或设备的 web 浏览器，因此需要用户在为每个授权请求，它被视为较差的用户体验的登录共享身份验证状态。
- 某些授权终结点采取措施来检测和阻止来自 web 视图的授权请求。

替代方法是使用设备的 web 浏览器来执行身份验证，这是最新版本所采用的方法[Azure Mobile Client SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)。 使用设备浏览器进行身份验证请求可提高应用程序的可用性，因为用户只需登录到标识提供程序一次每个设备，从而提高转换率的应用程序中的登录和授权流程。 设备浏览器还提供了改进的安全性，如应用程序都能够检查和修改内容在 web 视图中，但不是显示在浏览器中的内容。

## <a name="using-an-azure-mobile-apps-instance"></a>使用 Azure 移动应用实例

[Azure 移动客户端 SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)提供了`MobileServiceClient`类，该类 Xamarin.Forms 应用程序用于访问 Azure 移动应用实例。

示例应用程序使用 Google 作为标识提供程序，允许使用 Google 帐户登录到 Xamarin.Forms 应用程序的用户。 虽然 Google 用作这篇文章中的标识提供程序，方法是同样适用于其他标识提供者。

<a name="logging-in" />

### <a name="logging-in-users"></a>在用户登录

在示例应用程序的登录屏幕中的以下屏幕截图所示：

![](azure-images/login.png "登录页")

虽然 Google 用作标识提供者，可以使用各种其他标识提供者，包括 Facebook、 Microsoft、 Twitter 和 Azure Active Directory。

下面的代码示例演示如何调用登录过程：

```csharp
async void OnLoginButtonClicked(object sender, EventArgs e)
{
  ...
  if (App.Authenticator != null)
  {
    authenticated = await App.Authenticator.AuthenticateAsync();
  }
  ...
}
```

`App.Authenticator`属性是`IAuthenticate`实例设置的每个特定于平台的项目。 `IAuthenticate`接口指定`AuthenticateAsync`必须通过每个平台项目提供的操作。 因此，调用`App.Authenticator.AuthenticateAsync`方法执行`IAuthenticate.AuthenticateAsync`平台项目中的方法。

所有平台`IAuthenticate.AuthenticateAsync`方法将调用`MobileServiceClient.LoginAsync`方法以显示登录界面和缓存数据。 下面的代码示例演示`LoginAsync`为 iOS 平台的方法：

```csharp
public async Task<bool> AuthenticateAsync()
{
  ...
  // The authentication provider could also be Facebook, Twitter, or Microsoft
  user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(
    UIApplication.SharedApplication.KeyWindow.RootViewController,
    MobileServiceAuthenticationProvider.Google,
    Constants.URLScheme);
  ...
}
```

下面的代码示例演示`LoginAsync`Android 平台的方法：

```csharp
public async Task<bool> AuthenticateAsync()
{
  ...
  // The authentication provider could also be Facebook, Twitter, or Microsoft
  user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(
    this,
    MobileServiceAuthenticationProvider.Google,
    Constants.URLScheme);
  ...
}
```

下面的代码示例演示`LoginAsync`适用于通用 Windows 平台的方法：

```csharp
public async Task<bool> AuthenticateAsync()
{
  ...
  // The authentication provider could also be Facebook, Twitter, or Microsoft
  user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(
    MobileServiceAuthenticationProvider.Google,
    Constants.URLScheme);
  ...
}
```

在所有平台上`MobileServiceAuthenticationProvider`枚举用于指定身份验证过程中将使用的标识提供程序。 当`MobileServiceClient.LoginAsync`调用方法，Azure 移动应用启动的身份验证流，显示选定提供者的登录页和与标识提供程序的成功登录后生成的身份验证令牌。 `MobileServiceClient.LoginAsync`方法将返回`MobileServiceUser`实例，它将存储在`MobileServiceClient.CurrentUser`属性。 此属性提供`UserId`和`MobileServiceAuthenticationToken`属性。 这些表示已经过身份验证的用户和用户的身份验证令牌。 身份验证令牌将包含在对 Azure 移动应用实例，允许 Xamarin.Forms 应用程序需要身份验证的用户权限的 Azure 移动应用实例上执行操作所做的所有请求。

<a name="logging-out" />

### <a name="logging-out-users"></a>用户的注销

下面的代码示例演示如何调用注销过程：

```csharp
async void OnLogoutButtonClicked(object sender, EventArgs e)
{
  bool loggedOut = false;

  if (App.Authenticator != null)
  {
    loggedOut = await App.Authenticator.LogoutAsync ();
  }
  ...
}
```

`App.Authenticator`属性是`IAuthenticate`由每个 platformproject 设置的实例。 `IAuthenticate`接口指定`LogoutAsync`必须通过每个平台项目提供的操作。 因此，调用`App.Authenticator.LogoutAsync`方法执行`IAuthenticate.LogoutAsync`平台项目中的方法。

所有平台`IAuthenticate.LogoutAsync`方法将调用`MobileServiceClient.LogoutAsync`方法来取消其登录的用户进行身份验证标识提供程序。 下面的代码示例演示`LogoutAsync`为 iOS 平台的方法：

```csharp
public async Task<bool> LogoutAsync()
{
  ...
  foreach (var cookie in NSHttpCookieStorage.SharedStorage.Cookies)
  {
    NSHttpCookieStorage.SharedStorage.DeleteCookie (cookie);
  }
  await TodoItemManager.DefaultManager.CurrentClient.LogoutAsync();
  ...
}
```

下面的代码示例演示`LogoutAsync`Android 平台的方法：

```csharp
public async Task<bool> LogoutAsync()
{
  ...
  CookieManager.Instance.RemoveAllCookie();
  await TodoItemManager.DefaultManager.CurrentClient.LogoutAsync();
  ...
}
```

下面的代码示例演示`LogoutAsync`适用于通用 Windows 平台的方法：

```csharp
public async Task<bool> LogoutAsync()
{
  ...
  await TodoItemManager.DefaultManager.CurrentClient.LogoutAsync();
  ...
}
```

当`IAuthenticate.LogoutAsync`方法调用、 清除由标识提供者设置的任何 cookie，之前`MobileServiceClient.LogoutAsync`会调用方法来取消其登录的用户进行身份验证标识提供程序。

## <a name="summary"></a>总结

本文介绍了如何使用 Azure 移动应用进行管理的 Xamarin.Forms 应用程序中的身份验证过程。 Azure 移动应用使用各种外部标识提供程序来支持身份验证和授权应用程序用户，包括 Facebook、 Google、 Microsoft、 Twitter 和 Azure Active Directory。 `MobileServiceClient` Xamarin.Forms 应用程序使用类来控制对 Azure 移动应用实例的访问。


## <a name="related-links"></a>相关链接

- [TodoAzureAuth （示例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuth/)
- [使用 Azure 移动应用](~/xamarin-forms/data-cloud/consuming/azure.md)
- [向 Xamarin.Forms 应用添加身份验证](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users/)
- [Azure 移动客户端 SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
- [MobileServiceClient](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx)
