---
title: watchOS 简介
description: 本文档概述了 watchOS，描述了应用程序生命周期、用户界面类型、屏幕大小、限制等。
ms.prod: xamarin
ms.assetid: 99c316d6-6707-40f6-bec9-801d05888759
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/13/2016
ms.openlocfilehash: b3c2908d8ae9a68189fbff4d47afa49da21b88a5
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030191"
---
# <a name="introduction-to-watchos"></a>watchOS 简介

> [!NOTE]
> 有关最新功能的概述，请查看[watchOS 3 简介](~/ios/watchos/platform/introduction-to-watchos3/index.md)。

## <a name="about-watchos"></a>关于 watchOS

WatchOS 应用解决方案包含3个项目：

- **监视扩展**-包含 Watch 应用代码的项目。
- **监视应用**-包含用户界面情节提要和资源。
- **IOS 父应用**–此应用是普通的 iPhone 应用。 监视应用和扩展捆绑到 iPhone 应用中，以便交付到用户的手表。

在 watchOS 1 应用中，扩展中的代码在 iPhone 上运行– Apple Watch 实际上是外部显示。 watchOS 2 和3应用完全在 Apple Watch 上运行。 下图显示了这种差异：

[![](intro-to-watchos-images/arch-sml.png "The difference between watchOS 1 and watchOS 2 (and greater) is shown in this diagram")](intro-to-watchos-images/arch.png#lightbox)

无论 watchOS 的版本是哪个版本，Visual Studio for Mac 的 Solution Pad 完整的解决方案将如下所示：

[![](intro-to-watchos-images/projectstructure-sml.png "The Solution Pad")](intro-to-watchos-images/projectstructure.png#lightbox)

WatchOS 解决方案中的*父应用*是一种常规的 iOS 应用。 这是解决方案中可在**手机上**看到的唯一项目。 此应用程序的用例包括教程、管理屏幕和中间层筛选 cacheing 等。但是，用户可以在不打开父应用**的情况下**安装和运行 watch 应用/扩展，因此，如果需要父应用程序运行一次或管理，则需要对你的手表应用程序/扩展进行编程，以通知用户。

虽然父应用程序提供了 watch 应用和扩展，但它们在不同的沙箱中运行。

在 watchOS 1 上，他们可以通过共享应用组或静态函数 `WKInterfaceController.OpenParentApplication`共享数据，这将触发父应用 `AppDelegate` 中的 `UIApplicationDelegate.HandleWatchKitExtensionRequest` 方法（请参阅使用[父应用](~/ios/watchos/app-fundamentals/parent-app.md)）。

在 watchOS 2 或更高版本中，使用 `WCSession` 类将 Watch 连接框架与父应用进行通信。

## <a name="application-lifecycle"></a>应用程序生命周期

在监视扩展中，为每个情节提要场景创建 `WKInterfaceController` 类的子类。

这些 `WKInterfaceController` 类类似于 iOS 编程中的 `UIViewController` 对象，但对视图没有相同的访问级别。
例如，不能将控件动态添加到 UI 或重构 UI。
但是，您可以隐藏和显示控件，并在某些控件中更改其大小、透明度和外观选项。

`WKInterfaceController` 对象的生命周期涉及以下调用：

- [唤醒](xref:WatchKit.WKInterfaceController.Awake*)：应在此方法中执行大多数初始化。
- [WillActivate](xref:WatchKit.WKInterfaceController.WillActivate) ：在向用户显示 Watch 应用之前立即调用。 使用此方法可以执行最后一次初始化、启动动画等。
- 此时，将显示 "监视" 应用，并且扩展会开始响应用户输入，并根据应用程序逻辑更新监视应用的显示。
- [DidDeactivate](xref:WatchKit.WKInterfaceController.DidDeactivate)用户消除了 Watch 应用后，将调用此方法。 此方法返回后，将无法修改用户界面控件，直到下一次调用 `WillActivate`。 如果与 iPhone 的连接断开，还会调用此方法。
- 停用扩展后，程序将无法访问该扩展。 **将不**会调用挂起的异步函数。 手表工具包扩展不能使用后台处理模式。 如果用户重新激活了该程序，但该应用程序尚未被操作系统终止，则将 `WillActivate`第一个调用的方法。

![](intro-to-watchos-images/wkinterfacecontrollerlifecycle.png "Application Lifecycle overview")

## <a name="types-of-user-interface"></a>用户界面的类型

有三种类型的用户可与手表应用交互。
所有使用 `WKInterfaceController`的自定义子类别进行编程，因此，之前讨论的生命周期序列会广泛应用（通知是使用 `WKUserNotificationController`的子类进行编程的，后者本身是 `WKInterfaceController`的子类）：

### <a name="normal-interaction"></a>正常交互

大多数监视应用/扩展交互将与你编写的 `WKInterfaceController` 的子类结合在手表应用的**界面中。** [安装](~/ios/watchos/get-started/installation.md)和[入门](~/ios/watchos/get-started/index.md)文章中对此进行了详细介绍。
下图显示了 "[观看工具包目录](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)" 示例的情节提要的一部分。 对于此处显示的每个场景，扩展项目中都有相应的自定义 `WKInterfaceController` （`LabelDetailController`、`ButtonDetailController`、`SwitchDetailController`等）。

![](intro-to-watchos-images/scenes.png "Normal Interaction examples")

### <a name="notifications"></a>通知

[通知](~/ios/watchos/platform/notifications.md)是 Apple Watch 的主要用例。 支持本地和远程通知。 与通知的交互发生在两个阶段中，称为短期和长期。

简短外观显示，并显示 "监视应用" 图标、名称和标题（如 `WKInterfaceController.SetTitle`所指定）。

长时间的外观将系统提供的**窗扇**区域和 "解除" 按钮与自定义的基于情节提要的内容合并在一起。

`WKUserNotificationInterfaceController` 将 `WKInterfaceController` 与 `DidReceiveLocalNotification` 和 `DidReceiveRemoteNotification`的方法进行扩展。
重写这些方法以响应通知事件。

有关通知 UI 设计的详细信息，请参阅[Apple Watch 人体学接口准则](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/Notifications.html#//apple_ref/doc/uid/TP40014992-CH20-SW1)

![](intro-to-watchos-images/notifications.png "Sample notifications")

## <a name="screen-sizes"></a>屏幕大小

Apple Watch 有两种表面大小：38mm 和42mm，两者都具有5:4 显示比例和 Retina 显示。 可用大小为：

- 38mm： 136 x 170 逻辑像素（272 x 340 物理像素）
- 42mm： 156 x 195 逻辑像素（312 x 390 物理像素）。

使用 `WKInterfaceDevice.ScreenBounds` 确定监视应用正在运行的显示。

通常，通过更受限制的38mm 显示来开发文本和布局设计会更容易，然后增加。
如果从较大的环境开始，缩小可能会导致不太好的重叠或文本截断。

阅读更多有关使用[屏幕大小的](~/ios/watchos/app-fundamentals/screen-sizes.md)信息。

## <a name="limitations-of-watchos"></a>WatchOS 的限制

开发 watchOS 应用时，需要注意一些限制：

- Apple Watch 设备具有有限的存储空间，请在下载大文件之前注意可用空间（例如 音频或电影文件）。

- 许多 watchOS[控件](~/ios/watchos/user-interface/index.md)在 UIKit 中具有物，但它们是不同的类（`WKInterfaceButton` 而不是 `UIButton`、`WKInterfaceSwitch` `UISwitch`等），并且具有一组有限的方法与 UIKit 等效项相比。 此外，watchOS 还提供了一些控件，如 `WKInterfaceDate` （用于显示日期和时间），UIKit 不具有该功能。

  - 不能将通知仅路由到手表，也不能将通知路由到仅限 iPhone （用户已通过路由的控件）。

一些其他已知限制/常见问题：

- Apple 将不允许第三方自定义监视面。

- 允许监视控制连接的电话上的 iTunes 的 Api 是专用的。

## <a name="further-reading"></a>其他阅读材料

查看 Apple 的文档：

- [针对手表工具包进行开发](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/index.html#//apple_ref/doc/uid/TP40014969-CH8-SW1)

- [观看工具包编程指南](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/DesigningaWatchKitApp.html)

- [Apple Watch 人体学接口准则](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/index.html#//apple_ref/doc/uid/TP40014992-CH3-SW1)

## <a name="related-links"></a>相关链接

- [watchOS 3 目录（示例）](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [watchOS 1 目录（示例）](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [设置和安装](~/ios/watchos/get-started/installation.md)
- [第一次观看应用视频](https://blog.xamarin.com/your-first-watch-kit-app/)
- [Apple 开发观看套件指南](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/index.html)
- [Apple 的 WatchKit 提示](https://developer.apple.com/watchkit/tips/)
- [watchOS 3 简介](~/ios/watchos/platform/introduction-to-watchos3/index.md)
