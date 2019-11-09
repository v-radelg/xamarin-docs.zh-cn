---
title: 使用标识提供程序的 AuthenticateUsers
description: 本文介绍如何使用 Xamarin 在 Xamarin. Forms 应用程序中管理身份验证过程。
ms.prod: xamarin
ms.assetid: D44745D5-77BB-4596-9B8C-EC75C259157C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2019
ms.openlocfilehash: 83fbad8a9bbb9afef5ee80705fe9e86e51284e7d
ms.sourcegitcommit: efbc69acf4ea484d8815311b058114379c9db8a2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73842984"
---
# <a name="authenticate-users-with-an-identity-provider"></a>使用标识提供者对用户进行身份验证

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-oauthnativeflow)

_Xamarin 是一个跨平台 SDK，用于对用户进行身份验证并存储帐户。它包括提供对使用标识提供者（如 Google、Microsoft、Facebook 和 Twitter）的支持的 OAuth 验证器。本文介绍如何使用 Xamarin 在 Xamarin. Forms 应用程序中管理身份验证过程。_

OAuth 是一种用于身份验证的开放标准，并使资源所有者可以通知资源提供程序，应将该权限授予第三方访问其信息的权限，而无需共享资源所有者标识。 这种情况的一个示例就是让用户通知标识提供者（如 Google、Microsoft、Facebook 或 Twitter）应授予应用程序访问其数据的权限，而无需共享用户的标识。 它通常用作用户使用标识提供者登录到网站和应用程序的方法，但不会向网站或应用程序公开其密码。

使用 OAuth 标识提供程序时，身份验证流的高级概述如下：

1. 应用程序将浏览器导航到标识提供程序 URL。
1. 标识提供程序处理用户身份验证，并将授权代码返回到应用程序。
1. 应用程序从标识提供程序中交换访问令牌的授权代码。
1. 应用程序使用访问令牌来访问标识提供者的 Api，如用于请求基本用户数据的 API。

