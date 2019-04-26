---
title: watchOS 3 简介
description: 本文介绍适用于 Xamarin 开发人员的所有新的和修改 Api 和 watchOS 3 中提供的功能。
ms.prod: xamarin
ms.assetid: B8ABE1E1-8688-4262-BE66-A16813C2D671
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 10/07/2017
ms.openlocfilehash: 0428a0df157e359ab34a6a71dbba31bdeb6962fa
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61224057"
---
# <a name="introduction-to-watchos-3"></a>watchOS 3 简介

_本文介绍适用于 Xamarin 开发人员的所有新的和修改 Api 和 watchOS 3 中提供的功能。_

本文档将介绍以下主题：

- [What's New watchOS 3 中](#Whats-New-in-watchOS-3)
    - [Apple 支付增强功能](#Apple-Pay-Enhancements)Apple Watch 上添加为应用程序内付款的支持。
    - [后台任务](#Background-Tasks)使能够更新其在后台中的信息，以便当用户需要它时，它已准备的应用程序。
    - [复杂情况增强功能](#Complications-Enhancements)已用于 watchOS 3 的应用程序提供新功能。
    - [新可用的框架](#Newly-Available-Frameworks)已公开的 watchOS 应用。
    - [主动建议](#Proactive-Suggestions)允许此应用主动向用户显示信息。
    * 多个[安全性和隐私增强功能](#Security-and-Privacy-Enhancements)已对 watchOS 3。
    - [快照和停靠](#Snapshots-and-Dock)用户提供快速访问应用 watchOS 应用。
    - [用户通知](#User-Notifications)向用户提供本地和远程通知。
    * 多个[监视连接框架增强功能](#Watch-Connectivity-Framework-Enhancements)watchOS 3 中进行。
    * 多个[WatchKit Framework 增强功能](#WatchKit-Framework-Enhancements)watchOS 3 中进行。
    - [测验应用增强功能](#Workout-App-Enhancements)健身提供新功能相关的 Apple Watch 应用。
- [其他框架更改](#Additional-Framework-Changes)进行了整个 watchOS 3。
- [已弃用的 Api](#Deprecated-APIs) watchOS 3 中。

<a name="Whats-New-in-watchOS-3" />

## <a name="whats-new-in-watchos-3"></a>What's New watchOS 3 中

Apple 已以及对现有功能，包括许多增强功能的 watchOS 3 中添加几个新 Api 和服务：

<a name="Apple-Pay-Enhancements" />

## <a name="apple-pay-enhancements"></a>Apple Pay 增强功能

在 watchOS 3，PassKit 框架已扩展为允许安全应用程序内付款 （的实体商品和服务） 在 Apple Watch 上运行的应用程序的支持。

使用新[PKPaymentAuthorizationController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontroller)并[PKPaymentAuthorizationControllerDelegate](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontrollerdelegate)类和的接口，用户可以授权支付的请求响应程序。

若要获取详细信息，请参阅我们[Apple 支付增强功能](~/ios/watchos/platform/apple-pay.md)指南。

<a name="Background-Tasks" />

## <a name="background-tasks"></a>后台任务

watchOS 3 引入了多个应用程序可用于更新用户需要打开它之前确保它具有内容其信息的后台任务。

以下新的后台任务有：

- **后台应用程序刷新**- [WKApplicationRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkapplicationrefreshbackgroundtask)任务，应用程序以更新其在后台中的状态。 通常，这将包括另一个任务，例如从 internet 中使用下载新内容[NSUrlSession](https://developer.apple.com/reference/foundation/nsurlsession)。
- **后台刷新快照**- [WKSnapshotRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wksnapshotrefreshbackgroundtask)任务，应用程序以更新其内容和 UI 之前系统采用将用于填充停靠的快照。
- **监视连接的背景**- [WKWatchConnectivityRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkwatchconnectivityrefreshbackgroundtask)从配对的 iPhone 接收背景数据时，应用程序启动的任务。
- **后台 URL 会话**- [WKURLSessionRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkurlsessionrefreshbackgroundtask)后台传输需要授权或完成 （成功或错误） 时，应用程序启动的任务。

若要获取详细信息，请参阅我们[后台任务](~/ios/watchos/platform/background-tasks.md)指南。

<a name="Complications-Enhancements" />

## <a name="complications-enhancements"></a>复杂情况增强功能

复杂性是提供一目了然的有用信息的小型可视元素。 具体取决于所选手表表盘，用户必须具有一个或多个复杂手表表盘的自定义功能。

watchOS 3 为该应用，以便用户可以访问其信息在快速从手表表盘创建监视应用程序的一个或多个复杂的功能。

此外，问题提供以下优势：

- 用户可以快速启动应用，通过直接从手表表盘复杂性上点击。
- 具有一个应用程序的复杂性上的监视人脸原因系统以保持在就绪启动状态，它会尝试启动该应用程序在后台，该应用程序将其保存在内存和向提供了其额外时间更新。
- 复杂性被保证至少 50 个每日推送更新。
- 如果应用包含复杂情况，它将具有在 Apple Watch 人脸库中 (请参阅 Apple 的[添加到库的复杂情况](https://developer.apple.com/documentation/clockkit/adding_complications_to_the_gallery)文档了解详细信息)。

在 watchOS 3，ClockKit 框架现在包括多个用于特大规模的并发数据的新模板如[CLKComplicationTemplateExtraLargeColumnsText](https://developer.apple.com/reference/clockkit/clkcomplicationtemplateextralargecolumnstext)和[CLKComplicationTemplateExtraLargeRingImage](https://developer.apple.com/reference/clockkit/clkcomplicationtemplateextralargeringimage). 此外，若要创建可本地化的文本，可使用的新方法[CLKTextProvider](https://developer.apple.com/reference/clockkit/clktextprovider)类。

若要获取详细信息，请参阅我们[快速交互技术用于 watchOS 3](~/ios/watchos/platform/quick-interaction-techniques.md)指南。

<a name="Newly-Available-Frameworks" />

## <a name="newly-available-frameworks"></a>最新可用的框架

watchOS 3 包括如以前不可用的几个现有的 Apple 框架：

- **SceneKit** -使用 SceneKit 包括 3D 建模到 watch 应用的用户界面，包括大多数物理照明，明暗度的动画，正如其他平台上提供的功能和粒子系统。 不支持 3D 空间的音频、 自定义配置文件或 OpenGL 的着色器、 Core 映像筛选器和基于以物理方式的材料。
- **SpriteKit** -使用 SpriteKit 呈现并进行动画处理中包括的大多数操作、 物理特性、 照明和粒子系统等其他平台上可用的功能的应用程序监视应用程序的 UI 的 sprite。 不支持 3D 空间的音频、 视频播放和核心映像筛选器。
- **AVFoundation** -若要将管理和播放音频。
- **CloudKit** -监视应用程序和 iCloud 容器之间移动数据。
- **核心音频**-若要管理的数据类型用于表示音频流、 复杂缓冲区和时间值。
- **GameKit** -若要创建社交游戏。

<a name="Proactive-Suggestions" />

## <a name="proactive-suggestions"></a>主动建议

watchOS 3 允许此应用主动向内的用户显示信息提供上下文。 若要支持此功能， [NSUserActivity](https://developer.apple.com/reference/foundation/nsuseractivity)现在包括`MapItem`使应用可以通过其他应用提供位置信息以供将来使用的属性。

若要获取详细信息，请参阅我们[简介主动建议](~/ios/watchos/platform/proactive-suggestions.md)指南。

<a name="Security-and-Privacy-Enhancements" />

## <a name="security-and-privacy-enhancements"></a>安全和隐私的增强功能

对安全和隐私 watchOS 3，可帮助开发人员提高他们的应用的安全性，并确保最终用户的隐私，Apple 进行了多项增强功能。

因此，watchOS 3 （或更高版本） 上运行的应用必须以静态方式声明其意图，通过输入一个或多个隐私中的特定项来访问特定功能或用户信息及其`Info.plist`向该应用程序为何希望获得访问权限的用户说明的文件。

由于 watchOS 3 与 iOS 10 共享这些更改，请参阅我们的 iOS 10[安全性和隐私增强功能](~/ios/app-fundamentals/security-privacy.md)指南以获取详细信息。

<a name="Snapshots-and-Dock" />

## <a name="snapshots-and-dock"></a>快照和停靠

在 watchOS 3，Apple 已添加了用户可以将固定其最喜欢的应用并快速访问它们的停靠。 当用户在 Apple Watch 上按端按钮时，将显示一系列固定的应用快照。 左或向右查找所需的应用，然后点击以启动该快照替换为正在运行的应用界面的应用，用户可以往下轻扫。

系统定期将快照应用程序的 UI，并使用这些快照来填充文档。watchOS 使应用能够更新其内容和 UI 之前拍摄此快照。

有关详细信息，请参阅我们[后台任务](~/ios/watchos/platform/background-tasks.md)指南和 Apple [WKSnapshotRefreshBackgroundTask 引用](https://developer.apple.com/reference/watchkit/wksnapshotrefreshbackgroundtask)。

<a name="User-Notifications" />

## <a name="user-notifications"></a>用户通知

在 watchOS 3 中引入的用户通知框架支持到 Apple Watch 的本地和远程通知的传递。 使用此框架来计划基于特定条件的时间等的一天或位置的通知以及接收和处理通知。

若要获取详细信息，请参阅我们[快速交互技术用于 watchOS 3](~/ios/watchos/platform/quick-interaction-techniques.md)指南。

<a name="Watch-Connectivity-Framework-Enhancements" />

## <a name="watch-connectivity-framework-enhancements"></a>观看连接框架增强功能

新`HasContentPending`的属性[WCSession](https://developer.apple.com/reference/watchconnectivity/wcsession)类指示会话已在后台处理所需接收数据。 和`RemainingComplicationUserInfoTransfers`属性将返回时间，剩余的 iOS 应用可以更新其 watchOS 复杂性。

若要获取详细信息，请参阅我们[后台任务](~/ios/watchos/platform/background-tasks.md)指南。

<a name="WatchKit-Framework-Enhancements" />

## <a name="watchkit-framework-enhancements"></a>WatchKit Framework 增强功能

watchOS 3 包括 WatchKit 框架包括以下几项增强功能：

- 应用程序可以获取数字 Crown 状态使用新[WKCrownSequencer](https://developer.apple.com/reference/watchkit/wkcrownsequencer)类，并在用户旋转 crown 使用时接收更新[WKCrownDelegate](https://developer.apple.com/reference/watchkit/wkcrowndelegate)类。
- [WKExtension](https://developer.apple.com/reference/watchkit/wkextension)类现在包括`ApplicationState`方法并[WKApplicationState](https://developer.apple.com/reference/watchkit/wkapplicationstate)应用可用于跟踪应用程序的运行时状态的常量。 `WKExtension` 此外提供了可用于计划的后台任务的两个新方法。
- [WKExtensionDelegate](https://developer.apple.com/reference/watchkit/wkextensiondelegate)现在包含新的`ApplicationWillEnterForeground`，`ApplicationDidEnterBackground`和`HandleBackgroundTasks`监视应用程序的状态中的更改和处理后台任务更新的方法。
- 一个新[WKGestureRecognizer](https://developer.apple.com/reference/watchkit/wkgesturerecognizer)添加类以提供以下类型的手势识别到 watch 应用：[WKLongPressGestureRecognizer](https://developer.apple.com/reference/watchkit/wklongpressgesturerecognizer)， [WKPanGestureRecognizer](https://developer.apple.com/reference/watchkit/wkpangesturerecognizer)， [WKSwipeGestureRecognizer](https://developer.apple.com/reference/watchkit/wkswipegesturerecognizer)并[WKTapGestureRecognizer](https://developer.apple.com/reference/watchkit/wktapgesturerecognizer)。
- 新[WKinterfaceHMCamera](https://developer.apple.com/reference/watchkit/wkinterfacehmcamera)任何 HomeKit 附加 IP 相机类提供了的接口。
- 新[WKInterfaceInlineMovie](https://developer.apple.com/reference/watchkit/wkinterfaceinlinemovie)类允许应用程序以显示"海报"将在用户点击它时被替换为正在运行的电影的电影。
- 新[WKInterfacePaymentButton](https://developer.apple.com/reference/watchkit/wkinterfacepaymentbutton)类允许应用将启动一个付款请求点击时其 UI 中显示的 Apple Pay 按钮。
- 新[WKInterfaceSCNScene](https://developer.apple.com/reference/watchkit/wkinterfacescnscene)类提供了用于在 Apple Watch 上显示 SceneKit 场景的接口。
- 新[WKInterfaceSKScene](https://developer.apple.com/reference/watchkit/wkinterfaceskscene)类提供了用于在 Apple Watch 上显示 SpriteKit 场景的接口。

若要获取详细信息，请参阅我们[快速交互技术用于 watchOS 3](~/ios/watchos/platform/quick-interaction-techniques.md)指南。

<a name="Workout-App-Enhancements" />

## <a name="workout-app-enhancements"></a>测验应用增强功能

新 watchOS 3，与健身相关应用程序能够在 Apple Watch 上在后台运行。 若要启用此功能 （和获取对 HealthKit 数据访问），该应用程序必须包括`WKBackgroundModes`中的键`Info.plist`文件中的使用值`workout-processing`。

此外，开发人员现在可以启动配对的 iPhone 上的 iOS 应用程序版本中的 watchOS 健身应用程序的功能。

若要获取详细信息，请参阅我们[测验应用增强功能](~/ios/watchos/platform/workout-apps.md)指南。

<a name="Additional-Framework-Changes" />

## <a name="additional-framework-changes"></a>其他 Framework 更改

除了主要 framework 更改和添加上面列出的内容，Apple 进行了许多其他的次要 framework 更改 watchOS 3 中。

若要获取详细信息，请参阅我们[其他 Framework 更改](~/ios/watchos/platform/introduction-to-watchos3/additional-framework-changes.md)指南。

<a name="Deprecated-APIs" />

## <a name="deprecated-apis"></a>弃用的 API

WatchOS 3 中不推荐使用以下 Api:

- `UILocalNotification` UIKit 类已弃用，并应替换为用户通知框架。

请参阅 Apple [watchOS 2.2 watchOS 3.0 API 差异](https://developer.apple.com/library/prerelease/content/releasenotes/General/watchOS30APIDiffs/index.html)的弃用功能和更改的文档的完整列表。


## <a name="related-links"></a>相关链接

- [watchOS 示例](https://developer.xamarin.com/samples/watchos/all/)
- [什么是 watchOS 3 中的新增功能](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewInwatchOS/Articles/watchOS3.html#//apple_ref/doc/uid/TP40017085-SW1)
