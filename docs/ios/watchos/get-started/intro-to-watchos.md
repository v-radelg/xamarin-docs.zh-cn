---
title: WatchOS 简介
description: 本文档概述了 watchOS，描述应用程序生命周期、 用户接口类型、 屏幕大小、 限制和的详细信息。
ms.prod: xamarin
ms.assetid: 99c316d6-6707-40f6-bec9-801d05888759
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/13/2016
ms.openlocfilehash: faa52c80fdb776f2c6c7320874b0d08720b4e4ec
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57670449"
---
# <a name="introduction-to-watchos"></a>WatchOS 简介

> [!NOTE]
> 请查看[watchOS 3 简介](~/ios/watchos/platform/introduction-to-watchos3/index.md)有关最新功能的概述。

## <a name="about-watchos"></a>有关 watchOS

WatchOS 应用解决方案有 3 个项目：

- **观看扩展**– 包含监视应用程序的代码的项目。
- **观看应用**– 包含用户界面情节提要和资源。
- **iOS 父应用**– 此应用是正常的 iPhone 应用。 监视应用和扩展打包为传递到用户的观看的 iphone 版应用。

在 watchOS 1 应用中，在 iPhone 上运行的扩展中的代码-Apple Watch 实际上是外部显示器。 完全在 Apple Watch 上运行 2 和 3 的 watchOS 应用。 这种差异如下图所示：

