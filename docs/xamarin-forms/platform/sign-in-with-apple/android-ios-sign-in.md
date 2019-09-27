---
title: 使用 Apple for Xamarin 的登录。
description: 了解如何在 Xamarin 中实现 Apple 的登录。 Forms mobile 应用程序。
ms.prod: xamarin
ms.assetid: 2E47E7F2-93D4-4CA3-9E66-247466D25E4D
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 09/10/2019
ms.openlocfilehash: df011a6eb72b6eb30af0a197d4be48b0f2494384
ms.sourcegitcommit: fc689c1a6b641c124378dedc1bd157d96fc759a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2019
ms.locfileid: "71319512"
---
# <a name="use-sign-in-with-apple-in-xamarinforms"></a>在 Xamarin 中使用 Apple 的登录。 Forms

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/signinwithapple/)

使用 Apple 登录适用于 iOS 13 上使用第三方身份验证服务的所有新应用程序。 IOS 和 Android 之间的实现细节完全不同。 本指南逐步讲解如何在 Xamarin 中完成此操作。

在本指南和示例中，使用特定平台服务通过 Apple 处理登录：

- 使用带有 OpenID/OpenAuth 的 Azure Functions 的通用 web 服务的 Android
- iOS 在 iOS 13 上使用本机 API 进行身份验证，并回退到适用于 iOS 12 和更低的通用 web 服务

## <a name="a-sample-apple-sign-in-flow"></a>示例 Apple 登录流

此示例提供了一个固执实现，用于在 Xamarin. Forms 应用中使用 Apple 登录。

我们使用两个 Azure Functions 来帮助进行身份验证流：

1. `applesignin_auth`-生成 Apple 登录授权 URL 并重定向到该 URL。  我们会在服务器端而不是移动应用中执行此操作，因此我们可以在`state` Apple 的服务器发送回调时缓存并验证它。
2. `applesignin_callback`-处理 Apple 发出的 POST 回调并安全地交换访问令牌和 ID 令牌的授权代码。  最后，它会重定向回应用程序的 URI 方案，并传入 URL 片段中的令牌。

移动应用会自行注册，以处理我们选择的自定义 URI 方案（在本例`xamarinformsapplesignin://`中为） `applesignin_callback` ，以便函数可以将令牌中继回。

当用户启动身份验证时，会执行以下步骤：

1. 移动应用生成`nonce`和`state`值`applesignin_auth` ，并将其传递给 Azure 函数。
2. Azure `applesignin_auth`函数将使用提供`state`的和`nonce`，生成 Apple 登录授权 URL，并将移动应用浏览器重定向到该 URL。
3. 用户安全地在 Apple 的服务器上托管的 Apple 登录授权页中输入其凭据。
4. Apple 登录流在 apple 的服务器上完成后，apple 将重定向到`redirect_uri`将`applesignin_callback`成为 Azure 功能的。
5. 对发送到`applesignin_callback`函数的 Apple 发出的请求进行验证，以确保`state`返回正确的，并且 ID 令牌声明是有效的。
6. `code`Azure 函数会将 Apple 发布的发布到该访问令牌、刷新令牌和 ID 令牌（其中包含有关用户 ID、名称和电子邮件的声明）。 `applesignin_callback`
7. Azure `applesignin_callback`函数最后将重定向回应用程序的 uri 方案（`xamarinformsapplesignin://`），并将 URI 片段追加到标记（例如`xamarinformsapplesignin://#access_token=...&refresh_token=...&id_token=...`）。
8. 移动应用程序将 URI 片段分析为`AppleAccount` ，并`nonce`验证收到的声明是否与流`nonce`开始处生成的声明匹配。
9. 现在，移动应用已经过身份验证！

## <a name="azure-functions"></a>Azure Functions

此示例使用 Azure Functions。 或者，ASP.NET Core 控制器或类似的 web 服务器解决方案可以提供相同的功能。

### <a name="configuration"></a>配置

