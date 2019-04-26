---
title: 在 Xamarin.Mac 中.storyboard/.xib-less 用户界面设计
description: 本文介绍如何创建 Xamarin.Mac 应用程序的用户界面直接从C#代码，而无需.storyboard 文件、.xib 文件或 Interface Builder。
ms.prod: xamarin
ms.assetid: 02310F58-DCF1-4589-9F4A-065DF64FC0E1
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 076c6464359a58c2b36d157d9620673b0644cd4a
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61042239"
---
# <a name="storyboardxib-less-user-interface-design-in-xamarinmac"></a>在 Xamarin.Mac 中.storyboard/.xib-less 用户界面设计

_本文介绍如何创建 Xamarin.Mac 应用程序的用户界面直接从C#代码，而无需.storyboard 文件、.xib 文件或 Interface Builder。_

## <a name="overview"></a>概述

当使用C#和.NET 在 Xamarin.Mac 应用程序中，有权访问相同的用户界面元素和工具使用的开发人员*Objective C*并*Xcode* does。 通常情况下，当创建 Xamarin.Mac 应用程序，您将使用 Xcode 的 Interface Builder.storyboard 或.xib 文件来创建和维护应用程序的用户界面。

此外可以选择创建部分或全部 Xamarin.Mac 应用程序的 UI 直接在C#代码。 在本文中，我们将介绍创建用户界面和中的 UI 元素的基础知识C#代码。

