---
title: 使用平铺与 CocosSharp
description: 平铺是一个功能强大、 灵活，并用于创建正交和等角磁贴的成熟应用程序将游戏的映射。 CocosSharp 平铺的本机文件格式的内置集成。
ms.prod: xamarin
ms.assetid: 804C042C-F62A-4E6C-B10F-06528637F0E2
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 8e7ef890af264bb08827d86c635d555184f1ec00
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61235866"
---
# <a name="using-tiled-with-cocossharp"></a>使用平铺与 CocosSharp

_平铺是一个功能强大、 灵活，并用于创建正交和等角磁贴的成熟应用程序将游戏的映射。CocosSharp 平铺的本机文件格式的内置集成。_

*平铺*应用程序是用于创建标准*磁贴映射*在游戏开发中使用。 本指南将引导完成如何利用现有.tmx 文件 （文件创建的平铺） 并使用 CocosSharp 游戏中。 具体而言，本指南将介绍：

 - 磁贴地图的用途
 - 使用.tmx 文件
 - 用于呈现像素画的注意事项
 - 在运行时使用磁贴属性

当完成我们将具有以下演示：

![](tiled-images/image1.png "按照本指南中的步骤中创建的演示应用")


## <a name="the-purpose-of-tile-maps"></a>磁贴地图的用途

磁贴映射已存在于中的几十年来，游戏开发，但仍通常用于在 2D 游戏中其效率和 esthetics。 磁贴映射为能够实现非常高的效率，通过使用磁贴集 – 使用磁贴映射的源映像。 磁贴集是一组图像合并到一个文件。 虽然磁贴集引用在磁贴映射中使用的图像，但包含多个较小的图像文件也称为 sprite 表或子画面映射中的游戏开发。 我们可以可视化将网格添加到我们的演示中，我们将使用磁贴集使用磁贴集：

![](tiled-images/image2.png "将该演示中使用添加到磁贴网格设置使用磁贴集的可视化的视图")

