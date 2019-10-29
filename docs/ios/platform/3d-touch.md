---
title: Xamarin 中的3D 触控简介
description: 本文介绍如何使用 iPhone 6s 和 iPhone 6s Plus 引入的3D 触摸手势。 这些手势可实现压力敏感度、速览和 pop 以及快速操作。
ms.prod: xamarin
ms.assetid: 806D051E-3791-40F7-9776-4E4D3E56F7F3
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: d6dec70d30929fdc545bf2ee9107297f1ed8d346
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73022262"
---
# <a name="introduction-to-3d-touch-in-xamarinios"></a>Xamarin 中的3D 触控简介

_本文介绍如何在应用中使用新的 iPhone 6s 和 iPhone 6s 以及3D 触摸手势。_

[![](3d-touch-images/info01.png "Examples of 3D Touch enabled apps")](3d-touch-images/info01.png#lightbox)

本文将介绍如何使用新的 3D Touch Api 向在新的 iPhone 6s 和 iPhone 6s Plus 设备上运行的 Xamarin 应用程序添加压力敏感度。

使用3D 触控，iPhone 应用现在不仅可以知道用户正在触摸设备的屏幕，还可以判断用户施加的压力，并响应不同的压力级别。

3D Touch 为你的应用提供以下功能：

- [压力敏感度](#Pressure-Sensitivity)-应用现在可以测量用户在屏幕上的接触速度，并利用这些信息。
  例如，绘制应用可以根据用户对屏幕的接触情况，使线条更粗或更细。
- "[速览" 和 "弹出](#Peek-and-Pop)"-应用现在可以让用户与其数据交互，而无需导航到当前上下文。 通过在屏幕上按 "硬"，可以查看他们感兴趣的项目（例如预览邮件）。 通过按更难，他们可以弹出项目。
- "[快速操作](#Quick-Actions)"-当用户在桌面应用中右键单击某一项时，可以使用快捷菜单，如可以弹出的上下文菜单。
  使用 "快速操作"，可以直接从主屏幕上的应用程序图标添加应用程序中函数的快捷方式。
- [在模拟器中测试3D 触摸](#Testing-3D-Touch-in-the-Simulator)-通过正确的 Mac 硬件，你可以在 iOS 模拟器中测试已启用 3d touch 的应用。

<a name="Pressure-Sensitivity" />

## <a name="pressure-sensitivity"></a>压力敏感度

如上所述，通过使用[UITouch](xref:UIKit.UITouch)类的新属性，可以测量用户应用到 iOS 设备屏幕的压力量，并在用户界面中使用此信息。 例如，根据压力量使画笔笔划更半透明或不透明。

[![](3d-touch-images/pressure01.png "A brush stroke rendered as more translucent or opaque based on the amount of pressure")](3d-touch-images/pressure01.png#lightbox)

3D 触摸的结果是，如果你的应用在 iOS 9 （或更高版本）上运行，并且 iOS 设备能够支持3D 触摸，则压力改变将导致引发 `TouchesMoved` 事件。

例如，监视[UIView](xref:UIKit.UIView)的 `TouchesMoved` 事件时，可以使用以下代码来获取用户应用到屏幕的当前压力：

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

`MaximumPossibleForce`属性根据运行应用的 iOS 设备返回 [UITouch](xref:UIKit.UITouch) 的`Force`属性的最大可能值。

> [!IMPORTANT]
> 压力更改将导致引发 `TouchesMoved` 事件，即使 X/Y 坐标尚未更改也是如此。 由于这种行为发生了更改，因此，应该为你的 iOS 应用做好准备，以便更频繁地调用 `TouchesMoved` 事件，并且 X/Y 坐标与最后 `TouchesMoved` 调用相同。

有关详细信息，请参阅 Apple 的 [TouchCanvas：有效地使用 UITouch](https://developer.apple.com/library/prerelease/ios/samplecode/TouchCanvas/) 示例应用和[UITouch 类引用](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UITouch_Class/)。

<a name="Peek-and-Pop" />

## <a name="peek-and-pop"></a>速览和 Pop

3D Touch 提供了新的方法，使用户能够比以往更快地与应用程序中的信息交互，而不必从当前位置导航。

例如，如果你的应用程序显示的是一张消息，则用户可以按下某个项目的 "hard" 来预览覆盖视图（Apple*指的是查看）中*的内容。

[![](3d-touch-images/peekandpop01.png "An example of Peeking at content")](3d-touch-images/peekandpop01.png#lightbox)

如果用户按 "困难"，他们将进入常规消息视图（这在视图中称为 "*弹出*ping"）。

### <a name="checking-for-3d-touch-availability"></a>检查3D 触摸可用性

使用 `UIViewController` 时，可以使用以下代码来查看运行该应用的 iOS 设备是否支持3D 触摸：

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

此方法可在 `ViewDidLoad()`之前*或之后*调用。

### <a name="handling-peek-and-pop"></a>处理速览和 Pop

在可以处理三维触控的 iOS 设备上，我们可以使用 `UIViewControllerPreviewingDelegate` 类的实例来处理**速览**和**Pop**项详细信息的显示。 例如，如果有一个名为 `MasterViewController` 的表视图控制器，则可以使用以下代码来支持**速览**和**Pop**：

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

`GetViewControllerForPreview` 方法用于执行**查看**操作。 它可以访问表单元和备份数据，然后从当前情节提要加载 `DetailViewController`。 通过将 `PreferredContentSize` 设置为（0，0），我们要求提供默认的**速览**视图大小。 最后，我们将在 `previewingContext.SourceRect = cell.Frame` 显示的所有内容变得模糊，并返回新的显示视图。

当用户按**下 "更**难" 时，`CommitViewController` 重用在 "查看 **" 视图中**创建的视图。

### <a name="registering-for-peek-and-pop"></a>注册速览和 Pop

从我们希望允许用户**查看**和**弹出**项目的视图控制器，我们需要注册此服务。 在上面给定的表视图控制器（`MasterViewController`）的示例中，我们将使用以下代码：

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

这里，我们将使用我们在上面创建的 `PreviewingDelegate` 的实例调用 `RegisterForPreviewingWithDelegate` 方法。 在支持3D 触摸的 iOS 设备上，用户可以按下某个项目的 hard 来查看它。 如果他们按更难，此项将弹出到它的 "标准显示" 视图中。

有关详细信息，请参阅[iOS 9 ApplicationShortcuts 示例](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-viewcontrollerpreview)和 Apple [ViewControllerPreviews：使用 UIViewController 预览 Api](https://developer.apple.com/library/prerelease/ios/samplecode/ViewControllerPreviews/Introduction/Intro.html) 示例应用、 [UIPreviewAction 类引用](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewAction_Class/)、 [UIPreviewActionGroup 类引用](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewActionGroup_Class/)和[UIPreviewActionItem 协议引用](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewActionItem_Protocol/)。

<a name="Quick-Actions" />

## <a name="quick-actions"></a>快速操作

使用3D 触摸和快速操作，你可以通过 iOS 设备上的主屏幕图标从你的应用程序中添加常用、快速且易于访问的功能的快捷方式。

如上所述，可以考虑在用户右键单击桌面应用程序中的某一项时，可以弹出的上下文菜单等快速操作。 您应该使用 "快速操作" 提供应用程序的最常见函数或功能的快捷方式。

[![](3d-touch-images/quickactions01.png "An example of a Quick Actions menu")](3d-touch-images/quickactions01.png#lightbox)

### <a name="defining-static-quick-actions"></a>定义静态快速操作

如果你的应用程序所需的一个或多个快速操作是静态的，并且无需更改，你可以在应用的 `Info.plist` 文件中定义它们。 在外部编辑器中编辑此文件并添加以下项：

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

此处，我们将定义两个静态快速操作项，其中包含以下项：

- `UIApplicationShortcutItemIconType`-将 "快速操作" 项显示的图标定义为以下值之一：
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

- `UIApplicationShortcutItemSubtitle`-定义项的副标题。
- `UIApplicationShortcutItemTitle`-定义项的标题。
- `UIApplicationShortcutItemType`-是一个字符串值，我们将使用它来确定应用程序中的项。 有关详细信息，请参阅下一节。

> [!IMPORTANT]
> 无法通过 `Application.ShortcutItems` 属性访问在 `Info.plist` 文件中设置的快速操作快捷方式项。 它们只传递到 `HandleShortcutItem` 事件处理程序。

### <a name="identifying-quick-action-items"></a>正在识别快速操作项

如上所述，当你在应用的 `Info.plist`中定义了快速操作项时，将向 `UIApplicationShortcutItemType` 键分配一个字符串值以标识它们。

若要使这些标识符更易于在代码中使用，请将名为 `ShortcutIdentifier` 的类添加到应用的项目中，使其类似于以下内容：

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

### <a name="handling-a-quick-action"></a>处理快速操作

接下来，需要修改应用的 `AppDelegate.cs` 文件，以处理用户从主屏幕上应用的图标中选择 "快速操作" 项。

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

首先，我们定义一个公共 `LaunchedShortcutItem` 属性以跟踪用户最后选定的 "快速操作" 项。 然后，重写 `FinishedLaunching` 方法，并查看 `launchOptions` 是否已传递以及是否包含 "快速操作" 项。 如果是这样，请在 "`LaunchedShortcutItem`" 属性中存储 "快速操作"。

接下来，我们将重写 `OnActivated` 方法，并将所选的任何快速启动项传递到要操作的 `HandleShortcutItem` 方法。 目前，我们仅向**控制台**写入一条消息。 在实际应用中，您将处理所需的操作。 执行该操作后，将清除 "`LaunchedShortcutItem`" 属性。

最后，如果你的应用程序已在运行，则将调用 `PerformActionForShortcutItem` 方法来处理快速操作项，因此我们需要重写它并在此处调用我们的 `HandleShortcutItem` 方法。

### <a name="creating-dynamic-quick-action-items"></a>正在创建动态快速操作项

除了在应用的 `Info.plist` 文件中定义静态快速操作项外，还可以快速创建动态动态操作。 若要定义两个新的动态快速操作，请再次编辑 `AppDelegate.cs` 文件，并修改 `FinishedLaunching` 方法，使其类似于以下内容：

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

现在，我们要检查 `application` 是否已经包含一组动态创建的 `ShortcutItems`，如果不是，我们将创建两个新的 `UIMutableApplicationShortcutItem` 对象来定义新项目并将其添加到 `ShortcutItems` 数组。

我们在[处理 "快速操作](#Handling-a-Quick-Action)" 部分中添加的代码将处理这些动态快速操作，就像静态的操作一样。

应注意的是，可以创建静态和动态快速操作项的组合（如我们在此处执行的操作），而不是仅限于其中一项。

有关详细信息，请参阅[iOS 9 ViewControllerPreview 示例](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-viewcontrollerpreview)并参阅 Apple [ApplicationShortcuts：使用 UIApplicationShortcutItem](https://developer.apple.com/library/prerelease/ios/samplecode/ApplicationShortcuts/) 示例应用、 [UIApplicationShortcutItem 类引用](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationShortcutItem_class/)、 [UIMutableApplicationShortcutItem 类引用](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIMutableApplicationShortcutItem_class/)和[UIApplicationShortcutIcon 类引用](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationShortcutIcon_Class/)。

<a name="Testing-3D-Touch-in-the-Simulator" />

## <a name="testing-3d-touch-in-the-simulator"></a>在模拟器中测试 3D Touch

如果在兼容的 Mac 上使用最新版本的 Xcode 和 iOS 模拟器，并 Force Touch 启用触控板，则可以在模拟器中测试3D 触摸功能。

若要启用此功能，请在支持3D 触摸的模拟 iPhone 硬件（iPhone 6s 及更高版本）中运行任何应用。 接下来，选择 iOS 模拟器中的 "**硬件**" 菜单，并启用 "**对3d 触摸菜单项使用触控板强制**" 菜单项：

[![](3d-touch-images/simulator01.png "Select the Hardware menu in the iOS Simulator and enable the Use Trackpad Force for 3D touch menu item")](3d-touch-images/simulator01.png#lightbox)

此功能处于活动状态时，你可以在 Mac 的触控板上按更难的方式启用3D 触摸，就像在实际的 iPhone 硬件上一样。

## <a name="summary"></a>总结

本文介绍了在 iOS 9 中提供的新 3D Touch Api，适用于 iPhone 6s 和 iPhone 6s Plus。 它涵盖了向应用程序添加压力敏感度。使用速览和 Pop 快速显示当前上下文中的应用内信息，而无需导航;并使用 "快速操作" 提供应用最常用功能的快捷方式。

## <a name="related-links"></a>相关链接

- [iOS 9 ViewControllerPreview 示例](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-viewcontrollerpreview)
- [iOS 9 ApplicationShortcuts 示例](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-applicationshortcuts)
- [适用于开发人员的 iOS 9](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [准备好 iPhone 应用以便进行3D 触控](https://developer.apple.com/ios/3d-touch/)
