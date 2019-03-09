---
title: CocosSharp 中的实体
description: 实体模式是组织的游戏代码的强大方法。 它提高了可读性，使代码更易于维护，并使用内置的父/子功能。
ms.prod: xamarin
ms.assetid: 1D3261CE-AC96-4296-8A53-A76A42B927A8
author: conceptdev
ms.author: crdun
ms.date: 03/27/2017
ms.openlocfilehash: 42034261c374183346c8072eb42014f43a4fe22c
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57667602"
---
# <a name="entities-in-cocossharp"></a>CocosSharp 中的实体

_实体模式是组织的游戏代码的强大方法。它提高了可读性，使代码更易于维护，并使用内置的父/子功能。_

实体模式可以提高通过改进的代码的组织使用 CocosSharp 开发人员的工作。 本演练将演示如何创建两个实体 – 发货实体和项目符号实体的实际操作示例。 这些实体将是自包含的对象，这意味着，一次实例化它们将自动呈现，并将执行移动逻辑根据它们的类型。 

本指南涉及以下主题：

 - 介绍游戏实体
 - 与特定实体类型的常规
 - 项目设置
 - 创建实体类
 - 添加到的实体实例 `GameLayer`
 - 响应中的实体逻辑 `GameLayer`

已完成的游戏将如下所示：

![](entities-images/image1.png "已完成的游戏将如下所示")


## <a name="introduction-to-game-entities"></a>介绍游戏实体

游戏的实体是定义需要呈现、 冲突、 物理特性或人工智能逻辑的对象的类。 幸运的是，游戏的代码库中存在的实体通常与在游戏中的概念性对象。 在这种情况，确定需要在游戏中的实体可以更轻松地完成。 

