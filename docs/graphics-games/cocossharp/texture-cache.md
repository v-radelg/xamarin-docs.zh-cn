---
title: 纹理缓存使用 CCTextureCache
description: CocosSharp 的 CCTextureCache 类提供了一种标准方式组织，缓存中，并卸载内容。 它是不完全符合 RAM，从而简化了分组和释放的纹理的过程的大型游戏尤其有用。
ms.prod: xamarin
ms.assetid: 1B5F3F85-9E68-42A7-B516-E90E54BA7102
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 232363d6ce1cb93499716b2c1247c48403cf6cea
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61302320"
---
# <a name="texture-caching-using-cctexturecache"></a>纹理缓存使用 CCTextureCache

_CocosSharp 的 CCTextureCache 类提供了一种标准方式组织，缓存中，并卸载内容。它是用于大型游戏以至于可能不适合放 RAM，从而简化了分组和释放的纹理的过程完全尤其有用。_

`CCTextureCache`类是必不可少的组成部分 CocosSharp 游戏开发。 大多数 CocosSharp 游戏使用`CCTextureCache`对象，即使在内部使用多个 CocosSharp 方法未显式*共享*纹理缓存。

本指南介绍了`CCTextureCache`和对游戏开发很重要的原因。 具体而言，它介绍：

 - 为什么纹理缓存的相关问题
 - 纹理生命周期
 - 使用 SharedTextureCache
 - 延迟加载与使用 AddImage 预加载
 - 释放纹理


## <a name="why-texture-caching-matters"></a>为什么纹理缓存的相关问题

纹理缓存是在游戏开发中的一个重要考虑因素，如纹理加载是一个耗时的操作和纹理需要在运行时更大量的 RAM。

与文件的任何操作，从磁盘加载纹理可以是高昂的操作。 如果正在加载文件所需的处理，如正在解压缩 （按原样 png 和 jpg 图像的大小写），纹理加载可能需要额外的时间。 纹理缓存可以减少应用程序必须从磁盘加载文件的次数。

如上所述，纹理还占用大量的运行时内存。 例如背景图像的大小调整为 iPhone 6 (1344 x 750) 的解决方法会占用 4 兆字节的 RAM – 即使 PNG 文件是仅几个千字节为单位的大小。 纹理缓存提供了一种共享内应用的纹理引用方法以及不同的游戏状态之间转换时卸载所有内容的简单方法。


## <a name="texture-lifespan"></a>纹理生命周期

CocosSharp 纹理可能保留在内存中为应用程序的执行的整个长度，或者它们可能是短生存期。 若要最小化内存使用情况应用应释放纹理时不再需要。 当然，这意味着可能会释放和重新加载在更高版本时，可以增加加载时间或合并期间加载的性能可能会降低纹理。 

纹理加载通常需要权衡内存使用情况和负载时间 / 运行时性能。 使用少量的纹理内存的游戏可以保留在内存中根据需要所有纹理，但更大的游戏可能需要卸载纹理以释放空间。

下图显示了一个简单的游戏，该程序根据需要加载纹理并使其保持在内存中执行的整个长度为：

![](texture-cache-images/image1.png "下图显示了一个简单的游戏，该程序根据需要加载纹理并使其保持在内存中执行的整个长度")

前两个条表示游戏的执行时立即需要用到它的纹理。 以下三个条形表示每个级别，根据需要加载的纹理。

如果该游戏是较大，则最终会加载来填充由设备和 OS 提供的所有 RAM 足够纹理。 若要解决此问题，一个游戏可能不再需要时卸载纹理数据。 例如下, 图显示了将 Level1Texture 时它不再需要然后加载下一级别 Level2Texture 卸载该游戏。 最终结果是在任何给定时间只有三个纹理都保存在内存中： 

![](texture-cache-images/image2.png "最终结果是在任何给定时间，内存中保存只有三个纹理")


上面所示的图表指示可以通过卸载，减小纹理内存使用情况，但如果播放机决定重播一个级别，这可能需要进行额外的加载时间。 它也是值得一提，加载和永远不会卸载 UITexture 和 MainCharacter 纹理。 这意味着这些纹理中所有级别，需要因此它们始终保留在内存中。 


## <a name="using-sharedtexturecache"></a>使用 SharedTextureCache

CocosSharp 时加载它们通过自动缓存纹理`CCSprite`构造函数。 例如下面的代码仅创建一个纹理实例：


```csharp
for (int i = 0; i < 100; i++)
{
    CCSprite starSprite = new CCSprite ("star.png");
    starSprite.PositionX = i * 32;
    this.AddChild (starSprite);
} 
```

会自动缓存 CocosSharp`star.png`纹理以避免创建大量的昂贵的替代方案相同`CCTexture2D`实例。 这通过实现`AddImage`调用上，在不共享`CCTextureCache`具体实例`CCTextureCache.SharedTextureCache.Shared`。 若要了解如何`SharedTextureCache`使用我们可以看看以下代码用于在功能上等同于调用`CCSprite`与字符串参数的构造函数：


```

CCSprite starSprite = new CCSprite ();
 starSprite.Texture = CCTextureCache.SharedTextureCache.AddImage ("star.png");
```

