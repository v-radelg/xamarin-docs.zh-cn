---
title: 在 Xamarin.iOS 社交框架
description: 社交框架将为与社交网络在中国用户包括 Twitter 和 Facebook，以及 SinaWeibo 交互提供一个统一的 API。
ms.prod: xamarin
ms.assetid: A1C28E66-AA20-1C13-23AF-5A8712E6C752
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: 08ccd5b5ac78e82bf745764d70e59d2db9ec6776
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61185004"
---
# <a name="social-framework-in-xamarinios"></a>在 Xamarin.iOS 社交框架

_社交框架将为与社交网络在中国用户包括 Twitter 和 Facebook，以及 SinaWeibo 交互提供一个统一的 API。_

使用社交框架允许应用程序而无需管理身份验证与社交网络单一 API 进行交互。 它包括用于撰写文章，以及允许通过 HTTP 使用每个社交网络 API 的抽象提供视图控制器的系统。

> [!IMPORTANT]
> 若要连接到各种社交网络的跨平台 API，请参阅[Xamarin.Social](http://components.xamarin.com/view/xamarin.social/) Xamarin Component Store 组件。

## <a name="connecting-to-twitter"></a>连接到 Twitter

### <a name="twitter-account-settings"></a>Twitter 帐户设置

若要连接到 Twitter 使用社交框架，帐户需要在设备设置中配置，如下所示：

 [![](social-framework-images/twitter01.png "Twitter 帐户设置")](social-framework-images/twitter01.png#lightbox)

一旦已输入和验证与 Twitter 帐户，用于访问 Twitter 的社交框架类在设备上的任何应用程序将使用此帐户。

### <a name="sending-tweets"></a>发送推文

社交框架包括名为的控制器`SLComposeViewController`，提供了用于编辑和发送推文的系统提供视图。 下面的屏幕截图显示了此视图的一个示例：

 [![](social-framework-images/twitter02.png "此屏幕截图显示 SLComposeViewController 的示例")](social-framework-images/twitter02.png#lightbox)

若要使用`SLComposeViewController`与 Twitter，控制器的实例必须创建通过调用`FromService`方法替换`SLServiceType.Twitter`，如下所示：

```csharp
var slComposer = SLComposeViewController.FromService (SLServiceType.Twitter);
```

之后`SLComposeViewController`实例返回，所以可以用来显示 UI 来发布到 Twitter。 但是，首先要做是通过调用这种情况下，检查是否有社交网络，Twitter `IsAvailable`:

```csharp
if (SLComposeViewController.IsAvailable (SLServiceKind.Twitter)) {
  ...
}
```

 `SLComposeViewController` 永远不会发送的推文直接无需用户交互。 但是，它可以使用以下方法进行初始化：

-   `SetInitialText` – 将添加要显示推文中的初始文本。 
-  `AddUrl` – 将 Url 添加到推文。
-  `AddImage` – 将图像添加到推文。


初始化后，调用`PresentVIewController`显示创建的视图`SLComposeViewController`。 用户可以根据需要编辑和发送推文，或取消将其发送。 在任一情况下，应在关闭控制器`CompletionHandler`，其中还可以检查结果以看到是否推文发送或已取消，如下所示：

```csharp
slComposer.CompletionHandler += (result) => {
  InvokeOnMainThread (() => {
    DismissViewController (true, null);
    resultsTextView.Text = result.ToString ();
  });
};
```

#### <a name="tweet-example"></a>推文示例

下面的代码演示了如何使用`SLComposeViewController`呈现视图用于发送的推文：

```csharp
using System;
using Social;
using UIKit;

namespace SocialFrameworkDemo
{
    public partial class ViewController : UIViewController
    {
        #region Private Variables
        private SLComposeViewController _twitterComposer = SLComposeViewController.FromService (SLServiceType.Twitter);
        #endregion

        #region Computed Properties
        public bool isTwitterAvailable {
            get { return SLComposeViewController.IsAvailable (SLServiceKind.Twitter); }
        }

        public SLComposeViewController TwitterComposer {
            get { return _twitterComposer; }
        }
        #endregion

        #region Constructors
        protected ViewController (IntPtr handle) : base (handle)
        {
            
        }
        #endregion

        #region Override Methods
        public override void ViewWillAppear (bool animated)
        {
            base.ViewWillAppear (animated);

            // Update UI based on state
            SendTweet.Enabled = isTwitterAvailable;
        }
        #endregion

        #region Actions
        partial void SendTweet_TouchUpInside (UIButton sender)
        {
            // Set initial message
            TwitterComposer.SetInitialText ("Hello Twitter!");
            TwitterComposer.AddImage (UIImage.FromFile ("Icon.png"));
            TwitterComposer.CompletionHandler += (result) => {
                InvokeOnMainThread (() => {
                    DismissViewController (true, null);
                    Console.WriteLine ("Results: {0}", result);
                });
            };

            // Display controller
            PresentViewController (TwitterComposer, true, null);
        }
        #endregion
    }
}
```

### <a name="calling-twitter-api"></a>调用 Twitter API

社交框架还包括对社交网络向发出 HTTP 请求的支持。 它封装在请求`SLRequest`类，用于为面向特定的社交网络的 API。

例如，下面的代码将向 Twitter，公共时间线 （通过扩展上面给出的代码） 发出请求：

```csharp
using Accounts;
...

#region Private Variables
private ACAccount _twitterAccount;
#endregion

#region Computed Properties
public ACAccount TwitterAccount {
    get { return _twitterAccount; }
}
#endregion

#region Override Methods
public override void ViewWillAppear (bool animated)
{
    base.ViewWillAppear (animated);

    // Update UI based on state
    SendTweet.Enabled = isTwitterAvailable;
    RequestTwitterTimeline.Enabled = false;

    // Initialize Twitter Account access 
    var accountStore = new ACAccountStore ();
    var accountType = accountStore.FindAccountType (ACAccountType.Twitter);

    // Request access to Twitter account
    accountStore.RequestAccess (accountType, (granted, error) => {
        // Allowed by user?
        if (granted) {
            // Get account
            _twitterAccount = accountStore.Accounts [accountStore.Accounts.Length - 1];
            InvokeOnMainThread (() => {
                // Update UI
                RequestTwitterTimeline.Enabled = true;
            });
        }
    });
}
#endregion

#region Actions
partial void RequestTwitterTimeline_TouchUpInside (UIButton sender)
{
    // Initialize request
    var parameters = new NSDictionary ();
    var url = new NSUrl("https://api.twitter.com/1.1/statuses/user_timeline.json?count=10");
    var request = SLRequest.Create (SLServiceKind.Twitter, SLRequestMethod.Get, url, parameters);

    // Request data
    request.Account = TwitterAccount;
    request.PerformRequest ((data, response, error) => {
        // Was there an error?
        if (error == null) {
            // Was the request successful?
            if (response.StatusCode == 200) {
                // Yes, display it
                InvokeOnMainThread (() => {
                    Results.Text = data.ToString ();
                });
            } else {
                // No, display error
                InvokeOnMainThread (() => {
                    Results.Text = string.Format ("Error: {0}", response.StatusCode);
                });
            }
        } else {
            // No, display error
            InvokeOnMainThread (() => {
                Results.Text = string.Format ("Error: {0}", error);
            });
        }
    });
}
#endregion
```

让我们看看这些代码在详细信息。 首先，它可以访问帐户存储，并获取 Twitter 帐户的类型：

```csharp
var accountStore = new ACAccountStore ();
var accountType = accountStore.FindAccountType (ACAccountType.Twitter);
```

接下来，它会要求用户是否您的应用程序可以有权访问他们的 Twitter 帐户和该帐户授予访问权限，如果加载到内存和 UI 更新：

```csharp
// Request access to Twitter account
accountStore.RequestAccess (accountType, (granted, error) => {
    // Allowed by user?
    if (granted) {
        // Get account
        _twitterAccount = accountStore.Accounts [accountStore.Accounts.Length - 1];
        InvokeOnMainThread (() => {
            // Update UI
            RequestTwitterTimeline.Enabled = true;
        });
    }
});
```

当用户请求的时间线数据 （通过点击一个按钮在 UI 中的） 时，该应用程序首先窗体从 Twitter 访问数据的请求：

```csharp
// Initialize request
var parameters = new NSDictionary ();
var url = new NSUrl("https://api.twitter.com/1.1/statuses/user_timeline.json?count=10");
var request = SLRequest.Create (SLServiceKind.Twitter, SLRequestMethod.Get, url, parameters);
```
此示例通过包括限制返回的结果为 10 个最新条目`?count=10`在 URL 中。 最后，它将请求附加到 Twitter 帐户 （即已加载上方），并执行到 Twitter 的调用来提取数据：

```csharp
// Request data
request.Account = TwitterAccount;
request.PerformRequest ((data, response, error) => {
    // Was there an error?
    if (error == null) {
        // Was the request successful?
        if (response.StatusCode == 200) {
            // Yes, display it
            InvokeOnMainThread (() => {
                Results.Text = data.ToString ();
            });
        } else {
            // No, display error
            InvokeOnMainThread (() => {
                Results.Text = string.Format ("Error: {0}", response.StatusCode);
            });
        }
    } else {
        // No, display error
        InvokeOnMainThread (() => {
            Results.Text = string.Format ("Error: {0}", error);
        });
    }
});
```

如果已成功加载了数据，原始的 JSON 数据将显示 （如下面的示例输出中）：

[![](social-framework-images/twitter03.png "原始的 JSON 数据显示的一个示例")](social-framework-images/twitter03.png#lightbox)

在实际应用中，JSON 结果可能将被分析为 normal 和向用户显示的结果。 请参阅[介绍 Web 服务](~/cross-platform/data-cloud/web-services/index.md)有关如何分析 JSON 的信息。

## <a name="connecting-to-facebook"></a>连接到 Facebook

### <a name="facebook-account-settings"></a>Facebook 帐户设置

连接到 Facebook 社交框架与使用 Twitter 如上所示的过程几乎完全相同。 必须在设备设置中配置 Facebook 用户帐户，如下所示：

[![](social-framework-images/facebook01.png "Facebook 帐户设置")](social-framework-images/facebook01.png#lightbox)

配置后，使用社交框架在设备上的任何应用程序将使用此帐户连接到 Facebook。

### <a name="posting-to-facebook"></a>将发布到 Facebook

社交框架是一个统一的 API 旨在访问多个社交网络，如代码保持几乎完全相同，而不考虑所使用的社交网络。

例如，`SLComposeViewController`可用方式与显示更早版本，唯一的不同切换到特定于 Facebook 的设置和选项的 Twitter 示例完全相同。 例如：

```csharp
using System;
using Foundation;
using Social;
using UIKit;

namespace SocialFrameworkDemo
{
    public partial class ViewController : UIViewController
    {
        #region Private Variables
        private SLComposeViewController _facebookComposer = SLComposeViewController.FromService (SLServiceType.Facebook);
        #endregion

        #region Computed Properties
        public bool isFacebookAvailable {
            get { return SLComposeViewController.IsAvailable (SLServiceKind.Facebook); }
        }

        public SLComposeViewController FacebookComposer {
            get { return _facebookComposer; }
        }
        #endregion

        #region Constructors
        protected ViewController (IntPtr handle) : base (handle)
        {
            
        }
        #endregion

        #region Override Methods
        public override void ViewWillAppear (bool animated)
        {
            base.ViewWillAppear (animated);

            // Update UI based on state
            PostToFacebook.Enabled = isFacebookAvailable;
        }
        #endregion

        #region Actions
        partial void PostToFacebook_TouchUpInside (UIButton sender)
        {
            // Set initial message
            FacebookComposer.SetInitialText ("Hello Facebook!");
            FacebookComposer.AddImage (UIImage.FromFile ("Icon.png"));
            FacebookComposer.CompletionHandler += (result) => {
                InvokeOnMainThread (() => {
                    DismissViewController (true, null);
                    Console.WriteLine ("Results: {0}", result);
                });
            };

            // Display controller
            PresentViewController (FacebookComposer, true, null);
        }
        #endregion
    }
}
```

与 Facebook 一起使用时`SLComposeViewController`显示一个视图，它看起来几乎等同于 Twitter 的示例中，显示**Facebook**中这种情况下作为标题：

[![](social-framework-images/facebook02.png "SLComposeViewController 显示")](social-framework-images/facebook02.png#lightbox)

### <a name="calling-facebook-graph-api"></a>调用 Facebook 图形 API

类似于 Twitter 等社交框架的`SLRequest`对象可以用于 Facebook 的 graph API。 例如，下面的代码返回信息从 Xamarin 帐户有关图形 API （通过扩展上面给出的代码）：

```csharp
using Accounts;
...

#region Private Variables
private ACAccount _facebookAccount;
#endregion

#region Computed Properties
public ACAccount FacebookAccount {
    get { return _facebookAccount; }
}
#endregion

#region Override Methods
public override void ViewWillAppear (bool animated)
{
    base.ViewWillAppear (animated);

    // Update UI based on state
    PostToFacebook.Enabled = isFacebookAvailable;
    RequestFacebookTimeline.Enabled = false;

    // Initialize Facebook Account access 
    var accountStore = new ACAccountStore ();
    var options = new AccountStoreOptions ();
    var options.FacebookAppId = ""; // Enter your specific Facebook App ID here
    accountType = accountStore.FindAccountType (ACAccountType.Facebook);

    // Request access to Facebook account
    accountStore.RequestAccess (accountType, options, (granted, error) => {
        // Allowed by user?
        if (granted) {
            // Get account
            _facebookAccount = accountStore.Accounts [accountStore.Accounts.Length - 1];
            InvokeOnMainThread (() => {
                // Update UI
                RequestFacebookTimeline.Enabled = true;
            });
        }
    });

}
#endregion

#region Actions
partial void RequestFacebookTimeline_TouchUpInside (UIButton sender)
{
    // Initialize request
    var parameters = new NSDictionary ();
    var url = new NSUrl ("https://graph.facebook.com/283148898401104");
    var request = SLRequest.Create (SLServiceKind.Facebook, SLRequestMethod.Get, url, parameters);

    // Request data
    request.Account = FacebookAccount;
    request.PerformRequest ((data, response, error) => {
        // Was there an error?
        if (error == null) {
            // Was the request successful?
            if (response.StatusCode == 200) {
                // Yes, display it
                InvokeOnMainThread (() => {
                    Results.Text = data.ToString ();
                });
            } else {
                // No, display error
                InvokeOnMainThread (() => {
                    Results.Text = string.Format ("Error: {0}", response.StatusCode);
                });
            }
        } else {
            // No, display error
            InvokeOnMainThread (() => {
                Results.Text = string.Format ("Error: {0}", error);
            });
        }
    });
}
#endregion
```

此代码，上面介绍的 Twitter 版本之间唯一的真正的区别是 Facebook 的要求，以获取应用程序开发人员/特定 ID （其中你可以从 Facebook 的开发人员门户生成） 发出请求时必须将其设置为一个选项：

```csharp
var options = new AccountStoreOptions ();
var options.FacebookAppId = ""; // Enter your specific Facebook App ID here
...

// Request access to Facebook account
accountStore.RequestAccess (accountType, options, (granted, error) => {
    ...
});
```

未能设置此选项 （或使用了无效密钥） 将导致错误或返回任何数据。

## <a name="summary"></a>总结

本文介绍了如何使用社交框架与 Twitter 和 Facebook 进行交互。 它介绍了在设备设置中配置帐户以执行每个社交网络的位置。 它还介绍了如何使用`SLComposeViewController`提供发布到社交网络的统一的视图。 此外，它会检查`SLRequest`类，用于调用每个社交网络的 API。


## <a name="related-links"></a>相关链接

- [SocialFrameworkDemo （示例）](https://developer.xamarin.com/samples/SocialFrameworkDemo/)
- [Web 服务简介](~/cross-platform/data-cloud/web-services/index.md)
