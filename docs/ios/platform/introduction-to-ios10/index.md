---
title: iOS 10 简介
description: 本文介绍适用于 Xamarin.iOS 开发人员的所有新的和修改 Api 和 iOS 10 中提供的功能。
ms.prod: xamarin
ms.assetid: FB91DFFE-CF5E-4253-92CB-78A6371259D9
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/29/2017
ms.openlocfilehash: b018fe343a7d46f1323119b03a22cc3831a02d9f
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61402398"
---
# <a name="introduction-to-ios-10"></a>iOS 10 简介

_本文介绍适用于 Xamarin.iOS 开发人员的所有新的和修改 Api 和 iOS 10 中提供的功能。_

## <a name="introducing-ios-10"></a>引入了 iOS 10

使用新的 iOS 10 SDK，Apple 已包含新 Api 和服务，可让开发人员创建的应用和功能的新类别。 IOS 应用程序现在可以扩展消息、 Siri、 电话和地图应用程序以对以前不可用的最终用户提供丰富、 更有吸引力的功能。

有关 iOS 10 的详细信息，请参阅 Apple [iOS + 应用](https://developer.apple.com/ios/)文档。

## <a name="whats-new-in-ios-10"></a>What's New iOS 10 中

Apple 在 iOS 10 中以及对现有功能，包括许多增强功能已添加多个新 Api 和服务：


## <a name="adapting-to-the-true-tone-display"></a>适应 True 音显示

Apple 的 True 音显示技术使用 iOS 设备中的环境光线传感器来动态调整颜色和强度的显示效果以匹配当前的光照条件。 iOS 10 提供了新[UIWhitePointAdaptivityStyle](https://developer.apple.com/library/prerelease/content/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html#//apple_ref/doc/uid/TP40009252-SW31)可以添加到应用的密钥`Info.plist`文件，并控制，则返回 True 音应用标准颜色变化的方式。 

可用值如下：

- `UIWhitePointAdaptivityStyleStandard` **默认**-使用标准的白色点 adaptivity。
- `UIWhitePointAdaptivityStyleReading` -用于读取为中心的应用。
- `UIWhitePointAdaptivityStyleGame` -用于专注于游戏的应用。
- `UIWhitePointAdaptivityStyleVideo` -用于专注于视频的应用。
- `UIWhitePointAdaptivityStylePhoto` -使用专注于摄影的应用的色彩保真度所在环境的白色点调整比更重要。

<a name="app-extensions" />

## <a name="app-extensions"></a>应用扩展

Apple 提供了几个新的应用程序扩展点在 iOS 10 中：

- 调用目录
- Intents 和 Intents UI
- 消息
- 通知内容
- Notification Services
- 不干胶标签包

此外，第三方键盘应用扩展具有以下增强功能：

- 新`DocumentInputMode`属性的`UITextDocumentProxy`类可以确定文档的输入的语言并允许键盘扩展为与该语言一致。
- 新`HandleInputModeList`方法，可以显示所点击的全球范围内键的响应中系统的键盘选取器菜单的键盘扩展。

有关详细信息，请参阅我们[扩展插件简介](~/ios/platform/extensions.md)，[消息应用集成](~/ios/platform/message-app-integration/index.md)，[简介主动建议](~/ios/platform/search/proactive-suggestions.md)， [简介 SiriKit](~/ios/platform/sirikit/index.md)，[简介用户通知](~/ios/platform/user-notifications/index.md)和 Apple[应用扩展编程指南](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214)。

## <a name="app-search-enhancements"></a>应用搜索增强功能

核心聚焦在 iOS 10 如提供对应用程序搜索的多项增强功能：

- **众深层链接受欢迎程度 （与差异隐私）** -提供了一种方法来升级在搜索结果中的深层链接应用内容。
- **应用内搜索**-使用新`CSSearchQuery`类以提供应用程序内 Spotlight 搜索功能对邮件、 消息和说明应用程序的工作方式类似。
- **搜索延续**-用户可以启动搜索在聚焦或 Safari，然后打开应用并继续搜索。
- **验证结果的可视化效果**-Apple[应用搜索 API 验证工具](https://search.developer.apple.com/appsearch-validation-tool)时只有测试现在会显示网站的标记和深度链接的可视表示形式。
- **消息应用映像共享**-允许在消息 （通过消息应用扩展） 中的共享 Spotlight 搜索中显示为提供的常用应用内映像。

若要获取详细信息，请参阅我们[应用搜索增强功能](~/ios/platform/search/app-search-enhancements.md)指南。

## <a name="apple-pay-enhancements"></a>Apple Pay 增强功能

Apple 已对 Apple Pay 进行多项增强功能，允许用户从网站和通过与 Siri 和映射进行交互的安全还的 iOS 10。

IOS 10，几个新 Api 添加了采用了 iOS 和 watchOS 以支持动态付款网络和新的沙盒测试环境。

此外，PassKit 框架已扩展为支持 Apple Pay 之外`UIKit`，并允许卡颁发者提供他们从其应用程序中的卡。

若要获取详细信息，请参阅我们[Apple 支付增强功能](~/ios/platform/apple-pay.md)指南。

## <a name="alternate-app-icons"></a>备用应用图标

Apple 向 iOS 10.3 的允许的应用程序来管理其图标添加了多项增强功能：

 - `ApplicationIconBadgeNumber` -获取或设置应用图标徽章中 Springboard。
 - `SupportsAlternateIcons` -如果`true`应用具有一组替代的图标。
 - `AlternateIconName` -返回当前选定的备用图标的名称或`null`如果使用的主图标。
 - `SetAlternameIconName` -使用此方法以切换到给定替换图标的应用程序的图标。

若要获取详细信息，请参阅我们[备用应用图标](~/ios/app-fundamentals/images-icons/alternate-app-icons.md)指南。


## <a name="introduction-to-callkit"></a>CallKit 简介

在 iOS 10 中新的 CallKit API 提供用于 VOIP 的应用程序将与 iPhone UI 集成并提供熟悉的界面，然后向最终用户体验的方法。 使用此 API 时，用户可以查看和与 VOIP 呼叫从 iOS 设备锁定屏幕进行交互和管理使用的手机应用的联系人**收藏夹**并**最近**视图。

此外，CallKit API 提供的功能来创建应用扩展，可以将电话号码与名称 (调用方 ID) 相关联或者告知系统应将数字时被阻止 （阻止调用）。

若要获取详细信息，请参阅我们[简介 Callkit](~/ios/platform/callkit.md)指南。

## <a name="message-app-integration"></a>消息应用集成

iOS 10 中与集成的 Xamarin.iOS 解决方案允许包含的消息应用扩展**消息**应用并提供新功能提供给用户。 该扩展可以发送文本、 贴纸、 媒体文件和交互式消息。 提供了两种类型的消息应用扩展：

- **不干胶标签包**-包含一系列可以将用户添加到一条消息的贴纸。 不干胶标签包可以创建无需编写任何代码。
- **iMessage 应用**-可以显示用于选择不干胶标签、 输入文本，包括 （具有可选类型转换） 的媒体文件和创建、 编辑和发送交互消息在 Messages 应用内自定义用户界面。

若要获取详细信息，请参阅我们[消息应用集成](~/ios/platform/message-app-integration/index.md)指南。

## <a name="news-publisher-enhancements"></a>新闻发布服务器增强功能

与 iOS 10，Apple 将允许主要杂志和博客作者以及独立发布者注册和产品的新组织中的任何人，并将内容传送到 Apple 新闻应用。 若要了解详细信息，请参阅 Apple[新闻资源](https://newsresources.apple.com/)文档。

## <a name="providing-haptic-feedback"></a>提供 Haptic 反馈

在 iPhone 7 和 iPhone 7 以及 Apple 已包含提供以物理方式吸引用户的其他方法的新 haptics 响应。 使用新的边用反馈选项来获取用户的注意力并加强其操作。

多个内置的 UI 元素已提供 haptic 反馈，例如选取器、 交换机和滑块。 iOS 10 现在将能够以编程方式触发 haptics 使用的具体子类`UIFeedbackGenerator`类。

若要获取详细信息，请参阅我们[提供 Haptic 反馈](~/ios/user-interface/ios-ui/haptic-feedback.md)指南。

## <a name="proactive-suggestions"></a>主动建议

iOS 10 展示驾驶参与到应用程序的新方法使系统能够主动有用的信息自动向用户显示在适当的时间。 只需为 iOS 9 提供了 iOS 10 应用程序可以公开可以从系统中的以下位置提供给用户的功能使用 Spotlight、 切换和 Siri 建议向应用添加深层搜索的功能：

- 应用程序切换器
- 在锁定屏幕
- CarPlay
- 映射
- 使用 Siri 交互
- QuickType 建议 

应用程序公开此功能对系统使用一系列技术，如[NSUserActivity](xref:Foundation.NSUserActivity)，web 标记中，核心聚焦、 MapKit、 Media Player 和 UIKit。

若要获取详细信息，请参阅我们[简介主动建议](~/ios/platform/search/proactive-suggestions.md)指南。

## <a name="request-app-review"></a>请求应用评审

向 iOS 10.3，新`RequestReview()`方法允许 iOS 应用程序要求用户进行评估或查看它。 虽然可以在有意义的用户体验中的任意位置调用此方法，审核过程是控制，并由应用商店策略处理。 因此，此方法可能会或可能不会显示警报并应永远不会调用以响应用户操作，例如点击一个按钮。

若要获取详细信息，请参阅我们[请求应用评审](~/ios/platform/request-app-review.md)指南。

## <a name="security-and-privacy-enhancements"></a>安全和隐私的增强功能

对安全和隐私 iOS 10，可帮助开发人员提高他们的应用的安全性，并确保最终用户的隐私，Apple 进行了多项增强功能。

因此，iOS 10 （或更高版本） 上运行的应用必须以静态方式声明其意图，通过输入一个或多个隐私中的特定项来访问特定功能或用户信息及其`Info.plist`向该应用程序为何希望获得访问权限的用户说明的文件。

若要获取详细信息，请参阅我们[安全性和隐私增强功能](~/ios/app-fundamentals/security-privacy.md)指南。

## <a name="sirikit"></a>SiriKit

新 ios 10、 通过 SiriKit，Xamarin.iOS 应用程序提供的 iOS 设备上使用 Siri 用户可访问的服务。 此功能提供一个或多个应用扩展中使用新**意向**并**Intents UI**框架。

SiriKit 支持以下服务域：

- 音频或视频呼叫。
- 预订滑水感觉的时间。
- 管理锻炼。
- 消息传递。
- 正在搜索照片。
- 发送或接收付款。

当用户发出的 Siri 请求涉及应用扩展的服务之一时，SiriKit 发送该扩展**意向**对象，描述用户的请求和任何支持的数据。 然后，应用扩展生成的相应**响应**对象的给定**意向**，详细介绍如何扩展可以处理该请求。

应用扩展时使用 Siri 通常处理所有用户交互，可以使用**意向 UI**框架提供丰富的自定义用户界面提供应用程序的品牌和其他信息。

若要获取详细信息，请参阅我们[简介 SiriKit](~/ios/platform/sirikit/index.md)指南。

## <a name="speech-recognition"></a>语音识别

iOS 10 包括一个新的语音 API，允许应用支持连续语音识别和转录 （从直播或录制的音频流） 的语音到文本。

由于语音识别要求传输和 Apple 的服务器，该应用程序上的数据的临时存储_必须_请求通过包括执行识别的用户的权限`NSSpeechRecognitionUsageDescription`键中其`Info.plist`文件和调用`SFSpeechRecognizer.RequestAutorization`方法。

若要获取详细信息，请参阅我们[简介语音识别](~/ios/platform/speech.md)指南。

## <a name="user-notifications"></a>用户通知

新 iOS 10，其用户通知框架允许传递和处理本地和远程通知。 使用此框架，应用程序或应用程序扩展插件可以计划本地通知的传递通过指定一组条件，如位置或一天的时间。

此外，应用或扩展可以接收 （并可能修改） 本地和远程通知在传递到用户的 iOS 设备。

新的用户通知 UI 框架允许应用或应用扩展时向用户显示自定义的本地和远程通知的外观。

若要获取详细信息，请参阅我们[用户通知框架](~/ios/platform/user-notifications/index.md)指南。

## <a name="video-subscriber-account"></a>视频订户帐户

新建适用于 iOS 10，视频订户帐户框架允许应用该身份验证支持的流式处理或点播视频来向其有线或卫星电视提供商为最终用户使用单一登录体验。

## <a name="wide-color"></a>宽域颜色

iOS 10 扩展的扩展范围像素格式和整个系统的核心图形、 Core 映像、 裸机和 AVFoundation 等框架的范围内所有颜色空间的支持。 对具有广泛的颜色显示设备的支持进一步减轻通过提供在整个图形堆栈整个此行为。

此外， [UIKit](xref:UIKit)已修改才能在新扩展**sRGB**色彩空间，使其更轻松地混合中广泛的颜色色域显著的性能损失的颜色。

使用宽颜色时，Apple 提供的以下最佳实践：

- [UIColor](xref:UIKit.UIColor)现在使用 sRGB 颜色空间，将无法再将值与`0.0`到`1.0`范围。 如果应用依赖于上一次固定行为，它将需要修改适用于 iOS 10。
- 当执行自定义绘图环境将进行配置的 sRGB 颜色空间`UIView`在 iPad Pro 上绘制。
- 如果该应用程序执行的自定义呈现`UIImages`，使用新[UIGraphicsImageRender](https://developer.apple.com/reference/uikit/uigraphicsimagerenderer)类指定为扩展范围或标准范围格式的使用。
- 当使用核心图形或金属等低级别 API 提供图像处理，开发人员应使用支持 16 位浮点值的更大范围颜色空间和像素格式。 必要时，开发人员必须手动将颜色组件值。
- 核心图形、 Core 映像和金属性能着色器所有提供两个颜色空间之间进行转换的新方法。

若要获取详细信息，请参阅我们[简介广泛的颜色](~/ios/platform/wide-color.md)指南。

## <a name="widget-enhancements"></a>小组件增强功能

Apple 引入了几个小组件系统，以确保小组件查看任何新的 iOS 存在 10 锁定屏幕的背景上均表现出色的增强功能。 [NotificationCenterVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1613917-notificationcentervibrancyeffect)属性已弃用并已替换为新[WidgetPrimaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771278-widgetprimaryvibrancyeffect)或[WidgetSecondaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771277-widgetsecondaryvibrancyeffect)属性。 此外，小组件现在包含[NCWidgetDisplayMode](https://developer.apple.com/reference/notificationcenter/ncwidgetdisplaymode)属性允许开发人员来描述多少内容是否可用并允许用户展开和折叠内容。

若要获取详细信息，请参阅我们[搜索和主页屏幕小组件增强功能](~/ios/platform/search/widgets.md)指南。

## <a name="additional-framework-changes"></a>其他 Framework 更改

除了主要 framework 更改和添加上面列出的内容，Apple 进行了许多其他的次要 framework 更改在 iOS 10。

若要获取详细信息，请参阅我们[其他 Framework 更改](~/ios/platform/introduction-to-ios10/additional-framework-changes.md)指南。

## <a name="deprecated-apis"></a>弃用的 API

在 iOS 10 中不推荐使用以下 Api:

- `CKDiscoverAllContactsOperation`， `CKDiscoveredUserInfo`，`CKDiscoverUserInfosOperation`和`CKFetchRecordChangesOperation`类中已弃用 CloudKit 适用于 iOS 10。 使用[CKDiscoverAllUserIdentitiesOperation](xref:CloudKit.CKDiscoverUserIdentitiesOperation)， [CKUserIdentity](xref:CloudKit.CKUserIdentity)并[CKFetchRecordZoneChangesOperation](xref:CloudKit.CKFetchRecordZoneChangesOperation)类 （它们支持记录共享） 相反。
- 多个[CKSubscription](https://developer.apple.com/reference/cloudkit/cksubscription)已弃用的 Api （如基于区域和基于查询的订阅）。 使用[CKRecordZoneSubscription](xref:CloudKit.CKRecordZoneSubscription)并[CKQuerySubscription](xref:CloudKit.CKQuerySubscription) Api 相反。
- [NSPersistentStoreCoordnator](xref:CoreData.NSPersistentStoreCoordinator)与通用内容相关的符号已被弃用。
- `ADBannerView``ADInterstitialAd`和相关中的符号[UIViewController](xref:UIKit.UIViewController)类已被弃用。
- [SKUniform](https://developer.apple.com/reference/spritekit/skuniform)与浮点值的符号已被弃用。
- `UILocalNotification`， `UIMutableUserNotificationAction`， `UIMutableUserNotificationCategory`， `UIUserNotificationAction`，`UIUserNotificationCategory`和`UIUserNotificationSettings`UIKit 类已被弃用。 使用[用户通知](#user-notifications)framework 相反。
- `HandleActionForLocalNotification`， `HandleActionForRemoteNotification`，`DidReceiveLocalNotification`和`DidReceiveRemoteNotification`WatchKit 方法已被弃用。 使用`HandleActionForNotification`和`DidReceiveNotification`方法相反。
- `DidReceiveLocalNotification`并`DidReceiveRemoteNotification`方法的[WKExtensionDelegate](https://developer.apple.com/reference/watchkit/wkextensiondelegate)已弃用。 创建的实例[UNUserNotificationCenterDelegate](https://developer.apple.com/reference/usernotifications/unusernotificationcenterdelegate)的实现适当的方法，并将其分配给`Delegate`的属性[UNUserNotificationCenter](https://developer.apple.com/reference/usernotifications/unusernotificationcenter)对象。
- **游戏中心应用**已弃用，从 iOS 中删除。 如果应用使用 GameKit，其_必须_提供其自己的界面以显示 GameKit 功能，例如排行榜，等等。

请参阅 Apple [iOS 9.3 用于 iOS 10.0 API 差异](https://developer.apple.com/library/prerelease/content/releasenotes/General/iOS10APIDiffs/index.html)弃用功能的文档的完整列表。



## <a name="related-links"></a>相关链接

- [iOS 10 示例](https://developer.xamarin.com/samples/ios/iOS10/)
- [什么是 iOS 10 中的新增功能](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewIniOS/Articles/iOS10.html#//apple_ref/doc/uid/TP40017084-SW1)
