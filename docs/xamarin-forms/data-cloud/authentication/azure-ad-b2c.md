---
title: 使用 Azure Active Directory B2C 的用户进行身份验证
description: Azure Active Directory B2C 提供云身份管理适用于面向消费者的 web 和移动应用程序。 本文介绍如何使用 Azure Active Directory B2C 在使用 Microsoft 身份验证库的移动应用程序中集成标识管理。
ms.prod: xamarin
ms.assetid: B0A5DB65-0585-4A00-B908-22CCC286E6B6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/17/2019
ms.openlocfilehash: 06f5716c8decb21de39fd46abe734b5fdcd6bd43
ms.sourcegitcommit: 0f78ec17210b915b43ddab75937de8063e472c70
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2019
ms.locfileid: "67417939"
---
# <a name="authenticate-users-with-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 的用户进行身份验证

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/WebServices/AzureADB2CAuth/)

_Azure Active Directory B2C 提供云身份管理适用于面向消费者的 web 和移动应用程序。本文介绍如何使用 Azure Active Directory B2C 在使用 Microsoft 身份验证库的移动应用程序中集成标识管理。_

## <a name="overview"></a>概述

Azure Active Directory B2C (ADB2C) 是面向使用者的应用程序的标识管理服务。 它允许用户登录到你的应用程序使用其现有社交帐户或自定义凭据，例如电子邮件或用户名和密码。 自定义凭据帐户_本地_帐户。

将 Azure Active Directory B2C 标识管理服务集成到移动应用程序的过程如下所示：

1. 创建 Azure Active Directory B2C 租户
1. 向 Azure Active Directory B2C 租户注册的移动应用程序
1. 进行注册和登录、 创建策略并忘记了密码用户流
1. 使用 Microsoft 身份验证库 (MSAL) 来启动与 Azure Active Directory B2C 租户的身份验证工作流。

> [!NOTE]
> Azure Active Directory B2C 支持多个标识提供者包括 Microsoft、 GitHub、 Facebook、 Twitter 和的详细信息。 Azure Active Directory B2C 功能的详细信息，请参阅[Azure Active Directory B2C 文档](/azure/active-directory-b2c/)。
>
> Microsoft 身份验证库支持多个应用程序体系结构和平台。 MSAL 功能有关的信息，请参阅[Microsoft 身份验证库](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)GitHub 上。

## <a name="configure-an-azure-active-directory-b2c-tenant"></a>配置 Azure Active Directory B2C 租户

若要运行示例项目，必须创建一个 Azure Active Directory B2C 租户。 有关详细信息，请参阅[Azure 门户中创建一个 Azure Active Directory B2C 租户](/azure/active-directory-b2c/active-directory-b2c-get-started/)。

一旦创建租户时，将需要**租户名称**并**租户 ID**配置移动应用程序。 创建你的租户 URL 时，生成的域定义的租户 ID 和名称。 如果生成的租户 URL 是`https://contoso20190410tenant.onmicrosoft.com/`**租户 ID**是`contoso20190410tenant.onmicrosoft.com`并**租户名称**是`contoso20190410tenant`。 通过单击在 Azure 门户中找到租户域对租户**目录和订阅筛选器**顶部菜单中。 以下屏幕截图显示 Azure 目录和订阅筛选器按钮和租户域对租户：

