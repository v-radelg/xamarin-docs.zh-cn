---
title: 使用 ccspritesheet 提高帧速率的提高帧速率
description: Ccspritesheet 提高帧速率提供了用于组合和使用一个纹理中的多个图像文件的功能。 减少纹理计数可以提高游戏的加载时间和帧速率。
ms.prod: xamarin
ms.assetid: A1334030-750C-4C60-8B84-1A8A54B0D00E
author: conceptdev
ms.author: crdun
ms.date: 03/24/2017
ms.openlocfilehash: eab6153653a8c8df2068aaaf879d84d35473c541
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61245520"
---
# <a name="improving-frame-rate-with-ccspritesheet"></a>使用 ccspritesheet 提高帧速率的提高帧速率

_Ccspritesheet 提高帧速率提供了用于组合和使用一个纹理中的多个图像文件的功能。减少纹理计数可以提高游戏的加载时间和帧速率。_

许多游戏要求优化的工作要平稳地运行和加载速度快移动硬件上。 `CCSpriteSheet`类可帮助解决遇到的 CocosSharp 游戏的许多常见性能问题。 本指南介绍了常见性能问题和如何解决这些使用`CCSpriteSheet`类。


## <a name="what-is-a-sprite-sheet"></a>什么是子画面表单？

一个*子画面表单*，这可以也被称为*纹理地图集*，是用于将多个映像合并到一个文件的映像。 这可以提高运行时性能，以及内容的加载时间。

例如下, 图是由三个单独的映像创建简单的子画面表单。 各个图像可以是任意大小，并且生成的子画面表单不是需要完全填充：

![](ccspritesheet-images/image1.png "各个图像可以是任意大小，并且生成的子画面表单不需要完全填充")


### <a name="render-states"></a>呈现状态

CocosSharp 的可视化对象 (如`CCSprite`) 的要求创建顶点缓冲区通过传统的图形 API 呈现代码如 MonoGame 或 OpenGL，简化呈现代码 (如中所述[绘制 3D 图形与MonoGame 中的顶点](~/graphics-games/monogame/3d/part2.md)指南)。 它很简单，尽管 CocosSharp 并不能排除的设置成本*呈现状态*，哪些是呈现代码必须切换纹理或呈现相关的其他状态的次数。

CocosSharp 的内部代码呈现每个可视元素按顺序，通过遍历可视化树开始与当前`CCScene`。 例如，考虑以下场景：

![](ccspritesheet-images/image2.png "请考虑此场景")

CocosSharp 会在序列中呈现的四个星号：

![](ccspritesheet-images/image3.png "CocosSharp 会在序列中呈现四星级")

由于每个`CCSprite`使用同一纹理 CocosSharp 可以组合所有四个星形在一起。 此代码需要每一帧的只有一个呈现状态分配 （分配的星型纹理）。 此方案中是非常有效。

当然，很少游戏使用只有一个映像。 以下场景引入了一个全球图形：

![](ccspritesheet-images/image4.png "此场景引入了一个全球图形")

理想情况下 CocosSharp 应绘制一个映像第一次 （例如星形），则使用其他图像 （全球） sprite 的其余部分使用的所有子画面：

![](ccspritesheet-images/image5.png "理想情况下 CocosSharp 应绘制第一次使用一个映像，如星，然后使用其他图像全球 sprite 的其余部分的所有子画面")

更高版本的排序需要两个呈现状态： 一个在第一个地球上的第一个星型，一个。

如果所有`CCSprite`实例都是相同的子级`CCNode`，然后进行 CocosSharp 将优化以减少呈现状态更改的绘制顺序。 另一方面，如果`CCSprite`实例进行组织，这样，CocosSharp 无法优化呈现方式 (例如，如果它们是不同的实体的一部分`CCNode`实例)，则顺序可能不是最佳。 下面显示的最差的可能绘制顺序，导致五种呈现器状态：

![](ccspritesheet-images/image6.png "这将显示最差可能的绘制顺序，导致五种呈现器状态")

渲染状态可能很难优化的绘制顺序必须遵循的可视化树由于`CCNode`实例。 此树通常是结构化能够轻松地使用 （如实体包含其可见子级的），或定义的艺术家组织由于所需的可视化布局。

当然，理想情况下是具有单一呈现状态，尽管具有多个映像。 CocosSharp 游戏可以实现此目的： 将所有图像都合并为单个文件，然后加载的文件 (以及其随附 **.plist**文件) 到`CCSpriteSheet`。 使用`CCSpriteSheet`类变得更重要的游戏，其中包含大量的映像，或者非常复杂的布局。 

### <a name="load-times"></a>加载时间

将多个映像合并到一个文件还可以提高的原因有多的游戏的加载时间：

 - 将多个映像合并到单个文件可以减少通过有效地打包所用的像素总数
 - 正在加载更少的文件意味着开销更少每个文件，如分析.png 标头
 - 加载更少的文件需要更少寻道时间，对于基于磁盘的媒体，如 Dvd 和传统计算机硬盘很重要

## <a name="using-ccspritesheet-in-code"></a>在代码中使用 ccspritesheet 提高帧速率

若要创建`CCSpriteSheet`实例，该代码必须提供的映像和文件用于定义要用于每个帧的图像的区域。 可以为加载图像 **.png**或 **.xnb**文件 (如果使用[内容管道](~/graphics-games/cocossharp/content-pipeline/index.md))。 定义帧的文件是 **.plist**可以手动创建文件或*TexturePacker* （它我们将讨论下面）。

