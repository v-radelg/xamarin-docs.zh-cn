---
title: 在 Xamarin.iOS 中的增强型的用户通知
description: 本指南介绍了在 iOS 10 中引入的用户通知框架。 它讨论了本地通知、 远程通知，通知管理、 通知操作和的详细信息。
ms.prod: xamarin
ms.assetid: 4E1FF652-28F0-4566-B383-9D12664401A4
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/02/2017
ms.openlocfilehash: afa20a264e2509a5658cd0d8f90da3148315e803
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2019
ms.locfileid: "67865726"
---
# <a name="enhanced-user-notifications-in-xamarinios"></a>在 Xamarin.iOS 中的增强型的用户通知

新 iOS 10，其用户通知框架允许传递和处理本地和远程通知。 使用此框架，应用程序或应用扩展可以计划本地通知的传递通过指定一组条件，如位置或一天的时间。

## <a name="about-user-notifications"></a>有关用户通知

如上面所述，新的用户通知框架允许传递和处理本地和远程通知。 使用此框架，应用程序或应用扩展可以计划本地通知的传递通过指定一组条件，如位置或一天的时间。

此外，应用或扩展可以接收 （并可能修改） 本地和远程通知在传递到用户的 iOS 设备。

新的用户通知 UI 框架允许应用程序扩展时向用户显示自定义的本地和远程通知的外观。

此框架提供了应用程序可以将通知发送到用户的以下方面：

- **可视化警报**-其中通知将向下滚动以横幅形式屏幕的顶部。
- **声音和振动**-可以与通知相关联。
- **应用图标徽章**-应用程序的图标会显示锁屏提醒显示新内容可用，例如，未读的电子邮件数在那里。

此外，具体取决于用户的当前上下文中，有不同的方式，将显示一个通知：

- 如果设备已解锁，通知将向下滚动从屏幕顶部以横幅形式。
- 如果设备处于锁定状态，将用户的锁定屏幕上显示通知。
- 如果用户已丢失一条通知，他们可以打开通知中心并查看任何可用，等待通知。

Xamarin.iOS 应用程序有两种类型的用户能够发送的通知：

- **本地通知**-这些发送的用户设备上本地安装的应用。
- **远程通知**-发送从远程服务器，并向用户显示或它们触发后台更新的应用程序的内容。

### <a name="about-local-notifications"></a>有关本地通知

IOS 应用程序可以发送本地通知具有以下功能和特性：

- 它们是发送了本地用户的设备上的应用。 
- 它们是可配置为使用时间和地点基于触发器。 
- 应用程序与用户的设备计划通知和触发条件得以满足时显示它。
- 在用户交互时发送通知，应用程序将收到回调。

本地通知的一些示例包括：

- 日历警报
- 提醒警报
- 位置感知触发器

