---
title: Xamarin.iOS 中的三维点触控简介
description: 本文介绍如何使用三维触控笔势引入了 iPhone 6s 和 iPhone 6s Plus。 这些笔势启用压力敏感度、 查看和 pop 和快速操作。
ms.prod: xamarin
ms.assetid: 806D051E-3791-40F7-9776-4E4D3E56F7F3
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: 6a12d157b3de7c3841f5d69d209c01fbc612f79b
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57668733"
---
# <a name="introduction-to-3d-touch-in-xamarinios"></a>Xamarin.iOS 中的三维点触控简介

_本文介绍如何使用新 iPhone 6s 和 iPhone 6s Plus 3D Touch 应用程序中的笔势。_

[![](3d-touch-images/info01.png "3D Touch 的示例启用的应用")](3d-touch-images/info01.png#lightbox)

本文将提供并介绍如何使用新的 3D Touch Api 将压力敏感手势添加到 Xamarin.iOS 应用运行在新 iPhone 6s 和 iPhone 6s Plus 设备。

3D Touch 与 iPhone 应用现可不仅告诉用户触摸设备的屏幕上，但就可以感测多少用户施加的压力和响应不同压力级别。

3D Touch 提供到您的应用程序的以下功能：

- [压力敏感度](#Pressure-Sensitivity)-现在可以难测量应用程序或 light 用户触摸屏幕并且希望利用该信息。
  例如，绘画应用程序可以使之更宽或更薄根据用户难触摸屏幕的行。
- [查看和弹出](#Peek-and-Pop)-您的应用程序现在可以让用户使用其数据而无需导航从其当前上下文交互。 通过按硬屏幕在屏幕上，他们可以查看其感兴趣 （例如预览一条消息） 的项。 通过按更难，它们可进入该项目。
- [快速操作](#Quick-Actions)-认为的快速操作，例如，可以将弹出向上时在用户右键单击桌面应用程序中的项的上下文菜单。
  使用快速操作，您可以添加快捷方式到函数应用程序中直接从主页屏幕上的应用图标。
- [在模拟器中测试 3D Touch](#Testing-3D-Touch-in-the-Simulator) -使用正确的 Mac 硬件，可以在 iOS 模拟器中测试三维触控启用应用程序。

<a name="Pressure-Sensitivity" />

## <a name="pressure-sensitivity"></a>压力敏感度

如上所述，通过使用新属性[UITouch](xref:UIKit.UITouch)类可以测量的用户将应用到 iOS 设备的屏幕的压力，并在用户界面中使用此信息。 例如，画笔笔画更透明或不透明基于上进行的压力。

[![](3d-touch-images/pressure01.png "呈现为更透明或不透明画笔笔画根据的压力量")](3d-touch-images/pressure01.png#lightbox)

由于 3D Touch，而如果 iOS 9 （或更高版本） 上运行你的应用和 iOS 设备是否能够支持 3D Touch 压力中的更改将导致`TouchesMoved`事件被引发。

例如，当监视`TouchesMoved`的事件[UIView](xref:UIKit.UIView)，可以使用以下代码以获取该用户将应用于屏幕的当前压力：

```csharp
public override void TouchesMoved (NSSet touches, UIEvent evt)
{
    base.TouchesMoved (touches, evt);
    UITouch touch = touches.AnyObject as UITouch;
    if (touch != null)
    {
        // Get the pressure
        var force = touch.Force;
        var maxForce = touch.MaximumPossibleForce;

        // Do something with the touch and the pressure
        ...
    }
}
```

`MaximumPossibleForce`属性返回的最大可能值`Force`的属性[UITouch](xref:UIKit.UITouch)基于 iOS 设备上运行应用。

> [!IMPORTANT]
> 压力中的更改将导致`TouchesMoved`事件被引发，即使 X / Y 坐标未发生更改。 由于此更改的行为，应为准备 iOS 应用`TouchesMoved`事件将调用频率和 x / Y 坐标与上一相同`TouchesMoved`调用。




有关详细信息，请参阅 Apple 的[TouchCanvas:高效且有效地使用 UITouch](https://developer.apple.com/library/prerelease/ios/samplecode/TouchCanvas/)示例应用程序和[UITouch 类引用](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UITouch_Class/)。

<a name="Peek-and-Pop" />

## <a name="peek-and-pop"></a>查看和 Pop

3D Touch 提供了用户可以与您比以往任何时候，更快的应用程序中的信息而无需导航从其当前位置进行交互的新方法。

例如，如果您的应用程序显示的消息表，用户可以按硬上要预览其内容的覆盖层视图中的项 (它是 Apple 指作为*扫视*)。

[![](3d-touch-images/peekandpop01.png "举例说明如何查看在内容")](3d-touch-images/peekandpop01.png#lightbox)

如果用户按更困难，需要输入常规消息视图 (这称为*弹出*-ping 到视图)。

### <a name="checking-for-3d-touch-availability"></a>3D Touch 可用性检查

使用时`UIViewController`可以使用以下代码以查看 iOS 设备运行该应用支持 3D Touch:

```csharp
public override void TraitCollectionDidChange(UITraitCollection previousTraitCollection)
{
    //Important: call the base function
    base.TraitCollectionDidChange(previousTraitCollection);

    //See if the new TraitCollection value includes force touch
    if (TraitCollection.ForceTouchCapability == UIForceTouchCapability.Available) {
        //Do something with 3D touch, for instance...
        RegisterForPreviewingWithDelegate (this, View);
        ...
```

可能之前调用此方法*或之后* `ViewDidLoad()`。

### <a name="handling-peek-and-pop"></a>处理扫视和 Pop

在可以处理 3D Touch 的 iOS 设备，我们可以使用的实例`UIViewControllerPreviewingDelegate`类来处理的显示**扫视**和**弹出**项详细信息。 例如，如果我们的表格视图控制器称为`MasterViewController `我们可以使用以下代码以支持**扫视**并**弹出**:

```csharp
using System;
using System.Collections.Generic;
using UIKit;
using Foundation;
using CoreGraphics;

namespace DTouch
{
    public class PreviewingDelegate : UIViewControllerPreviewingDelegate
    {
        #region Computed Properties
        public MasterViewController MasterController { get; set; }
        #endregion

        #region Constructors
        public PreviewingDelegate (MasterViewController masterController)
        {
            // Initialize
            this.MasterController = masterController;
        }

        public PreviewingDelegate (NSObjectFlag t) : base(t)
        {
        }

        public PreviewingDelegate (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        /// Present the view controller for the "Pop" action.
        public override void CommitViewController (IUIViewControllerPreviewing previewingContext, UIViewController viewControllerToCommit)
        {
            // Reuse Peek view controller for details presentation
            MasterController.ShowViewController(viewControllerToCommit,this);
        }

        /// Create a previewing view controller to be shown at "Peek".
        public override UIViewController GetViewControllerForPreview (IUIViewControllerPreviewing previewingContext, CGPoint location)
        {
            // Grab the item to preview
            var indexPath = MasterController.TableView.IndexPathForRowAtPoint (location);
            var cell = MasterController.TableView.CellAt (indexPath);
            var item = MasterController.dataSource.Objects [indexPath.Row];

            // Grab a controller and set it to the default sizes
            var detailViewController = MasterController.Storyboard.InstantiateViewController ("DetailViewController") as DetailViewController;
            detailViewController.PreferredContentSize = new CGSize (0, 0);

            // Set the data for the display
            detailViewController.SetDetailItem (item);
            detailViewController.NavigationItem.LeftBarButtonItem = MasterController.SplitViewController.DisplayModeButtonItem;
            detailViewController.NavigationItem.LeftItemsSupplementBackButton = true;

            // Set the source rect to the cell frame, so everything else is blurred.
            previewingContext.SourceRect = cell.Frame;

            return detailViewController;
        }
        #endregion
    }
}
```

`GetViewControllerForPreview`方法用于执行**扫视**操作。 它可获取对表的单元格和备份数据的访问权限，然后加载`DetailViewController`从当前情节提要。 通过设置`PreferredContentSize`(0，0) 中，我们会要求默认值**扫视**视图大小。 最后，我们模糊我们显示与该单元格以外的所有内容`previewingContext.SourceRect = cell.Frame`和我们返回新视图，以显示。

`CommitViewController`重复使用我们在中创建的视图**扫视**有关**弹出**查看当用户按更难。

### <a name="registering-for-peek-and-pop"></a>查看和 Pop 注册

我们想要允许用户与视图控制器**扫视**并**弹出**中的项，我们需要注册此服务。 在上述示例中的表格视图控制器 (`MasterViewController`)，我们将使用以下代码：

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Check to see if 3D Touch is available
    if (TraitCollection.ForceTouchCapability == UIForceTouchCapability.Available) {
        // Register for Peek and Pop
        RegisterForPreviewingWithDelegate(new PreviewingDelegate(this), View);
    }
    ...

}
```

此处，我们将调用`RegisterForPreviewingWithDelegate`的实例方法`PreviewingDelegate`我们在上面创建。 在支持 3D Touch 的 iOS 设备，用户可以按硬上快速地概览在它的项。 如果他们甚至很难按，该项将弹入其标准显示视图。

有关详细信息，请参阅我们[iOS 9 ApplicationShortcuts 示例](https://developer.xamarin.com/samples/monotouch/iOS9/ViewControllerPreview/)和 Apple 的[ViewControllerPreviews:使用预览 Api UIViewController](https://developer.apple.com/library/prerelease/ios/samplecode/ViewControllerPreviews/Introduction/Intro.html)示例应用[UIPreviewAction 类引用](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewAction_Class/)， [UIPreviewActionGroup 类引用](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewActionGroup_Class/)和[UIPreviewActionItem协议参考](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewActionItem_Protocol/)。

<a name="Quick-Actions" />

## <a name="quick-actions"></a>快速操作

使用 3D Touch 和快速操作，您可以添加常见、 快速且简单到函数的快捷方式访问应用程序中从 iOS 设备上的主页屏幕图标。

如上面所述，可以像上下文菜单，可以将弹出向上用户右键单击桌面应用程序中的项时一样思考的快速操作。 您应使用快速操作提供的最常见的函数或您的应用程序的功能的快捷方式。

[![](3d-touch-images/quickactions01.png "快速操作菜单的一个示例")](3d-touch-images/quickactions01.png#lightbox)


### <a name="defining-static-quick-actions"></a>定义静态快速操作

如果一个或多个应用所需的快速操作是静态的不需要更改，您可以在应用中定义它们`Info.plist`文件。 编辑外部编辑器中的此文件并添加以下项：

```xml
<key>UIApplicationShortcutItems</key>
<array>
    <dict>
        <key>UIApplicationShortcutItemIconType</key>
        <string>UIApplicationShortcutIconTypeSearch</string>
        <key>UIApplicationShortcutItemSubtitle</key>
        <string>Will search for an item</string>
        <key>UIApplicationShortcutItemTitle</key>
        <string>Search</string>
        <key>UIApplicationShortcutItemType</key>
        <string>com.company.appname.000</string>
    </dict>
    <dict>
        <key>UIApplicationShortcutItemIconType</key>
        <string>UIApplicationShortcutIconTypeShare</string>
        <key>UIApplicationShortcutItemSubtitle</key>
        <string>Will share an item</string>
        <key>UIApplicationShortcutItemTitle</key>
        <string>Share</string>
        <key>UIApplicationShortcutItemType</key>
        <string>com.company.appname.001</string>
    </dict>
</array>
```

此处我们使用以下密钥定义两个静态快速操作项：

- `UIApplicationShortcutItemIconType` -定义将通过快速操作项显示为以下值之一的图标：
  - `UIApplicationShortcutIconTypeAdd`
  - `UIApplicationShortcutIconTypeAlarm`
  - `UIApplicationShortcutIconTypeAudio`
  - `UIApplicationShortcutIconTypeBookmark`
  - `UIApplicationShortcutIconTypeCapturePhoto`
  - `UIApplicationShortcutIconTypeCaptureVideo`
  - `UIApplicationShortcutIconTypeCloud`
  - `UIApplicationShortcutIconTypeCompose`
  - `UIApplicationShortcutIconTypeConfirmation`
  - `UIApplicationShortcutIconTypeContact`
  - `UIApplicationShortcutIconTypeDate`
  - `UIApplicationShortcutIconTypeFavorite`
  - `UIApplicationShortcutIconTypeHome`
  - `UIApplicationShortcutIconTypeInvitation`
  - `UIApplicationShortcutIconTypeLocation`
  - `UIApplicationShortcutIconTypeLove`
  - `UIApplicationShortcutIconTypeMail`
  - `UIApplicationShortcutIconTypeMarkLocation`
  - `UIApplicationShortcutIconTypeMessage`
  - `UIApplicationShortcutIconTypePause`
  - `UIApplicationShortcutIconTypePlay`
  - `UIApplicationShortcutIconTypeProhibit`
  - `UIApplicationShortcutIconTypeSearch`
  - `UIApplicationShortcutIconTypeShare`
  - `UIApplicationShortcutIconTypeShuffle`
  - `UIApplicationShortcutIconTypeTask`
  - `UIApplicationShortcutIconTypeTaskCompleted`
  - `UIApplicationShortcutIconTypeTime`
  - `UIApplicationShortcutIconTypeUpdate`

        ![](3d-touch-images/uiapplicationshortcuticontype.png "UIApplicationShortcutIconType imagery")

* `UIApplicationShortcutItemSubtitle` -定义项的副标题。
* `UIApplicationShortcutItemTitle` -定义项的标题。
* `UIApplicationShortcutItemType` -是一个字符串值，我们将使用来确定我们的应用程序中的项。 有关详细信息，请参阅下一节。

> [!IMPORTANT]
> 在中设置的快速操作快捷方式项`Info.plist`不能与访问文件`Application.ShortcutItems`属性。 它们仅传入到`HandleShortcutItem`事件处理程序。





### <a name="identifying-quick-action-items"></a>标识快速操作项

如上面所见，当您快速操作项中定义您的应用程序`Info.plist`，分配到一个字符串值`UIApplicationShortcutItemType`键来标识它们。

若要更轻松地在代码中使用这些标识符，将添加名为的类`ShortcutIdentifier`到应用程序的项目，然后使其看起来如下所示：

```csharp
using System;

namespace AppSearch
{
    public static class ShortcutIdentifier
    {
        public const string First = "com.company.appname.000";
        public const string Second = "com.company.appname.001";
        public const string Third = "com.company.appname.002";
        public const string Fourth = "com.company.appname.003";
    }
}
```

<a name="Handling-a-Quick-Action" />

### <a name="handling-a-quick-action"></a>处理的快速操作

接下来，您需要修改应用的`AppDelegate.cs`文件，以处理从主屏幕上的应用程序的图标中选择一个快速操作项的用户。

进行以下编辑：

```csharp
using System;
...

public UIApplicationShortcutItem LaunchedShortcutItem { get; set; }

public bool HandleShortcutItem(UIApplicationShortcutItem shortcutItem) {
    var handled = false;

    // Anything to process?
    if (shortcutItem == null) return false;

    // Take action based on the shortcut type
    switch (shortcutItem.Type) {
    case ShortcutIdentifier.First:
        Console.WriteLine ("First shortcut selected");
        handled = true;
        break;
    case ShortcutIdentifier.Second:
        Console.WriteLine ("Second shortcut selected");
        handled = true;
        break;
    case ShortcutIdentifier.Third:
        Console.WriteLine ("Third shortcut selected");
        handled = true;
        break;
    case ShortcutIdentifier.Fourth:
        Console.WriteLine ("Forth shortcut selected");
        handled = true;
        break;
    }

    // Return results
    return handled;
}

public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
{
    var shouldPerformAdditionalDelegateHandling = true;

    // Get possible shortcut item
    if (launchOptions != null) {
        LaunchedShortcutItem = launchOptions [UIApplication.LaunchOptionsShortcutItemKey] as UIApplicationShortcutItem;
        shouldPerformAdditionalDelegateHandling = (LaunchedShortcutItem == null);
    }

    return shouldPerformAdditionalDelegateHandling;
}

public override void OnActivated (UIApplication application)
{
    // Handle any shortcut item being selected
    HandleShortcutItem(LaunchedShortcutItem);

    // Clear shortcut after it's been handled
    LaunchedShortcutItem = null;
}

public override void PerformActionForShortcutItem (UIApplication application, UIApplicationShortcutItem shortcutItem, UIOperationHandler completionHandler)
{
    // Perform action
    completionHandler(HandleShortcutItem(shortcutItem));
}
```

首先，我们定义一个公共`LaunchedShortcutItem`属性来跟踪用户所选的最后一个快速操作项。 然后，我们重写`FinishedLaunching`方法，请参阅如果`launchOptions`已通过，如果它们包含快速操作项。 如果是这样，我们将存储中的快速操作`LaunchedShortcutItem`属性。

接下来，我们重写`OnActivated`方法并传递到快速启动项选择任何`HandleShortcutItem`方法来处理。 目前我们仅写入一条消息**控制台**。 在实际应用中，将处理所需的任何操作。 执行操作后，`LaunchedShortcutItem`清除属性。

最后，如果您的应用程序正在运行，则`PerformActionForShortcutItem`会调用方法来处理的快速操作项，因此我们需要将其重写并调用我们`HandleShortcutItem`方法还有。


### <a name="creating-dynamic-quick-action-items"></a>创建动态的快速操作项

除了定义静态快速操作中的项应用的`Info.plist`文件中，您可以创建动态上动态快速操作。 若要定义两个新的动态快速操作，编辑你`AppDelegate.cs`再次文件并修改`FinishedLaunching`方法看起来如下所示：

```csharp
public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
{
    var shouldPerformAdditionalDelegateHandling = true;

    // Get possible shortcut item
    if (launchOptions != null) {
        LaunchedShortcutItem = launchOptions [UIApplication.LaunchOptionsShortcutItemKey] as UIApplicationShortcutItem;
        shouldPerformAdditionalDelegateHandling = (LaunchedShortcutItem == null);
    }

    // Add dynamic shortcut items
    if (application.ShortcutItems.Length == 0) {
        var shortcut3 = new UIMutableApplicationShortcutItem (ShortcutIdentifier.Third, "Play") {
            LocalizedSubtitle = "Will play an item",
            Icon = UIApplicationShortcutIcon.FromType(UIApplicationShortcutIconType.Play)
        };

        var shortcut4 = new UIMutableApplicationShortcutItem (ShortcutIdentifier.Fourth, "Pause") {
            LocalizedSubtitle = "Will pause an item",
            Icon = UIApplicationShortcutIcon.FromType(UIApplicationShortcutIconType.Pause)
        };

        // Update the application providing the initial 'dynamic' shortcut items.
        application.ShortcutItems = new UIApplicationShortcutItem[]{shortcut3, shortcut4};
    }

    return shouldPerformAdditionalDelegateHandling;
}
```

现在，我们会检查以查看是否`application`动态创建的则已包含的一组`ShortcutItems`，如果不是，我们将创建两个新`UIMutableApplicationShortcutItem`对象以定义新项并将其添加到`ShortcutItems`数组。

我们已中添加代码[处理快速操作](#Handling-a-Quick-Action)上面一节将处理这些动态快速操作，就像静态的。

应注意，可以创建混合使用静态和动态快速操作项 （如我们在这里），您并不局限于一个或另一个。

有关详细信息，请我们[iOS 9 ViewControllerPreview 示例](https://developer.xamarin.com/samples/monotouch/iOS9/ViewControllerPreview/)，请参阅 Apple 的[ApplicationShortcuts:使用 UIApplicationShortcutItem](https://developer.apple.com/library/prerelease/ios/samplecode/ApplicationShortcuts/)示例应用[UIApplicationShortcutItem 类引用](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationShortcutItem_class/)， [UIMutableApplicationShortcutItem 类引用](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIMutableApplicationShortcutItem_class/)和[UIApplicationShortcutIcon 类引用](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationShortcutIcon_Class/)。

<a name="Testing-3D-Touch-in-the-Simulator" />

## <a name="testing-3d-touch-in-the-simulator"></a>在模拟器中测试 3D Touch

使用 Force Touch 兼容 Mac 上使用 Xcode 和 iOS 模拟器的最新版本启用触控板，可以在模拟器中测试 3D 触摸功能。

若要启用此功能，在支持 3D Touch 的模拟的 iPhone 硬件运行任何应用 (iPhone 6s 及更高版本)。 接下来，选择**硬件**在 iOS 模拟器和启用菜单**三维触控使用触控板强制**菜单项：

[![](3d-touch-images/simulator01.png "在 iOS 模拟器中选择硬件菜单上，启用 3D touch 菜单项使用触控板强制")](3d-touch-images/simulator01.png#lightbox)

使用此活动的功能，您可以按更难 Mac 的触控板上启用三维触控，就像实际的 iPhone 硬件上。

## <a name="summary"></a>总结

本文已介绍了新 3D Touch 的 Api 可在 iOS 9 iPhone 6s 和 iPhone 6s Plus。 其中包括如何添加到应用程序; 压力敏感度使用扫视和 Pop 快速显示而无需导航; 当前上下文中的应用程序内信息并使用快速操作提供您的应用程序的快捷方式的最常用的功能。



## <a name="related-links"></a>相关链接

- [iOS 9 ViewControllerPreview 示例](https://developer.xamarin.com/samples/monotouch/ios9/ViewControllerPreview/)
- [iOS 9 ApplicationShortcuts 示例](https://developer.xamarin.com/samples/monotouch/ios9/ApplicationShortcuts/)
- [面向开发人员的 iOS 9](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [获取你的 iPhone 应用准备好进行 3D Touch](https://developer.apple.com/ios/3d-touch/)
