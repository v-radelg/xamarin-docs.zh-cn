---
title: BouncingGame 详细信息
description: 本文档提供的演练用于 BouncingGame，使用 CocosSharp 构建简单跳动的球游戏创建。
ms.prod: xamarin
ms.assetid: AC9FD56F-6E4A-40DA-8168-45A761D869FD
author: conceptdev
ms.author: crdun
ms.date: 03/29/2018
ms.openlocfilehash: 9fd6c9108695f58bd69a1c4aa307ca2e4be6dede
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61357462"
---
# <a name="bouncinggame-details"></a>BouncingGame 详细信息

> [!TIP]
> 可以中找到的代码用于 BouncingGame [Xamarin 示例](https://developer.xamarin.com/samples/mobile/BouncingGame/)。 若要最适合按照本指南，请下载并浏览代码，如本指南引用它。

本演练演示如何实现使用 CocosSharp 的简单跳动的球游戏。 

 - 解压缩游戏内容
 - 常见 CocosSharp 可视元素
 - 添加到可视元素 `GameLayer`
 - 实现每个帧逻辑

我们已完成的游戏中将如下所示：

![BouncingGame，完成](bouncing-game-images/image1.png "BouncingGame，已完成")

## <a name="unzipping-game-content"></a>解压缩游戏内容

游戏开发人员通常使用术语*内容*来引用其通常由 visual 艺术家、 游戏设计器或音频设计器创建非代码文件。 常见类型的内容包括用于显示视觉对象、 播放声音，或控制人工智能 (AI) 行为的文件。 游戏开发团队的角度来看，从非程序员通常创建内容。 我们的游戏中包括两种类型的内容：

 - 若要定义球和球拍的显示方式的 png 文件。
 - 要定义分数显示 （当我们将添加得分显示屏下面更详细地讨论） 使用的字体的单个 xnb 文件

可在此处使用此内容[内容 zip](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Content.zip?raw=true)。 我们将需要下载并解压缩到的位置，我们将稍后在本演练中访问这些文件。

## <a name="common-cocossharp-visual-elements"></a>常见 CocosSharp 可视元素

CocosSharp 提供大量用于显示视觉对象的类。 某些元素是直接可见，而其他人用于进行组织。 在游戏中，我们将使用以下：

 - `CCNode` – 基类 CocosSharp 中的所有视觉对象。 `CCNode`类包含`AddChild`函数可用于构造父/子层次结构，它也称为*可视化树*或*场景图*。 下面所述的所有类都继承自`CCNode`。
 - `CCScene` – 所有 CocosSharp 游戏的可视化树的根。 所有可视化元素必须是与可视化树的一部分`CCScene`根位置，或它们不会显示。
 - `CCLayer` – 容器视觉对象，如`CCSprite`。 顾名思义，`CCLayer`类用于控制如何可视化元素层彼此堆叠。
 - `CCSprite` – 显示图像的一部分。 `CCSprite` 实例可以定位，调整大小，并提供大量视觉效果。
 - `CCLabel` – 在屏幕上显示的字符串。 使用的字体`CCLabel`xnb 文件中定义。 我们将讨论下面更详细地 xnbs。

若要了解如何使用不同类型的我们考虑一个`CCSprite`。 必须将可视元素添加到`CCLayer`，并且必须具有可视化树`CCScene`在其根目录。 因此，单一的父/子关系`CCSprite`会`CCScene`  >  `CCLayer`  >  `CCSprite`。

## <a name="adding-visual-elements-to-gamelayer"></a>将可视元素添加到 GameLayer

### <a name="adding-the-paddle-sprite"></a>添加球拍子画面

最初，我们将设置游戏的背景以黑色和也将添加单个`CCSprite`呈现在屏幕上。 修改`GameLayer`类来添加`CCSprite`，如下所示：

```csharp
using System;
using System.Collections.Generic;
using CocosSharp;
namespace BouncingGame
{
    public class GameLayer : CCLayer
    {
        CCSprite paddleSprite;
        public GameLayer () : base(CCColor4B.Black)
        {
            // "paddle" refers to the paddle.png image
            paddleSprite = new CCSprite ("paddle");
            paddleSprite.PositionX = 100;
            paddleSprite.PositionY = 100;
            AddChild (paddleSprite);
        }
        protected override void AddedToScene ()
        {
            base.AddedToScene ();
            // Use the bounds to layout the positioning of the drawable assets
            CCRect bounds = VisibleBoundsWorldspace;
            // Register for touch events
            var touchListener = new CCEventListenerTouchAllAtOnce ();
            touchListener.OnTouchesEnded = OnTouchesEnded;
            AddEventListener (touchListener, this);
        }
        void OnTouchesEnded (List<CCTouch> touches, CCEvent touchEvent)
        {
            if (touches.Count > 0)
            {
                // Perform touch handling here
            }
        }
    }
}
```

上面的代码创建一个`CCSprite`并将其添加为子级的`GameLayer`。 `CCSprite`构造函数可用于定义要用作字符串的图像文件。 我们的代码告知 CocosSharp 查找名为的文件`paddle`（扩展省略了，我们将稍后在本指南中进行讨论）。 任何文件的名称将查找 CocosSharp`paddle`根内容文件夹中 (即**内容**) 以及添加到任何文件夹`gameView.ContentManager.SearchPaths`（在上一节中讨论）。

我们将这些文件将直接添加到根**内容**适用于 iOS 和 Android 的文件夹。 为此，右键单击或上的控件 click**内容**文件夹中的 iOS 项目并选择**添加** > **添加文件...** 导航到在其中我们解压缩内容前面并选择**paddle.png**。 如果系统询问有关如何将文件添加到文件夹，我们应选择**复制**选项：

![将文件添加到文件夹对话框](bouncing-game-images/image2.png "将文件添加到文件夹对话框")

接下来，我们将添加到 Android 项目的文件。 右键单击或单击控件中内容的文件夹 (位于**资产**上 Android 项目的文件夹)，然后选择**添加** > **添加文件...**.这一次，导航到 iOS 项目**内容**文件夹。 当系统询问有关如何添加该文件，选择**添加链接**选项：

![将文件添加到文件夹对话框](bouncing-game-images/addalink.png "将文件添加到文件夹对话框")

我们将讨论为何文件必须添加到以下这两个项目。 每个项目**内容**文件夹现在包含**paddle.png**文件：

![内容文件夹的内容](bouncing-game-images/image3.png "内容文件夹的内容")

如果我们运行该游戏就会看到`CCSprite`所绘制：

![球拍，屏幕上绘制](bouncing-game-images/image4.png "球拍，屏幕上绘制")

### <a name="file-details"></a>文件的详细信息

到目前为止我们已添加到项目中，单个文件和过程是相当简单直接。 我们只需添加**paddle.png**的文件**内容**文件夹和在代码中引用它。 让我们看一段时间来处理 CocosSharp 中的文件时，看看一些注意事项。

#### <a name="capitalization"></a>大写

请注意，文件名称和我们使用在代码中访问该文件的字符串都是小写。 这是因为某些平台 （如 Windows 桌面和 iOS 模拟器） 是不区分大小写，而其他平台 （如 Android 和 iOS 设备） 是区分大小写。 我们将本教程的其余部分使用小写的所有文件，以便为跨平台会保留文件和代码尽可能。

#### <a name="extensions"></a>Extensions

引用球拍文件时，创建 Sprite 的构造函数不包括".png"扩展。 文件的扩展名通常省略时相同的资产类型的文件扩展名为 CocosSharp 项目平台之间可能有所不同。 例如，音频文件可能取决于平台的.aiff、.mp3 或.wma 文件格式。 保留禁用该扩展可确保相同的代码而不考虑文件扩展名的所有平台上工作。

#### <a name="content-in-platform-specific-projects"></a>特定于平台的项目中的内容

与大多数代码文件可以是在 PCL 中，不同的内容文件必须添加到每个特定于平台的项目。 CocosSharp 需要这两个原因：

1. 每个平台都有不同**生成操作**。 添加到 iOS 项目中的内容才能使用**BundleResource**生成操作。 应使用添加到 Android 项目的内容**AndroidAsset**生成操作。
2. 某些平台需要使用特定于平台的文件格式。 例如，字体.xnb 文件之间存在差异 iOS 和 Android，正如我们将稍后在本演练中看到。

如果文件格式并无差别 （如.png) 平台，每个平台可以使用同一个文件，其中一个或多个平台可能会将位于同一位置的该文件链接。

