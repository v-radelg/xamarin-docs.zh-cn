---
title: 在 Xamarin.iOS 中的高级的用户通知
description: 本文将深入地了解在 iOS 10 中引入的用户通知框架。 它讨论用户通知、 用户通知界面、 媒体附件、 自定义用户界面和的详细信息。
ms.prod: xamarin
ms.assetid: 4E0C60AE-6F54-4098-8FA0-AADF9AC86805
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/03/2018
ms.openlocfilehash: 255603eefb4d7cfd3b906e1744aa19da6a77259a
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2019
ms.locfileid: "67865291"
---
# <a name="advanced-user-notifications-in-xamarinios"></a>在 Xamarin.iOS 中的高级的用户通知

新 iOS 10，其用户通知框架允许传递和处理本地和远程通知。 使用此框架，应用程序或应用扩展可以计划本地通知的传递通过指定一组条件，如位置或一天的时间。

## <a name="about-user-notifications"></a>有关用户通知

新的用户通知框架允许传递和处理本地和远程通知。 使用此框架，应用程序或应用扩展可以计划本地通知的传递通过指定一组条件，如位置或一天的时间。

此外，应用或扩展可以接收 （并可能修改） 本地和远程通知在传递到用户的 iOS 设备。

新的用户通知 UI 框架允许应用程序扩展时向用户显示自定义的本地和远程通知的外观。

此框架提供了应用程序可以将通知发送到用户的以下方面：

- **可视化警报**-其中通知将向下滚动以横幅形式屏幕的顶部。
- **声音和振动**-可以与通知相关联。
- **应用图标徽章**-可供显示新内容可显示一个徽章，应用程序的图标。 如未读电子邮件消息数。

此外，具体取决于用户的当前上下文中，有不同的方式，将显示一个通知：

- 如果设备已解锁，通知将向下滚动从屏幕顶部以横幅形式。
- 如果设备处于锁定状态，将用户的锁定屏幕上显示通知。
- 如果用户已丢失一条通知，他们可以打开通知中心并查看任何可用，等待通知。

Xamarin.iOS 应用程序有两种类型的用户能够发送的通知：

- **本地通知**-这些发送的用户设备上本地安装的应用。
- **远程通知**-从远程服务器，并向用户显示发送或触发后台更新的应用程序的内容。

有关详细信息，请参阅我们[增强型用户通知](~/ios/platform/user-notifications/enhanced-user-notifications.md)文档。

## <a name="the-new-user-notification-interface"></a>新的用户通知接口

在 iOS 10 中的用户通知会显示为的设备或通知中心顶部横幅在锁定屏幕上，提供更多的内容，如标题、 副标题和可选媒体附件，可以提供该新用户界面设计。

无论其中用户通知显示在 iOS 10 中，提供具有相同的外观和使用相同的特性和功能。

在 iOS 8 中，Apple 引入了可操作的通知，其中开发人员可以将自定义操作附加到一条通知，并允许用户对通知执行操作，而无需启动该应用程序。 在 iOS 9 中，Apple 增强的可操作的通知使用快速回复这样用户就可以对包含文本输入的通知作出响应。

由于用户通知是在 iOS 10 中的用户体验的更多组成部分，Apple 已进一步扩展可操作的通知，以支持 3D Touch，其中用户按上一条通知，自定义用户界面是显示效果以提供丰富的交互与通知。

当显示自定义用户通知 UI 时，如果用户与附加到该通知的任何操作时，自定义 UI 可以立即更新，以提供反馈已更改的内容。

新 iOS 10，向用户通知 UI API 允许 Xamarin.iOS 应用程序以轻松地利用这些新的用户通知 UI 功能。

## <a name="adding-media-attachments"></a>添加媒体附件

获取共享的用户之间的更常见项目之一是上下文的照片，因此 iOS 10 添加附加媒体项 （例如照片） 的功能直接向通知，它将在提交并可随时提供其余部分通知的用户和nt。

