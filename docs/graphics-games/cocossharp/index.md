---
title: CocosSharp 的 2D 游戏引擎
description: 本文档所链接到有关使用 CocosSharp 开发游戏的各种文章。 链接的内容介绍示例应用程序、 图形、 动画和的详细信息。
ms.prod: xamarin
ms.assetid: 5E72869D-3541-408B-AB64-D34C777AFB79
author: conceptdev
ms.author: crdun
ms.date: 03/29/2018
ms.openlocfilehash: add73360ea98d8c516e413f0cc0264f68c58d79d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61024360"
---
# <a name="cocossharp-2d-game-engine"></a>CocosSharp 的 2D 游戏引擎

_CocosSharp 是用于生成使用的 2D 游戏的库C#和F#。它是受欢迎的 Cocos2D 引擎的一个.NET 端口。_

## <a name="introduction-to-cocossharp"></a>CocosSharp 简介

CocosSharp 的 2D 游戏引擎提供技术的跨平台游戏。 有关受支持的平台的完整列表请参阅[CocosSharp GitHub 上的 wiki](https://github.com/mono/CocosSharp/wiki)。
这些指南使用C#有关代码示例，虽然 CocosSharp 是完全可操作F#以及。

由提供的核心 CocosSharp [MonoGame framework](http://www.monogame.net/)、 它本身就是跨平台、 硬件加速 API 的导入资产提供图形、 音频、 游戏状态管理、 输入和内容的管道。
CocosSharp 是非常适合 2D 游戏的高效的抽象层。
此外，较大的游戏可以根据游戏的复杂性有所增加执行其自己优化其核心库之外。 换而言之，CocosSharp 提供多种易用性和性能，使开发人员能够快速入门而不会限制游戏大小或复杂性。

此动手视频介绍如何创建简单的跨平台 CocosSharp 游戏：

> [!Video https://channel9.msdn.com/Shows/Visual-Studio-Toolbox/Developing-Cross-platform-2D-Games-in-C-and-CocosSharp/player]

## <a name="bouncinggamegraphics-gamescocossharpbouncing-gamemd"></a>[BouncingGame](~/graphics-games/cocossharp/bouncing-game.md)

![BouncingGame](images/bouncing-game.png "BouncingGame")

本指南介绍了 BouncingGame，包括如何使用游戏的内容，用来构建游戏、 添加游戏逻辑和的详细信息的各种可视元素。

## <a name="fruity-falls-gamegraphics-gamescocossharpfruity-fallsmd"></a>[Fruity Falls 游戏](~/graphics-games/cocossharp/fruity-falls.md)

![Fruity Falls 游戏屏幕截图](images/fruity-falls.png "Fruity Falls 游戏屏幕截图")

本指南介绍了 Fruity 降到游戏中，介绍了常见 CocosSharp 和游戏开发概念，如物理特性、 内容管理、 游戏状态和游戏设计。  

## <a name="coin-time-gamegraphics-gamescocossharpcointimemd"></a>[硬币时间游戏](~/graphics-games/cocossharp/cointime.md)

![创造时间游戏屏幕截图](images/cointime.png "硬币时间游戏屏幕截图")

硬币时间是完整的平台动作游戏适用于 iOS 和 Android 游戏。 游戏的目标是收集所有级别中硬币，然后同时避免敌人和障碍物到达出口门。

## <a name="drawing-geometry-with-ccdrawnodegraphics-gamescocossharpccdrawnodemd"></a>[绘制使用 CCDrawNode 的几何图形](~/graphics-games/cocossharp/ccdrawnode.md)

![使用 CCDrawNode 绘制的形状](images/ccdrawnode.png "使用 CCDrawNode 绘制的形状")

CCDrawNode 提供如线条、 圆形和三角形的图形基元对象的方法。

## <a name="animating-with-ccactiongraphics-gamescocossharpccactionmd"></a>[使用 CCAction 进行动画处理](~/graphics-games/cocossharp/ccaction.md)

![一个 ccaction 进行动画处理的动画](images/ccaction.png "ccaction 进行动画处理动画")

`CCAction` 是可用于 CocosSharp 对象进行动画处理的基类。 本指南介绍了内置`CCAction`实现常见任务，例如定位、 缩放和旋转。 它还会查看如何通过继承创建自定义实现`CCAction`。

## <a name="using-tiled-with-cocossharpgraphics-gamescocossharptiledmd"></a>[将平铺与 CocosSharp 结合使用](~/graphics-games/cocossharp/tiled.md)

![在游戏中的级别](images/tiled.png "在游戏中的级别")

平铺是一个功能强大、 灵活，并用于创建正交和等角磁贴的成熟应用程序将游戏的映射。 CocosSharp 平铺的本机文件格式的内置集成。

## <a name="entities-in-cocossharpgraphics-gamescocossharpentitiesmd"></a>[CocosSharp 中的实体](~/graphics-games/cocossharp/entities.md)

![从游戏中的宇宙飞船](images/entities.png "禁止玩游戏中的宇宙飞船")

实体模式是组织的游戏代码的强大方法。 它提高了可读性，使代码更易于维护，并使用内置的父/子功能。

## <a name="handling-multiple-resolutions-in-cocossharpgraphics-gamescocossharpresolutionsmd"></a>[处理 CocosSharp 中的多个解决方法](~/graphics-games/cocossharp/resolutions.md)

![表示屏幕分辨率的网格](images/resolutions.png "表示屏幕分辨率的网格")

本指南演示如何使用 CocosSharp 开发游戏的不同分辨率的设备正确显示。

## <a name="cocossharp-content-pipelinegraphics-gamescocossharpcontent-pipelineindexmd"></a>[CocosSharp 内容管道](~/graphics-games/cocossharp/content-pipeline/index.md)

![XNB](images/content-pipeline.png "XNB")

内容管道通常用于在游戏开发优化内容并设置其格式，以便它可以加载特定硬件上或通过某些游戏开发框架。

## <a name="improving-frame-rate-with-ccspritesheetgraphics-gamescocossharpccspritesheetmd"></a>[使用 ccspritesheet 提高帧速率的提高帧速率](~/graphics-games/cocossharp/ccspritesheet.md)

![树从 ccspritesheet 提高帧速率](images/ccspritesheet.png "树从 ccspritesheet 提高帧速率")

`CCSpriteSheet` 提供组合和使用一个纹理中的多个图像文件的功能。 减少纹理计数可以提高游戏的加载时间和帧速率。

## <a name="texture-caching-using-cctexturecachegraphics-gamescocossharptexture-cachemd"></a>[纹理缓存使用 CCTextureCache](~/graphics-games/cocossharp/texture-cache.md)

![CocosSharp 缓存图像的方式的表示形式](images/texture-cache.png "CocosSharp 缓存图像的方式的表示形式")

CocosSharp 的`CCTextureCache`类提供了一种标准方式组织、 缓存和卸载内容。 

## <a name="2d-math-with-cocossharpgraphics-gamescocossharpmathmd"></a>[使用 CocosSharp 的 2D 数学](~/graphics-games/cocossharp/math.md)

![旋转图像](images/math.png "旋转图像")

本指南介绍了开发游戏的 2D 数学。 它使用 CocosSharp 来演示如何执行常见的游戏开发任务，并解释了这些任务背后的数学函数。

## <a name="performance-and-visual-effects-with-ccrendertexturegraphics-gamescocossharpccrendertexturemd"></a>[性能和使用 CCRenderTexture 视觉效果](~/graphics-games/cocossharp/ccrendertexture.md)

![从游戏 sprite](images/ccrendertexture.png "sprite 禁止玩游戏")

`CCRenderTexture`类提供用于呈现为单个的纹理的多个 CocosSharp 对象的功能。 创建之后，`CCRenderTexture`来高效地呈现图形以及实现视觉效果，可以使用实例。
