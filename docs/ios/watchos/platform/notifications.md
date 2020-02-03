---
title: watchOS 中 Xamarin 的通知
description: 本文档介绍如何使用 Xamarin 中的 watchOS 通知。 它讨论了创建通知控制器，生成通知，并将测试通知。
ms.prod: xamarin
ms.assetid: 0BC1306E-0713-4592-996E-7530CCF281E7
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 85a55967446da5cf89e8ce19dadf88d0de16d80a
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76725067"
---
# <a name="watchos-notifications-in-xamarin"></a>watchOS 中 Xamarin 的通知

如果包含 iOS 应用程序支持它们，watch 应用可以接收通知。 存在内置的通知处理，因此你无*需*添加下面所述的其他通知支持，但是，如果你想要自定义通知行为和外观，请继续阅读。

有关将通知支持添加到解决方案中的 iOS 应用的详细信息，请参阅[IOS 通知](~/ios/platform/user-notifications/deprecated/index.md)文档。

## <a name="creating-notification-controllers"></a>创建通知控制器

情节提要上通知控制器有一种特殊类型的 segue 触发它们。 将新的**通知界面控制器**拖动到情节提要时，它会自动连接到 segue：

![](notifications-images/notification-storyboard1.png "A new Notification Interface Controller with a segue attached")

选择通知的 segue 时您可以编辑其属性：

![](notifications-images/notification-storyboard2.png "The notification segue selected")

自定义控制器后，它可能看起来如以下示例从 WatchKitCatalog:

![](notifications-images/notifications-segue.png "The Notification Properties")

有两种类型的通知：

- 系统定义的**简短**无滚动静态视图。

- 你定义的**长时间**可滚动、可自定义的视图！ 可以指定一个更简单的、 静态版本和更复杂的动态版本。

### <a name="short-look-notification-controller"></a>短查看通知控制器

简短介绍 UI 包含只是应用程序图标、 应用程序名称和通知标题字符串。

如果用户不会忽略该通知，系统将自动切换到提供的详细信息的长时间查看通知。

### <a name="long-look-notification-controller"></a>长时间查看通知控制器

操作系统决定是否显示基于许多因素的静态或动态视图。 必须提供静态接口，并可以选择性地还包括了动态接口以通知。

#### <a name="static"></a>静态

静态视图应该既简单又快速显示。

![](notifications-images/notification-static.png "The static view")

#### <a name="dynamic"></a>Dynamic

动态视图可以显示更多数据，并提供更多交互性。

![](notifications-images/notification-dynamic.png "The dynamic view")

## <a name="generating-notifications"></a>生成通知

通知可以来自远程服务器，也可以在 iOS 应用中本地生成。

有关如何生成本地通知的示例，请参阅[IOS 通知演练](~/ios/platform/user-notifications/deprecated/local-notifications-in-ios-walkthrough.md)。

本地通知必须将 `AlertTitle` 设置为显示在 Apple Watch 上-`AlertTitle` 的字符串显示在 "简略外观" 界面中。 `AlertTitle` 和 `AlertBody` 都显示在 "通知" 列表中;并且 `AlertBody` 显示在 "长期" 界面中。

此屏幕截图显示在 "通知" 列表中显示的 `AlertTitle`，并且 `AlertBody` 显示在 "长外观" 界面中：

![](notifications-images/watch-notificationslist-sml.png "此屏幕截图显示在 "通知" 列表中显示的 AlertTitle")![](notifications-images/watch-notificationcontroller-sml.png "显示在 "长外观" 界面中的 AlertBody")

## <a name="testing-notifications"></a>测试通知

只能在设备上正确测试通知（本地和远程），但可以在 iOS 模拟器中使用**json**文件对其进行模拟。

### <a name="testing-on-apple-watch"></a>在 Apple Watch 上进行测试

在 Apple Watch 上测试通知时，请记住[Apple 的文档](https://developer.apple.com/library/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/BasicSupport.html)会指出以下内容：

> 当用户的 iPhone 上的一个应用程序的本地或远程通知到达时，iOS 将决定是否在 iPhone 上或在 Apple Watch 上显示该通知。

这这一事实 alluding 该 iOS 决定是否在 iPhone 或 Watch 上将出现一条通知。 如果在收到通知时，配对的 iPhone 处于活动状态，则可能会在 iPhone 上显示通知，而*不*会将其路由到手表。

若要确保 watch 上会显示通知，请关闭 iPhone 屏幕 （一次按电源按钮） 或让其进入睡眠状态。 如果配对的监视处于范围内，已通电并且佩戴手腕上，通知会存在路由和监视文件 （伴随细微） 上显示。

### <a name="testing-on-the-ios-simulator"></a>在 iOS 模拟器上测试

在 iOS 模拟器中测试通知模式时，*必须*提供测试 JSON 有效负载。 在 Visual Studio for Mac 的 "**自定义执行参数**" 窗口中设置路径。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

当监视扩展设置为**启动项目**时，Visual Studio for Mac 将显示其他选项。
右键单击 "监视扩展" 项目，然后选择 "**运行 > 自定义参数 ...** "：

[![](notifications-images/runwith-customparams-sml.png "Running with Custom Properties")](notifications-images/runwith-customparams.png#lightbox)

这会打开 "**执行参数**" 窗口，其中包含 " **WatchKit** " 选项卡。选择**通知**并提供 JSON 负载，然后按 "**执行**" 在模拟器中启动 watch 应用：

[![](notifications-images/runwith-execargs-sml.png "Select Notification Payload Default")](notifications-images/runwith-execargs.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

若要在 Visual Studio 中设置测试通知负载，请右键单击监视扩展以编辑**项目属性**。 中转到 "**调试**" 部分，并从列表中选择一个通知 json 文件（它会自动列出项目中包含的所有 json 文件）。

[![](notifications-images/runwith-execargs-sml-vs.png "Select a notifications JSON file")](notifications-images/runwith-execargs-vs.png#lightbox)

当监视扩展为**启动项目**时，Visual Studio 将显示其他选项，如下所示。 选择其中一个**通知**选项以在**通知**模式下启动 "监视应用" （使用在 "属性" 窗口中选择的 JSON 文件）：

![](notifications-images/runwith-vs.png "The Device menu")

-----

在模拟器上测试使用默认有效负载的 JSON 文件时，默认通知控制器如下所示：

![](notifications-images/notification-debug-sml.png "An example notification")

还可以使用[命令行](~/ios/watchos/troubleshooting.md#command_line)启动 iOS 模拟器。

### <a name="example-notification-payload"></a>示例通知有效负载

在 "[监视工具包目录](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)" 示例中，有一个示例负载 JSON 文件**NotificationPayload** （如下所列）。

```json
{
    "aps": {
        "alert": "Test message content",
        "title": "Optional title",
        "category": "myCategory"
        },

        "WatchKit Simulator Actions": [
        {
            "title": "First Button",
            "identifier": "firstButtonAction"
        }
        ],

        "customKey": "Use this file to define a testing payload for your notifications. The aps dictionary specifies the category, alert text and title. The WatchKit Simulator Actions array can provide info for one or more action buttons in addition to the standard Dismiss button. Any other top level keys are custom payload. If you have multiple such JSON files in your project, you'll be able to choose between them in when selecting to debug the notification interface of your Watch App."
    }
```

## <a name="related-links"></a>相关链接

- [WatchKitCatalog （示例）](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Apple 的手表套件通知文档](https://developer.apple.com/library/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/BasicSupport.html)