但是，由于发送所涉及的大小更小图像，将其附加到远程通知有效负载会变得不切实际。 若要处理这种情况下，开发人员可以使用新的服务扩展在 iOS 10 中从另一个源 （如 CloudKit 数据存储库） 下载映像并将其附加到通知的内容之前向用户显示。

为远程通知服务扩展的修改，其有效负载必须标记为可变。 例如：

```csharp
{
    aps : {
        alert : "New Photo Available",
        mutable-content: 1
    },
    my-attachment : "https://example.com/photo.jpg"
}
```

看看以下概述的过程：

[![](advanced-user-notifications-images/extension02.png "添加媒体附件进程")](advanced-user-notifications-images/extension02.png#lightbox)

远程通知传递到设备 （仅通过 APNs)，服务扩展可以下载所需的任何方式通过所需的图像 (如`NSURLSession`) 和收到该映像后，它可以修改的通知和显示的内容它给用户。

以下是可能会在代码中处理此过程的方式的示例：

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;

namespace MonkeyNotification
{
    public class NotificationService : UNNotificationServiceExtension
    {
        #region Constructors
        public NotificationService ()
        {
        }
        #endregion

        #region Override Methods
        public override void DidReceiveNotificationRequest (UNNotificationRequest request, Action<UNNotificationContent> contentHandler)
        {
            // Get file URL
            var attachementPath = request.Content.UserInfo.ObjectForKey (new NSString ("my-attachment"));
            var url = new NSUrl (attachementPath.ToString ());

            // Download the file
            var localURL = new NSUrl ("PathToLocalCopy");

            // Create attachment
            var attachmentID = "image";
            var options = new UNNotificationAttachmentOptions ();
            NSError err;
            var attachment = UNNotificationAttachment.FromIdentifier (attachmentID, localURL, options , out err);

            // Modify contents
            var content = request.Content.MutableCopy() as UNMutableNotificationContent;
            content.Attachments = new UNNotificationAttachment [] { attachment };

            // Display notification
            contentHandler (content);
        }

