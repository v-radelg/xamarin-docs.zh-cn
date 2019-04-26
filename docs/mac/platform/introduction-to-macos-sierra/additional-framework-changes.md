---
title: 其他 macOS Sierra Framework 更改
description: 本文档介绍了细微的更改和现有框架在 macOS Sierra 中引入的增强功能。 它将检查对加速 framework、 AppKit、 AVFoundation、 核心数据、 Core 映像、 Foundation，和的详细信息的更改。
ms.prod: xamarin
ms.assetid: CA701269-D11E-4DE3-89C1-58EF8993A482
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 6ed827c018931e5b79887dc355f136e2a84623d6
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61031622"
---
# <a name="additional-macos-sierra-framework-changes"></a>其他 macOS Sierra Framework 更改

<a name="Accelerate-Framework-Enhancements" />

## <a name="accelerate-framework-enhancements"></a>加速 Framework 增强功能

适用于 macOS Sierra 加速框架进行了以下增强功能：

- 添加了正交调幅 （整型微积分）。
- 添加了用于构造神经网络的基本功能。
- 添加了几何谓词函数来测试两个几何对象的交集之类的内容。

<a name="AppKit-Framework-Enhancements" />

## <a name="appkit-framework-enhancements"></a>AppKit Framework 增强功能

向 macOS Sierra AppKit 框架进行了以下增强功能：

