---
title: Xamarin 中的社交框架
description: 社交框架提供了一个统一的 API，用于与中国的用户（包括 Twitter 和 Facebook）以及 SinaWeibo 的社交网络交互。
ms.prod: xamarin
ms.assetid: A1C28E66-AA20-1C13-23AF-5A8712E6C752
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/20/2017
ms.openlocfilehash: 9c5714d5819831047d10d83b4de01fc826b9cc01
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031469"
---
# <a name="social-framework-in-xamarinios"></a>Xamarin 中的社交框架

_社交框架提供了一个统一的 API，用于与中国的用户（包括 Twitter 和 Facebook）以及 SinaWeibo 的社交网络交互。_

使用社交框架，应用程序可以从单个 API 与社交网络交互，而无需管理身份验证。 它包括系统提供的用于撰写帖子的视图控制器，以及允许通过 HTTP 使用各个社交网络 API 的抽象。

## <a name="connecting-to-twitter"></a>连接到 Twitter

### <a name="twitter-account-settings"></a>Twitter 帐户设置

若要使用社交框架连接 Twitter，需要在设备设置中配置帐户，如下所示：

 [![](social-framework-images/twitter01.png "Twitter Account Settings")](social-framework-images/twitter01.png#lightbox)

使用 Twitter 输入和验证帐户后，设备上使用社交框架类访问 Twitter 的任何应用程序都将使用此帐户。

### <a name="sending-tweets"></a>发送推文

社交框架包含一个名为 `SLComposeViewController` 的控制器，该控制器提供了一个用于编辑和发送推文的系统提供视图。 以下屏幕截图显示了此视图的一个示例：

 [![](social-framework-images/twitter02.png "This screenshot shows an example of the SLComposeViewController")](social-framework-images/twitter02.png#lightbox)

若要将 `SLComposeViewController` 与 Twitter 一起使用，必须通过在 `SLServiceType.Twitter` 调用 `FromService` 方法来创建控制器实例，如下所示：

```csharp
var slComposer = SLComposeViewController.FromService (SLServiceType.Twitter);
```

返回 `SLComposeViewController` 实例后，可以使用它来呈现 UI，以发布到 Twitter。 但首先要做的事情就是通过调用 `IsAvailable`来检查社交网络（在本例中为 Twitter）的可用性：

```csharp
if (SLComposeViewController.IsAvailable (SLServiceKind.Twitter)) {
  ...
}
```

 `SLComposeViewController` 决不会直接发送推文，而无需用户交互。 但是，可以通过以下方法对其进行初始化：

- `SetInitialText` –添加要在推文中显示的初始文本。
- `AddUrl` –将 Url 添加到推文。
- `AddImage` –将图像添加到推文。

初始化后，调用 `PresentVIewController` 将显示 `SLComposeViewController`创建的视图。 然后，用户可以选择编辑并发送推文，或取消发送。 无论是哪种情况，控制器都应在 `CompletionHandler`中解除，还可以检查结果，以查看推文是已发送还是已取消，如下所示：

```csharp
slComposer.CompletionHandler += (result) => {
  InvokeOnMainThread (() => {
    DismissViewController (true, null);
    resultsTextView.Text = result.ToString ();
  });
};
```

#### <a name="tweet-example"></a>推文示例

下面的代码演示如何使用 `SLComposeViewController` 显示用于发送推文的视图：

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

社交框架还包括对向社交网络发出 HTTP 请求的支持。 它将请求封装到用于面向特定社交网络 API 的 `SLRequest` 类中。

例如，以下代码向 Twitter 发出请求，以获取公共时间线（通过扩展上面给出的代码）：

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

让我们详细了解一下此代码。 首先，它获得帐户存储的访问权限，并获取 Twitter 帐户的类型：

```csharp
var accountStore = new ACAccountStore ();
var accountType = accountStore.FindAccountType (ACAccountType.Twitter);
```

接下来，如果你的应用程序可以访问其 Twitter 帐户，则会询问用户，如果已授予访问权限，则该帐户将加载到内存中并更新 UI：

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

当用户请求时间线数据时（通过点击 UI 中的按钮），应用首先会形成从 Twitter 访问数据的请求：

```csharp
// Initialize request
var parameters = new NSDictionary ();
var url = new NSUrl("https://api.twitter.com/1.1/statuses/user_timeline.json?count=10");
var request = SLRequest.Create (SLServiceKind.Twitter, SLRequestMethod.Get, url, parameters);
```

此示例通过在 URL 中包含 `?count=10` 来将返回的结果限制为最后10个条目。 最后，它会将请求附加到 Twitter 帐户（在上面加载），并对 Twitter 执行调用以提取数据：

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

如果数据已成功加载，将显示原始 JSON 数据（如下面的示例输出所示）：

[![](social-framework-images/twitter03.png "An example of the raw JSON data display")](social-framework-images/twitter03.png#lightbox)

在实际应用中，可以将 JSON 结果分析为正常，并向用户显示结果。 请参阅[Web 服务简介](~/cross-platform/data-cloud/web-services/index.md)，了解有关如何分析 JSON 的信息。

## <a name="connecting-to-facebook"></a>连接到 Facebook

### <a name="facebook-account-settings"></a>Facebook 帐户设置

使用社交框架连接到 Facebook 几乎与用于 Twitter 的过程完全相同。 必须在设备设置中配置 Facebook 用户帐户，如下所示：

[![](social-framework-images/facebook01.png "Facebook Account Settings")](social-framework-images/facebook01.png#lightbox)

配置后，设备上使用社交框架的任何应用程序都将使用此帐户连接到 Facebook。

### <a name="posting-to-facebook"></a>发布到 Facebook

由于社交框架是设计用于访问多个社交网络的统一 API，因此无论使用何种社交网络，代码几乎都是相同的。

例如，`SLComposeViewController` 的使用方式与前面所示的 Twitter 示例完全相同，只是切换到特定于 Facebook 的设置和选项。 例如:

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

与 Facebook 结合使用时，`SLComposeViewController` 会显示与 Twitter 示例大致相同的视图，在本例中显示**Facebook**作为标题：

[![](social-framework-images/facebook02.png "The SLComposeViewController display")](social-framework-images/facebook02.png#lightbox)

### <a name="calling-facebook-graph-api"></a>调用 Facebook 图形 API

类似于 Twitter 示例，社会框架的 `SLRequest` 对象可以与 Facebook 的 graph API 一起使用。 例如，下面的代码从 graph API 返回有关 Xamarin 帐户的信息（通过展开上面给出的代码）：

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

此代码与上面提供的 Twitter 版本之间唯一的区别在于 Facebook 需要获取开发人员/应用特定 ID （可从 Facebook 开发人员门户生成），在发出请求时，必须将其设置为选项：

```csharp
var options = new AccountStoreOptions ();
var options.FacebookAppId = ""; // Enter your specific Facebook App ID here
...

// Request access to Facebook account
accountStore.RequestAccess (accountType, options, (granted, error) => {
    ...
});
```

如果未设置此选项（或使用无效的键），将导致错误或不返回任何数据。

## <a name="summary"></a>总结

本文演示了如何使用社交框架与 Twitter 和 Facebook 交互。 其中显示了在何处为设备设置中的每个社交网络配置帐户。 还介绍了如何使用 `SLComposeViewController` 显示用于发布到社交网络的统一视图。 此外，它还检查了用于调用每个社交网络 API 的 `SLRequest` 类。

## <a name="related-links"></a>相关链接

- [SocialFrameworkDemo （示例）](https://docs.microsoft.com/samples/xamarin/ios-samples/socialframeworkdemo)
- [Web 服务简介](~/cross-platform/data-cloud/web-services/index.md)