        public override void TimeWillExpire ()
        {
            // Handle service timing out
        }
        #endregion
    }
}
```

从 APNs 收到通知时，图像的自定义地址从内容中读取和从服务器下载文件。 然后`UNNotificationAttachement`创建具有唯一的 ID 和图像的本地位置 (作为`NSUrl`)。 创建通知内容的可变副本并添加媒体附件。 最后，该通知向用户显示通过调用`contentHandler`。

附件添加到通知后，系统将于移动和管理文件。

除了上面介绍远程通知、 媒体附件也支持从本地通知，其中`UNNotificationAttachement`创建并附加到其内容以及通知。

在 iOS 10 通知支持的映像的媒体附件 (静态和 Gif)，音频或视频和系统将自动显示正确的自定义 UI 为每个这些类型的附件时向用户显示通知。

> [!NOTE]
> 应格外小心以优化媒体大小，它将从远程服务器下载媒体 （或者用于本地通知装配媒体） 作为系统的时间施加严格限制同时运行的应用服务扩展时。 例如，请考虑发送按比例缩小版本的映像或极小的要在通知中显示的视频剪辑。

## <a name="creating-custom-user-interfaces"></a>创建自定义用户界面

若要为其用户通知创建自定义用户界面，开发人员需要将通知内容扩展 （iOS 10 到新） 添加到应用程序的解决方案。

通知内容扩展，开发人员可将自己的视图添加到通知 UI 和绘制出所需的任何内容。 从 iOS 12，通知内容扩展支持交互式 UI 控件，例如按钮和滑块。 有关详细信息，请参阅[iOS 12 中的交互式通知](~/ios/platform/introduction-to-ios12/notifications/interactive.md)文档。

若要支持用户通知与用户交互，自定义操作应创建、 向系统注册并与系统计划之前附加到该通知。 通知内容扩展将调用以处理这些操作的处理。 请参阅[使用的通知操作](~/ios/platform/user-notifications/enhanced-user-notifications.md)一部分[增强型用户通知](~/ios/platform/user-notifications/enhanced-user-notifications.md)有关自定义操作的详细信息的文档。

当向用户提供包含自定义用户界面的用户通知时，它将具有以下元素：

[![](advanced-user-notifications-images/customui01.png "包含自定义用户界面元素的用户通知")](advanced-user-notifications-images/customui01.png#lightbox)

如果用户交互 （下面通知提供） 的自定义操作时，可以更新用户界面，以便为它们调用给定的操作时，发生了什么情况的用户反馈。

### <a name="adding-a-notification-content-extension"></a>添加通知内容扩展

若要在 Xamarin.iOS 应用程序中实现自定义用户通知 UI，执行以下操作：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 打开应用的解决方案在 Visual Studio for mac。
2. 右键单击解决方案名称在**Solution Pad** ，然后选择**添加** > **添加新项目**。
3. 选择**iOS** > **扩展** > **通知内容扩展**然后单击**下一步**按钮： 

    [![](advanced-user-notifications-images/notify01.png "选择通知内容扩展")](advanced-user-notifications-images/notify01.png#lightbox)
4. 输入**名称**作为扩展，然后单击**下一步**按钮： 

    [![](advanced-user-notifications-images/notify02.png "输入扩展的名称")](advanced-user-notifications-images/notify02.png#lightbox)
5. 调整**项目名称**和/或**解决方案名称**如果需要，再单击**创建**按钮： 

    [![](advanced-user-notifications-images/notify03.png "调整项目名称和/或解决方案名称")](advanced-user-notifications-images/notify03.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 打开应用的解决方案在 Visual Studio for mac。
2. 右键单击解决方案名称在**解决方案资源管理器**，然后选择**添加 > 新建项目...** .
3. 选择**可视化C#> iOS 扩展 > 通知内容扩展**:

    [![](advanced-user-notifications-images/notify01.w157-sml.png "选择通知内容扩展")](advanced-user-notifications-images/notify01.w157.png#lightbox)
4. 输入**名称**作为扩展，然后单击**确定**按钮。

-----

通知内容扩展添加到解决方案中，将扩展的项目中创建三个文件：

1. `NotificationViewController.cs` -这是通知内容扩展的主视图控制器。
2. `MainInterface.storyboard` -其中开发人员介绍了可见 UI 对于 iOS 设计器中通知内容扩展。
3. `Info.plist` -控制通知内容扩展的配置。

默认值`NotificationViewController.cs`文件看起来如下所示：

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;
using UserNotificationsUI;


namespace MonkeyChatNotifyExtension
{
    public partial class NotificationViewController : UIViewController, IUNNotificationContentExtension
    {
        #region Constructors
        protected NotificationViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Do any required interface initialization here.
        }
        #endregion

        #region Public Methods
        [Export ("didReceiveNotification:")]
        public void DidReceiveNotification (UNNotification notification)
        {
            label.Text = notification.Request.Content.Body;

            // Grab content
            var content = notification.Request.Content;

        }
        #endregion
    }
}
```

`DidReceiveNotification`通知扩展用户，以便通知内容扩展可以填充自定义 UI 的内容时调用方法`UNNotification`。 对于上述示例中，标签已添加到视图，公开给代码名称`label`，用于显示通知的正文。

### <a name="setting-the-notification-content-extensions-categories"></a>设置通知内容扩展的类别

