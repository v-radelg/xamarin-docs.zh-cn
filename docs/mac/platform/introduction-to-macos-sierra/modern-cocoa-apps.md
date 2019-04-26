---
title: 生成新式 macOS 应用
description: 这篇文章介绍了几个技巧、 功能和技术开发人员可以使用生成新式 macOS 应用在 Xamarin.Mac 中。
ms.prod: xamarin
ms.assetid: F20EE590-246E-40EB-B309-D9D8C090C7F1
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 53bfc9f147b6cf369b8f5ce8d1257dbaf6b0f807
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61032722"
---
# <a name="building-modern-macos-apps"></a>生成新式 macOS 应用

_这篇文章介绍了几个技巧、 功能和技术开发人员可以使用生成新式 macOS 应用在 Xamarin.Mac 中。_

<a name="Building-Modern-Looks-with-Modern-Views" />

## <a name="building-modern-looks-with-modern-views"></a>构建现代看起来与现代视图

现代样式将包括现代的窗口和工具栏外观如示例应用如下所示：

[![](modern-cocoa-apps-images/content08.png "现代的 Mac 应用程序 UI 示例")](modern-cocoa-apps-images/content08.png#lightbox)

<a name="Enabling-Full-Sized-Content-Views" />

### <a name="enabling-full-sized-content-views"></a>启用完整大小内容视图

若要实现此值看上去 Xamarin.Mac 应用中的，开发人员将需要使用_完整大小的内容视图_，这意味着下的工具和标题栏区域扩展内容和将 macos 自动模糊。

若要启用此功能在代码中的，创建的自定义类`NSWindowController`并使其如下所示：

```csharp
using System;
using Foundation;
using AppKit;

namespace MacModern
{
    public partial class MainWindowController : NSWindowController
    {
        #region Constructor
        public MainWindowController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void WindowDidLoad ()
        {
            base.WindowDidLoad ();

            // Set window to use Full Size Content View
            Window.StyleMask = NSWindowStyle.FullSizeContentView;
        }
        #endregion
    }
}
```

此功能还在 Xcode 的 Interface Builder 中： 选择窗口中，选中启用**完整大小的内容视图**:

[![](modern-cocoa-apps-images/content01.png "编辑在 Xcode 的 Interface Builder 中主要情节提要")](modern-cocoa-apps-images/content01.png#lightbox)

使用完整的大小的内容视图时，开发人员可能需要偏移下方的标题和工具栏区域的内容，以便特定内容 （例如标签） 不会在其下幻灯片。

若要使此问题变得复杂，标题和工具栏区域可以有根据用户当前正在执行，该操作的动态高度 macOS 用户安装和/或 Mac 硬件上运行应用的版本。

因此，将无法工作时对用户界面只是硬编码的偏移量。 开发人员将需要采用动态方法。

包含的 Apple[键值对的可观察](~/mac/app-fundamentals/databinding.md#Observing_Value_Changes)`ContentLayoutRect`属性的`NSWindow`类，以获取当前的内容区域中的代码。 开发人员可以使用此值来手动定位所需的元素内容的区域发生更改时。

更好的解决方案是使用自动布局和大小类来定位在代码或 Interface Builder 中的 UI 元素。

可以使用类似下面的示例代码来定位 UI 元素在应用程序的视图控制器中使用自动布局和大小类：

```csharp
using System;
using AppKit;
using Foundation;

namespace MacModern
{
    public partial class ViewController : NSViewController
    {
        #region Computed Properties
        public NSLayoutConstraint topConstraint { get; set; }
        #endregion

        ...

        #region Override Methods
        public override void UpdateViewConstraints ()
        {
            // Has the constraint already been set?
            if (topConstraint == null) {
                // Get the top anchor point
                var contentLayoutGuide = ItemTitle.Window?.ContentLayoutGuide as NSLayoutGuide;
                var topAnchor = contentLayoutGuide.TopAnchor;

                // Found?
                if (topAnchor != null) {
                    // Assemble constraint and activate it
                    topConstraint = topAnchor.ConstraintEqualToAnchor (topAnchor, 20);
                    topConstraint.Active = true;
                }
            }

            base.UpdateViewConstraints ();
        }
        #endregion
    }
}
```

此代码将创建用于将应用于一个标签的顶部约束存储 (`ItemTitle`) 以确保它不会放在标题和工具栏区域下：

```csharp
public NSLayoutConstraint topConstraint { get; set; }
```

通过重写视图控制器的`UpdateViewConstraints`方法，开发人员可以进行测试，看是否已生成所需的约束并根据需要创建它。

如果需要生成，一个新约束`ContentLayoutGuide`访问数据并需要进行约束的控件转换为窗口的属性`NSLayoutGuide`:

```csharp
var contentLayoutGuide = ItemTitle.Window?.ContentLayoutGuide as NSLayoutGuide;
```

TopAnchor 属性`NSLayoutGuide`访问时，如果可用，它用于生成具有所需的偏移量的新约束，并使新约束处于活动状态，以将其应用：

```csharp
// Assemble constraint and activate it
topConstraint = topAnchor.ConstraintEqualToAnchor (topAnchor, 20);
topConstraint.Active = true;
```

<a name="Enabling-Streamlined-Toolbars" />

### <a name="enabling-streamlined-toolbars"></a>启用简化的工具栏

正常 macOS 窗口包括在标题栏的窗口上边缘到运行的标准。 如果该窗口还包括一个工具条，它将显示在此标题栏区域下：

[![](modern-cocoa-apps-images/content02.png "标准 Mac 工具栏")](modern-cocoa-apps-images/content02.png#lightbox)

当使用简化的工具栏，标题区域将消失，工具栏上移到标题栏的位置的行中的窗口关闭、 最小化和最大化按钮：

[![](modern-cocoa-apps-images/content03.png "简化的 Mac 工具栏")](modern-cocoa-apps-images/content03.png#lightbox)

简化工具栏启用通过重写`ViewWillAppear`方法的`NSViewController`并使其看起来如下所示：

```csharp
public override void ViewWillAppear ()
{
    base.ViewWillAppear ();

    // Enable streamlined Toolbars
    View.Window.TitleVisibility = NSWindowTitleVisibility.Hidden;
}
```

这种效果通常用于_Shoebox 应用程序_（一个窗口应用），如映射、 日历、 说明和系统首选项。 

<a name="Using-Accessory-View-Controllers" />

### <a name="using-accessory-view-controllers"></a>使用附件视图控制器

取决于应用程序的设计，开发人员还可能想要补充恰好标题/工具栏区域提供上下文相关控件添加到基于活动的用户他们的下方显示附件视图控制器在区域是在标题栏当前参与：

[![](modern-cocoa-apps-images/content04.png "示例装饰视图控制器")](modern-cocoa-apps-images/content04.png#lightbox)

将自动模糊附件视图控制器，并为其调整大小而无需开发人员干预系统。

若要添加附件视图控制器，请执行以下操作：

1. 在“解决方案资源管理器”中，双击 `Main.storyboard` 文件，将其打开进行编辑。
2. 拖动**自定义视图控制器**到窗口的层次结构： 

    [![](modern-cocoa-apps-images/content05.png "添加新的自定义视图控制器")](modern-cocoa-apps-images/content05.png#lightbox)
3. 布局附件视图 UI: 

    [![](modern-cocoa-apps-images/content06.png "设计新视图")](modern-cocoa-apps-images/content06.png#lightbox)
4. 公开附件视作**输出口**和任何其他**操作**或**输出口**其 ui: 

    [![](modern-cocoa-apps-images/content07.png "添加所需的输出口")](modern-cocoa-apps-images/content07.png#lightbox)
5. 保存更改。
6. 返回到 Visual Studio for Mac 进行同步所做的更改。

编辑`NSWindowController`并使其如下所示：

```csharp
using System;
using Foundation;
using AppKit;

namespace MacModern
{
    public partial class MainWindowController : NSWindowController
    {
        #region Constructor
        public MainWindowController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void WindowDidLoad ()
        {
            base.WindowDidLoad ();

            // Create a title bar accessory view controller and attach
            // the view created in Interface Builder
            var accessoryView = new NSTitlebarAccessoryViewController ();
            accessoryView.View = AccessoryViewGoBar;

            // Set the location and attach the accessory view to the
            // titlebar to be displayed
            accessoryView.LayoutAttribute = NSLayoutAttribute.Bottom;
            Window.AddTitlebarAccessoryViewController (accessoryView);

        }
        #endregion
    }
}
```

此代码的要点是： 其中视图设置为自定义视图中 Interface Builder 定义并作为公开**输出口**:

```csharp
accessoryView.View = AccessoryViewGoBar;
```

并`LayoutAttribute`，用于定义_其中_将显示附件：

```csharp
accessoryView.LayoutAttribute = NSLayoutAttribute.Bottom;
```

MacOS 现在已完全本地化，因为`Left`并`Right``NSLayoutAttribute`属性已弃用，应使用替换`Leading`和`Trailing`。

<a name="Using-Tabbed-Windows" />

### <a name="using-tabbed-windows"></a>使用选项卡式的 Windows

此外，macOS 系统可能会添加到应用程序窗口的附件视图控制器。 例如，若要创建多个应用程序的 Windows 将合并到一个虚拟窗口选项卡式 Windows:

[![](modern-cocoa-apps-images/content08.png "选项卡式 Mac 窗口的一个示例")](modern-cocoa-apps-images/content08.png#lightbox)

通常情况下，开发人员将需要执行有限的操作，请使用选项卡式 Windows Xamarin.Mac 应用中，系统将自动按如下所示处理它们：

- Windows 会自动将选项卡式时`OrderFront`调用方法。
- Windows 会自动将 Untabbed 时`OrderOut`调用方法。
- 在代码中的所有选项卡式窗口仍被视为"可见"，但是任何非最前面的选项卡是隐藏的系统使用 CoreGraphics。
- 使用`TabbingIdentifier`属性的`NSWindow`到组 Windows 组合在一起成为选项卡。
- 如果它是`NSDocument`基于应用程序，这些功能的几个将启用了自动 （如加号的按钮添加到选项卡栏） 而无需任何开发人员操作。
- 非`NSDocument`的应用可以启用要通过重写中添加一个新文档的选项卡组中的"加号"按钮`GetNewWindowForTab`方法的`NSWindowsController`。

将所有部分集中起来，`AppDelegate`想要使用的应用的基于系统选项卡式 Windows 看起来如下所示：

```csharp
using AppKit;
using Foundation;

namespace MacModern
{
    [Register ("AppDelegate")]
    public class AppDelegate : NSApplicationDelegate
    {
        #region Computed Properties
        public int NewDocumentNumber { get; set; } = 0;
        #endregion

        #region Constructors
        public AppDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override void DidFinishLaunching (NSNotification notification)
        {
            // Insert code here to initialize your application
        }

        public override void WillTerminate (NSNotification notification)
        {
            // Insert code here to tear down your application
        }
        #endregion

        #region Custom Actions
        [Export ("newDocument:")]
        public void NewDocument (NSObject sender)
        {
            // Get new window
            var storyboard = NSStoryboard.FromName ("Main", null);
            var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

            // Display
            controller.ShowWindow (this);
        } 
        #endregion
    }
}
```

其中`NewDocumentNumber`属性跟踪的新创建的文档数和`NewDocument`方法创建一个新文档并将其显示。

`NSWindowController`然后看起来类似于：

```csharp
using System;
using Foundation;
using AppKit;

namespace MacModern
{
    public partial class MainWindowController : NSWindowController
    {
        #region Application Access
        /// <summary>
        /// A helper shortcut to the app delegate.
        /// </summary>
        /// <value>The app.</value>
        public static AppDelegate App {
            get { return (AppDelegate)NSApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Constructor
        public MainWindowController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Public Methods
        public void SetDefaultDocumentTitle ()
        {
            // Is this the first document?
            if (App.NewDocumentNumber == 0) {
                // Yes, set title and increment
                Window.Title = "Untitled";
                ++App.NewDocumentNumber;
            } else {
                // No, show title and count
                Window.Title = $"Untitled {App.NewDocumentNumber++}";
            }
        }
        #endregion

        #region Override Methods
        public override void WindowDidLoad ()
        {
            base.WindowDidLoad ();

            // Prefer Tabbed Windows
            Window.TabbingMode = NSWindowTabbingMode.Preferred;
            Window.TabbingIdentifier = "Main";

            // Set default window title
            SetDefaultDocumentTitle ();

            // Set window to use Full Size Content View
            // Window.StyleMask = NSWindowStyle.FullSizeContentView;

            // Create a title bar accessory view controller and attach
            // the view created in Interface Builder
            var accessoryView = new NSTitlebarAccessoryViewController ();
            accessoryView.View = AccessoryViewGoBar;

            // Set the location and attach the accessory view to the
            // titlebar to be displayed
            accessoryView.LayoutAttribute = NSLayoutAttribute.Bottom;
            Window.AddTitlebarAccessoryViewController (accessoryView);

        }

        public override void GetNewWindowForTab (NSObject sender)
        {
            // Ask app to open a new document window
            App.NewDocument (this);
        }
        #endregion
    }
}
```

其中静态`App`属性提供的快捷方式，以获取到`AppDelegate`。 `SetDefaultDocumentTitle`方法设置新的文档标题基于新创建的文档数。

下面的代码，告知 macOS 应用倾向于使用选项卡，并提供一个字符串，允许应用程序的 Windows 来分组到选项卡：

```csharp
// Prefer Tabbed Windows
Window.TabbingMode = NSWindowTabbingMode.Preferred;
Window.TabbingIdentifier = "Main";
```

和以下重写方法将加号按钮添加到将创建一个新文档时用户单击选项卡栏：

```csharp
public override void GetNewWindowForTab (NSObject sender)
{
    // Ask app to open a new document window
    App.NewDocument (this);
}
```

<a name="Using-Core-Animation" />

### <a name="using-core-animation"></a>使用核心动画

核心动画是一个内置于 macOS 的强大的图形呈现引擎。 核心动画已经过优化，以利用新式 macOS 硬件，而不是运行在 CPU 可能会降低在计算机上的图形操作中可用 GPU （图形处理单元）。

`CALayer`、 提供的核心动画、 可用于任务，例如快速、 流畅滚动和动画。 应用的用户界面应由多个子视图和充分利用核心动画的层组成。

一个`CALayer`对象提供允许开发人员来控制提供内容，如向用户屏幕上的多个属性：

- `Content` -可以是`NSImage`或`CGImage`提供层的内容。
- `BackgroundColor` -设置为层的背景色 `CGColor`
- `BorderWidth` -设置的边框宽度。
- `BorderColor` -设置的边框颜色。

若要利用应用程序的 UI 中的核心图形，它必须使用_层支持_视图，开发人员应始终启用窗口的内容视图中，Apple 提供建议。 这样一来，所有子视图将自动都继承层备份以及。

此外，Apple 建议使用而不添加新的层支持视图`CALayer`作为子层因为系统将自动处理多个所需的设置 （如所需的 Retina 显示屏）。

可以通过设置启用备份的层`WantsLayer`的`NSView`到`true`或在 Xcode 的 Interface Builder 下内部**视图效果检查器**通过检查**核心动画层**:

[![](modern-cocoa-apps-images/content09.png "视图效果检查器")](modern-cocoa-apps-images/content09.png#lightbox)

<a name="Redrawing-Views-with-Layers" />

#### <a name="redrawing-views-with-layers"></a>重绘视图层

设置在 Xamarin.Mac 应用中使用层支持视图时的另一个重要步骤`LayerContentsRedrawPolicy`的`NSView`到`OnSetNeedsDisplay`中`NSViewController`。 例如：

```csharp
public override void ViewWillAppear ()
{
    base.ViewWillAppear ();

    // Set the content redraw policy
    View.LayerContentsRedrawPolicy = NSViewLayerContentsRedrawPolicy.OnSetNeedsDisplay;
}
```

当其帧源发生更改时，如果开发人员不会设置此属性，该视图将重绘其不需要为了提高性能。 此属性设置为`OnSetNeedsDisplay`开发人员手动将需要设置`NeedsDisplay`到`true`以强制重绘，但是的内容。

当视图被标记为已更新时，系统将检查`WantsUpdateLayer`在视图的属性。 如果它返回`true`然后`UpdateLayer`调用方法，否则`DrawRect`视图的方法调用以更新视图的内容。

Apple 已更新的视图内容时所需的以下建议：

- 使用 Apple 首选`UpdateLater`转移`DrawRect`每当可能因为它提供了显著的性能提升。
- 使用相同`layer.Contents`的如下所示的 UI 元素。
- Apple 还首选的开发人员编写使用的标准视图类似于其 UI `NSTextField`、 再次只要有可能。

若要使用`UpdateLayer`，创建的自定义类`NSView`并使代码如下所示：

```csharp
using System;
using Foundation;
using AppKit;

namespace MacModern
{
    public partial class MainView : NSView
    {
        #region Computed Properties
        public override bool WantsLayer {
            get { return true; }
        }

        public override bool WantsUpdateLayer {
            get { return true; }
        }
        #endregion

        #region Constructor
        public MainView (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void DrawRect (CoreGraphics.CGRect dirtyRect)
        {
            base.DrawRect (dirtyRect);

        }

        public override void UpdateLayer ()
        {
            base.UpdateLayer ();

            // Draw view 
            Layer.BackgroundColor = NSColor.Red.CGColor;
        }
        #endregion
    }
}
```

<a name="Using-Modern-Drag-and-Drop" />

## <a name="using-modern-drag-and-drop"></a>使用新型拖放

若要提供现代的拖放功能的用户体验，开发人员应采用_拖动以_在其应用程序的拖放操作。 拖动 Flocking 是作为 flocks （组一起光标下的项目数的计数），随着用户不断拖动操作的各个元素的每个单独的文件或最初被拖动的项的显示位置。

如果用户终止拖动操作，各个元素将 Unflock 并返回到其原始位置。

以下代码示例启用拖放以对自定义视图：

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using AppKit;

namespace MacModern
{
    public partial class MainView : NSView, INSDraggingSource, INSDraggingDestination
    {
        #region Constructor
        public MainView (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void MouseDragged (NSEvent theEvent)
        {
            // Create group of string to be dragged
            var string1 = new NSDraggingItem ((NSString)"Item 1");
            var string2 = new NSDraggingItem ((NSString)"Item 2");
            var string3 = new NSDraggingItem ((NSString)"Item 3");

            // Drag a cluster of items
            BeginDraggingSession (new [] { string1, string2, string3 }, theEvent, this);
        }
        #endregion
    }
}
```

Flocking 效果通过发送到正被拖动每个项来实现`BeginDraggingSession`方法的`NSView`作为数组中一个单独的元素。

使用时`NSTableView`或`NSOutlineView`，使用`PastboardWriterForRow`方法的`NSTableViewDataSource`类开始拖动操作：

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using AppKit;

namespace MacModern
{
    public class ContentsTableDataSource: NSTableViewDataSource
    {
        #region Constructors
        public ContentsTableDataSource ()
        {
        }
        #endregion

        #region Override Methods
        public override INSPasteboardWriting GetPasteboardWriterForRow (NSTableView tableView, nint row)
        {
            // Return required pasteboard writer
            ...
            
            // Pasteboard writer failed
            return null;
        }
        #endregion
    }
}
```

这允许开发人员提供个人`NSDraggingItem`而不是较旧方法正被拖动的表中每个项目`WriteRowsWith`，写入的所有行作为一个组粘贴板。

使用时`NSCollectionViews`，再次使用`PasteboardWriterForItemAt`方法而不是`WriteItemsAt`方法拖动时开始。

开发人员应尽可能避免将粘贴板上的大型文件上传。 向 macOS Sierra，新_文件承诺_允许开发人员可以将引用为给定的用户完成使用新的拖放操作时就更高版本执行粘贴板上的文件`NSFilePromiseProvider`和`NSFilePromiseReceiver`类。

<a name="Using-Modern-Event-Tracking" />

## <a name="using-modern-event-tracking"></a>使用现代的事件跟踪

用户界面元素 (如`NSButton`) 已添加到标题或工具栏区域中，用户应该能够单击元素，并将其激发 （例如，显示一个弹出窗口） 的正常事件。 但是，由于项还在标题或工具栏区域中，用户应该能够单击并拖动要移动窗口也的元素。

若要在代码中实现此目的，创建该元素的自定义类 (如`NSButton`) 和重写`MouseDown`事件，如下所示：

```csharp
public override void MouseDown (NSEvent theEvent)
{
    var shouldCallSuper = false;

    Window.TrackEventsMatching (NSEventMask.LeftMouseUp, 2000, NSRunLoop.NSRunLoopEventTracking, (NSEvent evt, ref bool stop) => {
        // Handle event as normal
        stop = true;
        shouldCallSuper = true;
    });

    Window.TrackEventsMatching(NSEventMask.LeftMouseDragged, 2000, NSRunLoop.NSRunLoopEventTracking, (NSEvent evt, ref bool stop) => {
        // Pass drag event to window
        stop = true;
        Window.PerformWindowDrag (evt);
    });

    // Call super to handle mousedown
    if (shouldCallSuper) {
        base.MouseDown (theEvent);
    }
}
```

此代码使用`TrackEventsMatching`方法`NSWindow`的 UI 元素附加到截获`LeftMouseUp`和`LeftMouseDragged`事件。 有关`LeftMouseUp`事件，像平时一样进行响应的 UI 元素。 有关`LeftMouseDragged`事件，该事件传递给`NSWindow`的`PerformWindowDrag`方法以在屏幕上移动窗口。

调用`PerformWindowDrag`方法的`NSWindow`类提供以下优势：

- 它允许窗口移动，即使挂起应用程序 (例如处理深度循环)。
- 空间切换将按预期方式工作。
- 空格栏将显示正常。
- 窗口对齐和对齐方式能正常工作。

<a name="Using-Modern-Container-View-Controls" />

## <a name="using-modern-container-view-controls"></a>使用新式容器视图控件

macOS Sierra 提供了许多现代改进到上一版本的操作系统中可用的现有容器视图控件。

<a name="Table View Enhancements" />

## <a name="table-view-enhancements"></a>表视图增强功能

开发人员应始终使用新`NSView`如基于容器视图控件版本`NSTableView`。 例如：

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using AppKit;

namespace MacModern
{
    public class ContentsTableDelegate : NSTableViewDelegate
    {
        #region Constructors
        public ContentsTableDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
        {
            // Build new view
            var view = new NSView ();
            ...

            // Return the view representing the item to display
            return view;
        }
        #endregion
    }
}
```

这可以自定义来附加到给定的表 （如轻扫权限才能删除的行） 中的行的表行操作。 若要启用此行为，请重写`RowActions`方法的`NSTableViewDelegate`:

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using AppKit;

namespace MacModern
{
    public class ContentsTableDelegate : NSTableViewDelegate
    {
        #region Constructors
        public ContentsTableDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
        {
            // Build new view
            var view = new NSView ();
            ...

            // Return the view representing the item to display
            return view;
        }

        public override NSTableViewRowAction [] RowActions (NSTableView tableView, nint row, NSTableRowActionEdge edge)
        {
            // Take action based on the edge
            if (edge == NSTableRowActionEdge.Trailing) {
                // Create row actions
                var editAction = NSTableViewRowAction.FromStyle (NSTableViewRowActionStyle.Regular, "Edit", (action, rowNum) => {
                    // Handle row being edited
                    ...
                });

                var deleteAction = NSTableViewRowAction.FromStyle (NSTableViewRowActionStyle.Destructive, "Delete", (action, rowNum) => {
                    // Handle row being deleted
                    ...
                });

                // Return actions
                return new [] { editAction, deleteAction };
            } else {
                // No matching actions
                return null;
            }
        }
        #endregion
    }
}
```

静态`NSTableViewRowAction.FromStyle`用于创建新的表行操作的以下样式：

- `Regular` -执行标准的非破坏性操作，例如编辑行的内容。
- `Destructive` -执行了破坏性操作，例如删除行从表。 将包含一个红色背景呈现这些操作。

<a name="Scroll-View-Enhancements" />

## <a name="scroll-view-enhancements"></a>滚动视图增强功能 

使用滚动视图时 (`NSScrollView`) 直接，或另一个控件的一部分 (如`NSTableView`)，在使用现代的查找和视图的 Xamarin.Mac 应用中的标题和工具栏区域下可以滑动滚动视图的内容。

因此，标题和工具栏区域可以部分遮住滚动视图内容区域中的第一项。

若要更正此问题，Apple 已添加到两个新属性`NSScrollView`类：

- `ContentInsets` -允许开发人员提供`NSEdgeInsets`对象，用于定义将应用于滚动视图顶部的偏移量。
- `AutomaticallyAdjustsContentInsets` -如果`true`滚动视图将自动处理`ContentInsets`为开发人员。

通过使用`ContentInsets`开发人员可以调整要允许包含附件，如滚动视图的开始：

- 排序指示符，如邮件应用程序中所示。
- 搜索字段。
- 刷新或更新按钮。

<a name="Auto-Layout-and-Localization-in-Modern-Apps" />

## <a name="auto-layout-and-localization-in-modern-apps"></a>自动布局和新型应用程序中本地化

Apple 开发人员可轻松地创建国际化的 macOS 应用的 Xcode 中包含多项技术。 现在，Xcode，开发人员可单独从其情节提要文件中的应用程序的用户界面设计的面向用户的文本，并提供工具来维护这种分离，在 UI 更改时。

有关详细信息，请参阅 Apple[国际化和本地化指南](https://developer.apple.com/library/content/documentation/MacOSX/Conceptual/BPInternational/InternationalizingYourUserInterface/InternationalizingYourUserInterface.html)。

<a name="Implementing-Base-Internationalization" />

### <a name="implementing-base-internationalization"></a>实现基国际化 

通过实现基本国际化，开发人员可以提供单一的情节提要文件，表示应用程序的 UI 和分离出来的所有面向用户的字符串。 

初始情节提要文件 （或文件），开发人员创建时定义应用程序的用户界面，它们将生成基本国际化 （开发人员说出的语言） 中。

接下来，开发人员可以导出本地化和翻译为多种语言的基本国际化字符串 （在情节提要 UI 设计）。

更高版本，可以导入这些本地化和 Xcode 的情节提要会生成特定于语言的字符串的文件。

<a name="Implementing-Auto-Layout-to-Support-Localization" />

### <a name="implementing-auto-layout-to-support-localization"></a>实现自动布局以支持本地化

由于本地化版本的字符串值可以大大不同大小和/或读取方向，开发人员应使用自动布局的位置和大小的情节提要文件中的应用程序的用户界面。

Apple 建议执行以下操作：

- **删除固定宽度约束**-所有基于文本的视图应允许调整大小基于其内容。 固定宽度视图可能会裁剪其特定语言中的内容。
- **使用内部函数的内容大小**-通过默认基于文本的视图将自动调整大小以适合其内容。 对于不会正确调整大小的基于文本的视图，在 Xcode 的 Interface Builder 中选择它们，然后选择**编辑** > **大小到内容适应**。
- **将前导和尾随属性应用**-因为中文本的方向可以更改基于用户的语言，使用新`Leading`并`Trailing`约束属性而不是现有`Right`和`Left`特性。 `Leading` 和`Trailing`会自动调整根据语言方向。
- **Pin 视图到相邻视图**-这样，若要重新定位并调整其周围的视图更改为所选的语言的响应中的视图。
- **未设置 Windows 最小值和/或最大大小**-允许 Windows 根据所选的语言调整他们的内容区域的大小更改大小。
- **布局不断地更改测试**-期间应在不同的语言中不断地测试应用程序开发。 请参阅 Apple[测试您国际化应用](https://developer.apple.com/library/content/documentation/MacOSX/Conceptual/BPInternational/TestingYourInternationalApp/TestingYourInternationalApp.html#//apple_ref/doc/uid/10000171i-CH7-SW1)文档了解详细信息。
- **使用到一起 Pin 视图 NSStackViews**  -  `NSStackViews`允许它们的内容以便按住 shift 并在可预测的方式发展和内容更改大小根据所选的语言。

<a name="Localizing-in-Xcodes-Interface-Builder" />

### <a name="localizing-in-xcodes-interface-builder"></a>在 Xcode 的 Interface Builder 中本地化

Apple 提供了 Xcode 的 Interface Builder 中的许多功能，开发人员可用于设计或编辑应用程序的 UI 时支持的本地化。 **文本方向**一部分**属性检查器**允许开发人员提供有关如何使用和更新在选择基于文本的视图方向的提示 (如`NSTextField`):

[![](modern-cocoa-apps-images/content10.png "文本方向选项")](modern-cocoa-apps-images/content10.png#lightbox)

有三个可能值为**文本方向**:

- **自然**-布局基于分配给控件的字符串。
- **从左到右**-布局始终强制为从左到右。
- **从右到左**-布局始终强制为从右到左。

有两个可能值**布局**:

- **从左到右**-布局始终从左到右。
- **从右到左**-布局始终是从右到左。

通常这些不应更改除非需要特定的对齐方式。

**镜像**属性通知系统 （如单元格图像位置） 切换特定的控件属性。 它具有三个可能值：

- **自动**-的位置将自动更改基于所选的语言的方向。
- **在右到左界面**-仅将更改从右到左的基于语言中的位置。
- **永远不会**-的位置将永远不会更改。

如果已指定开发人员**Center**， **Justify**或**完整**对齐方式的内容的基于文本的视图，这些将永远不会为翻转所选的基于的语言。

在 macOS Sierra 之前, 在代码中创建的控件将不能镜像自动。 开发人员必须使用如下所示的代码来处理镜像：

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Setting a button's mirroring based on the layout direction
    var button = new NSButton ();
    if (button.UserInterfaceLayoutDirection == NSUserInterfaceLayoutDirection.LeftToRight) {
        button.Alignment = NSTextAlignment.Right;
        button.ImagePosition = NSCellImagePosition.ImageLeft;
    } else {
        button.Alignment = NSTextAlignment.Left;
        button.ImagePosition = NSCellImagePosition.ImageRight;
    }
}
```

其中`Alignment`并`ImagePosition`正在设置根据`UserInterfaceLayoutDirection`的控件。

macOS Sierra 添加多个新的便利构造函数 (通过静态`CreateButton`方法) 采用多个参数 （如标题、 图像和操作） 并将自动正确镜像。 例如：

```csharp
var button2 = NSButton.CreateButton (myTitle, myImage, () => {
    // Take action when the button is pressed
    ...
});
```

<a name="Using-System-Appearances" />

## <a name="using-system-appearances"></a>使用系统的外观

新式 macOS 应用可以采用非常适用于图像创建、 编辑或演示文稿应用新的深界面外观：

[![](modern-cocoa-apps-images/content11.png "深的 Mac 窗口 UI 的示例")](modern-cocoa-apps-images/content11.png#lightbox)

这可以通过显示窗口之前将添加一行代码。 例如：

```csharp
using System;
using AppKit;
using Foundation;

namespace MacModern
{
    public partial class ViewController : NSViewController
    {
        ...
    
        #region Override Methods
        public override void ViewWillAppear ()
        {
            base.ViewWillAppear ();

            // Apply the Dark Interface Appearance
            View.Window.Appearance = NSAppearance.GetAppearance (NSAppearance.NameVibrantDark);

            ...
        }
        #endregion
    }
}
```

静态`GetAppearance`方法`NSAppearance`类用于获取来自系统的已命名的外观 (在这种情况下`NSAppearance.NameVibrantDark`)。

Apple 具有以下建议以获得使用系统的外观：

- 为首硬编码值的已命名的颜色 (如`LabelColor`和`SelectedControlColor`)。
- 在可能的情况，请使用系统标准控件样式。

使用系统外观的 macOS 应用将自动为用户启用了从系统首选项应用的辅助功能正常工作。 因此，Apple 提供建议，开发人员始终应在其 macOS 应用中使用系统的外观。

<a name="Designing-UIs-with-Storyboards" />

## <a name="designing-uis-with-storyboards"></a>使用演示图板的设计 Ui

情节提要允许开发人员不仅的各个元素，从而使应用程序的用户界面，但可视化和设计 UI 流的设计和给定元素的层次结构。

控制器允许开发人员元素收集到的操作组合和 segues 设计抽象单位并删除典型"粘附代码"需要以移动整个视图层次结构：

[![](modern-cocoa-apps-images/content12.png "编辑 Xcode 的 Interface Builder 中的用户界面")](modern-cocoa-apps-images/content12.png#lightbox)

有关详细信息，请参阅我们[情节提要简介](~/mac/platform/storyboards/index.md)文档。

很多情况下在情节提要中定义的给定的场景需要视图层次结构中的上一个场景中的数据。 Apple 具有场景之间传递信息的以下建议：

- 数据依赖事项应始终通过层次结构向下层叠。
- 避免进行硬编码 UI 结构依赖事项，因为这就限制了灵活的 UI。
- 使用C#接口来提供通用数据相关性。

充当源的 Segue，视图控制器可以重写`PrepareForSegue`执行方法，并执行操作前 Segue （如传递数据） 所需的任何初始化，以显示目标视图控制器。 例如：

```csharp
public override void PrepareForSegue (NSStoryboardSegue segue, NSObject sender)
{
    base.PrepareForSegue (segue, sender);

    // Take action based on Segue ID
    switch (segue.Identifier) {
    case "MyNamedSegue":
        // Prepare for the segue to happen
        ...
        break;
    }
}
```

有关详细信息，请参阅我们[segues 设计](~/mac/platform/storyboards/indepth.md#Segues)文档。

<a name="Propagating-Actions" />

## <a name="propagating-actions"></a>传播操作

根据 macOS 应用的设计，可能有的时间时 UI 控件上的某个操作的最佳处理程序可能在其他位置 UI 层次结构中。 这种情况的菜单和位于其自己的场景中，应用程序的 UI 的其余部分隔离的菜单项。

若要处理这种情况下，开发人员可以创建自定义操作和传递操作的响应方链向上。 有关详细信息请参阅我们[使用自定义窗口操作](~/mac/user-interface/menu.md)文档。

<a name="Modern-Mac-Features" />

## <a name="modern-mac-features"></a>现代 Mac 功能

Apple 已包含多个面向用户的功能在 macOS Sierra，允许开发人员充分利用 Mac 平台中，如：

- **NSUserActivity** -这可让应用来描述用户当前参与的活动。 `NSUserActivity` 最初创建以支持的移交，无法提取并继续在另一台设备上启动其中一个用户的设备上的活动。 `NSUserActivity` 在 iOS 中的 macOS 作为它在相同作用的工作原理，因此请参阅我们[简介切换](~/ios/platform/handoff.md)iOS 文档了解详细信息。
- **在 Mac 上的使用 Siri** -Siri 使用当前的活动 (`NSUserActivity`) 提供的用户可以发出的命令的上下文。
- **状态还原**-当用户退出应用程序在 macOS 上，并稍后 relaunches 它，该应用将自动返回到其以前的状态。 开发人员可以使用状态还原 API 进行编码，并向用户显示用户界面之前还原瞬时 UI 状态。 如果应用`NSDocument`基于，状态还原自动处理。 若要启用状态还原为非`NSDocument`的应用，设置`Restorable`的`NSWindow`类来`true`。
- **云中的文档**-在 macOS Sierra 之前应用程序必须显式选择项以在用户的 iCloud 驱动器中处理文档。 在 macOS Sierra 用户**桌面**并**文档**文件夹可能会与同步其 iCloud 驱动器自动系统。 因此，文档的本地副本可能会删除以释放用户的计算机上的空间。 `NSDocument` 基于应用程序将自动处理此更改。 所有其他应用类型将需要使用`NSFileCoordinator`同步读取和写入的文档。

<a name="Summary" />

## <a name="summary"></a>总结

本文只讨论了几个技巧、 功能和技术开发人员可以使用生成新式 macOS 应用在 Xamarin.Mac 中。



## <a name="related-links"></a>相关链接

- [macOS 示例](https://developer.xamarin.com/samples/mac/)
