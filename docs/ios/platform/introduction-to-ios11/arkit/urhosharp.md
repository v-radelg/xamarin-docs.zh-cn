---
title: 在 Xamarin 中将 ARKit 与 UrhoSharp 配合使用
description: 本文档介绍如何在 Xamarin 中设置 ARKit 应用程序，然后查看如何呈现帧，如何调整照相机，如何检测平面，如何使用光照等。 它还讨论了用于 HoloLens 的 UrhoSharp 和编写代码。
ms.prod: xamarin
ms.assetid: 877AF974-CC2E-48A2-8E1A-0EF9ABF2C92D
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 08/01/2017
ms.openlocfilehash: 7f53108460c4e0799ab6c4078d8bb26788b0bf6e
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70752549"
---
# <a name="using-arkit-with-urhosharp-in-xamarinios"></a>在 Xamarin 中将 ARKit 与 UrhoSharp 配合使用

随着[ARKit](https://developer.apple.com/arkit/)的引入，Apple 使开发人员能够更轻松地创建更扩充的现实应用程序。 ARKit 可以跟踪设备的确切位置并检测世界上的各种表面，然后由开发人员将 ARKit 中的数据混合到代码中。

[UrhoSharp](~/graphics-games/urhosharp/index.md)提供了一个全面且易于使用的 3d API，可用于创建3d 应用程序。   这两种方法都可以组合在一起，ARKit 来提供有关世界的物理信息，并 Urho 呈现结果。

本页介绍如何将这两个世界连接在一起，以创建强大的更高的现实应用程序。

## <a name="the-basics"></a>基本知识

我们要做的就是将三维内容呈现在世界上，如 iPhone/iPad 所见。   其思路是将来自设备照相机的内容与3D 内容混合在一起，而设备的用户在房间内移动以确保3D 对象的行为与它们在房间中的行为一样-这是通过将对象锚定到这一领域来完成的。

![ARKit 中的动画图形](urhosharp-images/image1.gif)

我们将使用 Urho 库来加载我们的3D 资产，并将其放在世界上，我们将使用 ARKit 来获取来自照相机的视频流，以及手机在世界中的位置。   当用户通过手机移动时，我们将使用该位置中的更改来更新 Urho 引擎正在显示的坐标系统。

这样一来，当您将一个对象放在三维空间中并移动用户时，三维对象的位置就会反映其放置位置和位置。

## <a name="setting-up-your-application"></a>设置应用程序

### <a name="ios-application-launch"></a>iOS 应用程序启动

你的 iOS 应用程序需要创建和启动你的3d 内容，你可以通过创建实现的子类`Urho.Application`并通过`Start`重写方法来提供安装代码来实现此目的。  这是在其中填充了数据的场景，事件处理程序是设置等。

我们引入了一个`Urho.ArkitApp`类，子类`Urho.Application`及其`Start`方法执行繁重的工作。   你需要对现有的 Urho 应用程序进行的所有操作都是将基类更改为类型`Urho.ArkitApp` ，并且你的应用程序将在世界上运行 Urho 场景。

### <a name="the-arkitapp-class"></a>ArkitApp 类

此类提供一组方便的默认值，这两种默认情况下，场景都包含一些关键对象，以及由操作系统传递的 ARKit 事件的处理。

此设置在`Start`虚拟方法中进行。   当你对子类重写此方法时，需要确保使用`base.Start()`你自己的实现与你的父项链接。

方法`Start`设置场景、视区、照相机和方向光，并将其作为公共属性的表面显示：

- 用于`Scene`保存对象的。
- 带有阴影`Light`的方向，其位置`LightNode`通过属性提供
- 一个`Camera` ，其组件在 ARKit 将更新传递到应用程序时更新，
- 显示`ViewPort`结果的。

### <a name="your-code"></a>你的代码

然后，需要为`ArkitApp`类划分子类并重`Start`写方法。   你的方法应执行的第一件事是`ArkitApp.Start`通过调用`base.Start()`链接到。  然后，可以使用 ArkitApp 设置的任何属性将对象添加到场景中，自定义要处理的光源、阴影或事件。

ARKit/UrhoSharp 示例使用纹理加载动画字符并播放动画，并提供以下实现：

```csharp
public class MutantDemo : ArkitApp
{
    [Preserve]
    public MutantDemo(ApplicationOptions opts) : base(opts) { }

    Node mutantNode;

    protected override void Start()
    {
        base.Start ();

        // Mutant
        mutantNode = Scene.CreateChild();
        mutantNode.Rotation = new Quaternion(x: 0, y:15, z:0);
        mutantNode.Position = new Vector3(0, -1f, 2f); /*two meters away*/
        mutantNode.SetScale(0.5f);

        var mutant = mutantNode.CreateComponent<AnimatedModel>();
        mutant.Model = ResourceCache.GetModel("Models/Mutant.mdl");
        mutant.Material = ResourceCache.GetMaterial("Materials/mutant_M.xml");

        var animation = mutantNode.CreateComponent<AnimationController>();
        animation.Play("Animations/Mutant_HipHop1.ani", 0, true, 0.2f);
    }
}
```

这就是您在这个时候必须执行的所有操作，使您的3D 内容在增加的现实中显示出来。

Urho 对三维模型和动画使用自定义格式，因此需要将资产导出为此格式。   可以使用[Urho3D Blender 外接程序](https://github.com/reattiva/Urho3D-Blender)和[UrhoAssetImporter](https://github.com/EgorBo/UrhoAssetImporter)之类的工具，这些工具可将这些资产从 .DBX、DAE、OBJ、Blend、三维最大格式转换为 Urho 所需的格式。

若要了解有关使用 Urho 创建3D 应用程序的详细信息，请参阅[UrhoSharp 指南简介](~/graphics-games/urhosharp/introduction.md)。

## <a name="arkitapp-in-depth"></a>深度 ArkitApp

> [!NOTE]
> 本部分适用于想要自定义 UrhoSharp 和 ARKit 的默认体验的开发人员，或想要深入了解集成的工作方式。   不需要阅读此部分。

ARKit API 非常简单，你可以创建和配置[ARSession](https://developer.apple.com/documentation/arkit/arsession)对象，该对象随后会开始提供[ARFrame](https://developer.apple.com/documentation/arkit/arframe)对象。   其中包括相机捕获的图像，以及设备的预计实际位置。

我们将使用我们的3D 内容将相机提供的图像组合到一起，并调整 UrhoSharp 中的相机，使其与设备位置和位置的可能性相匹配。

下图显示了`ArkitApp`类中发生的情况：

[![ArkitApp 中的类和屏幕的关系图](urhosharp-images/image2.png)](urhosharp-images/image2.png#lightbox)

### <a name="rendering-the-frames"></a>呈现框架

这种思路很简单，只需要将相机中的视频与三维图形组合在一起即可生成合并的图像。     我们将按顺序获取这些捕获的映像，并将此输入与 Urho 场景混合使用。

要执行此操作，最简单的方法是`RenderPathCommand`将插入到`RenderPath`主。  这是一组用于绘制单个帧的命令。  此命令将用传递给它的任何纹理填充视区。    我们在第一帧上进行了此设置，实际定义是在此时加载的第一个**ARRenderPath**文件中完成的。

但是，我们面临着两个问题将这两个世界混合在一起：

1. 在 iOS 上，GPU 纹理的分辨率必须是2的幂，但我们将从照相机获取的帧的分辨率不是2的幂，例如：1280x720.
2. 帧以[YUV](https://en.wikipedia.org/wiki/YUV)格式进行编码，用两个图像亮度和色度表示。

YUV 帧采用两种不同的分辨率。  表示亮度的1280x720 图像（基本上为灰色缩放图像）和色度组件的更小640x360：

![演示组合 Y 和 UV 组件的图像](urhosharp-images/image3.png)

若要使用 OpenGL 来绘制完全彩色图像，必须编写一个小型着色器，使其从纹理槽获取亮度（Y 分量）和色度（UV 平面）。  在 UrhoSharp 中，它们具有名称 "sDiffMap" 和 "sNormalMap"，并将其转换为 RGB 格式：

```csharp
mat4 ycbcrToRGBTransform = mat4(
    vec4(+1.0000, +1.0000, +1.0000, +0.0000),
    vec4(+0.0000, -0.3441, +1.7720, +0.0000),
    vec4(+1.4020, -0.7141, +0.0000, +0.0000),
    vec4(-0.7010, +0.5291, -0.8860, +1.0000));

vec4 ycbcr = vec4(texture2D(sDiffMap, vTexCoord).r,
                    texture2D(sNormalMap, vTexCoord).ra, 1.0);
gl_FragColor = ycbcrToRGBTransform * ycbcr;
```

若要呈现没有两个分辨率幂的纹理，必须使用以下参数定义 Texture2D：

```chsarp
// texture for UV-plane;
cameraUVtexture = new Texture2D();
cameraUVtexture.SetNumLevels(1);
cameraUVtexture.SetSize(640, 360, Graphics.LuminanceAlphaFormat, TextureUsage.Dynamic);
cameraUVtexture.FilterMode = TextureFilterMode.Bilinear;
cameraUVtexture.SetAddressMode(TextureCoordinate.U, TextureAddressMode.Clamp);
cameraUVtexture.SetAddressMode(TextureCoordinate.V, TextureAddressMode.Clamp);
```

因此，我们可以将捕获的图像呈现为背景，并在其上方呈现任何场景，就像这种突变的变化一样。

### <a name="adjusting-the-camera"></a>调整照相机

这些`ARFrame`对象还包含估计的设备位置。  现在，我们需要移动游戏摄像机 ARFrame-在 ARKit 之前，跟踪设备方向（滚动、螺距和偏航）并在视频上渲染固定的全息影像并不是一个大的

出现这种情况的原因是，内置传感器（如陀螺仪）无法跟踪移动，只能加速。  ARKit 分析每个帧并提取要跟踪的功能点，从而能够为我们生成包含移动和旋转数据的准确的转换矩阵。

例如，可以通过以下方式获取当前位置：

```csharp
var row = arCamera.Transform.Row3;
CameraNode.Position = new Vector3(row.X, row.Y, -row.Z);
```

我们使用`-row.Z`的是因为 ARKit 使用右手坐标系。

### <a name="plane-detection"></a>平面检测

ARKit 能够检测到水平平面，此功能使您可以与现实世界交互，例如，我们可以将变化置于真实表或楼层上。 要执行此操作，最简单的方法是使用 System.windows.media.visualtreehelper.hittest 方法（raycasting）。 它将屏幕坐标（0.5; 0.5 是中心）转换为实际坐标（0; 0; 0）是第一个帧的位置）。

```chsarp
protected Vector3? HitTest(float screenX = 0.5f, float screenY = 0.5f)
{
    var result = ARSession.CurrentFrame.HitTest(new CGPoint(screenX, screenY),
        ARHitTestResultType.ExistingPlaneUsingExtent)?.FirstOrDefault();
    if (result != null)
    {
        var row = result.WorldTransform.Row3;
        return new Vector3(row.X, row.Y, -row.Z);
    }
    return null;
}
```

现在，我们可以根据设备屏幕上点击的位置，将变化置于水平表面上：

```chsarp
void OnTouchEnd(TouchEndEventArgs e)
{
    float x = e.X / (float)Graphics.Width;
    float y = e.Y / (float)Graphics.Height;
    var pos = HitTest(x, y);
    if (pos != null)
    mutantNode.Position = pos.Value;
}
```

![动画图将平面更改为视图移动](urhosharp-images/image4.gif)

### <a name="realistic-lighting"></a>真实照明

根据真实的照明条件，虚拟场景应较浅或更暗，以更好地匹配其周围的环境。 ARFrame 包含一个 LightEstimate 属性，可用于调整 Urho 环境光源，此操作如下所示：

```csharp
var ambientIntensity = (float) frame.LightEstimate.AmbientIntensity / 1000f;
var zone = Scene.GetComponent<Zone>();
zone.AmbientColor = Color.White * ambientIntensity;
```

### <a name="beyond-ios---hololens"></a>IOS 以外-HoloLens

UrhoSharp[在所有主要的操作系统上运行](~/graphics-games/urhosharp/platform/index.md)，因此你可以在其他位置重复使用现有代码。

HoloLens 是运行它的最令人兴奋的平台之一。   这意味着你可以轻松地在 iOS 和 HoloLens 之间切换，以使用 UrhoSharp 生成令人惊叹的更好的现实应用程序。

可以在[github.com/EgorBo/ARKitXamarinDemo](https://github.com/EgorBo/ARKitXamarinDemo)中找到 MutantDemo 源。

## <a name="related-links"></a>相关链接

- [UrhoSharp](~/graphics-games/urhosharp/index.md)
- [ARKitXamarinDemo （带 UrhoSharp）（示例）](https://github.com/EgorBo/ARKitXamarinDemo)