示例应用程序演示如何使用 Xamarin 来实现针对 Google 的本机身份验证流。 虽然 Google 用作本主题中的标识提供者，但该方法同样适用于其他标识提供者。 有关使用 Google 的 OAuth 2.0 终结点进行身份验证的详细信息，请参阅[使用 OAuth 2.0 访问](https://developers.google.com/identity/protocols/OAuth2)google 网站上的 google api。

> [!NOTE]
> 在 iOS 9 及更高版本中，应用传输安全（ATS）在 internet 资源（如应用的后端服务器）和应用之间强制实施安全连接，从而防止意外泄漏敏感信息。 由于默认情况下在为 iOS 9 构建的应用中启用了 ATS，因此所有连接都将受到 ATS 的安全要求。 如果连接不满足这些要求，它们将失败并出现异常。
> 如果无法为 internet 资源使用 `HTTPS` 协议和安全通信，则可以选择不使用 ATS。 这可以通过更新应用的**info.plist**文件来实现。 有关详细信息，请参阅[应用传输安全性](~/ios/app-fundamentals/ats.md)。

## <a name="using-xamarinauth-to-authenticate-users"></a>使用 Xamarin 对用户进行身份验证

Xamarin 支持应用程序与标识提供者的授权终结点进行交互的两种方法：

1. 使用嵌入的 web 视图。 虽然这是一种常见的做法，但由于以下原因，不建议这样做：

    - 承载 web 视图的应用程序可以访问用户的完整身份验证凭据，而不只是适用于应用程序的 OAuth 授权授予。 这违反了最低权限原则，因为应用程序可以访问比所需权限更强大的凭据，这可能会增加应用程序的攻击面。
    - 主机应用程序可以捕获用户名和密码，自动提交表单并绕过用户同意，复制会话 cookie，并使用它们以用户身份执行身份验证操作。
    - 嵌入的 web 视图不会与其他应用程序或设备的 web 浏览器共享身份验证状态，要求用户为每个授权请求登录，这会被视为用户体验的不满。
    - 某些授权终结点采取措施来检测和阻止来自 web 视图的授权请求。

1. 使用设备的 web 浏览器，这是建议的方法。 使用适用于 OAuth 请求的设备浏览器可提高应用程序的可用性，因为用户只需登录到标识提供者每个设备一次，从而提高应用程序中登录和授权流的转换率。 由于应用程序能够在 web 视图中检查和修改内容，而不是在浏览器中显示的内容，因此设备浏览器还提供了更高的安全性。 这是本文和示例应用程序中所采用的方法。

下图显示了示例应用程序如何使用 Xamarin 对用户进行身份验证并检索其基本数据的高级概述：

![](oauth-images/google-auth.png "Using Xamarin.Auth to Authenticate with Google")

应用程序使用 `OAuth2Authenticator` 类向 Google 发出身份验证请求。 用户通过登录页面（包括访问令牌）成功通过 Google 身份验证后，将返回身份验证响应。 然后，应用程序使用 `OAuth2Request` 类向 Google 请求基本用户数据，并在请求中包含访问令牌。

### <a name="setup"></a>安装

必须创建 Google API 控制台项目，才能将 Google 登录与 Xamarin 应用程序集成。 这可以通过以下操作实现：

1. 请参阅[GOOGLE API 控制台](https://console.developers.google.com)网站，并以 Google 帐户凭据登录。
1. 从 "项目" 下拉箭头中，选择一个现有项目，或创建一个新项目。
1. 在 "API 管理器" 下的边栏中，选择 "**凭据**"，然后选择 " **OAuth 许可屏幕" 选项卡**。选择一个**电子邮件地址**，指定**向用户显示的产品名称**，然后按 "**保存**"。
1. 在 "**凭据**" 选项卡中，选择 "**创建凭据**" 下拉列表，然后选择 " **OAuth 客户端 ID**"。
1. 在 "**应用程序类型**" 下，选择移动应用程序将在其上运行的平台（**iOS**或**Android**）。
1. 填写所需的详细信息，然后选择 "**创建**" 按钮。

> [!NOTE]
> 客户端 ID 允许应用程序访问启用 Google Api，适用于移动应用程序对于单一平台是唯一的。 因此，应为将使用 Google 登录的每个平台创建**OAuth 客户端 ID** 。

执行这些步骤后，可以使用 Xamarin 通过 Google 启动 OAuth2 authentication flow。

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
- **作用域**–这会标识应用程序请求的 API 访问权限，值会通知向用户显示的同意屏幕。 有关作用域的详细信息，请参阅在 Google 网站上[授权 API 请求](https://developers.google.com/+/web/api/rest/oauth)。
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

当用户成功进行身份验证或取消登录时，将激发此事件。

还可以注册 `OAuth2Authenticator.Error` 事件的事件处理程序。

### <a name="presenting-the-sign-in-user-interface"></a>演示登录用户界面

可以通过使用 Xamarin 身份验证登录表示器向用户显示登录用户界面，必须在每个平台项目中对其进行初始化。 下面的代码示例演示如何在 iOS 项目中初始化 `AppDelegate` 类中的登录表示器：

```csharp
global::Xamarin.Auth.Presenters.XamarinIOS.AuthenticationConfiguration.Init();
```

下面的代码示例演示如何在 Android 项目中初始化 `MainActivity` 类中的登录表示器：

```csharp
global::Xamarin.Auth.Presenters.XamarinAndroid.AuthenticationConfiguration.Init(this, bundle);
```

然后 .NET Standard 库项目可以调用登录表示器，如下所示：

```csharp
var presenter = new Xamarin.Auth.Presenters.OAuthLoginPresenter();
presenter.Login(authenticator);
```

请注意，`Xamarin.Auth.Presenters.OAuthLoginPresenter.Login` 方法的参数是 `OAuth2Authenticator` 实例。 调用 `Login` 方法时，会在设备的 web 浏览器的选项卡中向用户显示登录用户界面，如以下屏幕截图所示：

![](oauth-images/login.png "Google Sign-In")

### <a name="processing-the-redirect-url"></a>处理重定向 URL

用户完成身份验证过程后，控件将从 web 浏览器选项卡返回到应用程序。为实现此目的，可以为从身份验证过程返回的重定向 URL 注册自定义 URL 方案，然后在发送自定义 URL 后对其进行检测和处理。

选择要与应用程序关联的自定义 URL 方案时，应用程序必须基于其控制下的名称使用方案。 这可以通过在 iOS 上使用捆绑标识符名称和 Android 上的包名称来实现，然后将其反向使用来生成 URL 方案。 但是，某些标识提供者（如 Google）将基于域名分配客户端标识符，然后将其反向并用作 URL 方案。 例如，如果 Google 创建 `902730282010-ks3kd03ksoasioda93jldas93jjj22kr.apps.googleusercontent.com`的客户端 id，则将 `com.googleusercontent.apps.902730282010-ks3kd03ksoasioda93jldas93jjj22kr`URL 方案。 请注意，方案组件后只能出现一个 `/`。 因此，`com.googleusercontent.apps.902730282010-ks3kd03ksoasioda93jldas93jjj22kr:/oauth2redirect`使用自定义 URL 方案的重定向 URL 的完整示例。

当 web 浏览器从包含自定义 URL 方案的标识提供者收到响应时，它将尝试加载 URL，这会失败。 而是通过引发事件向操作系统报告自定义 URL 方案。 然后，操作系统检查已注册的方案，如果找到，操作系统将启动注册该方案的应用程序，并将其发送到重定向 URL。

向操作系统注册自定义 URL 方案和处理方案的机制特定于每个平台。

#### <a name="ios"></a>iOS

在 iOS 上，会在**info.plist**中注册自定义 URL 方案，如以下屏幕截图所示：

![](oauth-images/info-plist.png "URL Scheme Registration")

**标识符**值可以是任何值，并且**Role**值必须设置为**查看器**。 **Url 方案**值以 `com.googleusercontent.apps`开头，可从[Google API 控制台](https://console.developers.google.com)上项目的 iOS 客户端 id 中获取。

标识提供者完成授权请求后，会重定向到应用程序的重定向 URL。 因为该 URL 使用自定义方案，所以它会导致 iOS 启动应用程序，并将该 URL 作为启动参数传入，该参数由应用程序 `AppDelegate` 类的 `OpenUrl` 重写进行处理，如以下代码示例所示：:

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

`OpenUrl` 方法先将接收的 URL 从 `NSUrl` 转换为 .NET `Uri`，然后再使用公共 `OAuth2Authenticator` 对象的 `OnPageLoading` 方法来处理重定向 URL。 这将导致 Xamarin 关闭 web 浏览器选项卡，并分析收到的 OAuth 数据。

#### <a name="android"></a>Android

在 Android 上，通过在将处理方案的 `Activity` 上指定[`IntentFilter`](xref:Android.App.IntentFilterAttribute)特性来注册自定义 URL 方案。 标识提供者完成授权请求后，会重定向到应用程序的重定向 URL。 因为 URL 使用自定义方案，所以它会导致 Android 启动应用程序，并将 URL 作为启动参数传入，该参数由注册的 `Activity` 的 `OnCreate` 方法进行处理，以处理自定义 URL 方案。 下面的代码示例演示了处理自定义 URL 方案的示例应用程序中的类：

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

`OnCreate` 方法先将接收的 URL 从 `Android.Net.Url` 转换为 .NET `Uri`，然后再使用公共 `OAuth2Authenticator` 对象的 `OnPageLoading` 方法来处理重定向 URL。 这将导致 Xamarin 关闭 web 浏览器选项卡，并分析收到的 OAuth 数据。

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

成功的身份验证所收集的数据将在 `AuthenticatorCompletedEventArgs.Account` 属性中提供。 这包括一个访问令牌，可用于对标识提供者提供的 API 的数据请求进行签名。

### <a name="making-requests-for-data"></a>发出数据请求

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

`OAuth2Request` 实例还指定了一个 `Account` 实例，该实例包含用于对由 `Constants.UserInfoUrl` 属性指定的 URL 签名请求的访问令牌。 然后，标识提供者将基本用户数据作为 JSON 响应返回，包括用户的姓名和电子邮件地址，前提是它将访问令牌识别为有效。 然后读取 JSON 响应，并将其反序列化为 `user` 变量。

有关详细信息，请参阅 Google 开发人员门户上的[调用 GOOGLE API](https://developers.google.com/identity/protocols/OAuth2InstalledApp#callinganapi) 。

### <a name="storing-and-retrieving-account-information-on-devices"></a>在设备上存储和检索帐户信息

Xamarin 将 `Account` 对象安全地存储在帐户存储中，以便应用程序不必总是重新对用户进行身份验证。 `AccountStore` 类负责存储帐户信息，并由 iOS 中的密钥链 services 和 Android 中的 `KeyStore` 类提供支持。

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

## <a name="troubleshooting"></a>疑难解答

- 在 Android 上，如果在身份验证后关闭浏览器时收到 toast 通知，并且想要停止 toast 通知，请在初始化 Xamarin 之后将以下代码添加到 Android 项目中：

```csharp
Xamarin.Auth.CustomTabsConfiguration.CustomTabsClosingMessage = null;
```

- 在 Android 上，如果浏览器未自动关闭，则一个暂时的解决方法是将 Xamarin. authentication 包降级到版本1.5.0.3。 然后，将[PCL 加密 v 2.0.147](https://www.nuget.org/packages/PCLCrypto/2.0.147)添加到 Android 项目。

## <a name="summary"></a>总结

本文介绍了如何使用 Xamarin 在 Xamarin. Forms 应用程序中管理身份验证过程。 Xamarin 提供 Xamarin 使用的 `OAuth2Authenticator` 和 `OAuth2Request` 类。 Forms 应用程序使用诸如 Google、Microsoft、Facebook 和 Twitter 等标识提供者。

## <a name="related-links"></a>相关链接

- [OAuthNativeFlow （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-oauthnativeflow)
- [适用于本机应用的 OAuth 2。0](https://tools.ietf.org/html/draft-ietf-oauth-native-apps-12)
- [使用 OAuth 2.0 访问 Google Api](https://developers.google.com/identity/protocols/OAuth2)
- [Xamarin （NuGet）](https://www.nuget.org/packages/xamarin.auth/)
- [Xamarin （GitHub）](https://github.com/xamarin/Xamarin.Auth)
