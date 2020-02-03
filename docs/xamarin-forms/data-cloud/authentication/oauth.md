---
title: 使用标识提供程序的 AuthenticateUsers
description: 本文介绍如何使用 Xamarin.Auth 管理 Xamarin.Forms 应用程序中的身份验证过程。
ms.prod: xamarin
ms.assetid: D44745D5-77BB-4596-9B8C-EC75C259157C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2019
ms.openlocfilehash: 0fa433de7fd1acb6fb27741f1615a644315f373f
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76725557"
---
# <a name="authenticate-users-with-an-identity-provider"></a>使用标识提供者对用户进行身份验证

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-oauthnativeflow)

_Xamarin 是一个跨平台 SDK，用于对用户进行身份验证并存储帐户。它包括提供对使用标识提供者（如 Google、Microsoft、Facebook 和 Twitter）的支持的 OAuth 验证器。本文介绍如何使用 Xamarin 在 Xamarin. Forms 应用程序中管理身份验证过程。_

OAuth 是开放式的标准进行身份验证，并启用通知资源提供程序应授予权限的第三方而无需共享资源所有者标识访问其信息的资源所有者。 此示例将启用通知 （如 Google、 Microsoft、 Facebook 或 Twitter） 的标识提供者，将向应用程序访问其数据，而无需共享用户的标识授予权限的用户。 它通常用于作为用户的方法登录到网站和应用程序使用标识提供程序，但不公开到网站或应用程序密码。

使用 OAuth 标识提供程序时的身份验证流的高级概述如下所示：

1. 应用程序导航到标识提供者 URL 的浏览器。
1. 标识提供程序处理用户身份验证，并将授权代码返回到应用程序。
1. 应用程序与其交换中的标识提供程序的访问令牌的授权代码。
1. 应用程序使用访问令牌来访问标识提供程序，如请求基本用户数据的 API 的 Api。

