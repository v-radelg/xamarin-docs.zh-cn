---
title: tvOS 10 简介
description: 本文介绍适用于 Xamarin.tvOS 开发人员的所有新的和修改 Api 和 tvOS 10 中提供的功能。
ms.prod: xamarin
ms.assetid: CB9C1EC8-6008-43AD-977E-976AE7C73DD8
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 260d01d6aa8344dd3cf107f1ffc34167c457a491
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61269304"
---
# <a name="introduction-to-tvos-10"></a>tvOS 10 简介

_本文介绍适用于 Xamarin.tvOS 开发人员的所有新的和修改 Api 和 tvOS 10 中提供的功能。_

与新的 tvOS 10 SDK Apple 已包含新 Api 和服务，可让开发人员创建的应用和功能的新类别。 

TvOS 10 的详细信息，请参阅 Apple [tvOS + 应用](https://developer.apple.com/tvos/)文档。

## <a name="whats-new-in-tvos-10"></a>什么是新建 tvOS 10

Apple 已新增了几个新 Api 和服务以及对现有功能，包括许多增强功能的 tvOS 10:

## <a name="new-user-interface-styles"></a>新用户界面样式

tvOS 10 现在支持深色和浅色用户界面主题，所有内置的 UIKit 控件将自动适应，基于用户的首选项。

当创建和实现新的自定义 UI 控件，开发人员应使用[UITraitCollection](https://developer.apple.com/reference/uikit/uitraitcollection)类，以适应用户的所选主题。

有关详细信息，请参阅我们[新用户界面样式](~/ios/tvos/platform/user-interface-styles.md)文档。

## <a name="security-and-privacy-enhancements"></a>安全和隐私的增强功能

对安全和隐私 tvOS 10，可帮助开发人员提高他们的应用的安全性，并确保最终用户的隐私，Apple 进行了多项增强功能。

因此，watchOS 3 （或更高版本） 上运行的应用必须以静态方式声明其意图，通过输入一个或多个隐私中的特定项来访问特定功能或用户信息及其`Info.plist`向该应用程序为何希望获得访问权限的用户说明的文件。

由于 tvOS 10 与 iOS 10 共享这些更改，请参阅我们的 iOS 10[安全性和隐私增强功能](~/ios/app-fundamentals/security-privacy.md)指南以获取详细信息。

## <a name="video-subscriber-account"></a>视频订户帐户

新的 tvOS 10，视频订户帐户框架允许应用该身份验证支持的流式处理或点播视频使用其有线或卫星电视提供商为最终用户使用单一登录体验进行身份验证。

<!--To find out more, please see our [Video Subscriber Account](~/ios/platform-features/introduction-to-ios10/video-subscriber-account/) guide.-->

## <a name="wide-color"></a>宽域颜色

tvOS 10 扩展的扩展范围像素格式和整个系统的核心图形、 Core 映像、 裸机和 AVFoundation 等框架的范围内所有颜色空间的支持。 对具有广泛的颜色显示设备的支持进一步减轻通过提供在整个图形堆栈整个此行为。

此外，`UIKit`已被修改才能在新扩展**sRGB**色彩空间，使其更轻松地混合中广泛的颜色色域显著的性能损失的颜色。

使用宽颜色时，Apple 提供的以下最佳实践：

 - `UIColor` 现在使用 sRGB 颜色空间，将无法再将值与`0.0`到`1.0`范围。 如果应用依赖于上一次固定行为，它将需要针对 tvOS 10 进行修改。
 - 如果该应用程序执行的自定义呈现`UIImages`，使用新[UIGraphicsImageRender](https://developer.apple.com/reference/uikit/uigraphicsimagerenderer)类指定为扩展范围或标准范围格式的使用。
 - 当使用核心图形或金属等低级别 API 提供图像处理，应用应使用支持 16 位浮点值的更大范围颜色空间和像素格式。 必要时，应用将需要手动将颜色组件值。
 - 核心图形、 Core 映像和金属性能着色器所有提供两个颜色空间之间进行转换的新方法。

若要获取详细信息，请参阅我们[简介广泛的颜色](~/ios/platform/wide-color.md)指南。

## <a name="newly-available-existing-frameworks"></a>最新可用的现有框架

在 iOS （和不 tvOS） 可用的多个框架进行提供对 tvOS 10 如：

 - ExternalAccessory
 - HomeKit
 - MultipeerConnectivity
 - 照片
 - ReplayKit
 - UserNotification

## <a name="additional-framework-changes"></a>其他 Framework 更改

除了主要 framework 更改和添加上面列出的内容，Apple 进行了许多其他的次要 framework 更改 tvOS 10 中。

若要获取详细信息，请参阅我们[其他 Framework 更改](~/ios/tvos/platform/introduction-to-tvos10/additional-framework-changes.md)指南。

## <a name="deprecated-apis"></a>弃用的 API

任何 Api 或框架不推荐使用的 tvOS 10。 请参阅 Apple [tvOS 10 API 差异](https://developer.apple.com/library/prerelease/content/releasenotes/General/tvOS10APIDiffs/index.html)API 修改的文档的完整列表。



## <a name="related-links"></a>相关链接

- [tvOS 示例](https://developer.xamarin.com/samples/tvos/all/)
- [什么是 tvOS 10 中的新增功能](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)
