---
title: 使用 Xamarin.iOS 中的属性列表
description: 本文档介绍 Visual Studio for Mac 的图形和高级属性列表 (.plist) 编辑器使用 Info.plist 和 Entitlements.plist。 它说明了设置图标和启动图像从 iOS 应用程序在 Visual Studio for mac。
ms.prod: xamarin
ms.assetid: 5E687043-0443-377C-9A12-9C5A05958646
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 87015163eca9fdb2fb8cee35e74d5031314fd42e
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61245485"
---
# <a name="working-with-property-lists-in-xamarinios"></a>使用 Xamarin.iOS 中的属性列表

_本文档介绍 Visual Studio for Mac 的图形和高级属性列表 (.plist) 编辑器使用 Info.plist 和 Entitlements.plist。它说明了设置图标和启动图像从 iOS 应用程序在 Visual Studio for mac。_

Visual Studio for Mac 的功能，使编辑应用程序属性和功能更轻松的图形.plist 编辑器。 Visual Studio for Mac 具有两个.plists-`Info.plist`用于编辑应用程序属性和图标和`Entitlements.plist`用于管理应用程序功能。 本指南介绍 Info.plists 并概述以及在中使用 Visual Studio for mac。 Entitlements.plist 的信息，请参阅[使用权利](~/ios/deploy-test/provisioning/entitlements.md)指南。

## <a name="infoplist"></a>Info.plist