[![Visual Studio for Mac 代码编辑器](xibless-ui-images/intro01.png "Visual Studio for Mac 代码编辑器")](xibless-ui-images/intro01-large.png#lightbox)

<a name="Switching_a_Window_to_use_Code" />

## <a name="switching-a-window-to-use-code"></a>切换一个窗口来使用代码

在创建新的 Xamarin.Mac 的 Cocoa 应用程序时，默认情况下获得标准保留为空，窗口。 在中定义此 windows **Main.storyboard** (或传统**MainWindow.xib**) 自动包含在项目中的文件。 这还包括**ViewController.cs**文件，用于管理应用程序的主视图 (或传统上再次**了构成**和一个**MainWindowController.cs**文件)。

若要切换到 Xibless 窗口应用程序，请执行以下操作：

1. 打开你想要停止使用的应用程序`.storyboard`或.xib 文件定义用户界面在 Visual Studio for mac。
2. 在中**Solution Pad**，右键单击**Main.storyboard**或**MainWindow.xib**文件，然后选择**删除**: 

    ![主要情节提要或窗口中删除](xibless-ui-images/switch01.png "删除主要情节提要或窗口")
3. 从**删除对话框**，单击**删除**按钮以从项目中完全删除.storyboard 或.xib: 

    ![确认删除](xibless-ui-images/switch02.png "确认删除")

现在，我们将需要修改**了构成**文件以定义窗口的布局和修改**ViewController.cs**或**MainWindowController.cs**文件以创建实例我们`MainWindow`类由于我们不能再使用.storyboard 或.xib 文件。

新型 Xamarin.Mac 应用程序的使用可能不会自动包括其用户界面情节提要**了构成**， **ViewController.cs**或**MainWindowController.cs**文件。 根据需要，只需添加一个新的空C#项目的类 (**添加** > **新建文件...**  > **常规** > **空类**) 并将其命名为缺少的文件相同。 


### <a name="defining-the-window-in-code"></a>在代码中定义窗口

接下来，编辑**了构成**文件，并使其看起来如下所示：

```csharp
using System;
using Foundation;
using AppKit;
using CoreGraphics;

namespace MacXibless
{
    public partial class MainWindow : NSWindow
    {
        #region Private Variables
        private int NumberOfTimesClicked = 0;
        #endregion

        #region Computed Properties
        public NSButton ClickMeButton { get; set;}
        public NSTextField ClickMeLabel { get ; set;}
        #endregion

        #region Constructors
        public MainWindow (IntPtr handle) : base (handle)
        {
        }

        [Export ("initWithCoder:")]
        public MainWindow (NSCoder coder) : base (coder)
        {
        }

        public MainWindow(CGRect contentRect, NSWindowStyle aStyle, NSBackingStore bufferingType, bool deferCreation): base (contentRect, aStyle,bufferingType,deferCreation) {
            // Define the user interface of the window here
            Title = "Window From Code";

            // Create the content view for the window and make it fill the window
            ContentView = new NSView (Frame);

            // Add UI elements to window
            ClickMeButton = new NSButton (new CGRect (10, Frame.Height-70, 100, 30)){
                AutoresizingMask = NSViewResizingMask.MinYMargin
            };
            ContentView.AddSubview (ClickMeButton);

            ClickMeLabel = new NSTextField (new CGRect (120, Frame.Height - 65, Frame.Width - 130, 20)) {
                BackgroundColor = NSColor.Clear,
                TextColor = NSColor.Black,
                Editable = false,
                Bezeled = false,
                AutoresizingMask = NSViewResizingMask.WidthSizable | NSViewResizingMask.MinYMargin,
                StringValue = "Button has not been clicked yet."
            };
            ContentView.AddSubview (ClickMeLabel);
        }
        #endregion

        #region Override Methods
        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();

            // Wireup events
            ClickMeButton.Activated += (sender, e) => {
                // Update count
                ClickMeLabel.StringValue = (++NumberOfTimesClicked == 1) ? "Button clicked one time." : string.Format("Button clicked {0} times.",NumberOfTimesClicked);
            };
        }
        #endregion

    }
}
```

让我们讨论几个关键元素。

首先，我们添加了少量_计算属性_将充当输出口 （就像在.storyboard 或.xib 文件中创建的窗口）：

```csharp
public NSButton ClickMeButton { get; set;}
public NSTextField ClickMeLabel { get ; set;}
```

这将使得我们能够访问我们将在窗口上显示的 UI 元素。 由于窗口不正在从.storyboard 或.xib 文件进行扩充，因此我们需要它进行实例化的方法 (正如我们将更高版本中看到`MainWindowController`类)。 这就是此新的构造函数方法的作用：

```csharp
public MainWindow(CGRect contentRect, NSWindowStyle aStyle, NSBackingStore bufferingType, bool deferCreation): base (contentRect, aStyle,bufferingType,deferCreation) {
    ...
}
```

这是我们将设计窗口的布局，并将创建所需的用户界面所需的任何 UI 元素。 我们可以将任何 UI 元素添加到一个窗口之前，它需要_内容视图_以包含元素：

```csharp
ContentView = new NSView (Frame);
```

这将创建一个内容视图，它将填满窗口。 现在，我们添加我们的第一个 UI 元素， `NSButton`，到窗口：

```csharp
ClickMeButton = new NSButton (new CGRect (10, Frame.Height-70, 100, 30)){
    AutoresizingMask = NSViewResizingMask.MinYMargin
};
ContentView.AddSubview (ClickMeButton);
```

首先要注意的一点是，与不同的是 iOS、 macOS 使用数学表示法来定义其窗口坐标系统。 因此的起始点是窗口的具有值增加右键和向窗口的右上角左下角中。 当我们创建的新`NSButton`，我们考虑到这一点我们在屏幕上定义其位置和大小。

`AutoresizingMask = NSViewResizingMask.MinYMargin`属性会告知按钮我们希望它处于窗口顶部的同一个位置，当垂直调整窗口的大小。 同样，这是必需，因为 (0，0) 是窗口的左下角。

最后，`ContentView.AddSubview (ClickMeButton)`方法将添加`NSButton`到内容视图，因此它将显示在屏幕上，应用程序时运行和显示的窗口。

接下来将标签添加到窗口将显示的次数的`NSButton`已单击： 

```csharp
ClickMeLabel = new NSTextField (new CGRect (120, Frame.Height - 65, Frame.Width - 130, 20)) {
    BackgroundColor = NSColor.Clear,
    TextColor = NSColor.Black,
    Editable = false,
    Bezeled = false,
    AutoresizingMask = NSViewResizingMask.WidthSizable | NSViewResizingMask.MinYMargin,
    StringValue = "Button has not been clicked yet."
};
ContentView.AddSubview (ClickMeLabel);
``` 

由于 macOS 不包含特定_标签_UI 元素中，我们已添加了特殊样式不可编辑`NSTextField`充当一个标签。 就像之前的大小和位置会考虑按钮一样的 (0，0) 是窗口的左下角。 `AutoresizingMask = NSViewResizingMask.WidthSizable | NSViewResizingMask.MinYMargin`使用属性**或**运算符以组合两个`NSViewResizingMask`功能。 这将使窗口调整大小时垂直保留在从窗口顶部的同一个位置和收缩和水平调整窗口的大小随着宽度的标签。

同样，`ContentView.AddSubview (ClickMeLabel)`方法将添加`NSTextField`到内容视图，因此当运行该应用程序和打开窗口时，将屏幕上显示它。


### <a name="adjusting-the-window-controller"></a>调整窗口控制器

由于设计的`MainWindow`不再加载从.storyboard 或.xib 文件，我们将需要进行一些调整到窗口控制器。 编辑**MainWindowController.cs**文件，并使其看起来如下所示：

```csharp
using System;

using Foundation;
using AppKit;
using CoreGraphics;

namespace MacXibless
{
    public partial class MainWindowController : NSWindowController
    {
        public MainWindowController (IntPtr handle) : base (handle)
        {
        }

        [Export ("initWithCoder:")]
        public MainWindowController (NSCoder coder) : base (coder)
        {
        }

        public MainWindowController () : base ("MainWindow")
        {
            // Construct the window from code here
            CGRect contentRect = new CGRect (0, 0, 1000, 500);
            base.Window = new MainWindow(contentRect, (NSWindowStyle.Titled | NSWindowStyle.Closable | NSWindowStyle.Miniaturizable | NSWindowStyle.Resizable), NSBackingStore.Buffered, false);

            // Simulate Awaking from Nib
            Window.AwakeFromNib ();
        }

        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();
        }

        public new MainWindow Window {
            get { return (MainWindow)base.Window; }
        }
    }
}

```

允许讨论此修改的关键元素。

首先，我们定义的新实例`MainWindow`类，并将其分配给基窗口控制器`Window`属性：

```csharp
CGRect contentRect = new CGRect (0, 0, 1000, 500);
base.Window = new MainWindow(contentRect, (NSWindowStyle.Titled | NSWindowStyle.Closable | NSWindowStyle.Miniaturizable | NSWindowStyle.Resizable), NSBackingStore.Buffered, false);
```

我们定义的屏幕，其中包含的窗口位置`CGRect`。 窗口的坐标系统，就像屏幕定义较低的左下角 (0，0)。 接下来，我们通过使用定义的窗口样式**或者**连接两个或多个运算符，`NSWindowStyle`功能：

```csharp
... (NSWindowStyle.Titled | NSWindowStyle.Closable | NSWindowStyle.Miniaturizable | NSWindowStyle.Resizable) ...
``` 

以下`NSWindowStyle`功能都可用：

- **无边距**-窗口将具有无边框。
- **标题为**-窗口将具有标题栏。
- **Closable** -窗口都有一个关闭按钮，可以被关闭。
- **Miniaturizable** -窗口具有 Miniaturize 按钮，可以最小化。
- **可调整大小**-窗口将具有重设大小的按钮，并且是可调整大小。
- **实用工具**-窗口是一个实用程序样式窗口 （面板）。
- **DocModal** -如果窗口是一个面板，它将为文档模式对话框而不是系统模式。 
- **NonactivatingPanel** -如果窗口是一个面板，它不会在主窗口。
- **TexturedBackground** -窗口将具有带纹理的背景。
- **不成比例**-不将缩放窗口。
- **UnifiedTitleAndToolbar** -将已加入窗口的标题和工具栏区域。
- **平视仪**-窗口将显示为平视显示面板。
- **FullScreenWindow** -该窗口可以进入全屏模式。
- **FullSizeContentView** -窗口的内容视图是隐藏的标题和工具栏区域。

最后两个属性定义_缓冲类型_窗口，如果窗口的绘图将被延迟。 有关详细信息`NSWindows`，请参阅 Apple[简介 Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)文档。

最后，由于窗口不正在从.storyboard 或.xib 文件进行扩充，我们需要模拟中我们**MainWindowController.cs**通过调用 windows`AwakeFromNib`方法：

```csharp
Window.AwakeFromNib ();
```

这将允许您针对窗口只需编写代码喜欢从.storyboard 或.xib 文件加载一个标准窗口。


### <a name="displaying-the-window"></a>显示窗口

使用.storyboard 或.xib 文件删除和**了构成**并**MainWindowController.cs**文件修改，您要将窗口就像任何正常窗口中创建Xcode 的 Interface Builder.xib 文件。

以下将创建新的窗口和它的控制器实例并在屏幕上显示窗口：

```csharp
private MainWindowController mainWindowController;
...

mainWindowController = new MainWindowController ();
mainWindowController.Window.MakeKeyAndOrderFront (this);
```

此时，如果运行该应用程序，并单击几次按钮，下面将显示：

![运行示例应用](xibless-ui-images/run01.png "运行示例应用")


## <a name="adding-a-code-only-window"></a>添加代码唯一窗口

如果我们想要将仅适用于代码，xibless 窗口添加到现有的 Xamarin.Mac 应用程序，右键单击该项目中**Solution Pad** ，然后选择**添加** > **新文件...**.在中**新的文件**对话框中，选择**Xamarin.Mac** > **与控制器的 Cocoa 窗口**，如下所示：

![添加新的窗口控制器](xibless-ui-images/add01.png "添加新的窗口控制器") 

就像之前，我们将从项目中删除默认.storyboard 或.xib 文件 (在这种情况下**SecondWindow.xib**)，按照中的步骤[切换一个窗口来使用代码](#Switching_a_Window_to_use_Code)上面介绍的部分代码窗口的定义。


## <a name="adding-a-ui-element-to-a-window-in-code"></a>将 UI 元素添加到代码中的窗口

是否在代码中创建或从.storyboard 或.xib 文件加载一个窗口，可能有时间我们想要从代码添加到窗口的 UI 元素。 例如：

```csharp
var ClickMeButton = new NSButton (new CGRect (10, 10, 100, 30)){
    AutoresizingMask = NSViewResizingMask.MinYMargin
};
MyWindow.ContentView.AddSubview (ClickMeButton);
```

上面的代码创建一个新`NSButton`并将其添加到`MyWindow`显示的窗口实例。 基本上可以在代码中创建可以在 Xcode 的 Interface Builder 中.storyboard 或.xib 文件中定义任何 UI 元素并将其显示在窗口中。


## <a name="defining-the-menu-bar-in-code"></a>在代码中定义的菜单栏

由于在 Xamarin.Mac 中的当前限制，不建议您创建的 Xamarin.Mac 应用程序的菜单栏 –`NSMenuBar`– 中的代码，但继续使用**Main.storyboard**或**MainMenu.xib**文件，以定义它。 话虽如此，您可以添加和删除菜单和菜单项在C#代码。

例如，编辑**AppDelegate.cs**文件，然后进行`DidFinishLaunching`方法看起来像以下：

```csharp
public override void DidFinishLaunching (NSNotification notification)
{
    mainWindowController = new MainWindowController ();
    mainWindowController.Window.MakeKeyAndOrderFront (this);

    // Create a Status Bar Menu
    NSStatusBar statusBar = NSStatusBar.SystemStatusBar;

    var item = statusBar.CreateStatusItem (NSStatusItemLength.Variable);
    item.Title = "Phrases";
    item.HighlightMode = true;
    item.Menu = new NSMenu ("Phrases");

    var address = new NSMenuItem ("Address");
    address.Activated += (sender, e) => {
        Console.WriteLine("Address Selected");
    };
    item.Menu.AddItem (address);

    var date = new NSMenuItem ("Date");
    date.Activated += (sender, e) => {
        Console.WriteLine("Date Selected");
    };
    item.Menu.AddItem (date);

    var greeting = new NSMenuItem ("Greeting");
    greeting.Activated += (sender, e) => {
        Console.WriteLine("Greetings Selected");
    };
    item.Menu.AddItem (greeting);

    var signature = new NSMenuItem ("Signature");
    signature.Activated += (sender, e) => {
        Console.WriteLine("Signature Selected");
    };
    item.Menu.AddItem (signature);
}
```

上述从代码创建状态栏菜单并启动应用程序时显示它。 使用菜单的详细信息，请参阅我们[菜单](~/mac/user-interface/menu.md)文档。


## <a name="summary"></a>总结

本文已创建 Xamarin.Mac 应用程序中的用户界面的详细的信息C#而不是使用.storyboard 或.xib 文件使用 Xcode 的 Interface Builder 的代码。



## <a name="related-links"></a>相关链接

- [MacXibless （示例）](https://developer.xamarin.com/samples/mac/MacXibless/)
- [Windows](~/mac/user-interface/window.md)
- [菜单](~/mac/user-interface/menu.md)
- [macOS 人机界面指南](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
- [Windows 简介](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/WinPanel/Introduction.html)
