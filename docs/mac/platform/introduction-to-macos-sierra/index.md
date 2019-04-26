---
title: macOS Sierra 简介
description: 本文介绍适用于 Xamarin.Mac 开发人员的所有新的和修改 Api 和 macOS Sierra 中提供的功能。
ms.prod: xamarin
ms.assetid: 71A8A737-F310-4320-BD23-743AA1E9033C
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 5a944fd8f7dcfdcbb3f025c92b4afac35673416f
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61031498"
---
# <a name="introduction-to-macos-sierra"></a>macOS Sierra 简介

新 macos Sierra，开发人员可以充分利用新的 Api，使最终用户能够使用其应用和网站以前不可用方式进行交互。 例如，Apple 现在允许网站将为客户提供付费安全地通过 Apple Pay 和增强功能的金属框架提升应用程序的图形和执行计算的选项可能。 

有关 macOS Sierra 的详细信息，请参阅 Apple [macOS + 应用](https://developer.apple.com/macos/)文档。

<a name="Whats-New-in-macOS-Sierra" />

## <a name="whats-new-in-macos-sierra"></a>What's New macOS Sierra 中

Apple 已在 macOS Sierra 以及对现有功能，包括许多增强功能中添加几个新 Api 和服务：

<a name="Apple-File-System" />

### <a name="apple-file-system"></a>Apple 文件系统

Macos Sierra，Apple 已作为适用于 iOS、 macOS、 tvOS 和 watchOS 的最新文件系统发布新的 Apple 文件系统。 Apple 文件系统针对 Flash 和 SSD 存储进行了优化，并提供以下功能： 强加密，写入时复制元数据，空间共享、 文件和目录、 快照、 快速的目录大小调整和原子安全保存基元克隆。

有关详细信息，请参阅 Apple [Apple 文件系统指南](https://developer.apple.com/library/prerelease/content/documentation/FileManagement/Conceptual/APFS_Guide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40016999)。

<a name="Apple-Pay-Enhancements" />

### <a name="apple-pay-enhancements"></a>Apple Pay 增强功能

Apple 已对 Apple Pay 进行多项增强功能，允许用户从网站的安全还的 macOS Sierra。

MacOS Sierra，几个新 Api 添加了采用了 macOS Sierra、 iOS 和 watchOS 以支持动态付款网络和新的沙盒测试环境。

macOS Sierra 包括新的 ApplePay Javascript 框架，允许开发人员可以将 Apple Pay 应用直接到 iOS 和 macOS 基于 Safari 的网站。 对于支持 Apple Pay 的网站，用户可以授权使用他们的 iPhone 或 Apple Watch 的付款。

有关详细信息，请参阅 Apple [ApplePay JS 框架](https://developer.apple.com/reference/applepayjs)引用。

<a name="Building-Modern-macOS-Apps" />

### <a name="building-modern-macos-apps"></a>生成新式 macOS 应用

如 Apple Safari web 浏览器、 页面字处理器和数字分布表使用很多新技术以提供一个统一的上下文相关的用户界面，消除与传统的 UI 元素，如浮动面板和多个打开的新式 macOS 应用windows。

[![选项卡式 Mac 窗口的一个示例](images/content08.png)](images/content08.png#lightbox)

我们[生成新式 macOS 应用](~/mac/platform/introduction-to-macos-sierra/modern-cocoa-apps.md)指南涵盖了几个技巧、 功能和技术开发人员可以使用生成新式 macOS 应用在 Xamarin.Mac 中的。

<a name="CloudKit-Data-Sharing" />

### <a name="cloudkit-data-sharing"></a>CloudKit 数据共享

已在 macOS Sierra 以允许用户快速轻松地共享记录或记录集从其专用 iCloud 数据库中扩展 CloudKit 框架。

CloudKit 提供完整的 UI 来发送和接受记录的共享的邀请，并且用户具有对有权访问记录的人员的完整读/写控制。

有关详细信息，请参阅 Apple [CloudKit 框架引用](https://developer.apple.com/reference/clockkit)并[CloudKit JS 框架引用](https://developer.apple.com/reference/cloudkitjs)。

> [!IMPORTANT]
> Apple [提供工具](https://developer.apple.com/support/allowing-users-to-manage-data/)，用于帮助开发人员正确处理欧盟一般数据保护条例 (GDPR)。

<a name="Safari-App-Extensions-Support" />

### <a name="safari-app-extensions-support"></a>Safari 应用扩展支持

Safari 应用扩展允许应用将扩展的 Safari web 浏览器的行为，同时与 macOS Sierra 紧密集成。 由于 macOS Safari 应用扩展的工作类似于 iOS Safari 应用扩展，因此可以轻松到端口从一个系统到另一个。

有关详细信息，请参阅 Apple [Safari 应用扩展编程指南](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternetWeb/Conceptual/SafariAppExtension_PG/index.html#//apple_ref/doc/uid/TP40017319)。

<a name="Security-and-Privacy-Enhancements" />

### <a name="security-and-privacy-enhancements"></a>安全和隐私的增强功能

Apple 对安全和隐私 macOS Sierra，可帮助提高应用程序的安全性，并确保最终用户的隐私，其中包括应用程序中进行了多项增强功能：

- 新`NSAllowsArbitraryLoadsInWebContent`键可以添加到应用程序的`Info.plist`文件，并将允许网页时的应用程序的其余部分仍启用 Apple 传输安全 (ATS) 保护正确加载。
- 常见数据安全体系结构 (CDSA) API 已弃用，并应替换为要生成非对称密钥的 SecKey API。
- 对于所有 SSL/TLS 连接，RC4 对称密码现在默认处于禁用状态。 此外，安全传输 API 不再支持 SSLv3，建议应用程序停止使用 sha-1 和 3DES 加密方法越早越好。
- 由于新剪贴板中 iOS 10 和 macOS Sierra 允许用户设备之间进行复制和粘贴，该 API 已扩展为允许剪贴板限制为特定设备和加盖时间戳来给定时点会自动清除。 此外，命名的选不再保留，并且应替换为共享粘贴板容器。
- 如果应用程序访问受保护的数据 （如用户的日历），它_必须_正确目的字符串值中具有键声明该意图其`Info.plist`文件 (`NSCalendarUsageDescription`在日历的情况下)。
- 未发送通过 Mac App Store 的开发人员已签名应用现在可以充分利用 CloudKit、 iCloud 密钥链、 iCloud 驱动器、 远程推送通知、 MapKit 和 VPN 的权利。
- macOS Sierra 不再支持交付外部代码或数据以及在其 zip 存档或无符号的磁盘映像中的代码签名者应用，因为在运行时之前不识别的运行时路径。

此外，在 macOS Sierra 上运行的应用 （或更高版本） 必须以静态方式声明其意图，通过输入一个或多个隐私中的特定项来访问特定功能或用户信息及其`Info.plist`向该应用程序为何希望获得的用户说明的文件访问权限。

由于 macOS Sierra 与 iOS 10 共享这些更改，请参阅我们的 iOS 10[安全性和隐私增强功能](~/ios/app-fundamentals/security-privacy.md)指南以获取详细信息。

<a name="Smart-Card-Driver-Extension-Support" />

### <a name="smart-card-driver-extension-support"></a>智能卡驱动程序扩展支持

在 macos Sierra，该应用可以创建`NSExtension`基于内容的只读访问权限允许从特定类型的智能卡的智能卡驱动程序。 （替换不推荐使用的常见数据安全体系结构方法） 的系统密钥链内然后显示此信息。

有关详细信息，请参阅 Apple [CryptoTokenKit 框架引用](https://developer.apple.com/reference/cryptotokenkit)。

<a name="Unified-Logging" />

### <a name="unified-logging"></a>统一日志记录

统一日志记录提供有效的消息传递跨所有级别的系统使用单个 API 应用。 使用统一日志记录应用程序可以精细控制包括隐私控制与活动跟踪，更加易于调试的多个级别的日志记录。 

活动跟踪和日志记录一起使用时，日志记录提供了自动消息关联。

macOS Sierra 包括新的控制台应用 （在应用程序/实用程序），能够显示来自多个源包括连接的设备的日志数据。 它还支持已标记化，并已保存搜索并显示跨多个进程的相关消息之间的连接。

此外，日志消息可以查看和维护使用命令行工具。

有关详细信息，请参阅 Apple[日志记录引用](https://developer.apple.com/reference/os/1891852-logging)。

<a name="Wide-Color" />

### <a name="wide-color"></a>宽域颜色

macOS Sierra 扩展的扩展范围像素格式和整个系统的核心图形、 Core 映像、 裸机和 AVFoundation 等框架的范围内所有颜色空间的支持。 对具有广泛的颜色显示设备的支持进一步减轻通过提供在整个图形堆栈整个此行为。

此外，`AppKit`已被修改才能在新扩展**sRGB**色彩空间，使其更轻松地混合中广泛的颜色色域显著的性能损失的颜色。

使用宽颜色时，Apple 提供的以下最佳实践：

- `NSColor` 现在使用 sRGB 颜色空间，将无法再将值与`0.0`到`1.0`范围。 如果应用依赖于上一次固定行为，它将需要修改为 macOS Sierra。
- 当使用核心图形或金属等低级别 API 提供图像处理，应用应使用支持 16 位浮点值的更大范围颜色空间和像素格式。 必要时，应用将需要手动将颜色组件值。
- 核心图形、 Core 映像和金属性能着色器所有提供两个颜色空间之间进行转换的新方法。

若要获取详细信息，请参阅我们[简介广泛的颜色](~/ios/platform/wide-color.md)指南。

<a name="Additional-Framework-Changes" />

## <a name="additional-framework-changes"></a>其他 Framework 更改

除了主要 framework 更改和添加上面列出的内容，Apple 进行了许多其他的次要 framework 更改在 macOS Sierra 中。

若要获取详细信息，请参阅我们[其他 Framework 更改](~/mac/platform/introduction-to-macos-sierra/additional-framework-changes.md)指南。

<a name="Deprecated-APIs" />

## <a name="deprecated-apis"></a>弃用的 API

在 macOS Sierra 中不推荐使用以下 Api:

- 不再支持 HFS 标准文件系统。

请参阅 Apple [OS X v10.12 API 差异](https://developer.apple.com/library/prerelease/content/releasenotes/Miscellaneous/APIDiffsMacOS10_12/index.html)的弃用功能和更改的文档的完整列表。

## <a name="related-links"></a>相关链接

- [Mac 示例](https://developer.xamarin.com/samples/mac/)
- [什么是 OS X 10.12 中的新增功能](https://developer.apple.com/library/prerelease/content/releasenotes/MacOSX/WhatsNewInOSX/Articles/OSXv10.html#//apple_ref/doc/uid/TP40017145-SW1)
