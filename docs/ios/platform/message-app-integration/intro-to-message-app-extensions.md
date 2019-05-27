---
title: 在 Xamarin.iOS 中的消息应用扩展基本知识
description: 本文演示如何包含消息应用扩展中的 Xamarin.iOS 解决方案与消息应用集成，并向用户提供新功能。
ms.prod: xamarin
ms.assetid: 0CFB494C-376C-449D-B714-9E82644F9DA3
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/02/2017
ms.openlocfilehash: cdeaae6cb83062f0d84a3605582b9779c9f36145
ms.sourcegitcommit: 6ad272c2c7b0c3c30e375ad17ce6296ac1ce72b2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/23/2019
ms.locfileid: "66178044"
---
# <a name="message-app-extension-basics-in-xamarinios"></a>在 Xamarin.iOS 中的消息应用扩展基本知识

_本文演示如何包含消息应用扩展中的 Xamarin.iOS 解决方案与消息应用集成，并向用户提供新功能。_

新建与消息应用扩展集成到 iOS 10**消息**应用并提供新功能提供给用户。 该扩展可以发送文本、 贴纸、 媒体文件和交互式消息。

## <a name="about-message-app-extensions"></a>有关消息应用扩展

如上面所述，消息应用扩展可与集成**消息**应用并提供新功能提供给用户。 该扩展可以发送文本、 贴纸、 媒体文件和交互式消息。 提供了两种类型的消息应用扩展：

- **不干胶标签包**-包含一系列可以将用户添加到一条消息的贴纸。 不干胶标签包可以创建无需编写任何代码。
- **iMessage 应用**-可以显示用于选择不干胶标签、 输入文本，包括 （具有可选类型转换） 的媒体文件和创建、 编辑和发送交互消息在 Messages 应用内自定义用户界面。

消息应用扩展提供了三种主要的内容类型：

- **交互式消息**-是一种类型的应用程序生成的自定义消息内容，当在用户点击该消息，该应用程序将启动在前台。
- **贴纸**-是由可以包含在用户之间发送的消息的应用程序生成的图像。
- **其他支持内容**-应用程序可提供内容，例如照片、 视频、 文本或任何其他内容的链接类型始终通过消息应用受支持。

新 ios 10、 消息应用程序现在包括其自己专用的内置应用商店。 包含消息应用扩展的任何应用将显示，并且可以提升此存储区中。 新的消息应用抽屉将显示已从要向用户提供快速访问的消息应用程序存储区中下载任何应用。

此外新在 iOS 10 中，Apple 已添加内联应用归属这样用户就可以轻松发现应用程序。 例如，如果一个用户从第二个用户不具有的应用将内容发送到另一个安装 （如示例的贴纸），发送应用的名称被下列出的消息历史记录中的内容。 如果在用户点击应用的名称，我们打开消息应用商店和存储区中选择的应用程序。

消息应用扩展是类似于现有 iOS 应用开发人员已熟悉如何创建和他们将有权访问所有的标准框架和标准的 iOS 应用的功能。 例如：

- 他们有权访问应用内购买。
- 他们有权访问 Apple Pay。
- 他们有权访问设备的硬件，例如照相机。

IOS 10 仅支持消息应用扩展，但是，这些扩展将发送的内容是在 watchOS 和 macOS 设备上可查看。 新_最近使用的项目页_添加到 watchOS 3，将显示通过手机，包括那些从消息应用扩展，已发送的最新不干胶标签，并允许用户从手表发送这些贴纸。

## <a name="about-the-messages-framework"></a>有关消息框架

新 ios 10、 消息 framework 所提供的用户的 iOS 设备上的消息应用扩展和消息应用程序之间的接口。 当用户启动时从应用内部的消息的应用时，此框架允许要发现的应用，并提供数据和布局其 UI 所需的上下文。

启动应用程序后，用户交互来创建一条消息通过共享的新内容。 应用程序然后使用消息框架来将新创建的内容传输到适用于处理的消息应用。

消息框架和消息应用扩展基于现有 iOS 应用扩展技术而构建。 有关应用扩展的详细信息，请参阅 Apple[应用程序扩展编程指南](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214)。

