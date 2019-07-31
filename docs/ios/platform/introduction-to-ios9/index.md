---
title: iOS 9 简介
description: 本文介绍了适用于 Xamarin 的 iOS 开发人员提供的所有新的和修改的 Api 和功能。
ms.prod: xamarin
ms.assetid: 4D71BBD9-B948-4B59-9AF5-F199C51CBEB3
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: 733f50954c7ea6bd4ada7468eb14443f4f3b1f1d
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68654416"
---
# <a name="introduction-to-ios-9"></a>iOS 9 简介

_本文介绍了适用于 Xamarin 的 iOS 开发人员提供的所有新的和修改的 Api 和功能。_

![](images/ios9-sml.png "IOS 9 徽标")

Apple 在 iOS 9 中添加了几个新的 Api 和服务, 并对现有功能进行了很多增强。

## <a name="3d-touch"></a>3D Touch

在 iOS 9 和 iPhone 6s 和 iPhone 6s Plus 的新手中, 3D 触控为你的 iOS 应用程序增加了压力敏感度。 使用3D 触控, iPhone 应用现在不仅能够告诉用户正在触摸设备的屏幕, 还可以确定用户施加的压力, 并响应不同的压力级别。

3D Touch 为你的应用提供以下功能:

- **压力敏感度**-应用现在可以测量用户在屏幕上的接触速度, 并利用这些信息。 例如, 绘制应用可以根据用户对屏幕的接触情况, 使线条更粗或更细。
- "**速览" 和 "弹出**"-应用现在可以让用户与其数据交互, 而无需导航到当前上下文。 通过在屏幕上按 "hard", 他们可以*查看*他们感兴趣的项目 (例如预览邮件)。 通过按更难, 他们可以*弹出*项目。
- "**快速操作**"-当用户在桌面应用中右键单击某一项时, 可以使用快捷菜单, 如可以弹出的上下文菜单。 使用 "快速操作", 可以从 iOS 设备上的主屏幕图标中添加常用、快速且易于访问的功能的快捷方式。

若要了解详细信息, 请参阅[3D Touch 指南简介](~/ios/platform/3d-touch.md)。

## <a name="app-transport-security"></a>应用传输安全性

应用传输安全 (ATS) 是 iOS 9 的新手, 它强制实施 internet 资源 (如应用的后端服务器) 和你的应用之间的安全连接。 ATS 确保所有 internet 通信都符合安全连接最佳做法, 从而防止直接通过应用或正在使用的库泄露敏感信息。

由于默认情况下, 在为 iOS 9 和 OS X 10.11 (El Capitan) 构建的应用中启用了 ATS, 因此, 使用[NSUrlConnection](xref:Foundation.NSUrlConnection)、 [CFUrl](xref:CoreFoundation.CFUrl)或[NSUrlSession](xref:Foundation.NSUrlSession)的所有连接都将受到 ATS 安全要求。 如果连接不满足这些要求, 它们将失败并出现异常。

若要了解有关 ATS 的详细信息, 请参阅我们的[应用传输安全](~/ios/app-fundamentals/ats.md)指南。

<a name="multitasking" />

## <a name="multitasking-for-ipad"></a>适用于 iPad 的多任务

在 iOS 9 中, Apple 增加了对在特定 iPad 硬件上同时运行两个应用程序的多任务支持。 因此, 你的 Xamarin iOS 应用程序无法再假设它们是在任意给定时间运行的唯一应用, 或者他们有权访问整个屏幕或设备资源。

通过以下功能支持 iPad 的多任务:

- **滑过**-允许用户在滑出面板中临时运行第二个 iOS 应用 (根据语言方向在屏幕的右侧或左侧), 该面板大约包含当前正在运行的主要应用程序的 25%。 滑过只能在 iPad Pro、iPad Air、iPad Air 2、iPad 迷你2、iPad 迷你3或 iPad 迷你4上使用。
- **拆分视图**-在受支持的 ipad 硬件上 (ipad Air 2、ipad 迷你4和 ipad Pro), 用户可以选择第二个应用, 并在拆分屏幕模式下并行运行当前正在运行的应用程序。 用户可以控制每个应用占用的主屏幕的百分比。
- 图片 **: 对于**播放视频内容的应用, 视频现在可以在可移动且可调整大小的窗口中播放, 该窗口将浮动在当前运行在 iOS 设备上的其他应用。 用户可以完全控制此窗口的大小和位置。 图片中的图片仅在 iPad Pro、iPad Air、iPad Air 2、iPad 迷你2、iPad 迷你3或 iPad 迷你4上可用。

