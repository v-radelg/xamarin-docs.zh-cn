---
title: Fruity Falls 游戏详细信息
description: 本指南回顾 Fruity 降到游戏中，介绍了常见 CocosSharp 和游戏开发概念，如物理特性、 内容管理、 游戏状态和游戏设计。
ms.prod: xamarin
ms.assetid: A5664930-F9F0-4A08-965D-26EF266FED24
author: conceptdev
ms.author: crdun
ms.date: 03/27/2017
ms.openlocfilehash: 959f5eb149ad375d686b17a85eb3d3b8fbdf3659
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61159979"
---
# <a name="fruity-falls-game-details"></a>Fruity Falls 游戏详细信息

_本指南回顾 Fruity 降到游戏中，介绍了常见 CocosSharp 和游戏开发概念，如物理特性、 内容管理、 游戏状态和游戏设计。_

Fruity Falls 是该播放机入彩色的存储桶，若要获得积分排序红色和黄色水果一个简单的、 基于物理的游戏。 游戏的目标是尽可能获得多个点，而不让水果拖放到错误中，结束游戏。

![](fruity-falls-images/image1.png "游戏的目标是尽可能获得多个点，而不让水果拖放到错误中，结束游戏")

Fruity Falls 扩展中引入的概念[BouncingGame 指南](~/graphics-games/cocossharp/bouncing-game.md)通过将以下内容添加：

 - 内容格式为 Png
 - 高级的物理
 - 游戏状态 （场景之间的转换）
 - 能够优化游戏系数通过单个类中包含的变量
 - 内置 visual 调试支持
 - 使用游戏的实体的代码组织
 - 游戏设计侧重于有趣和重播值

虽然[BouncingGame 指南](~/graphics-games/cocossharp/bouncing-game.md)重点介绍了核心 CocosSharp 概念，Fruity 降到演示如何将其组合在一起引入的成品的游戏。 因为本指南引用 BouncingGame，读者应首先熟悉[BouncingGame 指南](~/graphics-games/cocossharp/bouncing-game.md)之前阅读此指南。

本指南介绍了实现和 Fruity 介于的设计，以提供见解，从而帮助你做出你自己的游戏。 它涵盖以下主题：


