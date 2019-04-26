---
title: 与标识提供程序的用户进行身份验证
description: 本文介绍如何使用 Xamarin.Auth 管理 Xamarin.Forms 应用程序中的身份验证过程。
ms.prod: xamarin
ms.assetid: D44745D5-77BB-4596-9B8C-EC75C259157C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/19/2017
ms.openlocfilehash: 786f1503fcc0cc07f76a7cdc55731d341607429f
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61331091"
---
# <a name="authenticating-users-with-an-identity-provider"></a>与标识提供程序的用户进行身份验证

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/WebServices/OAuthNativeFlow/)

_Xamarin.Auth 是用于对用户进行身份验证和存储其帐户的跨平台 SDK。它包括对使用 Google、 Microsoft、 Facebook 和 Twitter 等标识提供程序提供支持的 OAuth 身份验证器。本文介绍如何使用 Xamarin.Auth 管理 Xamarin.Forms 应用程序中的身份验证过程。_

OAuth 是开放式的标准进行身份验证，并启用通知资源提供程序应授予权限的第三方而无需共享资源所有者标识访问其信息的资源所有者。 此示例将启用通知 （如 Google、 Microsoft、 Facebook 或 Twitter） 的标识提供者，将向应用程序访问其数据，而无需共享用户的标识授予权限的用户。 它通常用于作为用户的方法登录到网站和应用程序使用标识提供程序，但不公开到网站或应用程序密码。

使用 OAuth 标识提供程序时的身份验证流的高级概述如下所示：

1. 应用程序导航到标识提供者 URL 的浏览器。
1. 标识提供程序处理用户身份验证，并将授权代码返回到应用程序。
1. 应用程序与其交换中的标识提供程序的访问令牌的授权代码。
1. 应用程序使用访问令牌来访问标识提供程序，如请求基本用户数据的 API 的 Api。