- 多项增强功能到`NSCollectionView`如：
    - **可折叠的部分**-允许用户以折叠成单个水平行的集合视图部分。
    - **浮点标头**的页眉和页脚现在可以浮动 （在流布局） 使用相同的 API [UICollectionView](https://developer.apple.com/reference/uikit/uicollectionview)在 iOS 中。
    - **可滚动的背景视图**-现在可以设置集合视图背景以及内容滚动。
- 延迟的视图布局处理过程已进行了优化并扩展。
- 拖放 API 现在包括新`NSFilePromiseProvider`和`NSFilePromiseReceiver`类以支持拖动以。
- 多个方便构造函数已添加到现有控件：
    -  `NSButton` 包含新的构造函数，用于创建下压按钮、 复选框和单选按钮。
    -  `NSTextField` 包含用于创建包装和非换行的标签、 特性化的标签和可编辑文本字段的新构造函数。
    -  `NSSegmentedControl` 包含用于创建从一组标签或映像的分段的控件的新构造函数。
    -  `NSSlider` 包含用于创建水平线性滑块的新构造函数。
    -  `NSImageView` 包含用于创建从非可编辑图像视图的新构造函数给定`NSImage`。
-  新`NSGridView`已添加到网格中使用变量子视图的集合大小的行和列可以动态隐藏或显示的自动布局。

<a name="AVFoundation-Framework-Enhancements" />

## <a name="avfoundation-framework-enhancements"></a>AVFoundation Framework 增强功能

向 macOS Sierra AVFoundation 框架进行了以下增强功能：

- 在 macOS，则应用不再必须实现不同[AVPlayerItem](https://developer.apple.com/reference/avfoundation/avplayeritem)行为基于内容类型。 只需设置`Rate`属性和 AVFoundation 将确定足够的内容时可用于播放而无需停止。
- 新`AVPlayerLooper`类可以更轻松地在播放期间循环一段给定的媒体。
- `AVAssetDownloadURLSession`类可用于下载和更高版本播放 FairPlay 加密 HLS 流。

<a name="Core-Data-Framework-Enhancements" />

## <a name="core-data-framework-enhancements"></a>核心数据框架增强功能

向 macOS Sierra 核心数据框架进行了以下增强功能：

- 根[NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext)对象支持并发故障和提取而无需序列化。
- [NSPersistentStoreCoordinator](https://developer.apple.com/reference/coredata/nspersistentstorecoordinator)类维护的 SQLite 数据存储区池。
- [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) SQLite 在 WAL 日志模式下支持新的查询生成的数据存储的对象功能可以将托管对象上下文 (MOC) 固定到将来中提取的特定数据库版本和出错的事务。
- 使用高级`NSPersistenceContainer`引用`NSPersistentStoreCoordinator`， [NSManagedObjectModel](https://developer.apple.com/reference/coredata/nsmanagedobjectmodel)和其他核心数据配置资源。
- 已添加到多个新的便捷方法`NSManagedObject`轻松地执行提取操作，并创建子类。

有关详细信息，请参阅 Apple[核心数据框架引用](https://developer.apple.com/reference/coredata)。

<a name="Core-Image-Framework-Enhancements" />

## <a name="core-image-framework-enhancements"></a>核心映像 Framework 增强功能

向 macOS Sierra 核心映像框架进行了以下增强功能：

- `ImageWithExtent`方法[CIFilter](https://developer.apple.com/reference/coreimage/cifilter)类可用于筛选器操作中插入自定义处理。 核心映像将调用给定的回调之间筛选器时处理输出的图像或显示。
- 应用程序现在可以通过之前和之后处理传入和传出的颜色空间转换处理颜色空间以外的核心映像上下文的工作颜色空间中的映像。
- 核心映像内核现在可以请求特定像素输出格式。
- 已添加以下新的映像筛选器： `CINinePartTitled`， `CINinePartStretched`， `CIHueSaturationValueGradient`，`CIEdgePreserveUpsampleFilter`和`CIClamp`。

<a name="Foundation-Framework-Enhancements" />

## <a name="foundation-framework-enhancements"></a>Foundation 框架增强功能

适用于 macOS Sierra Foundation 框架进行了以下增强功能：

- 使用[NSDimentions](https://developer.apple.com/reference/foundation/nsdimension) API 用于表示、 转换和显示许多最常见的物理单元如质量，长度、 速度、 持续时间和温度。
- 使用[NSISO8601DateFormatter](https://developer.apple.com/reference/foundation/nsiso8601dateformatter)类以进行分析和生成 ISO 8601 格式的日期。
- 使用新[NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval)类，以使日期和时间间隔计算，如持续时间，用于比较的时间间隔和测试的时间间隔的交叉点。
- 使用[NSPersonNameComponentsFormatter](https://developer.apple.com/reference/foundation/nspersonnamecomponentsformatter)类，以分析从字符串的人的名称的元素。
- 使用新[NSURLSessionTaskMetrics](https://developer.apple.com/reference/foundation/nsurlsessiontaskmetrics)类来为网络会话的 URL 获取指标。

有关详细信息，请参阅 Apple [Foundation 发行说明适用于 OS X v10.12 和 iOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/Miscellaneous/RN-Foundation-OSX10.12/index.html)。

<a name="GameKit-Framework-Enhancements" />

## <a name="gamekit-framework-enhancements"></a>GameKit Framework 增强功能

向 macOS Sierra GameKit 框架进行了以下增强功能：

- **游戏中心应用**已弃用，从 macOS 中删除。 如果应用使用 GameKit，其_必须_提供其自己的界面以显示 GameKit 功能，例如排行榜，等等。 
- 新的仅限 iCloud 帐户类型由[GKCloudPlayer](https://developer.apple.com/reference/gamekit/gkcloudplayer)类。
- 新[GKGameSession](https://developer.apple.com/reference/gamekit/gkgamesession)类提供通用的解决方案用于管理在游戏中心永久性数据存储。 `GKGameSession` 维护的玩家列表和应用程序是负责窗体实现如何以及何时参与者日期存储、 检索或播放机之间交换。 在许多情况下游戏会话可以替换现有的基于轮次的匹配项、 实时的匹配项或持久性方法保存的游戏。

<a name="GamePlayKit-Framework-Enhancements" />

## <a name="gameplaykit-framework-enhancements"></a>GamePlayKit Framework 增强功能

向 macOS Sierra GamePlayKit 框架进行了以下增强功能：

- 过程干扰生成已添加，并可用于增强真实性自然纹理中的、 添加照相机移动到的真实性并帮助生成丰富游戏世界。
- 使用空间分区用于提高搜索效率的游戏世界数据进行分区。
- 新的 Monte Carlo 规划师 ([GKMonteCarloStrategist](https://developer.apple.com/reference/gameplaykit/gkmontecarlostrategist)) 已添加对详尽的可能移动计算。
- 已添加新的决策树 API ([GKDecisionTree](https://developer.apple.com/reference/gameplaykit/gkdecisiontree)并[GKDecisionNode](https://developer.apple.com/reference/gameplaykit/gkdecisionnode)) 来增强游戏构建 AI。
- 三维支持已添加到现有的代理，并使用新的路径查找行为[GKAgent3D](https://developer.apple.com/reference/gameplaykit/gkagent3d)并[GKGraphNode3D](https://developer.apple.com/reference/gameplaykit/gkgraphnode3d)类。
- 使用新[GKMeshGraph](https://developer.apple.com/reference/gameplaykit/gkmeshgraph)类以提供高性能、 自然的路径。
- 新[GKScene](https://developer.apple.com/reference/gameplaykit/gkscene)并[GKSKNodeComponent](https://developer.apple.com/reference/gameplaykit/gksknodecomponent)类进行组合 GameplayKit 和 SpriteKit 比以往更容易。

<a name="Metal-Framework-Enhancements" />

## <a name="metal-framework-enhancements"></a>金属框架增强功能

向 macOS Sierra 金属框架进行了以下增强功能：

- 三维应用和游戏现在可以使用_分割_以有效地呈现复杂的场景和通过 GPU 的几何图形。
- 使用函数专用化来创建高度优化的材料和光线组合函数场景的集合。
- 提供的更好地控制资源分配，以优化性能的金属基于堆使用资源的应用和 Memoryless 呈现器目标。

若要了解详细信息，请参阅 Apple[金属编程指南](https://developer.apple.com/library/prerelease/content/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221)。

<a name="ModelIO-Framework-Enhancements" />

## <a name="model-io-framework-enhancements"></a>模型 I/O Framework 增强功能

向 macos Sierra 模型 I/O 框架进行了以下增强功能：

- 现在支持美元文件格式。
- 使用新`MDLMaterialPropertyGraph`类，以轻松地支持运行时更改为模型。
- 签名距离字段支持添加到[MDLVoxelArray](https://developer.apple.com/reference/modelio/mdlvoxelarray)类。
- 使用新`MDLLightProbeIrradianceDataSource`类，以帮助在 Light 探测位置。

<a name="Photos-Framework-Enhancements" />

## <a name="photos-framework-enhancements"></a>照片 Framework 增强功能

向 macOS Sierra 照片框架进行了以下增强功能：

- 实时照片编辑现已推出为支持的照片框架的应用和照片编辑扩展插件 (用于内部的照片和相机应用)。
- 使用新[PHLivePhotoEditingContext](https://developer.apple.com/reference/photos/phlivephotoeditingcontext)类，以编辑同时适用于视频和仍的 Live 照片内容。
- 使用[CIImageProcessorInput](https://developer.apple.com/reference/coreimage/ciimageprocessorinput)并[CIImageProcessorOutput](https://developer.apple.com/reference/coreimage/ciimageprocessoroutput)类，以充分利用新的核心映像处理器功能来执行编辑。
- 若要支持 Live 照片[PHLivePhoto](https://developer.apple.com/reference/photos/phlivephoto)并[PHLivePhotoView](https://developer.apple.com/reference/photosui/phlivephotoview)类具有从 iOS 移植到 macOS。

<a name="SceneKit-Framework-Enhancements" />

## <a name="scenekit-framework-enhancements"></a>SceneKit Framework 增强功能

向 macOS Sierra SceneKit 框架进行了以下增强功能：

- 现在包括新的以物理方式基于呈现 (PBR) 系统，用于更简单资产创作更真实的结果。
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

<a name="Security-Framework-Enhancements" />

## <a name="security-framework-enhancements"></a>安全框架增强功能

向 macOS Sierra 安全框架进行了以下增强功能：

- `SecKey`已得到改进并跨所有平台 （iOS、 tvOS、 watchOS 和 macOS） 的统一接口。

<a name="SpriteKit-Framework-Enhancements" />

## <a name="spritekit-framework-enhancements"></a>SpriteKit Framework 增强功能

向 macOS Sierra SpriteKit 框架进行了以下增强功能：

- Tilemaps 现在支持针对 2D、 2.5 D 和使用端滚动游戏方形、 六边形和等角的磁贴形状`SKTileMapMode`， `SKTileGroup`，`SKTileGroupRule`和`SKTileSet`类。
- 使用新`SKWarpGeometry`类来拉伸或扭曲[SKSpriteNode](https://developer.apple.com/reference/spritekit/skspritenode)或[SKEffectNode](https://developer.apple.com/reference/spritekit/skeffectnode)呈现。 新[SKAction](https://developer.apple.com/reference/spritekit/skaction)类可用于对 warp 效果之间的转换进行动画处理。
- 自定义着色器可以提供属性 (`SKAttribute`)，可单独进行配置的情况下提供属性值，使用着色器的每个节点 (`SKAttributeValue`)。
- [SKView](https://developer.apple.com/reference/spritekit/skview)类提供了几种新方法来提供精细地控制何时以及如何呈现场景。

<a name="New-Frameworks" />

## <a name="new-frameworks"></a>新框架

已向 macOS Sierra 添加以下框架：

- **意向 Framework** -此框架使应用能够检查交互 （如位置或用户操作），并根据该信息采取措施。
- **SafariServices Framework** -此框架允许应用程序以用于 macOS 和 iOS Safari （如内容阻止程序） 开发应用程序扩展。

## <a name="related-links"></a>相关链接

- [Mac 示例](https://developer.xamarin.com/samples/mac/)
- [什么是 OS X 10.12 中的新增功能](https://developer.apple.com/library/prerelease/content/releasenotes/MacOSX/WhatsNewInOSX/Articles/OSXv10.html#//apple_ref/doc/uid/TP40017145-SW1)
