---
title: IPad Xamarin.iOS 的多任务
description: iOS 9 支持两个应用程序运行在同一时间、 通过使用幻灯片或拆分视图。 它还支持画中画中播放视频。
ms.prod: xamarin
ms.assetid: 0F2266D7-21FF-404D-A148-0CFDE76B12AA
ms.technology: xamarin-ios
ms.custom: xamu-video
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: 68c2ae6eace2669d2ea6c77d72f4476d767c0a7d
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57672438"
---
# <a name="multitasking-for-ipad-in-xamarinios"></a>IPad Xamarin.iOS 的多任务

_iOS 9 支持两个应用程序运行在同一时间、 通过使用幻灯片或拆分视图。它还支持画中画中播放视频。_

![](multitasking-images/about02-sml.png "拆分屏幕示例") ![](multitasking-images/about03-sml.png "图片中图示例")

iOS 9 增加了对特定 iPad 硬件上同时运行两个应用的多任务处理支持。 适用于 iPad 的多任务支持通过以下功能：

- [**通过幻灯片**](#Slide-Over) -允许用户暂时滑出面板 （根据语言方向在屏幕右侧或左侧边缘上或者） 涵盖大约 25%的当前运行的主应用程序中运行的第二个 iOS 应用程序。 幻灯片上是仅适用于 iPad Pro、 iPad 以无线方式、 iPad 空气 2、 iPad Mini 2、 iPad Mini 3 或 iPad Mini 4。
- [**拆分视图**](#Split-View) -用户可以在支持的 iPad 硬件 (iPad 空气 2，iPad Mini 4 和专业人员仅限 iPad) 上，选择第二个应用和使用当前运行的应用在拆分屏幕模式下运行的并行。 用户可以控制在主屏幕中的每个应用所占的百分比。
- [**在图中的图片**](#Picture-in-Picture) -适用于播放的视频内容，现在视频可以播放在可移动、 可调整大小窗口浮动转移当前在 iOS 设备上运行的其他应用中的应用。 用户可以完全控制的大小和位置的此窗口。 IPad Pro、 iPad 以无线方式、 iPad 空气 2、 iPad Mini 2、 iPad Mini 3 或 iPad Mini 4 上仅可在图中的图片。

有许多时要考虑的因素[应用程序中支持多任务处理](#Supporting-Multitasking-in-your-App)，其中包括：

- [屏幕大小和方向](#Screen-Size-Considerations)
- [自定义硬件键盘快捷方式](#Custom-Hardware-Keyboard-Shortcuts)
- [资源管理](#Resource-Management-Considerations)

作为应用开发人员还可以[选择退出的多任务](#Opting-Out-of-Multitasking)，其中包括[禁用 PIP 视频播放](#Disabling-PIP-Video-Playback)。

本文将介绍以确保在多任务处理环境中正常运行 Xamarin.iOS 应用程序，或者如何选择退出的多任务，如果它不是很适合于您的应用程序所需的步骤。

> [!VIDEO https://youtube.com/embed/GctYAozoLr8]

**适用于 iPad，多任务处理通过[Xamarin University](https://university.xamarin.com)**


<a name="Multitasking-QuickStart" />

## <a name="multitasking-quickstart"></a>多任务快速入门

若要支持**幻灯片转移**或**拆分视图**您的应用程序必须执行以下操作：

 - 生成针对 iOS 9 （或更高版本）。
 - 用于启动屏幕情节提要 （和不图像资产）。
 - 为其 UI 使用自动布局和大小类使用情节提要。
 - 支持所有 4 个的 iOS 设备方向 （纵向、 散乱的纵向、 横向左侧和横向右侧）。

<a name="Multitasking" />

## <a name="about-multitasking-for-ipad"></a>有关适用于 iPad 的多任务

iOS 9 提供了新引入的 iPad 上的多任务处理性能_通过滑动_，_拆分视图_(iPad 空气 2，iPad Mini 4 和仅限 iPad Pro) 和_Picture in Picture_。 我们将详细介绍这些功能在以下各节中。

<a name="Slide-Over" />

### <a name="slide-over"></a>通过幻灯片

幻灯片通过功能允许用户选择第二个应用并将其显示在小的滑动面板中，以提供快速的交互。 幻灯片通过面板是临时的用户将恢复为再次使用主应用程序时，将关闭。

[![](multitasking-images/about01.png "在幻灯片上面板")](multitasking-images/about01.png#lightbox)

需要记住的主要一点是用户决定将运行的两个应用，通过并行和开发人员可以不控制此过程。 因此，有几件事，需要确保在幻灯片上面板中正常运行 Xamarin.iOS 应用程序：

- **使用自动布局和大小类**-Xamarin.iOS 应用程序现在可以在幻灯片向外侧面板中运行，因为您可以不再依赖于设备、 其屏幕大小或自己的方向为布局 UI。 若要确保您的应用程序能够正确缩放的接口，您将需要使用自动布局和大小类。 有关详细信息，请参阅我们[统一情节提要简介](~/ios/user-interface/storyboards/unified-storyboards.md)文档。
- **资源有效地使用**— 现在共享您的应用程序与另一个正在运行的应用的系统，因为它是关键应用程序有效地使用系统资源。 稀疏内存后，系统将自动终止占用了大多数内存应用程序。 请参阅 Apple[能源效率指南适用于 iOS 应用](https://developer.apple.com/library/prerelease/ios/documentation/Performance/Conceptual/EnergyGuide-iOS/index.html#//apple_ref/doc/uid/TP40015243)的更多详细信息。

幻灯片上是仅适用于 iPad Pro、 iPad 以无线方式、 iPad 空气 2、 iPad Mini 2、 iPad Mini 3 或 iPad Mini 4。 若要了解有关为幻灯片转移准备您的应用程序的详细信息，请参阅 Apple [iPad 上采用多任务处理增强功能](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/index.html#//apple_ref/doc/uid/TP40015145)文档。

<a name="Split-View" />

### <a name="split-view"></a>拆分视图

支持的 iPad 在硬件上 (iPad 空气 2，iPad Mini 4 和 iPad 仅限 Pro)，用户可以选择第二个应用并使用当前运行的应用在拆分屏幕模式下运行的并行。 用户可以控制在主屏幕中的每个应用通过拖动所占的百分比屏幕分隔符。

[![](multitasking-images/about02.png "拆分视图")](multitasking-images/about02.png#lightbox)

用户决定将通过并行运行的两个应用和同样，开发人员可以控制此过程不一样幻灯片上。 因此，拆分视图置于一个 Xamarin.iOS 应用程序类似的要求：

- **使用自动布局和大小类**-Xamarin.iOS 应用程序现在可以在用户的指定大小在拆分屏幕模式下运行，因为你可以不再依赖于设备、 其屏幕大小或自己的方向为布局 UI。 若要确保您的应用程序能够正确缩放的接口，您将需要使用自动布局和大小类。 有关详细信息，请参阅我们[统一情节提要简介](~/ios/user-interface/storyboards/unified-storyboards.md)文档。
- **资源有效地使用**— 现在共享您的应用程序与另一个正在运行的应用的系统，因为它是关键应用程序有效地使用系统资源。 稀疏内存后，系统将自动终止占用了大多数内存应用程序。 请参阅 Apple[能源效率指南适用于 iOS 应用](https://developer.apple.com/library/prerelease/ios/documentation/Performance/Conceptual/EnergyGuide-iOS/index.html#//apple_ref/doc/uid/TP40015243)的更多详细信息。

若要了解有关为拆分视图准备您的应用程序的详细信息，请参阅 Apple [iPad 上采用多任务处理增强功能](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/index.html#//apple_ref/doc/uid/TP40015145)文档。

<a name="Picture-in-Picture" />

### <a name="picture-in-picture"></a>在图中的图片

在图功能的新图片 (也称为_PIP_) 允许用户要观看的视频中用户可以在以上其他正在运行的应用的屏幕任意位置放置一个小、 浮动窗口。

[![](multitasking-images/about03.png "示例图片浮动窗口中的图片")](multitasking-images/about03.png#lightbox)

为与滑动转移和拆分视图中，用户可以完全控制电视的图片模式中所示。 如果应用程序的主要功能是若要观看视频，它将需要进行一些修改，以在 PIP 模式下正常运行。 否则，不需要进行更改以支持 PIP。

应用 PIP 视频显示在用户的请求时，将需要使用任一_AVKit_或_AV Foundation Api_。 媒体播放器框架已弃用在 iOS 9 中和不支持 PIP。

IPad Pro、 iPad 以无线方式、 iPad 空气 2、 iPad Mini 2、 iPad Mini 3 或 iPad Mini 4 上仅可在图中的图片。 有关详细信息，请参阅我们[PictureInPicture 示例应用](https://developer.xamarin.com/samples/ios/iOS9/)和 Apple[图片快速入门中的图片](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/QuickStartForPictureInPicture.html#//apple_ref/doc/uid/TP40015145-CH14)文档。

<a name="Supporting-Multitasking-in-your-App" />

## <a name="supporting-multitasking-in-your-app"></a>在您的应用程序中支持多任务

对于任何现有的 Xamarin.iOS 应用，支持多任务处理是透明的任务，只要您的应用程序已经遵循 Apple 的设计指南和最佳实践适用于 iOS 8。 这意味着，应用应使用情节提要使用自动布局和大小类对于其用户界面的布局 (请参阅我们[统一情节提要简介](~/ios/user-interface/storyboards/unified-storyboards.md)有关详细信息)。

对于这些应用，不需要以支持多任务并在其中的行为很少或没有更改。 如果您的应用程序的 UI，使用其他方法，例如直接定位和调整 UI 元素中的创建C#代码，或者如果它依赖于特定设备的屏幕大小或方向，它将需要重大修改，即可正确支持 iOS 9 的多任务。

若要支持 iOS 9 多任务处理任何新的 Xamarin.iOS 应用程序，再次对所有应用程序的用户界面布局使用自动布局和大小类使用情节提要，并实现以下各节中的说明进行操作。

<a name="Screen-Size-Considerations" />

### <a name="screen-size-and-orientation-considerations"></a>屏幕大小和方向的注意事项

IOS 9 之前, 您可以设计您的应用程序针对特定设备屏幕尺寸和方向。 因为滑出面板中或在拆分视图模式下，现在可以运行应用，它可以查找本身中任何一种紧凑或常规的水平大小类上运行 iPad，而不考虑设备的物理方向或屏幕大小。

[![](multitasking-images/sizeclasses01.png "屏幕大小和方向的注意事项")](multitasking-images/sizeclasses01.png#lightbox)

适用于 iPad 的全屏应用具有正则水平和垂直大小类。 所有 iPhone 但 iPhone 6 Plus 和 iPhone 6s Plus，位于采用任何方向的两个方向中有压缩大小类。 iPhone 6 Plus 和 iPhone 6s Plus 在横向模式中具有一个正则的水平大小类和 Compact 的垂直大小类 （非常类似于 iPad 迷你）。

在支持滑动通过和拆分视图的 iPad，你可以结束，以下组合：

| **方向** | **主应用程序** | **辅助应用程序** |
|--- |--- |--- |
| **纵向** |75%的屏幕<br />Compact 水平<br />正则垂直|25%的屏幕<br />Compact 水平<br />正则垂直|
| **Landscape** |75%的屏幕<br />正则水平<br />正则垂直|25%的屏幕<br />Compact 水平<br />正则垂直|
| **Landscape** |50%的屏幕<br />Compact 水平<br />正则垂直|50%的屏幕<br />Compact 水平<br />正则垂直|

在示例[MuliTask](https://developer.xamarin.com/samples/monotouch/ios9/MultiTask/)应用程序中，如果它在横向模式下在 iPad 上运行全屏幕，它将显示列表和详细信息视图中的同一时间：

[![](multitasking-images/sizeclasses03.png "列表和显示在同一时间的详细信息视图")](multitasking-images/sizeclasses03.png#lightbox)

如果滑动通过面板中运行同一应用程序，则它作为 Compact 的水平大小类的布局方式，并只显示列表：

[![](multitasking-images/sizeclasses04.png "仅显示时是水平的设备的列表")](multitasking-images/sizeclasses04.png#lightbox)

若要确保您的应用程序行为正确在这些情况下，应采用以及大小类的特性集合，并符合`IUIContentContainer`和`IUITraitEnvironment`接口。 请参阅 Apple [UITraitCollection 类引用](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UITraitCollection_ClassReference/index.html#//apple_ref/doc/uid/TP40014202)和我们[统一情节提要简介](~/ios/user-interface/storyboards/unified-storyboards.md)指南以获取详细信息。

此外，您可以不再依赖于要定义应用的可见区域的设备屏幕边界，将需要改为使用您的应用程序窗口边界。 由于窗口边界是完全受用户控制的无法以编程方式对其进行调整或阻止用户更改这些边界。

最后，您的应用程序必须使用情节提要文件以显示启动屏幕而不是使用一套 **.png**图像文件和支持全部四个界面方向 （纵向倒置纵向、 横向和横向 Right）若要被视为对于滑动通过面板中或在拆分视图模式下运行。

<a name="Custom-Hardware-Keyboard-Shortcuts" />

### <a name="custom-hardware-keyboard-shortcuts"></a>自定义硬件键盘快捷方式

在 iOS 9 iPad 上运行，Apple 已扩展的支持的硬件键盘。 iPad 始终具有包括基本外部键盘支持通过蓝牙和某些键盘制造商创建键盘包含硬连线的特定于 iOS 的密钥。

现在，使用 iOS 9，应用可以创建其自己的自定义键盘快捷方式。 此外，一些基本的键盘快捷方式可像**命令-C** （复制），**命令 X** （剪切）**命令 V** （粘贴） 和**命令 Shift H** （主页），而无需应用程序正在对它们进行专门编写的响应。

**命令选项卡**会弹出允许用户在键盘上类似于 Mac OS 的应用程序之间快速切换应用切换器：

[![](multitasking-images/keyboard01.png "应用程序切换器")](multitasking-images/keyboard01.png#lightbox)

如果 iOS 9 应用程序中包含的键盘快捷方式，用户可以按住**命令**，**选项**或**控制**密钥以在弹出窗口中显示它们：

[![](multitasking-images/keyboard02.png "键盘快捷方式弹出项")](multitasking-images/keyboard02.png#lightbox)

#### <a name="defining-custom-keyboard-shortcuts"></a>定义自定义键盘快捷方式

如果我们以下代码添加到视图或视图控制器在我们的应用程序，该视图或控制器可见时，将提供的自定义键盘快捷方式：

```csharp
#region Custom Keyboard Shortcut
public override bool CanBecomeFirstResponder {
    get { return true; }
}

public override UIKeyCommand[] KeyCommands {
    get {

        var keyCommand = UIKeyCommand.Create (new NSString("n"), UIKeyModifierFlags.Command, new Selector ("NewEntry"), new NSString("New Entry"));
        return new UIKeyCommand[]{ keyCommand };
    }
}

[Export("NewEntry")]
public void NewEntry() {

    // Add a new entry
    ...

}
#endregion
```

首先，我们重写`CanBecomeFirstResponder`属性，并返回`true`以便视图或视图控制器可以接收键盘输入。 

接下来，我们重写`KeyCommands`属性，并创建一个新`UIKeyCommand`有关**命令 N**击键。 当激活击键时，我们调用`NewEntry`方法 (我们向 iOS 9 使用公开`Export`命令) 来执行请求的操作。

如果运行该应用程序在 iPad 上使用附加硬件键盘和用户类型**命令 N**，将新条目添加到列表。 如果用户按下**命令**键，快捷方式的列表将显示：

[![](multitasking-images/keyboard03.png "键盘快捷方式弹出项")](multitasking-images/keyboard03.png#lightbox)

该示例，请参阅[MultiTask 应用](https://developer.xamarin.com/samples/monotouch/ios9/MultiTask/)有关的示例实现。

<a name="Resource-Management-Considerations" />

### <a name="resource-management-considerations"></a>资源管理注意事项

即使对于已在使用 iOS 8 的设计指南和最佳实践的应用程序，有效的资源管理仍可能会出现问题。 在 iOS 9 中，应用程序不再需要内存、 CPU 或其他系统资源的独占的使用。

因此，必须优化你的 Xamarin.iOS 应用以有效地使用系统资源或面临内存不足的情况下终止。 这是选择退出多任务处理的应用程序也同样适用，因为第二个应用程序可能仍运行幻灯片通过面板或图片在图片窗口需要额外的资源或导致刷新频率低于每秒 60 帧中。

请考虑以下用户操作和及其含义：

- **幻灯片通过面板中输入文本**-即使您的应用程序有任何文本输入，在系统键盘，现在可以通过其 UI 显示。 因此，应用可能需要对键盘显示通知 （如显示和隐藏键盘） 做出响应。
- **在幻灯片上面板中运行第二个应用**-新的应用现在在前台运行，并与现有应用程序的内存和 CPU 周期等系统资源争用。
- **播放视频 PIP 窗口中的**-不只可以此窗口涵盖应用程序的接口的一部分，但应用启动视频仍在后台运行并占用 CPU 和内存资源。

若要确保你的应用高效地使用资源，应执行以下操作：

- **分析应用程序使用 Instruments** -检查内存泄漏、 产品说明具有明显的 CPU 使用率和在其中应用程序可能会阻止主线程的区域。
- **响应状态的转换方法**-在你**AppDelegate.cs**文件重写和响应状态更改输入或返回从后台应用之类的方法。 释放任何不是必需的资产，如图像、 数据或视图和视图控制器。
- **测试与内存密集型应用程序并行**-运行应用程序使用滑出和物理 iOS 硬件上的拆分视图与内存密集型中的应用映射如 （附属视图模式） 和测试这两个应用程序保持响应状态和不崩溃。

请参阅 Apple[能源效率指南适用于 iOS 应用](https://developer.apple.com/library/prerelease/ios/documentation/Performance/Conceptual/EnergyGuide-iOS/index.html#//apple_ref/doc/uid/TP40015243)为资源管理的详细信息。

<a name="Opting-Out-of-Multitasking" />

## <a name="opting-out-of-multitasking"></a>选择退出的多任务

虽然所有 iOS 9 应用都支持多任务，Apple 提供建议，可能那里极特殊原因需要应用程序不太如游戏或需要全屏才能正常工作的相机应用。

Xamarin.iOS 应用程序可以选择不使用任一滑块滑出在面板中或在拆分视图模式下运行，编辑项目的**Info.plist**文件，然后检查**需要全屏**:

[![](multitasking-images/fullscreen01.png "选择退出的多任务")](multitasking-images/fullscreen01.png#lightbox)

> [!IMPORTANT]
> 选择退出的多任务使您的应用程序无法运行滑出或拆分视图中，而它不会阻止另一个应用以滑出或视频的图片中的图片显示与您的应用程序一起运行。

<a name="Disabling-PIP-Video-Playback" />

### <a name="disabling-pip-video-playback"></a>禁用 PIP 视频播放

在大多数情况下，您的应用程序应当允许用户以用于播放在图片中图片的浮动窗口中显示任何视频内容。 但是，可能情况下，这可能不所希望的例如游戏剪切的场景视频。

若要选择退出 PIP 视频播放，请执行以下操作应用程序中：

 - 如果使用的`AVPlayerViewController`若要显示视频，请设置`AllowsPictureInPicturePlayback`属性设置为`false`。
 - 如果使用的`AVPlayerLayer`若要显示视频，不实例化`AVPictureInPictureController`。
 - 如果使用的`WKWebView`若要显示视频，请设置`AllowsPictureInPictureMediaPlayback`属性设置为`false`。

<a name="Summary" />

## <a name="summary"></a>总结

本文介绍了为确保 Xamarin.iOS 应用程序将运行并在 iOS 9 的新多任务能力 iPad 中的正确行为所需的步骤。 此外，其中包括如何选择退出应用，它不是非常适合多任务。



## <a name="related-links"></a>相关链接

- [iOS 9 示例](https://developer.xamarin.com/samples/ios/iOS9/)
- [执行多任务 （示例）](https://developer.xamarin.com/samples/monotouch/ios9/MultiTask/)
- [统一的情节提要简介](~/ios/user-interface/storyboards/unified-storyboards.md)
- [面向开发人员的 iOS 9](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [在 iPad 上采用多任务处理增强功能](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/index.html#//apple_ref/doc/uid/TP40015145)
- [博客文章](https://blog.xamarin.com/using-auto-layouts-for-ios-9-splitview/)