系统需要了解有关如何查找应用的通知内容扩展基于其响应的特定类别。 请执行以下操作：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 双击该扩展`Info.plist`文件中**Solution Pad**打开进行编辑。
2. 切换到**源**视图。
3. 展开`NSExtension`密钥。
4. 添加`UNNotificationExtensionCategory`作为类型键**字符串**扩展所属的类别的值 (在此示例中活动邀请): 

    [![](advanced-user-notifications-images/customui02.png "添加 UNNotificationExtensionCategory 密钥")](advanced-user-notifications-images/customui02.png#lightbox)
5. 保存更改。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 双击该扩展`Info.plist`文件中**解决方案资源管理器**打开进行编辑。
2. 展开`NSExtension`密钥。
3. 添加`UNNotificationExtensionCategory`作为类型键**字符串**扩展所属的类别的值 (在此示例中活动邀请): 

    [![](advanced-user-notifications-images/customui02w.png "添加 UNNotificationExtensionCategory 密钥")](advanced-user-notifications-images/customui02w.png#lightbox)
4. 保存更改。

-----

通知内容扩展类别 (`UNNotificationExtensionCategory`) 使用相同的类别值用来注册通知操作。 在其中应用将使用相同的 UI，多个类别的情况下，切换`UNNotificationExtensionCategory`为类型**数组**并提供所有所需的类别。 例如:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![](advanced-user-notifications-images/customui03.png "通知内容扩展类别")](advanced-user-notifications-images/customui03.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](advanced-user-notifications-images/customui03w.png "通知内容扩展类别")](advanced-user-notifications-images/customui03w.png#lightbox)

-----

### <a name="hiding-the-default-notification-content"></a>隐藏默认通知内容

在其中自定义通知用户界面将显示相同的内容为默认值 （标题、 副标题和正文底部的通知 UI，将自动显示） 通知的情况下，可以通过添加隐藏此默认信息`UNNotificationExtensionDefaultContentHidden`关键`NSExtensionAttributes`作为类型键**布尔**值为`YES`中的扩展`Info.plist`文件：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![](advanced-user-notifications-images/customui04.png "查找默认的信息")](advanced-user-notifications-images/customui04.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](advanced-user-notifications-images/customui04w.png "查找默认的信息")](advanced-user-notifications-images/customui04w.png#lightbox)

-----

### <a name="designing-the-custom-ui"></a>设计自定义 UI

若要设计通知内容扩展的自定义用户界面，双击`MainInterface.storyboard`文件以打开它以在 iOS 设计器中编辑拖动中构建所需的接口所需的元素 (如`UILabels`和`UIImageViews`)。

> [!NOTE]
> 截至 iOS 12，通知内容扩展可以包括交互式控件，如按钮和文本字段。 有关详细信息，请参阅[iOS 12 中的交互式通知](~/ios/platform/introduction-to-ios12/notifications/interactive.md)文档。

UI 布局并向公开所需的控件后C#代码中，打开`NotificationViewController.cs`以进行编辑和修改`DidReceiveNotification`方法在用户展开通知时填充 UI。 例如:

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;
using UserNotificationsUI;


namespace MonkeyChatNotifyExtension
{
    public partial class NotificationViewController : UIViewController, IUNNotificationContentExtension
    {
        #region Constructors
        protected NotificationViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Do any required interface initialization here.
        }
        #endregion

        #region Public Methods
        [Export ("didReceiveNotification:")]
        public void DidReceiveNotification (UNNotification notification)
        {
            label.Text = notification.Request.Content.Body;

            // Grab content
            var content = notification.Request.Content;

            // Display content in the UILabels
            EventTitle.Text = content.Title;
            EventDate.Text = content.Subtitle;
            EventMessage.Text = content.Body;

            // Get location and display
            var location = content.UserInfo ["location"].ToString ();
            if (location != null) {
                Event.Location.Text = location;
            }

        }
        #endregion
    }
}
```

### <a name="setting-the-content-area-size"></a>设置内容区域大小

若要调整向用户显示的内容区域的大小，请设置下面的代码`PreferredContentSize`中的属性`ViewDidLoad`到所需大小的方法。 此大小也进行调整，通过将约束应用于 iOS 设计器中的视图，它留给开发人员要选取最适合他们的方法。

因为系统已在运行之前通知内容扩展将调用，在内容区域的通知将启动完整大小和下时向用户显示请求的大小进行动画处理。

若要消除这种效果，请编辑`Info.plist`扩展并将设置文件`UNNotificationExtensionInitialContentSizeRatio`键`NSExtensionAttributes`键将键入**数**与一个值，表示所需的比率。 例如：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![](advanced-user-notifications-images/customui05.png "UNNotificationExtensionInitialContentSizeRatio 密钥")](advanced-user-notifications-images/customui05.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](advanced-user-notifications-images/customui05w.png "UNNotificationExtensionInitialContentSizeRatio 密钥")](advanced-user-notifications-images/customui05w.png#lightbox)

-----

### <a name="using-media-attachments-in-custom-ui"></a>在自定义用户界面中使用媒体附件

由于媒体附件 (如中所示[添加媒体附件](#adding-media-attachments)上面部分) 是通知有效负载的一部分，可以访问和显示在通知内容扩展，就像它们会采用默认值通知用户界面。

例如，如果上述自定义用户界面包括`UIImageView`到已公开的C#的代码，以下代码可用于从其填充媒体附件：

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;
using UserNotificationsUI;

namespace MonkeyChatNotifyExtension
{
    public partial class NotificationViewController : UIViewController, IUNNotificationContentExtension
    {
        #region Constructors
        protected NotificationViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Do any required interface initialization here.
        }
        #endregion

        #region Public Methods
        [Export ("didReceiveNotification:")]
        public void DidReceiveNotification (UNNotification notification)
        {
            label.Text = notification.Request.Content.Body;

            // Grab content
            var content = notification.Request.Content;

            // Display content in the UILabels
            EventTitle.Text = content.Title;
            EventDate.Text = content.Subtitle;
            EventMessage.Text = content.Body;

            // Get location and display
            var location = content.UserInfo ["location"].ToString ();
            if (location != null) {
                Event.Location.Text = location;
            }

            // Get Media Attachment
            if (content.Attachements.Length > 1) {
                var attachment = content.Attachments [0];
                if (attachment.Url.StartAccessingSecurityScopedResource ()) {
                    EventImage.Image = UIImage.FromFile (attachment.Url.Path);
                    attachment.Url.StopAccessingSecurityScopedResource ();
                }
            }
        }
        #endregion
    }
}
```

