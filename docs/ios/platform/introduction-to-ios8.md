---
title: iOS 8 简介
description: IOS 8，Apple 提供了大量的新框架和 Api，用于激发且能够取悦开发人员。 本指南中我们将介绍这些新的 Api，并了解开发人员和用户 iOS 8 可如何获益。
ms.prod: xamarin
ms.assetid: 33AD66C0-3743-49FE-9DCE-88ED3A16BA63
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/14/2017
ms.openlocfilehash: 9299322eb20561444262c2b2ba87191d2bddcde4
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61317611"
---
# <a name="introduction-to-ios-8"></a>iOS 8 简介

_IOS 8，Apple 提供了大量的新框架和 Api，用于激发且能够取悦开发人员。本指南中我们将介绍这些新的 Api，并了解开发人员和用户 iOS 8 可如何获益。_

iOS 7 直观地从哪些用户和开发人员有位出现的情况，直接从第一个 iPhone 操作系统更改整个 iOS 用户界面。 IOS 8 继续这通过允许用户控制其生命周期直接从其 iPhone 的几乎所有方面对于开发人员，提供许多框架。 例如与分析运行状况和适用性*HealthKit*，密码是使用生物识别身份验证使用过时*LocalAuthentication*，*应用扩展*打开第三方应用程序之间的通信通道和*HomeKit*使你能够将您的家庭财产变成未来的主页。 

如果 iOS 7 有关取悦用户，iOS 8 重点介绍取悦开发人员提供一整个系列的这些 tasty 的新工具。 

本指南介绍适用于 Xamarin.iOS 开发人员的新 Api。  

此外，还有几个 iOS 8 中，对其进行详细的本文档末尾中已弃用的 Api。

## <a name="requirements"></a>要求

在 Visual Studio for Mac 中创建 iOS 8 应用需要以下：

- **Xcode 7 和 iOS 8 或更高版本**– Apple 的最新的 Xcode 和 iOS Api 需要安装并配置开发人员的计算机上。
- **Visual Studio for Mac** – 应安装并配置用户设备上的 Visual Studio for Mac 的最新版本。
- **iOS 8 设备或模拟器**– 运行 iOS 8 的测试的最新版本的 iOS 设备。

## <a name="home-and-leisure"></a>家庭和閒旅遊

iOS 8 已帮助使牢固地直接到通过使用 HomeKit 和 HealthKit 家庭的核心计划 Apple 和 iOS 设备。 在本部分中，我们将介绍如何这两个这些新框架起作用，以及它们如何可以集成到 Xamarin.iOS 应用程序。

## <a name="homekit"></a>HomeKit

控制在从你的 iPhone 设备不是技术的新的应用程序;许多连接主页产品可通过 iOS 应用程序进行控制。 但是 HomeKit 现在只需这一步提升家庭自动化设备的常见协议和对某些制造商，如 iHome，Philips 和 Honeywell 的使用公共 API。 对用户来说，这意味着他们可以控制的无缝地从其主页几乎所有方面在一个应用程序。 它不相关，知道它们使用的 Philips Hue 灯泡或嵌套警报。 用户还可以将链接放入"场景"许多智能家庭过程。

与 HomeKit，第三方应用和 Siri 可以发现附件并将它们添加到其个人家庭配置数据库、 编辑和对此数据，其执行操作和与其附件和其服务，以执行操作。

### <a name="configuration"></a>配置

下图显示了基本的层次结构的 HomeKit 附件的配置：

![](introduction-to-ios8-images/image1.png "下图显示了 HomeKit 附件的配置的基本层次结构")
 