[![在 Azure 目录和订阅筛选器视图中的租户名称](azure-ad-b2c-images/azure-tenant-name-cropped.png)](azure-ad-b2c-images/azure-tenant-name.png#lightbox)

在示例项目中，编辑**Constants.cs**文件以设置`tenantName`和`tenantId`字段。 下面的代码显示了应该如何设置这些值租户域是否为`https://contoso20190410tenant.onmicrosoft.com/`，这些值替换为在门户中的值：

```csharp
public static class Constants
{
    static readonly string tenantName = "contoso20190410tenant";
    static readonly string tenantId = "contoso20190410tenant.onmicrosoft.com";
    ...
}
```

## <a name="register-your-mobile-application-with-azure-active-directory-b2c"></a>移动应用程序注册到 Azure Active Directory B2C

它可以连接并对用户进行身份验证之前，必须向租户注册的移动应用程序。 注册过程将分配一个唯一**应用程序 ID**应用程序，和一个**重定向 URL**定向回应用程序进行身份验证后的响应。 有关详细信息，请参阅[Azure Active Directory B2C:注册应用程序](/azure/active-directory-b2c/active-directory-b2c-app-registration/)。 将需要知道**应用程序 ID**分配给应用程序，这后在属性视图中的应用程序名称列出。 下面的屏幕截图显示了在何处可以找到应用程序 ID:

[![在 Azure 应用程序属性视图中的应用程序 ID](azure-ad-b2c-images/azure-application-id-cropped.png)](azure-ad-b2c-images/azure-application-id.png#lightbox)

Microsoft 身份验证库要求**重定向 URL**为应用程序为您**应用程序 ID**带有文本"msal"前缀后, 跟一个名为"身份验证"的终结点。 如果你的应用程序 ID 为"1234abcd"，应为的完整 URL `msal1234abcd://auth`。 请确保你的应用程序已启用**本机客户端**设置并创建**自定义重定向 URI**使用你的应用程序 ID，如以下屏幕截图中所示：

![在 Azure 应用程序属性视图中的自定义重定向 URI](azure-ad-b2c-images/azure-redirect-uri.png)

URL 将使用更高版本中这两个 Android **ApplicationManifest.xml**和 iOS **Info.plist**。

在示例项目中，编辑**Constants.cs**文件以设置`clientId`字段你**应用程序 ID**。 下面的代码显示了应如何设置此值是否为应用程序 ID `1234abcd`:

```csharp
public static class Constants
{
    static readonly string tenantName = "contoso20190410tenant";
    static readonly string tenantId = "contoso20190410tenant.onmicrosoft.com";
    static readonly string clientId = "1234abcd";
    ...
}
```

## <a name="create-sign-up-and-sign-in-policies-and-forgot-password-policies"></a>创建注册和登录策略和忘记了密码策略

策略是用户可以通过完成的任务，例如，创建一个帐户或重置密码的体验。 策略还指定用户返回从体验时，应用程序收到的令牌的内容。 必须注册和登录中设置了这两个帐户的策略和重置密码。 Azure 具有简化的常见策略创建的内置策略。 有关详细信息，请参阅[Azure Active Directory B2C:内置策略](/azure/active-directory-b2c/active-directory-b2c-reference-policies/)。

完成策略设置后，应具有两个策略**用户流 （策略）** Azure 门户中的视图。 下面的屏幕截图演示了如何在 Azure 门户中的两个配置的策略：

![查看在 Azure 用户流 （策略） 中的两个配置的策略](azure-ad-b2c-images/azure-application-policies.png)

在示例项目中，编辑**Constants.cs**文件以设置`policySignin`和`policyPassword`字段以反映在策略设置期间选择的名称：

```csharp
public static class Constants
{
    static readonly string tenantName = "contoso20190410tenant";
    static readonly string tenantId = "contoso20190410tenant.onmicrosoft.com";
    static readonly string clientId = "1234abcd";
    static readonly string policySignin = "B2C_1_signupsignin1";
    static readonly string policyPassword = "B2C_1_passwordreset";
    ...
}
```

## <a name="use-the-microsoft-authentication-library-msal-for-authentication"></a>使用 Microsoft 身份验证库 (MSAL) 进行身份验证

Microsoft 身份验证库 (MSAL) NuGet 包必须添加到共享、.NET Standard 项目中，和 Xamarin.Forms 解决方案中的平台项目。 包括 MSAL`PublicClientApplicationBuilder`类构造一个对象，遵守`IPublicClientApplication`接口。 MSAL 利用`With`子句以提供额外的参数的构造函数和身份验证方法。

在示例项目中的隐藏代码**App.xaml**定义名为静态属性`AuthenticationClient`并`UIParent`，并实例化`AuthenticationClient`构造函数中的对象。 `WithIosKeychainSecurityGroup`子句提供了 iOS 应用程序的安全组名称。 `WithB2CAuthority`子句提供的默认值**颁发机构**，或将使用用户进行身份验证的策略。 下面的示例演示如何实例化`PublicClientApplication`:

```csharp
public partial class App : Application
{
    public static IPublicClientApplication AuthenticationClient { get; private set; }

    public static object UIParent { get; set; } = null;

    public App()
    {
        InitializeComponent();

        AuthenticationClient = PublicClientApplicationBuilder.Create(Constants.ClientId)
            .WithIosKeychainSecurityGroup(Constants.IosKeychainSecurityGroups)
            .WithB2CAuthority(Constants.AuthoritySignin)
            .Build();

        MainPage = new NavigationPage(new LoginPage());
    }

    ...
```

`OnAppearing`中的事件处理程序**LoginPage.xaml.cs**代码隐藏调用`AcquireTokenSilentAsync`刷新之前已登录的用户的身份验证令牌。 身份验证过程将重定向到`LogoutPage`如果成功，则并不执行任何操作失败。 下面的示例演示中的无提示重新进行身份验证过程`OnAppearing`:

```csharp
public partial class LoginPage : ContentPage
{
    ...

    protected override async void OnAppearing()
    {
        try
        {
            // Look for existing account
            IEnumerable<IAccount> accounts = await App.AuthenticationClient.GetAccountsAsync();

            AuthenticationResult result = await App.AuthenticationClient
                .AcquireTokenSilent(Constants.Scopes, accounts.FirstOrDefault())
                .ExecuteAsync();

            await Navigation.PushAsync(new LogoutPage(result));
        }
        catch
        {
            // Do nothing - the user isn't logged in
        }
        base.OnAppearing();
    }

    ...
}
```

`OnLoginButtonClicked` （单击登录按钮时触发） 的事件处理程序调用`AcquireTokenAsync`。 MSAL 库会自动打开移动设备浏览器并导航到登录页。 登录 URL，称为**颁发机构**，是租户名称的组合中定义的策略**Constants.cs**文件。 如果用户选择忘记了密码选项，它们将返回到应用程序异常，它会启动与忘记了密码体验。 下面的示例显示了身份验证过程：

```csharp
public partial class LoginPage : ContentPage
{
    ...

    async void OnLoginButtonClicked(object sender, EventArgs e)
    {
        AuthenticationResult result;
        try
        {
            result = await App.AuthenticationClient
                .AcquireTokenInteractive(Constants.Scopes)
                .WithPrompt(Prompt.SelectAccount)
                .WithParentActivityOrWindow(App.UIParent)
                .ExecuteAsync();
    
            await Navigation.PushAsync(new LogoutPage(result));
        }
        catch (MsalException ex)
        {
            if (ex.Message != null && ex.Message.Contains("AADB2C90118"))
            {
                result = await OnForgotPassword();
                await Navigation.PushAsync(new LogoutPage(result));
            }
            else if (ex.ErrorCode != "authentication_canceled")
            {
                await DisplayAlert("An error has occurred", "Exception message: " + ex.Message, "Dismiss");
            }
        }
    }

    ...
}
```

`OnForgotPassword`方法类似于在登录过程，但实现自定义策略。 `OnForgotPassword` 使用的不同重载`AcquireTokenAsync`，这使您可以提供特定**颁发机构**。 下面的示例演示如何提供自定义**机构**时获取令牌：

```csharp
public partial class LoginPage : ContentPage
{
    ...
    async Task<AuthenticationResult> OnForgotPassword()
    {
        try
        {
            return await App.AuthenticationClient
                .AcquireTokenInteractive(Constants.Scopes)
                .WithPrompt(Prompt.SelectAccount)
                .WithParentActivityOrWindow(App.UIParent)
                .WithB2CAuthority(Constants.AuthorityPasswordReset)
                .ExecuteAsync();
        }
        catch (MsalException)
        {
            // Do nothing - ErrorCode will be displayed in OnLoginButtonClicked
            return null;
        }
    }
}
```

身份验证的最后一个部分是注销过程。 `OnLogoutButtonClicked`在用户按注销按钮时调用方法。 它循环访问所有帐户，并确保其令牌已失效。 下面的示例演示实现注销：

```csharp
public partial class LogoutPage : ContentPage
{
    ...
    async void OnLogoutButtonClicked(object sender, EventArgs e)
    {
        IEnumerable<IAccount> accounts = await App.AuthenticationClient.GetAccountsAsync();

        while (accounts.Any())
        {
            await App.AuthenticationClient.RemoveAsync(accounts.First());
            accounts = await App.AuthenticationClient.GetAccountsAsync();
        }

        await Navigation.PopAsync();
    }
}
```

### <a name="ios"></a>iOS

在 iOS 上，与 Azure Active Directory B2C 注册的自定义 URL 方案必须注册中**Info.plist**。 MSAL 需要遵循前面所述的特定模式的 URL 方案[移动应用程序注册到 Azure Active Directory B2C](/docs/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md#register-your-mobile-application-with-azure-active-directory-b2c)。 以下屏幕截图显示了中的自定义 URL 方案**Info.plist**。

!["注册在 iOS 上的自定义 URL 方案"](azure-ad-b2c-images/customurl-ios.png)

MSAL 还需要在 iOS 上，注册在 Keychain 权利**Entitilements.plist**，如以下屏幕截图中所示：

!["在 ios 设备上设置应用权利"](azure-ad-b2c-images/entitlements-ios.png)

Azure Active Directory B2C 完成授权请求，它将重定向到注册的重定向 URL。 在 iOS 中启动移动应用程序并在 URL 中传递作为启动参数，它由处理结果的自定义 URL 方案`OpenUrl`在应用程序的重写`AppDelegate`类，并将控制权返还到 MSAL 的体验。 `OpenUrl`实现下面的代码示例所示：

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
            return base.OpenUrl(app, url, options);
        }
    }
}
```

### <a name="android"></a>Android

在 Android 上，与 Azure Active Directory B2C 注册的自定义 URL 方案必须注册中**AndroidManifest.xml**。 MSAL 需要遵循前面所述的特定模式的 URL 方案[移动应用程序注册到 Azure Active Directory B2C](/docs/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md#register-your-mobile-application-with-azure-active-directory-b2c)。 下面的示例演示中的自定义 URL 方案**AndroidManifest.xml**。

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android" android:versionCode="1" android:versionName="1.0" package="com.xamarin.adb2cauthorization">
  <uses-sdk android:minSdkVersion="15" />
  <application android:label="ADB2CAuthorization">
    <activity android:name="microsoft.identity.client.BrowserTabActivity">
      <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="INSERT_URI_SCHEME_HERE" android:host="auth" />"
      </intent-filter>
    </activity>"
  </application>
</manifest>
```