与不同的是 Apple 在整个系统提供了其他扩展点，不需要提供其消息应用扩展主机应用程序，因为消息应用本身充当容器，开发人员。 但是，开发人员可以包括新的或现有 iOS 应用内消息应用扩展并将其传送以及捆绑包的选项。

如果 iOS 应用的捆绑包中包含消息应用扩展，则在设备的主屏幕上，从邮件应用内消息应用抽屉中，将显示应用的图标。 如果它不包含在应用程序捆绑包，则消息应用扩展将仅显示在消息应用抽屉中。

即使不包括在主机应用程序捆绑包中的消息应用扩展，开发人员需要提供消息应用扩展的捆绑包中的应用程序图标，因为这是将在消息应用抽屉或设置等系统其他部分中显示的图标扩展。

## <a name="about-stickers"></a>有关贴纸

Apple 设计为一种新方法进行通信，从而不干胶标签的 iMessage 用户作为任何其他消息内容发送内联的贴纸或将它们附加到会话内的上一个消息气泡。

不干胶标签有哪些？

- 它们是消息应用扩展提供的映像。
- 它们可以是动画或静态图像。
- 它们提供共享从应用内的图像内容的新方法。

有两种方法创建不干胶标签：

1. 不干胶标签包消息应用扩展可以从创建在 Xcode 内而不包含任何代码。 只需要是资产的贴纸和应用程序图标。
2. 通过创建标准的消息应用扩展，它提供从通过消息框架代码的贴纸。

### <a name="creating-sticker-packs"></a>创建不干胶标签包

不干胶标签 Pack 通过 Xcode 内特殊模板创建，并且只需提供一组静态图像资产可以用作贴纸。 如上面所述，它们不需要任何代码，开发人员只需将图像文件拖动到贴纸资产目录内的不干胶标签包文件夹。

要贴纸包中包含的图像，它必须满足以下要求：

- 映像必须可采用 PNG、 APNG、 GIF 或 JPEG 格式。 使用仅 PNG 和 APNG 格式提供不干胶标签资产时，Apple 提供建议。
- 经过动画处理的不干胶标签仅支持 APNG 和 GIF 格式。
- 不干胶标签图像应提供透明背景，因为可以将它们放在会话中消息气泡用户。
- 单独的图像文件必须小于 500 kb。
- 映像不能为小于 100 x 100 点或更大，206 x 206 指向。

> [!IMPORTANT]
> 应始终提供不干胶标签图像，且`@3x`300x300 到 618 x 618 像素范围内的解决方法。 系统将自动生成`@2x`和`@1x`在运行时所需的版本。

测试针对各种不同的彩色的背景 （如白色、 黑色、 红色、 黄色和多彩色） 和随着照片不干胶标签图像资产，以确保它们在所有可能的情况下查找最佳，Apple 提供建议。

不干胶标签包可以提供贴纸，三个可用大小之一：

- **小**-100 x 100 点。
- **中等**-136 x 136 点。 这是默认大小。
- **大型**-206 x 206 点。

使用 Xcode 的属性检查器设置的不干胶标签在内的整个包大小，并仅提供匹配请求的大小，为获得最佳结果消息应用内不干胶标签浏览器中的图像资产。

