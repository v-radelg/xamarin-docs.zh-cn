---
title: 在 Xamarin 中使用属性列表
description: 本文档介绍 Visual Studio for Mac 的图形和高级属性列表（info.plist）编辑器来使用 info.plist 和 info.plist。 它说明了如何在 Visual Studio for Mac 中设置用于 iOS 应用程序的图标和启动映像。
ms.prod: xamarin
ms.assetid: 5E687043-0443-377C-9A12-9C5A05958646
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/18/2017
ms.openlocfilehash: ac1ded56137cf85ec5852358f75240176dbc7671
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70286793"
---
# <a name="working-with-property-lists-in-xamarinios"></a>在 Xamarin 中使用属性列表

_本文档介绍 Visual Studio for Mac 的图形和高级属性列表（info.plist）编辑器来使用 info.plist 和 info.plist。它说明了如何在 Visual Studio for Mac 中设置用于 iOS 应用程序的图标和启动映像。_

Visual Studio for Mac 功能 info.plist 编辑器，使编辑应用属性和功能变得更加容易。 Visual Studio for Mac 有两个 plists- `Info.plist`用于编辑应用程序属性和图标`Entitlements.plist`以及用于管理应用程序功能。 本指南介绍 plists，并概述如何在 Visual Studio for Mac 中使用它们。 有关 info.plist 的信息，请参阅[使用权利](~/ios/deploy-test/provisioning/entitlements.md)指南。

## <a name="infoplist"></a>Info.plist