由系统管理媒体附件，因为它不在应用程序的沙盒。 扩展插件需要通知系统它通过调用需要文件访问权`StartAccessingSecurityScopedResource`方法。 如果扩展用该文件完成的它需要调用`StopAccessingSecurityScopedResource`释放它的连接。

### <a name="adding-custom-actions-to-a-custom-ui"></a>将自定义操作添加到自定义 UI

自定义操作按钮可用来向自定义通知 UI 中添加交互性。 请参阅[使用的通知操作](~/ios/platform/user-notifications/enhanced-user-notifications.md)一部分[增强型用户通知](~/ios/platform/user-notifications/enhanced-user-notifications.md)有关自定义操作的详细信息的文档。

除了自定义操作，以下内置操作也可以响应通知内容扩展：

- **默认操作**-这是当用户点击通知以打开应用并显示给定通知的详细信息。
- **关闭操作**-当用户关闭给定的通知时，此操作发送到应用。

通知内容扩展还具有更新它们的 UI，当用户调用了一个自定义操作时的功能，例如显示为已接受时的日期在用户点击**接受**自定义操作按钮。 此外，通知内容扩展可以告知系统延迟的通知 UI 上诉以便关闭通知之前，用户可以看到其操作的效果。

这是通过实现的第二个版本`DidReceiveNotification`包括完成处理程序的方法。 例如：

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;
using UserNotificationsUI;
using CoreGraphics;

namespace myApp {
    public class NotificationViewController : UIViewController, UNNotificationContentExtension {

