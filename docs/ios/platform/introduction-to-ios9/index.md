---
title: IOS 9 简介
description: 本文介绍适用于 Xamarin.iOS 开发人员的所有新的和修改 Api 和 iOS 9 中的可用功能。
ms.prod: xamarin
ms.assetid: 4D71BBD9-B948-4B59-9AF5-F199C51CBEB3
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: becba36655a5247a11decb7dc54334f9397ecdfc
ms.sourcegitcommit: a1a58afea68912c79d16a3f64de9a0c1feb2aeb4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2019
ms.locfileid: "55233530"
---
# <a name="introduction-to-ios-9"></a>IOS 9 简介

_本文介绍适用于 Xamarin.iOS 开发人员的所有新的和修改 Api 和 iOS 9 中的可用功能。_

![](images/ios9-sml.png "IOS 9 徽标")

Apple 在 iOS 9 中以及对现有功能的许多增强功能已添加多个新 Api 和服务。

## <a name="3d-touch"></a>3D Touch

新 iOS 9 iPhone 6s 和 iPhone 6s Plus，3D Touch 压力敏感手势添加到你的 iOS 应用。 使用 3D Touch，iPhone 应用现在可以不仅知道用户触摸设备的屏幕上，它可以还感应多少用户施加的压力和响应不同压力级别。

3D Touch 提供到您的应用程序的以下功能：

- **压力敏感度**-现在可以难测量应用程序或 light 用户触摸屏幕并且希望利用该信息。 例如，绘画应用程序可以使之更宽或更薄根据用户难触摸屏幕的行。
- **查看和弹出**-您的应用程序现在可以让用户使用其数据而无需导航从其当前上下文交互。 通过按硬在屏幕上，他们可以*扫视*在他们感兴趣 （例如预览一条消息） 的项。 通过按更难，他们可以*弹出*到该项。
- **快速操作**-认为的快速操作，例如，可以将弹出向上时在用户右键单击桌面应用程序中的项的上下文菜单。 使用快速操作，您可以添加常见、 快速且简单到函数的快捷方式访问应用程序中从 iOS 设备上的主页屏幕图标。

若要获取详细信息，请参阅我们[简介 3D Touch](~/ios/platform/3d-touch.md)指南。

## <a name="app-transport-security"></a>应用程序传输安全

新 ios 9、 应用传输安全 (ATS) 实施 internet 资源 （如应用程序的后端服务器） 和您的应用程序之间的安全连接。 ATS 可确保所有 internet 通信都符合以确保连接安全最佳实践，从而防止意外泄露敏感信息直接通过您的应用程序或正在使用的库。

由于默认情况下，所有连接使用适用于 iOS 9 和 OS X 10.11 (El Capitan) 都构建应用程序中启用了 ATS [NSUrlConnection](xref:Foundation.NSUrlConnection)， [CFUrl](xref:CoreFoundation.CFUrl)或[NSUrlSession](xref:Foundation.NSUrlSession)收费依据ATS 安全要求。 如果你的连接不能满足这些要求，它们将失败并出现异常。

若要了解有关 ATS 的详细信息，请参阅我们[应用程序传输安全](~/ios/app-fundamentals/ats.md)指南。

<a name="multitasking" />

## <a name="multitasking-for-ipad"></a>适用于 iPad 的多任务

与 iOS 9、 Apple 添加了对特定 iPad 硬件上同时运行两个应用的多任务处理支持。 因此，Xamarin.iOS 应用不能再认为它们是唯一的应用在任何给定时间运行或它们有权访问的全屏幕或设备的资源。

适用于 iPad 的多任务支持通过以下功能：

- **幻灯片通过**-允许用户暂时滑出面板 （根据语言方向在屏幕右侧或左侧边缘上或者） 涵盖大约 25%的当前运行的主应用程序中运行的第二个 iOS 应用程序。 幻灯片上是仅适用于 iPad Pro、 iPad 以无线方式、 iPad 空气 2、 iPad Mini 2、 iPad Mini 3 或 iPad Mini 4。
- **拆分视图**-用户可以在支持的 iPad 硬件 (iPad 空气 2，iPad Mini 4 和专业人员仅限 iPad) 上，选择第二个应用和使用当前运行的应用在拆分屏幕模式下运行的并行。 用户可以控制在主屏幕中的每个应用所占的百分比。
- **在图中的图片**-适用于播放的视频内容，现在视频可以播放在可移动、 可调整大小窗口浮动转移当前在 iOS 设备上运行的其他应用中的应用。 用户可以完全控制的大小和位置的此窗口。 IPad Pro、 iPad 以无线方式、 iPad 空气 2、 iPad Mini 2、 iPad Mini 3 或 iPad Mini 4 上仅可在图中的图片。