若要开始使用 HomeKit，开发人员将需要确保其预配配置文件选择的 HomeKit 服务。 Apple 的 Xcode 还提供开发人员提供一个 HomeKit 模拟器外接程序。 可以在中找到此[Apple 开发人员中心](https://developer.apple.com/downloads/index.action)下`Hardware IO Tools for Xcode`。 

有关详细信息，请参阅我们[HomeKit](~/ios/platform/homekit.md)指南。

## <a name="healthkit"></a>HealthKit

HealthKit 是 iOS 8 的运行状况相关信息提供集中、 协调和安全数据存储中引入一个框架。 隐私和安全的运行状况信息，并使用运行状况应用程序中，针对用户的仪表板，可确保操作系统。 使用用户的权限、 应用程序可以读取和写入各种运行状况信息。

有关 Xamarin.iOS 应用程序中使用此的详细信息，请参阅[简介 HealthKit](~/ios/platform/healthkit.md)指南。

## <a name="extending-iphone-functionality"></a>扩展 iPhone 功能
与 iOS8，开发人员正在提供了更多控制谁可以其应用程序中，并增加了的功能更多开放之间的通信使用第三方应用。 功能，例如应用扩展和文档选取器打开应用程序如何使用 Apple 的生态系统中，提供了的可能。

### <a name="app-extensions"></a>应用扩展
应用扩展，一句话说，两者是彼此通信的第三方应用一种方法。 若要维护高安全性标准，并要秉承的沙盒应用程序的完整性，此通信不会直接在应用程序之间。 相反，它由执行*扩展*中间。

创建应用程序扩展的第一步是定义正确的扩展点，这可确保行为和正确的 Api 的可用性。 若要在 Visual Studio for Mac 创建应用扩展，将其添加到现有应用程序通过将新项目添加到你的解决方案。

在中**新的项目**对话框导航到**C#**  >  **iOS** > **Unified API**  > **扩展**，如以下屏幕截图中所示：

![](introduction-to-ios8-images/image2.png "创建一个新的扩展")
 
新项目对话框提供用于创建应用扩展，七个新的项目模板，以及下面讨论。 请注意，许多扩展与其他新的 Api 在 iOS 中，如文档选取器：

- **操作**– 这样，开发人员可以创建允许用户执行某些任务的唯一自定义操作按钮
- **自定义键盘**– 这允许开发人员将添加到的范围通过添加自己自定义的一个内置的 Apple 键盘。 常用键盘 Swype 使用它来将其键盘到 iOS。
- **文档选取器**– 这包含文档选取器视图控制器，它允许用户访问应用程序的沙箱以外的文件。
- **文档选取器文件提供程序**– 此文件使用文档选取器提供安全的存储。
- **照片编辑**– 此延伸筛选器，编辑照片应用程序时为用户提供更好地控制和更多选项编辑他们的照片中已提供 Apple 的工具。
- **今天**– 这使应用程序能够在通知中心的今天部分中显示的小组件。

在 Xamarin 中使用应用扩展的详细信息，请参阅[应用程序扩展插件简介](~/ios/platform/extensions.md)指南。

### <a name="touch-id"></a>Touch ID

Touch ID 的引入了 iOS 7 中作为一种对用户进行身份验证-类似于密码。 但是，被限制在解锁设备、 应用商店、 使用 iTunes 和 iCloud 密钥链仅进行身份验证 

现在有两种方法使用 Touch ID 作为 iOS 8 应用程序使用本地身份验证 API 中的身份验证机制。 目前不能使用本地身份验证进行远程身份验证。

首先，它有助于通过使用新密钥链访问控制列表 (Acl) 的现有密钥链服务。 密钥链数据可以是与成功的身份验证的用户指纹解锁。

其次，LocalAuthentication 提供两种方法进行身份验证本地应用程序。 开发人员应使用`CanEvaluatePolicy`来确定设备是否处于可接受 Touch ID，然后`EvaluatePolicy`启动身份验证操作。

有关在 Touch ID，并了解如何将其集成到 Xamarin.iOS 应用程序的详细信息，请参阅[简介到 TouchID](~/ios/platform/touchid.md)指南。

### <a name="document-picker"></a>文档选取器

再次返回文档选取器适用于用户 iCloud 驱动器以允许用户打开已在不同的应用中创建、 导入和对其进行处理并将其导出的文件。 这将创建的直观工作流，并因此更好的体验，为用户。 iCloud 同步进一步执行此步骤 — 一个应用程序中所做的任何更改也将会反映一致地在所有设备。

若要了解更多的深度中的文档选取器并了解如何将其集成到 Xamarin.iOS 应用程序，请参见[简介文档选取器](~/ios/platform/document-picker.md)指南。

### <a name="handoff"></a>Handoff

切换，这是更大的连续性功能的一部分，采用更进一步好地集成 OS X 和 iOS。 这包括跨平台使用 AirDrop，取得的调用 iPhone、 iPad 和 Mac 中的和改进 tethering 从 iPhone 上的 SMS 的功能。

适用于 iOS 8 和 Yosemite，和需要的 iCloud 帐户记录所有不同的设备到你想要使用非切换。 它应使用预安装的 Apple 应用，包括 Safari、 iWork、 映射、 日历和联系人。

有关详细信息，请参阅我们[移交](~/ios/platform/handoff.md)指南。

## <a name="unified-storyboards"></a>统一的情节提要
iOS 8 包括一个新易于使用的机制来创建用户界面-统一的情节提要。 使用单个情节提要以涵盖所有不同的硬件的屏幕尺寸，可以则返回 true 的"设计一次，使用许多"样式中创建快速且高度可响应的视图。

在 iOS8 之前, 使用开发人员`UIInterfaceOrientation`纵向和横向模式之间进行区分和`UIInterfaceIdiom`iOS 设备之间进行区分。 在 iOS8 就不再需要创建单独的情节提要，适用于 iPhone 和 iPad 设备 — 通过使用确定方向和设备*大小类*。

每个设备定义按大小类，在垂直和水平轴，并有两种类型的大小在 iOS 8 中的类：

- **正则**-这是大屏幕大小 （例如 iPad) 或让人觉得 （如 UIScrollView 较大的一个小工具
- **Compact** -这是用于较小的设备 （如 iPhone)。 此大小会考虑设备的方向。