有关详细信息，请参阅我们[Ice cream 生成器](https://developer.xamarin.com/samples/monotouch/ios10/IceCreamBuilder/)应用程序和 Apple[消息引用](https://developer.apple.com/reference/messages)。

## <a name="creating-a-custom-sticker-experience"></a>创建自定义的不干胶标签体验

如果应用需要更多控制或灵活性所能提供的不干胶标签包，它可包含消息应用扩展，并提供自定义不干胶标签体验通过消息框架贴纸。

创建自定义不干胶标签体验的优势是什么？

1. 允许此应用自定义便签应用程序的用户的显示方式。 例如，若存在不干胶标签的格式标准的网格布局以外或在不同的彩色背景上。
2. 允许以动态创建从代码而不是包括为静态图像资产的贴纸。
3. 允许以动态地从开发人员的 web 服务器下载而无需发布到 App Store 的新版本的不干胶标签图像资产。
4. 允许访问设备的摄像机的不干胶标签上的动态创建。
5. 允许应用内购买，以便用户可以购买应用内从的多个贴纸。

若要创建自定义不干胶标签体验，执行以下步骤：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 启动 Visual Studio for mac。
2. 打开解决方案，以添加到消息应用扩展。 
3. 选择**iOS** > **扩展** > **iMessage 扩展**然后单击**下一步**按钮： 

    [![](intro-to-message-app-extensions-images/message01.png "选择 iMessage 扩展")](intro-to-message-app-extensions-images/message01.png#lightbox)
4. 输入**扩展插件名称**然后单击**下一步**按钮： 

    [![](intro-to-message-app-extensions-images/message02.png "输入扩展名称")](intro-to-message-app-extensions-images/message02.png#lightbox)
5. 单击**创建**按钮以生成扩展： 

    [![](intro-to-message-app-extensions-images/message03.png "单击创建按钮")](intro-to-message-app-extensions-images/message03.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 启动 Visual Studio。
2. 打开解决方案，以添加消息应用扩展。
3. 选择**iOS 扩展 > iMessage 扩展 (iOS)** 然后单击**下一步**按钮：

    [![选择 iMessage 扩展 (iOS)](intro-to-message-app-extensions-images/message01.w157-sml.png)](intro-to-message-app-extensions-images/message01.w157.png#lightbox)

4. 输入**名称**然后单击**确定**按钮

-----

默认情况下，`MessagesViewController.cs`文件将添加到解决方案。 这是在扩展的主入口点，它继承自`MSMessageAppViewController`类。

消息框架提供了用于向用户显示可用的贴纸的类：

- `MSStickerBrowserViewController` -控制贴纸会中看到的视图。 它还符合`IMSStickerBrowserViewDataSource`接口返回不干胶标签数和给定的浏览器索引的不干胶标签。
- `MSStickerBrowserView` -这是可用的不干胶标签将显示在该视图。
- `MSStickerSize` -决定不干胶标签显示在浏览器视图中的网格的各个单元格的大小。

### <a name="creating-a-custom-sticker-browser"></a>创建自定义的不干胶标签浏览器

开发人员可以进一步自定义的不干胶标签体验用户通过提供自定义不干胶标签浏览器 (`MSMessageAppBrowserViewController`) 消息应用扩展中。 自定义不干胶标签浏览器更改时它们会选择要包含在消息流中的贴纸贴纸给用户的呈现方式。

请执行以下操作：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 在中**Solution Pad**，右键单击扩展的项目名称并选择**添加** > **新文件...**  >  **iOS |Apple Watch** > **界面控制器**。
2. 输入`StickerBrowserViewController`有关**名称**然后单击**新建**按钮： 

    [![](intro-to-message-app-extensions-images/browser01.png "为名称输入 StickerBrowserViewController")](intro-to-message-app-extensions-images/browser01.png#lightbox)
3. 打开`StickerBrowserViewController.cs`文件进行编辑。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 在中**解决方案资源管理器**，右键单击扩展的项目名称并选择**添加** > **新文件...**  >  **iOS |Apple Watch** > **界面控制器**。
2. 输入`StickerBrowserViewController`有关**名称**然后单击**新建**按钮： 

    [![](intro-to-message-app-extensions-images/browser01.w157-sml.png "为名称输入 StickerBrowserViewController")](intro-to-message-app-extensions-images/browser01.w157.png#lightbox)
3. 打开`StickerBrowserViewController.cs`文件进行编辑。

-----

使`StickerBrowserViewController.cs`如下所示：

```csharp
using System;
using System.Collections.Generic;
using UIKit;
using Messages;
using Foundation;

namespace MonkeyStickers
{
    public partial class StickerBrowserViewController : MSStickerBrowserViewController
    {
        #region Computed Properties
        public List<MSSticker> Stickers { get; set; } = new List<MSSticker> ();
        #endregion

        #region Constructors
        public StickerBrowserViewController (MSStickerSize stickerSize) : base (stickerSize)
        {
        }
        #endregion

        #region Private Methods
        private void CreateSticker (string assetName, string localizedDescription)
        {

            // Get path to asset
            var path = NSBundle.MainBundle.PathForResource (assetName, "png");
            if (path == null) {
                Console.WriteLine ("Couldn't create sticker {0}.", assetName);
                return;
            }

            // Build new sticker
            var stickerURL = new NSUrl (path);
            NSError error = null;
            var sticker = new MSSticker (stickerURL, localizedDescription, out error);
            if (error == null) {
                // Add to collection
                Stickers.Add (sticker);
            } else {
                // Report error
                Console.WriteLine ("Error, couldn't create sticker {0}: {1}", assetName, error);
            }
        }

        private void LoadStickers ()
        {

            // Load sticker assets from disk
            CreateSticker ("canada", "Canada Sticker");
            CreateSticker ("clouds", "Clouds Sticker");
            ...
            CreateSticker ("tree", "Tree Sticker");
        }
        #endregion

        #region Public Methods
        public void ChangeBackgroundColor (UIColor color)
        {
            StickerBrowserView.BackgroundColor = color;

        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Initialize
            LoadStickers ();
        }

        public override nint GetNumberOfStickers (MSStickerBrowserView stickerBrowserView)
        {
            return Stickers.Count;
        }

        public override MSSticker GetSticker (MSStickerBrowserView stickerBrowserView, nint index)
        {
            return Stickers[(int)index];
        }
        #endregion
    }
}
```

看看上面的代码中详细信息。 它将创建该扩展提供贴纸存储：

```csharp
public List<MSSticker> Stickers { get; set; } = new List<MSSticker> ();
```

重写的两个方法和`MSStickerBrowserViewController`类来为该数据存储区中的浏览器提供数据：

```csharp
public override nint GetNumberOfStickers (MSStickerBrowserView stickerBrowserView)
{
    return Stickers.Count;
}

public override MSSticker GetSticker (MSStickerBrowserView stickerBrowserView, nint index)
{
    return Stickers[(int)index];
}
```

`CreateSticker`方法获取从扩展的捆绑包的图像资产的路径，并使用它来创建的新实例`MSSticker`此资产，它将添加到集合中：

```csharp
private void CreateSticker (string assetName, string localizedDescription)
{

    // Get path to asset
    var path = NSBundle.MainBundle.PathForResource (assetName, "png");
    if (path == null) {
        Console.WriteLine ("Couldn't create sticker {0}.", assetName);
        return;
    }

    // Build new sticker
    var stickerURL = new NSUrl (path);
    NSError error = null;
    var sticker = new MSSticker (stickerURL, localizedDescription, out error);
    if (error == null) {
        // Add to collection
        Stickers.Add (sticker);
    } else {
        // Report error
        Console.WriteLine ("Error, couldn't create sticker {0}: {1}", assetName, error);
    }
}
```

`LoadSticker`方法从调用`ViewDidLoad`从命名的图像资产 （包括在应用的捆绑包） 创建不干胶标签并将其添加到集合的贴纸。

若要实现自定义不干胶标签浏览器，编辑`MessagesViewController.cs`文件，并使其看起来如下所示：

```csharp
using System;
using UIKit;
using Messages;

namespace MonkeyStickers
{
    public partial class MessagesViewController : MSMessagesAppViewController
    {
        #region Computed Properties
        public StickerBrowserViewController BrowserViewController { get; set;}
        #endregion

        #region Constructors
        protected ViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();


            // Create new browser and configure it
            BrowserViewController = new StickerBrowserViewController (MSStickerSize.Regular);
            BrowserViewController.View.Frame = View.Frame;
            BrowserViewController.ChangeBackgroundColor (UIColor.Gray);

            // Add to view
            AddChildViewController (BrowserViewController);
            BrowserViewController.DidMoveToParentViewController (this);
            View.AddSubview (BrowserViewController.View);
        }
        #endregion
    }
}
```

它将详细地看一看此代码，创建自定义浏览器的存储：

```csharp
public StickerBrowserViewController BrowserViewController { get; set;}
```

然后在`ViewDidLoad`方法，它会实例化并配置新的浏览器：

```csharp
// Create new browser and configure it
BrowserViewController = new StickerBrowserViewController (MSStickerSize.Regular);
BrowserViewController.View.Frame = View.Frame;
BrowserViewController.ChangeBackgroundColor (UIColor.Gray);
```

然后它将在浏览器添加到视图以显示它：

```csharp
// Add to view
AddChildViewController (BrowserViewController);
BrowserViewController.DidMoveToParentViewController (this);
View.AddSubview (BrowserViewController.View);
```

### <a name="further-sticker-customization"></a>进一步的不干胶标签自定义

进一步的不干胶标签自定义可通过消息应用扩展中包括两个类：

- `MSStickerView`
- `MSSticker`

使用上述方法，该扩展可以支持不依赖于标准的不干胶标签浏览器方法的不干胶标签选择。 此外，可以两种不同的视图模式之间切换的不干胶标签显示：

- **Compact** -这是默认模式的不干胶标签视图占用消息视图中的底部 25%。
- **展开**-不干胶标签视图填充整个消息视图。

此不干胶标签视图可以这些模式之间手动或以编程方式通过来切换用户。

看看下面的示例处理两种不同的视图模式之间切换。 两个不同的视图控制器将需要为每个状态。 `StickerBrowserViewController`句柄**Compact**视图和看起来如下所示：

```csharp
using System;
using System.Collections.Generic;
using UIKit;
using Messages;
using Foundation;

namespace MessageExtension
{
    public partial class StickerBrowserViewController : MSStickerBrowserViewController
    {
        #region Computed Properties
        public MessagesViewController MessagesAppViewController { get; set; }
        public List<MSSticker> Stickers { get; set; } = new List<MSSticker> ();
        #endregion

        #region Constructors
        public StickerBrowserViewController (MessagesViewController messagesAppViewController, MSStickerSize stickerSize) : base (stickerSize)
        {
            // Initialize
            this.MessagesAppViewController = messagesAppViewController;
        }
        #endregion

        #region Private Methods
        private void CreateSticker (string assetName, string localizedDescription)
        {

            // Get path to asset
            var path = NSBundle.MainBundle.PathForResource (assetName, "png");
            if (path == null) {
                Console.WriteLine ("Couldn't create sticker {0}.", assetName);
                return;
            }

            // Build new sticker
            var stickerURL = new NSUrl (path);
            NSError error = null;
            var sticker = new MSSticker (stickerURL, localizedDescription, out error);
            if (error == null) {
                // Add to collection
                Stickers.Add (sticker);
            } else {
                // Report error
                Console.WriteLine ("Error, couldn't create sticker {0}: {1}", assetName, error);
            }
        }

        private void LoadStickers ()
        {

            // Load sticker assets from disk
            CreateSticker ("add", "Add New Sticker");
            CreateSticker ("canada", "Canada Sticker");
            CreateSticker ("clouds", "Clouds Sticker");
            CreateSticker ("tree", "Tree Sticker");
        }
        #endregion

        #region Public Methods
        public void ChangeBackgroundColor (UIColor color)
        {
            StickerBrowserView.BackgroundColor = color;

        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Initialize
            LoadStickers ();

        }

        public override nint GetNumberOfStickers (MSStickerBrowserView stickerBrowserView)
        {
            return Stickers.Count;
        }

        public override MSSticker GetSticker (MSStickerBrowserView stickerBrowserView, nint index)
        {
            // Wanting to add a new sticker?
            if (index == 0) {
                // Yes, ask controller to present add sticker interface
                MessagesAppViewController.AddNewSticker ();
                return null;
            } else {
                // No, return existing sticker
                return Stickers [(int)index];
            }
        }
        #endregion
    }
}
```

`AddStickerViewController`将处理**扩展**不干胶标签视图并查看如下所示：

```csharp
using System;
using Foundation;
using UIKit;
using Messages;

namespace MessageExtension
{
    public class AddStickerViewController : UIViewController
    {
        #region Computed Properties
        public MessagesViewController MessagesAppViewController { get; set;}
        public MSSticker NewSticker { get; set;}
        #endregion

        #region Constructors
        public AddStickerViewController (MessagesViewController messagesAppViewController)
        {
            // Initialize
            this.MessagesAppViewController = messagesAppViewController;
        }
        #endregion

        #region Override Method
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Build interface to create new sticker
            var cancelButton = new UIButton (UIButtonType.RoundedRect);
            cancelButton.TouchDown += (sender, e) => {
                // Cancel add new sticker
                MessagesAppViewController.CancelAddNewSticker ();
            };
            View.AddSubview (cancelButton);

            var doneButton = new UIButton (UIButtonType.RoundedRect);
            doneButton.TouchDown += (sender, e) => {
                // Add new sticker to collection
                MessagesAppViewController.AddStickerToCollection (NewSticker);
            };
            View.AddSubview (doneButton);
            
            ...
        }
        #endregion
    }
}
```

`MessageViewController`实现这些视图控制器来驱动所请求的状态：

```csharp
using System;
using UIKit;
using Messages;

namespace MessageExtension
{
    public partial class MessagesViewController : MSMessagesAppViewController
    {
        #region Computed Properties
        public bool IsAddingSticker { get; set;}
        public StickerBrowserViewController BrowserViewController { get; set; }
        public AddStickerViewController AddStickerController { get; set;}
        #endregion

        #region Constructors
        protected MessagesViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Public Methods
        public void PresentStickerBrowser ()
        {
            // Is the Add sticker view being displayed?
            if (IsAddingSticker) {
                // Yes, remove it from view
                AddStickerController.RemoveFromParentViewController ();
                AddStickerController.View.RemoveFromSuperview ();
            }

            // Add to view
            AddChildViewController (BrowserViewController);
            BrowserViewController.DidMoveToParentViewController (this);
            View.AddSubview (BrowserViewController.View);

            // Save mode
            IsAddingSticker = false;
        }

        public void PresentAddSticker ()
        {
            // Is the sticker browser being displayed?
            if (!IsAddingSticker) {
                // Yes, remove it from view
                BrowserViewController.RemoveFromParentViewController ();
                BrowserViewController.View.RemoveFromSuperview ();
            }

            // Add to view
            AddChildViewController (AddStickerController);
            AddStickerController.DidMoveToParentViewController (this);
            View.AddSubview (AddStickerController.View);

            // Save mode
            IsAddingSticker = true;
        }

        public void AddNewSticker ()
        {
            // Switch to expanded view mode
            Request (MSMessagesAppPresentationStyle.Expanded);
        }

        public void CancelAddNewSticker ()
        {
            // Switch to compact view mode
            Request (MSMessagesAppPresentationStyle.Compact);
        }

        public void AddStickerToCollection (MSSticker sticker)
        {
            // Add sticker to collection
            BrowserViewController.Stickers.Add (sticker);

            // Switch to compact view mode
            Request (MSMessagesAppPresentationStyle.Compact);
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Create new browser and configure it
            BrowserViewController = new StickerBrowserViewController (this, MSStickerSize.Regular);
            BrowserViewController.View.Frame = View.Frame;
            BrowserViewController.ChangeBackgroundColor (UIColor.Gray);

            // Create new Add controller and configure it as well
            AddStickerController = new AddStickerViewController (this);
            AddStickerController.View.Frame = View.Frame;

            // Initially present the sticker browser
            PresentStickerBrowser ();
        }

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
        #endregion
    }
}
```

当用户请求以将新的不干胶标签添加到其可用的集合，一个新`AddStickerViewController`可见的控制器和不干胶标签视图进入**扩展**视图：

```csharp
// Switch to expanded view mode
Request (MSMessagesAppPresentationStyle.Expanded);
```

当用户，选择要添加的贴纸时，则将它添加到其可用集合和**Compact**请求视图：

```csharp
public void AddStickerToCollection (MSSticker sticker)
{
    // Add sticker to collection
    BrowserViewController.Stickers.Add (sticker);

    // Switch to compact view mode
    Request (MSMessagesAppPresentationStyle.Compact);
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

## <a name="summary"></a>总结

本文只讨论了与集成的 Xamarin.iOS 解决方案中包含消息应用扩展**消息**应用并向用户提供新功能。 其中包括如何使用扩展来发送文本、 贴纸、 媒体文件和交互式消息。



## <a name="related-links"></a>相关链接

- [Ice cream 生成器 （示例）](https://developer.xamarin.com/samples/monotouch/ios10/IceCreamBuilder/)
- [消息引用](https://developer.apple.com/reference/messages)
- [应用扩展编程指南](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214)