例如，主题空格[投篮机会控制在其启动游戏](https://en.wikipedia.org/wiki/Shoot_%27em_up)可能包括以下实体：

 - `PlayerShip`
 - `EnemyShip`
 - `PlayerBullet`
 - `EnemyBullet`
 - `CollectableItem`
 - `HUD`
 - `Environment`

这些实体将为其自己的类在游戏中，并且每个实例需要很少或没有安装超出了实例化。


## <a name="general-vs-specific-entity-types"></a>与特定实体类型的常规

使用实体系统的游戏开发人员所面临的首要问题之一是要对其实体进行一般化多少。 即使它们具有不同的几个特征的最具体的实现将定义的实体，每种类型的类。 更多常规系统将合并到一个类中，实体组，并允许进行自定义的实例。

例如，我们可以假设一个空间游戏，它定义了以下类：

 - `PlayerDogfighter`
 - `PlayerBomber`
 - `EnemyMissileShip`
 - `EnemyLaserShip`

更通用的方法就是创建播放机一起提供的单个类和敌人出厂，可以配置为支持不同的发布类型的单个类。 自定义项可能包含要加载，哪种类型的项目符号实体射击、 移动系数时创建的映像和 AI 逻辑的防范对象提供。 在这种情况下的实体列表可能会降低到：

 - `PlayerShip`
 - `EnemyShip`

当然，这些实体类型可以进一步通过允许每个实例自定义用于控制移动通用化。 敌人发货实例可能会执行 AI 逻辑时，播放机发货实例将从输入读取。 这意味着无法通用化实体甚至进一步深入到单个类：

 - `Ship`

一个游戏可能会使用单个基类的所有实体，泛化可以进一步 – 继续。 此单个类，可以将其称为`GameEntity`，是用于在整个游戏中每个实体实例的类，包括不是实体提供项目符号和可回收项 （增益道具） 等。

此系统的大多数常规通常称为*组件系统*。 在此类系统中，游戏实体可以具有单个组件的物理，AI，如或呈现组件添加自定义行为和外观。 纯粹基于组件的系统启用最大的灵活性，并可以避免使用复杂的继承链如继承所引起的问题。 与其他泛化，有效组件系统可能很难设置，并且可以减少代码的表现力。

使用的泛化的级别取决于考虑多种因素，包括：

 - 游戏大小 – 较小的游戏可以承受创建特定的类，而更大的游戏可能很难使用大量的类进行管理。
 - 数据驱动的开发-依赖于数据 （图像、 三维模型和数据文件，如 JSON 或 XML） 的游戏可能受益于具有通用化实体类型和配置数据的基础之上的细节。 这是预计会在开发期间或之后发布游戏中添加新内容的游戏尤其是导入。
 - 游戏引擎模式 – 某些游戏引擎强烈建议组件系统的使用情况而另一些则允许开发人员可以决定如何组织实体。 CocosSharp 不需要系统使用的组件，因此开发人员可以随意实施任何类型的实体。 

为简单起见，我们将使用特定的基于类的方法与一个发布和项目符号实体为本教程。


## <a name="project-setup"></a>项目设置

我们开始实现我们实体之前，我们需要创建一个项目。 我们将使用 CocosSharp 项目模板来简化项目创建。 [检查此文章](http://forums.xamarin.com/discussion/26822/cocossharp-project-templates-for-xamarin-studio)有关从 Visual Studio for Mac 模板创建 CocosSharp 项目信息。 本指南的其余部分将使用项目名称**EntityProject**。

我们的项目创建后我们将设置要在 480 x 320 运行我们的游戏中的解析方法。 若要执行此操作，调用`CCScene.SetDefaultDesignResolution`在`GameAppDelegate.ApplicationDidFinishLaunching`方法，如下所示：


```csharp
public override void ApplicationDidFinishLaunching (CCApplication application, CCWindow mainWindow)
{
    ...

    // New code for resolution setting:
    CCScene.SetDefaultDesignResolution(480, 320, CCSceneResolutionPolicy.ShowAll);
    
    CCScene scene = new CCScene (mainWindow);
    GameLayer gameLayer = new GameLayer ();

    scene.AddChild (gameLayer);
    mainWindow.RunWithScene (scene);
} 
```

处理 CocosSharp 的解决方法的详细信息，请参阅我们[上处理 CocosSharp 中的多个解决方案指南](~/graphics-games/cocossharp/resolutions.md)。


## <a name="adding-content-to-the-project"></a>将内容添加到项目

一旦创建我们的项目后，我们将添加中包含的文件[此内容的 zip 文件](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Entities.zip?raw=true)。 若要执行此操作，下载的 zip 文件并将其解压缩。 添加两者**ship.png**并**bullet.png**到**内容**文件夹。 **内容**文件夹将为内部**资产**文件夹在 Android 上，并且将在 iOS 上项目的根目录。 添加后，应该会看到这两个文件中的**内容**文件夹：

![](entities-images/image2.png "添加后，这两个文件应位于内容文件夹中")


## <a name="creating-the-ship-entity"></a>创建发货实体

`Ship`类将为我们的游戏中的第一个实体。 若要添加`Ship`类中，首先创建一个名为文件夹**实体**项目的根级别上。 添加新的类中**实体**名为的文件夹`Ship`:

![](entities-images/image3.png "在名为发货实体文件夹中添加一个新类")

我们将对的第一个更改我们`Ship`类是让它继承自`CCNode`类。 `CCNode` 可作为通用 CocosSharp 类的基类喜欢`CCSprite`和`CCLayer`，并为我们提供了以下功能：

 - `Position` 移动屏幕上四处飞船的属性。
 - `Children` 添加属性 `CCSprite.`
 - `Parent` 属性，可用于将附加`Ship`到其他实例`CCNodes`。 我们不会在本教程; 使用此功能更大的游戏通常要将实体附加到其他利用`CCNodes`。 
 - `AddEventListener` 响应输入移动飞船的方法。
 - `Schedule` 排除项目符号的方法。

我们还将添加`CCSprite`实例，以便在屏幕上看到我们发货：


```csharp
using System;
using CocosSharp;

namespace EntityProject
{
    public class Ship : CCNode
    {
        CCSprite sprite;

        public Ship () : base()
        {
            sprite = new CCSprite ("ship.png");
            // Center the Sprite in this entity to simplify
            // centering the Ship when it is instantiated
            sprite.AnchorPoint = CCPoint.AnchorMiddle;
            this.AddChild(sprite);
        }
    }
}
```

接下来，我们将添加到发货我们`GameLayer`若要查看其显示在我们的游戏：


```csharp
public class GameLayer : CCLayer
{
    Ship ship;

    public GameLayer ()
    {
        ship = new Ship ();
        ship.PositionX = 240;
        ship.PositionY = 50;
        this.AddChild (ship);
    } 
    ...
```

如果我们运行我们的游戏中我们现在将看到我们发货的实体：

![](entities-images/image4.png "当运行游戏时，将显示发货实体")


### <a name="why-inherit-from-ccnode-instead-of-ccsprite"></a>为什么继承而不是 CCSprite CCNode？

此时我们`Ship`类是简单的包装程序`CCSprite`实例。 由于`CCSprite`也继承`CCNode`，我们无法直接从已继承`CCSprite`，这将减少中的代码`Ship.cs`。 此外，直接从继承`CCSprite`减少了内存中对象的数目并提高性能，从而依赖关系树较小。

尽管这些好处，我们继承自`CCNode`来隐藏其中部分`CCSprite`每个实例中的属性。 例如，`Texture`属性不应修改之外`Ship`类，并继承自`CCNode`使我们能够隐藏该属性。 我们的实体的公共成员会变得尤为重要，正如游戏变得更大的以及其他开发人员添加到组中。


## <a name="adding-input-to-the-ship"></a>添加飞船的输入

现在我们这艘是屏幕上可见，我们将添加输入。 我们的方法将类似于中采用的方法[BouncingGame 指南](~/graphics-games/cocossharp/bouncing-game.md)，只不过我们放置的代码中的移动`Ship`类而不是在包含`CCLayer`或`CCScene`。

将代码添加到`Ship`以支持将它移动到用户触摸屏幕的任何位置：


```csharp
public class Ship : CCNode
{
    CCSprite sprite;

    CCEventListenerTouchAllAtOnce touchListener;

    public Ship () : base()
    {
        sprite = new CCSprite ("ship.png");
        // Center the Sprite in this entity to simplify
        // centering the Ship on screen
        sprite.AnchorPoint = CCPoint.AnchorMiddle;
        this.AddChild(sprite);

        touchListener = new CCEventListenerTouchAllAtOnce();
        touchListener.OnTouchesMoved = HandleInput;
        AddEventListener(touchListener, this);

    }

    private void HandleInput(System.Collections.Generic.List<CCTouch> touches, CCEvent touchEvent)
    {
        if(touches.Count > 0)
        {
            CCTouch firstTouch = touches[0];

            this.PositionX = firstTouch.Location.X;
            this.PositionY = firstTouch.Location.Y;
        } 
    }
} 
```

许多投篮机会控制在他们说了游戏实现最大速度，模拟传统的基于控制器的移动。 也就是说，我们只需将实现即时移动，以使我们更短的代码。


## <a name="creating-the-bullet-entity"></a>创建项目符号实体

简单的游戏中的第二个实体是用于显示项目符号的实体。 就像`Ship`实体，`Bullet`实体将包含`CCSprite`以使其显示在屏幕上。 要移动的逻辑不同之处在于它不依赖于移动; 的用户输入相反，`Bullet`实例将一条直线使用 velocity 属性。

首先，我们将添加到新的类文件我们**实体**文件夹，并调用它**项目符号**:

![](entities-images/image5.png "将新的类文件添加到实体文件夹，调用它项目符号")

添加我们将修改后`Bullet.cs`代码，如下所示：


```csharp
using System;
using CocosSharp;

namespace EntityProject
{
    public class Bullet : CCNode
    {
        CCSprite sprite;

        public float VelocityX
        {
            get;
            set;
        }

        public float VelocityY
        {
            get;
            set;
        }

        public Bullet () : base()
        {
            sprite = new CCSprite ("bullet.png");
            // Making the Sprite be centered makes
            // positioning easier.
            sprite.AnchorPoint = CCPoint.AnchorMiddle;
            this.AddChild(sprite);

            this.Schedule (ApplyVelocity);
        }

        void ApplyVelocity(float time)
        {
            PositionX += VelocityX * time;
            PositionY += VelocityY * time;
        }
    }
} 
```

除了更改为使用的文件`CCSprite`到`bullet.png`中的代码`ApplyVelocity`包括移动逻辑基于两个系数：`VelocityX`和`VelocityY`。

`Schedule`方法允许添加委托，调用每个帧。 在这种情况下，我们将添加`ApplyVelocity`方法，以便我们项目符号将根据其速度值。 `Schedule`方法采用`Action<float>`，其中的浮点型参数指定的最后一帧，我们使用来实现基于时间的移动时间 （以秒为单位）。 时间值以秒为单位，然后我们速度值表示中的移动*每秒像素*。


## <a name="adding-bullets-to-gamelayer"></a>向 GameLayer 添加项目符号

我们添加任何之前`Bullet`实例迁移到我们的游戏中，我们将生成一个容器，专门`List<Bullet>`。 修改`GameLayer`使其包含的项目符号列表：


```csharp
    public class GameLayer : CCLayer
    {
        Ship ship;
        List<Bullet> bullets;

        public GameLayer ()
        {
            ship = new Ship ();
            ship.PositionX = 240;
            ship.PositionY = 50;
            this.AddChild (ship);

            bullets = new List<Bullet> ();
        }
        ... 
```

接下来我们需要填充`Bullet`列表。 何时创建的逻辑`Bullet`应包含在`Ship`实体，但`GameLayer`是负责存储的项目符号列表。 我们将使用工厂模式以允许`Ship`实体创建`Bullet`实例。 此工厂将公开事件的`GameLayer`可以处理。 

若要执行此操作首先，我们会将文件夹添加到名为我们项目**工厂**，然后添加一个名为的新类`BulletFactory`:

![](entities-images/image6.png "将文件夹添加到名为工厂的项目，然后添加一个名为 BulletFactory 的新类")

接下来，我们将实现`BulletFactory`singleton 类：


```csharp
using System;

namespace EntityProject
{
    public class BulletFactory
    {
        static Lazy<BulletFactory> self = 
            new Lazy<BulletFactory>(()=>new BulletFactory());

        // simple singleton implementation
        public static BulletFactory Self
        {
            get
            {
                return self.Value;
            }
        }

        public event Action<Bullet> BulletCreated;

        private BulletFactory()
        {

        }

        public Bullet CreateNew()
        {
            Bullet newBullet = new Bullet ();

            if (BulletCreated != null)
            {
                BulletCreated (newBullet);
            }

            return newBullet;
        }
    }
} 
```

`Ship`实体将处理创建`Bullet`实例-具体而言，它将处理频率`Bullet`（即何种频率触发项目符号），应创建实例，其位置和其速度。

修改`Ship`实体的构造函数，以添加新`Schedule`调用，然后实现此方法，如下所示：


```csharp
...
public Ship () : base()
{
    sprite = new CCSprite ("ship.png");
    // Center the Sprite in this entity to simplify
    // centering the Ship on screen
    sprite.AnchorPoint = CCPoint.AnchorMiddle;
    this.AddChild(sprite);

    touchListener = new CCEventListenerTouchAllAtOnce();
    touchListener.OnTouchesMoved = HandleInput;
    AddEventListener(touchListener, this);

    Schedule (FireBullet, interval: 0.5f);

}

void FireBullet(float unusedValue)
{
    Bullet newBullet = BulletFactory.Self.CreateNew ();
    newBullet.Position = this.Position;
    newBullet.VelocityY = 100;
} 
...
```

最后一步是处理创建的新`Bullet`实例中`GameLayer`代码。 添加到事件处理程序`BulletCreated`添加了新创建的事件`Bullet`到相应的列表：


```csharp
...
public GameLayer ()
{
    ship = new Ship ();
    ship.PositionX = 240;
    ship.PositionY = 50;
    this.AddChild (ship);

    bullets = new List<Bullet> ();
    BulletFactory.Self.BulletCreated += HandleBulletCreated;
}

void HandleBulletCreated(Bullet newBullet)
{
    AddChild (newBullet);
    bullets.Add (newBullet);
}
... 
```

现在，我们可以运行游戏，请参阅`Ship`排除`Bullet`实例：

![](entities-images/image1.png "运行该游戏，并随附了将排除项目符号实例")


## <a name="why-gamelayer-has-ship-and-bullets-members"></a>为什么 GameLayer 具有发货和项目符号的成员

我们`GameLayer`类定义了两个字段来保存对我们实体实例的引用 (`ship`和`bullets`)，但不与它们执行任何操作。 此外，实体负责自己行为，如移动和排除。 因此，为何未我们添加`ship`并`bullets`字段设置为`GameLayer`？

我们添加了这些成员的原因是因为游戏实现可能要求中的逻辑`GameLayer`不同实体之间的交互。 例如，可以进一步开发此游戏包括可播放机销毁的敌人。 将中包含这些敌人`List`中`GameLayer`，和逻辑来测试是否`Bullet`实例与相冲突敌人将按`GameLayer`也。 换而言之，`GameLayer`是根*所有者*所有实体的实例，它是负责实体实例之间的交互。


## <a name="bullet-destruction-considerations"></a>项目符号析构注意事项

我们的游戏中目前缺少代码来销毁`Bullet`实例。 每个`Bullet`实例都有用于在屏幕上，移动逻辑，但我们尚未添加任何代码来销毁任何屏幕`Bullet`实例。

此外，析构`Bullet`实例可能不属于`GameLayer`。 例如，而被销毁时屏外，不是`Bullet`实体可能具有在一段时间后销毁自身的逻辑。 在这种情况下，`Bullet`需要通过某种方式进行通信应销毁到`GameLayer`，类似于`Ship`实体传递给`GameLayer`的新`Bullet`通过创建`BulletFactory`。

最简单的解决方案是展开的工厂类，以支持析构责任。 然后在工厂是否可以收到通知的实体实例被破坏，这可以由其他对象，如`GameLayer`从其列表中删除实体实例。 

## <a name="summary"></a>总结

本指南演示如何通过继承创建 CocosSharp 实体`CCNode`类。 这些实体是自包含的对象，处理创建他们自己的视觉对象和自定义逻辑。 本指南从属于根实体容器 （碰撞效果和其他实体的交互逻辑） 的代码指定所属的实体 （移动和创建其他实体） 内的代码。

## <a name="related-links"></a>相关链接

- [CocosSharp API 文档](https://developer.xamarin.com/api/namespace/CocosSharp/)
- [内容 zip](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Entities.zip?raw=true)