使用 Azure Functions 时需要配置多个应用设置：

- `APPLE_SIGNIN_KEY_ID`-这是你`KeyId`前面的。
- `APPLE_SIGNIN_TEAM_ID`-这通常是[成员身份配置文件](https://developer.apple.com/account/#/membership)中的_团队 ID_
- `APPLE_SIGNIN_SERVER_ID`：这是前面`ServerId`的。  它*不*是你的应用_程序包 id_，而是你创建的*服务 id*的*标识符*。
- `APPLE_SIGNIN_APP_CALLBACK_URI`-这是要重定向回应用程序的自定义 URI 方案。  在此示例`xamarinformsapplesignin://`中使用。
- `APPLE_SIGNIN_REDIRECT_URI`-在*Apple 登录*配置部分创建*服务 ID*时设置的*重定向 URL* 。  若要进行测试，它可能类似于：`http://local.test:7071/api/applesignin_callback`
- `APPLE_SIGNIN_P8_KEY`-已删除`\n`所有换行符的`.p8`文件的文本内容，因此它是一个长字符串

### <a name="security-considerations"></a>安全注意事项

**永远不要**将 P8 密钥存储在应用程序代码中。 应用程序代码易于下载和拆装。 

使用`WebView`承载身份验证流的操作也被视为一种不好的做法，并截获 URL 导航事件以获取授权代码。 目前，当前没有在非 iOS13 + 设备上使用 Apple 处理登录的完全安全方法，无需在服务器上托管一些代码来处理令牌交换。 建议在服务器上托管授权 url 生成代码，以便可以在 Apple 向服务器发出 POST 回调时缓存状态并对其进行验证。

## <a name="a-cross-platform-sign-in-service"></a>跨平台登录服务

使用 Xamarin. Forms DependencyService，可以创建单独的身份验证服务，该服务使用 iOS 上的平台服务，以及基于共享接口的适用于 Android 和其他非 iOS 平台的通用 web 服务。

```csharp
public interface IAppleSignInService
{
    bool Callback(string url);

    Task<AppleAccount> SignInAsync();
}
```

在 iOS 上，使用本机 Api：

```csharp
public class AppleSignInServiceiOS : IAppleSignInService
{
#if __IOS__13
    AuthManager authManager;
#endif

    bool Is13 => UIDevice.CurrentDevice.CheckSystemVersion(13, 0);
    WebAppleSignInService webSignInService;

    public AppleSignInServiceiOS()
    {
        if (!Is13)
            webSignInService = new WebAppleSignInService();
    }

    public async Task<AppleAccount> SignInAsync()
    {
        // Fallback to web for older iOS versions
        if (!Is13)
            return await webSignInService.SignInAsync();

        AppleAccount appleAccount = default;

#if __IOS__13
        var provider = new ASAuthorizationAppleIdProvider();
        var req = provider.CreateRequest();

        authManager = new AuthManager(UIApplication.SharedApplication.KeyWindow);

        req.RequestedScopes = new[] { ASAuthorizationScope.FullName, ASAuthorizationScope.Email };
        var controller = new ASAuthorizationController(new[] { req });

        controller.Delegate = authManager;
        controller.PresentationContextProvider = authManager;

        controller.PerformRequests();

        var creds = await authManager.Credentials;

        if (creds == null)
            return null;

        appleAccount = new AppleAccount();
        appleAccount.IdToken = JwtToken.Decode(new NSString(creds.IdentityToken, NSStringEncoding.UTF8).ToString());
        appleAccount.Email = creds.Email;
        appleAccount.UserId = creds.User;
        appleAccount.Name = NSPersonNameComponentsFormatter.GetLocalizedString(creds.FullName, NSPersonNameComponentsFormatterStyle.Default, NSPersonNameComponentsFormatterOptions.Phonetic);
        appleAccount.RealUserStatus = creds.RealUserStatus.ToString();
#endif

        return appleAccount;
    }

    public bool Callback(string url) => true;
}

#if __IOS__13
class AuthManager : NSObject, IASAuthorizationControllerDelegate, IASAuthorizationControllerPresentationContextProviding
{
    public Task<ASAuthorizationAppleIdCredential> Credentials
        => tcsCredential?.Task;

    TaskCompletionSource<ASAuthorizationAppleIdCredential> tcsCredential;

    UIWindow presentingAnchor;

    public AuthManager(UIWindow presentingWindow)
    {
        tcsCredential = new TaskCompletionSource<ASAuthorizationAppleIdCredential>();
        presentingAnchor = presentingWindow;
    }

    public UIWindow GetPresentationAnchor(ASAuthorizationController controller)
        => presentingAnchor;

    [Export("authorizationController:didCompleteWithAuthorization:")]
    public void DidComplete(ASAuthorizationController controller, ASAuthorization authorization)
    {
        var creds = authorization.GetCredential<ASAuthorizationAppleIdCredential>();
        tcsCredential?.TrySetResult(creds);
    }

    [Export("authorizationController:didCompleteWithError:")]
    public void DidComplete(ASAuthorizationController controller, NSError error)
        => tcsCredential?.TrySetException(new Exception(error.LocalizedDescription));
}
#endif
```

编译标志`__IOS__13`用于为 iOS 13 以及回退到一般 web 服务的旧版本提供支持。

在 Android 上，使用带有 Azure Functions 的泛型 web 服务：

```csharp
public class WebAppleSignInService : IAppleSignInService
{
    // IMPORTANT: This is what you register each native platform's url handler to be
    public const string CallbackUriScheme = "xamarinformsapplesignin";
    public const string InitialAuthUrl = "http://local.test:7071/api/applesignin_auth";

    string currentState;
    string currentNonce;

    TaskCompletionSource<AppleAccount> tcsAccount = null;

    public bool Callback(string url)
    {
        // Only handle the url with our callback uri scheme
        if (!url.StartsWith(CallbackUriScheme + "://"))
            return false;

        // Ensure we have a task waiting
        if (tcsAccount != null && !tcsAccount.Task.IsCompleted)
        {
            try
            {
                // Parse the account from the url the app opened with
                var account = AppleAccount.FromUrl(url);

                // IMPORTANT: Validate the nonce returned is the same as our originating request!!
                if (!account.IdToken.Nonce.Equals(currentNonce))
                    tcsAccount.TrySetException(new InvalidOperationException("Invalid or non-matching nonce returned"));

                // Set our account result
                tcsAccount.TrySetResult(account);
            }
            catch (Exception ex)
            {
                tcsAccount.TrySetException(ex);
            }
        }

        tcsAccount.TrySetResult(null);
        return false;
    }

    public async Task<AppleAccount> SignInAsync()
    {
        tcsAccount = new TaskCompletionSource<AppleAccount>();

        // Generate state and nonce which the server will use to initial the auth
        // with Apple.  The nonce should flow all the way back to us when our function
        // redirects to our app
        currentState = Util.GenerateState();
        currentNonce = Util.GenerateNonce();

        // Start the auth request on our function (which will redirect to apple)
        // inside a browser (either SFSafariViewController, Chrome Custom Tabs, or native browser)
        await Xamarin.Essentials.Browser.OpenAsync($"{InitialAuthUrl}?&state={currentState}&nonce={currentNonce}",
            Xamarin.Essentials.BrowserLaunchMode.SystemPreferred);

        return await tcsAccount.Task;
    }
}
```

## <a name="summary"></a>总结

本文介绍了使用 Apple 进行登录所需的步骤，以便在 Xamarin 中使用。

## <a name="related-links"></a>相关链接

- [XamarinFormsAppleSignIn （示例）](https://github.com/Redth/Xamarin.AppleSignIn.Sample)
- [用 Apple 准则登录](https://developer.apple.com/design/human-interface-guidelines/sign-in-with-apple/overview/)