信息属性列表 ( `Info.plist`) 是所需的 iOS 文件，提供对系统的应用程序的配置信息。 Visual Studio for Mac 的自定义`Info.plist`控制的选项卡底部的三个面板角的编辑器窗口的编辑器功能：

 [![](property-lists-images/tabs.png "在底部 Info.plist 编辑器选项卡左侧的编辑器窗口")](property-lists-images/tabs.png#lightbox)

每个面板控件不同的属性，如下所述：

-  **应用程序面板**-一个图形界面来设置常见应用程序属性，以及图标和启动映像; 指定地图集成和后台处理模式。
-  **高级面板**-高级面板是位置来指定支持的文档类型，Uti，和 URL 类型。
-  **源面板**-源面板控件不太常见的属性，以及应用程序的自定义属性。


接下来的三部分调查的更多详细信息中的每个面板的功能。

## <a name="application-panel"></a>应用程序面板

Visual Studio for Mac 提供用于编辑常见的图形界面`Info.plist`应用程序的条目：

1.  应用程序属性
1.  受支持的设备类型
1.  对于每个设备类型的支持的方向
1.  状态栏样式和颜色
1.  图标和启动屏幕
1.  映射和后台模式


以下各节中更详细地描述了这些内容。

 <a name="iOS_Application_Target" />


### <a name="ios-application-target"></a>iOS 应用程序目标

本部分包含用于描述应用程序的重要信息。
**标识符**存储此处必须与输入时，在 iTunes Connect （适用于应用商店应用） 中，还开发和分发证书 iOS 预配门户应用 Id 列表中的捆绑包标识符。

 [![](property-lists-images/image24.png "iOS 应用程序目标")](property-lists-images/image24.png#lightbox)

### <a name="device-deployment"></a>部署设备

 [![](property-lists-images/deployment.png "部署设备")](property-lists-images/deployment.png#lightbox)

设备**部署**具体取决于所选内容中有选择地，显示信息部分**设备**中的下拉列表**应用程序目标**上面一节。 **主界面**下拉列表设置为**MainStoryboard**情节提要驱动的应用程序中。 如果用户界面完全用代码编写，则此可以保留为空。

### <a name="supported-device-orientations"></a>支持的设备方向

 **支持设备方向**控制应用如何响应设备旋转。 它是很常见的 iPhone/iPad 应用仅支持**纵向**，或所有内容，但**正面朝下**。 通常所有 iPad 应用程序，除非游戏应都支持所有方向。

### <a name="status-bar-styles"></a>状态栏样式

**状态条样式**部分是用于编辑应用程序的一个图形界面`UIStatusBarStyle`:

 [![](property-lists-images/status.png "状态栏样式")](property-lists-images/status.png#lightbox)

 <a name="Icons" />


### <a name="icons-launch-images-and-itunes-artwork"></a>图标、 启动映像和 iTunes 图稿

在 Info.plist 文件中使用的图标、 图像和图片的信息可在[处理图像](~/ios/app-fundamentals/images-icons/index.md)指南。




### <a name="maps-integration-and-background-modes"></a>地图集成和后台模式

`Info.plist`包含特殊部分指定地图集成和后台处理模式。 选择你想要支持的选项将添加到你为你的应用程序所需的属性。

 [![](property-lists-images/maps.png "地图集成")](property-lists-images/maps.png#lightbox)

有关使用映射的详细信息，请参阅 Xamarin [iOS 地图](~/ios/user-interface/controls/ios-maps/index.md)指南。

 [![](property-lists-images/bging.png "后台模式")](property-lists-images/bging.png#lightbox)

有关后台模式的详细信息，请参阅 Xamarin [iOS 中的后台处理](~/ios/app-fundamentals/backgrounding/introduction-to-backgrounding-in-ios.md)指南。

## <a name="advanced-panel"></a>高级的面板

高级面板中控制的文档类型和应用程序支持的 URL 方案。

 [![](property-lists-images/image34.png "高级的面板")](property-lists-images/image34.png#lightbox)

 <a name="Document_Types" />


## <a name="document-types"></a>文档类型

对于支持打开特定类型的文件的应用程序，提供 iOS`CFBundleDocumentTypes`密钥。 如果我们希望我们的应用程序以支持某些已知的文件类型-例如 Pdf-我们会将 PDF 值添加到该密钥。 本部分提供了方便地输入数据将存储在`CFBundleDocumentTypes`键中`Info.plist`文件。

请参阅上的文档[注册你的应用程序支持的文件类型](https://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Articles/RegisteringtheFileTypesYourAppSupports.html)有关如何配置这些值的详细信息。

## <a name="utis"></a>Uti

有时应用程序需要支持打开自定义文件类型。 例如，我们可能想要使用自定义扩展打开映像文件 *.xam*。 若要指定自定义文件类型，我们将创建自定义 UTI-通用类型标识符-使用`UIExportedTypeDeclarations`密钥。 下面的屏幕截图演示了如何创建自定义 UTI.xam 扩展：

 [![](property-lists-images/uti.png "Uti 编辑器")](property-lists-images/uti.png#lightbox)

只需为导出的类型 Uti 指定特定于您的应用程序的自定义 Uti*导入类型 Uti* (`UIImportedTypeDeclarations`密钥) 指定受支持，但不是属于你的应用程序的自定义类型。

有关使用自定义 Uti 的详细信息，请参阅 Apple[注册文件类型您应用支持](https://developer.apple.com/library/ios/documentation/FileManagement/Conceptual/understanding_utis/understand_utis_declare/understand_utis_declare.html#//apple_ref/doc/uid/TP40001319-CH204-SW1)指南。

## <a name="custom-urls"></a>自定义 Url

（也称为协议） 的 URL 方案名称是 URL 的第一部分。 例如，`http://`和`https://`是普通的 URL 方案。 您可以创建你的应用程序的自定义 URL 方案。 自定义 URL 方案用于通信和其他应用程序使用来回发送数据。 下面的屏幕截图显示了如何创建名为的新自定义 URL 方案`monkeys://`:

 [![](property-lists-images/url.png "自定义 Url")](property-lists-images/url.png#lightbox)



有关实现自定义 URL 方案的详细信息，请参阅 Apple 的[本指南的实现自定义 URL 方案部分](https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/AdvancedAppTricks/AdvancedAppTricks.html)

## <a name="source-panel"></a>源面板

**源**选项卡`Info.plist`文件允许添加或编辑自定义值。 Visual Studio for Mac 提供了一系列的最常见属性：

 [![](property-lists-images/image31.png "从下拉列表中添加一个新的属性")](property-lists-images/image31.png#lightbox)

Visual Studio for Mac 已知属性将一组有效的值，如以下屏幕截图所示：

 [![](property-lists-images/image32.png "从已知值列表中选择值")](property-lists-images/image32.png#lightbox)

Visual Studio for Mac 还检测属性类型，如所示：

 [![](property-lists-images/image33.png "可用属性类型")](property-lists-images/image33.png#lightbox)

查看 Apple[应用程序相关的资源](https://developer.apple.com/library/ios/#DOCUMENTATION/iPhone/Conceptual/iPhoneOSProgrammingGuide/App-RelatedResources/App-RelatedResources.html)可选属性的其他信息的链接。

 <a name="Entitlements" />

## <a name="summary"></a>总结

本文演示了使用图形和高级.plist 编辑器来编辑常见应用程序配置中指定图标和启动图像。 它还引入了`Entitlements.plist`有关添加和管理应用程序功能。


## <a name="related-links"></a>相关链接

- [IDE](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide)
- [应用程序相关的资源](https://developer.apple.com/library/ios/#DOCUMENTATION/iPhone/Conceptual/iPhoneOSProgrammingGuide/App-RelatedResources/App-RelatedResources.html)
- [注册文件类型应用支持](https://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Articles/RegisteringtheFileTypesYourAppSupports.html)
- [实现自定义 URL 方案](https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/AdvancedAppTricks/AdvancedAppTricks.html)
- [资产目录格式参考](https://developer.apple.com/library/archive/documentation/Xcode/Reference/xcode_ref-Asset_Catalog_Format/index.html#//apple_ref/doc/uid/TP40015170-CH18-SW1)