若要了解有关 iOS 9 的新的多任务处理能力的详细信息，请参阅我们[适用于 iPad 的多任务](~/ios/platform/multitasking.md)指南。

## <a name="new-contacts-and-contacts-ui-frameworks"></a>新的联系人和联系人 UI 框架

IOS 9 的引入，Apple 已发布了两个新框架[联系人](https://developer.xamarin.com/api/namespace/Contacts/)并[ContactsUI](https://developer.xamarin.com/api/namespace/ContactsUI/)、 替换现有的通讯簿和地址簿 UI 框架由 iOS 8 和之前的版本。

这些新的、 面向对象的框架提供以下信息：

- **联系人**– 提供了 Xamarin.iOS 权访问用户的联系信息。 由于大多数应用程序仅需要只读访问权限，此框架已经过优化为线程安全的只读访问。
- **ContactsUI** – 提供了 Xamarin.iOS UI 元素以显示，请编辑、 选择和 iOS 设备上创建的联系人。

有关详细信息，请参阅我们[联系人和联系人 UI](~/ios/platform/contacts.md)文档。


## <a name="new-search-apis"></a>新的搜索 Api

搜索扩展了 iOS 9 提供了全新的方法来访问你的 Xamarin.iOS 应用程序内的信息。 使用新的搜索 Api，你可以使应用程序的内容可搜索通过聚焦和 Safari 搜索结果中，切换和 Siri 提醒和建议。 这允许用户快速访问活动和您的应用程序内的深度信息。

此外，新的搜索 Api 使其更轻松地将不在前一搜索实现体验的情况下应用程序中的搜索功能集成。 正因为如此，Apple 声明它通常需要数小时才能使普遍可供搜索，使用应用程序搜索 iOS 9 应用程序的内容。

有关详细信息，请参阅我们[搜索增强功能](~/ios/platform/search/index.md)文档。

## <a name="new-stack-view"></a>新堆栈视图

堆栈视图控件 ([UIStackView](xref:UIKit.UIStackView)利用动态响应的 iOS 设备的方向和屏幕大小自动布局和大小类来管理的子视图堆栈 （水平或垂直） 的强大功能。

通过使用堆栈视图控件，可大大降低用户界面的布局所需的工作量。 根据定义的开发人员属性轴、 分发、 对齐方式和间距等自动管理附加到堆栈视图的所有子视图的布局。

有关详细信息，请参阅我们[堆栈视图简介](~/ios/user-interface/controls/uistackview.md)文档。


## <a name="collection-view-changes"></a>集合视图更改

在 iOS 9，集合视图中 ([UICollectionView](xref:UIKit.UICollectionView)现在支持将通过添加新的默认手势识别程序和几个新的支持方法在初始状态下的项重新排序。

使用这些新方法，您可以轻松地在集合视图中实现拖放重新排序和具有自定义项重新排序过程任何阶段外观的选项。

若要了解有关 iOS 9 的集合视图更改的详细信息，请参阅我们[集合视图更改](~/ios/user-interface/controls/uicollectionview.md)指南。

## <a name="game-enhancements"></a>游戏的增强功能

与 iOS 9、 Apple 对进行了多项技术改进游戏 Api，以便更轻松地实现游戏图形和音频 Xamarin.iOS 应用程序中。 其中包括这两种易用性开发到高级框架和提高了的速度和低级别的增强功能的图形能力的 iOS 设备的 GPU 的强大功能。

这包括 GameplayKit、 ReplayKit、 模型 I/O、 MetalKit 和金属性能着色器以及裸机、 SceneKit 和 SpriteKit 的新的增强型功能。

有关详细信息，请参阅我们[游戏的增强功能](~/ios/platform/gaming/index.md)文档。

## <a name="homekit-framework-changes"></a>HomeKit 框架更改

[HomeKit](https://developer.xamarin.com/api/namespace/HomeKit/) iOS 8 中引入框架提供的功能来设置和控制 （如自动的光源、 门锁和车库开门器供电） 从 Xamarin.iOS 应用程序的各种启用 HomeKit 附件。 除了易于安装和配置外，可以通过语音 Siri 命令控制 HomeKit 附件。

在 iOS 9 中，Apple 已变得更加容易安装，则展开附件支持和提供多个附件交互 （例如，控制远程通过 iCloud 附件） 的类型。

有关详细信息，请参阅我们[简介 HomeKit](~/ios/platform/homekit.md)， [HomeKitIntro iOS 示例应用程序](https://developer.xamarin.com/samples/monotouch/HomeKit/HomeKitIntro/)和 Apple [HomeKit](https://developer.apple.com/homekit/)文档。

## <a name="handoff-framework-changes"></a>切换 Framework 更改

切换 （也称为连续性） 已作为一种方式为用户启动一个其设备 （iOS 或 Mac） 上的一个活动并在另一台设备 （如标识的用户的 iClou 继续该同一活动中引入的 Apple 在 iOS 8 和 OS X Yosemite (10.10)d 帐户)。

切换已扩展在 iOS 9，还支持新的、 增强的搜索功能。 有关详细信息，请参阅我们[搜索增强功能](~/ios/platform/search/index.md)文档。 有关使用切换的详细信息，请参阅我们[简介切换](~/ios/platform/handoff.md)文档。

## <a name="new-extension-points"></a>新扩展点

在 iOS 8 中，Apple 引入了扩展，库显示由操作系统在标准上下文中，如在通知中心中，当用户请求键盘，或当他们正在编辑的照片。

Ios 9，Apple 在不断扩展插件支持扩展通过提供几个新_扩展点_的定义使用策略和提供的 Api 用于处理给定区域内，如下所示：

- **新的音频单元扩展点**– 使用此扩展点 （例如 GarageBand) 其他音频设备主机应用程序中使用提供的音频效果、 乐器、 声音生成器，等等。 此扩展点还允许您销售_音频单元_（音频插件） 在 App Store 上。
- **新的索引维护扩展点**— 使用此扩展点来支持重建索引的应用程序数据，而无需应用程序重新启动。
- **新的网络扩展点**（这些需要从 Apple 的特殊权限）：
    - **应用代理提供程序扩展**— 使用此扩展点来实现透明的自定义客户端的网络代理。
    - **筛选数据提供程序 / 筛选器控件提供程序扩展**-使用这些扩展点来实现动态网络筛选设备上的内容。
    - **数据包隧道提供程序扩展**— 使用此扩展点来实现自定义 VPN 隧道协议客户端。
- **新的 Safari 扩展点**:
    - **内容阻止扩展**— 使用此扩展点来定义的用户在浏览 web 时将不会显示被阻止内容的列表。
    - **共享链接扩展**— 使用此扩展点来支持 Safari 的共享链接中的应用程序的内容的观看。

有关详细信息，请参阅我们[扩展插件简介](~/ios/platform/extensions.md)和 Apple[应用程序扩展编程指南](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214)文档。

## <a name="keychain-enhancements"></a>密钥链的增强功能

在 iOS 9 中，Apple 已增强的密钥链，以便为新的加密密钥类型安全 Enclave 和更多项保护选项，如下所示：

- 使密钥链项失效时将指纹数据库改为一个新的 Touch ID 约束。
- 允许仅使用 Touch ID 或密码创建访问控制列表项的新约束。
- 新的身份验证上下文，可用于调用独立于身份验证`SecItem`调用。
- 为应用程序提供密钥链项加密访问控制列表 （使用应用程序密码选项） 的平均信息量。
- 支持生成和使用内部安全 enclave 的密钥 (通过`kSecAttrTokenIDSecureEnclave`属性)。

有关详细信息，请参阅我们[简介 Touch ID](~/ios/platform/touchid.md)文档。


## <a name="right-to-left-language-support"></a>从右到左语言支持

在 iOS 9 中，Apple 已翻转后的用户界面比以往： 显示为右到左的语言提供完整支持。 这包括：

- 标准[UIKit](xref:UIKit)控件将自动翻转右到左的 iOS 设备的区域设置和语言设置。
- [UIView](xref:UIKit.UIView)类提供了属性，可用于定义给定的视图应如何显示时翻转从右到左。
- 能够以编程方式使用翻转图像[FlipsForRightToLeftLayoutDirection](xref:UIKit.UIImage.FlipsForRightToLeftLayoutDirection)的属性[UIImage](xref:UIKit.UIImage)类。

有关详细信息，请参阅 Apple[支持右到左的语言](https://developer.apple.com/library/prerelease/ios/documentation/MacOSX/Conceptual/BPInternational/SupportingRight-To-LeftLanguages/SupportingRight-To-LeftLanguages.html#//apple_ref/doc/uid/10000171i-CH17)文档。



## <a name="additional-framework-changes"></a>其他 Framework 更改

除了我们上面所述的主要更改，Apple 已修改和改进适用于 iOS 9 包括以下几个现有框架：

- AV Foundation 框架
- AVKit Framework
- CloudKit Framework
- Foundation 框架
- 切换 Framework
- HealthKit Framework
- HomeKit 框架
- 本地身份验证框架
- MapKit Framework
- PassKit 框架
- Safari 服务框架
- UIKit 框架

有关详细信息，请参阅我们[其他 iOS 9 框架更改](~/ios/platform/introduction-to-ios9/additional-framework-changes.md)文档。

## <a name="deprecated-apis-and-functions"></a>已弃用的 Api 和函数

Apple 已弃用的以下 Api 和 iOS 9 中的函数：

- **解决书籍和地址簿 UI** -的联系人和联系人 UI 框架已替换为这些 Api。 有关详细信息，请参阅我们[联系人和联系人 UI](~/ios/platform/contacts.md)文档。
- **CBCentralManager** -`RetrievePeripherals`并`RetrieveConnectedPeripherals`方法的`CBCentralManager`类已在 iOS 9 中删除。 调用这些方法会导致应用崩溃配对附件时或在应用启动。
- **FetchAllChanges** -`FetchAllChanges`的`CKFetchRecordChangesOperation`类已过时，将在 iOS 9 中删除。
- **Media Player** -iOS 9 中已弃用的媒体播放器框架。 改为使用 AVKit 或 AV Foundation Api。

有关特定 API 弃用功能的完整列表，请参阅 Apple [iOS 9.0 API 差异](https://developer.apple.com/library/prerelease/ios/releasenotes/General/iOS90APIDiffs/index.html#//apple_ref/doc/uid/TP40016222)文档。

## <a name="ios-9-sample-apps"></a>iOS 9 示例应用

我们有一些[iOS 9 特定的示例](https://developer.xamarin.com/samples/ios/iOS9/)若要开始：

- [AstroLayout](https://github.com/xamarin/monotouch-samples/tree/master/ios9/AstroLayout)
- [CollectionView](https://github.com/xamarin/monotouch-samples/tree/master/ios9/CollectionView)
- [MetalPerformanceShadersHelloWorld](https://developer.xamarin.com/samples/monotouch/ios9/MetalPerformanceShadersHelloWorld/)
- [MusicMotion](https://developer.xamarin.com/samples/monotouch/ios9/MusicMotion/)
- [PhotoProgress](https://developer.xamarin.com/samples/monotouch/ios9/PhotoProgress/)
- [SegueCatalog](https://developer.xamarin.com/samples/monotouch/ios9/SegueCatalog/)
- [StackView](https://github.com/xamarin/monotouch-samples/tree/master/ios9/StackView)
- [StickyCorners](https://github.com/xamarin/monotouch-samples/tree/master/ios9/StickyCorners)

此外可以查看这些示例 （随附 Mac OS X 版本即将 ！） 的 iOS 部分：

- [AgentsCatalog](https://github.com/xamarin/mac-ios-samples/tree/master/AgentsCatalog)
- [MetalKitEssentials](https://github.com/xamarin/mac-ios-samples/tree/master/MetalKitEssentials)



## <a name="related-links"></a>相关链接

- [iOS 9 示例](https://developer.xamarin.com/samples/ios/iOS9/)
- [3D Touch 简介](~/ios/platform/3d-touch.md)
- [应用传输安全性](~/ios/app-fundamentals/ats.md)
- [适用于 iPad 的多任务](~/ios/platform/multitasking.md)
- [联系人和联系人 UI](~/ios/platform/contacts.md)
- [新的搜索 Api](~/ios/platform/search/index.md)
- [堆栈视图简介](~/ios/user-interface/controls/uistackview.md)
- [集合视图更改](~/ios/user-interface/controls/uicollectionview.md)
- [游戏的增强功能](~/ios/platform/gaming/index.md)
- [HomeKit 简介](~/ios/platform/homekit.md)
- [切换简介](~/ios/platform/handoff.md)
- [其他 iOS 9 框架更改](~/ios/platform/introduction-to-ios9/additional-framework-changes.md)
- [疑难解答](~/ios/platform/introduction-to-ios9/troubleshooting.md)
- [面向开发人员的 iOS 9](https://developer.apple.com/ios/pre-release/)
- [What's New iOS 9.0 中](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [更新你的 Xamarin.iOS 应用到 iOS9 （视频）](https://university.xamarin.com/lightninglectures/Updating-your-XamariniOS-apps-to-iOS9)
