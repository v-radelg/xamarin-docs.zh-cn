---
title: 大致了解 UrhoSharp
description: 本文档介绍 UrhoSharp 应用程序和指向各种指南和演示如何使用 UrhoSharp 示例应用程序的基本结构。
ms.prod: xamarin
ms.assetid: 18041443-5093-4AF7-8B20-03E00478EF35
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: a3e14ebca961e828fc578035adaca5ba2a809438
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61288505"
---
# <a name="an-introduction-to-urhosharp"></a>大致了解 UrhoSharp

![UrhoSharp 徽标](introduction-images/urhosharp-icon.png)

UrhoSharp 是 Xamarin 和.NET 开发人员的强大 3D 游戏引擎。  它与 Apple 的 SceneKit 和 SpriteKit 的精神相似，包括物理，导航、 网络和很多更时仍在跨平台。

它是绑定到的.NET [Urho3D](http://urho3d.github.io/)引擎并使开发人员可以编写跨平台代码可面向 Android、 iOS、 Windows 和 Mac 具有相同基本代码，并可以呈现为 OpenGL 和 Direct3D 系统。

UrhoSharp 是包含大量的现成功能的游戏引擎：

- 功能强大的 3D 图形绘制
- [物理模拟](https://developer.xamarin.com/api/namespace/Urho.Physics/)（使用项目符号库）
- [场景处理](https://developer.xamarin.com/api/type/Urho.Scene/)
- 等待异步支持
- [友好操作 API](https://developer.xamarin.com/api/namespace/Urho.Actions/)
- [与 3D 场景的 2D 集成](https://developer.xamarin.com/api/namespace/Urho.Urho2D/)
- [使用 FreeType 字体呈现](https://developer.xamarin.com/api/type/Urho.Gui.FontFaceFreeType/)
- [客户端和服务器网络功能](https://developer.xamarin.com/api/namespace/Urho.Network/)
- [导入范围广泛的资产](https://developer.xamarin.com/api/namespace/Urho.Resources/)（与打开资产库）
- [导航网格和 pathfinding](https://developer.xamarin.com/api/namespace/Urho.Navigation/) （使用重新强制转换/Detour）
- [碰撞检测的凸包生成](https://developer.xamarin.com/api/type/Urho.Physics.CollisionShape/)（使用 StanHull）
- [音频播放](https://developer.xamarin.com/api/namespace/Urho.Audio/)(使用**libvorbis**)

## <a name="getting-started"></a>入门

作为方便地分发 UrhoSharp [NuGet 包](https://www.nuget.org/)，它可以被添加到在C#或F#面向 Windows、 Mac、 Android 或 iOS 的项目。  NuGet 附带了运行应用程序所需的库以及由引擎使用的基本资产 (CoreData)。

### <a name="urho-as-a-portable-class-library"></a>作为可移植类库 Urho

从特定于平台的项目，或从可移植类库项目，让你重复所有代码使用跨所有平台，可以使用 Urho 包。  这意味着将只需在每个平台上将编写平台特定的入口点，然后将控件传输到您共享的游戏代码。

### <a name="samples"></a>示例

可以通过在 Visual Studio for Mac 或 Visual Studio 中打开示例解决方案从获取 Urho 功能尝试：

[https://github.com/xamarin/urho-samples](https://github.com/xamarin/urho-samples)

默认解决方案包含项目的 Android、 iOS、 Windows 和 mac  我们已经构建了该解决方案，以便我们有极小的平台特定启动程序，并位于可移植类库的所有示例代码和游戏代码演示如何在所有平台上最大化代码重用。

请查阅[Urho 和你的平台](~/graphics-games/urhosharp/platform/index.md)页面，详细了解如何创建您自己的解决方案。

因为共享一组通用的用户界面元素的所有示例，这些示例具有提取此文件中的基本设置：

[https://github.com/xamarin/urho-samples/blob/master/FeatureSamples/Core/Sample.cs](https://github.com/xamarin/urho-samples/blob/master/FeatureSamples/Core/Sample.cs)

这提供了处理一些基本的击键示例基类和触摸事件，设置照相机，提供了基本用户界面元素，这样每个示例可以专注于进行展示的特定功能。

下面的示例显示了此引擎是支持的功能：

- [Samply 游戏](https://github.com/xamarin/urho-samples/tree/master/SamplyGame)ShootySkies 的简单克隆。

而其他示例显示每个示例的各个属性。

## <a name="basic-structure"></a>基本结构

您的游戏应子类 [`Application`](https://developer.xamarin.com/api/type/Urho.Application/)
类，这是将在此安装程序您的游戏 (在[ `Setup` ](https://developer.xamarin.com/api/member/Urho.Application.Setup/)方法) 并启动您的游戏 (在[ `Start` ](https://developer.xamarin.com/api/member/Urho.Application.Start)方法)。  然后构建您的主用户界面。  我们将引导完成小示例展示了如何设置三维场景中，一些 UI 元素和将简单行为附加到它的 Api。

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

如果运行此应用程序，您很快就会发现投诉运行时你的资产不存在。  您需要执行操作是启动具有特殊目录名称"数据"，在项目中创建层次结构，并会在此，将在应用程序中引用的资产。  然后，您必须设置在每个资产"复制到输出目录"到"复制如果较新"的项属性，这将确保你的数据是否有。

让我们解释这怎么回事此处。

若要启动应用程序，调用引擎初始化函数，接着创建应用程序类，此类的新实例：

```csharp
new MySample().Run();
```

在运行时将调用`Setup`和`Start`为您的方法。  如果重写`Setup`可以配置引擎参数 （不显示在此示例中）。

必须重写`Start`因为这将启动您的游戏。  在这种方法将加载你的资产、 连接事件处理程序、 设置您的场景并启动所需的任何操作。  在本示例中，我们都创建很少的 UI，可显示给用户，以及设置一个三维场景。

以下代码段使用 UI 框架来创建一个文本元素并将其添加到你的应用程序：

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

UI 框架，提供了非常简单的游戏用户界面，和它的工作方式添加到新节点[ `UI.Root` ](https://developer.xamarin.com/api/property/Urho.Gui.UI.Root/)节点。

我们的示例安装的第二部分主要场景。  这涉及到多个步骤，创建一个的三维场景，在屏幕中，创建一个三维框添加光、 照相机和视区。  部分中的更详细地探讨了这些[场景、 节点、 组件和相机](~/graphics-games/urhosharp/using.md#scenenodescomponentsandcameras)。

在本例中的第三个部分将触发执行的一些操作。  操作是配方，描述特定的效果，并创建后，它们可以通过调用执行按需节点[ `RunActionAsync` ](https://developer.xamarin.com/api/member/Urho.Node.RunActionsAsync)方法`Node`。

第一个操作缩放弹跳效果包装盒和第二个旋转框不限次数：

```csharp
await boxNode.RunActionsAsync(
    new EaseBounceOut(new ScaleTo(duration: 1f, scale: 1)));
```

上图显示的我们创建的第一个操作是如何[ `ScaleTo` ](https://developer.xamarin.com/api/type/Urho.Actions.ScaleTo/)操作，这将是仅仅是食谱，指示你想要缩放为 1 的值针对第二个节点的位数属性。  此操作反过来包装在一个缓动操作[ `EaseBounceOut` ](https://developer.xamarin.com/api/type/Urho.Actions.EaseBounceInOut/)操作。  缓动操作扭曲线性模式执行的操作和应用效果，在这种情况下，它提供了向外弹跳效果。
因此，我们的方案也编写为：

```csharp
var recipe = new EaseBounceOut(new ScaleTo(duration: 1f, scale: 1));
```

一旦创建方案后，我们将执行该方案：

```csharp
await boxNode.RunActionsAsync (recipe)
```

等待指示将想要在操作完成后恢复执行此行的后面。  操作完成后我们将触发第二个动画。

[使用 UrhoSharp](~/graphics-games/urhosharp/using.md)文档探讨更深入地 Urho 以及如何构建您的代码来构建游戏背后的概念。

## <a name="copyrights"></a>版权

本文档包含从 Xamarin Inc 的原始内容，但从 Urho3D 项目的开放源代码文档广泛地绘制，包含 Cocos2D 项目中的屏幕快照。