## <a name="orientation"></a>方向

就像任何其他应用一样 CocosSharp 应用可以运行以纵向或横向方向。 我们将调整游戏在仅纵向模式下运行。 首先，我们将更改以处理纵向纵横比在游戏中的解析代码。 若要执行此操作，修改`width`并`height`中的值`LoadGame`中的方法`ViewController`在 iOS 上的类和`MainActivity`在 Android 上的类：

```csharp
void LoadGame (object sender, EventArgs e)
{
    CCGameView gameView = sender as CCGameView;

    if (gameView != null)
    {
        var contentSearchPaths = new List<string> () { "Fonts", "Sounds" };
        CCSizeI viewSize = gameView.ViewSize;

        int width = 768;
        int height = 1027;
    // ...
```

接下来我们需要修改每个特定于平台的项目，以在纵向模式下运行。

### <a name="ios-orientation"></a>iOS 方向

若要修改 iOS 项目的方向，请选择**Info.plist**中的文件**BouncingGame.iOS**项目，并更改**iPhone/iPod 部署信息**和**iPad 部署信息**将只包括纵向方向：

![方向选项](bouncing-game-images/image5.png "方向选项")

### <a name="android-orientation"></a>Android 方向

若要修改 Android 项目的方向，BouncingGame.Android 项目中打开 MainActivity.cs 文件。 修改活动属性定义，以便它指定仅纵向方向，如下所示：

