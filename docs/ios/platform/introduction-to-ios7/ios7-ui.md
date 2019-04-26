---
title: iOS 7 用户界面概述
description: iOS 7 引入了大量的用户界面更改。 本文重点介绍一些更大的变化，在控件的可视外观和委托中支持的新设计的 Api。
ms.prod: xamarin
ms.assetid: FADCEA7C-8968-42A1-9E9E-F4BBAB7BCF2C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 132265c27e1d1ba3b8f3fc8db10d7b3cfa746197
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61038941"
---
# <a name="ios-7-user-interface-overview"></a>iOS 7 用户界面概述

_iOS 7 引入了大量的用户界面更改。本文重点介绍一些更大的变化，在控件的可视外观和委托中支持的新设计的 Api。_

iOS 7 通过 chrome 重点内容。 在 iOS 7 中的用户界面元素不强调 chrome 通过删除属性，如外部边框、 状态栏和导航栏，减少使用的内容视图的屏幕空间量。 在 iOS 7 中，内容旨在使用整个屏幕。

iOS 7 引入了几个其他更改： 使用颜色来区分用户界面元素，而不是属性，如按钮边框。 很多元素，如导航栏和状态栏，现在都经过模糊处理和透明或透明的使用记录在其下的区域的内容视图。 这些内容视图呈现通过经过模糊处理的条，传达用户界面中的深度的感觉。

本文介绍如何在 iOS 7 以及各种 Api 与新的用户界面设计相关的用户界面元素的更改的几个。

## <a name="view-and-control-changes"></a>查看和控制更改

在 UIKit 中的视图的所有符合的 iOS 7 的新外观和感觉。 本部分重点介绍一些对这些视图，以及已更改，以支持新的用户界面的相关的 Api 的更改。

### <a name="uibutton"></a>UIButton

创建从按钮`UIButton`类现无边距，默认情况下，没有背景，如下所示：

 ![](ios7-ui-images/button.png "示例 UIButton")

`UIButtonType.RoundedRect`样式已被弃用。 如果在 iOS 7 中使用`UIButtonType.RoundedRect`将导致`UIButtonType.System`正在使用，这会生成不带任何背景或可见边缘的默认按钮样式如上所示。

### <a name="uibarbuttonitem"></a>UIBarButtonItem

类似于`UIButton`，栏按钮也是无边距，默认设置为新`UIBarButtonItemStyle.Plain`样式如下所示：

 ![](ios7-ui-images/barbuttonplain.png "示例 UIBarButtonItem")

此外，`UIBarButtonItemStyle.Bordered`样式已被弃用。 设置`UIBarButtonItemStyle.Bordered`在 iOS 7 将导致`UIBarButtonItemStyle.Plain`正在使用的样式。

`UIBarButtonItemStyle.Done`样式不推荐使用。 但是，它还将创建一个无边距的按钮，仅使用粗体文本样式所示：

 ![](ios7-ui-images/barbuttondone.png "在完成样式示例 UIBarButtonItem")

### <a name="uialertview"></a>UIAlertView

除了新的 iOS 7 界面外观的样式更改，警报视图不再支持通过子视图的自定义。 即使`UIAlertView`继承自`UIView`，则调用`AddSubview`上`UIAlertView`不起作用。 例如，考虑以下代码：

```csharp
UIBarButtonItem button = new UIBarButtonItem ("Bar Button", UIBarButtonItemStyle.Plain, (s,e) =>
{
    UIAlertView alert = new UIAlertView ("Title", "Message", null, "Cancel", "OK");

    alert.AddSubview (new UIView () {
        Frame = new CGRect(50, 50,100, 100),
        BackgroundColor = UIColor.Green
    });

    alert.Show ();
});
```

这将产生标准的警报视图，与子视图被忽略，如下所示：

 ![](ios7-ui-images/alert.png "示例 UIAlertView")
 
 注意:在 iOS 8 中已弃用 UIAlertView。 视图[警报控制器](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/alertcontroller)方案使用警报视图，在 iOS 8 及更高版本。

### <a name="uisegmentedcontrol"></a>UISegmentedControl

在 iOS 7 中的分段的控件是透明的支持色调颜色。 着色颜色用于文本和边框颜色。 选择一个段后，颜色背景和文本之间交换与用来突出显示所选的段落，如下所示的色调颜色：

 ![](ios7-ui-images/segmentedcontrol.png "示例 UISegmentedControl")

此外， `UISegmentedControlStyle` iOS 7 中已弃用。

### <a name="picker-views"></a>选取器视图

选取器视图的 API 是很大程度上保持不变;但是，iOS 7 设计准则现在状态应以内联方式存在选取器视图，而不是如从动画输入的视图底部屏幕或通过新的控制器推送到导航控制器的堆栈，如下所示以前 iOS 版本。 这可以在系统日历应用中看到：

 ![](ios7-ui-images/inlinepicker.png "这可在系统日历应用")

### <a name="uisearchdisplaycontroller"></a>UISearchDisplayController