信息属性列表（ `Info.plist`）是必需的 iOS 文件，它提供有关应用程序配置到系统的信息。 Visual Studio for Mac 的自`Info.plist`定义编辑器功能由编辑器窗口左下角的选项卡控制三个面板：

 [![](property-lists-images/tabs.png "编辑器窗口左下角的 info.plist 编辑器选项卡")](property-lists-images/tabs.png#lightbox)

每个面板控制不同的属性，如下所述：

- **应用程序面板**-一种图形界面，用于设置常用应用程序属性以及图标和启动图像;指定 maps integration 和后台处理模式。
- **高级面板**-"高级" 面板是用于指定支持的文档类型、UTI 和 URL 类型的位置。
- **源面板**-"源" 面板控制不太常见的属性和应用程序的自定义属性。


接下来的三个部分将更详细地研究每个面板的功能。

## <a name="application-panel"></a>应用程序面板

Visual Studio for Mac 功能是一种图形界面， `Info.plist`用于编辑应用程序的常见条目：

1. 应用程序属性
1. 支持的设备类型
1. 每种设备类型的支持方向
1. 状态栏样式和颜色
1. 图标和启动屏幕
1. 映射和后台模式


下一节将对此进行更详细的介绍。

 <a name="iOS_Application_Target" />


### <a name="ios-application-target"></a>iOS 应用程序目标

本部分包含描述你的应用程序的重要信息。
此处存储的**标识符**必须与在 iTunes Connect 中输入的捆绑标识符（适用于应用商店应用）以及 IOS 预配门户应用 id 列表和开发和分发证书匹配。

 [![](property-lists-images/image24.png "iOS 应用程序目标")](property-lists-images/image24.png#lightbox)

### <a name="device-deployment"></a>设备部署

 [![](property-lists-images/deployment.png "设备部署")](property-lists-images/deployment.png#lightbox)

根据上面的 "**应用程序目标**" 部分中的 "**设备**" 下拉列表中的选择，可以有选择地显示 "设备**部署**信息" 部分。 在情节提要驱动的应用程序中，**主接口**下拉菜单设置为**mainstoryboard.storyboard** 。 如果用户界面是用代码完全编写的，则可以保留为空。

### <a name="supported-device-orientations"></a>支持的设备方向

 **支持的设备方向**控制应用如何响应设备旋转。 IPhone/iPad 应用程序仅支持**纵向**或一切 **，但却**不是很常见。 通常，除游戏之外的所有 iPad 应用程序都应支持所有方向。

### <a name="status-bar-styles"></a>状态栏样式

"**状态栏样式**" 部分是用于编辑应用程序的`UIStatusBarStyle`图形界面：

 [![](property-lists-images/status.png "状态栏样式")](property-lists-images/status.png#lightbox)

 <a name="Icons" />


### <a name="icons-launch-images-and-itunes-artwork"></a>图标、启动图像和 iTunes 图稿

有关如何在 info.plist 文件中使用图标、图像和图稿的信息，请参阅使用[图像](~/ios/app-fundamentals/images-icons/index.md)指南。




### <a name="maps-integration-and-background-modes"></a>映射集成和后台模式

包含`Info.plist`用于指定 maps integration 和后台处理模式的特殊部分。 选择要支持的选项会将所需的属性添加到你的应用程序。

 [![](property-lists-images/maps.png "映射集成")](property-lists-images/maps.png#lightbox)

有关使用映射的详细信息，请参阅 Xamarin [IOS maps](~/ios/user-interface/controls/ios-maps/index.md)指南。

 [![](property-lists-images/bging.png "后台模式")](property-lists-images/bging.png#lightbox)

有关后台模式的详细信息，请参阅适用于[iOS](~/ios/app-fundamentals/backgrounding/introduction-to-backgrounding-in-ios.md)的 Xamarin 后台处理指南。

## <a name="advanced-panel"></a>高级面板

"高级" 面板控制应用程序支持的文档类型和 URL 方案。

 [![](property-lists-images/image34.png "高级面板")](property-lists-images/image34.png#lightbox)

 <a name="Document_Types" />


## <a name="document-types"></a>文档类型

对于支持打开特定类型文件的应用程序，iOS 提供`CFBundleDocumentTypes`密钥。 如果我们希望应用程序支持某些已知文件类型（例如 Pdf），我们将向密钥添加 PDF 值。 本部分提供一种方便的方法来输入将存储在`CFBundleDocumentTypes` `Info.plist`文件中的密钥中的数据。

有关如何配置这些值的详细信息，请参阅有关[注册应用程序支持的文件类型](https://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Articles/RegisteringtheFileTypesYourAppSupports.html)的文档。

## <a name="utis"></a>Uti

有时，应用程序需要支持打开自定义文件类型。 例如，我们可能希望打开带有自*定义扩展的映像文件。* 若要指定自定义文件类型，我们将创建一个自定义的 UTI-通用类型标识符`UIExportedTypeDeclarations` -使用密钥。 下面的屏幕截图演示了如何为 xam 扩展创建自定义 UTI：

 [![](property-lists-images/uti.png "Uti 编辑器")](property-lists-images/uti.png#lightbox)

正如导出的类型 uti 指定特定于你的应用的自定义 uti 时， `UIImportedTypeDeclarations` *导入的类型 uti* （key）指定了支持但不由你的应用程序拥有的自定义类型。

有关使用自定义 Uti 的详细信息，请参阅 Apple 的[注册文件类型应用支持](https://developer.apple.com/library/ios/documentation/FileManagement/Conceptual/understanding_utis/understand_utis_declare/understand_utis_declare.html#//apple_ref/doc/uid/TP40001319-CH204-SW1)指南。

## <a name="custom-urls"></a>自定义 Url

URL 方案名称（也称为协议）是 URL 的第一部分。 例如， `http://`和`https://`是常用的 URL 方案。 你可以选择为应用程序创建自定义 URL 方案。 自定义 URL 方案用于与其他应用程序来回通信和发送数据。 以下屏幕截图演示了如何创建名`monkeys://`为的新的自定义 URL 方案：

 [![](property-lists-images/url.png "自定义 Url")](property-lists-images/url.png#lightbox)



有关实现自定义 URL 方案的详细信息，请参阅[本指南的 Apple 实现自定义 Url 方案部分](https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/AdvancedAppTricks/AdvancedAppTricks.html)

## <a name="source-panel"></a>源面板

`Info.plist`文件的 "**源**" 选项卡允许添加或编辑自定义值。 Visual Studio for Mac 提供最常见属性的列表：

 [![](property-lists-images/image31.png "从下拉列表中添加新属性")](property-lists-images/image31.png#lightbox)

对于已知属性 Visual Studio for Mac 会列出有效值，如以下屏幕截图所示：

 [![](property-lists-images/image32.png "从 \"已知值\" 列表中选择一个值")](property-lists-images/image32.png#lightbox)

Visual Studio for Mac 还会检测属性类型，如下所示：

 [![](property-lists-images/image33.png "可用的属性类型")](property-lists-images/image33.png#lightbox)

有关可选属性的其他信息，请参阅 Apple 的[应用相关资源](https://developer.apple.com/library/ios/#DOCUMENTATION/iPhone/Conceptual/iPhoneOSProgrammingGuide/App-RelatedResources/App-RelatedResources.html)链接。

 <a name="Entitlements" />

## <a name="summary"></a>总结

本文演示了如何使用图形和 info.plist 编辑器来编辑常见的应用配置以及指定图标和启动图像。 它还引入了`Entitlements.plist`用于添加和管理应用功能的。


## <a name="related-links"></a>相关链接

- [IDE](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide)
- [与应用相关的资源](https://developer.apple.com/library/ios/#DOCUMENTATION/iPhone/Conceptual/iPhoneOSProgrammingGuide/App-RelatedResources/App-RelatedResources.html)
- [注册应用支持的文件类型](https://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Articles/RegisteringtheFileTypesYourAppSupports.html)
- [实现自定义 URL 方案](https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/AdvancedAppTricks/AdvancedAppTricks.html)
- [资产目录格式引用](https://developer.apple.com/library/archive/documentation/Xcode/Reference/xcode_ref-Asset_Catalog_Format/index.html#//apple_ref/doc/uid/TP40015170-CH18-SW1)