`AddImage` 检查参数文件 (在这种情况下`star.png`) 已加载。 如果是这样，则返回的缓存的实例。 如果从文件系统中，然后不加载它并对纹理的引用的内部存储后续`AddImage`调用。 换而言之`star.png`映像仅加载一次，并且在后续调用需要没有额外的磁盘访问或其他纹理内存。


## <a name="lazy-loading-vs-pre-loading-with-addimage"></a>延迟加载与使用 AddImage 预加载

`AddImage` 允许写入相同的代码是否请求的纹理是否已加载。 这意味着内容将不会加载，直到需要;但是，这可能导致在运行时由于加载的内容不可预测的性能问题。

例如，请考虑的游戏玩家的武器其中可以升级。 升级时，起武器并炮弹将以可视方式改变，从而导致正在使用的新纹理。 如果内容是延迟加载则与升级后的武器关联的纹理将不会加载一开始，而在以后当播放机将获取升级。 

这种中旬游戏玩法加载可能会导致到游戏*pop*，这是在执行过程中发生短暂而明显的冻结。 若要防止此情况，代码可以预测可能需要的纹理，提前和预加载它们。 例如，以下可能用于预加载纹理：


```csharp
void PreLoadImages()
{
    var cache = CCTextureCache.SharedTextureCache;

    cache.AddImage ("powerup1.png");
    cache.AddImage ("powerup2.png");
    cache.AddImage ("powerup3.png");

    cache.AddImage ("enemy1.png");
    cache.AddImage ("enemy2.png");
    cache.AddImage ("enemy3.png");

    // pre-load any additional content here to 
    // prevent pops at runtime
} 
```

此预加载可能会导致内存浪费，并且会增加启动时间。 例如，播放机可能永远不会实际获取道具由`powerup3.png`纹理，因此将不必要地加载。 当然，这可能是必要的成本，需付费即可避免潜在 pop 中游戏玩法，因此它将容纳在 RAM 中时，通常最好的办法预加载内容。


## <a name="disposing-textures"></a>释放纹理

如果某个游戏不需要为最小规格的设备上可用，则纹理无需释放更多的纹理内存。 另一方面，较大的游戏可能需要释放纹理的内存来为新内容留出空间。 例如一个游戏可能会使用大量的内存存储环境的纹理。 如果仅在特定级别中使用环境则它应卸载级别结束时。


### <a name="disposing-a-single-texture"></a>释放单纹理

删除单纹理，首先需要调用`Dispose`方法，然后手动删除从`CCTextureCache`。

下面演示了如何完全删除背景 sprite 以及其纹理：


```csharp
void DisposeBackground()
{
    // Assuming this is called from a CCLayer:
    this.RemoveChild (backgroundSprite);

    CCTextureCache.SharedTextureCache.RemoveTexture (backgroundsprite.Texture);

    backgroundSprite.Texture.Dispose ();
} 
```

处理较少的纹理，但这可能会变得容易出错时面对的较大的纹理集时，直接释放纹理非常有效。

纹理可以分组到自定义 （非共享）`CCTextureCache`实例，以简化纹理清理。

例如，考虑一个示例在已预加载内容使用级别特定`CCTextureCache`实例。 `CCTextureCache`可能会在类中定义级别定义实例 (这可能会`CCLayer`或`CCScene`):


```csharp
CCTextureCache levelTextures; 
```

`levelTextures`实例然后可用于预加载特定于级别的纹理： 


```

void PreloadLevelTextures(CCApplication application)
{
    levelTextures = new CCTextureCache (application);

    levelTextures.AddImage ("Background.png");
    levelTextures.AddImage ("Foreground.png");
    levelTextures.AddImage ("Enemy1.png");
    levelTextures.AddImage ("Enemy2.png");
    levelTextures.AddImage ("Enemy3.png");

    levelTextures.AddImage ("Powerups.png");
    levelTextures.AddImage ("Particles.png");
} 
```

最后级别结束时，纹理可以立即通过所有释放`CCTextureCache`:


```csharp
void EndLevel()
{
    levelTextures.Dispose ();
    // Perform any other end-level cleanup
} 
```

Dispose 方法将释放所有内部纹理，清除这些纹理使用的内存。 组合`CCTextureCache.Shared`具有级别或游戏的模式的特定`CCTextureCache`实例会导致整个游戏中，而另一些正在卸载模块级别最终，类似于本指南的开头显示的关系图通过保留某些纹理： 

![](texture-cache-images/image2.png "CCTextureCache.Shared 级别或游戏模式特定 CCTextureCache 实例将结果组合在整个游戏中，而另一些正在卸载模块级别最终，类似于本指南的开头显示的关系图通过保留某些纹理")




## <a name="summary"></a>总结

本指南演示如何使用`CCTextureCache`平衡内存使用情况和运行时性能的类。 `CCTexturCache.SharedTextureCache` 可以显式或隐式用于加载并缓存的应用程序生命周期的纹理，而`CCTextureCache`实例可用于卸载纹理以减少内存使用量。

## <a name="related-links"></a>相关链接

- [https://github.com/mono/CocosSharp](https://github.com/mono/CocosSharp)
- [/api/type/CocosSharp.CCTextureCache/](https://developer.xamarin.com/api/type/CocosSharp.CCTextureCache/)
