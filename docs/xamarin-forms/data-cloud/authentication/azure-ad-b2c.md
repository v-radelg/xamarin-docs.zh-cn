---
title: 使用 Azure Active Directory B2C 的用户进行身份验证
description: Azure Active Directory B2C 是面向消费者的 web 和移动应用程序的云标识管理解决方案。 本文演示如何使用 Microsoft 身份验证库和 Azure Active Directory B2C 集成到移动应用程序的使用者标识管理。
ms.prod: xamarin
ms.assetid: B0A5DB65-0585-4A00-B908-22CCC286E6B6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 7c12136a0dad0165c46f1559e7a2d61abaf7af1e
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61331472"
---
# <a name="authenticating-users-with-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 的用户进行身份验证

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/WebServices/AzureADB2CAuth/)

_Azure Active Directory B2C 是面向消费者的 web 和移动应用程序的云标识管理解决方案。本文演示如何使用 Microsoft 身份验证库和 Azure Active Directory B2C 集成到移动应用程序的使用者标识管理。_

![](~/media/shared/preview.png "此 API 是当前预发布版本")

> [!NOTE]
> [Microsoft 身份验证库](https://www.nuget.org/packages/Microsoft.Identity.Client)仍处于预览状态，但适用于在生产环境中使用。 但是，那里可能重大更改的 API、 内部缓存格式和库，这可能会影响你的应用程序的其他机制。

## <a name="overview"></a>概述

Azure Active Directory B2C 是标识管理服务的面向消费者的应用程序，允许使用者来登录到由应用程序：

- 使用现有社交帐户 （Microsoft、 Google、 Facebook、 Amazon、 LinkedIn）。
- 创建新凭据 （电子邮件地址和密码，或用户名和密码）。 这些凭据嘿*本地*帐户。

将 Azure Active Directory B2C 标识管理服务集成到移动应用程序的过程如下所示：

1. 创建 Azure Active Directory B2C 租户。 有关详细信息，请参阅[Azure 门户中创建一个 Azure Active Directory B2C 租户](/azure/active-directory-b2c/active-directory-b2c-get-started/)。
1. 移动应用程序注册到 Azure Active Directory B2C 租户。 注册过程将分配**应用程序 ID** ，用于唯一标识应用程序和一个**重定向 URL**可用于将响应定向回你的应用程序。 有关详细信息，请参阅[Azure Active Directory B2C:注册应用程序](/azure/active-directory-b2c/active-directory-b2c-app-registration/)。
1. 创建注册和登录策略。 此策略将定义使用者注册和登录，期间将经历的体验，并且还指定的应用程序将接收的令牌内容在成功注册或登录。 有关详细信息，请参阅[Azure Active Directory B2C:内置策略](/azure/active-directory-b2c/active-directory-b2c-reference-policies/)。
1. 使用[Microsoft 身份验证库](https://www.nuget.org/packages/Microsoft.Identity.Client)(MSAL) 在移动应用程序与 Azure Active Directory B2C 租户启动的身份验证的工作流中。

> [!NOTE]
> 将 Azure Active Directory B2C 标识管理集成到移动应用程序，以及 MSAL 还可用来将 Azure Active Directory 标识管理集成到移动应用程序。 这可以通过与 Azure Active Directory 在注册的移动应用程序来实现[应用程序注册门户](https://apps.dev.microsoft.com/)。 注册过程将分配**应用程序 ID**用于唯一标识应用程序，使用 MSAL 时，应指定。 有关详细信息，请参阅[如何使用 v2.0 终结点注册应用](/azure/active-directory/develop/active-directory-v2-app-registration/)，并[进行身份验证在移动应用使用 Microsoft 身份验证库](https://blog.xamarin.com/authenticate-mobile-apps-using-microsoft-authentication-library/)Xamarin 博客上。

MSAL 使用设备的 web 浏览器来执行身份验证。 这可提高应用程序的可用性，因为用户只需要在登录后每台设备，从而提高转换率的单一登录和授权流应用程序中。 设备浏览器还提供了改进的安全性。 用户完成身份验证过程后，控件将在 web 浏览器选项卡中，返回到应用程序。这被通过重定向 url 返回的身份验证过程中，检测并处理自定义 URL 后将其发送注册自定义的 URL 方案。 有关选择自定义的 URL 方案的详细信息，请参阅[选择本机应用重定向 URI](/azure/active-directory-b2c/active-directory-b2c-app-registration#choosing-a-native-app-redirect-uri/)。

> [!NOTE]
> 用于向操作系统注册自定义的 URL 方案和处理方案的机制是特定于每个平台。

每个请求都发送到 Azure Active Directory B2C 租户中指定*策略*。 策略描述了使用者标识体验，例如注册、 或单一登录。 例如，注册策略允许通过以下设置来配置 Azure Active Directory B2C 租户的行为：

- 使用者可以用来登录到应用程序的帐户类型。
- 要在注册期间从使用者收集的数据。
- 多重身份验证。
- 注册页面内容。
- 移动应用程序时执行的策略已接收的令牌声明。

Azure Active Directory 租户可以包含不同类型，然后根据需要在应用程序中使用该类型的多个策略。 此外，可以跨应用程序，允许您定义和修改使用者标识体验，而无需更改你的代码重用策略。 有关策略的详细信息，请参阅[Azure Active Directory B2C:内置策略](/azure/active-directory-b2c/active-directory-b2c-reference-policies/)。

## <a name="setup"></a>安装

必须将 Microsoft 身份验证库 (MSAL) NuGet 库添加到可移植类库 (PCL) 项目和 Xamarin.Forms 解决方案中的平台项目。 以下部分提供有关使用 MSAL 来与 Azure Active Directory B2C 租户从移动应用程序进行通信的其他安装说明。

### <a name="portable-class-library"></a>可移植类库

使用 MSAL 的 Pcl 将需要重定目标以使用 Profile7。 有关 PCL的详细信息，请参阅[可移植类库简介](~/cross-platform/app-fundamentals/pcl.md)。

### <a name="ios"></a>iOS

在 iOS 上，与 Azure Active Directory B2C 注册的自定义 URL 方案必须注册中**Info.plist**，如以下屏幕截图中所示：

![](azure-ad-b2c-images/customurl-ios.png "注册自定义 URL 方案，在 iOS 上")

Azure Active Directory B2C 完成授权请求，它将重定向到注册的重定向 URL。 由于 URL 使用它会导致启动的移动应用程序的 iOS 自定义方案，同时将 URL 传递作为启动参数，它由处理`OpenUrl`在应用程序的重写`AppDelegate`类，该类中的以下代码所示示例：

```csharp
using Microsoft.Identity.Client;

namespace TodoAzure.iOS
{
    [Register("AppDelegate")]
    public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate
    {
        ...
        public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
        {
            AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
            return true;
        }
    }
}
```

中的代码`OpenURL`方法可确保对 MSAL 返回控制后身份验证工作流的交互部分已结束。

### <a name="android"></a>Android

在 Android 上，与 Azure Active Directory B2C 注册的自定义 URL 方案必须注册中**AndroidManifest.xml**，通过添加`<activity>`元素内的现有`<application>`元素。 `<activity>`元素指定`IntentFilter`上`Activity`的处理方案，并在下面的示例所示：

```xml
<application ...>
  <activity android:name="microsoft.identity.client.BrowserTabActivity">
    <intent-filter>
      <action android:name="android.intent.action.VIEW" />
      <category android:name="android.intent.category.DEFAULT" />
      <category android:name="android.intent.category.BROWSABLE" />
      <data android:scheme="INSERT_URL_SCHEME_HERE" android:host="auth" />
    </intent-filter>
  </activity>
</application>
```

Azure Active Directory B2C 完成授权请求，它将重定向到注册的重定向 URL。 由于 URL 使用它会导致启动的移动应用程序的 Android 自定义方案，同时将 URL 传递作为启动参数，它由处理`microsoft.identity.client.BrowserTabActivity`。 请注意，`data android:scheme`属性必须设置为与 Azure Active Directory B2C 应用程序注册的自定义 URL 方案。

此外，`MainActivity`必须修改类，如下面的代码示例中所示：

```csharp
using Microsoft.Identity.Client;

namespace TodoAzure.Droid
{
    ...
    public class MainActivity : FormsAppCompatActivity
    {
        protected override void OnCreate(Bundle bundle)
        {
            base.OnCreate(bundle);

            global::Xamarin.Forms.Forms.Init(this, bundle);
            Microsoft.WindowsAzure.MobileServices.CurrentPlatform.Init();
            LoadApplication(new App());
            App.UiParent = new UIParent(this);
        }

        protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
        {
            base.OnActivityResult(requestCode, resultCode, data);
            AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode, resultCode, data);
        }
    }
}

```

`OnCreate`方法修改通过分配`UIParent`实例向`App.UiParent`属性。 这可确保身份验证流发生在当前活动的上下文中。

中的代码`OnActivityResult`方法可确保对 MSAL 返回控制后身份验证工作流的交互部分已结束。

### <a name="universal-windows-platform"></a>通用 Windows 平台

在通用 Windows 平台上，无需其他设置需要使用 MSAL。

## <a name="initialization"></a>初始化

Microsoft 身份验证库使用的成员`PublicClientApplication`启动身份验证工作流的类。 示例应用程序声明并初始化`public`名为此类型的属性`ADB2CClient`，请在`AuthenticationProvider`类。 下面的代码示例演示如何初始化此属性：

```csharp
ADB2CClient = new PublicClientApplication(Constants.ClientID, Constants.Authority);
```

移动应用程序已注册到 Azure Active Directory B2C 租户，注册过程分配**应用程序 ID**。 此 ID 必须以指定`PublicClientApplication`构造函数中，连同`Authority`常量，它包含要执行的基 URL，以及 Azure Active Directory B2C 策略。

## <a name="signing-in"></a>在登录

下面的屏幕截图显示了示例应用程序中的登录屏幕：

![](azure-ad-b2c-images/login.png "登录页")

单一登录与社交标识提供者，或使用本地帐户、 允许使用。 Microsoft、 Google 和 Facebook，如上所示，用作社交标识提供者时还可以使用其他标识提供者。

下面的代码示例演示如何调用登录过程：

```csharp
using Microsoft.Identity.Client;

public async Task<bool> LoginAsync(bool useSilent = false)
{
    ...
    AuthenticationResult authenticationResult = await ADB2CClient.AcquireTokenAsync(
        Constants.Scopes,
        GetUserByPolicy(ADB2CClient.Users, Constants.PolicySignUpSignIn),
        App.UiParent);
    ...
}

```

`AcquireTokenAsync`方法会启动设备的 web 浏览器并显示在通过引用的策略指定的 Azure Active Directory B2C 策略中定义的身份验证选项`Constants.Authority`常量。 此策略定义期间注册和登录中使用者将经历的体验和应用程序将接收在成功注册或登录的声明。

结果`AcquireTokenAsync`方法调用是`AuthenticationResult`实例。 如果身份验证成功，`AuthenticationResult`实例将包含一个标识令牌，将本地缓存。 如果身份验证不成功，`AuthenticationResult`实例将包含数据，该值指示身份验证失败的原因。

在示例应用程序，如果身份验证成功，`TodoList`页导航到。

## <a name="silent-re-authentication"></a>无提示重新进行身份验证

当`LoginPage`在示例应用程序出现，尝试检索用户令牌，而不显示任何身份验证的用户界面。 此，可以使用`AcquireTokenSilentAsync`方法，如下面的代码示例中所示：

```csharp
public async Task<bool> LoginAsync(bool useSilent = false)
{
    ...
    AuthenticationResult authenticationResult;

    if (useSilent)
    {
        authenticationResult = await ADB2CClient.AcquireTokenSilentAsync(
            Constants.Scopes,
            GetUserByPolicy(ADB2CClient.Users, Constants.PolicySignUpSignIn),
            Constants.Authority,
            false);
    }
    ...
}
```

`AcquireTokenSilentAsync`方法尝试从缓存中检索用户令牌，而无需用户登录。 这其中适合令牌可能已经出现在缓存中以前的会话处理方案。 如果成功，尝试获取令牌`TodoList`页导航到。 如果尝试获取的令牌不成功，会执行任何操作，所以用户必须启动新的身份验证工作流的选择。

## <a name="signing-out"></a>正在注销

下面的代码示例演示如何调用注销过程：

```csharp
public async Task<bool> LogoutAsync()
{
    ...
    foreach (var user in ADB2CClient.Users)
    {
        ADB2CClient.Remove(user);
    }
    ...
}
```

这将清除本地缓存中的所有身份验证令牌。

## <a name="summary"></a>总结

本文演示了如何使用 Microsoft 身份验证库 (MSAL) 和 Azure Active Directory B2C 将用户标识管理集成到移动应用程序。 Azure Active Directory B2C 是面向消费者的 web 和移动应用程序的云标识管理解决方案。


## <a name="related-links"></a>相关链接

- [AzureADB2CAuth （示例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/AzureADB2CAuth/)
- [Azure Active Directory B2C](/azure/active-directory-b2c/)
- [Microsoft 身份验证库](https://www.nuget.org/packages/Microsoft.Identity.Client)