如果同时使用了两个概念，结果将是一个 2 x 2 网格，定义可在这两个不同方向的不同可能大小，如以下关系图中所示：

![](introduction-to-ios8-images/image3.png "代表 2 x 2 网格，用于定义可用的不同可能大小中的这两个不同方向的关系图")
 
有关大小类的详细信息，请参阅[统一情节提要简介](~/ios/user-interface/storyboards/unified-storyboards.md)。

## <a name="photo-kit"></a>照片工具包
照片工具包是一个新框架，允许应用程序查询系统图像库和创建自定义用户界面，可以查看和修改其内容。 它包括多个类，表示图像和视频资源，以及资产，例如唱片集和文件夹的集合。

有关详细信息，请参阅我们[PhotoKit](~/ios/platform/photokit.md)指南。

## <a name="games"></a>游戏

<a name="scenekit" />

### <a name="scene-kit"></a>场景工具包

场景工具包是一个三维场景图形 API，用于简化 3D 图形。 它在 OS X 10.8 中首次引入，并具有现在转到 iOS 8。 使用场景工具包创建沉浸式 3D 可视化效果和休闲 3D 游戏不需要 OpenGL 的专业知识。 基于常见场景图形的概念，Scene Kit 抽象化 OpenGL 和 OpenGL ES，需要很轻松地添加 3D 内容到应用程序的复杂性。 但是，如果你是 OpenGL 方面的专家，场景工具包具有关联直接使用 OpenGL 以及强大的支持。 它还包括许多功能进行补充的 3D 图形，例如物理引擎，并与几个其他 Apple 框架，例如核心动画、 Core 映像和 Sprite Kit 很好地集成。

有关详细信息，请参阅我们[SceneKit](~/ios/platform/gaming/scenekit.md)文档。

<a name="spritekit" />

### <a name="sprite-kit"></a>子画面工具包

Sprite Kit，Apple 的 2D 游戏框架，已在 iOS 8 和 OS X Yosemite 一些有趣的新功能。 其中包括与 Scene Kit、 着色器的支持、 照明、 阴影、 约束、 法线贴图生成和物理引擎增强功能的集成。 具体而言，新的物理功能使其很容易将逼真的效果添加到游戏。

有关详细信息，请参阅我们[SpriteKit](~/ios/platform/gaming/spritekit.md)文档。

## <a name="other-changes"></a>其他更改
上面所述的 iOS 8 的主要变化，以及 Apple 此外已更新，许多现有框架。 下面详细介绍这些：

- **[Core 映像](https://developer.apple.com/library/prerelease/ios/documentation/GraphicsImaging/Reference/CoreImagingRef/index.html#//apple_ref/doc/uid/TP40001171)** – Apple 已通过添加对检测的矩形区域，更好地支持扩展其图像处理框架，并在映像内的 QR 代码。 Mike Bluestein 探讨了这在他的博客帖子标题为[iOS 8 中的图像检测](https://blog.xamarin.com/image-detection-in-ios-8/)

## <a name="deprecated-apis"></a>弃用的 API
在 iOS 8 中所做的所有改进，与多个 Api 已弃用。 下面详细介绍其中一些。

- **[UIApplication](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplication_Class/index.html#//apple_ref/occ/cl/UIApplication)**  – 已弃用的方法和属性用于注册远程通知。 这些是 registerForRemoteNotificationTypes 和 enabledRemoteNotificationTypes。
- **[UIViewController](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIViewController_Class/index.html#//apple_ref/occ/cl/UIViewController)**  – 特征和大小类已替代的方法和属性用来描述接口方向。 请参阅[统一情节提要简介](~/ios/user-interface/storyboards/unified-storyboards.md)有关如何使用这些详细信息。

- **[UISearchDisplayController](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UISearchDisplayController_Class/index.html#//apple_ref/occ/cl/UISearchDisplayController)**  – 这已由 UISearchController iOS8 中。

## <a name="summary"></a>总结
在本文中介绍了一些 Apple iOS 8 中引入的新功能。



## <a name="related-links"></a>相关链接

- [UIKitEnhancements （示例）](https://developer.xamarin.com/samples/monotouch/ios8/UIKitEnhancements)
- [应用程序扩展插件简介](~/ios/platform/extensions.md)
- [CloudKit 简介](~/ios/data-cloud/intro-to-cloudkit.md)
- [文档选取器简介](~/ios/platform/document-picker.md)
- [HealthKit 简介](~/ios/platform/healthkit.md)
- [手动相机控件简介](~/ios/user-interface/controls/intro-to-manual-camera-controls.md)
- [TouchID 简介](~/ios/platform/touchid.md)
- [统一的情节提要简介](~/ios/user-interface/storyboards/unified-storyboards.md)
