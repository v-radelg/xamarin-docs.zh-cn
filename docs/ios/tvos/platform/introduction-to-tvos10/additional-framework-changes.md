---
title: 其他 tvOS 10 框架更改
description: 本文档介绍了细微的更改和对现有框架在 iOS 10 中所做的增强功能。 它讨论了 AVFoundation、 AVKit、 核心数据、 核心图形、 Foundation、 GameKit、 GameplayKit，和的详细信息的更新。
ms.prod: xamarin
ms.assetid: F771640A-F92E-4954-82D5-2D720434971E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 4ca9856251d35a741d496b6e8b45b07e851e0e89
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60932533"
---
# <a name="additional-tvos-10-frameworks-changes"></a>其他 tvOS 10 框架更改

除了对 tvOS 的重大更改，Apple 已在 tvOS 10 中进行修改和改进到多个现有框架。

<a name="AV-Foundation-Framework" />

## <a name="avfoundation-framework-additions"></a>AVFoundation Framework 新增功能

AVFoundation 框架包括以下增强功能：

 - TvOS 10，在该应用不再实现不同[AVPlayerItem](https://developer.apple.com/reference/avfoundation/avplayeritem)行为基于内容类型。 只需设置`Rate`属性和 AVFoundation 将确定足够的内容时可用于播放而无需停止。
 - 新`AVPlayerLooper`类可以更轻松地在播放期间循环一段给定的媒体。

<a name="AVKit-Framework-Enhancements" />

## <a name="avkit-framework-enhancements"></a>AVKit Framework 增强功能

AVKit 框架包括以下增强功能：

 - 应用程序现在可以控制跳过的行为[AVPlayerViewController](https://developer.apple.com/reference/avkit/avplayerviewcontroller)以便跳过手势可能会将移动到播放列表或提前在当前项的下一项。

<a name="Core-Data-Enhancements" />

## <a name="core-data-enhancements"></a>核心数据增强功能

tvOS 10 包括核心数据框架的以下增强功能：

 - 根[NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext)对象支持并发故障和提取而无需序列化。
 - [NSPersistentStoreCoordinator](https://developer.apple.com/reference/coredata/nspersistentstorecoordinator)类维护的 SQLite 数据存储区池。
 - [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) SQLite 在 WAL 日志模式下支持新的查询生成的数据存储的对象功能可以将托管对象上下文 (MOC) 固定到将来中提取的特定数据库版本和出错的事务。
 - 使用高级`NSPersistenceContainer`引用`NSPersistentStoreCoordinator`， [NSManagedObjectModel](https://developer.apple.com/reference/coredata/nsmanagedobjectmodel)和其他核心数据配置资源。
 - 已添加到多个新的便捷方法`NSManagedObject`轻松地执行提取操作，并创建子类。

有关详细信息，请参阅 Apple[核心数据框架引用](https://developer.apple.com/reference/coredata)。

<a name="Core-Graphics-Enhancements" />

## <a name="core-graphics-enhancements"></a>核心图形增强功能

tvOS 10 包括核心图形框架的以下增强功能：

 - 新[CGColorConverterRef](https://developer.apple.com/reference/coregraphics/cgcolorconverterref)类可用于执行一系列的颜色转换。

<a name="Core-Image-Enhancements" />

## <a name="core-image-enhancements"></a>核心图像增强功能

tvOS 10 进行核心映像框架的以下增强功能：

 - `ImageWithExtent`方法[CIFilter](https://developer.apple.com/reference/coreimage/cifilter)类可用于筛选器操作中插入自定义处理。 核心映像将调用给定的回调之间筛选器时处理输出的图像或显示。
 - 应用程序现在可以通过之前和之后处理传入和传出的颜色空间转换处理颜色空间以外的核心映像上下文的工作颜色空间中的映像。
 - 对进行了多个呈现性能方面的增强`UIImage`呈现 （时由 Core 映像映像存储区） 中`UIImageView`对象。 
 - `UIImage` 对象标记的范围 gamut 将呈现为范围内所有颜色在`UIImageView`支持广泛的颜色的 iOS 设备上的对象。
 - 核心映像内核代码现在可以请求特定像素输出格式。

此外，已添加以下新的核心映像筛选器：

 - `CINinePartTiled`
 - `CINinePartStretched`
 - `CIHueSaturationValueGradient`
 - `CIEdgePreserveUpsampleFilter`
 - `CIClamp`

<a name="Foundation-Enhancements" />

## <a name="foundation-enhancements"></a>Foundation 增强功能

适用于 tvOS 10 Foundation 框架进行了以下增强功能：

 - 使用新[NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval)类，以使日期和时间间隔计算，如持续时间，用于比较的时间间隔和测试的时间间隔的交叉点。
 - 多个新属性已添加到[NSLocal](https://developer.apple.com/reference/foundation/nslocale)类来获取本地信息和可用的显示格式。
 - 使用新[NSMeasurement](https://developer.apple.com/reference/foundation/nsmeasurement)类来转换之间不同单位的度量值 (UOM) 或对不同 UOMs 中的值执行计算。
 - 使用新[NSMeasurementFormatter](https://developer.apple.com/reference/foundation/nsmeasurementformatter)类设置用于向最终用户显示本地化的度量值的格式。
 - 使用新[NSUnit](https://developer.apple.com/reference/foundation/nsunit)并[NSDimension](https://developer.apple.com/reference/foundation/nsdimension)类用于表示特定 UOMs。

<a name="GameKit-Enhancements" />

## <a name="gamekit-enhancements"></a>GameKit 增强功能

TvOS 10 中的 GameKit 框架已得到以下增强功能：

 - 新的仅限 iCloud 帐户类型由[GKCloudPlayer](https://developer.apple.com/reference/gamekit/gkcloudplayer)类。
 - 新[GKGameSession](https://developer.apple.com/reference/gamekit/gkgamesession)类提供通用的解决方案用于管理在游戏中心永久性数据存储。 `GKGameSession` 维护的玩家列表和应用程序是负责窗体实现如何以及何时参与者日期存储、 检索或播放机之间交换。 在许多情况下游戏会话可以替换现有的基于轮次的匹配项、 实时的匹配项或持久性方法保存的游戏。

<a name="GameplayKit-Enhancements" />

## <a name="gameplaykit-enhancements"></a>GameplayKit 增强功能

TvOS 10 中的 GameplayKit 框架已得到以下增强功能：

 - 过程干扰生成已添加，并可用于增强真实性自然纹理中的、 添加照相机移动到的真实性并帮助生成丰富游戏世界。
 - 使用空间分区用于提高搜索效率的游戏世界数据进行分区。
 - 新的 Monte Carlo 规划师 ([GKMonteCarloStrategist](https://developer.apple.com/reference/gameplaykit/gkmontecarlostrategist)) 已添加对详尽的可能移动计算。
 - 已添加新的决策树 API ([GKDecisionTree](https://developer.apple.com/reference/gameplaykit/gkdecisiontree)并[GKDecisionNode](https://developer.apple.com/reference/gameplaykit/gkdecisionnode)) 来增强游戏构建 AI。
 - 三维支持已添加到现有的代理，并使用新的路径查找行为[GKAgent3D](https://developer.apple.com/reference/gameplaykit/gkagent3d)并[GKGraphNode3D](https://developer.apple.com/reference/gameplaykit/gkgraphnode3d)类。
 - 使用新[GKMeshGraph](https://developer.apple.com/reference/gameplaykit/gkmeshgraph)类以提供高性能、 自然的路径。
 - 新[GKScene](https://developer.apple.com/reference/gameplaykit/gkscene)并[GKSKNodeComponent](https://developer.apple.com/reference/gameplaykit/gksknodecomponent)类进行组合 GameplayKit 和 SpriteKit 比以往更容易。

<a name="Metal-Enhancements" />

## <a name="metal-enhancements"></a>裸机增强功能

TvOS 10 中的金属框架已得到以下增强功能：

 - 三维应用和游戏现在可以使用_分割_以有效地呈现复杂的场景和通过 GPU 的几何图形。
 - 使用函数专用化来创建高度优化的材料和光线组合函数场景的集合。
 - 提供的更好地控制资源分配，以优化性能的金属基于堆使用资源的应用和 Memoryless 呈现器目标。

若要了解详细信息，请参阅 Apple[金属编程指南](https://developer.apple.com/library/prerelease/content/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221)。

<a name="Metal-Performance-Shaders-Enhancements" />

## <a name="metal-performance-shaders-enhancements"></a>裸机性能着色器增强功能

TvOS 10 中的裸机性能着色器框架已得到以下增强功能：

 - 许多新内核已添加到裸机性能着色器框架，以允许应用程序以充分利用高度优化、 数据并行计算，如颜色空间转换和神经网络操作。

<a name="ModelIO-Enhancements" />

## <a name="modelio-enhancements"></a>ModelIO 增强功能

TvOS 10 中的 ModelIO 框架已得到以下增强功能：

 - 现在支持美元文件格式。
 - 使用新`MDLMaterialPropertyGraph`类，以轻松地支持运行时更改为模型。
 - 签名距离字段支持添加到[MDLVoxelArray](https://developer.apple.com/reference/modelio/mdlvoxelarray)类。
 - 使用新`MDLLightProbeIrradianceDataSource`类，以帮助在 Light 探测位置。

<a name="SceneKit-Enhancements" />

## <a name="scenekit-enhancements"></a>SceneKit 增强功能

TvOS 10 中的 SceneKit 框架已得到以下增强功能：

 - SceneKit 现在包括新的以物理方式基于呈现 (PBR) 系统，用于更简单资产创作更真实的结果。
 - 使用新[SCNLightingModelPhysicallyBased](https://developer.apple.com/reference/scenekit/scnlightingmodelphysicallybased)明暗度模型到产品范围广泛的实心底纹效果要求只有三个基本属性 (`Diffuse`，`Metalness`和`Roughness`)。
 - 自 PBR 最佳基于环境的照明着色的工作原理，使用`LightingEnvironment`要分配基于映像的照明以 tan 整个场景属性。
 - 使用`IESProfileURL`要导入上实际值，例如强度 （以流明） 定义基本照明和颜色 （以度为单位 Kelvin） 温度的实际灯具属性。
 - [SCNCamera](https://developer.apple.com/reference/scenekit/scncamera)类可以通过使用 HDR 功能和影响提供更高版本的真实性。 使用自适应暴露创建自动效果或使用晕影、 色边和颜色评分为游戏添加 filmatic 效果。
 - PBR 和 HDR 相机功能提供更好的结果比传统的呈现技术，因此，SceneKit 现在中及执行的所有颜色计算线性颜色空间 （使用 P3 颜色域范围内颜色设备显示器上）。
 - SceneKit 现在颜色按读取颜色配置文件信息匹配的所有颜色。
 - SceneKit 解释所有着色器类型线性 RGB 颜色空间中的颜色组件值。
 - 由于 SceneKit 读取并调整纹理图像中的颜色配置文件信息，使用的所有图像资产目录以确保提供此信息。
 - 同时线性颜色空间呈现，可以禁用通过指定的范围内颜色`SCNDisableLinearSpaceRendering`并`SCNDisableWideGamut`在应用的密钥`Info.plist`。
 - 生成任意多边形一下灵长类 （不管是从文件加载或以编程方式生成） 来指定与新的几何图形[SCNGeometryPrimitiveTypePolygon](https://developer.apple.com/reference/scenekit/1772322-scenekit_enumerations/scngeometryprimitivetype/scngeometryprimitivetypepolygon)类。

<a name="SpriteKit-Enhancements" />

## <a name="spritekit-enhancements"></a>SpriteKit 的增强功能

TvOS 10 中的 SpriteKit 框架已得到以下增强功能：

 - Tilemaps 现在支持针对 2D、 2.5 D 和使用端滚动游戏方形、 六边形和等角的磁贴形状`SKTileMapMode`， `SKTileGroup`，`SKTileGroupRule`和`SKTileSet`类。
 - 使用新`SKWarpGeometry`类来拉伸或扭曲[SKSpriteNode](https://developer.apple.com/reference/spritekit/skspritenode)或[SKEffectNode](https://developer.apple.com/reference/spritekit/skeffectnode)呈现。 新[SKAction](https://developer.apple.com/reference/spritekit/skaction)类可用于对 warp 效果之间的转换进行动画处理。
 - 自定义着色器可以提供属性 (`SKAttribute`)，可单独进行配置的情况下提供属性值，使用着色器的每个节点 (`SKAttributeValue`)。
 - [SKView](https://developer.apple.com/reference/spritekit/skview)类提供了几种新方法来提供精细地控制何时以及如何呈现场景。

<a name="UIKit-Enhancements" />

## <a name="uikit-enhancements"></a>UIKit 增强功能

TvOS 10 中的 UIKit 框架已得到以下增强功能：

 - 焦点 API 已经过增强，除了支持非视图项的焦点到`UIViews`。 支持焦点的项_必须_实现`IUIFocusItem`接口。
 - 新`UIGraphicsRender`类提供一种面向对象的从 UIKit 呈现或核心图形创建位图或 pdf 文件的方法，并替换不推荐使用`UIGraphicsBeginImageContext`方法。
 - `UIUserInterfaceStyle`添加类以确定哪些用户界面主题 （深色或浅色） 是当前处于活动状态。
 - 添加了新的完全交互式的、 基于对象的、 可中断动画支持，并且 van 链接到手势。 请参阅 Apple [UIViewAnimating 协议参考](https://developer.apple.com/reference/uikit/uiviewanimating)， [UIViewPropertyAnimator 类引用](https://developer.apple.com/reference/uikit/uiviewpropertyanimator)， [UITimingCurveProvider 协议参考](https://developer.apple.com/reference/uikit/uitimingcurveprovider)， [UICubicTimingParameters 类引用](https://developer.apple.com/reference/uikit/uicubictimingparameters)并[UISpringTimingParameter 类引用](https://developer.apple.com/reference/uikit/uispringtimingparameters)有关详细信息。
 - 新`UIPreviewInteraction`和`UIPreviewInteractionDelegate`允许应用将提供用于查看和 pop 操作自定义接口。
 - 新`UIAccessibilityCustomRotor`类允许应用程序以提供对 Voice Over 等辅助技术的自定义的特定于上下文的功能。
 - 使用`UIAccessibilityIsAssistiveTouchRunning`和`UIAccessibilityAssistiveTouchStatusDidChangeNotification`符号来确定是否启用 AssistiveTouch。
 - 使用`UIAccessibilityHearingDevicePairedEar`和`UIAccessibilityHearingDevicePairedEarDidChangeNotification`要获取的任何状态的符号配对 MFi 听力辅助工具。
 - 新[UIPasteboard](https://developer.apple.com/reference/uikit/uipasteboard) API 提供了新选项 （如生存期限制），并将自动声明公共类类型兼容的内容类型。
 - 若要在标签中支持动态类型，文本字段和文本框中使用的新`PreferredFontForTextStyle`方法的`UIFont`类。
 - 若要确定是否元素应更新其字体时设备`UIContentSizeCategory`发生更改，使用`AdjustsFontForContentSizeCategory`属性的`UIContentSizeCategoryAdjusting`委托。
 - 应用程序现在可以控制用于选项卡栏项，如文本和背景颜色徽章的外观。
 - 刷新控件中现在支持所有滚动视图和滚动视图子类 (如`UICollectionView`)。
 - `OpenURL`方法的`UIApplication`类以异步方式调用现在支持打开已完成后，将调用一个完成处理程序。
 - 启动 CloudKit 共享和修改其属性使用的新`UICloudSharingController`和`UICloudSharingControllerDelegate`类。
 - 充分利用预提取的单元格以改进的滚动体验`UICollectionViews`与新`UICollectionViewDataSourcePrefetching`委托。



## <a name="related-links"></a>相关链接

- [tvOS 示例](https://developer.xamarin.com/samples/tvos/all/)
- [什么是 tvOS 10 中的新增功能](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)