示例应用程序演示了如何使用 Xamarin.Auth 实现本机身份验证流对 Google。 虽然 Google 用作本主题中的标识提供程序，方法是同样适用于其他标识提供者。 有关使用 Google 的 OAuth 2.0 终结点进行身份验证的详细信息，请参阅[使用 OAuth 2.0 访问](https://developers.google.com/identity/protocols/OAuth2)google 网站上的 google api。

> [!NOTE]
> 在 iOS 9 及更高版本中，应用传输安全 (ATS) 强制执行安全连接之间 （例如应用程序的后端服务器） 的 internet 资源和应用程序中，从而防止意外泄露敏感信息。 由于默认情况下构建适用于 iOS 9 应用程序中启用了 ATS，所有连接都将遵循 ATS 安全要求。 如果连接不能满足这些要求，它们将失败并出现异常。
> 如果无法为 internet 资源使用 `HTTPS` 协议和安全通信，则可以选择不使用 ATS。 这可以通过更新应用的**info.plist**文件来实现。 有关详细信息，请参阅[应用传输安全性](~/ios/app-fundamentals/ats.md)。

## <a name="using-xamarinauth-to-authenticate-users"></a>使用 Xamarin.Auth 用户进行身份验证

Xamarin.Auth 支持两种方法的应用程序与标识提供程序的授权终结点进行交互：

1. 使用嵌入式的 web 视图。 虽然这是一种常见做法，但不再建议出于以下原因：

    - 承载 web 视图的应用程序可以访问用户的完全身份验证凭据，而不仅仅是适用于应用程序的 OAuth 授权授予。 这违反了最低特权原则，如应用程序有权访问更强大的凭据不是它需要有可能增加应用程序的受攻击面。
    - 主机应用程序能捕获用户名和密码、 自动提交窗体和绕过用户同意，和复制会话 cookie 并使用它们的用户身份执行已经过身份验证的操作。
    - 嵌入式的 web 视图不与其他应用程序或设备的 web 浏览器，因此需要用户在为每个授权请求，它被视为较差的用户体验的登录共享身份验证状态。
    - 某些授权终结点采取措施来检测和阻止来自 web 视图的授权请求。

1. 使用设备的 web 浏览器中，这是建议的方法。 使用设备浏览器进行 OAuth 请求可提高应用程序的可用性，因为用户只需登录到标识提供程序一次每个设备，从而提高转换率的应用程序中的登录和授权流程。 设备浏览器还提供了改进的安全性，如应用程序都能够检查和修改内容在 web 视图中，但不是显示在浏览器中的内容。 这是此文章和示例应用程序中采用的方法。

下图中所示的示例应用程序如何使用 Xamarin.Auth 对用户进行身份验证和检索其基本数据的高级概述：

![](oauth-images/google-auth.png "Using Xamarin.Auth to Authenticate with Google")

应用程序使用 `OAuth2Authenticator` 类向 Google 发出身份验证请求。 返回身份验证响应时，在用户已成功通过身份验证 Google 通过其在登录页面，其中包括访问令牌。 然后，应用程序使用 `OAuth2Request` 类向 Google 请求基本用户数据，并在请求中包含访问令牌。

### <a name="setup"></a>安装

必须创建一个 Google API 控制台项目与 Xamarin.Forms 应用程序集成，Google 登录。 这可以通过以下操作实现：

1. 请参阅[GOOGLE API 控制台](https://console.developers.google.com)网站，并以 Google 帐户凭据登录。
1. 从项目下拉列表中选择一个现有项目，或创建一个新。
1. 在 "API 管理器" 下的边栏中，选择 "**凭据**"，然后选择 " **OAuth 许可屏幕" 选项卡**。选择一个**电子邮件地址**，指定**向用户显示的产品名称**，然后按 "**保存**"。
1. 在 "**凭据**" 选项卡中，选择 "**创建凭据**" 下拉列表，然后选择 " **OAuth 客户端 ID**"。
1. 在 "**应用程序类型**" 下，选择移动应用程序将在其上运行的平台（**iOS**或**Android**）。
1. 填写所需的详细信息，然后选择 "**创建**" 按钮。

> [!NOTE]
> 客户端 ID 可让应用程序访问已启用的 Google Api，并为移动应用程序仅适用于单个平台。 因此，应为将使用 Google 登录的每个平台创建**OAuth 客户端 ID** 。

执行这些步骤后，Xamarin.Auth 可用于启动使用 Google 的 OAuth2 身份验证流。

### <a name="creating-and-configuring-an-authenticator"></a>创建和配置身份验证器

Xamarin `OAuth2Authenticator` 类负责处理 OAuth 身份验证流。 下面的代码示例演示了使用设备的 web 浏览器执行身份验证时 `OAuth2Authenticator` 类的实例化：

```csharp
var authenticator = new OAuth2Authenticator(
    clientId,
    null,
    Constants.Scope,
    new Uri(Constants.AuthorizeUrl),
    new Uri(redirectUri),
    new Uri(Constants.AccessTokenUrl),
    null,
    true);
```

`OAuth2Authenticator` 类需要许多参数，如下所示：

- **客户端 ID** –此项标识发出请求的客户端，并且可以从[Google API 控制台](https://console.developers.google.com)中的项目检索。
- **客户端机密**–应 `null` 或 `string.Empty`。
- **作用域**–这会标识应用程序请求的 API 访问权限，值会通知向用户显示的同意屏幕。 有关范围的详细信息，请参阅在 Google 网站上[授权请求](https://developers.google.com/docs/api/how-tos/authorizing)。
- **授权 url** –标识从中获取授权代码的 url。
- **重定向 url** –标识将发送响应的 url。 此参数的值必须与[Google 开发人员控制台](https://console.developers.google.com/)中项目的 "**凭据**" 选项卡中显示的值之一匹配。
- **AccessToken url** –标识在获取授权代码后用于请求访问令牌的 url。
- **GetUserNameAsync Func** –可选的 `Func`，在成功通过身份验证后，将用于异步检索帐户的用户名。
- **使用本机 UI** –一个 `boolean` 值，该值指示是否使用设备的 web 浏览器来执行身份验证请求。

### <a name="setup-authentication-event-handlers"></a>设置身份验证事件处理程序

在呈现用户界面之前，必须注册 `OAuth2Authenticator.Completed` 事件的事件处理程序，如下面的代码示例所示：

```csharp
authenticator.Completed += OnAuthCompleted;
```

在用户成功进行身份验证或取消登录时，会触发此事件。

还可以注册 `OAuth2Authenticator.Error` 事件的事件处理程序。

### <a name="presenting-the-sign-in-user-interface"></a>显示登录用户界面

通过使用必须在每个平台项目中初始化 Xamarin.Auth 登录表示器，可以向用户显示登录用户界面。 下面的代码示例演示如何在 iOS 项目中初始化 `AppDelegate` 类中的登录表示器：

```csharp
global::Xamarin.Auth.Presenters.XamarinIOS.AuthenticationConfiguration.Init();
```

下面的代码示例演示如何在 Android 项目中初始化 `MainActivity` 类中的登录表示器：

```csharp
global::Xamarin.Auth.Presenters.XamarinAndroid.AuthenticationConfiguration.Init(this, bundle);
```

.NET Standard 库项目然后可以按如下所示调用登录演示者：

```csharp
var presenter = new Xamarin.Auth.Presenters.OAuthLoginPresenter();
presenter.Login(authenticator);
```

请注意，`Xamarin.Auth.Presenters.OAuthLoginPresenter.Login` 方法的参数是 `OAuth2Authenticator` 实例。 调用 `Login` 方法时，会在设备的 web 浏览器的选项卡中向用户显示登录用户界面，如以下屏幕截图所示：

![](oauth-images/login.png "Google Sign-In")

### <a name="processing-the-redirect-url"></a>处理重定向 URL

用户完成身份验证过程后，控件将从 web 浏览器选项卡返回到应用程序。为实现此目的，可以为从身份验证过程返回的重定向 URL 注册自定义 URL 方案，然后在发送自定义 URL 后对其进行检测和处理。

选择要与应用程序关联的自定义 URL 方案，应用程序必须使用基于名称受其控制的方案。 这可以通过 iOS 和 Android 上的包名称上使用捆绑包标识符名称，并反转，以便 URL 方案来实现。 但是，某些标识提供者，如 Google，分配基于域的名称，然后反转并用作 URL 方案的客户端标识符。 例如，如果 Google 创建 `902730282010-ks3kd03ksoasioda93jldas93jjj22kr.apps.googleusercontent.com`的客户端 id，则将 `com.googleusercontent.apps.902730282010-ks3kd03ksoasioda93jldas93jjj22kr`URL 方案。 请注意，方案组件后只能出现一个 `/`。 因此，`com.googleusercontent.apps.902730282010-ks3kd03ksoasioda93jldas93jjj22kr:/oauth2redirect`使用自定义 URL 方案的重定向 URL 的完整示例。

Web 浏览器收到响应后从包含自定义的 URL 方案的标识提供程序，它尝试加载的 URL，将会失败。 相反，自定义 URL 方案被报告给操作系统引发的事件。 对于已注册的架构，然后检查操作系统和操作系统如果找到一个对象，将启动应用程序注册方案，并将其发送重定向 URL。

用于向操作系统注册自定义的 URL 方案和处理方案的机制是特定于每个平台。

#### <a name="ios"></a>iOS

在 iOS 上，会在**info.plist**中注册自定义 URL 方案，如以下屏幕截图所示：

![](oauth-images/info-plist.png "URL Scheme Registration")

**标识符**值可以是任何值，并且**Role**值必须设置为**查看器**。 **Url 方案**值以 `com.googleusercontent.apps`开头，可从[Google API 控制台](https://console.developers.google.com)上项目的 iOS 客户端 id 中获取。

标识提供程序完成授权请求，它将重定向到应用程序的重定向 URL。 因为该 URL 使用自定义方案，所以它会导致 iOS 启动应用程序，并将该 URL 作为启动参数传入，该参数由应用程序 `AppDelegate` 类的 `OpenUrl` 重写进行处理，如以下代码示例所示：

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    // Convert NSUrl to Uri
    var uri = new Uri(url.AbsoluteString);

    // Load redirectUrl page
    AuthenticationState.Authenticator.OnPageLoading(uri);

    return true;
}
```

`OpenUrl` 方法先将接收的 URL 从 `NSUrl` 转换为 .NET `Uri`，然后再使用公共 `OAuth2Authenticator` 对象的 `OnPageLoading` 方法来处理重定向 URL。 这将导致 Xamarin.Auth 以关闭 web 浏览器选项卡上，并将收到的 OAuth 数据分析。

#### <a name="android"></a>Android

在 Android 上，通过在将处理方案的 `Activity` 上指定[`IntentFilter`](xref:Android.App.IntentFilterAttribute)特性来注册自定义 URL 方案。 标识提供程序完成授权请求，它将重定向到应用程序的重定向 URL。 因为 URL 使用自定义方案，所以它会导致 Android 启动应用程序，并将 URL 作为启动参数传入，该参数由注册的 `Activity` 的 `OnCreate` 方法进行处理，以处理自定义 URL 方案。 下面的代码示例显示了处理自定义 URL 方案的示例应用程序中的类：

```csharp
[Activity(Label = "CustomUrlSchemeInterceptorActivity", NoHistory = true, LaunchMode = LaunchMode.SingleTop )]
[IntentFilter(
    new[] { Intent.ActionView },
    Categories = new [] { Intent.CategoryDefault, Intent.CategoryBrowsable },
    DataSchemes = new [] { "<insert custom URL here>" },
    DataPath = "/oauth2redirect")]
public class CustomUrlSchemeInterceptorActivity : Activity
{
    protected override void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);

        // Convert Android.Net.Url to Uri
        var uri = new Uri(Intent.Data.ToString());

        // Load redirectUrl page
        AuthenticationState.Authenticator.OnPageLoading(uri);

        Finish();
    }
}
```

[`IntentFilter`](xref:Android.App.IntentFilterAttribute)的 `DataSchemes` 属性必须设置为在[Google API 控制台](https://console.developers.google.com)上从项目的 Android 客户端 id 获得的反向客户端标识符。

`OnCreate` 方法先将接收的 URL 从 `Android.Net.Url` 转换为 .NET `Uri`，然后再使用公共 `OAuth2Authenticator` 对象的 `OnPageLoading` 方法来处理重定向 URL。 这将导致 Xamarin.Auth 关闭 web 浏览器选项卡上，并将收到的 OAuth 数据分析。

> [!IMPORTANT]
> 在 Android 上，Xamarin 使用 `CustomTabs` API 与 web 浏览器和操作系统通信。 但是，不能保证在用户的设备上安装 `CustomTabs` 兼容的浏览器。

### <a name="examining-the-oauth-response"></a>检查 OAuth 响应

分析收到的 OAuth 数据之后，Xamarin 会引发 `OAuth2Authenticator.Completed` 事件。 在此事件的事件处理程序中，可以使用 `AuthenticatorCompletedEventArgs.IsAuthenticated` 属性来标识身份验证是否成功，如下面的代码示例所示：

```csharp
async void OnAuthCompleted(object sender, AuthenticatorCompletedEventArgs e)
{
  ...
  if (e.IsAuthenticated)
  {
    ...
  }
}
```

成功的身份验证所收集的数据将在 `AuthenticatorCompletedEventArgs.Account` 属性中提供。 这包括访问令牌，可以使用数据传输到标识提供程序提供的 API 的请求进行签名。

### <a name="making-requests-for-data"></a>发出请求的数据

应用程序获取访问令牌后，它将用于向 `https://www.googleapis.com/oauth2/v2/userinfo` API 发出请求，以从标识提供者请求基本用户数据。 此请求是通过 Xamarin `OAuth2Request` 类发出的，它表示使用从 `OAuth2Authenticator` 实例检索的帐户进行身份验证的请求，如下面的代码示例所示：

```csharp
// UserInfoUrl = https://www.googleapis.com/oauth2/v2/userinfo
var request = new OAuth2Request ("GET", new Uri (Constants.UserInfoUrl), null, e.Account);
var response = await request.GetResponseAsync ();
if (response != null)
{
  string userJson = response.GetResponseText ();
  var user = JsonConvert.DeserializeObject<User> (userJson);
}
```

`OAuth2Request` 实例还指定了一个 `Account` 实例，该实例包含用于对由 `Constants.UserInfoUrl` 属性指定的 URL 签名请求的访问令牌。 标识提供者然后返回作为 JSON 响应，包括用户的名称和电子邮件地址，前提它识别为有效的访问令牌的基本用户数据。 然后读取 JSON 响应，并将其反序列化为 `user` 变量。

有关详细信息，请参阅 Google 开发人员门户上的[调用 GOOGLE API](https://developers.google.com/identity/protocols/OAuth2InstalledApp#callinganapi) 。

### <a name="storing-and-retrieving-account-information-on-devices"></a>存储和检索设备上的帐户信息

Xamarin 将 `Account` 对象安全地存储在帐户存储中，以便应用程序不必总是重新对用户进行身份验证。 `AccountStore` 类负责存储帐户信息，并由 iOS 中的密钥链 services 和 Android 中的 `KeyStore` 类提供支持。

> [!IMPORTANT]
> Xamarin 中的 `AccountStore` 类已弃用，应改为使用 Xamarin `SecureStorage` 类。 有关详细信息，请参阅[从 AccountStore 迁移到 Xamarin SecureStorage](https://github.com/xamarin/Xamarin.Auth/wiki/Migrating-from-AccountStore-to-Xamarin.Essentials-SecureStorage)。

下面的代码示例演示如何安全保存 `Account` 对象：

```csharp
AccountStore.Create ().Save (e.Account, Constants.AppName);
```

已保存的帐户使用帐户的 `Username` 属性和服务 ID （从帐户存储提取帐户时使用的字符串）进行唯一标识。 如果之前保存了 `Account`，则再次调用 `Save` 方法将覆盖它。

可以通过调用 `FindAccountsForService` 方法来检索服务 `Account` 对象，如下面的代码示例所示：

```csharp
var account = AccountStore.Create ().FindAccountsForService (Constants.AppName).FirstOrDefault();
```

`FindAccountsForService` 方法返回 `Account` 对象的 `IEnumerable` 集合，并将集合中的第一项设置为匹配的帐户。

## <a name="troubleshooting"></a>故障排除

- 在 Android 上，如果在身份验证后关闭浏览器时收到 toast 通知，并且想要停止 toast 通知，请在初始化 Xamarin 之后将以下代码添加到 Android 项目中：

```csharp
Xamarin.Auth.CustomTabsConfiguration.CustomTabsClosingMessage = null;
```

- 在 Android 上，如果浏览器未自动关闭，则一个暂时的解决方法是将 Xamarin. authentication 包降级到版本1.5.0.3。 然后，将[PCL 加密 v 2.0.147](https://www.nuget.org/packages/PCLCrypto/2.0.147)添加到 Android 项目。

## <a name="summary"></a>摘要

本文介绍了如何使用 Xamarin.Auth 管理 Xamarin.Forms 应用程序中的身份验证过程。 Xamarin 提供 Xamarin 使用的 `OAuth2Authenticator` 和 `OAuth2Request` 类。 Forms 应用程序使用诸如 Google、Microsoft、Facebook 和 Twitter 等标识提供者。

## <a name="related-links"></a>相关链接

- [OAuthNativeFlow （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-oauthnativeflow)
- [适用于本机应用的 OAuth 2。0](https://tools.ietf.org/html/draft-ietf-oauth-native-apps-12)
- [使用 OAuth 2.0 访问 Google Api](https://developers.google.com/identity/protocols/OAuth2)
- [Xamarin （NuGet）](https://www.nuget.org/packages/xamarin.auth/)
- [Xamarin （GitHub）](https://github.com/xamarin/Xamarin.Auth)