        public override void ViewDidLoad() {
            base.ViewDidLoad();

            // Adjust the size of the content area
            var size = View.Bounds.Size
            PreferredContentSize = new CGSize(size.Width, size.Width/2);
        }

        public void DidReceiveNotification(UNNotification notification) {

            // Grab content
            var content = notification.Request.Content;

            // Display content in the UILabels
            EventTitle.Text = content.Title;
            EventDate.Text = content.Subtitle;
            EventMessage.Text = content.Body;

            // Get location and display
            var location = Content.UserInfo["location"] as string;
            if (location != null) {
                Event.Location.Text = location;
            }

            // Get Media Attachment
            if (content.Attachements.Length > 1) {
                var attachment = content.Attachments[0];
                if (attachment.Url.StartAccessingSecurityScopedResource()) {
                    EventImage.Image = UIImage.FromFile(attachment.Url.Path);
                    attachment.Url.StopAccessingSecurityScopedResource();
                }
            }
        }

        [Export ("didReceiveNotificationResponse:completionHandler:")]
        public void DidReceiveNotification (UNNotificationResponse response, Action<UNNotificationContentExtensionResponseOption> completionHandler)
        {

            // Update UI when the user interacts with the
            // Notification
            Server.PostEventResponse += (response) {
                // Take action based on the response
                switch(response.ActionIdentifier){
                case "accept":
                    EventResponse.Text = "Going!";
                    EventResponse.TextColor = UIColor.Green;
                    break;
                case "decline":
                    EventResponse.Text = "Not Going.";
                    EventResponse.TextColor = UIColor.Red;
                    break;
                }

                // Close Notification
                completionHandler (UNNotificationContentExtensionResponseOption.Dismiss);
            };
        }
    }
}
```

通过添加`Server.PostEventResponse`处理程序`DidReceiveNotification`通知内容扩展，扩展方法*必须*处理所有自定义操作。 扩展还可以通过更改转发到包含应用的自定义操作`UNNotificationContentExtensionResponseOption`。 例如：

```csharp
// Close Notification
completionHandler (UNNotificationContentExtensionResponseOption.DismissAndForwardAction);
```

### <a name="working-with-the-text-input-action-in-custom-ui"></a>使用自定义 UI 中的文本输入操作

根据应用程序和通知的设计，可能要求用户将文本输入 （例如回复输入一条消息） 通知的时间。 通知内容扩展具有内置的文本输入操作的访问权限，就像标准通知一样。

例如:

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;
using UserNotificationsUI;


namespace MonkeyChatNotifyExtension
{
    public partial class NotificationViewController : UIViewController, IUNNotificationContentExtension
    {
        #region Computed Properties
        // Allow to take input
        public override bool CanBecomeFirstResponder {
            get { return true; }
        }

        // Return the custom created text input view with the
        // required buttons and return here
        public override UIView InputAccessoryView {
            get { return InputView; }
        }
        #endregion

        #region Constructors
        protected NotificationViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Do any required interface initialization here.
        }
        #endregion

        #region Private Methods
        private UNNotificationCategory MakeExtensionCategory ()
        {

            // Create Accept Action
            ...

            // Create decline Action
            ...

            // Create Text Input Action
            var commentID = "comment";
            var commentTitle = "Comment";
            var textInputButtonTitle = "Send";
            var textInputPlaceholder = "Enter comment here...";
            var commentAction = UNTextInputNotificationAction.FromIdentifier (commentID, commentTitle, UNNotificationActionOptions.None, textInputButtonTitle, textInputPlaceholder);

            // Create category
            var categoryID = "event-invite";
            var actions = new UNNotificationAction [] { acceptAction, declineAction, commentAction };
            var intentIDs = new string [] { };
            var category = UNNotificationCategory.FromIdentifier (categoryID, actions, intentIDs, UNNotificationCategoryOptions.None);

            // Return new category
            return category;

        }
        #endregion

        #region Public Methods
        [Export ("didReceiveNotification:")]
        public void DidReceiveNotification (UNNotification notification)
        {
            label.Text = notification.Request.Content.Body;

            // Grab content
            var content = notification.Request.Content;

            // Display content in the UILabels
            EventTitle.Text = content.Title;
            EventDate.Text = content.Subtitle;
            EventMessage.Text = content.Body;

            // Get location and display
            var location = content.UserInfo ["location"].ToString ();
            if (location != null) {
                Event.Location.Text = location;
            }

            // Get Media Attachment
            if (content.Attachements.Length > 1) {
                var attachment = content.Attachments [0];
                if (attachment.Url.StartAccessingSecurityScopedResource ()) {
                    EventImage.Image = UIImage.FromFile (attachment.Url.Path);
                    attachment.Url.StopAccessingSecurityScopedResource ();
                }
            }
        }

        [Export ("didReceiveNotificationResponse:completionHandler:")]
        public void DidReceiveNotification (UNNotificationResponse response, Action<UNNotificationContentExtensionResponseOption> completionHandler)
        {

            // Is text input?
            if (response is UNTextInputNotificationResponse) {
                var textResponse = response as UNTextInputNotificationResponse;
                Server.Send (textResponse.UserText, () => {
                    // Close Notification
                    completionHandler (UNNotificationContentExtensionResponseOption.Dismiss);
                });
            }

            // Update UI when the user interacts with the
            // Notification
            Server.PostEventResponse += (response) {
                // Take action based on the response
                switch (response.ActionIdentifier) {
                case "accept":
                    EventResponse.Text = "Going!";
                    EventResponse.TextColor = UIColor.Green;
                    break;
                case "decline":
                    EventResponse.Text = "Not Going.";
                    EventResponse.TextColor = UIColor.Red;
                    break;
                }

                // Close Notification
                completionHandler (UNNotificationContentExtensionResponseOption.Dismiss);
            };
        }
        #endregion
    }
}
```