在搜索栏现在显示在导航栏时`UISearchDisplayController.DisplaysSearchBarInNavigationBar`属性设置为 true。 如果设置为 false-默认值为-显示搜索控制器，则隐藏导航栏。

以下屏幕截图显示了中的搜索栏`UISearchDisplayController`:

 ![](ios7-ui-images/searchbar.png "示例 UISearchDisplayController")

### <a name="uitableview"></a>UITableView

围绕 Api`UITableView`主要是保持不变; 但是，样式发生了重大变化以符合新的用户界面设计。 内部视图层次结构也是略有不同。 此更改不会影响大多数应用中，但这是一个需要注意。

#### <a name="grouped-table-style"></a>分组的表样式

更新已更改的分组的样式，现在将扩展到屏幕，如下所示的边缘的内容：

 ![](ios7-ui-images/table1.png "示例分组的表样式")

#### <a name="separatorinset"></a>SeparatorInset

现在可以通过设置缩进行分隔符`UITableVIewCell.SeparatorInset`属性。 例如，将使用下面的代码的单元格左边缘的缩进：

```csharp
cell.SeparatorInset = new UIEdgeInsets (0, 50, 0, 0);
```

这将产生与缩进的单元格的表视图中，如下所示：

 ![](ios7-ui-images/separatorinset.png "示例 UITableView SeparatorInset")

#### <a name="table-button-styles"></a>表按钮样式

所有已更改的表视图中使用的各种按钮。 下面的屏幕截图在编辑模式下呈现表视图：

 ![](ios7-ui-images/table2.png "此屏幕截图在编辑模式下呈现表视图")

### <a name="additional-control-changes"></a>更多控制更改

其他 UIKit 控件也已更改，包括滑块、 开关和分档器。 这些更改是纯粹 visual。 有关详细信息，请参阅 Apple [iOS 7 用户界面转换指导](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/TransitionGuide/index.html)。

## <a name="general-user-interface-changes"></a>常规用户界面更改

除了在 UIKit 中更改，iOS 7 引入了各种可视更改 ui，其中包括：

-  全屏显示内容
-  条形图外观
-  着色颜色

<a name="fullscreen" />

### <a name="full-screen-content"></a>全屏幕内容

iOS 7 旨在使应用程序充分利用整个屏幕。 视图控制器现在显示的状态栏和导航栏的重叠，如果存在-而不是显示的状态和导航栏的下方。

在准备适用于 iOS 7 应用程序时，可以重新调整子视图直观地使用*Interface Builder*或*Xamarin iOS 设计器*。 此外可以使用新的 Api 之一来以编程方式处理全屏幕内容。 下面介绍了这些 Api。