- [GameController 类](#gamecontroller-class)
- [游戏的实体](#game-entities)
- [水果图形](#fruit-graphics)
- [物理引擎](#physics)
- [游戏内容](#game-content)
- [GameCoefficients](#gamecoefficients)


## <a name="gamecontroller-class"></a>GameController 类

Fruity 落 PCL 项目中包括`GameController`类负责实例化游戏和场景之间移动。 此类的 iOS 和 Android 项目用于消除重复代码。

中包含的代码`Initialize`方法类似于中的代码是`Initialize`中保持不变的 CocosSharp 模板，但它的方法包含多个修改。

默认情况下，`GameView.ContentManager.SearchPaths`取决于设备的分辨率。 Fruity 降到如下所述更多详细信息中，使用相同的内容，而不考虑设备分辨率，因此`Initialize`方法将添加`Images`路径 （无子文件夹） 到`SearchPaths`:


```csharp
contentSearchPaths.Add ("Images");
```

新 CocosSharp 模板包括继承的类`CCLayer`，这意味着游戏的视觉对象和逻辑应添加到此类。 Fruity 介于相反，使用多个`CCLayer`到控件的实例绘制顺序。 这些`CCLayer`实例都包含在`GameView`类，该类继承自`CCScene`。 Fruity Falls 还包括多个场景，则第一个操作数`TitleScene`。 因此，`Initialize`方法实例化`TitleScene`实例传递到`RunWithScene`:


```csharp
var scene = new TitleScene (GameView);
GameView.Director.RunWithScene (scene);
```

Fruity 降到的内容已创建为低分辨率像素画面出于美观原因。 `GameView.DesignResolution`设置，以便游戏只是 384 个单位宽、 高度为 512:


```csharp
// We use a lower-resolution display to get a pixellated appearance
int width = 384;
int height = 512;
GameView.DesignResolution = new CCSizeI (width, height); 
```

最后，`GameController`类提供一个静态方法，它可以由任何调用`CCGameScene`转换为另一种`CCScene`。 此方法用于之间移动`TitleScene`和`GameScene`。


## <a name="game-entities"></a>游戏的实体

Fruity Falls 利用实体模式的大多数游戏对象。 此模式的详细的说明可在[CocosSharp 中的实体指导](~/graphics-games/cocossharp/entities.md)。

可以在实体文件夹中找到的实体实现游戏对象**FruityFalls.Common**项目：

![](fruity-falls-images/image2.png "FruityFalls.Common 项目中的实体文件夹")

实体是继承的对象`CCNode`，并且可能具有视觉对象、 碰撞效果和每个帧行为。

`Fruit`对象都表示一个典型的 CocosSharp 实体： 它包含视觉对象、 碰撞效果和每个帧逻辑。 其构造函数负责初始化水果：


```csharp
public Fruit ()
{
    CreateFruitGraphic ();
    if (GameCoefficients.ShowCollisionAreas)
    {
        CreateDebugGraphic ();
    }
    CreateCollision ();
    CreateExtraPointsLabel ();
    Acceleration.Y = GameCoefficients.FruitGravity;
}
```


### <a name="fruit-graphics"></a>水果图形

`CreateFruitGraphic`方法创建`CCSprite`实例，并将其添加到`Fruit`。 `IsAntialiased`属性设置为 false，以使游戏具有像素化外观。 此值设置为 false 上所有`CCSprite`和`CCLabel`在游戏的实例：


```csharp
private void CreateFruitGraphic()
{
    graphic = new CCSprite ("cherry.png");
    graphic.IsAntialiased = false;
    this.AddChild (graphic);
}
```

只要玩家碰触到`Fruit`实例与`Paddle`，该水果的点值增加 1。 这提供到经验丰富的播放机能够同时处理更多青睐的水果了额外的挑战。 使用显示水果的点值`extraPointsLabel`实例。

`CreateExtraPointsLabel`方法创建`CCLabel`实例，并将其添加到`Fruit`:


```csharp
private void CreateExtraPointsLabel()
{
    extraPointsLabel = new CCLabel("", "Arial", 12, CCLabelFormat.SystemFont);
    extraPointsLabel.IsAntialiased = false;
    extraPointsLabel.Color = CCColor3B.Black;
    this.AddChild(extraPointsLabel);
}
```

Fruity Falls 包括两种不同类型的水果 – 樱桃和柠檬。 `CreateFruitGraphic`可以通过更改默认视觉对象，但水果视觉对象的分配`FruitColor`属性，后者又调用`UpdateGraphics`:


```csharp
private void UpdateGraphics()
{
if (GameCoefficients.ShowCollisionAreas)
    {
        debugGrahic.Clear ();
        const float borderWidth = 4;
        debugGrahic.DrawSolidCircle (
            CCPoint.Zero,
            GameCoefficients.FruitRadius,
            CCColor4B.Black);
        debugGrahic.DrawSolidCircle (
            CCPoint.Zero,
            GameCoefficients.FruitRadius - borderWidth,
            fruitColor.ToCCColor ());
    }
    if (this.FruitColor == FruitColor.Yellow)
    {
        graphic.Texture = CCTextureCache.SharedTextureCache.AddImage ("lemon.png");
        extraPointsLabel.Color = CCColor3B.Black;
        extraPointsLabel.PositionY = 0;
    }
    else
    {
        graphic.Texture = CCTextureCache.SharedTextureCache.AddImage ("cherry.png");
        extraPointsLabel.Color = CCColor3B.White;
        extraPointsLabel.PositionY = -8;
    }
}
```

中的第一个 if 语句`UpdateGraphics`更新调试图形，将用它们来直观显示冲突的区域。 之前的最终发行的游戏进行，但还可以在开发用于调试的物理过程会在这些视觉对象已关闭。 第二个部件发生更改`graphic.Texture`属性通过调用`CCTextureCache.SharedTextureCache.AddImage`。 此方法提供对纹理访问的文件的名称。 此方法的详细信息可在[纹理缓存指南](~/graphics-games/cocossharp/texture-cache.md)。

`extraPointsLabel`颜色调整以保证对比度水果图像，并将其`PositionY`值调整到中心`CCLabel`上水果的`CCSprite`:

![](fruity-falls-images/image3.png "ExtraPointsLabel 颜色调整以保证与水果图像对比度和 PositionY 值调整居中上水果 CCSprite CCLabel")

![](fruity-falls-images/image4.png "ExtraPointsLabel 颜色调整以保证与水果图像对比度和 PositionY 值调整居中上水果 CCSprite CCLabel")


### <a name="collision"></a>冲突

Fruity Falls 实现使用 Geometry 文件夹中的对象的自定义冲突解决方案：

![](fruity-falls-images/image5.png "Fruity Falls 实现使用 Geometry 文件夹中的对象的自定义冲突解决方案")

使用实现冲突发生 Fruity 介于`Circle`或`Polygon`通常具有作为子级的实体添加这两种类型的一个对象。 例如，`Fruit`对象具有`Circle`称为`Collision`它中初始化其`CreateCollision`方法：


```csharp
private void CreateCollision()
{
    Collision = new Circle ();
    Collision.Radius = GameCoefficients.FruitRadius;
    this.AddChild (Collision);
}
```

请注意，`Circle`类继承自`CCNode`类，因此它可以作为子级添加，到我们的游戏中的实体：


```csharp
public class Circle : CCNode
{
    ...
}
```

`Polygon` 创建是类似于`Circle`创建，如中所示`Paddle`类：


```csharp
private void CreateCollision()
{
    Polygon = Polygon.CreateRectangle(width, height);
    this.AddChild (Polygon);
}
```

冲突逻辑涵盖[在本指南后面](#collision)。


## <a name="physics"></a>物理引擎

Fruity 落在物理可以划分为两个类别： 移动和冲突。 


### <a name="movement-using-velocity-and-acceleration"></a>使用速度和加速移动

使用 Fruity Falls`Velocity`并`Acceleration`值，以控制其实体，类似于移动[BouncingGame](~/graphics-games/cocossharp/bouncing-game.md)。 实体在一个名为方法中实现其移动逻辑`Activity`，这每帧调用一次。 例如，我们可以看到的实现中的移动`Fruit`类的`Activity`方法：

```csharp
public void Activity(float frameTimeInSeconds)
{
    timeUntilExtraPointsCanBeAdded -= frameTimeInSeconds;
    
    // linear approximation:
    this.Velocity += Acceleration * frameTimeInSeconds;

    // This is a linear approximation to drag. It's used to
    // keep the object from falling too fast, and eventually
    // to slow its horizontal movement. This makes the game easier
    this.Velocity -= Velocity * GameCoefficients.FruitDrag * frameTimeInSeconds;
    
    this.Position += Velocity * frameTimeInSeconds;
}
```
`Fruit`对象中是唯一的拖动其实现 – 一个值，该值会降低速度相对于速度水果的速度是移动。 将此实现提供*终端速度*，这是故障的成效的目标实例的最大速度。 拖动还会降低使游戏稍微简单一些，若要播放的水果的水平移动。

`Paddle`对象也适用`Velocity`在其`Activity`方法，但其`Velocity`使用计算`desiredLocation`值：


```csharp
public void Activity(float frameTimeInSeconds)
{
    // This code will cause the cursor to lag behind the touch point
    // Increasing this value reduces how far the paddle lags
    // behind the player’s finger. 
    const float velocityCoefficient = 4;
    // Get the velocity from current location and touch location
    Velocity = (desiredLocation - this.Position) * velocityCoefficient;
    this.Position += Velocity * frameTimeInSeconds;
    ...
}
```

通常情况下，使用该游戏`Velocity`不直接控制其对象执行操作的位置设置实体的位置，至少不初始化后。 而不是直接设置`Paddle`位置，`Paddle.HandleInput`方法分配`desiredLocation`值：

```csharp
public void HandleInput(CCPoint touchPoint)
{
    desiredLocation = touchPoint;
}
```

### <a name="collision"></a>冲突

Fruity Falls 实现半真实水果和其他 collidable 对象之间的冲突，如`Paddle`和`GameScene.Splitter`。 为了帮助调试冲突，Fruity 回退冲突区域可以让用户看到通过更改`GameCoefficients.ShowDebugInfo`在`GameCoefficients.cs`文件：


```csharp
public static class GameCoefficients
{
    ... 
    public const bool ShowCollisionAreas = true;
    ...
}
```

此值设置为`true`可以冲突区域呈现：  

![](fruity-falls-images/image6.png "将此值设置为 true 可以冲突区域呈现")

中开始冲突逻辑`GameScene.Activity`方法：


```csharp
private void Activity(float frameTimeInSeconds)
{
    if (hasGameEnded == false)
    {
        paddle.Activity(frameTimeInSeconds);
        foreach (var fruit in fruitList)
        {
            fruit.Activity(frameTimeInSeconds);
        }
        spawner.Activity(frameTimeInSeconds);
        DebugActivity();
        PerformCollision();
    }
}
```

`PerformCollision` 处理冲突所有`Fruit`与其他对象的实例： 


```csharp
private void PerformCollision()
{
    // reverse for loop since fruit may be destroyed:
    for(int i = fruitList.Count - 1; i > -1; i--)
    {
        var fruit = fruitList[i];
        FruitVsPaddle(fruit);
        FruitPolygonCollision(fruit, splitter.Polygon, CCPoint.Zero);
        FruitVsBorders(fruit);
        FruitVsBins(fruit);
    }
}
```

#### <a name="fruitvsborders"></a>FruitVsBorders

`FruitVsBorders` 冲突的冲突，而不是依赖于另一个类中包含的逻辑执行其自己的逻辑。 存在这种差异的原因 （水果） 和屏幕的边框之间的碰撞不完全可靠 – 很可能水果小心球拍移动推送关闭屏幕边缘。 显示在屏幕上，用球拍，当点击上下弹跳水果，但如果播放机缓慢推送水果会移动超过边缘和不在屏幕上：


```csharp
private void FruitVsBorders(Fruit fruit)
{
    if(fruit.PositionX - fruit.Radius < 0 && fruit.Velocity.X < 0)
    {
        fruit.Velocity.X *= -1 * GameCoefficients.FruitCollisionElasticity;
    }
    if(fruit.PositionX + fruit.Radius > gameplayLayer.ContentSize.Width && fruit.Velocity.X > 0)
    {
        fruit.Velocity.X *= -1 * GameCoefficients.FruitCollisionElasticity;
    }
    if(fruit.PositionY + fruit.Radius > gameplayLayer.ContentSize.Height && fruit.Velocity.Y > 0)
    {
        fruit.Velocity.Y *= -1 * GameCoefficients.FruitCollisionElasticity;
    }
}
```

#### <a name="fruitvsbins"></a>FruitVsBins

`FruitVsBins`方法负责检查是否任何水果已降到两个箱之一。如果是这样，播放机奖励积分，（如果水果/bin 颜色匹配），或者 （如果不匹配的颜色），则游戏结束：


```csharp
private void FruitVsBins(Fruit fruit)
{
    foreach(var bin in fruitBins)
    {
        if(bin.Polygon.CollideAgainst(fruit.Collision))
        {
            if(bin.FruitColor == fruit.FruitColor)
            {
                // award points:
                score += 1 + fruit.ExtraPointValue;
                scoreText.Score = score;
                Destroy(fruit);
            }
            else
            {
                EndGame();
            }
            break;
        }
    }
}
```

#### <a name="fruitvspaddle-and-fruitpolygoncollision"></a>FruitVsPaddle 和 FruitPolygonCollision

与家用球拍和与拆分器 （分隔两个箱的区域） 的水果的水果冲突都依赖`FruitPolygonCollision`方法。 此方法有三个部分：

1. 测试是否对象发生碰撞
1. 移动的对象 （只需在 Fruity 介于水果），使它们不再重叠
1. 调整对象 （只需在 Fruity 介于水果） 的速度来模拟的弹跳下面的代码演示上面所做的点：


```csharp
private static bool FruitPolygonCollision(Fruit fruit, Polygon polygon, CCPoint polygonVelocity)
{
    // Test whether the fruit collides
    bool didCollide = polygon.CollideAgainst(fruit.Collision);
    if (didCollide)
    {
        var circle = fruit.Collision;
        // Get the separation vector to reposition the fruit so it doesn't overlap the polygon
        var separation = CollisionResponse.GetSeparationVector(circle, polygon);
        fruit.Position += separation;
        // Adjust the fruit's Velocity to make it bounce:
        var normal = separation;
        normal.Normalize();
        fruit.Velocity = CollisionResponse.ApplyBounce(
            fruit.Velocity, 
            polygonVelocity, 
            normal, 
            GameCoefficients.FruitCollisionElasticity);
    }
    return didCollide;
}
```

Fruity Falls 冲突响应是单侧 – 调整仅水果的速度和位置。 值得注意的其他游戏可能有冲突，这会影响所涉及的例如将博尔德县或崩溃的两个汽车推送到每个其他字符这两个对象。

中包含的冲突逻辑背后的数学`Polygon`和`CollisionResponse`类已超出本指南的范围，但作为编写，这些方法可用于多种类型的游戏。 多边形和`CollisionResponse`类甚至支持非矩形的凸多边形，因此，此代码可用于多种类型的游戏。

 


## <a name="game-content"></a>游戏内容

Fruity 介于中的画立即将游戏与 BouncingGame 区分开来。 虽然游戏设计很相似，播放机将立即看到这两个游戏的显示方式存在差异。 游戏玩家通常决定是否通过其视觉对象中玩游戏。 因此，它是极其重要的开发人员投入资源使具有视觉冲击力游戏。

Fruity 介于艺术创建与以下目标：

 - 一致的主题
 - 侧重于只是一个字符 – Xamarin monkey
 - 明亮的颜色创建放松、 愉悦体验
 - 前景色和背景之间对比，以便可以轻松直观地跟踪在玩游戏对象
 - 创建简单的视觉效果，而无需大量资源的动画功能


### <a name="content-location"></a>内容位置

Fruity Falls 包括其所有的内容在 Android 项目中的图像文件夹中：

![](fruity-falls-images/image7.png "Fruity Falls 包括其所有的内容文件夹中的映像中的 Android 项目")

这些相同的文件链接在 iOS 项目，以避免重复，并因此对文件进行更改会影响这两个项目：

![](fruity-falls-images/image8.png "这些相同的文件链接在 iOS 项目，以避免重复，并因此对文件进行更改会影响这两个项目")

值得注意是内容不包含在**Ld**或**Hd**文件夹，它是默认 CocosSharp 模板的一部分。 **Ld**并**Hd**旨在提供两个集的内容 — — 一个用于较低分辨率的设备，如电话和一个用于更高分辨率设备，如平板电脑的游戏使用的文件夹。 Fruity 介于艺术有意创建像素化美观，因此它不需要为不同屏幕大小提供内容。 因此， **Ld**并**Hd**文件夹已完全删除从项目。


### <a name="gamescene-layering"></a>GameScene 分层

在本指南前面部分中所述`GameScene`负责所有游戏对象实例化、 定位和对象间逻辑 （例如冲突）。 所有的对象添加到一个四个`CCLayer`实例：


```csharp
CCLayer backgroundLayer;
CCLayer gameplayLayer;
CCLayer foregroundLayer;
CCLayer hudLayer;
```

这四个层中创建`CreateLayers`方法。 请注意，添加到`GameScene`后到前的顺序：


```csharp
private void CreateLayers()
{
    backgroundLayer = new CCLayer();
    this.AddLayer(backgroundLayer);
    gameplayLayer = new CCLayer();
    this.AddLayer(gameplayLayer);
    foregroundLayer = new CCLayer();
    this.AddLayer(foregroundLayer);
    hudLayer = new CCLayer();
    this.AddLayer(hudLayer);
}
```

创建层，好后，所有视觉对象添加到使用的层`AddChild`方法，如中所示`CreateBackground`方法：


```csharp
private void CreateBackground()
{
    var background = new CCSprite("background.png");
    background.AnchorPoint = new CCPoint(0, 0);
    background.IsAntialiased = false;
    backgroundLayer.AddChild(background);
}
```


### <a name="vine-entity"></a>着实体

`Vine`实体唯一用于内容-它不会影响对游戏玩法。 它组成大小为 20 个`CCSprite`实例，大量通过反复选择以确保着始终到达屏幕的顶部：


```csharp
public Vine ()
{
    const int numberOfVinesToAdd = 20;
    for (int i = 0; i < numberOfVinesToAdd; i++)
    {
        var graphic = new CCSprite ("vine.png");
        graphic.AnchorPoint = new CCPoint(.5f, 0);
        graphic.PositionY = i * graphic.ContentSize.Height;
        this.AddChild (graphic);
    }
}
```

第一个`CCSprite`使其下边缘与匹配的着位置定位。 这通过将 AnchorPoint 设置为实现`new CCPoint(.5f, 0)`。 `AnchorPoint`属性使用*规范化坐标*哪些介于 0 和 1 无论规模大小`CCSprite`:

![](fruity-falls-images/image9.png "AnchorPoint 属性使用规范化的坐标介于 0 和 1 而不考虑 CCSprite 大小之间的范围")

后续着 sprite 位于使用`graphic.ContentSize.Height`值，该值以像素为单位返回图像的高度。 下图显示了着图的平铺方式向上：

![](fruity-falls-images/image10.png "下图显示了着图的平铺方式向上")

由于的原点`Vine`着，底部是实体则定位它非常简单，如中所示`Paddle.CreateVines`方法：


```csharp
private void CreateVines()
{
    // Increasing this value moves the vines closer to the 
    // center of the paddle.
    const int vineDistanceFromEdge = 4;
    leftVine = new Vine ();
    var leftEdge = -width / 2.0f;
    leftVine.PositionX = leftEdge + vineDistanceFromEdge;
    this.AddChild (leftVine);
    rightVine = new Vine ();
    var rightEdge = width / 2.0f;
    rightVine.PositionX = rightEdge - vineDistanceFromEdge;
    this.AddChild (rightVine);
}
```

着实例中`Paddle`实体还具有有趣的旋转行为。 由于`Paddle`实体作为一个整体旋转 player 输入 （它下面更详细地介绍了本指南），根据`Vine`实例也会受到此旋转。 但是，旋转`Vine`实例与`Paddle`生成不需要的可视化效果，如下面的动画中所示：

![](fruity-falls-images/image11.gif "但是，旋转以及球拍着实例会生成不需要的可视化效果如以下动画中所示")

抵消`Paddle`旋转`leftVine`并`rightVine`实例将旋转球拍，如中所示的反方向`Paddle.Activity`方法：


```csharp
public void Activity(float frameTimeInSeconds)
{
    ...
    // This value adds a slight amount of rotation
    // to the vine. Having a small amount of tilt looks nice.
    float vineAngle = this.Velocity.X / 100.0f;
    leftVine.Rotation = -this.Rotation + vineAngle;
    rightVine.Rotation = -this.Rotation + vineAngle;
}
```

请注意，返回到着通过添加少量旋转`vineAngle`系数。 可以更改此值调整 vines 旋转的量。


## <a name="gamecoefficients"></a>GameCoefficients

每个很好的游戏是次迭代时，该产品，因此 Fruity 介于包括一个名为类`GameCoefficients`它可以控制如何播放游戏。 此类包含富有表现力的变量，在整个游戏到的物理控件、 布局、 生成，和评分。

例如，由下变量控制水果物理引擎：


```csharp
public static class GameCoefficients
{
    ...
    
    // The strength of the gravity. Making this a 
    // smaller (bigger negative) number will make the
    // fruit fall faster. A larger (smaller negative) number
    // will make the fruit more floaty.
    public const float FruitGravity = -60;
    // The impact of "air drag" on the fruit, which gives
    // the fruit terminal velocity (max falling speed) and slows
    // the fruit's horizontal movement (makes the game easier)
    public const float FruitDrag = .1f;
    // Controls fruit collision bouncyness. A value of 1
    // means no momentum is lost. A value of 0 means all
    // momentum is lost. Values greater than 1 create a spring-like effect
    public const float FruitCollisionElasticity = .5f;
    
    ...
}
```

顾名思义，这些变量可用于调整速度水果降到如何水平移动速度变慢随着时间和球拍反弹。

存储在代码或数据文件 （如 XML) 的游戏系数尤其是十分适合团队进行游戏的设计人员不是编程人员。

`GameCoefficients`类还包括有关启用调试信息，如生成的信息或有冲突的区域的值：


```csharp
public static class GameCoefficients
{
    ...
    // This controls whether debug information is displayed on screen.
    public const bool ShowDebugInfo = false;
    public const bool ShowCollisionAreas = false;
    ...
}
```


## <a name="conclusion"></a>结束语

本指南探讨了 Fruity 介于游戏。 它包括概念包括内容、 物理特性、 和游戏状态管理。

## <a name="related-links"></a>相关链接

- [CocosSharp API 文档](https://developer.xamarin.com/api/namespace/CocosSharp/)
- [已完成的项目 （示例）](https://developer.xamarin.com/samples/mobile/FruityFalls/)