此代码创建一个新的文本输入的操作并将其添加到扩展的类别 (在`MakeExtensionCategory`) 方法。 在`DidReceive`重写方法，它将处理用户输入文本，使用以下代码：

```csharp
// Is text input?
if (response is UNTextInputNotificationResponse) {
    var textResponse = response as UNTextInputNotificationResponse;
    Server.Send (textResponse.UserText, () => {
        // Close Notification
        completionHandler (UNNotificationContentExtensionResponseOption.Dismiss);
    });
}
```

如果设计调用文本输入字段中添加自定义按钮，添加以下代码以将它们包括：

```csharp
// Allow to take input
public override bool CanBecomeFirstResponder {
    get {return true;}
}

// Return the custom created text input view with the
// required buttons and return here
public override UIView InputAccessoryView {
    get {return InputView;}
}
```

由用户触发评论操作时，视图控制器和自定义文本输入的字段需要激活：

```csharp
// Update UI when the user interacts with the
// Notification
Server.PostEventResponse += (response) {
    // Take action based on the response
    switch(response.ActionIdentifier){
    ...
    case "comment":
        BecomeFirstResponder();
        TextField.BecomeFirstResponder();
        break;
    }

    // Close Notification
    completionHandler (UNNotificationContentExtensionResponseOption.Dismiss);

};
```

## <a name="summary"></a>总结

本文已在 Xamarin.iOS 应用程序中使用新的用户通知框架的高级的探讨。 其中包括如何添加到本地和远程通知的媒体附件，其中包括如何使用新的用户通知 UI 创建自定义通知 Ui。

## <a name="related-links"></a>相关链接

- [iOS 10 示例](https://developer.xamarin.com/samples/ios/iOS10/)
- [UserNotifications 框架引用](https://developer.apple.com/reference/usernotifications)
- [UserNotificationsUI](https://developer.apple.com/reference/usernotificationsui)
- [本地和远程通知编程指南](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/Introduction.html)