#### <a name="toplayoutguide-and-bottomlayoutguide"></a>TopLayoutGuide 和 BottomLayoutGuide

 `TopLayoutGuide` 并`BottomLayoutGuide`用作视图应开始或结束，其中的引用，以便内容不重叠的半透明`UIKit`栏中的，如以下示例所示：

 [![](ios7-ui-images/clipped.png "不重叠的半透明 UIKit 栏的示例内容")](ios7-ui-images/clipped.png#lightbox)

这些 Api 可用于计算视图的偏移量，从顶部或底部的屏幕，并相应地调整内容的位置：

```csharp
public override void ViewDidLayoutSubviews ()
{
    base.ViewDidLayoutSubviews ();

    if (UIDevice.CurrentDevice.CheckSystemVersion (7, 0)) { 
        nfloat displacement_y = this.TopLayoutGuide.Length;

        //load subviews with displacement
    }

}
```

我们可以使用设置上面计算得出的值我们`ImageView`的偏移量从屏幕上，因此整个图像是可见的顶部：

 [![](ios7-ui-images/good2.png "示例 ImageViews 从屏幕顶部的偏移量")](ios7-ui-images/good2.png#lightbox)

请参阅[ImageViewer](https://developer.xamarin.com/samples/mobile/iOS7-ui-updates)有关工作示例。

偏移量值动态生成的视图添加到层次结构，因此尝试读取后`TopLayoutGuide`并`BottomLayoutGuide`中的值以`ViewDidLoad`将返回 0。 在视图加载之后-例如，在计算值`ViewDidLayoutSubviews`。

> [!IMPORTANT]
> `TopLayoutGuide` 和`BottomLayoutGuide`以新的安全区域布局支持 iOS 11 中已弃用。 Apple 已声明，使用安全区域是与 iOS 版本早于 iOS 11 兼容。 有关详细信息，请参阅[更新适用于 iOS 11 应用](~/ios/platform/introduction-to-ios11/updating-your-app/visual-design.md#fullscreen)指南。

#### <a name="edgesforextendedlayout"></a>EdgesForExtendedLayout

此 API 指定视图的哪些边缘应扩展到全屏模式，而不考虑栏半透明度。 在 iOS 7 中，导航栏和工具栏将出现上面控制器的视图的不同于在上一 iOS 中的版本中，没有占用相同的空间。 IOS 7 照片应用程序演示默认`UIViewController.EdgesForExtendedLayout`值， `UIRectEdge.All`。 此设置填充所有四个边缘的内容，在视图中创建的重叠和全屏幕的效果：

 [![](ios7-ui-images/photos.png "示例 EdgesForExtendedLayout")](ios7-ui-images/photos.png#lightbox)

点击该映像删除线，并演示图像全屏幕：

 [![](ios7-ui-images/photos2.png "以条形图中删除 EdgesForExtendedLayout")](ios7-ui-images/photos2.png#lightbox)

由于全屏幕内容是默认值，配置适用于 iOS 6 的应用程序将具有剪辑，如下面的屏幕截图中所示的视图的一部分：

 [![](ios7-ui-images/clipped.png "配置适用于 iOS 6 的应用会剪切，如以下屏幕截图中所示的视图的一部分")](ios7-ui-images/clipped.png#lightbox)

修改`UIViewController.EdgesForExtendedLayout`属性根据需要调整此行为。 我们可以指定视图不填充任何边缘，因此我们视图会避免在导航或工具栏 （在每个方向） 所占用的空间中显示的内容：

```csharp
if (UIDevice.CurrentDevice.CheckSystemVersion (7, 0)) { 
    this.EdgesForExtendedLayout = UIRectEdge.None;
}
```

在我们的应用程序，我们将看到再次重新定位视图，使整个图像可见：

 [![](ios7-ui-images/good.png "使用可见的整个图像示例")](ios7-ui-images/good.png#lightbox)

请注意，尽管的效果`TopLayoutGuide/BottomLayoutGuide`和`EdgesForExtendedLayout`Api 很相似，它们专门用于填充不同类型的目标。 更改`EdgesForExtendedLayout`默认设置可能会在为 iOS 6，设计的应用程序中修复裁剪后的视图，但好 iOS 7 设计应遵循的全屏美学和提供的全屏观看体验、 依赖于`TopLayoutGuide`和`BottomLayoutGuide`正确放置内容的目的是要操作到适合用户的位置。

请参阅[ImageViewer](https://developer.xamarin.com/samples/mobile/iOS7-ui-updates)有关工作示例。

### <a name="status-and-navigation-bars"></a>状态和导航栏

使用透明度呈现的状态栏和导航栏。 状态栏是深度的透明的而工具栏和导航栏是深度的半透明和经过模糊处理以传达用户界面中的感觉。 下面的屏幕截图显示了此模糊处理和透明度，其中集合视图的蓝色背景色显示通过的状态和导航栏，为他们提供浅蓝色外观：

 ![](ios7-ui-images/transparent-navbar.png "示例状态和导航栏模糊处理")

#### <a name="status-bar-styles"></a>状态栏样式

模糊处理和透明度，以及状态栏的前景色可以 light 或深色 （深色正在默认值）。 可以从视图控制器设置状态栏样式。 视图控制器还可以设置是否隐藏或显示状态栏。

例如，以下代码重写`PreferredStatusBarStyle`的视图控制器，以使显示浅的前景色的状态栏的方法：

```csharp
public override UIStatusBarStyle PreferredStatusBarStyle ()
{
    return UIStatusBarStyle.LightContent;
}
```

这将导致状态栏中显示如下所示：

 ![](ios7-ui-images/light-status-bar.png "示例状态栏")

若要隐藏状态栏从视图控制器的代码，请重写`PrefersStatusBarHidden`，如下所示：

```csharp
public override bool PrefersStatusBarHidden ()
{
    return true;
}
```

这将隐藏状态栏：

 ![](ios7-ui-images/status-bar-hidden.png "隐藏状态栏")

### <a name="tint-color"></a>着色颜色

按钮现在显示为无镶边的文本。 可以使用的新控制的文本颜色`TintColor`属性上的`UIView`。 设置`TintColor`将颜色应用于整个视图层次结构的视图的将其设置。 若要将应用`TintColor`整个应用程序，请将其设置上`Window`。 您还可以检测通过更改着色颜色时`UIView.TintColorDidChange`方法。

例如，下面的屏幕截图显示了更改导航控制器的视图上的色调颜色的效果为紫色：

 ![](ios7-ui-images/tint-color.png "对导航控制器视图紫色浅色颜色")

着色颜色可以应用于图像以及何时`RenderingMode`设置为`UIImageRenderingMode.AlwaysTemplate`。

> [!IMPORTANT]
> 不能使用设置着色颜色`UIAppearance`。


### <a name="dynamic-type"></a>动态类型

在 iOS 7 中，用户可以指定系统设置中的文本大小。 使用动态类型，该字体将进行动态调整妙无论大小如何。 `UIFont.PreferredFontForTextStyle` 应该用于获得用户控制大小进行了优化的字体。

## <a name="summary"></a>总结

本文介绍如何在 iOS 7 中的用户界面元素的更改。 它会检查多个 UIKit，突出显示这两个可视更改中的视图和控件所做的更改以及将更改为相关的 Api。 最后，它引入了新的 Api 来使用全屏显示内容、 新的色调颜色支持和动态类型。

## <a name="related-links"></a>相关链接

- [ImageViewer （示例）](https://developer.xamarin.com/samples/monotouch/iOS7-ui-updates)
