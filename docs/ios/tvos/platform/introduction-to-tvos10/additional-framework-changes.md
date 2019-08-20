---
title: 其他 tvOS 10 框架更改
description: 本文档介绍了对 iOS 10 中现有框架进行的少量更改和增强。 它讨论了 AVFoundation、AVKit、Core 数据、核心图形、基础、Gamekit\、GameplayKit 等的更新。
ms.prod: xamarin
ms.assetid: F771640A-F92E-4954-82D5-2D720434971E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 230da58bba68b9411b67baacd53b534ae832510d
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68657440"
---
# <a name="additional-tvos-10-frameworks-changes"></a>其他 tvOS 10 框架更改

除了对 tvOS 的重大更改之外, Apple 还对 tvOS 10 中的几个现有框架进行了修改和改进。

<a name="AV-Foundation-Framework" />

## <a name="avfoundation-framework-additions"></a>添加 AVFoundation 框架

AVFoundation 框架包括以下增强功能:

- 在 tvOS 10 中, 应用不再实现基于内容类型的不同[AVPlayerItem](https://developer.apple.com/reference/avfoundation/avplayeritem)行为。 只需设置`Rate`属性, AVFoundation 就会确定在没有停止的情况下是否有足够的内容可用于播放。
- 使用新`AVPlayerLooper`类可以更轻松地在播放期间循环给定的媒体。

<a name="AVKit-Framework-Enhancements" />

## <a name="avkit-framework-enhancements"></a>AVKit 框架增强功能

AVKit 框架包括以下增强功能:

- 应用现在可以控制[AVPlayerViewController](https://developer.apple.com/reference/avkit/avplayerviewcontroller)的跳转行为, 因此跳过手势可能会移到播放列表中的下一项或在当前项中前进。

<a name="Core-Data-Enhancements" />

## <a name="core-data-enhancements"></a>核心数据增强

tvOS 10 对核心数据框架包括以下增强功能:

- 根[NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext)对象支持并发错误和不序列化的提取。
- [NSPersistentStoreCoordinator](https://developer.apple.com/reference/coredata/nspersistentstorecoordinator)类维护 SQLite 数据存储的池。
- 在 WAL 日志模式下, 具有 SQLite 数据存储的[NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext)对象支持新的查询生成功能, 在该功能中, 托管对象上下文 (MOC) 可以固定到特定数据库版本, 以供将来获取和出错事务。
- 使用高级`NSPersistenceContainer` `NSPersistentStoreCoordinator`引用、 [NSManagedObjectModel](https://developer.apple.com/reference/coredata/nsmanagedobjectmodel)和其他核心数据配置资源。
- 添加了几个新的便利方法`NSManagedObject` , 以便更轻松地执行提取和创建子类。

有关详细信息, 请参阅 Apple 的[核心数据框架参考](https://developer.apple.com/reference/coredata)。

<a name="Core-Graphics-Enhancements" />

## <a name="core-graphics-enhancements"></a>核心图形增强功能

tvOS 10 包括对核心图形框架的以下增强功能:

- 新的[CGColorConverterRef](https://developer.apple.com/reference/coregraphics/cgcolorconverterref)类可用于执行一系列颜色转换。

<a name="Core-Image-Enhancements" />

## <a name="core-image-enhancements"></a>核心映像增强功能

tvOS 10 对核心映像框架进行了以下改进:

- [CIFilter 类](https://developer.apple.com/reference/coreimage/cifilter)的方法可用于将自定义处理插入筛选器`ImageWithExtent`操作。 核心映像将在处理图像以进行输出或显示时调用筛选器之间的给定回调。
- 应用现在可以通过在处理之前和之后转换颜色空间来处理核心图像上下文工作颜色空间之外的颜色空间中的图像。
- 已`UIImage`在对象中`UIImageView`呈现 (由核心图像映像存储支持时) 的几个呈现性能增强功能。 
- `UIImage`标记为宽域的对象将在支持宽色的 iOS `UIImageView`设备上的对象中呈现为广角颜色。
- 核心映像内核代码现在可以请求特定的像素输出格式。

此外, 还添加了以下新的核心映像筛选器:

- `CINinePartTiled`
- `CINinePartStretched`
- `CIHueSaturationValueGradient`
- `CIEdgePreserveUpsampleFilter`
- `CIClamp`

<a name="Foundation-Enhancements" />

## <a name="foundation-enhancements"></a>基础增强功能

已对 tvOS 10 的基础框架进行了以下改进:

- 使用新的[NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval)类可以进行日期和时间间隔计算, 如持续时间, 用于比较间隔和测试间隔交点。
- 向[NSLocal](https://developer.apple.com/reference/foundation/nslocale)类添加了几个新属性, 以获取本地信息和可用的显示格式。
- 使用新的[NSMeasurement](https://developer.apple.com/reference/foundation/nsmeasurement)类在不同的度量单位 (UOM) 之间进行转换, 或对不同 UOMs 中的值执行计算。
- 使用新的[NSMeasurementFormatter](https://developer.apple.com/reference/foundation/nsmeasurementformatter)类来设置要向最终用户显示的本地化度量值的格式。
- 使用新的[NSUnit](https://developer.apple.com/reference/foundation/nsunit)和[NSDimension](https://developer.apple.com/reference/foundation/nsdimension)类来表示特定的 UOMs。

<a name="GameKit-Enhancements" />

## <a name="gamekit-enhancements"></a>Gamekit\ 增强功能

对 tvOS 10 中的 Gamekit\ 框架进行了以下改进:

- [GKCloudPlayer](https://developer.apple.com/reference/gamekit/gkcloudplayer)类已经实现了新的仅限 iCloud 的帐户类型。
- 新的[GKGameSession](https://developer.apple.com/reference/gamekit/gkgamesession)类提供了用于在 Game Center 上管理持久数据存储的通用解决方案。 `GKGameSession`维护播放器列表, 应用负责实现如何以及何时在参与者之间存储、检索或交换参与者日期。 在许多情况下, 游戏会话可以替换现有的基于转换的匹配、实时匹配或持久的游戏保存方法。

<a name="GameplayKit-Enhancements" />

## <a name="gameplaykit-enhancements"></a>GameplayKit 增强功能

对 tvOS 10 中的 GameplayKit 框架进行了以下改进:

- 添加了过程干扰, 并可用于在自然的纹理中提高真实感, 增加了对相机运动的真实感, 并帮助生成了丰富的游戏世界。
- 使用空间分区对游戏世界数据进行分区, 以便高效搜索。
- 添加了新的 Monte Carlo 战略家 ([GKMonteCarloStrategist](https://developer.apple.com/reference/gameplaykit/gkmontecarlostrategist)), 以便进行可能的移动计算。
- 添加了新的决策树 API ([GKDecisionTree](https://developer.apple.com/reference/gameplaykit/gkdecisiontree)和[GKDecisionNode](https://developer.apple.com/reference/gameplaykit/gkdecisionnode)), 以增强游戏构建 AI。
- 已使用新的[GKAgent3D](https://developer.apple.com/reference/gameplaykit/gkagent3d)和[GKGraphNode3D](https://developer.apple.com/reference/gameplaykit/gkgraphnode3d)类将3d 支持添加到现有代理和路径查找行为。
- 使用新的[GKMeshGraph](https://developer.apple.com/reference/gameplaykit/gkmeshgraph)类来提供高性能、外观上的路径。
- 新的[GKScene](https://developer.apple.com/reference/gameplaykit/gkscene)和[GKSKNodeComponent](https://developer.apple.com/reference/gameplaykit/gksknodecomponent)类使得 GameplayKit 和 SpriteKit 的组合比以往更容易。

<a name="Metal-Enhancements" />

## <a name="metal-enhancements"></a>金属增强功能

已对 tvOS 10 中的金属框架进行了以下改进:

- 3D 应用和游戏现在可以通过 GPU 使用_分割_来有效地呈现复杂的场景和几何。
- 使用函数特殊化为场景创建高优化的材料和光源组合函数集合。
- 提供资源分配的精细控制, 使用资源堆和 Memoryless 呈现目标优化基于金属的应用的性能。

若要了解详细信息, 请参阅 Apple 的[金属编程指南](https://developer.apple.com/library/prerelease/content/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221)。

<a name="Metal-Performance-Shaders-Enhancements" />

## <a name="metal-performance-shaders-enhancements"></a>金属绩效着色器增强功能

已对 tvOS 10 中的金属性能着色器框架进行了以下改进:

- 许多新内核已添加到金属性能着色器框架, 以允许应用程序利用高优化的数据并行计算, 如颜色空间转换和神经网络操作。

<a name="ModelIO-Enhancements" />

## <a name="modelio-enhancements"></a>ModelIO 增强功能

对 tvOS 10 中的 ModelIO 框架进行了以下改进:

- 现在支持 USD 文件格式。
- 使用新`MDLMaterialPropertyGraph`类可轻松支持对模型的运行时更改。
- 已将已签名的距离字段支持添加到了[MDLVoxelArray](https://developer.apple.com/reference/modelio/mdlvoxelarray)类中。
- 使用新`MDLLightProbeIrradianceDataSource`类来帮助进行轻型探测放置。

<a name="SceneKit-Enhancements" />

## <a name="scenekit-enhancements"></a>SceneKit 增强功能

对 tvOS 10 中的 SceneKit 框架进行了以下改进:

- SceneKit 现在提供了一个新的基于物理的渲染 (.PBR) 系统, 通过更简单的资产创作获得更真实的结果。
- 使用新的[SCNLightingModelPhysicallyBased](https://developer.apple.com/reference/scenekit/scnlightingmodelphysicallybased)明暗度模型来模拟各种现实的阴影效果,`Diffuse` `Metalness`同时仅需要三个基本属性 (和`Roughness`)。
- 由于 .pbr 着色最适用于基于环境的照明, 因此使用`LightingEnvironment`属性将基于图像的照明分配给茶色的整个场景。
- 使用 " `IESProfileURL`属性" 可导入在实际值 (如 "亮度" (流明) 和色温温度 (开氏度)) 中定义光照基准的实际光源固定装置。
- 使用 HDR 特性和效果, [SCNCamera](https://developer.apple.com/reference/scenekit/scncamera)类可提供更大的真实感。 使用自适应曝光创建自动效果, 或使用 vignetting、颜色 fringing 和颜色评分向游戏添加 filmatic 效果。
- 与传统的呈现技术相比, .PBR 和 HDR 相机功能提供了更好的结果, 因此, SceneKit 现在在线性颜色空间中执行所有颜色计算 (使用单色设备显示器上的 P3 颜色范围)。
- SceneKit 通过阅读颜色配置文件信息, 使颜色与所有颜色匹配。
- SceneKit 解释所有着色器类型的线性 RGB 颜色空间中的颜色分量值。
- 由于 SceneKit 读取和调整纹理图像中的颜色配置文件信息, 因此请对所有图像使用资产目录, 以确保提供此信息。
- 可以通过在应用的`SCNDisableLinearSpaceRendering` `Info.plist`中指定和`SCNDisableWideGamut`键来禁用线性颜色空间渲染和宽颜色。
- 生成任意多边形灵长类动物 (从文件加载或以编程方式生成), 以通过新的[SCNGeometryPrimitiveTypePolygon](https://developer.apple.com/reference/scenekit/1772322-scenekit_enumerations/scngeometryprimitivetype/scngeometryprimitivetypepolygon)类指定几何图形。

<a name="SpriteKit-Enhancements" />

## <a name="spritekit-enhancements"></a>SpriteKit 增强功能

对 tvOS 10 中的 SpriteKit 框架进行了以下改进:

- `SKTileMapMode`Tilemaps 现在支持使用`SKTileGroup` `SKTileGroupRule` 、和`SKTileSet`类的2d、2.5 d 和侧滚动游戏的正方形、六边形和等角磁贴形状。
- 使用新`SKWarpGeometry`类伸展或扭曲[SKSpriteNode](https://developer.apple.com/reference/spritekit/skspritenode)或[SKEffectNode](https://developer.apple.com/reference/spritekit/skeffectnode)渲染。 新的[SKAction](https://developer.apple.com/reference/spritekit/skaction)类可用于对扭曲效果之间的转换进行动画处理。
- 自定义着色器可以提供`SKAttribute`属性 (), 这些属性可通过提供属性值 (`SKAttributeValue`) 由使用着色器的每个节点单独配置。
- [SKView](https://developer.apple.com/reference/spritekit/skview)类提供了几种新的方法, 可对场景的呈现时间和方式进行精细的控制。

<a name="UIKit-Enhancements" />

## <a name="uikit-enhancements"></a>UIKit 增强功能

对 tvOS 10 中的 UIKit 框架进行了以下改进:

- 增强了焦点 API, 以支持除之外`UIViews`的非查看项的焦点。 支持焦点的项_必须_实现`IUIFocusItem`接口。
- 新`UIGraphicsRender`的类提供了一个面向对象的方法, 该方法通过 UIKit 呈现或核心图形创建位图或 pdf, `UIGraphicsBeginImageContext`并替换不推荐使用的方法。
- 添加`UIUserInterfaceStyle`了类, 以确定哪个用户界面主题 (深色或浅色) 当前处于活动状态。
- 添加了新的完全交互式的、基于对象的、可中断的动画支持, 并将 van 链接到手势。 Pleas 请参阅 Apple 的[UIViewAnimating 协议参考](https://developer.apple.com/reference/uikit/uiviewanimating)、 [UIViewPropertyAnimator 类引用](https://developer.apple.com/reference/uikit/uiviewpropertyanimator)、 [UITimingCurveProvider 协议参考](https://developer.apple.com/reference/uikit/uitimingcurveprovider)、 [UICubicTimingParameters 类引用](https://developer.apple.com/reference/uikit/uicubictimingparameters)和有关详细信息, 请参阅 [UISpringTimingParameter 类](https://developer.apple.com/reference/uikit/uispringtimingparameters)。
- 新`UIPreviewInteraction`的和`UIPreviewInteractionDelegate`允许应用为速览和 pop 操作提供自定义界面。
- 新`UIAccessibilityCustomRotor`类允许应用为辅助技术 (例如语音) 提供自定义的上下文特定功能。
- `UIAccessibilityIsAssistiveTouchRunning`使用和`UIAccessibilityAssistiveTouchStatusDidChangeNotification`符号来确定是否启用了 AssistiveTouch。
- `UIAccessibilityHearingDevicePairedEar`使用和`UIAccessibilityHearingDevicePairedEarDidChangeNotification`符号可获取任何配对 MFi 听觉助手的状态。
- 新的[UIPasteboard](https://developer.apple.com/reference/uikit/uipasteboard) API 提供了新选项 (如生存期限制), 并将为常见类类型自动声明兼容的内容类型。
- 为了支持标签中的动态类型, 文本字段和文本框使用`PreferredFontForTextStyle` `UIFont`类的新方法。
- 若要在设备`UIContentSizeCategory`发生更改时确定元素是否应更新它的字体, 请`AdjustsFontForContentSizeCategory`使用`UIContentSizeCategoryAdjusting`委托的属性。
- 应用现在可以控制选项卡栏项 (如文本和背景色) 的外观。
- 现在, 中的 "刷新" 控件支持所有滚动视图和滚动视图子类 ( `UICollectionView`如)。
- 异步调用`UIApplication`类的方法现在支持在打开完成后调用的完成处理程序。`OpenURL`
- 使用新`UICloudSharingController`的和`UICloudSharingControllerDelegate`类启动 CloudKit 共享并修改其属性。
- 利用预提取的单元, `UICollectionViews`通过新`UICollectionViewDataSourcePrefetching`委托提高滚动体验。



## <a name="related-links"></a>相关链接

- [tvOS 示例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [TvOS 10 中的新增功能](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)