磁贴映射排列磁贴集中的各个磁贴。 我们应注意每个磁贴映射不需要存储其自身的磁贴副本设置 – 而不是，多个磁贴映射可以引用相同磁贴的集。 这意味着除了磁贴集磁贴地图需要很少的内存。 这样将创建大量磁贴映射，即使它们用于创建一个大型游戏玩法的领域，如[滚动平台动作游戏](https://en.wikipedia.org/wiki/Platform_game)环境。 下面显示了使用相同的磁贴集可能排列：

![](tiled-images/image3.png "下图显示了使用相同的磁贴集可能排列")

![](tiled-images/image4.png "下图显示了使用相同的磁贴集可能排列")


## <a name="working-with-tmx-files"></a>使用.tmx 文件

.Tmx 文件格式是平铺应用程序，可以创建一个 XML 文件[平铺网站上免费下载](http://www.mapeditor.org/)。 .Tmx 文件格式存储磁贴映射的信息。 通常游戏将具有每个级别或单独的区域的一个.tmx 文件。

本指南重点介绍如何使用 CocosSharp; 中的现有.tmx 文件但是，其他教程可在联机状态，包括[平铺映射编辑器此简介](http://gamedevelopment.tutsplus.com/tutorials/introduction-to-tiled-map-editor--gamedev-2838)。

你将需要解压缩[内容的 zip 文件](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Tiled.zip?raw=true)在我们的游戏中使用它。 第一件事要注意是磁贴映射使用这两个.tmx 文件 (dungeon.tmx) 以及一个或多个图像文件用于定义该磁贴设置数据 (dungeon_1.png)。 因此，游戏需要包含两个文件将在运行时，.tmx 添加到项目的这两**内容**文件夹 (包含在**资产**Android 项目中的文件夹)。 具体而言，将文件添加到名为的文件夹**tilemaps**内**内容**文件夹：

![](tiled-images/image5.png "将文件添加到内容的文件夹内名为 tilemaps 的文件夹")

**Dungeon.tmx**现在可以在运行时为加载文件`CCTileMap`对象。 接下来，修改`GameLayer`（或等效的根容器对象） 包含`CCTileMap`实例并将其添加为子级：


```csharp
public class GameLayer : CCLayer
{
    CCTileMap tileMap;

    public GameLayer ()
    {
    }

    protected override void AddedToScene ()
    {
        base.AddedToScene ();

        tileMap = new CCTileMap ("tilemaps/dungeon.tmx");
        this.AddChild (tileMap);
    }
} 
```

如果我们运行该游戏，我们将看到的磁贴映射显示在屏幕的左下角：

![](tiled-images/image6.png "如果运行游戏时，磁贴映射显示在屏幕的左下角")


## <a name="considerations-for-rendering-pixel-art"></a>用于呈现像素画的注意事项

像素门艺术，视频游戏开发的上下文中是指 2D 视觉艺术这通常创建的现有和通常是低分辨率。 像素艺术严格，则可以是时间密集型，若要创建，因此像素艺术磁贴集通常包含较低分辨率磁贴，如 16 或 32 个像素宽度和高度。 如果未在运行时缩放，像素画面通常是适用于大多数现代手机和平板电脑太小。

我们可以调整显示的尺寸以我们的游戏 GameAppDelegate.cs 文件中，我们将在其中添加调用`CCScene.SetDefaultDesignResolution`:


```csharp
 public override void ApplicationDidFinishLaunching (CCApplication application, CCWindow mainWindow)
{
    application.PreferMultiSampling = false;
    application.ContentRootDirectory = "Content";
    application.ContentSearchPaths.Add ("animations");
    application.ContentSearchPaths.Add ("fonts");
    application.ContentSearchPaths.Add ("sounds");

    CCSize windowSize = mainWindow.WindowSizeInPixels;

    float desiredWidth = 1024.0f;
    float desiredHeight = 768.0f;
    
    // This will set the world bounds to be (0,0, w, h)
    // CCSceneResolutionPolicy.ShowAll will ensure that the aspect ratio is preserved
    CCScene.SetDefaultDesignResolution (desiredWidth, desiredHeight, CCSceneResolutionPolicy.ShowAll);
    
    // Determine whether to use the high or low def versions of our images
    // Make sure the default texel to content size ratio is set correctly
    // Of course you're free to have a finer set of image resolutions e.g (ld, hd, super-hd)
    if (desiredWidth < windowSize.Width)
    {
        application.ContentSearchPaths.Add ("images/hd");
        CCSprite.DefaultTexelToContentSizeRatio = 2.0f;
    }
    else
    {
        application.ContentSearchPaths.Add ("images/ld");
        CCSprite.DefaultTexelToContentSizeRatio = 1.0f;
    }

    // New code:
    CCScene.SetDefaultDesignResolution (380, 240, CCSceneResolutionPolicy.ShowAll);

    CCScene scene = new CCScene (mainWindow);
    GameLayer gameLayer = new GameLayer ();

    scene.AddChild (gameLayer);

    mainWindow.RunWithScene (scene);
} 
```

有关详细信息`CCSceneResolutionPolicy`，参阅我们的指南[处理 CocosSharp 中的解决方法](~/graphics-games/cocossharp/resolutions.md)。

如果我们现在运行游戏，我们会看到游戏占用整个屏幕的我们的设备：

![](tiled-images/image7.png "游戏占用整个屏幕的设备")

最后，我们需要禁用上我们磁贴的地图的抗锯齿。 `Antialiased`呈现中放大的对象属性适用于模糊效果。 抗锯齿功能也可用于减少图形对象的像素化外观，但也可能会引入其自己的呈现项目。 具体而言，抗锯齿模糊每个磁贴的内容。 但是，不还模糊的每个磁贴边缘，从而突出显示而不是使用相邻的磁贴值混合处理的各个磁贴。 我们还应注意，像素艺术游戏通常保留其像素化的外观，以维护*复古*感觉。

设置`Antialiased`到`false`后构造`tileMap`:


```csharp
protected override void AddedToScene ()
{
    base.AddedToScene ();

    tileMap = new CCTileMap ("tilemaps/dungeon.tmx");

    // new code:
    tileMap.Antialiased = false;

    this.AddChild (tileMap);
} 
```

现在我们磁贴的地图不会模糊：

![](tiled-images/image8.png "现在磁贴映射将不会显示模糊")


## <a name="using-tile-properties-at-runtime"></a>在运行时使用磁贴属性

到目前为止，我们`CCTileMap`.tmx 文件加载和显示，但我们没有任何办法来与之交互。 具体而言，某些磁贴 （如我们极具价值衣柜） 需要具有自定义逻辑。 我们将逐步了解如何检测自定义磁贴属性，以及各种方法对这些属性在运行时一次标识做出反应。

我们编写任何代码之前，我们需要将属性添加到通过平铺我们磁贴的地图。 若要执行此操作，请在平铺程序中打开 dungeon.tmx 文件。 请务必在游戏项目中打开的文件正在使用该文件夹。

一旦打开，请选择设置以查看其属性的磁贴中极具价值衣柜：

![](tiled-images/image9.png "一旦打开，选择极具价值衣柜中设置以查看其属性的磁贴")

如果极具价值衣柜属性并不显示，在极具价值衣柜上右键单击并选择**图块属性**:

![](tiled-images/image10.png "如果未出现极具价值衣柜属性，极具价值衣柜右键单击并选择磁贴属性")

平铺的属性实现使用一个名称和值。 若要添加的属性，请单击 **+** 按钮，输入名称 **IsTreasure** ，单击 **确定** ，然后输入值 **true** : 

![](tiled-images/image11.png "若要添加一个属性，单击按钮，输入名称 IsTreasure，单击确定，然后输入值 true")

别忘了将.tmx 文件保存后修改属性。

最后，我们将添加代码以查找我们新添加的属性。 我们将遍历每个`CCTileMapLayer`（我们的地图有 2 层），然后通过每个行和列来查找任何磁贴具有`IsTreasure`属性：


```csharp
public class GameLayer : CCLayer
{
    CCTileMap tileMap;

    public GameLayer ()
    {
    }

    protected override void AddedToScene ()
    {
        base.AddedToScene ();

        tileMap = new CCTileMap ("tilemaps/dungeon.tmx");

        // new code:
        tileMap.Antialiased = false;

        this.AddChild (tileMap);

        HandleCustomTileProperties (tileMap);
    }

    void HandleCustomTileProperties(CCTileMap tileMap)
    {
        // Width and Height are equal so we can use either
        int tileDimension = (int)tileMap.TileTexelSize.Width;

        // Find out how many rows and columns are in our tile map
        int numberOfColumns = (int)tileMap.MapDimensions.Size.Width;
        int numberOfRows = (int)tileMap.MapDimensions.Size.Height;

        // Tile maps can have multiple layers, so let's loop through all of them:
        foreach (CCTileMapLayer layer in tileMap.TileLayersContainer.Children)
        {
            // Loop through the columns and rows to find all tiles
            for (int column = 0; column < numberOfColumns; column++)
            {
                // We're going to add tileDimension / 2 to get the position
                // of the center of the tile - this will help us in 
                // positioning entities, and will eliminate the possibility
                // of floating point error when calculating the nearest tile:
                int worldX = tileDimension * column + tileDimension / 2;
                for (int row = 0; row < numberOfRows; row++)
                {
                    // See above on why we add tileDimension / 2
                    int worldY = tileDimension * row + tileDimension / 2;

                    HandleCustomTilePropertyAt (worldX, worldY, layer);
                }
            }
        }
    }

    void HandleCustomTilePropertyAt(int worldX, int worldY, CCTileMapLayer layer)
    {
        CCTileMapCoordinates tileAtXy = layer.ClosestTileCoordAtNodePosition (new CCPoint (worldX, worldY));

        CCTileGidAndFlags info = layer.TileGIDAndFlags (tileAtXy.Column, tileAtXy.Row);

        if (info != null)
        {
            Dictionary<string, string> properties = null;

            try
            {
                properties = tileMap.TilePropertiesForGID (info.Gid);
            }
            catch
            {
                // CocosSharp 
            }

            if (properties != null && properties.ContainsKey ("IsTreasure") && properties["IsTreasure"] == "true" )
            {
                layer.RemoveTile (tileAtXy);

                // todo: Create a treasure chest entity
            }
        }
    }
} 
```

代码的大部分都简单易懂，但我们应该讨论极具价值的磁贴的处理。 在这种情况下，我们将删除的标识为极具价值 chests 任何磁贴。 这是因为极具价值 chests 可能需要在运行时影响冲突，并要把宝打开时的内容的播放机的自定义代码。 此外，可能需要极具价值来应对正在打开 （可视化外观发生变化），并可能逻辑的敌人仅出现在屏幕上的所有已大大降低。

换而言之，极具价值衣柜将受益于正在实体，而不是中的简单磁贴`CCTileMap`。 游戏的实体的详细信息，请参阅[CocosSharp 中的实体指导](~/graphics-games/cocossharp/entities.md)。


## <a name="summary"></a>总结

本演练介绍如何加载到 CocosSharp 应用程序创建的平铺的.tmx 文件。 它演示如何修改应用程序解析的较低分辨率像素门艺术，以及如何通过它们来执行自定义逻辑，例如创建实体实例的属性查找磁贴。

## <a name="related-links"></a>相关链接

- [平铺的网站](http://www.mapeditor.org/)
- [内容 zip](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Tiled.zip?raw=true)
