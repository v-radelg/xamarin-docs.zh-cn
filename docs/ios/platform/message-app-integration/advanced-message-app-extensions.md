---
title: 在 Xamarin.iOS 中的高级的消息应用扩展
description: 本文介绍用于使用与消息应用扩展的 Xamarin.iOS 解决方案可与该消息应用程序集成，并向用户提供新功能的高级的技术。
ms.prod: xamarin
ms.assetid: 394A1FDA-AF70-4493-9B2C-4CFE4BE791B6
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: baceb59116dd907918b34eca4f44293051190954
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61155506"
---
# <a name="advanced-message-app-extensions-in-xamarinios"></a>在 Xamarin.iOS 中的高级的消息应用扩展

_本文介绍用于使用与消息应用扩展的 Xamarin.iOS 解决方案可与该消息应用程序集成，并向用户提供新功能的高级的技术。_


新建与消息应用扩展集成到 iOS 10**消息**应用并提供新功能提供给用户。 该扩展可以发送文本、 贴纸、 媒体文件和交互式消息。

## <a name="about-message-app-extensions"></a>有关消息应用扩展

如上面所述，消息应用扩展可与集成**消息**应用并提供新功能提供给用户。 该扩展可以发送文本、 贴纸、 媒体文件和交互式消息。 提供了两种类型的消息应用扩展：

- **不干胶标签包**-包含一系列可以将用户添加到一条消息的贴纸。 不干胶标签包可以创建无需编写任何代码。
- **iMessage 应用**-可以显示用于选择不干胶标签、 输入文本，包括 （具有可选类型转换） 的媒体文件和创建、 编辑和发送交互消息在 Messages 应用内自定义用户界面。

消息应用扩展提供了三种主要的内容类型：

