---
title: iOS 游戏 Api 在 Xamarin.iOS
description: 本文介绍如何提供可用于改进您的 Xamarin.iOS 游戏图形和音频功能的 iOS 9 的新游戏增强功能。
ms.prod: xamarin
ms.assetid: 958D38FD-9240-482E-9A42-D6671ED8F2B0
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: d8a531e495a19be7437d4a600e758028594248ab
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60953303"
---
# <a name="ios-gaming-apis-in-xamarinios"></a>iOS 游戏 Api 在 Xamarin.iOS

_本文介绍如何提供可用于改进您的 Xamarin.iOS 游戏图形和音频功能的 iOS 9 的新游戏增强功能。_

Apple 已在 iOS 9 中玩游戏 Api，有助于轻松地在 Xamarin.iOS 应用程序中实现游戏图形和音频的多项技术改进。
其中包括这两种易用性开发到高级框架和提高了的速度和图形能力的 iOS 设备的 GPU 的强大功能。

[![](images/flocking01.png "运行以应用程序示例")](images/flocking01.png#lightbox)

这包括 GameplayKit、 ReplayKit、 模型 I/O、 MetalKit 和金属性能着色器以及裸机、 SceneKit 和 SpriteKit 的新的增强型功能。

本文将介绍各种方式来提高您的 Xamarin.iOS 游戏具有 iOS 9 的新游戏增强功能：

## <a name="introducing-gameplaykit"></a>引入 GameplayKit

Apple 的新 GameplayKit 框架提供了一组技术，它可以更轻松地减少重复的、 常见实现所需的代码量，创建适用于 iOS 设备的游戏。 GameplayKit 提供工具，用于为快速开发游戏机制，可轻松地组合使用 （例如 SceneKit 或 SpriteKit） 图形引擎提供已完成的游戏。

GameplayKit 包括若干常见，游戏播放算法，如：

- 基于行为，允许你定义的移动和 AI 将自动追求的目标的代理模拟。
- 基于轮次的游戏玩法 minmax 人工智能。
- 数据驱动的游戏逻辑与模糊推理来提供紧急行为规则系统。

此外，GameplayKit 构建基块通过采用的方法到游戏开发使用模块化体系结构，它提供以下功能：

- 用于处理复杂的过程性代码的状态机基于中玩游戏的系统。
- 提供的工具而不会导致调试问题随机化玩游戏和不可预测性。
- 可重用的组件化实体基础体系结构。

若要了解有关 GameplayKit 的详细信息，请参阅 Apple [Gameplaykit 编程指南](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/GameplayKit_Guide/index.html#//apple_ref/doc/uid/TP40015172)并[GameplayKit 框架引用](https://developer.apple.com/library/prerelease/ios/documentation/GameplayKit/Reference/GameplayKit_Framework/index.html#//apple_ref/doc/uid/TP40015199)。

## <a name="gameplaykit-examples"></a>GameplayKit 示例

让我们快速看一下使用玩游戏工具包的 Xamarin.iOS 应用中实现一些简单的游戏玩法机制。

### <a name="pathfinding"></a>Pathfinding

Pathfinding 是游戏的 AI 元素能够找到游戏板围绕其方法。
例如，查找其错综复杂的方式或通过第一人称世界地形 3D 字符 2D 防范对象。

请考虑以下映射：

[![](images/gkpathfindpath.png "示例 pathfinding 映射")](images/gkpathfindpath.png#lightbox)

使用 pathfinding 此C#代码可找到一种方法通过映射：

```csharp
var a = GKGraphNode2D.FromPoint (new Vector2 (0, 5));
var b = GKGraphNode2D.FromPoint (new Vector2 (3, 0));
var c = GKGraphNode2D.FromPoint (new Vector2 (2, 6));
var d = GKGraphNode2D.FromPoint (new Vector2 (4, 6));
var e = GKGraphNode2D.FromPoint (new Vector2 (6, 5));
var f = GKGraphNode2D.FromPoint (new Vector2 (6, 0));

a.AddConnections (new [] { b, c }, false);
b.AddConnections (new [] { e, f }, false);
c.AddConnections (new [] { d }, false);
d.AddConnections (new [] { e, f }, false);

var graph = GKGraph.FromNodes(new [] { a, b, c, d, e, f });

var a2e = graph.FindPath (a, e); // [ a, c, d, e ]
var a2f = graph.FindPath (a, f); // [ a, b, f ]

Console.WriteLine(String.Join ("->", (object[]) a2e));
Console.WriteLine(String.Join ("->", (object[]) a2f));
```

### <a name="classical-expert-system"></a>经典专家系统

以下代码片段的C#的代码演示如何使用 GameplayKit 实现经典的专家系统：

```csharp
string output = "";
bool reset = false;
int input = 15;

public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    /*
    If reset is true, clear the output and set reset to false
    */
    var clearRule = GKRule.FromPredicate ((rules) => reset, rules => {
        output = "";
        reset = false;
    });
    clearRule.Salience = 1;

    var fizzRule = GKRule.FromPredicate (mod (3), rules => {
        output += "fizz";
    });
    fizzRule.Salience = 2;

    var buzzRule = GKRule.FromPredicate (mod (5), rules => {
        output += "buzz";
    });
    buzzRule.Salience = 2;

    /*
    This *always* evaluates to true, but is higher Salience, so evaluates after lower-salience items
    (which is counter-intuitive). Print the output, and reset (thus triggering "ResetRule" next time)
    */
    var outputRule = GKRule.FromPredicate (rules => true, rules => {
        System.Console.WriteLine(output == "" ? input.ToString() : output);
        reset = true;
    });
    outputRule.Salience = 3;

    var rs = new GKRuleSystem ();
    rs.AddRules (new [] {
        clearRule,
        fizzRule,
        buzzRule,
        outputRule
    });

    for (input = 1; input < 16; input++) {
        rs.Evaluate ();
        rs.Reset ();
    }
}

protected Func<GKRuleSystem, bool> mod(int m)
{
    Func<GKRuleSystem,bool> partiallyApplied = (rs) => input % m == 0;
    return partiallyApplied;
}
```

基于一组给定的规则 (`GKRule`) 和一组已知的输入，专家系统 (`GKRuleSystem`) 将创建可预测输出 (`fizzbuzz`为上述示例)。

### <a name="flocking"></a>以

以允许 AI 的组控制游戏表现为纷纷采用，其中组响应的动作和操作的航班的鸟纷纷采用或鱼徜徉学校等的潜在客户实体的实体。

以下代码片段的C#代码实现的图形显示使用 GameplayKit 和 SpriteKit 的以行为：

```csharp
using System;
using SpriteKit;
using CoreGraphics;
using UIKit;
using GameplayKit;
using Foundation;
using System.Collections.Generic;
using System.Linq;
using OpenTK;

namespace FieldBehaviorExplorer
{
    public static class FlockRandom
    {
        private static GKARC4RandomSource rand = new GKARC4RandomSource ();

        static FlockRandom ()
        {
            rand.DropValues (769);
        }

        public static float NextUniform ()
        {
            return rand.GetNextUniform ();
        }
    }

    public class FlockingScene : SKScene
    {
        List<Boid> boids = new List<Boid> ();
        GKComponentSystem componentSystem;
        GKAgent2D trackingAgent; //Tracks finger on screen
        double lastUpdateTime = Double.NaN;
        //Hold on to behavior so it doesn't get GC'ed
        static GKBehavior flockingBehavior;
        static GKGoal seekGoal;


        public FlockingScene (CGSize size) : base (size)
        {
            AddRandomBoids (20);

            var scale = 0.4f;
            //Flocking system
            componentSystem = new GKComponentSystem (typeof(GKAgent2D));
            var behavior = DefineFlockingBehavior (boids.Select (boid => boid.Agent).ToArray<GKAgent2D>(), scale);
            boids.ForEach (boid => {
                boid.Agent.Behavior = behavior;
                componentSystem.AddComponent(boid.Agent);
            });

            trackingAgent = new GKAgent2D ();
            trackingAgent.Position = new Vector2 ((float) size.Width / 2.0f, (float) size.Height / 2.0f);
            seekGoal = GKGoal.GetGoalToSeekAgent (trackingAgent);
        }

        public override void TouchesBegan (NSSet touches, UIEvent evt)
        {
            boids.ForEach(boid => boid.Agent.Behavior.SetWeight(1.0f, seekGoal));
        }

        public override void TouchesEnded (NSSet touches, UIEvent evt)
        {
            boids.ForEach (boid => boid.Agent.Behavior.SetWeight (0.0f, seekGoal));
        }

        public override void TouchesMoved (NSSet touches, UIEvent evt)
        {
            var touch = (UITouch) touches.First();
            var loc = touch.LocationInNode (this);
            trackingAgent.Position = new Vector2((float) loc.X, (float) loc.Y);
        }


        private void AddRandomBoids (int count)
        {
            var scale = 0.4f;
            for (var i = 0; i < count; i++) {
                var b = new Boid (UIColor.Red, this.Size, scale);
                boids.Add (b);
                this.AddChild (b);
            }
        }

        internal static GKBehavior DefineFlockingBehavior(GKAgent2D[] boidBrains, float scale)
        {
            if (flockingBehavior == null) {
                var flockingGoals = new GKGoal[3];
                flockingGoals [0] = GKGoal.GetGoalToSeparate (boidBrains, 100.0f * scale, (float)Math.PI * 8.0f);
                flockingGoals [1] = GKGoal.GetGoalToAlign (boidBrains, 40.0f * scale, (float)Math.PI * 8.0f);
                flockingGoals [2] = GKGoal.GetGoalToCohere (boidBrains, 40.0f * scale, (float)Math.PI * 8.0f);

                flockingBehavior = new GKBehavior ();
                flockingBehavior.SetWeight (25.0f, flockingGoals [0]);
                flockingBehavior.SetWeight (10.0f, flockingGoals [1]);
                flockingBehavior.SetWeight (10.0f, flockingGoals [2]);
            }
            return flockingBehavior;
        }

        public override void Update (double currentTime)
        {
            base.Update (currentTime);
            if (Double.IsNaN(lastUpdateTime)) {
                lastUpdateTime = currentTime;
            }
            var delta = currentTime - lastUpdateTime;
            componentSystem.Update (delta);
        }
    }

    public class Boid : SKNode, IGKAgentDelegate
    {
        public GKAgent2D Agent { get { return brains; } }
        public SKShapeNode Sprite { get { return sprite; } }

        class BoidSprite : SKShapeNode
        {
            public BoidSprite (UIColor color, float scale)
            {
                var rot = CGAffineTransform.MakeRotation((float) (Math.PI / 2.0f));
                var path = new CGPath ();
                path.MoveToPoint (rot, new CGPoint (10.0, 0.0));
                path.AddLineToPoint (rot, new CGPoint (0.0, 30.0));
                path.AddLineToPoint (rot, new CGPoint (10.0, 20.0));
                path.AddLineToPoint (rot, new CGPoint (20.0, 30.0));
                path.AddLineToPoint (rot, new CGPoint (10.0, 0.0));
                path.CloseSubpath ();

                this.SetScale (scale);
                this.Path = path;
                this.FillColor = color;
                this.StrokeColor = UIColor.White;

            }
        }

        private GKAgent2D brains;
        private BoidSprite sprite;
        private static int boidId = 0;

        public Boid (UIColor color, CGSize size, float scale)
        {
            brains = BoidBrains (size, scale);
            sprite = new BoidSprite (color, scale);
            sprite.Position = new CGPoint(brains.Position.X, brains.Position.Y);
            sprite.ZRotation = brains.Rotation;
            sprite.Name = boidId++.ToString ();

            brains.Delegate = this;

            this.AddChild (sprite);
        }

        private GKAgent2D BoidBrains(CGSize size, float scale)
        {
            var brains = new GKAgent2D ();
            var x = (float) (FlockRandom.NextUniform () * size.Width);
            var y = (float) (FlockRandom.NextUniform () * size.Height);
            brains.Position = new Vector2 (x, y);

            brains.Rotation = (float)(FlockRandom.NextUniform () * Math.PI * 2.0);
            brains.Radius = 30.0f * scale;
            brains.MaxSpeed = 0.5f;
            return brains;
        }

        [Export ("agentDidUpdate:")]
        public void AgentDidUpdate (GameplayKit.GKAgent agent)
        {
        }

        [Export ("agentWillUpdate:")]
        public void AgentWillUpdate (GameplayKit.GKAgent agent)
        {
            var brainsIn = (GKAgent2D) agent;
            sprite.Position = new CGPoint(brainsIn.Position.X, brainsIn.Position.Y);
            sprite.ZRotation = brainsIn.Rotation;
            Console.WriteLine ($"{sprite.Name} -> [{sprite.Position}], {sprite.ZRotation}");
        }
    }
}
```

接下来，在视图控制器中实现此场景：

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();
        // Perform any additional setup after loading the view, typically from a nib.
        this.View = new SKView {
        ShowsFPS = true,
        ShowsNodeCount = true,
        ShowsDrawCount = true
    };
}

public override void ViewWillLayoutSubviews ()
{
    base.ViewWillLayoutSubviews ();

    var v = (SKView)View;
    if (v.Scene == null) {
        var scene = new FlockingScene (View.Bounds.Size);
        scene.ScaleMode = SKSceneScaleMode.AspectFill;
        v.PresentScene (scene);
    }
}
```

运行时，一些动画 _"Boids"_ 将围绕我们手指点击 flock:

[![](images/flocking01.png "一些动画的 Boids 将 flock 围绕手指点击")](images/flocking01.png#lightbox)

### <a name="other-apple-examples"></a>其他 Apple 示例

除了上面介绍的示例，Apple 提供了以下示例应用可将转换为C#和 Xamarin.iOS:

- [FourInARow:使用适用于对手 AI GameplayKit Minmax 规划师](https://developer.apple.com/library/prerelease/ios/samplecode/FourInARow/Introduction/Intro.html#//apple_ref/doc/uid/TP40016142)
- [AgentsCatalog:使用中 GameplayKit 代理系统](https://developer.apple.com/library/prerelease/ios/samplecode/AgentsCatalog/Introduction/Intro.html#//apple_ref/doc/uid/TP40016141)
- [DemoBots:构建跨平台游戏，SpriteKit 和 GameplayKit](https://developer.apple.com/library/prerelease/ios/samplecode/DemoBots/Introduction/Intro.html#//apple_ref/doc/uid/TP40015179)

## <a name="metal"></a>Metal

在 iOS 9 中，Apple 对进行了几处更改和新增功能来提供对 GPU 的开销较低的访问配置文件。 使用配置文件可以最大化的图形和 iOS 应用的计算可能。

金属框架包括以下新功能：

- 新的私有和深度模具纹理中适用于 OS X。
- 改进了卷影的效果，而深度限制和单独前端和后模具值。
- 裸机明暗度的语言和金属标准库改进。
- 计算着色器支持广泛的像素格式。

### <a name="the-metalkit-framework"></a>MetalKit Framework

MetalKit 框架提供了一套实用工具类和减少的 iOS 应用中使用配置文件所需的功能。 MetalKit 提供了三个关键领域中的支持：

1. 从各种源包括 PNG、 JPEG、 KTX 和了解 PVR 等常见格式加载异步纹理。
2. 模型 I/O 的轻松访问基于金属特定模型处理的资产。 这些功能已高度优化，可提供高效的数据模型 I/O 网格和金属缓冲区之间的传输。
3. 预定义的金属视图和视图管理可极大减少显示的 iOS 应用中的图形呈现所需的代码量。

若要了解有关 MetalKit 的详细信息，请参阅 Apple [MetalKit 框架引用](https://developer.apple.com/library/prerelease/ios/documentation/MetalKit/Reference/MTKFrameworkReference/index.html#//apple_ref/doc/uid/TP40015356)，[金属编程指南](https://developer.apple.com/library/prerelease/ios/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221)，[金属框架引用](https://developer.apple.com/library/prerelease/ios/documentation/Metal/Reference/MetalFrameworkReference/index.html#//apple_ref/doc/uid/TP40014161)和[裸机明暗度语言指南](https://developer.apple.com/library/prerelease/ios/documentation/Metal/Reference/MetalShadingLanguageGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014364)。

### <a name="metal-performance-shaders-framework"></a>裸机性能着色器框架

裸机性能着色器框架提供了一组高度优化图形和计算基于在您配置文件中使用的着色器基于的 iOS 应用。 每个已专门优化框架在裸机上提供高性能的金属性能着色器中的着色器支持 iOS 的 Gpu。

您可以通过使用裸机性能着色器类，而无需为目标和维护单个代码库来获得在每个特定 iOS GPU 上可能的最高性能。 裸机性能着色器可以用于任何裸机纹理和缓冲区等资源。

裸机性能着色器 framework 提供了一组常见的着色器，例如：

- **高斯模糊**(`MPSImageGaussianBlur`)
- **Sobel 边缘检测**(`MPSImageSobel`)
- **图像直方图**(`MPSImageHistogram`)

有关详细信息，请参阅 Apple[金属明暗度语言指南](https://developer.apple.com/library/prerelease/ios/documentation/Metal/Reference/MetalShadingLanguageGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014364)。

## <a name="introducing-model-io"></a>引入模型 I/O

Apple 的模型 I/O 框架提供了深入了解三维资产 （如模型和及其相关的资源）。 模型 I/O 提供基于物理的材料，模型和可以用于 GameplayKit、 裸机和 SceneKit 的照明你的 iOS 游戏。

进行的 I/O 模型，可以支持下列类型的任务：

- 导入照明极差，材料，网格数据、 相机设置和各种常用的软件和游戏引擎格式从其他基于场景的信息。
- 处理或生成基于场景的信息，如： 分步创建纹理天空圆顶或制作照明到网格。
- 适用于 MetalKit、 SceneKit 和 GLKit，若要有效地将游戏资产加载到呈现的 GPU 缓冲区。
- 将基于场景的信息导出为各种常用的软件和游戏引擎格式。

若要了解有关模型 I/O 的详细信息，请参阅 Apple 的[模型 I/O 框架引用](https://developer.apple.com/library/prerelease/ios/documentation/ModelIO/Reference/ModelIO_Framework/index.html#//apple_ref/doc/uid/TP40015421)

## <a name="introducing-replaykit"></a>引入 ReplayKit

Apple 的新 ReplayKit 框架，可轻松地将玩游戏的录制添加到您的 iOS 游戏并允许用户快速轻松地编辑和共享在应用内的从该视频。

有关详细信息，请参阅 Apple [ReplayKit 和 Game Center 视频将社交](https://developer.apple.com/videos/wwdc/2015/?id=605)及其[DemoBots:构建跨平台游戏使用 SpriteKit 和 GameplayKit](https://developer.apple.com/library/prerelease/ios/samplecode/DemoBots/Introduction/Intro.html#//apple_ref/doc/uid/TP40015179)示例应用程序。

## <a name="scenekit"></a>SceneKit

场景工具包是一个三维场景图形 API，用于简化 3D 图形。 它在 OS X 10.8 中首次引入，并具有现在转到 iOS 8。 使用场景工具包创建沉浸式 3D 可视化效果和休闲 3D 游戏不需要 OpenGL 的专业知识。 基于常见场景图形的概念，Scene Kit 抽象化 OpenGL 和 OpenGL ES，需要很轻松地添加 3D 内容到应用程序的复杂性。 但是，如果你是 OpenGL 方面的专家，场景工具包具有关联直接使用 OpenGL 以及强大的支持。 它还包括许多功能进行补充的 3D 图形，例如物理引擎，并与几个其他 Apple 框架，例如核心动画、 Core 映像和 Sprite Kit 很好地集成。

有关详细信息，请参阅我们[SceneKit](~/ios/platform/gaming/scenekit.md)文档。

### <a name="scenekit-changes"></a>SceneKit 更改

Apple 已添加了以下新功能到 SceneKit 适用于 iOS 9:

- Xcode 现在提供了一个场景编辑器，可用于快速构建游戏和交互式 3D 应用程序，通过编辑 Xcode 内的直接从场景。
- `SCNView`和`SCNSceneRenderer`类可用于启用 （在支持的 iOS 设备） 的裸机呈现。
- `SCNAudioPlayer`和`SCNNode`类可用于添加到 iOS 应用程序中自动跟踪玩家位置的空间音频效果。

有关详细信息，请参阅我们[SceneKit 文档](~/ios/platform/introduction-to-ios8.md#scenekit)和 Apple [SceneKit 框架引用](https://developer.apple.com/library/prerelease/ios/documentation/SceneKit/Reference/SceneKit_Framework/index.html#//apple_ref/doc/uid/TP40012283)和[Fox:构建 SceneKit 游戏使用 Xcode 场景编辑器](https://developer.apple.com/library/prerelease/ios/samplecode/Fox/Introduction/Intro.html#//apple_ref/doc/uid/TP40016154)示例项目。

## <a name="spritekit"></a>SpriteKit

Sprite Kit，Apple 的 2D 游戏框架，已在 iOS 8 和 OS X Yosemite 一些有趣的新功能。 其中包括与 Scene Kit、 着色器的支持、 照明、 阴影、 约束、 法线贴图生成和物理引擎增强功能的集成。 具体而言，新的物理功能使其很容易将逼真的效果添加到游戏。

有关详细信息，请参阅我们[SpriteKit](~/ios/platform/gaming/spritekit.md)文档。

### <a name="spritekit-changes"></a>SpriteKit 的更改

Apple 已添加了以下新功能到 SpriteKit 适用于 iOS 9:

- 自动跟踪玩家的位置使用的空间音频效果`SKAudioNode`类。
- Xcode 目前精选场景编辑器和简单的 2D 游戏和应用程序创建的操作编辑器。
- 轻松滚动游戏的照相机的新节点的支持 (`SKCameraNode`) 对象。
- 上支持裸机的 iOS 设备，SpriteKit 将自动使用它进行呈现，即使你已在使用自定义的 OpenGL ES 着色器。

有关详细信息，请参阅我们[SpriteKit 文档](~/ios/platform/introduction-to-ios8.md#spritekit)Apple [SpriteKit 框架引用](https://developer.apple.com/library/prerelease/ios/documentation/SpriteKit/Reference/SpriteKitFramework_Ref/index.html#//apple_ref/doc/uid/TP40013041)及其[DemoBots:构建跨平台游戏使用 SpriteKit 和 GameplayKit](https://developer.apple.com/library/prerelease/ios/samplecode/DemoBots/Introduction/Intro.html#//apple_ref/doc/uid/TP40015179)示例应用程序。

## <a name="summary"></a>总结

本文只讨论了新游戏功能，该 iOS 9 提供适用于 Xamarin.iOS 应用。
它引入了 GameplayKit 和模型 I/O;向的金属; 主要的增强功能和 SceneKit 和 SpriteKit 的新功能。



## <a name="related-links"></a>相关链接

- [iOS 9 示例](https://developer.xamarin.com/samples/ios/iOS9/)
- [面向开发人员的 iOS 9](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