若要了解有关 iOS 9 的新多任务功能的详细信息, 请参阅我们的[多任务 For iPad](~/ios/platform/multitasking.md)指南。

## <a name="new-contacts-and-contacts-ui-frameworks"></a>新联系人和联系人 UI 框架

随着 iOS 9 的引入, Apple 发布了两个新框架, 即[联系人](xref:Contacts)和[ContactsUI](xref:ContactsUI), 用于替换 iOS 8 及更早版使用的现有通讯簿和通讯簿 UI 框架。

这些面向对象的新框架提供以下内容:

- **联系人**–提供对用户的联系人信息的 Xamarin iOS 访问权限。 由于大多数应用只需要只读访问权限, 因此此框架已针对线程安全只读访问进行了优化。
- **ContactsUI** –提供 XAMARIN ios UI 元素, 用于在 iOS 设备上显示、编辑、选择和创建联系人。

有关详细信息, 请参阅[联系人和联系人 UI](~/ios/platform/contacts.md)文档。


## <a name="new-search-apis"></a>新搜索 Api

已在 iOS 9 中展开搜索, 以提供更好的新方法来访问 Xamarin iOS 应用内的信息。 使用新的搜索 Api, 你可以通过聚焦和 Safari 搜索结果、移交和 Siri 提醒和建议, 使你的应用内容可供搜索。 这样, 用户就可以快速访问应用中的活动和信息。

此外, 新的搜索 Api 使您可以更轻松地在应用程序中集成搜索, 而无需事先搜索实现。 因此, Apple 声称, 通常需要几个小时才能使用应用搜索来统一搜索 iOS 9 应用的内容。

有关详细信息, 请参阅我们的[搜索增强](~/ios/platform/search/index.md)文档。

## <a name="new-stack-view"></a>新建堆栈视图