```csharp
[Activity (
    Label = "BouncingGame.Android",
    AlwaysRetainTaskState = true,
    Icon = "@drawable/icon",
    Theme = "@android:style/Theme.NoTitleBar",
    ScreenOrientation = ScreenOrientation.Portrait,
    LaunchMode = LaunchMode.SingleInstance,
    MainLauncher = true,
    ConfigurationChanges = ConfigChanges.Orientation | ConfigChanges.ScreenSize | ConfigChanges.Keyboard | ConfigChanges.KeyboardHidden)
]
public class MainActivity : Activity
{ 
...
```

## <a name="default-coordinate-system"></a>默认坐标系统

我们的代码，它会实例化`CCSprite`，设置`PositionX`和`PositionY`为 100 的值。 默认情况下，这意味着，`CCSprite`中心将位于 100 像素，最多并从屏幕的左下右侧。 可以通过将附加修改坐标系`CCCamera`到`CCLayer`。 我们不会使用`CCCamera`在此项目中，但的详细信息`CCCamera`中可以找到[CocosSharp API 文档](https://developer.xamarin.com/api/type/CocosSharp.CCLayer/)。

100 个像素，而不是像素为单位的"游戏"像素上面提到的硬件上。 这意味着，不同的解决方法 （如 iPhone 和 iPad) 在设备上运行相同的游戏会导致对象被定位和正确设置相对于物理屏幕大小。 具体而言，游戏的可见区域始终将 1024年像素、 高和 768 个像素宽，因为这是解决我们已在前面指定`LoadGame`方法。

## <a name="adding-the-ball-sprite"></a>添加球子画面

现在，我们要熟悉使用的基础知识`CCSprite`，我们将添加第二个`CCSprite`– 一个球。 我们将遵循步骤非常类似于我们如何创建球拍`CCSprite`。 

首先，我们将添加**ball.png**解压缩到 iOS 项目的文件夹中的映像**内容**文件夹。 选择**副本**到文件**内容**目录。 按照相同的步骤与上面添加一个指向**ball.png** Android 项目中的文件。

接下来，创建`CCSprite`对于通过将成员添加此球被称为`ballSprite`到`GameScene`类，以及为实例化代码`ballSprite`。 完成后`GameLayer`类将如下所示：

```csharp
public class GameLayer : CCLayer
{
    CCSprite paddleSprite;
    CCSprite ballSprite;
    public GameLayer () : base (CCColor4B.Black)
    {
        // "paddle" refers to the paddle.png image
        paddleSprite = new CCSprite ("paddle");
        paddleSprite.PositionX = 100;
        paddleSprite.PositionY = 100;
        AddChild (paddleSprite);
        ballSprite = new CCSprite ("ball");
        ballSprite.PositionX = 320;
        ballSprite.PositionY = 600;
        AddChild (ballSprite);
    }
```

## <a name="adding-the-score-label"></a>添加的评分标签

我们将向游戏添加的最后一个可视元素是`CCLabel`要显示的次数用户已成功退回球。 `CCLabel`使用构造函数中指定的字体在屏幕上显示字符串。

添加以下代码以创建`CCLabel`实例中`GameLayer`。 完成后，代码应如下所示：

```csharp
public class GameLayer : CCLayer
{
    CCSprite paddleSprite;
    CCSprite ballSprite;
    CCLabel scoreLabel;
    public GameLayer () : base (CCColor4B.Black)
    {
        // "paddle" refers to the paddle.png image
        paddleSprite = new CCSprite ("paddle");
        paddleSprite.PositionX = 100;
        paddleSprite.PositionY = 100;
        AddChild (paddleSprite);
        ballSprite = new CCSprite ("ball");
        ballSprite.PositionX = 320;
        ballSprite.PositionY = 600;
        AddChild (ballSprite);
        scoreLabel = new CCLabel ("Score: 0", "Arial", 20, CCLabelFormat.SystemFont);
        scoreLabel.PositionX = 50;
        scoreLabel.PositionY = 1000;
        scoreLabel.AnchorPoint = CCPoint.AnchorUpperLeft;
        AddChild (scoreLabel);
    }
    // ...
```