[ ![](intro-to-watchos-images/arch-sml.png "在 watchOS 1 和 watchOS 2 （和更高版本） 之间的区别是此图中所示")](intro-to-watchos-images/arch.png#lightbox)

无论哪个版本的 watchOS 为目标，在 Visual Studio for Mac 的 Solution Pad 中完整的解决方案将如下所示：

[![](intro-to-watchos-images/projectstructure-sml.png "在 Solution Pad")](intro-to-watchos-images/projectstructure.png#lightbox)

*父应用*watchOS 中的解决方案是一个常规的 iOS 应用程序。 这是唯一的解决方案中的可见**在手机上**。 为此应用的用例包括教程、 管理屏幕和中间层筛选，cacheing，等等。但是，就可以为用户安装和运行而无需监视应用程序/扩展**曾经**打开父应用程序，因此如果您需要父应用一次性初始化或管理工作，为运行需要进行编程手表应用/扩展以告知用户的。

尽管父应用提供了监视应用和扩展，但它们在不同的沙箱中运行。

在 watchOS 1 上，它们可以共享数据通过共享的应用组或静态函数`WKInterfaceController.OpenParentApplication`，这将触发`UIApplicationDelegate.HandleWatchKitExtensionRequest`父应用程序中的方法`AppDelegate`(请参阅[处理父应用](~/ios/watchos/app-fundamentals/parent-app.md))。

也可以用于监视连接框架与父应用程序中，进行通信的 watchOS 2 或更高版本上使用`WCSession`类。

## <a name="application-lifecycle"></a>应用程序生命周期

监视扩展的子类中`WKInterfaceController`为每个情节提要场景创建类。

这些`WKInterfaceController`类是类似于`UIViewController`iOS 编程中的对象但不是具有相同级别的视图访问权限。
例如，不能动态将控件添加到或重新构建您的 UI。
可以但是，隐藏和显示控件并，利用一些控件，更改其大小、 透明度和外观选项。

生命周期`WKInterfaceController`对象涉及以下调用：

- [唤醒状态](xref:WatchKit.WKInterfaceController.Awake*):您应在这种方法来执行大部分你的初始化。
- [WillActivate](xref:WatchKit.WKInterfaceController.WillActivate) :Watch 应用调用后显示给用户。 使用此方法以执行最后一个时刻初始化、 启动动画等。
- 此时，Watch 应用会显示并扩展开始响应用户输入，并更新每个应用程序逻辑的监视应用程序的显示。
- [DidDeactivate](xref:WatchKit.WKInterfaceController.DidDeactivate)后 Watch 应用已由用户已关闭，调用此方法。 此方法返回后，用户界面控件不能修改下一次之前`WillActivate`调用。 如果为 iPhone 连接已断开，则还将调用此方法。
- 该扩展已被停用后，都无法访问你的程序它。 挂起的异步函数**将不会**调用。 观看工具包扩展可能不会使用后台处理模式。 如果用户重新激活该程序，但未由操作系统终止应用程序，第一种方法调用将`WillActivate`。

![](intro-to-watchos-images/wkinterfacecontrollerlifecycle.png "应用程序生命周期概述")

## <a name="types-of-user-interface"></a>类型的用户界面

有三种类型的用户可以使用监视应用程序的交互。
所有编程使用的自定义子类`WKInterfaceController`，因此之前讨论过的生命周期序列普遍适用 (通知进行编程的类与`WKUserNotificationController`，它本身是一个子类别的`WKInterfaceController`):

### <a name="normal-interaction"></a>正常的交互

子类，将监视应用/扩展交互大部分`WKInterfaceController`用于对应于场景中监视应用程序的编写**Interface.storyboard**。 对此进行了详细[安装](~/ios/watchos/get-started/installation.md)并[入门](~/ios/watchos/get-started/index.md)文章。
下图显示了一部分[监视工具包目录](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)示例的情节提要。 此处介绍了每个场景，没有相应的自定义`WKInterfaceController`(`LabelDetailController`， `ButtonDetailController`，`SwitchDetailController`等) 在扩展项目。

![](intro-to-watchos-images/scenes.png "正常交互示例")

### <a name="notifications"></a>通知

[通知](~/ios/watchos/platform/notifications.md)适用于 Apple Watch 的主要用例。 支持本地和远程通知。 与通知交互发生在两个阶段中，名为短和长时间看。

短看起来简要显示，并显示监视应用程序图标、 名称和标题 (指定的`WKInterfaceController.SetTitle`)。

系统提供很长的查找组合**框格**区域和自定义基于情节提要的内容使用解除按钮。

`WKUserNotificationInterfaceController` 扩展了`WKInterfaceController`的方法`DidReceiveLocalNotification`和`DidReceiveRemoteNotification`。
重写这些方法可以响应的通知事件。

有关通知 UI 设计的详细信息，请参阅[Apple Watch 人机接口指南 》](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/Notifications.html#//apple_ref/doc/uid/TP40014992-CH20-SW1)

![](intro-to-watchos-images/notifications.png "示例通知")

## <a name="screen-sizes"></a>屏幕大小

Apple Watch 具有两种人脸大小：38mm 和 42 mm，5:4 显示比率，和 Retina 显示屏。 其易于使用的大小为：

- 38mm:第 136 x 170 逻辑像素为单位 （272 x 340 物理像素为单位）
- 42 mm:156 x 195 逻辑像素 （312 x 390 物理像素为单位）。

使用`WKInterfaceDevice.ScreenBounds`确定哪些显示器上监视应用程序是否正在运行。

通常情况下，它是更轻松地开发更受约束的 38mm 显示文本和布局的设计和扩展。
如果启动具有较大环境时，向下缩放可能会导致难以理解重叠或文本截断。

详细了解[处理屏幕尺寸](~/ios/watchos/app-fundamentals/screen-sizes.md)。


## <a name="limitations-of-watchos"></a>WatchOS 的限制

有的 watchOS 来开发 watchOS 应用程序时应注意的一些限制：

- Apple Watch 设备具有有限的存储-下载大型文件 （例如之前, 应注意的可用空间。 音频或电影文件）。

- 许多 watchOS[控件](~/ios/watchos/user-interface/index.md)类似物中 UIKit，但个不同的类 (`WKInterfaceButton`而非`UIButton`，`WKInterfaceSwitch`为`UISwitch`等) 和具有一组有限的方法相比其 UIKit等效项。 此外，watchOS 具有某些控件如`WKInterfaceDate`（对于显示的日期和时间） 不具有该 UIKit。

  - 不能路由到 Watch 仅通知或 iPhone 仅 （的用户具有个路由的控件类型尚未公布 apple）。

其他已知的限制 / 方面的常见问题：

- Apple 将不允许第三方自定义监视的人脸。

- 允许监视来控制连接的手机上的 iTunes 的 Api 是私有的。


## <a name="further-reading"></a>其他阅读材料

请参阅 apple 文档：

* [为监视工具包开发](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/index.html#//apple_ref/doc/uid/TP40014969-CH8-SW1)

* [观看工具包编程指南](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/DesigningaWatchKitApp.html)

* [Apple Watch 人体学接口指南](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/index.html#//apple_ref/doc/uid/TP40014992-CH3-SW1)


## <a name="related-links"></a>相关链接

- [watchOS 3 目录 （示例）](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [在 watchOS 1 中目录 （示例）](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [设置和安装](~/ios/watchos/get-started/installation.md)
- [第一个 Watch 应用视频](https://blog.xamarin.com/your-first-watch-kit-app/)
- [Apple 的开发的监视包指南](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/index.html)
- [Apple WatchKit 提示](https://developer.apple.com/watchkit/tips/)
- [watchOS 3 简介](~/ios/watchos/platform/introduction-to-watchos3/index.md)