示例应用中，其中[可以在此处下载](https://developer.xamarin.com/samples/mobile/SpriteSheetDemo/)，创建`CCSpriteSheet`从 **.png**并 **.plist**文件中使用以下代码：

```csharp
CCSpriteSheet sheet = new CCSpriteSheet ("sheet.plist", "sheet.png"); 
```

加载后，`CCSpriteSheet`包含`List`的`CCSpriteFrame`实例 – 每个对应于用于创建整个工作表的源映像的一个实例。 情况下**SpriteSheetDemo**项目，`CCSpriteSheet`包含三个映像。 **.Plist**可以检查文件，Visual Studio for Mac 中或在任何文本编辑器，若要查看可用的映像中。 如果我们查看 **.plist**文本编辑器，我们可以看到三个框架 （省略以强调键名称的部分） 中的文件：


```csharp
...
<dict>
    <key>frames</key>
    <dict>
        <key>farBackground.png</key>
        ...
        <key>foreground.png</key>
        ...
<key>forestBackground.png</key>
...
```

我们可以使用`Find`方法，如下所示的名称，查找帧 (省略代码来强调`CCSpriteFrame`使用情况):


```csharp
CCSpriteFrame frame;
...
frame = sheet.Frames.Find(item=>item.TextureFilename == "farBackground.png"); 
CCSprite sprite = new CCSprite (frame); 
...
```

由于`CCSprite`构造函数可以采用`CCSpriteFrame`参数，该代码永远不会具有要调查的详细信息`CCSpriteFrame`，如哪一个纹理，它使用，或在区域中的主子画面图像。


## <a name="creating-a-sprite-sheet-plist"></a>创建 sprite 表.plist

.Plist 文件是一个基于 xml 的文件，可以创建和进行手动编辑。 同样，可以使用图像编辑程序，将多个文件合并到一个更大的文件。 创建和维护 sprite 表可能非常耗时，因为我们将看看该 TexturePacker 程序，它可以导出 CocosSharp 格式的文件。 TexturePacker 提供免费和"Pro"版本，并且适用于 Windows 和 Mac OS。 可以使用免费版本遵循本指南的其余部分。 

可以是 TexturePacker[从 TexturePacker 网站下载](https://www.codeandweb.com/texturepacker)。 打开时，TexturePacker 没有加载的项目。 起始屏幕，可添加子画面，打开最近的项目 （如果已创建其他项目），并选择要使用的子画面的格式。 CocosSharp 使用 Cocos2D 数据格式：

![](ccspritesheet-images/image7.png "CocosSharp 使用 Cocos2D 数据格式")

图像文件 (如 **.png**) 可以通过拖放它们从 Windows 资源管理器在 Windows 或 mac 上的查找器上添加到 TexturePacker 每当添加文件，TexturePacker 会自动更新 sprite 表预览：

![](ccspritesheet-images/image8.png "每当添加文件 TexturePacker 将自动更新 sprite 表预览")

若要将导出子画面表单，请单击**发布子画面表单**按钮，然后选择子画面表单的位置。 TexturePacker 将节省.plist 文件和图像文件。

若要使用生成的文件，将添加到 CocosSharp 项目的.png 和.plist。 有关将文件添加到 CocosSharp 项目的信息，请参阅[BouncingGame 指南](~/graphics-games/cocossharp/bouncing-game.md)。 一旦将文件添加，他们可以将加载到`CCSpriteSheet`如上面的代码前面所示：

```csharp
CCSpriteSheet sheet = new CCSpriteSheet ("sheet.plist", "sheet.png"); 
```

### <a name="considerations-for-maintaining-a-texturepacker-sprite-sheet"></a>维护 TexturePacker 子画面表单注意事项

随着开发游戏，艺术家可能添加、 删除或修改画面。 任何更改都需要更新子画面表单。 以下注意事项可以轻松地 sprite 表维护：

 - 将原始文件 （用于创建 sprite 表的文件） 保存在文件夹中你的项目，并确保它们添加到版本控制。 这些文件将需要重新创建子画面表单，只要进行了更改。
 - 未添加到 Visual Studio for Mac/Visual Studio 的原始文件或如果添加，设置**生成操作**到**None**。 如果文件已添加并且特定于平台的**生成操作**，则它们将会不必要地增加生成的应用的文件大小。
 - 请考虑使用*智能文件夹*TexturePacker 中。 智能文件夹会自动将任何包含的图像添加到子画面表单。 此功能可以与大量的映像保存了很多开发游戏的时间。 

    ![](ccspritesheet-images/image9.png "此功能可以与大量的映像节省大量时间开发游戏")
 - 请关注 sprite 表纹理大小。 某些较旧的电话硬件不支持大于指达到 2048x2048 纹理大小。 此外，指达到 2048x2048 的 32 位映像使用 RAM – 大量的内存几乎 17 兆的字节。
 - TexturePacker 不包括文件夹子画面名称中默认情况下，因此可能会出现名称冲突。 最好是决定是否要包括文件夹名称中或不是在开发的开头。 更大的游戏应考虑使用文件夹名称以防止冲突。 若要包括文件夹路径，请单击**显示高级**中**数据**部分，并检查**预置文件夹名称**。 

    ![](ccspritesheet-images/image10.png "若要包括文件夹路径，单击 Data 节中显示高级并检查预置文件夹名称")

## <a name="summary"></a>总结

本指南介绍了如何创建和使用`CCSpriteSheet`类。 它还介绍了如何构造文件可以加载到`CCSpriteSheet`实例使用 TexturePacker 程序。

## <a name="related-links"></a>相关链接

- [CCSpriteSheet](https://developer.xamarin.com/api/type/CocosSharp.CCSpriteSheet/)
- [完整的演示 （示例）](https://developer.xamarin.com/samples/mobile/SpriteSheetDemo/)