请注意，使用 scoreLabel`AnchorPoint`的`CCPoint.AnchorUpperLeft`，这意味着`PositionX`和`PositionY`值定义其左上角位置。 这可以让我们位置`scoreLabel`相对于而无需考虑标签的尺寸屏幕的左上方。

## <a name="implementing-every-frame-logic"></a>实现每个帧逻辑

到目前为止，游戏提供静态场景。 我们将添加逻辑以通过添加代码，这会更新以高频率的对象的位置来控制的对象在场景中移动。 在这种情况下，代码将运行每秒- 60 次*帧*每秒 （除非硬件不能处理频繁地更新此）。 具体而言，我们将添加逻辑以使球从处于并且针对球拍，来移动球拍根据输入，并更新玩家的分数，每次球发生球拍反弹。

`Schedule`方法，它提供的`CCNode`类中，可以让我们将每个帧逻辑添加到游戏。 我们将添加代码后的`// New code`GameLayer 构造函数：

```csharp
public GameLayer () : base (CCColor4B.Black)
{
    // "paddle" refers to the paddle.png image
    paddleSprite = new CCSprite ("paddle");
    paddleSprite.PositionX = 100;
    paddleSprite.PositionY = 100;
    AddChild (paddleSprite);
    ballSprite = new CCSprite ("ball");
    ballSprite.PositionX = 320;
    ballSprite.PositionY = 600;
    AddChild (ballSprite);
        scoreLabel = new CCLabel ("Score: 0", "arial", 22, CCLabelFormat.SpriteFont);
    scoreLabel.PositionX = 50;
    scoreLabel.PositionY = 1000;
    scoreLabel.AnchorPoint = CCPoint.AnchorUpperLeft;
    AddChild (scoreLabel);
    // New code:
    Schedule (RunGameLogic);
}
```

现在，创建`RunGameLogic`中的方法`GameLayer`类，该类将容纳所有的每个帧逻辑：

```csharp
void RunGameLogic(float frameTimeInSeconds)
{
}
```

浮点型参数定义多长时间帧都是以秒为单位。 实现该球的移动时，我们将在使用此值。

### <a name="making-the-ball-fall"></a>使球在

我们可以使球从属于任何手动实现的重力代码或使用 CocosSharp 中内置的 Box2D 功能。 Box2D 物理模拟引擎可供 CocosSharp 游戏。 这是非常强大且有效，但需要编写安装程序代码。 由于物理模拟是相当简单，此处它将实现手动。

实施重力我们需要对存储当前的 X 和 Y 的球的速度。 我们将添加到两个成员`GameLayer`类：

```csharp
float ballXVelocity;
float ballYVelocity;
// How much to modify the ball's y velocity per second:
const float gravity = 140;
```

接下来，我们可以实现中的诈骗逻辑`RunGameLogic`:

```csharp
void RunGameLogic(float frameTimeInSeconds)
{
    // This is a linear approximation, so not 100% accurate
    ballYVelocity += frameTimeInSeconds * -gravity;
    ballSprite.PositionX += ballXVelocity * frameTimeInSeconds;
    ballSprite.PositionY += ballYVelocity * frameTimeInSeconds;
}
```

### <a name="moving-the-paddle-with-touch-input"></a>移动球拍使用触摸输入

现在，这就会球，我们将添加水平移动到球拍使用`CCEventListenerTouchAllAtOnce`对象。 此对象提供与输入相关的事件的数。 在这种情况下，我们想要如果任何接触点移动，以便我们可以调整球拍的位置，则通知。 `GameLayer`已实例化`CCEventListenerTouchAllAtOnce`，因此我们只需将`OnTouchesMoved`委托。 若要执行此操作，请按如下所示修改 AddedToScene 方法：

```csharp
protected override void AddedToScene ()
{
    base.AddedToScene ();
    // Use the bounds to layout the positioning of the drawable assets
    CCRect bounds = VisibleBoundsWorldspace;
    // Register for touch events
    var touchListener = new CCEventListenerTouchAllAtOnce ();
    touchListener.OnTouchesEnded = OnTouchesEnded;
    // new code:
    touchListener.OnTouchesMoved = HandleTouchesMoved;
    AddEventListener (touchListener, this);
}
```

