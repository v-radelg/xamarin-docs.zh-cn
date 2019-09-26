---
title: UrhoSharp 简介
description: 本文档介绍 UrhoSharp 应用程序的基本结构，并链接到演示如何使用 UrhoSharp 的各种指南和示例应用程序。
ms.prod: xamarin
ms.assetid: 18041443-5093-4AF7-8B20-03E00478EF35
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: 441a3cc19b4246fb2bdea54508142a894af5c051
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "67832548"
---
# <a name="introduction-to-urhosharp"></a>UrhoSharp 简介

![UrhoSharp 徽标](introduction-images/urhosharp-icon.png)

UrhoSharp 是一个功能强大的3D 游戏引擎，适用于 Xamarin 和 .NET 开发人员。  它在本质上与 Apple 的 SceneKit 和 SpriteKit 相似，并包括物理学、导航、网络等，同时仍然跨平台。

它是一个到[Urho3D](http://urho3d.github.io/)引擎的 .net 绑定，允许开发人员编写跨平台代码，该代码可面向具有相同基本代码的 Android、IOS、Windows 和 Mac，并可呈现给 OpenGL 和 Direct3D 系统。

UrhoSharp 是一种游戏引擎，具有现成的许多功能：

- 强大的三维图形呈现
- 物理学模拟（使用项目符号库）
- 场景处理
- Await/Async 支持
- 友好操作 API
- 二维集成到3D 场景
- 用 FreeType 呈现字体
- 客户端和服务器网络功能
- 导入各种资产（使用开放资产库）
- 导航网格和 pathfinding （使用重塑/Detour）
- 用于冲突检测的凸形凸形（使用 StanHull）
- 音频播放（with **libvorbis**）

## <a name="get-started"></a>入门

UrhoSharp 可以方便地以[NuGet 包](https://www.nuget.org/)的形式分发，并且可以添加到C#面向F# Windows、Mac、Android 或 iOS 的或项目。  NuGet 附带了运行程序所需的库，以及引擎使用的基本资产（CoreData）。

### <a name="urho-as-a-portable-class-library"></a>作为可移植类库的 Urho

可以从特定于平台的项目或可移植类库项目使用 Urho 包，从而可在所有平台上重复使用所有代码。  这意味着，你只需在每个平台上编写特定于平台的入口点，然后将控制转移到共享游戏代码。

### <a name="samples"></a>示例

可以通过在 Visual Studio for Mac 或 Visual Studio 中打开示例解决方案中的，来了解 Urho 的功能：

[https://github.com/xamarin/urho-samples](https://github.com/xamarin/urho-samples)

默认解决方案包含适用于 Android、iOS、Windows 和 Mac 的项目。  我们已将该解决方案结构化，以便我们有一个小型平台特定的启动器，所有示例代码和游戏代码都在一个可移植类库中，说明如何在所有平台上最大程度地重复使用代码。

有关如何创建自己的解决方案的详细信息，请参阅 " [Urho" 和 "平台](~/graphics-games/urhosharp/platform/index.md)" 页。

由于所有示例均共享一组公共的用户界面元素，因此示例已在此文件中对基本设置进行了抽象：

[https://github.com/xamarin/urho-samples/blob/master/FeatureSamples/Core/Sample.cs](https://github.com/xamarin/urho-samples/blob/master/FeatureSamples/Core/Sample.cs)

这提供了一个示例基类，用于处理一些基本的击键和触控事件、设置照相机、提供基本的用户界面元素，并使每个示例都专注于正在展示的特定功能。

下面的示例演示了引擎的功能：

- [Samply 游戏](https://github.com/xamarin/urho-samples/tree/master/SamplyGame)是 ShootySkies 的简单克隆。

其他示例显示每个示例的各属性。

## <a name="basic-structure"></a>基本结构

你的`Application`游戏应为类设置子类，这是你将在其中设置游戏（ `Setup`在方法上）和启动游戏（在`Start`方法中）的位置。  然后构造您的主用户界面。  我们将演练一个小示例，其中显示了用于设置三维场景的 Api、一些 UI 元素，并为其附加了简单的行为。

```csharp
class MySample : Application {
    protected override void Start ()
    {
        CreateScene ();
        Input.KeyDown += (args) => {
            if (args.Key == Key.Esc) Exit ();
        };
    }

    async void CreateScene()
    {
        // UI text
        var helloText = new Text()
        {
            Value = "Hello World from MySample",
            HorizontalAlignment = HorizontalAlignment.Center,
            VerticalAlignment = VerticalAlignment.Center
        };
        helloText.SetColor(new Color(0f, 1f, 1f));
        helloText.SetFont(
            font: ResourceCache.GetFont("Fonts/Font.ttf"),
            size: 30);
        UI.Root.AddChild(helloText);

        // Create a top-level scene, must add the Octree
        // to visualize any 3D content.
        var scene = new Scene();
        scene.CreateComponent<Octree>();
        // Box
        Node boxNode = scene.CreateChild();
        boxNode.Position = new Vector3(0, 0, 5);
        boxNode.Rotation = new Quaternion(60, 0, 30);
        boxNode.SetScale(0f);
        StaticModel modelObject = boxNode.CreateComponent<StaticModel>();
        modelObject.Model = ResourceCache.GetModel("Models/Box.mdl");
        // Light
        Node lightNode = scene.CreateChild(name: "light");
        lightNode.SetDirection(new Vector3(0.6f, -1.0f, 0.8f));
        lightNode.CreateComponent<Light>();
        // Camera
        Node cameraNode = scene.CreateChild(name: "camera");
        Camera camera = cameraNode.CreateComponent<Camera>();
        // Viewport
        Renderer.SetViewport(0, new Viewport(scene, camera, null));
        // Perform some actions
        await boxNode.RunActionsAsync(
            new EaseBounceOut(new ScaleTo(duration: 1f, scale: 1)));
        await boxNode.RunActionsAsync(
            new RepeatForever(new RotateBy(duration: 1,
                deltaAngleX: 90, deltaAngleY: 0, deltaAngleZ: 0)));
     }
}
```

如果你运行此应用程序，你将很快发现，运行时不会抱怨你的资产。  你需要做的就是在项目中创建一个以特殊的目录名称 "数据" 开头的层次结构，然后将引用的资产放在你的程序中。  然后，必须将 "复制到输出目录" 每个资产的 "项属性" 设置为 "如果较新则复制"，这将确保数据存在。

让我们解释一下这里的内容。

若要启动应用程序，请调用引擎初始化函数，然后创建应用程序类的新实例，如下所示：

```csharp
new MySample().Run();
```

运行时将调用`Setup`和`Start`方法。  如果重写`Setup` ，则可以配置引擎参数（本示例中不显示）。

您必须重`Start`写，因为这将启动您的游戏。  在此方法中，您将加载您的资产，连接事件处理程序，设置您的场景并启动所需的任何操作。  在我们的示例中，我们创建了一些 UI 来向用户显示，并设置了三维场景。

下面的代码段使用 UI 框架来创建文本元素，并将其添加到你的应用程序：

```csharp
// UI text
var helloText = new Text()
{
    Value = "Hello World from UrhoSharp",
    HorizontalAlignment = HorizontalAlignment.Center,
    VerticalAlignment = VerticalAlignment.Center
};
helloText.SetColor(new Color(0f, 1f, 1f));
helloText.SetFont(
    font: ResourceCache.GetFont("Fonts/Font.ttf"),
    size: 30);
UI.Root.AddChild(helloText);
```

UI 框架提供了一个非常简单的游戏内用户界面，它可以通过向`UI.Root`节点添加新节点来运行。

示例的第二部分设置主场景。  这涉及多个步骤：创建三维场景、在屏幕中创建三维框、添加光、照相机和视区。  [主题、节点、组件和照相机](~/graphics-games/urhosharp/using.md#scenenodescomponentsandcameras)部分更详细地探讨了这些内容。

本示例的第三部分将触发几个操作。  操作是说明特定效果的食谱，一旦创建，就可以通过在`RunActionAsync` `Node`上调用方法，按需通过节点执行。

第一个操作用弹跳效果缩放框，第二个操作将始终旋转框：

```csharp
await boxNode.RunActionsAsync(
    new EaseBounceOut(new ScaleTo(duration: 1f, scale: 1)));
```

上面显示了我们创建的第一个操作是一个`ScaleTo`操作，这只是一个食谱，指示你要将缩放到一个节点的 scale 属性值。  此操作反过来会绕缓动操作（ `EaseBounceOut`操作）括起来。  缓动操作使操作的线性执行扭曲，应用效果，在这种情况下，它将提供弹跳效果。
所以，我们的食谱可以编写为：

```csharp
var recipe = new EaseBounceOut(new ScaleTo(duration: 1f, scale: 1));
```

创建食谱后，请执行食谱：

```csharp
await boxNode.RunActionsAsync (recipe)
```

Await 指示在操作完成后，将需要在此行之后恢复执行。  操作完成后，将触发第二个动画。

[使用 UrhoSharp](~/graphics-games/urhosharp/using.md)文档更深入地探讨了 Urho 背后的概念以及如何构建代码来构建游戏。

## <a name="copyrights"></a>版权

此文档包含来自 Xamarin Inc. 的原始内容，但会广泛地从 Urho3D 项目的开源文档中进行绘制，并包含 Cocos2d 为后盾项目的屏幕截图。