有关详细信息，请参阅 Apple[本地和远程通知编程指南](https://developer.apple.com/documentation/usernotifications)文档。

### <a name="about-remote-notifications"></a>有关远程通知

IOS 应用程序可以发送远程通知的以下功能和特性：

- 应用程序具有与之通信的服务器端组件。
- Apple 推送通知服务 (APNs) 用于从开发人员的基于云的服务器传输到用户的设备的远程通知的最大努力交货。
- 当应用程序接收远程通知时，它将向用户显示。
- 在用户交互与通知时，应用程序将收到回调。

远程通知的一些示例包括：

- 新闻的提示
- 体育方面的更新
- 即时消息传送消息

有两种类型的远程通知到 iOS 应用程序可用：

- **面向用户**-这些设备上向用户显示。
- **无提示更新**-这些提供一种机制，以更新在后台中的 iOS 应用的内容。 当收到无提示更新时，应用可以联系到删除的服务器请求内容的最新内容。

有关详细信息，请参阅 Apple[本地和远程通知编程指南](https://developer.apple.com/documentation/usernotifications)文档。

### <a name="about-the-existing-notifications-api"></a>有关 API 的现有通知

在 iOS 10 之前的 iOS 应用将使用`UIApplication`向系统注册通知并计划 （无论是时间或地点） 应如何触发该通知。

有几个使用现有通知 API 时，开发人员可能会遇到的问题：

- 没有所需的本地或远程通知这可能导致重复代码的不同回调。
- 应用有限通知的控件之后它已与系统已计划了。
- 跨所有 Apple 的现有平台都没有不同的支持级别。

### <a name="about-the-new-user-notification-framework"></a>有关新的用户通知框架

与 iOS 10、 Apple 引入了新的用户通知框架将替换现有`UIApplication`上文所述的方法。

用户通知框架提供以下功能：

- 与以前的方法就可以轻松移植代码从现有框架包括功能奇偶一致性熟悉 API。
- 包括一组扩展的内容选项，允许更丰富的通知发送到用户。
- 可以通过相同的代码和回调处理本地和远程通知。
- 简化了处理用户交互发送通知时发送到应用程序的回调的流程。
- 挂起并传送通知包括删除或更新通知的功能的增强的管理。
- 添加了执行操作的通知的应用程序内演示的功能。
- 添加了计划和处理从内部应用扩展的通知的功能。
- 添加新的通知本身的扩展点。 

新的用户通知框架提供了一个统一的通知 API 跨平台的多个 Apple 支持包括： 

- **iOS** -完全支持来管理和安排通知。
- **tvOS** -将功能添加到本地和远程通知的徽章应用图标。
- **watchOS** -添加了将转发到其 Apple Watch 通知从用户的配对的 iOS 设备的功能，并使 watch 应用能够执行直接在 watch 自身上的本地通知。

有关详细信息，请参阅 Apple [UserNotifications 框架引用](https://developer.apple.com/reference/usernotifications)并[UserNotificationsUI](https://developer.apple.com/reference/usernotificationsui)文档。

## <a name="preparing-for-notification-delivery"></a>为通知传递准备

之前 iOS 应用可以将通知发送到应用程序必须向系统注册的用户和应用程序由于一条通知是向用户中断，必须显式请求权限，然后将它们发送。

有三个不同级别的通知请求的用户可以批准的应用：

- 横幅显示。
- 声音警报。
- 徽章向应用程序图标。

此外，还必须请求并为本地和远程通知设置这些审核级别。

应请求通知的权限，只要该应用将启动以下代码添加到`FinishedLaunching`方法`AppDelegate`并设置所需的通知类型 (`UNAuthorizationOptions`):

```csharp
using UserNotifications;
...

public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
{
    // Request notification permissions from the user
    UNUserNotificationCenter.Current.RequestAuthorization (UNAuthorizationOptions.Alert, (approved, err) => {
        // Handle approval
    });

    return true;
}
```

此外，用户可以随时更改应用程序在任何时间使用的通知特权**设置**在设备上的应用。 应用应开始演示使用下面的代码的通知之前先检查用户的请求的通知权限：

```csharp
// Get current notification settings
UNUserNotificationCenter.Current.GetNotificationSettings ((settings) => {
    var alertsAllowed = (settings.AlertSetting == UNNotificationSetting.Enabled);
});    
``` 

### <a name="configuring-the-remote-notifications-environment"></a>配置远程通知环境

新开发人员必须向 iOS 10，通知将推送通知哪种环境中以开发或生产运行的操作系统。 如果未提供此信息可能会导致应用程序提交到 iTune 应用商店使用类似于以下的通知时被拒绝：

> 缺少的推送通知权利-您的应用程序包括用于 Apple 的推送通知服务的 API 但`aps-environment`权利缺少应用程序的签名。

若要提供所需的权利，执行以下操作：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 双击`Entitlements.plist`文件中**Solution Pad**打开进行编辑。
2. 切换到**源**视图： 

    [![](enhanced-user-notifications-images/setup01.png "源视图")](enhanced-user-notifications-images/setup01.png#lightbox)
3. 单击 **+** 按钮以添加新的密钥。
4. 输入`aps-environment`有关**属性**，将保留**类型**作为`String`并输入`development`或`production`为**值**: 

    [![](enhanced-user-notifications-images/setup02.png "Aps 环境属性")](enhanced-user-notifications-images/setup02.png#lightbox)
5. 保存对文件所做的更改。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 双击`Entitlements.plist`文件中**解决方案资源管理器**打开进行编辑。
2. 单击 **+** 按钮以添加新的密钥。
3. 输入`aps-environment`有关**属性**，将保留**类型**作为`String`并输入`development`或`production`为**值**: 

    [![](enhanced-user-notifications-images/setup02w.png "Aps 环境属性")](enhanced-user-notifications-images/setup02.png#lightbox)
4. 保存对文件所做的更改。

-----

### <a name="registering-for-remote-notifications"></a>注册远程通知

如果将发送和接收远程通知应用程序，它仍需要执行操作_令牌注册_使用现有`UIApplication`API。 此注册需要设备以实现实时的网络连接访问 APNs，将生成将发送到应用的必要令牌。 应用程序必须再将转发到开发人员的服务器端应用程序以注册远程通知此令牌：

[![](enhanced-user-notifications-images/token01.png "令牌注册概述")](enhanced-user-notifications-images/token01.png#lightbox)

使用以下代码以初始化所需的注册：

```csharp
UIApplication.SharedApplication.RegisterForRemoteNotifications ();
```

获取发送到开发人员的服务器端应用的令牌将需要的通知有效负载的 get 的一部分发送从服务器向 APNs 发送远程通知时要包含：

[![](enhanced-user-notifications-images/token02.png "通知有效负载中包含令牌")](enhanced-user-notifications-images/token02.png#lightbox)

令牌的作用好似将联系在一起，通知和应用程序打开或响应通知使用的密钥。

有关详细信息，请参阅 Apple[本地和远程通知编程指南](https://developer.apple.com/documentation/usernotifications)文档。

## <a name="notification-delivery"></a>通知传达

与该应用程序完全注册并从请求所需的权限并授予用户，应用程序现在已准备好发送和接收通知。 

### <a name="providing-notification-content"></a>提供通知内容

新 ios 10、 所有通知同时都包含**标题**和**副标题**，将始终显示与**正文**的通知内容。 新增功能还包括，能够添加**媒体附件**通知内容。

若要创建的本地通知内容，请使用以下代码：

```csharp
var content = new UNMutableNotificationContent();
content.Title = "Notification Title";
content.Subtitle = "Notification Subtitle";
content.Body = "This is the message body of the notification.";
content.Badge = 1;
```

对远程通知的过程是类似：

```csharp
{
    "aps":{
        "alert":{
            "title":"Notification Title",
            "subtitle":"Notification Subtitle",
            "body":"This is the message body of the notification."
        },
        "badge":1
    }
}
```

### <a name="scheduling-when-a-notification-is-sent"></a>发送计划时的通知

使用创建通知的内容，应用程序需要计划时将通过设置向用户显示通知*触发器*。 iOS 10 提供了四种不同的触发器类型：

- **推送通知**-以独占方式用于远程通知和 APNs 通知发送到设备上运行的应用打包时，会触发。
- **时间间隔**-允许本地通知，以从时间计划现在和将来某个时间点结束的间隔开始。 例如，`var trigger =  UNTimeIntervalNotificationTrigger.CreateTrigger (5, false);`
- **日历日期**-允许本地通知，以按计划进行特定日期和时间。
- **位置基于**-允许本地通知，以进入或离开特定地理位置或任何蓝牙信号给定接近 iOS 设备时，其计划。

本地通知准备就绪后，需要调用应用程序`Add`方法的`UNUserNotificationCenter`对象来计划其显示给用户。 对于远程通知，服务器端应用发送通知有效负载向 APNs，后者随后将发送到用户的设备的数据包。

将所有部分集中起来，本地通知的示例可能都如下所示：

```csharp
using UserNotifications;
...

var content = new UNMutableNotificationContent ();
content.Title = "Notification Title";
content.Subtitle = "Notification Subtitle";
content.Body = "This is the message body of the notification.";
content.Badge = 1;

var trigger =  UNTimeIntervalNotificationTrigger.CreateTrigger (5, false);

var requestID = "sampleRequest";
var request = UNNotificationRequest.FromIdentifier (requestID, content, trigger);

UNUserNotificationCenter.Current.AddNotificationRequest (request, (err) => {
    if (err != null) {
        // Do something with error...
    }
});
```

## <a name="handling-foreground-app-notifications"></a>处理前台应用程序通知

新增到 iOS 10，应用可以通知以不同方式处理时位于前台，并触发通知。 通过提供`UNUserNotificationCenterDelegate`和实现`WillPresentNotification`方法，该应用程序可以接管通过显示通知。 例如：

```csharp
using System;
using UserNotifications;

namespace MonkeyNotification
{
    public class UserNotificationCenterDelegate : UNUserNotificationCenterDelegate
    {
        #region Constructors
        public UserNotificationCenterDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override void WillPresentNotification (UNUserNotificationCenter center, UNNotification notification, Action<UNNotificationPresentationOptions> completionHandler)
        {
            // Do something with the notification
            Console.WriteLine ("Active Notification: {0}", notification);

            // Tell system to display the notification anyway or use
            // `None` to say we have handled the display locally.
            completionHandler (UNNotificationPresentationOptions.Alert);
        }
        #endregion
    }
}
```

此代码只需编写出的内容`UNNotification`为应用程序输出，将要求系统显示标准的警报的通知。 

如果应用程序想要在前台时显示通知本身并不使用系统默认值，则传递`None`到完成处理程序。 示例:

```csharp
completionHandler (UNNotificationPresentationOptions.None);
```

利用此代码，打开`AppDelegate.cs`文件以进行编辑，并更改`FinishedLaunching`方法看起来如下所示：

```csharp
public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
{
    // Request notification permissions from the user
    UNUserNotificationCenter.Current.RequestAuthorization (UNAuthorizationOptions.Alert, (approved, err) => {
        // Handle approval
    });

    // Watch for notifications while the app is active
    UNUserNotificationCenter.Current.Delegate = new UserNotificationCenterDelegate ();

    return true;
}
```

此代码附加自定义`UNUserNotificationCenterDelegate`上面提供的与当前`UNUserNotificationCenter`因此处于活动状态时，应用可以处理通知，并在前景中。

## <a name="notification-management"></a>通知管理

新 ios 10、 通知管理挂起并传送通知提供访问，并添加了要删除、 更新或升级这些通知的功能。

通知管理的一个重要部分是_请求标识符_时创建和计划与系统的已分配给通知。 有关远程通知，这分配通过新`apps-collapse-id`字段中的 HTTP 请求标头。

请求标识符用于选择该应用程序希望执行通知管理上的通知。

### <a name="removing-notifications"></a>删除通知

若要从系统删除挂起的通知，请使用以下代码：

```csharp
var requests = new string [] { "sampleRequest" };
UNUserNotificationCenter.Current.RemovePendingNotificationRequests (requests);
```

若要删除的已传递的通知，请使用以下代码：

```csharp
var requests = new string [] { "sampleRequest" };
UNUserNotificationCenter.Current.RemoveDeliveredNotifications (requests);
```

### <a name="updating-an-existing-notification"></a>更新现有的通知

若要更新现有的通知，只需创建和修改 （如新的触发时间） 所需参数的新通知，并将其添加到具有相同的请求标识符为需要对其进行修改的通知系统。 示例:


```csharp
using UserNotifications;
...

// Rebuild notification
var content = new UNMutableNotificationContent ();
content.Title = "Notification Title";
content.Subtitle = "Notification Subtitle";
content.Body = "This is the message body of the notification.";
content.Badge = 1;

// New trigger time
var trigger = UNTimeIntervalNotificationTrigger.CreateTrigger (10, false);

// ID of Notification to be updated
var requestID = "sampleRequest";
var request = UNNotificationRequest.FromIdentifier (requestID, content, trigger);

// Add to system to modify existing Notification
UNUserNotificationCenter.Current.AddNotificationRequest (request, (err) => {
    if (err != null) {
        // Do something with error...
    }
});
```

已送达通知，将获取更新和提升到列表主页和锁定屏幕和通知中心中的顶部，如果已读取用户现有的通知。

## <a name="working-with-notification-actions"></a>使用通知操作

在 iOS 10 中，提供给用户的通知不是静态的并提供用户可交互的多种方式使用它们 （从内置于自定义操作）。

有三种类型的 iOS 应用程序可以响应的操作：

- **默认操作**-这是当用户点击通知以打开应用并显示给定通知的详细信息。
- **自定义操作**-这些 iOS 8 以添加的并提供用户直接从通知执行自定义任务，而无需启动该应用程序的快速方法。 它们可以显示为一组具有可自定义标题的按钮或文本输入的字段可以运行在 （其中应用程序指定少量的时间来完成该请求） 的背景或前景中 （其中启动应用程序在前台到 fulfill 请求)。 IOS 和 watchOS 上提供了自定义操作。
- **关闭操作**-当用户关闭给定的通知时，此操作发送到应用。

### <a name="creating-custom-actions"></a>创建自定义操作

若要创建并向系统注册自定义操作，使用以下代码：

```csharp
// Create action
var actionID = "reply";
var title = "Reply";
var action = UNNotificationAction.FromIdentifier (actionID, title, UNNotificationActionOptions.None);

// Create category
var categoryID = "message";
var actions = new UNNotificationAction [] { action };
var intentIDs = new string [] { };
var categoryOptions = new UNNotificationCategoryOptions [] { };
var category = UNNotificationCategory.FromIdentifier (categoryID, actions, intentIDs, UNNotificationCategoryOptions.None);
    
// Register category
var categories = new UNNotificationCategory [] { category };
UNUserNotificationCenter.Current.SetNotificationCategories (new NSSet<UNNotificationCategory>(categories)); 
```

在创建新`UNNotificationAction`，它分配一个唯一的 ID 和标题，它将出现在按钮上。 默认情况下，将创建操作作为后台操作，但可以提供选项来调整此动作的行为 （例如将其设置为前台操作）。

每个创建的操作需要与类别相关联。 在创建新`UNNotificationCategory`、 其分配唯一 ID，它可以执行的操作的列表，意图 Id，以提供有关意向的类别中的操作的详细信息和某些选项来控制行为的类别的列表。

最后，所有的类别注册到系统使用`SetNotificationCategories`方法。

### <a name="presenting-custom-actions"></a>提供自定义操作

一旦已创建并向系统注册的一组自定义操作和类别，可以从本地或远程通知向他们显示。

对于远程通知设置`category`与上面创建的类别之一相匹配的远程通知有效负载中。 例如：

```csharp
{
    aps:{
        alert:"Hello world!",
        category:"message"
    }
}
```

对于本地通知设置`CategoryIdentifier`属性的`UNMutableNotificationContent`对象。 例如:

```csharp
var content = new UNMutableNotificationContent ();
content.Title = "Notification Title";
content.Subtitle = "Notification Subtitle";
content.Body = "This is the message body of the notification.";
content.Badge = 1;
content.CategoryIdentifier = "message";
...
```

同样，此 ID 必须与上面创建的类别之一匹配。

### <a name="handling-dismiss-actions"></a>处理取消操作

如上面所述，关闭操作在用户关闭通知可以发送到应用。 由于这不是标准操作，需要创建类别时，设置选项。 例如:

```csharp
var categoryID = "message";
var actions = new UNNotificationAction [] { action };
var intentIDs = new string [] { };
var categoryOptions = new UNNotificationCategoryOptions [] { };
var category = UNNotificationCategory.FromIdentifier (categoryID, actions, intentIDs, UNNotificationCategoryOptions.CustomDismissAction);

```

### <a name="handling-action-responses"></a>处理操作响应

当用户交互使用的自定义操作和上面创建的类别时，应用需要以完成请求的任务。 这是通过提供`UNUserNotificationCenterDelegate`和实现`UserNotificationCenter`方法。 例如：

```csharp
using System;
using UserNotifications;

namespace MonkeyNotification
{
    public class UserNotificationCenterDelegate : UNUserNotificationCenterDelegate
    {
        ...

        #region Override Methods
        public override void DidReceiveNotificationResponse (UNUserNotificationCenter center, UNNotificationResponse response, Action completionHandler)
        {
            // Take action based on Action ID
            switch (response.ActionIdentifier) {
            case "reply":
                // Do something
                break;
            default:
                // Take action based on identifier
                if (response.IsDefaultAction) {
                    // Handle default action...
                } else if (response.IsDismissAction) {
                    // Handle dismiss action
                }
                break;
            }

            // Inform caller it has been handled
            completionHandler();
        }
        #endregion
    }
}
```

传递中`UNNotificationResponse`类具有`ActionIdentifier`属性可以是默认操作或关闭操作。 使用`response.Notification.Request.Identifier`若要测试的任何自定义操作。

`UserText`属性包含的任何用户文本输入的值。 `Notification`属性包含原始通知，其中包括与触发器请求并通知内容。 如果它是本地或远程通知基于触发器的类型，可以决定应用程序。

> [!NOTE]
> iOS 12 使自定义通知 UI 要修改其在运行时的操作按钮。 有关详细信息，看一看[动态通知操作按钮](~/ios/platform/introduction-to-ios12/notifications/dynamic-actions.md)文档。

## <a name="working-with-service-extensions"></a>使用服务扩展

使用远程通知时_服务扩展_提供一种启用通知有效负载内的端到端加密方法。 服务扩展是通知的一个用户界面扩展 （适用于 iOS 10），在使用扩充或替换可见内容显示给用户的主要目的在后台中运行。 

[![](enhanced-user-notifications-images/extension01.png "服务扩展概述")](enhanced-user-notifications-images/extension01.png#lightbox)

服务扩展旨在快速运行，并仅有一小段时间由系统执行。 在的服务扩展无法在分配时间内完成其任务，将调用的回退方法。 如果回退失败，原始通知内容将显示给用户。

服务扩展的某些可能用途包括：

- 提供远程通知内容的端到端的加密。
- 将附件添加到远程通知，以将其扩充。

### <a name="implementing-a-service-extension"></a>实现服务扩展插件

若要实现一个服务扩展在 Xamarin.iOS 应用程序中，执行以下操作：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 打开应用的解决方案在 Visual Studio for mac。
2. 右键单击解决方案名称在**Solution Pad** ，然后选择**添加** > **添加新项目**。
3. 选择**iOS** > **扩展** > **通知服务扩展**然后单击**下一步**按钮： 

    [![](enhanced-user-notifications-images/extension02.png "选择通知服务扩展")](enhanced-user-notifications-images/extension02.png#lightbox)
4. 输入**名称**作为扩展，然后单击**下一步**按钮： 

    [![](enhanced-user-notifications-images/extension03.png "输入扩展的名称")](enhanced-user-notifications-images/extension03.png#lightbox)
5. 调整**项目名称**和/或**解决方案名称**如果需要，再单击**创建**按钮： 

    [![](enhanced-user-notifications-images/extension04.png "调整项目名称和/或解决方案名称")](enhanced-user-notifications-images/extension04.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 在 Visual Studio 中打开应用的解决方案。
2. 右键单击解决方案名称在**解决方案资源管理器**，然后选择**添加 > 新建项目...** .
3. 选择**可视化C#> iOS 扩展 > 通知服务扩展**:

    [![](enhanced-user-notifications-images/extension01.w157-sml.png "选择通知服务扩展")](enhanced-user-notifications-images/extension01.w157.png#lightbox)
4. 输入**名称**作为扩展，然后单击**确定**按钮。

-----

> [!IMPORTANT]
> 服务扩展的捆绑标识符应与使用主应用程序的捆绑包标识符匹配`.appnameserviceextension`追加到末尾。 例如，如果主应用程序必须由捆绑包标识符`com.xamarin.monkeynotify`，服务扩展应具有的捆绑标识符`com.xamarin.monkeynotify.monkeynotifyserviceextension`。 这应会自动设置时将扩展添加到解决方案。 

将需要进行修改，以提供所需的功能的通知服务扩展中没有一个主类。 例如:

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;

namespace MonkeyChatServiceExtension
{
    [Register ("NotificationService")]
    public class NotificationService : UNNotificationServiceExtension
    {
        #region Computed Properties
        public Action<UNNotificationContent> ContentHandler { get; set; }
        public UNMutableNotificationContent BestAttemptContent { get; set; }
        #endregion

        #region Constructors
        protected NotificationService (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void DidReceiveNotificationRequest (UNNotificationRequest request, Action<UNNotificationContent> contentHandler)
        {
            ContentHandler = contentHandler;
            BestAttemptContent = (UNMutableNotificationContent)request.Content.MutableCopy ();

            // Modify the notification content here...
            BestAttemptContent.Title = $"{BestAttemptContent.Title}[modified]";

            ContentHandler (BestAttemptContent);
        }

        public override void TimeWillExpire ()
        {
            // Called just before the extension will be terminated by the system.
            // Use this as an opportunity to deliver your "best attempt" at modified content, otherwise the original push payload will be used.

            ContentHandler (BestAttemptContent);
        }
        #endregion
    }
}
```

第一种方法， `DidReceiveNotificationRequest`，将通知标识符，以及通过的内容通知传递`request`对象。 传递中`contentHandler`将需要调用以向用户显示通知。

第二种方法， `TimeWillExpire`，将时间即将耗尽服务扩展来处理请求之前调用。 如果服务扩展无法调用`contentHandler`在分配时间内，将向用户显示的原始内容。

### <a name="triggering-a-service-extension"></a>触发服务扩展

与服务扩展创建和传递与该应用程序，可以通过修改远程通知有效负载发送到设备触发它。 例如:

```csharp
{
    aps : {
        alert : "New Message Available",
        mutable-content: 1
    },
    encrypted-content : "#theencryptedcontent"
}
```

新`mutable-content`键指定服务扩展将需要启动更新远程通知内容。 `encrypted-content`键会保存服务扩展可以开始向用户演示之前先解密的加密的数据。

看看下面的示例服务扩展：

```csharp
using UserNotification;

namespace myApp {
    public class NotificationService : UNNotificationServiceExtension {
    
        public override void DidReceiveNotificationRequest(UNNotificationRequest request, contentHandler) {
            // Decrypt payload
            var decryptedBody = Decrypt(Request.Content.UserInfo["encrypted-content"]);
            
            // Modify Notification body
            var newContent = new UNMutableNotificationContent();
            newContent.Body = decryptedBody;
            
            // Present to user
            contentHandler(newContent);
        }
        
        public override void TimeWillExpire() {
            // Handle out-of-time fallback event
            ...
        }
        
    }
}
```

此代码会解密从加密的内容`encrypted-content`密钥，请创建一个新`UNMutableNotificationContent`，设置`Body`属性设置为已解密的内容并使用`contentHandler`以向用户显示通知。

## <a name="summary"></a>总结

本文介绍的所有 ios 10 已经增强了用户通知方法。 它提供新的用户通知框架以及如何在 Xamarin.iOS 应用程序或应用扩展中使用它。



## <a name="related-links"></a>相关链接

- [iOS 10 示例](https://developer.xamarin.com/samples/ios/iOS10/)
- [UserNotifications 框架引用](https://developer.apple.com/reference/usernotifications)
- [UserNotificationsUI](https://developer.apple.com/reference/usernotificationsui)
- [本地和远程通知编程指南](https://developer.apple.com/documentation/usernotifications)
