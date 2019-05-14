---
title: 将 Azure Active Directory B2C 集成与 Azure 移动应用程序
description: Azure Active Directory B2C 是面向消费者的 web 和移动应用程序的云标识管理解决方案。 本文演示如何使用 Azure Active Directory B2C 为 Xamarin.Forms 中提供身份验证和授权对 Azure 移动应用实例。
ms.prod: xamarin
ms.assetid: 53F52036-A997-4D0F-86B4-4302C6913136
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/22/2019
ms.openlocfilehash: 4c3cc489f7c65636ad9f2b5541e552665b10980e
ms.sourcegitcommit: 0cb62b02a7efb5426f2356d7dbdfd9afd85f2f4a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/13/2019
ms.locfileid: "65557295"
---
# <a name="integrating-azure-active-directory-b2c-with-azure-mobile-apps"></a>将 Azure Active Directory B2C 集成与 Azure 移动应用程序

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuthADB2CClientFlow/)

_Azure Active Directory B2C 提供云身份管理面向使用者的应用程序。本文介绍如何使用 Azure Active Directory B2C 将标识管理集成到 Azure 移动应用实例使用 Xamarin.Forms。_

## <a name="overview"></a>概述

Azure 移动应用让你能够使用可缩放的后端，托管在 Azure 应用服务中开发应用程序。 它支持移动身份验证、 脱机同步和推送通知。 有关 Azure 移动应用的详细信息，请参阅[使用 Azure 移动应用](~/xamarin-forms/data-cloud/consuming/azure.md)，并[使用 Azure 移动应用进行身份验证用户](~/xamarin-forms/data-cloud/authentication/azure.md)。

Azure Active Directory B2C 是面向消费者的应用程序，允许使用者标识管理服务：

- 使用现有社交帐户 （Microsoft、 Google、 Facebook、 Amazon、 LinkedIn） 进行登录。
- 创建新凭据 （电子邮件地址和密码，或用户名和密码）。 这些凭据嘿*本地*帐户。

有关 Azure Active Directory B2C 的详细信息，请参阅[与 Azure Active Directory B2C 进行身份验证用户](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md)。

可以使用 azure Active Directory B2C 来管理 Azure 移动应用身份验证工作流。 这种方法在云中配置标识管理，并允许更改而无需修改应用程序代码。


使用 Azure Active Directory B2C 的 azure 移动应用允许两种身份验证方法：

