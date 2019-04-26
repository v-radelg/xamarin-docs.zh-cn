---
title: 硬币时间游戏的详细信息
description: 本指南介绍在硬币时间游戏中，包括使用磁贴映射、 创建实体、 对 sprite，进行动画处理和实现高效的冲突的实现详细信息。
ms.prod: xamarin
ms.assetid: 5D285684-0417-4E16-BD14-2D1F6DEFBB8B
author: conceptdev
ms.author: crdun
ms.date: 03/24/2017
ms.openlocfilehash: af914e10eb93aa0668743a113ffe647a939fea75
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61395733"
---
# <a name="coin-time-game-details"></a>硬币时间游戏的详细信息

_本指南介绍在硬币时间游戏中，包括使用磁贴映射、 创建实体、 对 sprite，进行动画处理和实现高效的冲突的实现详细信息。_

硬币时间是完整的平台动作游戏适用于 iOS 和 Android 游戏。 游戏的目标是收集所有级别中硬币，然后同时避免敌人和障碍物到达出口门。

![](cointime-images/image1.png "游戏的目标是收集所有级别中硬币并随后将同时避免敌人和障碍物到达出口门")

本指南介绍硬币时间，涵盖以下主题中的实现详细信息：

- [使用 tmx 文件](#working-with-tmx-files)
- [级别加载](#level-loading)
- [添加新实体](#adding-new-entities)
- [经过动画处理的实体](#animated-entities)


## <a name="content-in-coin-time"></a>硬币时间中的内容

硬币时间是表示完整的 CocosSharp 项目可能组织方式的示例项目。 Coin Time 的结构，旨在简化的添加和维护的内容。 它使用 **.tmx**创建的文件[平铺](http://www.mapeditor.org)级别和 XML 文件定义动画。 修改或添加新内容，可以通过最少的工作量。 

虽然这种方法使硬币时间用于学习和试验的有效项目，它也反映了如何专业游戏进行。 本指南介绍一些简化添加和修改内容所用的方法。


## <a name="working-with-tmx-files"></a>使用 tmx 文件

使用.tmx 文件格式的输出： 定义硬币时间级别[平铺](http://www.mapeditor.org)磁贴映射编辑器。 有关使用平铺的详细讨论，请参阅[科科斯 Sharp 指南使用平铺](~/graphics-games/cocossharp/tiled.md)。 

每个级别中包含其自身.tmx 文件中定义**CoinTime/资产/内容/级别**文件夹。 所有硬币时间级别都共享中定义的一个 tileset 文件**mastersheet.tsx**文件。 此文件定义每个磁贴，如磁贴是否具有 solid 冲突或是否在磁贴应替换为实体实例的自定义属性。 Mastersheet.tsx 文件允许仅定义一次并在所有级别上使用的属性。 


### <a name="editing-a-tile-map"></a>编辑磁贴映射

若要编辑的磁贴映射，请通过双击.tmx 文件或打开通过平铺中的文件菜单中平铺打开.tmx 文件。 Coin 的 Time 级别磁贴图包含三个层： 

- **实体**– 这一层包含磁贴将替换为在运行时的实体的实例。 示例包括播放机、 硬币、 敌人和最终级别门。
- **地形**– 这一层包含磁贴通常具有坚实的冲突。 Solid 冲突使得玩家可以引导这些磁贴上，而无需故障。 使用 solid 冲突的磁贴还可以充当墙壁和上限。
- **背景**– 背景图层包含用作静态背景的磁贴。 当照相机移动整个创建通过视差深度的外观的级别时，此层不滚动。

我们将探讨更高版本，如级别加载代码中，要求在所有硬币时间级别这三层。

#### <a name="editing-terrain"></a>编辑地形

可以通过单击在放置磁贴**mastersheet** tileset，然后单击该磁贴上映射。 例如，若要绘制一个级别中的新地形：

1. 选择地形层
1. 单击磁贴以绘制
1. 单击或推送，然后拖动该地图以绘制该磁贴

    ![](cointime-images/image2.png "单击磁贴以绘制 1")

左上方的 tileset 包含所有硬币时间地形。 地形，为纯色，包括**SolidCollision**属性，如在屏幕左侧的磁贴属性中所示：

![](cointime-images/image3.png "地形，为纯色，包括 SolidCollision 属性中，显示在屏幕左侧的磁贴属性中的相同")

#### <a name="editing-entities"></a>编辑实体

可以添加或从中级别 – 就像地形实体。 **Mastersheet** tileset 具有放置有关一半水平，所有实体，因此它们可能不会显示无需向右滚动：

![](cointime-images/image4.png "Mastersheet tileset 具有放置有关一半水平，所有实体，因此它们可能不会显示无需向右滚动")

新的实体应置于**实体**层。

![](cointime-images/image5.png "新的实体应放置在实体层")

CoinTime 代码寻找**EntityType**时加载级别来标识的实体应替换为磁贴： 

![](cointime-images/image6.png "加载级别来标识的实体应替换为磁贴时，EntityType CoinTime 代码查找")

一旦具有已修改并保存该文件，所做的更改会自动显示在项目生成和运行：

![](cointime-images/image7.png "一旦具有已修改并保存该文件，所做的更改会自动显示在项目生成和运行")


### <a name="adding-new-levels"></a>添加新的级别

将级别添加到硬币时间的过程需要任何代码更改，并只更改了少量小到项目。 若要添加一个新的级别：

1. 打开文件夹位于 <**CoinTime 根 > \CoinTime\Assets\Content\levels**
1. 复制并粘贴的一个级别，如**level0.tmx**
1. 重命名新.tmx 文件，因此它将继续使用现有的级别，级别将数序列如**level8.tmx**
1. 在 Visual Studio 或 Visual Studio for Mac 中，将新的.tmx 文件添加到 Android 级别文件夹。 验证该文件使用**AndroidAsset**生成操作。

    ![](cointime-images/image8.png "验证该文件使用 AndroidAsset 生成操作")

1. 将新的.tmx 文件添加到 iOS 级别文件夹。 请务必将该文件从其原始位置链接并验证它使用**BundleResource**生成操作。

    ![](cointime-images/image9.png "请务必将该文件从其原始位置链接并验证它使用 BundleResource 生成操作")

新级别应在级别选择屏幕中显示为级别 9 (级别文件的名称保持为 0，启动，但级别按钮以数字 1 开头):

![](cointime-images/image10.png "新级别应在级别选择屏幕中显示为级别 9 级别文件开始保持为 0，但级别按钮以数字 1 开头")


## <a name="level-loading"></a>级别加载

如前面所示，更高级别的无需在代码中的任何更改 – 游戏会自动检测级别，如果它们都正确命名并添加到**级别**文件夹具有正确的生成操作 (**BundleResource**或**AndroidAsset**)。

用于确定的级别数逻辑包含在`LevelManager`类。 实例时`LevelManager`（使用单一实例模式），构造`DetermineAvailbleLevels`调用方法：


```csharp
private void DetermineAvailableLevels()
{
    // This game relies on levels being named "levelx.tmx" where x is an integer beginning with
    // 1. We have to rely on MonoGame's TitleContainer which doesn't give us a GetFiles method - we simply
    // have to check if a file exists, and if we get an exception on the call then we know the file doesn't
    // exist. 
    NumberOfLevels = 0;
    while (true)
    {
        bool fileExists = false;
        try
        {
            using(var stream = TitleContainer.OpenStream("Content/levels/level" + NumberOfLevels + ".tmx"))
            {
            }
            // if we got here then the file exists!
            fileExists = true;
        }
        catch
        {
            // do nothing, fileExists will remain false
        }
        if (!fileExists)
        {
            break;
        }
        else
        {
            NumberOfLevels++;
        }
    }
}
```

CocosSharp 不提供跨平台方法来检测文件是否存在，因此我们必须依赖`TitleContainer`类尝试打开的流。 如果打开一个流的代码引发异常，则该文件 does 不存在，while 循环中断。 在循环完成后，`NumberOfLevels`属性报告多少个有效级别是项目的一部分。

`LevelSelectScene`类使用`LevelManager.NumberOfLevels`来确定在中创建的按钮个数`CreateLevelButtons`方法：


```csharp
private void CreateLevelButtons()
{
    const int buttonsPerPage = 6;
    int levelIndex0Based = buttonsPerPage * pageNumber;
    int maxLevelExclusive = System.Math.Min (levelIndex0Based + 6, LevelManager.Self.NumberOfLevels);
    int buttonIndex = 0;
    float centerX = this.ContentSize.Center.X;
    const float topRowOffsetFromCenter = 16;
    float topRowY = this.ContentSize.Center.Y + topRowOffsetFromCenter;
    for (int i = levelIndex0Based; i < maxLevelExclusive; i++)
    {
        ...
    }
}
```

`NumberOflevels`属性用于确定应创建的按钮。 此代码会考虑用户当前正在查看并仅创建最多为每个页面的六个按钮的页。 按钮单击时，实例调用`HandleButtonClicked`方法：


```csharp
private void HandleButtonClicked(object sender, EventArgs args)
{
    // levelNumber is 1-based, so subtract 1:
    var levelIndex = (sender as Button).LevelNumber - 1;
    LevelManager.Self.CurrentLevel = levelIndex;
    CoinTime.GameAppDelegate.GoToGameScene ();
}
```

此方法将分配`CurrentLevel`属性由`GameScene`时加载一个级别。 设置后`CurrentLevel`，则`GoToGameScene`方法会引发，切换从场景`LevelSelectScene`到`GameScene`。

`GameScene`构造函数调用`GoToLevel`，该文件将执行级别加载逻辑：


```csharp
private void GoToLevel(int levelNumber)
{
    LoadLevel (levelNumber);
    CreateCollision();
    ProcessTileProperties ();
    touchScreen = new TouchScreenInput(gameplayLayer);
    secondsLeft = secondsPerLevel;
}
```

接下来我们来看看中调用方法`GoToLevel`。


### <a name="loadlevel"></a>LoadLevel

`LoadLevel`方法负责加载.tmx 文件并将其添加到`GameScene`。 此方法不会创建任何交互式对象，例如冲突或实体 – 它只不过是创建的级别，也称为视觉对象*环境*。


```csharp
private void LoadLevel(int levelNumber)
{
    currentLevel = new CCTileMap ("level" + levelNumber + ".tmx");
    currentLevel.Antialiased = false;
    backgroundLayer = currentLevel.LayerNamed ("Background");
    // CCTileMap is a CCLayer, so we'll just add it under all entities
    this.AddChild (currentLevel);
    // put the game layer after
    this.RemoveChild(gameplayLayer);
    this.AddChild(gameplayLayer);
    this.RemoveChild (hudLayer);
    this.AddChild (hudLayer);
}
```

`CCTileMap`构造函数采用一个文件名，使用级别编号传递给创建`LoadLevel`。 有关详细信息创建和处理`CCTileMap`实例，请参阅[使用平铺与 CocosSharp 指南](~/graphics-games/cocossharp/tiled.md)。

目前，CocosSharp 不允许重新排序的层，而无需删除并重新将它们添加到其父`CCScene`(即`GameScene`这种情况下)，因此该方法的最后几行所需重新排序图层。


### <a name="createcollision"></a>CreateCollision

`CreateCollision`方法构造`LevelCollision`实例，它用于执行*solid 冲突*播放机和环境之间。


```csharp
private void CreateCollision()
{
    levelCollision = new LevelCollision();
    levelCollision.PopulateFrom(currentLevel);
}
```

而无需此冲突时，播放机将贯穿级别和游戏将是无法播放。 Solid 冲突让播放机引导到地上，防止播放机通过墙或通过的上限，会跳转。

可以使用任何其他代码 – 对平铺的文件的唯一修改添加硬币时间中的冲突。 


### <a name="processtileproperties"></a>ProcessTileProperties

一旦加载一个级别并创建此冲突，`ProcessTileProperties`调用来执行基于磁贴属性的逻辑。 硬币时间包括`PropertyLocation`用于定义属性和这些属性的磁贴的坐标的结构：


```csharp
public struct PropertyLocation
{
    public CCTileMapLayer Layer;
    public CCTileMapCoordinates TileCoordinates;
    public float WorldX;
    public float WorldY;
    public Dictionary<string, string> Properties;
}
```

此结构用于构造创建实体实例，并删除不必要的磁贴中`ProcessTileProperties`方法：


```csharp
private void ProcessTileProperties()
{
    TileMapPropertyFinder finder = new TileMapPropertyFinder (currentLevel);
    foreach (var propertyLocation in finder.GetPropertyLocations())
    {
        var properties = propertyLocation.Properties;
        if (properties.ContainsKey ("EntityType"))
        {
            float worldX = propertyLocation.WorldX;
            float worldY = propertyLocation.WorldY;
            if (properties.ContainsKey ("YOffset"))
            {
                string yOffsetAsString = properties ["YOffset"];
                float yOffset = 0;
                float.TryParse (yOffsetAsString, out yOffset);
                worldY += yOffset;
            }
            bool created = TryCreateEntity (properties ["EntityType"], worldX, worldY);
            if (created)
            {
                propertyLocation.Layer.RemoveTile (propertyLocation.TileCoordinates);
            }
        }
        else if (properties.ContainsKey ("RemoveMe"))
        {
            propertyLocation.Layer.RemoveTile (propertyLocation.TileCoordinates);
        }
    }
}
```

Foreach 循环的每个磁贴属性，检查键是否是计算结果`EntityType`或`RemoveMe`。 `EntityType` 指示应创建的实体实例。 `RemoveMe` 指示该磁贴应在运行时完全删除。

如果具有的属性`EntityType`找到键，然后`TryCreateEntity`调用时，若要创建使用属性匹配的实体都尝试`EntityType`密钥：


```csharp
private bool TryCreateEntity(string entityType, float worldX, float worldY)
{
    CCNode entityAsNode = null;
    switch (entityType)
    {
    case "Player":
        player = new Player ();
        entityAsNode = player;
        break;
    case "Coin":
        Coin coin = new Coin ();
        entityAsNode = coin;
        coins.Add (coin);
        break;
    case "Door":
        door = new Door ();
        entityAsNode = door;
        break;
    case "Spikes":
        var spikes = new Spikes ();
        this.damageDealers.Add (spikes);
        entityAsNode = spikes;
        break;
    case "Enemy":
        var enemy = new Enemy ();
        this.damageDealers.Add (enemy);
        this.enemies.Add (enemy);
        entityAsNode = enemy;
        break;
    }
    if(entityAsNode != null)
    {
        entityAsNode.PositionX = worldX;
        entityAsNode.PositionY = worldY;
        gameplayLayer.AddChild (entityAsNode);
    }
    return entityAsNode != null;
}
```


## <a name="adding-new-entities"></a>添加新实体

硬币时间对其游戏对象使用的实体模式 (此内容中进行介绍[CocosSharp 中的实体指导](~/graphics-games/cocossharp/entities.md))。 所有实体都继承自`CCNode`，这意味着可以将它们添加为子级的`gameplayLayer`。

直接通过列表或单个实例也引用每个实体类型。 例如，`Player`引用`player`字段中，以及所有`Coin`实例中引用`coins`列表。 保持对实体的直接引用 (而不是引用它们通过`gameLayer.Children`列表) 使访问更轻松地读取这些实体并消除了开销可能很大的类型强制转换的代码。

现有代码提供了如何创建新的实体的示例为多个实体类型。 若要创建新实体，可以使用下列步骤：


### <a name="1---define-a-new-class-using-the-entity-pattern"></a>1-定义一个新类，使用实体模式

创建实体的唯一要求是创建一个类，该类继承自`CCNode`。 大多数实体都有一些视觉对象，如`CCSprite`，其应为其构造函数中的实体的子级添加。

CoinTime 提供`AnimatedSpriteEntity`这简化了创建动画，实体类。 将更详细地介绍动画[经过动画处理的实体部分](#animated-entities)。


### <a name="2--add-a-new-entry-to-the-trycreateentity-switch-statement"></a>2 – 新条目添加到 TryCreateEntity switch 语句

新的实体的实例应在实例化`TryCreateEntity`。 如果实体要求每个帧逻辑，如冲突、 AI 或读取输入，则`GameScene`需要保持对对象的引用。 如果需要多个实例 (如`Coin`或`Enemy`实例)，然后新`List`应添加到`GameScene`类。


### <a name="3--modify-tile-properties-for-the-new-entity"></a>3 – 修改新的实体的磁贴属性

一旦该代码支持创建新实体，将新实体需要添加到 tileset。 可以通过打开任何级别编辑 tileset`.tmx`文件。 

与在.tmx 分开存储 tileset **mastersheet.tsx**文件，因此它可以对其进行编辑之前必须导入：

![](cointime-images/image11.png "Tileset 存储独立于.tsx 文件中，因此它可以对其进行编辑之前必须导入")

一旦导入，所选磁贴上的属性是可编辑，并且可以添加 EntityType:

![](cointime-images/image12.png "导入，所选磁贴上的属性是可编辑，并可以添加 EntityType 后")

创建属性后，可以设置其值以匹配新`case`在`TryCreateEntity`:

![](cointime-images/image13.png "创建属性后，可以将其值设置为匹配 TryCreateEntity 中的新用例")

Tileset 更改后，必须导出 – 这使所做的更改可用于所有其他级别：

![](cointime-images/image14.png "Tileset 更改后，必须导出")

Tileset 应覆盖现有**mastersheet.tsx** tileset:

![](cointime-images/image15.png "他 tileset 应覆盖现有 mastersheet.tsx tileset")


## <a name="entity-tile-removal"></a>删除实体磁贴

磁贴映射加载到一个游戏，各个磁贴时，静态对象。 由于实体需要自定义的行为，例如移动，硬币时代码中创建实体时删除磁贴。

`ProcessTileProperties` 包含要删除磁贴创建使用实体的逻辑`RemoveTile`方法：


```csharp
private void ProcessTileProperties()
{
    TileMapPropertyFinder finder = new TileMapPropertyFinder (currentLevel);
    foreach (var propertyLocation in finder.GetPropertyLocations())
    {
        var properties = propertyLocation.Properties;
        if (properties.ContainsKey ("EntityType"))
        {
            ...
            bool created = TryCreateEntity (properties ["EntityType"], worldX, worldY);
            if (created)
            {
                propertyLocation.Layer.RemoveTile (propertyLocation.TileCoordinates);
            }
        }
        ...
    }
}
```

此自动删除的磁贴足以占用 tileset，如硬币和敌人中只有一个磁贴的实体。 更大的实体需要额外的逻辑和属性。

门需要两个磁贴完全绘制：

![](cointime-images/image16.png "门需要两个磁贴完全绘制")

门中的底部磁贴包含用于创建实体的属性 (**EntityType**设置为**门**):

![](cointime-images/image17.png "门中的底部磁贴包含用于创建一个实体集到门的 EntityType 的属性")

由于仅门底部磁贴删除在创建门实例时，需要额外的逻辑来删除在运行时顶部磁贴。 顶部磁贴具有**RemoveMe**属性设置为**true**:

![](cointime-images/image18.png "顶部磁贴具有 RemoveMe 属性设置为 true")



此属性用于删除中的磁贴`ProcessTileProperties`:


```csharp
private void ProcessTileProperties()
{
    TileMapPropertyFinder finder = new TileMapPropertyFinder (currentLevel);
    foreach (var propertyLocation in finder.GetPropertyLocations())
    {
        var properties = propertyLocation.Properties;
        ...
        else if (properties.ContainsKey ("RemoveMe"))
        {
            propertyLocation.Layer.RemoveTile (propertyLocation.TileCoordinates);
        }
    }
}
```


## <a name="entity-offsets"></a>实体的偏移量

从磁贴创建的实体位于通过将与该磁贴的中心实体的中心对齐。 更大的实体，如`Door`，使用其他属性和逻辑来正确放置。 

定义的底部门磁贴`Door`实体放置功能，指定**正文**值为 4。 如果没有此属性，`Door`实例将被放在磁贴的中心：

![](cointime-images/image19.png "如果没有此属性，门实例放在磁贴的中心")

 

这通过应用更正**正文**中的值`ProcessTileProperties`:


```csharp
private void ProcessTileProperties()
{
    TileMapPropertyFinder finder = new TileMapPropertyFinder (currentLevel);
    foreach (var propertyLocation in finder.GetPropertyLocations())
    {
        var properties = propertyLocation.Properties;
        if (properties.ContainsKey ("EntityType"))
        {
            float worldX = propertyLocation.WorldX;
            float worldY = propertyLocation.WorldY;
            if (properties.ContainsKey ("YOffset"))
            {
                string yOffsetAsString = properties ["YOffset"];
                float yOffset = 0;
                float.TryParse (yOffsetAsString, out yOffset);
                worldY += yOffset;
            }
            bool created = TryCreateEntity (properties ["EntityType"], worldX, worldY);
            ...
        }
...
    }
}
```


## <a name="animated-entities"></a>经过动画处理的实体

硬币时间包括多个经过动画处理的实体。 `Player`并`Enemy`实体播放遍历动画和`Door`实体收集所有硬币后播放动画。


### <a name="achx-files"></a>.achx 文件

硬币时间动画.achx 文件中定义。 每个动画定义之间`AnimationChain`标记，如下面的动画中定义中所示**propanimations.achx**:


```xml
<AnimationChain>
  <Name>Spikes</Name>
  <ColorKey>0</ColorKey>
  <Frame>
    <FlipHorizontal>false</FlipHorizontal>
    <FlipVertical>false</FlipVertical>
    <TextureName>..\images\mastersheet.png</TextureName>
    <FrameLength>0.1</FrameLength>
    <LeftCoordinate>1152</LeftCoordinate>
    <RightCoordinate>1168</RightCoordinate>
    <TopCoordinate>128</TopCoordinate>
    <BottomCoordinate>144</BottomCoordinate>
    <RelativeX>0</RelativeX>
    <RelativeY>0</RelativeY>
  </Frame>
</AnimationChain> 
```

此动画仅包含单个帧，导致峰值实体显示静态图像。 实体可以使用.achx 文件是否显示单个或多帧动画。 其他帧可以添加到.achx 文件，而无需在代码中的任何更改。 

框架定义中显示的图像`TextureName`参数，以及在显示的坐标`LeftCoordinate`， `RightCoordinate`， `TopCoordinate`，和`BottomCoordinate`标记。 这些表示在图中所用 – 动画的帧的像素坐标**mastersheet.png**这种情况下。

![](cointime-images/image20.png "这些表示映像中的动画的帧的像素坐标")

`FrameLength`属性定义应显示动画中的帧的秒数。 单个帧的动画忽略此值。

.Achx 文件中的所有其他 AnimationChain 属性将忽略硬币时间。


### <a name="animatedspriteentity"></a>AnimatedSpriteEntity

动画逻辑包含在`AnimatedSpriteEntity`类，该类用作基类，有关在中使用的大多数实体`GameScene`。 它提供以下功能：

 - 正在加载的`.achx`文件
 - 已加载动画的动画缓存
 - 用于显示动画 CCSprite 实例
 - 更改当前帧的逻辑

峰值构造函数提供了如何加载和使用动画的一个简单的示例：


```csharp
public Spikes ()
{
    LoadAnimations ("Content/animations/propanimations.achx");
    CurrentAnimation = animations [0];
}
```

**PropAnimations.achx**仅包含一个动画，因此构造函数通过索引来访问此动画。 如果.achx 文件包含多个动画，则可以按名称引用动画，如中所示`Enemy`构造函数：


```csharp
walkLeftAnimation = animations.Find (item => item.Name == "WalkLeft");
walkRightAnimation = animations.Find (item => item.Name == "WalkRight");
```


## <a name="summary"></a>总结

本指南介绍了硬币时间的实现细节。 硬币时间创建为一个完整的游戏，但也可以轻松地修改和扩展的项目。 添加新的级别，以及创建新的实体来进一步了解如何实现硬币时间鼓励花费时间对级别进行的修改将读取器。

## <a name="related-links"></a>相关链接

- [游戏项目 （示例）](https://developer.xamarin.com/samples/mobile/CoinTime/)