接下来，我们将实现`HandleTouchesMoved`:

```csharp
void HandleTouchesMoved (System.Collections.Generic.List<CCTouch> touches, CCEvent touchEvent)
{
    // we only care about the first touch:
    var locationOnScreen = touches [0].Location;
    paddleSprite.PositionX = locationOnScreen.X;
}
```

### <a name="implementing-ball-collision"></a>实现球冲突

如果我们运行游戏，现在我们会注意到球落通过球拍。 我们将实现*冲突*（逻辑来应对重叠的游戏对象） 中的每个帧代码。 通常是由于移动对象更改定位每个帧，冲突检测，也执行每一帧。 我们还将新增速度 X 轴上时球击中球拍向游戏中，添加一些挑战，但这意味着我们需要该球保持飘浮过去的屏幕的边缘移动。 我们将执行此操作`RunGameLogic`代码应用到的速度之后`ballSprite`后`// New Code`:

```csharp
void RunGameLogic(float frameTimeInSeconds)
{
    // This is a linear approximation, so not 100% accurate
    ballYVelocity += frameTimeInSeconds * -gravity;
    ballSprite.PositionX += ballXVelocity * frameTimeInSeconds;
    ballSprite.PositionY += ballYVelocity * frameTimeInSeconds;
    // New Code:
    // Check if the two CCSprites overlap...
    bool doesBallOverlapPaddle = ballSprite.BoundingBoxTransformedToParent.IntersectsRect(
        paddleSprite.BoundingBoxTransformedToParent);
    // ... and if the ball is moving downward.
    bool isMovingDownward = ballYVelocity < 0;
    if (doesBallOverlapPaddle && isMovingDownward)
    {
        // First let's invert the velocity:
        ballYVelocity *= -1;
        // Then let's assign a random value to the ball's x velocity:
        const float minXVelocity = -300;
        const float maxXVelocity = 300;
        ballXVelocity = CCRandom.GetRandomFloat (minXVelocity, maxXVelocity);
    }
    // First let’s get the ball position:   
    float ballRight = ballSprite.BoundingBoxTransformedToParent.MaxX;
    float ballLeft = ballSprite.BoundingBoxTransformedToParent.MinX;
    // Then let’s get the screen edges
    float screenRight = VisibleBoundsWorldspace.MaxX;
    float screenLeft = VisibleBoundsWorldspace.MinX;
    // Check if the ball is either too far to the right or left:    
    bool shouldReflectXVelocity = 
        (ballRight > screenRight && ballXVelocity > 0) ||
        (ballLeft < screenLeft && ballXVelocity < 0);
    if (shouldReflectXVelocity)
    {
        ballXVelocity *= -1;
    }
}
```

## <a name="adding-scoring"></a>添加评分

现在，可播放游戏，最后一步是添加评分的逻辑。 首先，我们将添加到名为的 GameLayer 类分数成员`score`:

```csharp
int score;
```

我们将使用此变量来跟踪玩家的分数，并显示其使用`scoreLabel`。 若要执行此添加以下代码中的 if 语句`RunGameLogic`当球和球拍重叠：

```csharp
// ...
if (doesBallOverlapPaddle && isMovingDownward)
{
    // First let's invert the velocity:
    ballYVelocity *= -1;
    // Then let's assign a random to the ball's x velocity:
    const float minXVelocity = -300;
    const float maxXVelocity = 300;
    ballXVelocity = CCRandom.GetRandomFloat (minXVelocity, maxXVelocity);
    // New code:
    score++;
    scoreLabel.Text = "Score: " + score;
}
// ...
```

现在我们可以运行该游戏，并查看游戏显示这增大如球击中从球拍分数：

![已完成的游戏，具有递增的评分](bouncing-game-images/image1.png "完成的游戏，具有递增的评分")

## <a name="summary"></a>总结

本演练介绍使用图形、 物理特性，以及使用 CocosSharp 输入创建跨平台游戏。 它是开始使用 CocosSharp 游戏开发的第一步。 我们了解了一些 CocosSharp、 如何构造可视化树，因此对象呈现正确，以及如何实现每个帧游戏逻辑中的最常见的类。

本演练介绍了仅 CocosSharp 游戏引擎提供一小的部分。 有关信息和其他 CocosSharp 主题上的演练，请参阅[CocosSharp 指南的其余部分](~/graphics-games/cocossharp/index.md)。

## <a name="related-links"></a>相关链接

- [已完成的游戏 （示例）](https://developer.xamarin.com/samples/mobile/BouncingGame/)
- [游戏内容 （示例）](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Content.zip?raw=true)