- [客户端托管](#client-managed-authentication)– Xamarin.Forms 移动应用程序启动身份验证过程与 Azure Active Directory B2C 租户，并将接收到的身份验证令牌传递给 Azure 移动应用实例。
- [服务器托管](#server-managed-authentication)-Azure 移动应用实例使用 Azure Active Directory B2C 租户来启动身份验证过程中通过基于 web 的工作流。

在这两种情况下，由 Azure Active Directory B2C 租户提供的身份验证体验。 在示例应用程序，登录屏幕应如以下屏幕截图所示：

![](azure-ad-b2c-mobile-app-images/screenshots.png "登录页")

此示例演示使用本地帐户登录，但您还可以启用 Microsoft、 Google、 Facebook 和其他标识提供者。

## <a name="setup"></a>安装

在这两个工作流中与 Azure 移动应用中集成 Azure Active Directory B2C 租户的初始过程如下所示：

1. 在 Azure 门户中创建 Azure 移动应用实例。 这会生成类似于示例项目的起始项目。 有关详细信息，请参阅[使用 Azure 移动应用](~/xamarin-forms/data-cloud/consuming/azure.md)。
1. 创建 Azure Active Directory B2C 租户。 有关详细信息，请参阅[与 Azure Active Directory B2C 进行身份验证用户](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md)。
1. 启用 Azure 移动应用实例和 Xamarin.Forms 应用程序中的身份验证。 有关详细信息，请参阅[使用 Azure 移动应用进行身份验证用户](~/xamarin-forms/data-cloud/authentication/azure.md)。

> [!NOTE]
> 使用客户端托管的身份验证工作流时，Microsoft 身份验证库 (MSAL) 是必需的。 MSAL 使用设备的 web 浏览器进行身份验证。 在浏览器中完成身份验证后，用户将重定向到自定义的 URL 方案。 应用程序注册的自定义的 URL 方案，使其能够重新获得控制用户体验和响应身份验证。 有关使用 MSAL 来与 Azure Active Directory B2C 租户通信的详细信息，请参阅[与 Azure Active Directory B2C 进行身份验证用户](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md)。

## <a name="client-managed-authentication"></a>客户端托管的身份验证

在客户端托管的身份验证，Xamarin.Forms 应用程序联系以启动身份验证流的 Azure Active Directory B2C 租户。 登录后，Azure Active Directory B2C 租户返回一个标识令牌，提供给 Azure 移动应用实例。 这样，Xamarin.Forms 应用程序执行需要身份验证的用户权限的操作。

### <a name="azure-active-directory-b2c-client-managed-tenant-configuration"></a>Azure Active Directory B2C 客户端托管的租户配置

客户端托管的身份验证的工作流，应按如下所示配置 Azure Active Directory B2C 租户：

- 设置**包括本机客户端**为"是"。
- 设置自定义重定向 URI。 MSAL 文档建议使用"msal"结合应用程序 ID，并后跟":/ / auth /"。 有关详细信息，请参阅[MSAL 客户端应用程序重定向 URI](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Client-Applications#redirect-uri)。

下面的屏幕截图演示了此配置：

[!["Azure Active Directory B2C 配置"](azure-ad-b2c-mobile-app-images/azure-redirect-uri-cropped.png)](azure-ad-b2c-mobile-app-images/azure-redirect-uri.png#lightbox "Azure Active Directory B2C 配置")

此外应配置 Azure Active Directory B2C 租户中使用的登录策略，以便回复 URL 设置为相同的自定义 URL 方案。 这可以通过选择**运行用户流**在 Azure 门户中访问的策略设置。 保存将移动应用配置为需要在此屏幕上找到的元数据 URL。 下面的屏幕截图演示了此配置，并应将复制的 URL:

[!["Azure Active Directory B2C 策略"](azure-ad-b2c-mobile-app-images/client-flow-policies-cropped.png)](azure-ad-b2c-mobile-app-images/client-flow-policies.png#lightbox "Azure Active Directory B2C 策略配置")

### <a name="azure-mobile-app-configuration"></a>Azure 移动应用配置

客户端托管的身份验证的工作流，将 Azure 移动应用实例配置，如下所示：

- 应启用应用服务身份验证。
- 当请求未经身份验证时要执行的操作应设置为**使用 Azure Active Directory 登录**。

下面的屏幕截图演示了此配置：

[!["Azure 移动应用身份验证配置"](azure-ad-b2c-mobile-app-images/ama-config-cropped.png)](azure-ad-b2c-mobile-app-images/ama-config.png#lightbox "Azure 移动应用身份验证配置")

配置要与 Azure Active Directory B2C 租户通信的 Azure 移动应用实例：

- 单击 Azure Active Directory 配置并启用**高级**Azure Active Directory 身份验证提供程序的模式。
- 设置**客户端 ID**到**应用程序 ID**的 Azure Active Directory B2C 租户。
- 设置**颁发者 Url**到以前在租户配置过程中复制的策略的元数据 URL。

下面的屏幕截图演示了此配置：

!["Azure 移动应用高级配置"](azure-ad-b2c-mobile-app-images/ama-advanced-config.png)

### <a name="signing-in"></a>登录

下面的代码示例显示了用于启动客户端托管的身份验证流的关键方法调用：

```csharp
public async Task<bool> LoginAsync(bool useSilent = false)
{
    ...

    authenticationResult = await ADB2CClient.AcquireTokenAsync(
        Constants.Scopes,
        string.Empty,
        UIBehavior.SelectAccount,
        string.Empty,
        App.UiParent);

    ...

    var payload = new JObject();
    if (authenticationResult != null && !string.IsNullOrWhiteSpace(authenticationResult.IdToken))
    {
        payload["access_token"] = authenticationResult.IdToken;
    }

    User = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(
        MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory,
        payload);
    success = true;

    ...
}
```

Microsoft 身份验证库 (MSAL) 用于与 Azure Active Directory B2C 租户启动的身份验证的工作流。 `AcquireTokenAsync`方法会启动设备的 web 浏览器并显示在通过引用的策略指定的 Azure Active Directory B2C 策略中定义的身份验证选项`Constants.AuthoritySignin`常量。 此策略将定义用户的登录和注册体验和应用程序身份验证成功后收到的声明。

结果`AcquireTokenAsync`方法调用是`AuthenticationResult`实例。 如果身份验证成功，`AuthenticationResult`实例将包含一个标识令牌，将本地缓存。 如果身份验证不成功，`AuthenticationResult`实例将包含数据，该值指示身份验证失败的原因。 有关如何使用 MSAL 来与 Azure Active Directory B2C 租户通信的信息，请参阅[与 Azure Active Directory B2C 进行身份验证用户](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md)。

当`CurrentClient.LoginAsync`调用方法，Azure 移动应用实例收到的标识令牌包装在`JObject`。 为有效的标记表示 Azure 移动应用实例并不需要启动其自己的 OAuth 2.0 身份验证流存在。 相反，`CurrentClient.LoginAsync`方法将返回`MobileServiceUser`实例，它将存储在`MobileServiceClient.CurrentUser`属性。 此属性提供`UserId`和`MobileServiceAuthenticationToken`属性。 这些表示已经过身份验证的用户和令牌在过期之前可以使用。 身份验证令牌将包含在对 Azure 移动应用实例，允许 Xamarin.Forms 应用程序执行需要身份验证的用户权限的操作所做的所有请求。

### <a name="signing-out"></a>正在注销

下面的代码示例演示如何调用客户端托管的注销过程：

```csharp
public async Task<bool> LogoutAsync()
{
    ...

    IEnumerable<IAccount> accounts = await ADB2CClient.GetAccountsAsync();
    while (accounts.Any())
    {
        await ADB2CClient.RemoveAsync(accounts.First());
        accounts = await ADB2CClient.GetAccountsAsync();
    }
    User = null;

    ...
}
```

`CurrentClient.LogoutAsync`方法取消对使用 Azure 移动应用实例中，用户身份验证，则所有身份验证令牌会清除从创建的 MSAL 的本地缓存。

## <a name="server-managed-authentication"></a>服务器托管的身份验证

服务器托管的身份验证，在 Xamarin.Forms 应用程序联系 Azure 移动应用实例，使用 Azure Active Directory B2C 租户来管理 OAuth 2.0 身份验证流的 B2C 策略中定义显示在登录页。 以下成功登录，Azure 移动应用实例返回令牌，可让 Xamarin.Forms 应用程序执行需要身份验证的用户权限的操作。

### <a name="azure-active-directory-b2c-server-managed-tenant-configuration"></a>Azure Active Directory B2C 服务器托管的租户配置

服务器托管的身份验证的工作流，应按如下所示配置 Azure Active Directory B2C 租户：

- 包括 web 应用/web API，并允许隐式流。
- 答复 URL 设置为 Azure 移动应用的地址后, 跟`/.auth/login/aad/callback`。

下面的屏幕截图演示了此配置：

[!["Azure Active Directory B2C 配置"](azure-ad-b2c-mobile-app-images/server-flow-config-cropped.png)](azure-ad-b2c-mobile-app-images/server-flow-config.png#lightbox "Azure Active Directory B2C 配置")

在 Azure Active Directory B2C 租户中使用的策略需要具有配置的回复 URL。 这通过将回复 URL 设置为 Azure 移动应用后, 跟的地址来实现`/.auth/login/aad/callback`。 您还应保存因为你将需要它的移动应用配置为在此屏幕的顶部找到的元数据 URL。 下面的屏幕截图演示了此配置，并应保存的元数据 URL:

!["Azure Active Directory B2C 策略配置"](azure-ad-b2c-mobile-app-images/server-flow-policies.png)

### <a name="azure-mobile-apps-instance-configuration"></a>Azure 移动应用实例配置

服务器托管的身份验证的工作流，应按如下所示配置 Azure 移动应用实例：

- 应启用应用服务身份验证。
- 当请求未经身份验证时要执行的操作应设置为**使用 Azure Active Directory 登录**。

下面的屏幕截图演示了此配置：

[!["Azure 移动应用身份验证配置"](azure-ad-b2c-mobile-app-images/ama-config-cropped.png)](azure-ad-b2c-mobile-app-images/ama-config.png#lightbox "Azure 移动应用身份验证配置")

Azure 移动应用实例也应配置为与 Azure Active Directory B2C 租户：

- 单击 Azure Active Directory 配置并启用**高级**Azure Active Directory 身份验证提供程序的模式。
- 设置**客户端 ID**到**应用程序 ID**的 Azure Active Directory B2C 租户。
- **颁发者 Url**是以前在租户配置过程中复制的策略的元数据 URL。

下面的屏幕截图演示了此配置：

!["Azure 移动应用高级配置"](azure-ad-b2c-mobile-app-images/ama-advanced-config.png)

### <a name="signing-in"></a>登录

下面的代码示例演示如何启动服务器托管的身份验证流：

```csharp
public async Task<bool> AuthenticateAsync()
{
    ...

    MobileServiceUser user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(
        MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory,
        Constants.URLScheme);

    ...
}
```

当`CurrentClient.LoginAsync`调用方法，Azure 移动应用实例执行链接的 Azure Active Directory B2C 策略，这将启动 OAuth 2.0 身份验证流。 每个`AuthenticateAsync`方法是特定于平台的。 但是，每个`AuthenticateAsync`方法调用`CurrentClient.LoginAsync`方法并指定将在身份验证过程中使用 Azure Active Directory 租户。 有关详细信息，请参阅[在用户登录](~/xamarin-forms/data-cloud/authentication/azure.md#logging-in)。

`CurrentClient.LoginAsync`方法将返回`MobileServiceUser`实例，它将存储在`CurrentClient.CurrentUser`属性。 此属性提供`UserId`和`MobileServiceAuthenticationToken`属性。 这些表示已经过身份验证的用户和用户，可以在它过期之前的身份验证令牌。 身份验证令牌将包含在对 Azure 移动应用实例，允许 Xamarin.Forms 应用程序需要身份验证的用户权限的 Azure 移动应用实例上执行操作所做的所有请求。

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


## <a name="related-links"></a>相关链接

- [TodoAzureAuth ClientFlow （示例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuthADB2CClientFlow/)
- [TodoAzureAuth ServerFlow （示例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuthADB2CServerFlow/)
- [使用 Azure 移动应用](~/xamarin-forms/data-cloud/consuming/azure.md)
- [使用 Azure 移动应用的用户进行身份验证](~/xamarin-forms/data-cloud/authentication/azure.md)
- [使用 Azure Active Directory B2C 的用户进行身份验证](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md)
- [Microsoft 身份验证库 nuget 包](https://www.nuget.org/packages/Microsoft.Identity.Client)
- [Microsoft 身份验证库文档](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)