示例应用程序演示了如何使用 Xamarin.Auth 实现本机身份验证流对 Google。 虽然 Google 用作本主题中的标识提供程序，方法是同样适用于其他标识提供者。 有关使用 Google OAuth 2.0 终结点身份验证的详细信息，请参阅[访问 Google api 使用 OAuth2.0](https://developers.google.com/identity/protocols/OAuth2) Google 的网站上。

> [!NOTE]
> 在 iOS 9 及更高版本中，应用传输安全 (ATS) 强制执行安全连接之间 （例如应用程序的后端服务器） 的 internet 资源和应用程序中，从而防止意外泄露敏感信息。 由于默认情况下构建适用于 iOS 9 应用程序中启用了 ATS，所有连接都将遵循 ATS 安全要求。 如果连接不能满足这些要求，它们将失败并出现异常。
> 如果不能使用 ATS 可以选择退出的`HTTPS`协议并确保对 internet 资源的通信安全。 这可以通过更新应用程序的实现**Info.plist**文件。 有关详细信息请参阅[应用程序传输安全](~/ios/app-fundamentals/ats.md)。

## <a name="using-xamarinauth-to-authenticate-users"></a>使用 Xamarin.Auth 用户进行身份验证

Xamarin.Auth 支持两种方法的应用程序与标识提供程序的授权终结点进行交互：

1. 使用嵌入式的 web 视图。 虽然这是一种常见做法，但不再建议出于以下原因：

    - 承载 web 视图的应用程序可以访问用户的完全身份验证凭据，而不仅仅是适用于应用程序的 OAuth 授权授予。 这违反了最低特权原则，如应用程序有权访问更强大的凭据不是它需要有可能增加应用程序的受攻击面。
    - 主机应用程序能捕获用户名和密码、 自动提交窗体和绕过用户同意，和复制会话 cookie 并使用它们的用户身份执行已经过身份验证的操作。
    - 嵌入式的 web 视图不与其他应用程序或设备的 web 浏览器，因此需要用户在为每个授权请求，它被视为较差的用户体验的登录共享身份验证状态。
    - 某些授权终结点采取措施来检测和阻止来自 web 视图的授权请求。

1. 使用设备的 web 浏览器中，这是建议的方法。 使用设备浏览器进行 OAuth 请求可提高应用程序的可用性，因为用户只需登录到标识提供程序一次每个设备，从而提高转换率的应用程序中的登录和授权流程。 设备浏览器还提供了改进的安全性，如应用程序都能够检查和修改内容在 web 视图中，但不是显示在浏览器中的内容。 这是此文章和示例应用程序中采用的方法。

下图中所示的示例应用程序如何使用 Xamarin.Auth 对用户进行身份验证和检索其基本数据的高级概述：

![](oauth-images/google-auth.png "使用 Xamarin.Auth 使用 Google 进行身份验证")

应用程序发出对使用 Google 的身份验证请求`OAuth2Authenticator`类。 返回身份验证响应时，在用户已成功通过身份验证 Google 通过其在登录页面，其中包括访问令牌。 然后，应用程序的基本用户数据，使用 Google 向发出请求`OAuth2Request`类，包含在请求中的访问令牌。

### <a name="setup"></a>安装

必须创建一个 Google API 控制台项目与 Xamarin.Forms 应用程序集成，Google 登录。 这可以通过以下操作实现：

1. 转到[Google API 控制台](http://console.developers.google.com)网站，然后使用 Google 帐户凭据登录。
1. 从项目下拉列表中选择一个现有项目，或创建一个新。
1. 在"API 管理器"下侧栏中选择**凭据**，然后选择**OAuth 许可屏幕选项卡**。选择**电子邮件地址**，指定**向用户显示产品名称**，然后按**保存**。
1. 在中**凭据**选项卡上，选择**创建凭据**下拉列表，并选择**OAuth 客户端 ID**。
1. 下**应用程序类型**，选择移动应用程序将在运行的平台 (**iOS**或**Android**)。
1. 填写所需的详细信息，然后选择**创建**按钮。

> [!NOTE]
> 客户端 ID 可让应用程序访问已启用的 Google Api，并为移动应用程序仅适用于单个平台。 因此， **OAuth 客户端 ID**应创建为将使用 Google 登录每个平台。

执行这些步骤后，Xamarin.Auth 可用于启动使用 Google 的 OAuth2 身份验证流。

### <a name="creating-and-configuring-an-authenticator"></a>创建和配置身份验证器

Xamarin.Auth 的`OAuth2Authenticator`类负责处理 OAuth 身份验证流。 下面的代码示例演示的实例化`OAuth2Authenticator`类执行身份验证设备的 web 浏览器时：

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

`OAuth2Authenticator`类需要多个参数，如下所示：

- **客户端 ID** – 此标识正在发出请求，并且可以从项目中检索的客户端[Google API 控制台](http://console.developers.google.com)。
- **客户端机密**– 这应该是`null`或`string.Empty`。
- **作用域**– 此标识符可标识正在请求应用程序的 API 访问权限和值通知向用户显示的许可屏幕。 有关作用域的详细信息，请参阅[授权 API 请求](https://developers.google.com/+/web/api/rest/oauth)Google 的网站上。
- **授权 URL** – 此标识将来获取授权代码的 URL。
- **重定向 URL** – 此标识，以便进行发送响应的 URL。 此参数的值必须匹配中显示的值之一**凭据**选项卡中的项目[Google 开发人员控制台](https://console.developers.google.com/)。
- **AccessToken Url** – 这标识用于获取授权代码后请求访问令牌的 URL。
- **GetUserNameAsync Func** – 一个可选的`Func`将用于它已成功完成身份验证后以异步方式检索帐户的用户名。
- **使用本机 UI** – 一`boolean`值，该值指示是否使用设备的 web 浏览器来执行身份验证请求。

### <a name="setup-authentication-event-handlers"></a>设置身份验证事件处理程序

显示用户界面，事件处理程序之前`OAuth2Authenticator.Completed`必须注册事件，如下面的代码示例中所示：

```csharp
authenticator.Completed += OnAuthCompleted;
```

在用户成功进行身份验证或取消登录时，会触发此事件。

（可选） 的事件处理程序`OAuth2Authenticator.Error`也可以注册事件。

### <a name="presenting-the-sign-in-user-interface"></a>显示登录用户界面

通过使用必须在每个平台项目中初始化 Xamarin.Auth 登录表示器，可以向用户显示登录用户界面。 下面的代码示例演示如何初始化中的登录名演示者`AppDelegate`iOS 项目中的类：

```csharp
global::Xamarin.Auth.Presenters.XamarinIOS.AuthenticationConfiguration.Init();
```

下面的代码示例演示如何初始化中的登录名演示者`MainActivity`Android 项目中的类：

```csharp
global::Xamarin.Auth.Presenters.XamarinAndroid.AuthenticationConfiguration.Init(this, bundle);
```

.NET Standard 库项目然后可以按如下所示调用登录演示者：

```csharp
var presenter = new Xamarin.Auth.Presenters.OAuthLoginPresenter();
presenter.Login(authenticator);
```

请注意，参数`Xamarin.Auth.Presenters.OAuthLoginPresenter.Login`方法是`OAuth2Authenticator`实例。 当`Login`调用方法、 登录用户界面显示为一个选项卡中的用户从设备的 web 浏览器中，下面的屏幕截图中所示：

![](oauth-images/login.png "Google 登录")

### <a name="processing-the-redirect-url"></a>处理重定向 URL

用户完成身份验证过程后，控件将在 web 浏览器选项卡中，返回到应用程序。这被通过重定向 url 返回的身份验证过程中，检测并处理自定义 URL 后将其发送注册自定义的 URL 方案。

选择要与应用程序关联的自定义 URL 方案，应用程序必须使用基于名称受其控制的方案。 这可以通过 iOS 和 Android 上的包名称上使用捆绑包标识符名称，并反转，以便 URL 方案来实现。 但是，某些标识提供者，如 Google，分配基于域的名称，然后反转并用作 URL 方案的客户端标识符。 例如，如果 Google 创建的客户端 id `902730282010-ks3kd03ksoasioda93jldas93jjj22kr.apps.googleusercontent.com`，将为 URL 方案`com.googleusercontent.apps.902730282010-ks3kd03ksoasioda93jldas93jjj22kr`。 请注意，只有一个`/`方案组件后可以显示。 因此，利用自定义的 URL 方案的重定向 URL 的完整示例是`com.googleusercontent.apps.902730282010-ks3kd03ksoasioda93jldas93jjj22kr:/oauth2redirect`。

Web 浏览器收到响应后从包含自定义的 URL 方案的标识提供程序，它尝试加载的 URL，将会失败。 相反，自定义 URL 方案被报告给操作系统引发的事件。 对于已注册的架构，然后检查操作系统和操作系统如果找到一个对象，将启动应用程序注册方案，并将其发送重定向 URL。

用于向操作系统注册自定义的 URL 方案和处理方案的机制是特定于每个平台。

#### <a name="ios"></a>iOS

在 iOS 上，自定义的 URL 方案中注册**Info.plist**，如以下屏幕截图中所示：

![](oauth-images/info-plist.png "注册 URL 方案")

**标识符**值可以是任何内容，并**角色**值必须设置为**查看器**。 **Url 方案**值，开头`com.googleusercontent.apps`，可以从项目的 iOS 客户端 id 获取上[Google API 控制台](http://console.developers.google.com)。

标识提供程序完成授权请求，它将重定向到应用程序的重定向 URL。 由于 URL 使用自定义方案，这样可以在 iOS 中启动应用程序，同时将 URL 传递作为启动参数，它由处理`OpenUrl`在应用程序的重写`AppDelegate`类，该类在下面的代码示例所示：

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

`OpenUrl`方法将从接收到的 URL 转换`NSUrl`到.NET `Uri`，然后再处理与重定向 URL`OnPageLoading`公共方法`OAuth2Authenticator`对象。 这将导致 Xamarin.Auth 以关闭 web 浏览器选项卡上，并将收到的 OAuth 数据分析。

#### <a name="android"></a>Android

在 Android 上，自定义的 URL 方案注册通过指定[ `IntentFilter` ](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/)特性，可以在`Activity`，用于处理方案。 标识提供程序完成授权请求，它将重定向到应用程序的重定向 URL。 为 URL 使用自定义方案，这样可以在 Android 中启动应用程序，同时将 URL 传递作为启动参数，它由处理`OnCreate`方法的`Activity`注册用于处理自定义 URL 方案。 下面的代码示例显示了处理自定义 URL 方案的示例应用程序中的类：

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

`DataSchemes`的属性[ `IntentFilter` ](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/)必须设置为在获取项目的 Android 客户端 id 的反向客户端标识符[Google API 控制台](http://console.developers.google.com)。

`OnCreate`方法将从接收到的 URL 转换`Android.Net.Url`到.NET `Uri`，然后再处理与重定向 URL`OnPageLoading`公共方法`OAuth2Authenticator`对象。 这将导致 Xamarin.Auth 关闭 web 浏览器选项卡上，并将收到的 OAuth 数据分析。

> [!IMPORTANT]
> 在 Android 上，使用 Xamarin.Auth `CustomTabs` API 与 web 浏览器和操作系统进行通信。 但是，它不能保证的`CustomTabs`将用户的设备上安装兼容的浏览器。

### <a name="examining-the-oauth-response"></a>检查 OAuth 响应

分析收到的 OAuth 数据之后, 将引发 Xamarin.Auth`OAuth2Authenticator.Completed`事件。 此事件的事件处理程序中`AuthenticatorCompletedEventArgs.IsAuthenticated`属性可用于确定是否已成功身份验证，如下面的代码示例中所示：

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

收集从身份验证成功的数据现已推出`AuthenticatorCompletedEventArgs.Account`属性。 这包括访问令牌，可以使用数据传输到标识提供程序提供的 API 的请求进行签名。

### <a name="making-requests-for-data"></a>发出请求的数据

应用程序获取访问令牌后，它用于向发送请求`https://www.googleapis.com/oauth2/v2/userinfo`API，请求从标识提供者的基本用户数据。 使用 Xamarin.Auth 的发出此请求`OAuth2Request`类，该类表示使用从检索到的帐户进行身份验证的请求`OAuth2Authenticator`实例，如下面的代码示例中所示：

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

HTTP 方法和 API URL，以及`OAuth2Request`还指定了实例`Account`实例，其中包含访问令牌来登录到由指定的 URL 请求`Constants.UserInfoUrl`属性。 标识提供者然后返回作为 JSON 响应，包括用户的名称和电子邮件地址，前提它识别为有效的访问令牌的基本用户数据。 JSON 响应然后读取和反序列化为`user`变量。

有关详细信息，请参阅[调用 Google API](https://developers.google.com/identity/protocols/OAuth2InstalledApp#callinganapi) Google 开发人员门户上。

### <a name="storing-and-retrieving-account-information-on-devices"></a>存储和检索设备上的帐户信息

安全地存储 Xamarin.Auth`Account`存储帐户中的对象，以便应用程序并不总是有重新进行身份验证的用户。 `AccountStore`类是负责存储帐户信息，由密钥链在 iOS 中，服务提供支持和`KeyStore`在 Android 中的类。

下面的代码示例演示如何`Account`安全地保存对象：

```csharp
AccountStore.Create ().Save (e.Account, Constants.AppName);
```

已保存的帐户进行唯一标识使用组成该帐户的密钥`Username`属性和服务 ID，这是一个字符串，用于提取帐户时从帐户存储。 如果`Account`以前保存，调用`Save`方法再次将其覆盖。

`Account` 对象服务可以检索通过调用`FindAccountsForService`方法，如下面的代码示例中所示：

```csharp
var account = AccountStore.Create ().FindAccountsForService (Constants.AppName).FirstOrDefault();
```

`FindAccountsForService`方法将返回`IEnumerable`的集合`Account`对象，与设置为匹配帐户的集合中的第一个项。

## <a name="summary"></a>总结

本文介绍了如何使用 Xamarin.Auth 管理 Xamarin.Forms 应用程序中的身份验证过程。 提供了 Xamarin.Auth`OAuth2Authenticator`和`OAuth2Request`Xamarin.Forms 应用程序用于使用 Google、 Microsoft、 Facebook 和 Twitter 等标识提供程序的类。


## <a name="related-links"></a>相关链接

- [OAuthNativeFlow （示例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/OAuthNativeFlow/)
- [对于本机应用程序的 OAuth 2.0](https://tools.ietf.org/html/draft-ietf-oauth-native-apps-12)
- [使用 OAuth2.0 访问 Google Api](https://developers.google.com/identity/protocols/OAuth2)
- [Xamarin.Auth (NuGet)](https://www.nuget.org/packages/xamarin.auth/)
- [Xamarin.Auth (GitHub)](https://github.com/xamarin/Xamarin.Auth)