`MainActivity`必须修改类，以提供`UIParent`期间的应用程序的对象`OnCreate`调用。 Azure Active Directory B2C 完成授权请求，它将重定向到已注册的 URL 方案，从**AndroidManifest.xml**。 已注册的 URI 方案会导致 Android 电话`OnActivityResult`方法替换为启动参数，它由处理 URL`SetAuthenticationContinuationEventArgs`方法。

```csharp
public class MainActivity : FormsAppCompatActivity
{
    protected override void OnCreate(Bundle bundle)
    {
        TabLayoutResource = Resource.Layout.Tabbar;
        ToolbarResource = Resource.Layout.Toolbar;

        base.OnCreate(bundle);

        Forms.Init(this, bundle);
        LoadApplication(new App());
        App.UIParent = this;
    }

    protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
    {
        base.OnActivityResult(requestCode, resultCode, data);
        AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode, resultCode, data);
    }
}
```

### <a name="universal-windows-platform"></a>通用 Windows 平台

在通用 Windows 平台上使用 MSAL 所不需的任何其他设置

## <a name="run-the-project"></a>运行该项目

虚拟或物理设备上运行应用程序。 点击**登录名**按钮应该打开浏览器并导航到在其中登录或创建帐户的页面。 在登录过程后，您应返回到应用程序的注销页面。 下面的屏幕截图显示用户注册 Android 和 iOS 上运行的屏幕中：

!["Azure ADB2C 登录 Android 和 iOS 上的屏幕"](azure-ad-b2c-images/login.png)

## <a name="related-links"></a>相关链接

- [AzureADB2CAuth （示例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/AzureADB2CAuth/)
- [Azure Active Directory B2C](/azure/active-directory-b2c/)
- [Microsoft 身份验证库](https://www.nuget.org/packages/Microsoft.Identity.Client)
- [Microsoft 身份验证库文档](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)
