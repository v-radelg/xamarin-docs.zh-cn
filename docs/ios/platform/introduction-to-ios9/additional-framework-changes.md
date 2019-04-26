---
title: 其他 iOS 9 框架更改
description: 本文档介绍在 iOS 9 中引入的其他 framework 更改。 它讨论 AVFoundation、 AVKit 和 CloudKit。
ms.prod: xamarin
ms.assetid: CFDE1FC4-9327-402B-95A0-581D4AA0E9D5
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: 5156259f8178da69595464f75a10cd8f41965519
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61293792"
---
# <a name="additional-ios-9-frameworks-changes"></a>其他 iOS 9 框架更改

_本文介绍如何附加的次要更改或增强现有框架，可用于 iOS 9 功能。_

[![](additional-framework-changes-images/ios9-sml.png "iOS 9 徽标")](additional-framework-changes-images/ios9.png#lightbox)

除了 iOS 的主要更改，Apple 已在 iOS 9 中进行修改和改进到多个现有框架。

## <a name="avfoundation-framework-additions"></a>AVFoundation Framework 新增功能

在 AVFoundation framework 中， [AVSpeechSynthesisVoice](xref:AVFoundation.AVSpeechSynthesisVoice)类现在允许你通过除了语言标识符来指定一种声音。

例如，以下代码将获取所有可用的声音的列表：

```csharp
var voices = AVSpeechSynthesisVoice.GetSpeechVoices ();
```

然后可以通过设置为使用一个从列表中的语音`Voice`的实例的属性[AVSpeachUtterance](xref:AVFoundation.AVSpeechUtterance)类。

[AVQueuePlayer](xref:AVFoundation.AVQueuePlayer)类现在支持混合使用 internet 流式处理和基于文件的介质的队列中。 以前的版本可能相同类型的队列介质。

有关详细信息，请参阅 Apple [AVSpeechSynthesisVoice 引用](https://developer.apple.com/library/prerelease/ios/documentation/AVFoundation/Reference/AVSpeechSynthesisVoice_Ref/index.html#//apple_ref/occ/cl/AVSpeechSynthesisVoice)。

## <a name="avkit-framework-additions"></a>AVKit Framework 新增功能

若要使用新的画中画 (PIP) 功能，AVKit framework 包含新的`AVPictureInPictureController`并[AVPlayerViewController](xref:AVKit.AVPlayerViewController)类：

- **AVPictureInPictureController** -此类允许 iOS 9 应用程序以响应用户启动在 iPad 上的 PIP 窗口浮动、 可调整大小中视频播放。
- **AVPlayerViewController** -管理`AVPlayer`控制器用于呈现在 iPad 上的 PIP 窗口浮动、 可调整大小的视频。

有关详细信息，请参阅我们[适用于 iPad 的多任务](~/ios/platform/introduction-to-ios9/index.md#multitasking)文档和 Apple [AVPictureInPictureController 引用](https://developer.apple.com/library/prerelease/ios/documentation/AVKit/Reference/AVPictureInPictureController_Class/index.html#//apple_ref/occ/cl/AVPictureInPictureController)和[AVPlayerViewController 引用](https://developer.apple.com/library/prerelease/ios/documentation/AVFoundation/Reference/AVPlayerViewController_Class/index.html#//apple_ref/occ/cl/AVPlayerViewController).

## <a name="introducing-cloudkit-web-services"></a>CloudKit Web Services 简介

CloudKit 框架简化了开发应用程序的访问 iCloud。 这包括应用程序数据和资产的权限，以及能够安全地存储应用程序信息的检索。 此工具包通过允许访问其 iCloud Id 与应用程序而无需共享个人信息，使用户匿名的层。

新_CloudKit Web 服务_框架提供了一个 JavaScript 库 (CloudKit JS)，可以纳入你的网站以提供对相同 CloudKit 基于数据和内容与 Xamarin.iOS 应用的访问。

> [!IMPORTANT]
> 您可以访问、 显示或更新使用 CloudKit JS CloudKit 数据库中的内容之前，必须具有以前定义的数据库的架构。




有关详细信息，请参阅以下文档：

- [CloudKit 简介](~/ios/data-cloud/intro-to-cloudkit.md)-我们介绍如何在 Xamarin.iOS 应用程序中使用 CloudKit。
- [CloudKit 快速启动](https://developer.apple.com/library/prerelease/ios/documentation/DataManagement/Conceptual/CloudKitQuickStart/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014987)-Apple 的 CloudKit 简介。
- [CloudKit JS 引用](https://developer.apple.com/library/prerelease/ios/documentation/CloudKitJS/Reference/CloudKitJavaScriptReference/index.html#//apple_ref/doc/uid/TP40015359)-Apple 的 CloudKit JS 文档。
- [CloudKit Web 服务引用](https://developer.apple.com/library/prerelease/ios/documentation/DataManagement/Conceptual/CloutKitWebServicesReference/Introduction/Introduction.html#//apple_ref/doc/uid/TP40015240)-Apple 的引用，描述 CloudKit 简介 HTTP 接口。
- [CloudKit 目录：（Cocoa 和 JavaScript） CloudKit 简介](https://developer.apple.com/library/prerelease/ios/samplecode/CloudAtlas/Introduction/Intro.html#//apple_ref/doc/uid/TP40014599)-Apple 的示例应用程序使用 CloudKit 和 CloudKit JS。

> [!IMPORTANT]
> Apple [提供工具](https://developer.apple.com/support/allowing-users-to-manage-data/)，用于帮助开发人员正确处理欧盟一般数据保护条例 (GDPR)。

## <a name="foundation-framework-additions"></a>Foundation Framework 新增功能

Apple iOS 9 中包括对 Foundation 框架的以下更改：

### <a name="changes-to-nsbundle"></a>对 NSBundle 的更改

已对以下更改[NSBundle](xref:Foundation.NSBundle)适用于 iOS 9 的类：

* `GetPreservationPriorityForTag (NSString tag)` -获取具有给定标记的资源的当前保留优先级。 有效的值处于该范围内`0.0`到`1.0`，将首先清除优先级最低的资源。
* `SetPreservationPriorityForTag (double priority, NSSet tags)` -设置具有给定标记的资源的当前保留优先级。 有效的值处于该范围内`0.0`到`1.0`，将首先清除优先级最低的资源。

有关详细信息，请参阅 Apple [NSBundle 引用](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSBundle_Class/index.html#//apple_ref/occ/cl/NSBundle)。

### <a name="changes-to-nsprocessinfo"></a>对 NSProcessInfo 的更改

IOS 设备上运行每个进程都有一个，_进程信息代理_(PIA)。 使用[NSProcessInfo](xref:Foundation.NSProcessInfo)类以提供有关当前的 PIA 和控制电源和散热管理给定进程的信息。

例如，若要控制自动终止进程可以使用以下代码：

```csharp
// Disable automatic termination
var activity = NSProcessInfo.ProcessInfo.BeginActivity(NSActivityOptions.AutomaticTerminationDisabled, "Define reason for change here...");

// Perform the required task
...

// Return to normal operation
NSProcessInfo.ProcessInfo.EndActivity(activity);
```

有关详细信息，请参阅 Apple [NSProcessInfo 引用](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSProcessInfo_Class/index.html#//apple_ref/occ/cl/NSProcessInfo)。

### <a name="reacting-to-low-power-mode"></a>对低能耗模式作出反应

使用`LowPowerModeEnabled`的属性[NSProcessInfo](xref:Foundation.NSProcessInfo)类来确定是否已启用低功耗模式下运行应用的 iOS 设备上。 例如：

```csharp
// Is the device in low power mode?
if (NSProcessInfo.ProcessInfo.LowPowerModeEnabled) {
    // Reduce activity to conserve energy...
} else {
    // Return to normal activity...
}
```

## <a name="healthkit-framework-changes"></a>HealthKit Framework 更改

Apple 中包括下列更改到[HealthKit](xref:HealthKit)中 iOS 9 框架：

- 支持大容量删除和删除跟踪 HealthKit 数据库中的条目。 请参阅 Apple [HKDeletedObject](https://developer.apple.com/library/prerelease/ios/documentation/HealthKit/Reference/HKDeletedObject_ClassReference/index.html#//apple_ref/occ/cl/HKDeletedObject)， [HKAnchoredObjectQuery](https://developer.apple.com/library/prerelease/ios/documentation/HealthKit/Reference/HKAnchoredObjectQuery_Class/index.html#//apple_ref/occ/cl/HKAnchoredObjectQuery)并[HKHealthStore 类引用](https://developer.apple.com/library/prerelease/ios/documentation/HealthKit/Reference/HKHealthStore_Class/index.html#//apple_ref/doc/uid/TP40014708)有关详细信息。
- 已添加到新的跟踪类别和特征`HKQuantityTypeIdentifier`类 (如`UVExposure`) 和`HKCategoryTypeIdentifier`类 (如`OvulationTestResult`)。 请参阅 Apple [HealthKit 常量引用](https://developer.apple.com/library/prerelease/ios/documentation/HealthKit/Reference/HealthKit_Constants/index.html#//apple_ref/doc/uid/TP40014710)有关详细信息。

请参阅我们[简介 HealthKit](~/ios/platform/healthkit.md)文档了解有关使用 Xamarin.iOS 中 HealthKit 的详细信息。

## <a name="local-authentication-framework-changes"></a>本地身份验证框架更改

Apple 中包括下列更改到[本地身份验证](xref:LocalAuthentication)中 iOS 9 框架：

- 使用`EvaluateAccessControl`并`EvaluatePolicy`方法的[LAContext](xref:LocalAuthentication.LAContext)类，现在，你可以尝试重复使用 Touch ID 的匹配上一个成功解锁。
- 获取当前已注册的手指的列表的功能。
- 用于跟踪为指添加或删除从身份验证时的支持。
- 若要使用的功能_身份验证上下文_在密钥链的调用以及对评估密钥链访问控制的支持列表中。
- 取消用户提示从代码的功能。

请参阅我们[简介 Touch ID](~/ios/platform/touchid.md)文档了解有关使用 Xamarin.iOS 中 Touch ID 的详细信息。

### <a name="lacontext-changes"></a>LAContext 更改

已对以下更改[LAContext](xref:LocalAuthentication.LAContext)适用于 iOS 9 的类：

- **TouchIdAuthenticationMaximumAllowableReuseDuration** -返回的最大可重复使用 touch ID 身份验证的时间量。
- **EvaluatedPolicyDomainState** -获取或设置计算策略的状态。
- **MaxBiometryFailures** -已在 iOS 9 中已弃用。
- **TouchIdAuthenticationAllowableReuseDuration**获取或设置可重复使用 touch ID 身份验证的时间量。
- **EvaluateAccessControl** -异步评估身份验证策略。
- **使之无效**-使给定的 touch ID 身份验证。
- **IsCredentialSet** -返回`true`如果当前设置凭据。
- **SetCredentialType**设置给定的凭据类型。

请参阅 Apple [LAContext 引用](https://developer.apple.com/library/prerelease/ios/documentation/LocalAuthentication/Reference/LAContext_Class/index.html#//apple_ref/occ/instm/LAContext/evaluatePolicy:localizedReason:reply:)的更多详细信息。

## <a name="mapkit-framework-changes"></a>MapKit Framework 更改

Apple 中包括下列更改到[MapKit](xref:MapKit)中 iOS 9 框架：

- MapKit 现在提供支持，用于直接在传输方向启动地图应用程序和用于查询传输到达 (ETA) 使用的估计时间[MKLaunchOptions](xref:MapKit.MKLaunchOptions)并[MKDirections](xref:MapKit.MKLaunchOptions)类。
- MapKit 返回的搜索结果并[CLGeocoder](xref:CoreLocation.CLGeocoder)类还可以提供结果的时区。
- 你现在可以完全自定义提供的 iOS 应用程序使用的映射批注`DetailCalloutAccessoryView`的属性[MKAnnotationView](xref:MapKit.MKAnnotationView)类。

请参阅我们[iOS 地图](~/ios/user-interface/controls/ios-maps/index.md)并[演练-探索批注和 MapKit 中的覆盖](~/ios/user-interface/controls/ios-maps/ios-maps-walkthrough.md)文档使用 Maps 和 Xamarin.iOS 和 Apple中的批注的详细信息[CLGeocoder 引用](https://developer.apple.com/library/prerelease/ios/documentation/CoreLocation/Reference/CLGeocoder_class/index.html#//apple_ref/occ/cl/CLGeocoder)有关详细信息。

## <a name="passkit-framework-additions"></a>PassKit 框架新增功能

Apple 中包括下列更改到[PassKit](xref:PassKit)中 iOS 9 框架：

- Apple Pay 现在支持存储借和信用卡以及发现卡。 请参阅**付款网络**Apple 的一部分[PKPaymentRequest 类引用](https://developer.apple.com/library/prerelease/ios/documentation/PassKit/Reference/PKPaymentRequest_Ref/index.html#//apple_ref/doc/uid/TP40014832)有关详细信息。
- 从直接在 Xamarin.iOS 应用程序，您现在可以添加付款网络和卡颁发者到 Apple Pay。 请参阅 Apple [PKAddPaymentPassViewController 类引用](https://developer.apple.com/library/prerelease/ios/documentation/PassKit/Reference/PKAddPaymentPassViewController_Class/index.html#//apple_ref/doc/uid/TP40016116)的更多详细信息。

请参阅我们[PassKit 简介](~/ios/platform/passkit.md)文档了解有关使用 Xamarin.iOS 中 PassKit 的详细信息。

## <a name="safari-services-framework-additions"></a>Safari Services Framework 新增功能

Apple 中包括下列更改到[Safari 服务](xref:SafariServices)中 iOS 9 框架：

- 你现在可以使用新[SFSafariViewController](xref:SafariServices.SFSafariViewController)类，以显示一个 Xamarin.iOS 应用程序中的 web 内容。 它提供了与 Safari 应用共享网站数据和 cookie 的功能，并包含多个 Safari 的功能 （如读取器和自动填充）。 [SFSafariViewController](xref:SafariServices.SFSafariViewController)功能**完成**完成查看 web 内容时将返回到您的应用程序的用户的按钮。

因为[SFSafariViewController](xref:SafariServices.SFSafariViewController)类定制用于显示 web 内容的单个页，则应考虑使用它来替换任何[WKWebKit](xref:WebKit.WKWebView)或[UIWebView](xref:UIKit.UIWebView)现有 Xamarin.iOS 应用程序中的控件。

### <a name="displaying-a-website"></a>显示网站

下面的代码是调用的示例[SFSafariViewController](xref:SafariServices.SFSafariViewController)从内另一个视图控制器：

```csharp
// Create an instance of the Safari Services View Controller
var controller = new SFSafariViewController(new NSUrl("http://www.xamarin.com"));

// Display website
PresentViewController(controller, true, null);
```

## <a name="uikit-framework-changes"></a>UIKit 框架更改

Apple 已包含很多增强功能的多个元素[UIKit](xref:UIKit)适用于 iOS 9 框架。 以下各节将详细介绍这些更改。

### <a name="3d-touch-events"></a>三维触控事件

新 iOS 9 iPhone 6s 和 iPhone 6s Plus，3D Touch 压力敏感手势添加到你的 iOS 应用。 因此，如果 iOS 9 （或更高版本） 上运行你的应用和 iOS 设备是否能够支持 3D Touch，压力中的更改将导致`TouchesMoved`事件被引发。

由于此更改的行为，应为准备 iOS 应用`TouchesMoved`事件要调用更多时候，即使 X / Y 坐标未发生更改。

有关详细信息，请参阅我们[简介 3D Touch](~/ios/platform/3d-touch.md)指南。

### <a name="document-open-in-place-functionality"></a>文档打开的就地功能

通过使用`FinishedLaunching (application, launchOptions)`或`WillFinishLaunching (Application, launchOptions)`方法的[UIApplicationDelegate](xref:UIKit.UIApplicationDelegate)类，现在可以打开文档，并修改它 （而不是工作副本上） 的位置。

若要支持新打开的就地功能，将添加`LSSupportsOpeningDocumentsInPlace`到 Xamarin.iOS 应用程序的关键**Info.plist**文件中使用的值`YES`。

请参阅 Apple [UIApplicationDelegate 引用](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationDelegate_Protocol/index.html#//apple_ref/occ/intf/UIApplicationDelegate)的更多详细信息。

### <a name="enhanced-touch-events"></a>增强的触摸事件

Apple 提供了多项增强功能的触摸事件在 iOS 9。 其中包括能够使用触摸预测并有权访问显示刷新之间的中间收尾工作了。

请参阅 Apple[适用于 iOS 的事件处理指南](https://developer.apple.com/library/ios/documentation/EventHandling/Conceptual/EventHandlingiPhoneOS/Introduction/Introduction.html)的更多详细信息。

### <a name="fetching-tailored-content"></a>正在提取定制的内容

新`NSDataAsset`类允许在 Xamarin.iOS 应用程序以提取到的内存和当前正在运行的 iOS 设备的图形功能定制的内容。

### <a name="new-layout-anchors"></a>新布局定位点

新`NSLayoutAnchor`并`NSLayoutDimension`布局定位点类使用的新的定位点属性[UIView](xref:UIKit.UIView)类 (如`LeadingAnchor`和`WidthAnchor`) 若要在 iOS 9 简化布局。

请参阅我们[统一情节提要简介](~/ios/user-interface/storyboards/unified-storyboards.md)文档中的 Xamarin.iOS 应用程序和 Apple 的自动布局和大小类使用的详细信息[NSLayoutAnchor 引用](https://developer.apple.com/library/prerelease/ios/documentation/AppKit/Reference/NSLayoutAnchor_ClassReference/index.html#//apple_ref/occ/cl/NSLayoutAnchor)， [NSLayoutDimension 引用](https://developer.apple.com/library/prerelease/ios/documentation/AppKit/Reference/NSLayoutDimension_ClassReference/index.html#//apple_ref/occ/cl/NSLayoutDimension)并[UIView 引用](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/occ/cl/UIView)有关详细信息。

### <a name="new-readable-content-margins"></a>新的可读内容边距

新`UILayoutGuide`类可用于提供可读的内容边距，并定义一个视图内的内容的绘图区域。 请参阅 Apple [UILayoutGuide 引用](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UILayoutGuide_Class_Reference/index.html#//apple_ref/occ/cl/UILayoutGuide)有关详细信息。

### <a name="text-input-in-notifications-modifications"></a>通知的修改的文本输入

[UIUserNotificationAction](xref:UIKit.UIUserNotificationAction)类有一个新的`Behavior`可用于支持通知的文本输入的属性。

### <a name="uiapplicationdelegate-changes"></a>UIApplicationDelegate 更改

虽然不正式 apple 不推荐使用，这些建议将替换为对所有调用`FinishedLaunching (UIApplication application)`方法[UIApplicationDelegate](xref:UIKit.UIApplicationDelegate)类具有`FinishedLaunching (UIApplication application, NSDictionary launchOptions)`或`WillFinishLaunching (UIApplication application, NSDictionary launchOptions)`方法。

请参阅 Apple [UIApplicationDelegate 引用](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationDelegate_Protocol/index.html#//apple_ref/occ/intf/UIApplicationDelegate)的更多详细信息。

### <a name="uikit-dynamics-changes"></a>UIKit Dynamics 更改

Apple iOS 9 中包括对 UIKit Dynamics 的以下更改：

- Dynamics 现在为非矩形冲突边界提供支持。
- 将新的、 可自定义`UIFieldBehavior`类用于支持不同的字段类型。
- 其他的附件类型已添加到`UIAttachmentBehavior`类。

请参阅 Apple [UIAttachment 引用](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIAttachmentBehavior_Class/index.html#//apple_ref/occ/cl/UIAttachmentBehavior)的更多详细信息。

### <a name="uipickerview-and-uidatepicker-changes"></a>UIPickerView 和 UIDatePicker 更改

在 iOS 9 前, [UIPickerView](xref:UIKit.UIPickerView)并[UIDatePicker](xref:UIKit.UIDatePicker)控件所无法调整大小和将自动调整大小以填充其容器 （通常宽度应用程序的 iOS 设备的宽度在上运行）。

在 iOS 9 中，这种自动调整大小不会再出现和控件将呈现在所有的 iOS 设备，而不考虑屏幕大小和方向上 320 点宽度。

若要更正这种情况下，使用自动布局和大小类将控件与父容器 （视图） 的边缘的宽度固定，并指定所需的高度。 请参阅我们[统一情节提要简介](~/ios/user-interface/storyboards/unified-storyboards.md)在 Xamarin.iOS 应用程序中使用自动布局和大小类的详细信息的文档。

### <a name="new-uitextinputassistantitem-class"></a>新 UITextInputAssistantItem 类

使用新`UITextInputAssistantItem`布局栏按钮组中的类_快捷工具栏_。 快捷方式栏是一个新的区域，现已推出软键盘，提供键入快捷方式。



## <a name="related-links"></a>相关链接

- [iOS 9 示例](https://developer.xamarin.com/samples/ios/iOS9/)
- [iOS 9 简介](~/ios/platform/introduction-to-ios9/index.md)
- [面向开发人员的 iOS 9](https://developer.apple.com/ios/pre-release/)
- [What's New iOS 9.0 中](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