- **交互式消息**-是一种类型的应用程序生成的自定义消息内容，当在用户点击该消息，该应用程序将启动在前台。
- **贴纸**-是由可以包含在用户之间发送的消息的应用程序生成的图像。 请参阅我们[Ice cream 生成器](https://developer.xamarin.com/samples/monotouch/ios10/IceCreamBuilder/)不干胶标签包应用的示例实现的示例应用程序。
- **其他支持内容**-应用程序可提供内容，如照片、 视频、 文本或始终支持通过邮件应用类型的链接。

新 ios 10、 消息应用程序现在包括其自己专用的内置应用商店。 包含消息应用扩展的任何应用将显示，并且可以提升此存储区中。 新的消息应用抽屉将显示已从要向用户提供快速访问的消息应用程序存储区中下载任何应用。

此外新在 iOS 10 中，Apple 已添加内联应用归属这样用户就可以轻松发现应用程序。 例如，如果一个用户从第二个用户不具有的应用将内容发送到另一个安装 （如示例的贴纸），发送应用的名称被下列出的消息历史记录中的内容。 如果在用户点击应用的名称，我们打开消息应用商店和存储区中选择的应用程序。

消息应用扩展是类似于现有 iOS 应用开发人员所熟悉创建，且必须对所有标准框架和标准的 iOS 应用的功能的访问。 例如：

- 他们有权访问应用内购买。
- 他们有权访问 Apple Pay。
- 他们有权访问设备的硬件，例如照相机。

IOS 10 仅支持消息应用扩展，但是，这些扩展将发送的内容是在 watchOS 和 macOS 设备上可查看。 新_最近使用的项目页_添加到 watchOS 3，将显示通过手机，包括那些从消息应用扩展，已发送的最新不干胶标签，并允许用户从手表发送这些贴纸。

## <a name="about-interactive-messages"></a>有关交互式消息

交互式消息提供自定义消息气泡，并提供的消息应用扩展。 它们允许用户创建交互式消息内容，将其插入消息输入字段中，并将其发送。

[![](advanced-message-app-extensions-images/interactive01.png "创建交互式消息内容")](advanced-message-app-extensions-images/interactive01.png#lightbox)

接收的用户可以回复而使用交互式消息通过点击以加载创建消息应用扩展的消息历史记录中其消息气泡。 该扩展将会启动的全屏幕，并允许用户撰写回复并将其发送回原始用户。

[![](advanced-message-app-extensions-images/interactive02.png "扩展启动全屏幕")](advanced-message-app-extensions-images/interactive02.png#lightbox)


将下面将详细介绍以下主题：

- 消息 API 概述
- 扩展生命周期
- 编写一条消息
- 发送消息

## <a name="messages-api-overview"></a>消息 API 概述

在用户调用，消息应用扩展将显示在底部的精简视图模式中的消息历史记录：

[![](advanced-message-app-extensions-images/interactive03.png "消息 API 概述")](advanced-message-app-extensions-images/interactive03.png#lightbox)

1. `MSMessageAppViewController`消息应用扩展中的对象是向用户显示的扩展视图时调用的主类。
2. 会话提供给用户作为`MSConversation`对象实例。
3. `MSMessage`类表示会话中给定的消息气泡。
4. `MSSession` 控制如何发送一条消息。
5. `MSMessageTemplateLayout` 控制消息的显示方式

## <a name="the-extension-lifecycle"></a>扩展生命周期

看看变为活动状态消息应用扩展的过程：

[![](advanced-message-app-extensions-images/interactive04.png "变为活动状态消息应用扩展的过程")](advanced-message-app-extensions-images/interactive04.png#lightbox)

1. 扩展在启动时 （例如从应用抽屉中），消息应用程序将启动一个进程。
2. `DidBecomeActive`方法调用并传递`MSConversation`，它表示消息应用扩展运行中的会话。
3. 因为基于扩展`UIViewController`两者`ViewWillAppear`和`ViewDidAppear`调用。

接下来，看看成为停用消息应用扩展的过程：

[![](advanced-message-app-extensions-images/interactive05.png "成为停用消息应用扩展的过程")](advanced-message-app-extensions-images/interactive05.png#lightbox)

1. 消息应用扩展正在停用后，`ViewWillDisappear`将首先调用方法。
2. 然后`ViewDidDisappear`将调用方法。
3. `WillResignActive`方法调用并传递`MSConversation`，它表示消息应用扩展运行中的会话。 此时，邮件应用和扩展之间的连接已释放。
4. 在以后某个时刻，进程将终止通过消息应用程序。

扩展是一个短生存期的进程，因为它是主动的方式终止系统来节省处理和电池电量。 开发人员应记住这一点设计和实现消息应用扩展时。

## <a name="composing-a-message"></a>编写一条消息

一旦消息应用扩展的进程中运行，并显示其用户界面，下面的代码可用于撰写新消息：

```csharp
MSMessage ComposeMessage (IceCream iceCream, string caption, MSSession session = null)
{
    var components = new NSUrlComponents {
        QueryItems = iceCream.QueryItems
    };

    var layout = new MSMessageTemplateLayout {
        Image = iceCream.RenderSticker (true),
        Caption = caption
    };

    var message = new MSMessage (session ?? new MSSession()) {
        Url = components.Url,
        Layout = layout
    };

    return message;
}
```

此代码将创建一个新`MSMessage`，并设置几个属性 (如`Url`)。 尽管仅在 ios 设备上创建消息，它可以发送到 iOS 和 macOS 显示。

如果用户单击消息气泡图在 macOS 上的会话中，Mac 将尝试打开 web 浏览器中的 URL 中指定的地址。 因此，开发人员网站应该能够显示在 macOS 上的 web 浏览器中的消息的某些表示基于计算机。

`AccessibilityLabel`屏幕阅读器使用属性来读取用户的会话的脚本。 `Layout`属性指定如何将显示消息，当前仅`MSMessageTemplateLayout`支持，如以下所示：

[![](advanced-message-app-extensions-images/interactive06.png "MSMessageTemplateLayout 模板")](advanced-message-app-extensions-images/interactive06.png#lightbox)

`Image`属性的`MSMessageTemplateLayout`MessageBubble 屏幕上的主要部分的提供内容。 `MediaFileUrl`属性也提供消息气泡的正文内容，但允许的内容的不受`UIImage`（例如将在后台循环播放视频文件）。 如果这两个`Image`并`MediaFileUrl`提供了属性，`Image`属性将优先。 `MediaFileUrl`支持 PNG、 JPEG、 GIF 和格式的视频 （任何可以播放的媒体播放器框架） 的媒体格式。

建议大小为 3 x 分辨率 300 x 300 像素。 也接受略有更大、 较小的资产并测试了几个不同的大小以获得最佳结果，Apple 提供建议。 消息应用程序将向下采样和缩放要求此媒体。

当资产发送到接收方时，附加任何媒体会自动转码消息应用程序以对其通过网络传输从优化操作。 正因为如此，Apple 会阻止包括的媒体的媒体将按比例缩小和压缩以便传输，因为附加到邮件中的文本这样可能会呈现文本无法看清。

`ImageTitle`和`ImageSubtitle`属性提供的媒体，消息气泡图中显示的说明。 这些属性将发送以文本形式接收设备到其中它们将清晰地呈现在图像的左下角。

`Caption`， `SubCaption`，`TrailingCaption`和`TrailingSubcaption`属性进一步描述映像，并将呈现在图像下的部分。 设置所有这些属性设置为`null`将创建没有标题区域消息气泡：

[![](advanced-message-app-extensions-images/interactive07.png "而无需标题区域消息气泡图")](advanced-message-app-extensions-images/interactive07.png#lightbox)

最后要注意的是邮件应用将在消息气泡的左上角绘制消息应用扩展的图标。

## <a name="sending-a-message"></a>发送消息

一次`MSMessage`已组合，可以使用以下代码以将其发送：

```csharp
public void SendMessage (MSMessage message)
{
    // Insert the message into the conversation
    ActiveConversation.InsertMessage (message, (error) => {
        // Did the message send successfully?
        if (error == null) {
            // Handle successful send
        } else {
            // Report Error
            Console.WriteLine ("Error: {0}", error);
        }
    };

}
```

`ActiveConversation`属性的`MSMessagesAppViewController`将保存当前会话中启动了消息应用扩展。

调用`InsertMessage`的`MSConversation`要包含在会话中的消息处理可能会出现任何错误。 如果成功地包括该消息，则将在输入字段中显示消息气泡。

此外，扩展可以将不同类型的数据发送到如会话：

- **Text** - `ActiveConversation.InsertText ("Message", (error) => {...});`
- **附件** - `ActiveConversation.InsertAttachment (new NSUrl ("path"), "filename", (error) => {...});`
- **贴纸** -  `ActiveConversation.InsertSticker (sticker, (obj) => {...});`其中`sticker`是`MSSticker`。

后的新内容的输入字段中，用户便可将消息发送通过点击蓝色**发送**按钮 （就像任何典型的消息）。 没有自动将内容发送该消息应用扩展的方法，此过程是完全受控制的用户。

## <a name="handling-the-compact-and-expanded-modes"></a>处理的紧凑和扩展模式

消息应用扩展可以在两种不同的视图模式之一显示：

[![](advanced-message-app-extensions-images/interactive08.png "在两个不同的视图模式下显示一个消息应用扩展：Compact 和扩展")](advanced-message-app-extensions-images/interactive08.png#lightbox)

- **Compact** -这是默认模式消息应用扩展，将消息视图中的底部 25%占用。 以简洁模式，应用不具有访问键盘、 水平滚动或轻扫手势识别器。 应用程序有权访问输入字段以及对调用`InsertMessage`将立即显示给用户存在。
- **展开**-消息应用扩展会填充整个消息视图。 它不能访问到输入字段，但确实有权访问键盘、 水平滚动和轻扫手势识别器。

消息应用扩展可以切换这些模式之间手动或以编程方式由用户在任何时候，应该立即响应任何更改在视图模式。

看看下面的示例处理两种不同的视图模式之间切换。 两个不同的视图控制器将需要为每个状态。 `StickerBrowserViewController`句柄**Compact**视图和`AddStickerViewController`将处理**扩展**视图：

```csharp
using System;

using Messages;
using Foundation;
using UIKit;

namespace MessagesExtension {
    public partial class MessagesViewController : MSMessagesAppViewController, IIceCreamsViewControllerDelegate, IBuildIceCreamViewControllerDelegate {
        public MessagesViewController (IntPtr handle) : base (handle)
        {
        }

        public override void WillBecomeActive (MSConversation conversation)
        {
            base.WillBecomeActive (conversation);

            // Present the view controller appropriate for the conversation and presentation style.
            PresentViewController (conversation, PresentationStyle);
        }

        public override void WillTransition (MSMessagesAppPresentationStyle presentationStyle)
        {
            var conversation = ActiveConversation;
            if (conversation == null)
                throw new Exception ("Expected an active converstation");

            // Present the view controller appropriate for the conversation and presentation style.
            PresentViewController (conversation, presentationStyle);
        }

        void PresentViewController (MSConversation conversation, MSMessagesAppPresentationStyle presentationStyle)
        {
            // Determine the controller to present.
            UIViewController controller;

            if (presentationStyle == MSMessagesAppPresentationStyle.Compact) {
                // Show a list of previously created ice creams.
                controller = InstantiateIceCreamsController ();
            } else {
                var iceCream = new IceCream (conversation.SelectedMessage);
                controller = iceCream.IsComplete ? InstantiateCompletedIceCreamController (iceCream) : InstantiateBuildIceCreamController (iceCream);
            }

            foreach (var child in ChildViewControllers) {
                child.WillMoveToParentViewController (null);
                child.View.RemoveFromSuperview ();
                child.RemoveFromParentViewController ();
            }

            AddChildViewController (controller);
            controller.View.Frame = View.Bounds;
            controller.View.TranslatesAutoresizingMaskIntoConstraints = false;
            View.AddSubview (controller.View);

            controller.View.LeftAnchor.ConstraintEqualTo (View.LeftAnchor).Active = true;
            controller.View.RightAnchor.ConstraintEqualTo (View.RightAnchor).Active = true;
            controller.View.TopAnchor.ConstraintEqualTo (View.TopAnchor).Active = true;
            controller.View.BottomAnchor.ConstraintEqualTo (View.BottomAnchor).Active = true;

            controller.DidMoveToParentViewController (this);
        }

        UIViewController InstantiateIceCreamsController ()
        {
            // Instantiate a `IceCreamsViewController` and present it.
            var controller = Storyboard.InstantiateViewController (IceCreamsViewController.StoryboardIdentifier) as IceCreamsViewController;
            if (controller == null)
                throw new Exception ("Unable to instantiate an IceCreamsViewController from the storyboard");

            controller.Builder = this;
            return controller;
        }

        UIViewController InstantiateBuildIceCreamController (IceCream iceCream)
        {
            // Instantiate a `BuildIceCreamViewController` and present it.
            var controller = Storyboard.InstantiateViewController (BuildIceCreamViewController.StoryboardIdentifier) as BuildIceCreamViewController;
            if (controller == null)
                throw new Exception ("Unable to instantiate an BuildIceCreamViewController from the storyboard");

            controller.IceCream = iceCream;
            controller.Builder = this;

            return controller;
        }

        public UIViewController InstantiateCompletedIceCreamController (IceCream iceCream)
        {
            // Instantiate a `BuildIceCreamViewController` and present it.
            var controller = Storyboard.InstantiateViewController (CompletedIceCreamViewController.StoryboardIdentifier) as CompletedIceCreamViewController;
            if (controller == null)
                throw new Exception ("Unable to instantiate an CompletedIceCreamViewController from the storyboard");

            controller.IceCream = iceCream;
            return controller;
        }

        public void DidSelectAdd (IceCreamsViewController controller)
        {
            Request (MSMessagesAppPresentationStyle.Expanded);
        }

        public void Build (BuildIceCreamViewController controller, IceCreamPart iceCreamPart)
        {
            var conversation = ActiveConversation;
            if (conversation == null)
                throw new Exception ("Expected a conversation");

            var iceCream = controller.IceCream;
            if (iceCream == null)
                throw new Exception ("Expected the controller to be displaying an ice cream");

            string messageCaption = string.Empty;
            var b = iceCreamPart as Base;
            var s = iceCreamPart as Scoops;
            var t = iceCreamPart as Topping;

            if (b != null) {
                iceCream.Base = b;
                messageCaption = "Let's build an ice cream";
            } else if (s != null) {
                iceCream.Scoops = s;
                messageCaption = "I added some scoops";
            } else if (t != null) {
                iceCream.Topping = t;
                messageCaption = "Our finished ice cream";
            } else {
                throw new Exception ("Unexpected type of ice cream part selected.");
            }

            // Create a new message with the same session as any currently selected message.
            var message = ComposeMessage (iceCream, messageCaption, conversation.SelectedMessage?.Session);

            // Add the message to the conversation.
            conversation.InsertMessage (message, null);

            // If the ice cream is complete, save it in the history.
            if (iceCream.IsComplete) {
                var history = IceCreamHistory.Load ();
                history.Append (iceCream);
                history.Save ();
            }

            Dismiss ();
        }

        MSMessage ComposeMessage (IceCream iceCream, string caption, MSSession session = null)
        {
            var components = new NSUrlComponents {
                QueryItems = iceCream.QueryItems
            };

            var layout = new MSMessageTemplateLayout {
                Image = iceCream.RenderSticker (true),
                Caption = caption
            };

            var message = new MSMessage (session ?? new MSSession()) {
                Url = components.Url,
                Layout = layout
            };

            return message;
        }
    }
}
```

`DidTransition`重写方法以处理两种模式之间切换：

```csharp
public override void DidTransition (MSMessagesAppPresentationStyle presentationStyle)
{
    base.DidTransition (presentationStyle);

    // Take action based on style
    switch (presentationStyle) {
    case MSMessagesAppPresentationStyle.Compact:
        PresentStickerBrowser ();
        break;
    case MSMessagesAppPresentationStyle.Expanded:
        PresentAddSticker ();
        break;
    }
}
```

（可选） 可以使用应用`WillTransition`方法以处理视图模式更改之前 （就像在上面的 Icecream 生成器示例） 向用户显示。 有关详细信息，请参阅我们[进一步不干胶标签自定义](~/ios/platform/message-app-integration/intro-to-message-app-extensions.md)文档。

## <a name="replying-to-a-message"></a>答复消息

有两种情况下，将需要处理答复消息时消息应用扩展：

[![](advanced-message-app-extensions-images/interactive09.png "非活动和主动模式中的消息应用扩展")](advanced-message-app-extensions-images/interactive09.png#lightbox)

- **扩展插件为非活动**-还有一个脚本中的用户可以点击以激活扩展并继续此交互式会话的消息文字消息应用扩展消息气泡。
- **扩展处于活动状态**-用户可以点击该消息应用扩展消息气泡图中的消息文字输入已展开视图模式并继续从上次退出的位置的交互式过程。

### <a name="the-extension-is-inactive"></a>扩展的非活动状态

当消息气泡点击消息脚本中的用户以及消息应用扩展处于非活动状态时将发生以下过程：

[![](advanced-message-app-extensions-images/interactive10.png "处理非活动状态的消息气泡图")](advanced-message-app-extensions-images/interactive10.png#lightbox)

1. 在用户点击的扩展消息气泡。
2. 启动扩展时，消息应用程序将启动一个进程。
3. `DidBecomeActive`方法调用并传递`MSConversation`，它表示消息应用扩展运行中的会话。
4. 因为基于扩展`UIViewController`两者`ViewWillAppear`和`ViewDidAppear`调用。

该过程完成后，将在扩展视图模式中显示消息应用扩展。

### <a name="the-extension-is-active"></a>扩展处于活动状态

当消息气泡点击消息脚本中的用户以及消息应用扩展处于活动状态时将发生以下过程：

[![](advanced-message-app-extensions-images/interactive11.png "处理活动的消息气泡图")](advanced-message-app-extensions-images/interactive11.png#lightbox)

1. 在用户点击的扩展消息气泡。
2. 消息应用扩展已处于活动状态，因为`WillTransition`方法的`MSMessagesAppViewController`调用以处理从 Compact 切换到已展开视图模式。
3. `DidSelectMessage`方法`MSMessagesAppViewController`调用并传递`MSMessage`和`MSConversation`消息气泡所属。
4. `DidTransition`方法的`MSMessagesAppViewController`调用以处理从 Compact 切换到已展开视图模式。

同样，该过程完成后，会扩展视图模式中看到消息应用扩展。

### <a name="accessing-the-selected-message"></a>访问所选的邮件

在任一情况下，当用户点击消息气泡属于消息应用扩展，它将需要获取访问权限`MSMessage`的点击的使用`SelectedMessage`属性的`MSConversation`。

例如：

```csharp
using System;
using Foundation;
using Messages;
using UIKit;


namespace MessageExtension
{
    public partial class MessagesViewController : MSMessagesAppViewController
    {
        ...

        #region Override Methods
        public override void DidSelectMessage (MSMessage message, MSConversation conversation)
        {
            base.DidSelectMessage (message, conversation);

            // Get selected message
            var selected = conversation.SelectedMessage;

            // Present the user interface to continue editing
            // the conversation
            ...
        }
        #endregion
    }
}
```

应在消息应用扩展的用户界面中显示所选的消息，并且应允许用户编写响应。

## <a name="removing-partially-completed-messages"></a>删除部分完成消息

发送会话中的两个用户之间的不同步骤的交互式会话，过程中部分已完成的消息气泡可以开始变得混乱消息脚本：

[![](advanced-message-app-extensions-images/interactive12.png "部分完成的消息气泡可导致消息脚本")](advanced-message-app-extensions-images/interactive12.png#lightbox)

相反，消息应用扩展应折叠到脚本中的消息文字的简洁注释以前的消息气泡：

[![](advanced-message-app-extensions-images/interactive13.png "折叠消息脚本中的上一个消息气泡")](advanced-message-app-extensions-images/interactive13.png#lightbox)

这处理使用`MSSession`折叠所有现有的步骤。 因此`DidSelectMessage`方法的`MSMessagesAppViewController`类无法修改为如下所示：

```csharp
public override void DidSelectMessage (MSMessage message, MSConversation conversation)
{
    base.DidSelectMessage (message, conversation);

    MSSession session;
    var summary = "";

    // Get selected message
    var selected = conversation.SelectedMessage;

    // Does the selected message already have a session?
    if (selected.Session == null) {
        // No, create a new session
        session = new MSSession ();
        summary = "Let's build an ice cream";
    } else {
        // Yes, use the existing session
        session = selected.Session;
        summary = "I added some scoops";
    }

    // Create an instance of the message being composed
    var existingMessage = new MSMessage (session);
    message.SummaryText = summary;
    ...

    // Present the user interface to continue editing
    // the conversation
    ...
}
```

如果所选的消息已具有退出`MSSession`，它其他使用新`MSSession`创建。 `SummaryText`属性的`MSMessage`用于将标题添加到折叠前面的步骤。 如果`SummaryText`属性设置为`null`，将从会话脚本完全删除会话中的上一步骤。

## <a name="advanced-message-api-features"></a>高级消息 API 功能

使用更高版本的详细介绍新的消息 API 的基本功能，接下来检查一些更高级的功能，Apple 已内置到框架。

首先，有几个其他重写方法中的`MSMessagesAppViewController`提供更深入地访问会话的类：

- `DidStartSendingMessage` -这在用户点击发送按钮时调用。 这并不意味着，消息确实已传递给收件人，只需发送进程已启动。
- `DidCancelSendingMessage` 的发生这种情况是当用户点击*X*会话脚本中的消息气泡图右上角的按钮。
- `DidReceiveMessage` 的消息应用扩展处于活动状态时调用此方法从一个会话中参与者收到新消息。

### <a name="group-conversations"></a>组对话

虽然组对话 （有 3 个或更多的人） 所涉及的用户，这将需要加以考虑，设计和实现消息应用扩展时，可以使用消息应用扩展。

看看以下交互与三个用户的组对话中：

[![](advanced-message-app-extensions-images/interactive14.png "与三个用户的组对话中的交互")](advanced-message-app-extensions-images/interactive14.png#lightbox)

1. 用户 1 会发送一组交互式消息询问用户 2 和用户 3 中进行选择点子浇头。
2. 用户 2 选择 tomatoes。
3. 用户 3 选择像。
4. 选择用户 2 和用户 3 到达回用户 1 在几乎同一时间。 因此，用户 2 选择折叠成一个摘要行和不可用。 这种情况下可能有也已翻转、 与用户 2 的选择显示和用户 3 的被折叠。

在任一情况下，此行为是不需要用户 1 应能够访问用户 2 和 3 用户的选择。 若要处理这种情况下，Apple 建议消息应用扩展在云中存储的消息状态和使用的 URL 属性`MSMessage`（，获取在用户之间发送的） 若要访问此状态。

当用户发送一条消息时，会话令牌生成，并推送到云与当前消息状态。 当用户点击上会话脚本中有消息气泡时，会话令牌用于从云中检索当前会话状态。

### <a name="sender-identifiers"></a>发件人标识符

若要讨论访问消息的发件人标识符，执行组对话上面给出的示例：

[![](advanced-message-app-extensions-images/interactive15.png "发送标识符的组对话")](advanced-message-app-extensions-images/interactive15.png#lightbox)

1. 同样，用户 1 会发送一组交互式消息询问用户 2 和用户 3 中进行选择点子浇头。
2. 用户 3 选取像。
3. 用户 3 的选择能够到达回用户 1，并且用户 2 不尚未回复。
4. 由于 Apple 是非常关心用户隐私，消息应用扩展只了解唯一标识符 (作为`NSUUID`) 分配会话中的每个参与者。 在本地设备上仅当前用户的标识符是已知的。
5. `MSMessage`具有`SenderIdentifier`属性之一相匹配的用户的参与者列表中已知的扩展。
6. 每个用户设备都有其自己副本，其中同样，只有本地用户的标识符已知的参与者列表。
7. 发送一条消息，其`SenderIdentifier`属性也称为为本地用户。

可以按以下方式使用发件人标识符：

- 通过查看参与者列表扩展可以获取会话中的用户数。
- 当在扩展插件接收来自用户的一条消息时，它可以跟踪的发送方标识符。 如果收到具有相同的发件人标识符的另一条消息，该扩展知道它是来自同一用户。
- 它们可以用于帮助识别特定用户会话中。

可在任何文本字段的发件人标识符`MSMessageTemplateLayout`前缀美元符号 (`$`)。 例如：

```csharp
// Pass along the sender identifier
var layout = new MSMessageTemplateLayout()
layout.Caption = string.format("${0} wants pickles.",Conversation.LocalParticipantIdentifier.UuidString);
```

当邮件应用显示与此类型的格式设置消息气泡时，它将替换`$uuid...`与发送消息的会话中的参与者的联系人名称。

发件人标识符是每个设备上唯一的因此查看另请注意上面的关系图，该用户 1 的设备和用户 3 设备的会话中的每个参与者都有一个不同的唯一发件人标识符。

发件人标识符的作用域的消息应用扩展安装。 因此如果用户卸载并重新安装消息应用扩展新的发送方标识符将生成新的安装。

若要访问发件人标识符，该扩展可以使用以下代码：

```csharp
public override void DidStartSendingMessage (MSMessage message, MSConversation conversation)
{
    base.DidStartSendingMessage (message, conversation);

    // Get the sender's participant ID
    var senderID = message.SenderParticipantIdentifier;

    // Get the local participant ID
    var localID = conversation.LocalParticipantIdentifier;

    // Get the remote participant IDs
    var remoteIDs = conversation.RemoteParticipantIdentifiers;
}
```

## <a name="supported-platforms"></a>支持的平台

将下列 Apple 平台上传送消息应用扩展生成的交互式消息：

- watchOS 3
- macOS Sierra
- iOS 10

三个平台，只有 iOS 10 将允许用户生成交互式消息。 在 macOS Sierra，如果用户单击交互式消息气泡图 URL 附加到`MSMessage`将在 Safari 中打开和消息的表示形式应在此处显示。

在 watchOS 中，在邮件应用可切换到附加的 iOS 设备，用户可撰写回复的交互式消息。

如果交互式消息收到较旧的 Apple 平台上，新消息 API 具有回退的支持：

- watchOS 2 +
- OS X 10.11 +
- iOS 9 +

为两个单独的消息，将回退格式传递它们：

- 提供的模板布局，其中一个将成为的图像。
- 中提供另一个将是 URL `MSMessage`。

## <a name="summary"></a>总结

本文介绍了用于使用与消息应用扩展与集成的 Xamarin.iOS 解决方案的高级的技术**消息**应用并向用户提供新功能。


## <a name="related-links"></a>相关链接

- [Ice cream 生成器 （示例）](https://developer.xamarin.com/samples/monotouch/ios10/IceCreamBuilder/)
- [消息引用](https://developer.apple.com/reference/messages)
- [应用扩展编程指南](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214)
