---
title: 在 Xamarin 中通过 Apple 登录
description: 使用 Apple 登录可以在使用第三方身份验证服务时提供标识保护。
ms.prod: xamarin
ms.assetid: CDA59BBF-AAE1-4D4F-B4AE-DB37220D41EB
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/10/2019
ms.openlocfilehash: a8ea06d81fcc79a24f155a1562818daea3ba982a
ms.sourcegitcommit: 13e43f510da37ad55f1c2f5de1913fb0aede6362
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/16/2019
ms.locfileid: "71021398"
---
# <a name="sign-in-with-apple-in-xamarinios"></a>在 Xamarin 中通过 Apple 登录

![此 API 目前处于预览阶段](~/media/shared/preview.png)

使用 Apple 登录是一项新服务，为第三方身份验证服务的用户提供标识保护。 从 iOS 13 开始，Apple 要求使用第三方身份验证服务的任何新应用还应提供 Apple 的登录。 要更新的现有应用在2020年4月之前无需添加 Apple 的登录。

本文档介绍如何向 iOS 13 应用程序添加与 Apple 的登录。

## <a name="requirements"></a>要求

此功能需要：

* iOS 13
* Xamarin 12.99
* Visual Studio 2019 或 Visual Studio 2019 for Mac 支持 Xcode 11。

有关更多详细信息，请参阅[入门](get-started.md)。

## <a name="apple-developer-setup"></a>Apple 开发人员设置

使用 Apple 的登录生成和运行应用之前，需要完成以下步骤。 在[Apple 开发人员证书上，标识符 & 配置文件][5]门户：

1. 创建新的**应用程序 id**标识符。
2. 在 "**说明**" 字段中设置说明。
3. 选择**显式**捆绑 ID 并在字段`com.xamarin.AddingTheSignInWithAppleFlowToYourApp`中设置。
4. 启用**使用 Apple 功能登录**并注册新标识。
5. 使用新标识创建新的预配配置文件。
6. 下载并将其安装在你的设备上。
7. 在 Visual Studio 中，启用**info.plist**文件中的**使用 Apple 功能登录**。

## <a name="check-sign-in-status"></a>检查登录状态

当你的应用程序开始运行时，或在你首次需要检查用户的身份验证状态`ASAuthorizationAppleIdProvider`时，实例化并检查当前状态：

```csharp
var appleIdProvider = new ASAuthorizationAppleIdProvider ();
appleIdProvider.GetCredentialState (KeychainItem.CurrentUserIdentifier, (credentialState, error) => {
    switch (credentialState) {
    case ASAuthorizationAppleIdProviderCredentialState.Authorized:
        // The Apple ID credential is valid.
        break;
    case ASAuthorizationAppleIdProviderCredentialState.Revoked:
        // The Apple ID credential is revoked.
        break;
    case ASAuthorizationAppleIdProviderCredentialState.NotFound:
        // No credential was found, so show the sign-in UI.
        InvokeOnMainThread (() => {
            var storyboard = UIStoryboard.FromName ("Main", null);

            if (!(storyboard.InstantiateViewController (nameof (LoginViewController)) is LoginViewController viewController))
                return;

            viewController.ModalPresentationStyle = UIModalPresentationStyle.FormSheet;
            viewController.ModalInPresentation = true;
            Window?.RootViewController?.PresentViewController (viewController, true, null);
        });
        break;
    }
});
```

在`FinishedLaunching`此代码中， `AppDelegate.cs`在期间调用，应用将在`LoginViewController`状态为`NotFound`且向用户显示时处理。 如果状态返回`Authorized`了或`Revoked`，则用户可能会看到不同的操作。

## <a name="a-loginviewcontroller-for-sign-in-with-apple"></a>使用 Apple 进行登录时的 LoginViewController

实现登录逻辑并提供与 Apple 的登录的必须实现`IASAuthorizationControllerDelegate`和`IASAuthorizationControllerPresentationContextProviding` ，如下面的`LoginViewController`示例中所示。 `UIViewController`

```csharp
public partial class LoginViewController : UIViewController, IASAuthorizationControllerDelegate, IASAuthorizationControllerPresentationContextProviding {
    public LoginViewController (IntPtr handle) : base (handle)
    {
    }

    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();
        // Perform any additional setup after loading the view, typically from a nib.

        SetupProviderLoginView ();
    }

    public override void ViewDidAppear (bool animated)
    {
        base.ViewDidAppear (animated);

        PerformExistingAccountSetupFlows ();
    }

    void SetupProviderLoginView ()
    {
        var authorizationButton = new ASAuthorizationAppleIdButton (ASAuthorizationAppleIdButtonType.Default, ASAuthorizationAppleIdButtonStyle.White);
        authorizationButton.TouchUpInside += HandleAuthorizationAppleIDButtonPress;
        loginProviderStackView.AddArrangedSubview (authorizationButton);
    }

    // Prompts the user if an existing iCloud Keychain credential or Apple ID credential is found.
    void PerformExistingAccountSetupFlows ()
    {
        // Prepare requests for both Apple ID and password providers.
        ASAuthorizationRequest [] requests = {
            new ASAuthorizationAppleIdProvider ().CreateRequest (),
            new ASAuthorizationPasswordProvider ().CreateRequest ()
        };

        // Create an authorization controller with the given requests.
        var authorizationController = new ASAuthorizationController (requests);
        authorizationController.Delegate = this;
        authorizationController.PresentationContextProvider = this;
        authorizationController.PerformRequests ();
    }

    private void HandleAuthorizationAppleIDButtonPress (object sender, EventArgs e)
    {
        var appleIdProvider = new ASAuthorizationAppleIdProvider ();
        var request = appleIdProvider.CreateRequest ();
        request.RequestedScopes = new [] { ASAuthorizationScope.Email, ASAuthorizationScope.FullName };

        var authorizationController = new ASAuthorizationController (new [] { request });
        authorizationController.Delegate = this;
        authorizationController.PresentationContextProvider = this;
        authorizationController.PerformRequests ();
    }
}
```