堆栈视图控件 ([UIStackView](xref:UIKit.UIStackView)利用自动布局和大小类的功能来管理动态响应 iOS 设备方向和屏幕大小的子视图堆栈 (水平或垂直)。

通过使用 "堆栈视图" 控件, 可大大减少布局用户界面所需的工作量。 基于开发人员定义的属性 (如轴、分布、对齐方式和间距) 自动管理附加到堆栈视图的所有子视图的布局。

有关详细信息, 请参阅[堆栈视图文档简介](~/ios/user-interface/controls/uistackview.md)。


## <a name="collection-view-changes"></a>集合视图更改

在 iOS 9 中, "集合" 视图 ([UICollectionView](xref:UIKit.UICollectionView)现在支持通过添加新的默认手势识别器和几个新的支持方法, 从框中拖动项的重新排序。

使用这些新方法, 你可以在集合视图中轻松实现拖放操作, 并可选择在重新排序过程的任何阶段自定义项外观。

若要了解有关 iOS 9 的集合视图更改的详细信息, 请参阅我们的[集合视图更改](~/ios/user-interface/controls/uicollectionview.md)指南。

## <a name="game-enhancements"></a>游戏增强功能

在 iOS 9 中, Apple 对游戏 Api 做出了多项技术改进, 使你可以更轻松地在 Xamarin iOS 应用程序中实现游戏图形和音频。 其中包括通过高级框架的轻松开发, 并利用 iOS 设备 GPU 的强大功能, 通过低级别增强功能提高速度和图形能力。

这包括 GameplayKit、ReplayKit、Model i/o、MetalKit 和金属绩效着色器, 以及金属、SceneKit 和 SpriteKit 的新增功能和增强功能。

有关详细信息, 请参阅[游戏增强](~/ios/platform/gaming/index.md)文档。

## <a name="homekit-framework-changes"></a>HomeKit Framework 更改

IOS 8 中引入的[HomeKit](xref:HomeKit)框架提供了一种功能, 可用于设置和控制 Xamarin iOS 应用中的各种 HomeKit 启用的附件 (例如自动光源、门锁定和车库门 openers)。 除了易于设置和配置外, 还可以通过口头 Siri 命令控制 HomeKit 的附件。

在 iOS 9 中, Apple 使设置更简单, 扩展了支持的附件类型, 并提供了更多的附件交互 (如通过 iCloud 远程控制附件)。

有关详细信息, 请参阅[HomeKit 的简介](~/ios/platform/homekit.md)、 [HomeKitIntro iOS 示例应用](https://docs.microsoft.com/samples/xamarin/ios-samples/homekit-homekitintro)和 Apple 的[HomeKit](https://developer.apple.com/homekit/)文档。

## <a name="handoff-framework-changes"></a>切换框架更改

IOS 8 和 OS X Yosemite (10.10) 中的 Apple 引入了移交 (也称为连续性), 以使用户能够在其某个设备 (iOS 或 Mac) 上启动活动, 并在其设备上继续此相同活动 (由用户的 iClou 标识)d 帐户)。

在 iOS 9 中扩展了移交, 还支持新的增强搜索功能。 有关详细信息, 请参阅我们的[搜索增强](~/ios/platform/search/index.md)文档。 有关使用移交的详细信息, 请参阅我们[的移交文档简介](~/ios/platform/handoff.md)。

## <a name="new-extension-points"></a>新扩展点

在 iOS 8 中, Apple 引入了扩展-操作系统在标准上下文中提供的库, 如通知中心内、当用户请求键盘或编辑照片时。

在 iOS 9 中, Apple 通过提供几个新_扩展点_(用于定义使用策略, 并提供用于在给定区域中工作的 api) 来扩展扩展支持, 如下所示:

- **新的音频设备扩展点**–使用此扩展点来提供音频效果、乐器、声音生成器等, 以便在其他音频设备主机应用 (如 GarageBand) 中使用。 此扩展点还允许你在应用商店中销售_音频单位_(音频插件)。
- **新索引维护扩展点**-使用此扩展点来支持应用程序数据的重新索引, 而无需重新启动应用程序。
- **新网络扩展点**(这需要 Apple 的特殊权限):
    - **应用代理提供程序扩展**-使用此扩展点实现自定义透明客户端网络代理。
    - **筛选数据提供程序/筛选器控件提供程序扩展**-使用这些扩展点来实现设备上的动态网络内容筛选。
    - **数据包隧道提供程序扩展**-使用此扩展点实现自定义 VPN 隧道协议客户端。
- **新的 Safari 扩展点**:
    - **内容阻止扩展**-使用此扩展点定义用户浏览 web 时不会显示的阻止内容的列表。
    - **共享链接扩展**-使用此扩展点可以在 Safari 的共享链接中查看应用内容。

有关详细信息, 请参阅我们的扩展和 Apple[应用扩展编程指南](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214)文档[介绍](~/ios/platform/extensions.md)。

## <a name="keychain-enhancements"></a>密钥链增强功能

在 iOS 9 中, Apple 增强了密钥链, 为 Secure Enclave 和更多项保护选项提供新的加密密钥类型, 如下所示:

- 修改指纹数据库时使密钥链项无效的新 Touch ID 约束。
- 允许只使用 Touch ID 或密码创建访问控制列表项的新约束。
- 一个新的身份验证上下文, 可用于调用独立于`SecItem`调用的身份验证。
- 应用提供的密钥链项加密的访问控制列表熵 (使用应用程序密码选项)。
- 支持在安全 enclave 内生成和使用密钥 (通过`kSecAttrTokenIDSecureEnclave`属性)。

有关详细信息, 请参阅我们[对 TOUCH ID](~/ios/platform/touchid.md)文档的介绍。


## <a name="right-to-left-language-support"></a>从右到左语言支持

在 iOS 9 中, 通过提供对从右到左语言的完全支持, Apple 使你比以往更轻松地呈现翻转的用户界面。 这包括：

- 标准[UIKit](xref:UIKit)控件将根据 iOS 设备区域设置和语言设置自动从右到左翻转。
- [UIView](xref:UIKit.UIView)类提供了属性, 使用这些属性可以定义给定视图在从右到左翻转时应如何显示。
- 能够使用[UIImage](xref:UIKit.UIImage)类的[FlipsForRightToLeftLayoutDirection](xref:UIKit.UIImage.FlipsForRightToLeftLayoutDirection)属性以编程方式翻转图像。

有关详细信息, 请参阅 Apple 的[支持从右到左语言](https://developer.apple.com/library/prerelease/ios/documentation/MacOSX/Conceptual/BPInternational/SupportingRight-To-LeftLanguages/SupportingRight-To-LeftLanguages.html#//apple_ref/doc/uid/10000171i-CH17)文档。



## <a name="additional-framework-changes"></a>其他框架更改

除了上面所述的重大更改之外, Apple 还对 iOS 9 的若干现有框架进行了修改和改进, 其中包括:

- AV 基础框架
- AVKit 框架
- CloudKit 框架
- Foundation 框架
- 切换框架
- HealthKit 框架
- HomeKit 框架
- 本地身份验证框架
- MapKit 框架
- PassKit 框架
- Safari 服务框架
- UIKit 框架

有关详细信息, 请参阅我们的[附加 iOS 9 Framework 更改](~/ios/platform/introduction-to-ios9/additional-framework-changes.md)文档。

## <a name="deprecated-apis-and-functions"></a>弃用的 Api 和函数

Apple 在 iOS 9 中弃用了以下 Api 和函数:

- **通讯簿 & 通讯簿 ui** -这些 api 已被联系人和联系人 UI 框架取代。 有关详细信息, 请参阅[联系人和联系人 UI](~/ios/platform/contacts.md)文档。
- **CBCentralManager** - `RetrievePeripherals` 类`CBCentralManager`的`RetrieveConnectedPeripherals`和方法已在 iOS 9 中被删除。 调用这些方法将导致应用在配对附件或应用启动时崩溃。
- **FetchAllChanges** `FetchAllChanges` -`CKFetchRecordChangesOperation`已对类进行了折旧, 并将在 iOS 9 中将其删除。
- **Media Player** -Media Player 框架已在 iOS 9 中弃用。 请改用 AVKit 或 AV Foundation Api。

有关特定 API 弃用功能的完整列表, 请参阅 Apple 的[iOS 9.0 API 差异](https://developer.apple.com/library/prerelease/ios/releasenotes/General/iOS90APIDiffs/index.html#//apple_ref/doc/uid/TP40016222)文档。

## <a name="ios-9-sample-apps"></a>iOS 9 示例应用

我们有一些[特定于 iOS 9 的示例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9)可供你开始使用:

- [AstroLayout](https://github.com/xamarin/monotouch-samples/tree/master/ios9/AstroLayout)
- [CollectionView](https://github.com/xamarin/monotouch-samples/tree/master/ios9/CollectionView)
- [MetalPerformanceShadersHelloWorld](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-metalperformanceshadershelloworld)
- [MusicMotion](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-musicmotion)
- [PhotoProgress](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-photoprogress)
- [SegueCatalog](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-seguecatalog)
- [StackView](https://github.com/xamarin/monotouch-samples/tree/master/ios9/StackView)
- [StickyCorners](https://github.com/xamarin/monotouch-samples/tree/master/ios9/StickyCorners)

另外, 请查看这些示例的 iOS 部分 (随附 Mac OS X 版本!):

- [AgentsCatalog](https://github.com/xamarin/mac-ios-samples/tree/master/AgentsCatalog)
- [MetalKitEssentials](https://github.com/xamarin/mac-ios-samples/tree/master/MetalKitEssentials)



## <a name="related-links"></a>相关链接

- [iOS 9 示例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9)
- [3D Touch 简介](~/ios/platform/3d-touch.md)
- [应用传输安全性](~/ios/app-fundamentals/ats.md)
- [适用于 iPad 的多任务](~/ios/platform/multitasking.md)
- [联系人和联系人 UI](~/ios/platform/contacts.md)
- [新搜索 Api](~/ios/platform/search/index.md)
- [堆栈视图简介](~/ios/user-interface/controls/uistackview.md)
- [集合视图更改](~/ios/user-interface/controls/uicollectionview.md)
- [游戏增强功能](~/ios/platform/gaming/index.md)
- [HomeKit 简介](~/ios/platform/homekit.md)
- [移交简介](~/ios/platform/handoff.md)
- [其他 iOS 9 框架更改](~/ios/platform/introduction-to-ios9/additional-framework-changes.md)
- [疑难解答](~/ios/platform/introduction-to-ios9/troubleshooting.md)
- [适用于开发人员的 iOS 9](https://developer.apple.com/ios/pre-release/)
- [IOS 9.0 中的新增功能](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [将你的 Xamarin iOS 应用程序更新到 iOS9 (视频)](https://university.xamarin.com/lightninglectures/Updating-your-XamariniOS-apps-to-iOS9)
