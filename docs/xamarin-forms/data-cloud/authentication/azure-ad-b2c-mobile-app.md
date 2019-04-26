---
title: 将 Azure Active Directory B2C 集成与 Azure 移动应用程序
description: Azure Active Directory B2C 是面向消费者的 web 和移动应用程序的云标识管理解决方案。 本文演示如何使用 Azure Active Directory B2C 为 Xamarin.Forms 中提供身份验证和授权对 Azure 移动应用实例。
ms.prod: xamarin
ms.assetid: 53F52036-A997-4D0F-86B4-4302C6913136
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 135977329e2a190dd4c611937f6b8a664f135f5c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61332077"
---
# <a name="integrating-azure-active-directory-b2c-with-azure-mobile-apps"></a>将 Azure Active Directory B2C 集成与 Azure 移动应用程序

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuthADB2CClientFlow/)

_Azure Active Directory B2C 是面向消费者的 web 和移动应用程序的云标识管理解决方案。本文演示如何使用 Azure Active Directory B2C 为 Xamarin.Forms 中提供身份验证和授权对 Azure 移动应用实例。_

![](~/media/shared/preview.png "此 API 是当前预发布版本")

> [!NOTE]
> [Microsoft 身份验证库](https://www.nuget.org/packages/Microsoft.Identity.Client)仍处于预览状态，但适用于在生产环境中使用。 但是，那里可能重大更改的 API、 内部缓存格式和库，这可能会影响你的应用程序的其他机制。

## <a name="overview"></a>概述

Azure 移动应用让你能够使用移动身份验证、 脱机同步和推送通知的支持在 Azure 应用服务中托管的可缩放后端进行开发的应用程序。 有关 Azure 移动应用的详细信息，请参阅[使用 Azure 移动应用](~/xamarin-forms/data-cloud/consuming/azure.md)，并[使用 Azure 移动应用进行身份验证用户](~/xamarin-forms/data-cloud/authentication/azure.md)。

Azure Active Directory B2C 是标识管理服务的面向消费者的应用程序，允许使用者来登录到由应用程序：

- 使用现有社交帐户 （Microsoft、 Google、 Facebook、 Amazon、 LinkedIn）。
- 创建新凭据 （电子邮件地址和密码，或用户名和密码）。 这些凭据嘿*本地*帐户。

有关 Azure Active Directory B2C 的详细信息，请参阅[与 Azure Active Directory B2C 进行身份验证用户](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md)。

可以使用 azure Active Directory B2C 来管理 Azure 移动应用身份验证工作流。 使用此方法时，标识管理体验已完全定义为在云中，并可以修改而无需更改你的移动应用程序代码。

有与 Azure 移动应用实例集成的 Azure Active Directory B2C 租户时可采用的两个身份验证工作流：

- [客户端托管](#client_managed)– 在此方法的身份验证过程使用 Azure Active Directory B2C 租户，则将 Xamarin.Forms 移动应用程序启动并将接收到的身份验证令牌传递给 Azure 移动应用实例。
- [服务器托管](#server_managed)– 在这种方法在 Azure Mobile Apps 实例使用 Azure Active Directory B2C 租户启动的基于 web 的工作流通过身份验证过程。

在这两种情况下，由 Azure Active Directory B2C 租户提供的身份验证体验。 在示例应用程序，这会导致登录屏幕中的以下屏幕截图所示：

![](azure-ad-b2c-mobile-app-images/screenshots.png "登录页")

单一登录与社交标识提供者，或使用本地帐户、 允许使用。 虽然 Microsoft、 Google 和 Facebook 用作社交标识提供者在此示例中，还可以使用其他标识提供者。

## <a name="setup"></a>安装

无论使用的身份验证工作流，与 Azure 移动应用实例集成的 Azure Active Directory B2C 租户的初始过程如下所示：

1. 创建 Azure 移动应用实例。 有关详细信息，请参阅[使用 Azure 移动应用](~/xamarin-forms/data-cloud/consuming/azure.md)。
1. 启用 Azure 移动应用实例和 Xamarin.Forms 应用程序中的身份验证。 有关详细信息，请参阅[使用 Azure 移动应用进行身份验证用户](~/xamarin-forms/data-cloud/authentication/azure.md)。
1. 创建 Azure Active Directory B2C 租户。 有关详细信息，请参阅[与 Azure Active Directory B2C 进行身份验证用户](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md)。

请注意，Microsoft 身份验证库 (MSAL) 需要在使用客户端托管的身份验证工作流。 MSAL 使用设备的 web 浏览器来执行身份验证。 这可提高应用程序的可用性，因为用户只需要在登录后每台设备，从而提高转换率的单一登录和授权流应用程序中。 设备浏览器还提供了改进的安全性。 用户完成身份验证过程后，控件将在 web 浏览器选项卡中，返回到应用程序。这被通过重定向 url 返回的身份验证过程中，检测并处理自定义 URL 后将其发送注册自定义的 URL 方案。 有关使用 MSAL 来与 Azure Active Directory B2C 租户通信的详细信息，请参阅[与 Azure Active Directory B2C 进行身份验证用户](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md)。

<a name="client_managed" />

## <a name="client-managed-authentication"></a>客户端托管的身份验证

在客户端托管的身份验证，Xamarin.Forms 移动应用程序联系启动身份验证流的 Azure Active Directory B2C 租户。 后成功登录 Azure Active Directory B2C 租户将返回一个标识令牌，然后提供在登录到 Azure 移动应用实例。 这样，Xamarin.Forms 应用程序需要身份验证的用户权限的 Azure 移动应用实例上执行操作。

### <a name="azure-active-directory-b2c-tenant-configuration"></a>Azure Active Directory B2C 租户配置

客户端托管的身份验证的工作流，应按如下所示配置 Azure Active Directory B2C 租户：

- 包含本机客户端。
- 将自定义重定向 URI 设置为唯一地标识移动应用程序后, 跟的 URL 方案`://auth/`。 有关选择自定义的 URL 方案的详细信息，请参阅[选择本机应用重定向 URI](/azure/active-directory-b2c/active-directory-b2c-app-registration#choosing-a-native-app-redirect-uri)。

下面的屏幕截图演示了此配置：

[![](azure-ad-b2c-mobile-app-images/client-flow-config-sml.png "Azure Active Directory B2C 配置")](azure-ad-b2c-mobile-app-images/client-flow-config.png#lightbox "Azure Active Directory B2C 配置")

使用 Azure Active Directory B2C，回复 URL 设为相同的自定义 URL 方案，也可配置租户中的策略后跟`://auth/`。 下面的屏幕截图演示了此配置：

![](azure-ad-b2c-mobile-app-images/client-flow-policies.png "Azure Active Directory B2C 策略")

### <a name="azure-mobile-app-configuration"></a>Azure 移动应用配置

客户端托管的身份验证的工作流，应按如下所示配置 Azure 移动应用实例：

- 应启用应用服务身份验证。
- 当请求未经过身份验证时要执行的操作应设置为**Azure Active Directory 登录**。

下面的屏幕截图演示了此配置：

![](azure-ad-b2c-mobile-app-images/client-flow-ama-config.png "Azure 移动应用配置")

Azure 移动应用实例也应配置为与 Azure Active Directory B2C 租户。 这可以通过启用**高级**Azure Active Directory 身份验证提供程序，模式与**客户端 ID**正在**应用程序 ID**的 azureActive Directory B2C 租户，并**颁发者 Url**正在 Azure Active Directory B2C 策略的元数据终结点。 下面的屏幕截图演示了此配置：

![](azure-ad-b2c-mobile-app-images/client-flow-ama-advanced-config.png "高级配置的 azure 移动应用")

### <a name="signing-in"></a>在登录

下面的代码示例演示如何启动客户端托管的身份验证流：

```csharp
public async Task<bool> LoginAsync(bool useSilent = false)
{
    ...
    AuthenticationResult authenticationResult = await ADB2CClient.AcquireTokenAsync(
        Constants.Scopes,
        GetUserByPolicy(ADB2CClient.Users, Constants.PolicySignUpSignIn),
        App.UiParent);

    ...
    var payload = new JObject();
    payload["access_token"] = authenticationResult.IdToken;

    User = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(
        MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory,
        payload);
    ...
}
```

Microsoft 身份验证库 (MSAL) 用于与 Azure Active Directory B2C 租户启动的身份验证的工作流。 `AcquireTokenAsync`方法会启动设备的 web 浏览器并显示在通过引用的策略指定的 Azure Active Directory B2C 策略中定义的身份验证选项`Constants.Authority`常量。 此策略定义期间注册和登录中使用者将经历的体验和应用程序将接收在成功注册或登录的声明。

结果`AcquireTokenAsync`方法调用是`AuthenticationResult`实例。 如果身份验证成功，`AuthenticationResult`实例将包含一个标识令牌，将本地缓存。 如果身份验证不成功，`AuthenticationResult`实例将包含数据，该值指示身份验证失败的原因。 有关如何使用 MSAL 来与 Azure Active Directory B2C 租户通信的信息，请参阅[与 Azure Active Directory B2C 进行身份验证用户](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md)。

当`MobileServiceClient.LoginAsync`调用方法，Azure 移动应用实例收到的标识令牌包装在`JObject`。 为有效的标记表示 Azure 移动应用实例并不需要启动其自己的 OAuth 2.0 身份验证流存在。 相反，`MobileServiceClient.LoginAsync`方法将返回`MobileServiceUser`实例，它将存储在`MobileServiceClient.CurrentUser`属性。 此属性提供`UserId`和`MobileServiceAuthenticationToken`属性。 这些表示已经过身份验证的用户和用户，可以在它过期之前的身份验证令牌。 身份验证令牌将包含在对 Azure 移动应用实例，允许 Xamarin.Forms 应用程序需要身份验证的用户权限的 Azure 移动应用实例上执行操作所做的所有请求。

### <a name="signing-out"></a>正在注销

下面的代码示例演示如何调用客户端托管的注销过程：

```csharp
public async Task<bool> LogoutAsync()
{
    ...
    await TodoItemManager.DefaultManager.CurrentClient.LogoutAsync();

    foreach (var user in ADB2CClient.Users)
    {
        ADB2CClient.Remove(user);
    }
    ...
}
```

`MobileServiceClient.LogoutAsync`方法取消对使用 Azure 移动应用实例中，用户身份验证，则所有身份验证令牌会清除从创建的 MSAL 的本地缓存。

<a name="server_managed" />

## <a name="server-managed-authentication"></a>服务器托管的身份验证

服务器托管的身份验证，在 Xamarin.Forms 应用程序联系 Azure 移动应用实例，使用 Azure Active Directory B2C 租户来管理 OAuth 2.0 身份验证流的 B2C 策略中定义显示在登录页。 以下成功登录，Azure 移动应用实例返回令牌，可让 Xamarin.Forms 应用程序需要身份验证的用户权限的 Azure 移动应用实例上执行操作。

### <a name="azure-active-directory-b2c-tenant-configuration"></a>Azure Active Directory B2C 租户配置

服务器托管的身份验证的工作流，应按如下所示配置 Azure Active Directory B2C 租户：

- 包括 web 应用/web API，并允许隐式流。
- 答复 URL 设置为 Azure 移动应用的地址后, 跟`/.auth/login/aad/callback`。

下面的屏幕截图演示了此配置：

[![](azure-ad-b2c-mobile-app-images/server-flow-config-sml.png "Azure Active Directory B2C 配置")](azure-ad-b2c-mobile-app-images/server-flow-config.png#lightbox "Azure Active Directory B2C 配置")

使用 Azure Active Directory B2C，回复 URL 设为 Azure 移动应用的地址，还应该配置租户中的策略后跟`/.auth/login/aad/callback`。 下面的屏幕截图演示了此配置：

![](azure-ad-b2c-mobile-app-images/server-flow-policies.png "Azure Active Directory B2C 策略")

### <a name="azure-mobile-apps-instance-configuration"></a>Azure 移动应用实例配置

服务器托管的身份验证的工作流，应按如下所示配置 Azure 移动应用实例：

- 应启用应用服务身份验证。
- 当请求未经过身份验证时要执行的操作应设置为**Azure Active Directory 登录**。

下面的屏幕截图演示了此配置：

![](azure-ad-b2c-mobile-app-images/server-flow-ama-config.png "Azure 移动应用配置")

Azure 移动应用实例也应配置为与 Azure Active Directory B2C 租户。 这可以通过启用**高级**Azure Active Directory 身份验证提供程序，模式与**客户端 ID**正在**应用程序 ID**的 azureActive Directory B2C 租户，并**颁发者 Url**正在 Azure Active Directory B2C 策略的元数据终结点。 下面的屏幕截图演示了此配置：

![](azure-ad-b2c-mobile-app-images/server-flow-ama-advanced-config.png "高级配置的 azure 移动应用")

### <a name="signing-in"></a>在登录

下面的代码示例演示如何启动服务器托管的身份验证流：

```csharp
public async Task<bool> AuthenticateAsync()
{
    ...
    MobileServiceUser user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(
        UIApplication.SharedApplication.KeyWindow.RootViewController,
        MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory,
        Constants.URLScheme);
    ...
}
```

当`MobileServiceClient.LoginAsync`调用方法，Azure 移动应用实例执行链接的 Azure Active Directory B2C 策略，从而启动 OAuth 2.0 身份验证流。 请注意，每个`AuthenticateAsync`方法是特定于平台的。 但是，每个`AuthenticateAsync`方法使用`MobileServiceClient.LoginAsync`方法并指定将在身份验证过程中使用 Azure Active Directory 租户。 有关详细信息，请参阅[在用户登录](~/xamarin-forms/data-cloud/authentication/azure.md#logging-in)。

`MobileServiceClient.LoginAsync`方法将返回`MobileServiceUser`实例，它将存储在`MobileServiceClient.CurrentUser`属性。 此属性提供`UserId`和`MobileServiceAuthenticationToken`属性。 这些表示已经过身份验证的用户和用户，可以在它过期之前的身份验证令牌。 身份验证令牌将包含在对 Azure 移动应用实例，允许 Xamarin.Forms 应用程序需要身份验证的用户权限的 Azure 移动应用实例上执行操作所做的所有请求。

### <a name="signing-out"></a>正在注销

下面的代码示例演示如何调用服务器托管的注销过程：

```csharp
public async Task<bool> LogoutAsync()
{
    ...
    await TodoItemManager.DefaultManager.CurrentClient.LogoutAsync();
    ...
}
```

`MobileServiceClient.LogoutAsync`方法取消用户进行身份验证与 Azure 移动应用实例。 有关详细信息，请参阅[日志记录用户](~/xamarin-forms/data-cloud/authentication/azure.md#logging-out)。

## <a name="summary"></a>总结

本文演示了如何使用 Azure Active Directory B2C 为 Xamarin.Forms 中提供身份验证和授权对 Azure 移动应用实例。 Azure Active Directory B2C 是面向消费者的 web 和移动应用程序的云标识管理解决方案。


## <a name="related-links"></a>相关链接

- [TodoAzureAuth ServerFlow （示例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuthADB2CServerFlow/)
- [TodoAzureAuth ClientFlow （示例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuthADB2CClientFlow/)
- [使用 Azure 移动应用](~/xamarin-forms/data-cloud/consuming/azure.md)
- [使用 Azure 移动应用的用户进行身份验证](~/xamarin-forms/data-cloud/authentication/azure.md)
- [使用 Azure Active Directory B2C 的用户进行身份验证](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md)
- [Microsoft 身份验证库](https://www.nuget.org/packages/Microsoft.Identity.Client)