![使用 Apple 登录的示例应用的动画](sign-in-images/sign-in-flow.png)

此代码示例将检查中`PerformExistingAccountSetupFlows`的当前登录状态，并作为委托连接到当前视图。 如果找到现有的 iCloud 密钥链 credential 或 Apple ID 凭据，则系统会提示用户使用该凭据。

Apple 提供`ASAuthorizationAppleIdButton`了一个专门用于此目的的按钮。 接触后，该按钮将触发方法`HandleAuthorizationAppleIDButtonPress`中处理的工作流。

## <a name="handling-authorization"></a>处理授权

在中`IASAuthorizationController` ，实现任何自定义逻辑来存储用户的帐户。 下面的示例将用户帐户存储在 Apple 自己的密钥链存储服务中。

```csharp
#region IASAuthorizationController Delegate

[Export ("authorizationController:didCompleteWithAuthorization:")]
public void DidComplete (ASAuthorizationController controller, ASAuthorization authorization)
{
    if (authorization.GetCredential<ASAuthorizationAppleIdCredential> () is ASAuthorizationAppleIdCredential appleIdCredential) {
        var userIdentifier = appleIdCredential.User;
        var fullName = appleIdCredential.FullName;
        var email = appleIdCredential.Email;

        // Create an account in your system.
        // For the purpose of this demo app, store the userIdentifier in the keychain.
        try {
            new KeychainItem ("com.example.apple-samplecode.juice", "userIdentifier").SaveItem (userIdentifier);
        } catch (Exception) {
            Console.WriteLine ("Unable to save userIdentifier to keychain.");
        }

        // For the purpose of this demo app, show the Apple ID credential information in the ResultViewController.
        if (!(PresentingViewController is ResultViewController viewController))
            return;

        InvokeOnMainThread (() => {
            viewController.UserIdentifierText = userIdentifier;
            viewController.GivenNameText = fullName?.GivenName ?? "";
            viewController.FamilyNameText = fullName?.FamilyName ?? "";
            viewController.EmailText = email ?? "";

            DismissViewController (true, null);
        });
    } else if (authorization.GetCredential<ASPasswordCredential> () is ASPasswordCredential passwordCredential) {
        // Sign in using an existing iCloud Keychain credential.
        var username = passwordCredential.User;
        var password = passwordCredential.Password;

        // For the purpose of this demo app, show the password credential as an alert.
        InvokeOnMainThread (() => {
            var message = $"The app has received your selected credential from the keychain. \n\n Username: {username}\n Password: {password}";
            var alertController = UIAlertController.Create ("Keychain Credential Received", message, UIAlertControllerStyle.Alert);
            alertController.AddAction (UIAlertAction.Create ("Dismiss", UIAlertActionStyle.Cancel, null));

            PresentViewController (alertController, true, null);
        });
    }
}

[Export ("authorizationController:didCompleteWithError:")]
public void DidComplete (ASAuthorizationController controller, NSError error)
{
    Console.WriteLine (error);
}

#endregion
```

## <a name="authorization-controller"></a>授权控制器

此实现中的最后一个部分是`ASAuthorizationController` ，它管理提供程序的授权请求。

```csharp
#region IASAuthorizationControllerPresentation Context Providing

public UIWindow GetPresentationAnchor (ASAuthorizationController controller) => View.Window;

#endregion
```

## <a name="summary"></a>总结

本文介绍了如何在 Apple for iOS 中进行登录。

## <a name="related-links"></a>相关链接

* [用 Apple 准则登录](https://developer.apple.com/design/human-interface-guidelines/sign-in-with-apple/overview/)
* [用 Apple 权限登录。][2]
* [WWDC 2019 会话706：介绍如何在 Apple 中进行登录。][3]
* [安装通过 Apple for Xamarin 进行登录。 Forms][4]

[1]: https://developer.apple.com/documentation/authenticationservices/adding_the_sign_in_with_apple_flow_to_your_app
[2]: https://developer.apple.com/documentation/bundleresources/entitlements/com_apple_developer_applesignin
[3]: https://developer.apple.com/videos/play/wwdc19/706/
[4]: ~/xamarin-forms/platform/sign-in-with-apple/setup.md
[5]: https://developer.apple.com/account/resources/identifiers/list
