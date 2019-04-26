---
title: ARKit 使用 Xamarin.iOS 中 UrhoSharp
description: 本文档介绍如何设置 ARKit 应用在 Xamarin.iOS 中，然后查看帧的呈现方式，如何调整相机，如何检测平面，如何使用照明和的详细信息。 它还讨论 UrhoSharp 以及为 HoloLens 编写代码。
ms.prod: xamarin
ms.assetid: 877AF974-CC2E-48A2-8E1A-0EF9ABF2C92D
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 08/01/2017
ms.openlocfilehash: b02ecc8a40f6ff8a1862d50202439d369003a53d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61262381"
---
# <a name="using-arkit-with-urhosharp-in-xamarinios"></a>ARKit 使用 Xamarin.iOS 中 UrhoSharp

通过引入[ARKit](https://developer.apple.com/arkit/)，Apple 已进行简单开发人员能够创建增强的现实的应用程序。 ARKit 可以跟踪你的设备的确切位置并检测对世界范围的各个图面，这样，它将由开发人员的 blend 数据来自 ARKit 到你的代码。

[UrhoSharp](~/graphics-games/urhosharp/index.md)提供一个全面且易于使用的三维 API，可用于创建三维应用程序。   这两种可以是混合在一起，ARKit 提供物理世界中，信息和 Urho 呈现结果。

此页说明如何连接在一起以创建绝佳的增强的现实应用程序这两个领域。


## <a name="the-basics"></a>基本知识

我们要做并显示三维内容之上世界的 iPhone/iPad 所示。   我们的想法是 blend 来自设备的照相机，摄像机带有三维内容、 内容和用户的设备在将移动的房间内，确保它们是属于该房间像三维对象-这是由到此世界上定位对象。

![在 ARKit 动画的图](urhosharp-images/image1.gif)


我们将使用 Urho 库加载我们的三维资产并将它们放在世界上，我们将使用 ARKit 来获取来自照相机，以及手机在世界上的位置的视频流。   当用户移动他 phone 时，我们将使用的位置所做的更改来更新显示 Urho 引擎坐标系统。

这样一来，在将对象放置在 3D 空间并用户移动时，三维对象的位置反映的位置和放置位置的位置。

## <a name="setting-up-your-application"></a>设置应用程序

### <a name="ios-application-launch"></a>iOS 应用程序启动

在 iOS 应用程序需要创建并启动 3D 内容，执行此操作通过创建一个实现的子类[ `Urho.Application` ](https://developer.xamarin.com/api/type/Urho.Application/) ，并提供你的安装程序代码通过重写`Start`方法。  这是您的场景获取其中填充了数据，事件处理程序是安装程序，等等。

我们引入了`Urho.ArkitApp`类的子类`Urho.Application`并在其`Start`方法执行繁重的任务。   您需要为应用程序是在现有 Urho 做更改的类型的基类`Urho.ArkitApp`，并且必须将世界上运行你 urho 场景的应用程序。

### <a name="the-arkitapp-class"></a>ArkitApp 类

此类提供一组方便默认值，这两个场景的一些关键对象以及 ARKit 事件的处理由操作系统在传递。

安装程序会发生`Start`虚方法。   当在子类重写此方法时，您需要使用请确保链接到你的家长`base.Start()`上您自己的实现。

`Start`方法设置场景、 视区、 照相机和有向光线，并显示那些为公共属性：

- [ `Scene` ](https://developer.xamarin.com/api/type/Urho.Scene/)来保存您的对象，
- 一个定向[ `Light` ](https://developer.xamarin.com/api/type/Urho.Light/)阴影，并且其位置可以通过使用`LightNode`属性
- [ `Camera` ](https://developer.xamarin.com/api/type/Urho.Camera/) ARKit 将更新传递到应用程序时，会更新其组件和
- [ `ViewPort` ](https://developer.xamarin.com/api/type/Urho.Viewport/)显示结果。


### <a name="your-code"></a>你的代码

然后，你需要为子类`ArkitApp`类并重写`Start`方法。   您的方法应执行的第一件事是链最多`ArkitApp.Start`通过调用`base.Start()`。  之后，您可以使用的任何属性设置由 ArkitApp 将您的对象添加到场景，自定义的灯，阴影或你想要处理的事件。

ARKit/UrhoSharp 示例加载一个经过动画处理的字符与纹理并播放动画，使用以下实现：

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

而这才是您需要做现在具有三维内容显示在增强现实中的所有这些。

Urho 三维模型和动画，使用自定义格式，因此您需要为此格式导出你的资产。   可以使用像这样的工具[Urho3D Blender 接](https://github.com/reattiva/Urho3D-Blender)并[UrhoAssetImporter](https://github.com/EgorBo/UrhoAssetImporter) ，可以将转换这些资产从 DBX、 DAE、 OBJ、 Blend 等常用格式 3D-最大值的格式所需的 Urho。

若要了解有关创建使用 Urho 的 3D 应用程序的详细信息，请访问[简介 UrhoSharp](~/graphics-games/urhosharp/introduction.md)指南。

## <a name="arkitapp-in-depth"></a>在深度 ArkitApp

> [!NOTE]
> 本部分适用于开发人员想要自定义默认的体验的 UrhoSharp 和 ARKit 或想要更深入地了解集成的工作原理。   不需要阅读此部分。

ARKit API 很简单，创建和配置[ARSession](https://developer.apple.com/documentation/arkit/arsession)对象的并开始交付[ARFrame](https://developer.apple.com/documentation/arkit/arframe)对象。   它们包含这两个映像捕获的照相机，以及设备的估计的实际位置。

我们将编写的映像传递相机到我们 3D 内容，并调整 UrhoSharp 以匹配的可能性中的设备的位置和位置中的照相机。

下图显示了正在执行什么操作`ArkitApp`类：

[![类和 ArkitApp 中的屏幕的关系图](urhosharp-images/image2.png)](urhosharp-images/image2.png#lightbox)

### <a name="rendering-the-frames"></a>呈现帧

其原理很简单，合并来自我们 3D 图形，以生成组合的映像与相机的视频。     我们将在序列中，会获得一系列的这些捕获的映像，并且我们将使用 Urho 场景混合此输入。

若要执行此操作的最简单方法是插入[ `RenderPathCommand` ](https://developer.xamarin.com/api/type/Urho.RenderPathCommand/)到主[ `RenderPath` ](https://developer.xamarin.com/api/type/Urho.RenderPath/)。  这是一系列命令执行绘制单个帧。  此命令将填充与我们向它传递任何纹理视区。    我们对此进行设置的过程中，在第一个框架上并且在第中执行的实际定义**ARRenderPath.xml**此时加载的文件。

但是，我们都面临着两个问题要混合在一起这两个领域：


1. 在 iOS 上，GPU 的纹理必须具有分辨率的 2 的幂，但我们将从照相机中获取的帧不具有解析是 2 的幂，例如：1280 x 720。
2. 帧中进行编码[YUV](https://en.wikipedia.org/wiki/YUV)格式，表示由两个图像的亮度和色度。

YUV 帧有两个不同的分辨率。  表示亮度 （基本上灰度图像） 和多小 640 x 360 色度组件 1280 x 720 图像：

![演示组合 Y 和 UV 组件的映像](urhosharp-images/image3.png)


若要绘制使用 OpenGL ES 的完整彩色的图像我们必须编写从纹理槽采用亮度 （Y 组件） 和色度 （UV 平面） 的小着色器。  在 UrhoSharp 它们具有的名称-"sDiffMap"和"sNormalMap"，并将其转换成 RGB 格式：

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

若要呈现不具有两个解决方法的幂的纹理必须使用以下参数定义 Texture2D:

```chsarp
// texture for UV-plane;
cameraUVtexture = new Texture2D();
cameraUVtexture.SetNumLevels(1);
cameraUVtexture.SetSize(640, 360, Graphics.LuminanceAlphaFormat, TextureUsage.Dynamic);
cameraUVtexture.FilterMode = TextureFilterMode.Bilinear;
cameraUVtexture.SetAddressMode(TextureCoordinate.U, TextureAddressMode.Clamp);
cameraUVtexture.SetAddressMode(TextureCoordinate.V, TextureAddressMode.Clamp);
```

因此，我们将能够作为背景呈现捕获的映像并呈现其上方的任何场景的可怕的变化。

### <a name="adjusting-the-camera"></a>调整摄像头

`ARFrame`对象还包含估计的设备位置。  现在我们需要移动游戏照相机 ARFrame-ARKit 之前它不是什么大问题到跟踪设备方向 （滚动、 俯仰和横） 和呈现固定全息图位于视频顶层的但如果有点移动设备-全息，我们将发生偏移。

发生这种情况是因为不能将跟踪移动如陀螺仪的内置传感器，它们可以仅加速。  ARKit 分析每个帧和提取功能点来跟踪，因此可以向我们提供准确转换矩阵包含移动和旋转的数据。

例如，这是我们如何获取当前位置：

```csharp
var row = arCamera.Transform.Row3;
CameraNode.Position = new Vector3(row.X, row.Y, -row.Z);
```

我们使用`-row.Z`因为 ARKit 使用右手坐标系。


### <a name="plane-detection"></a>平面检测

ARKit 能够检测到水平平面和此功能可让您能够与现实世界进行交互，例如，我们可以将实际的表或地板上变化的。 若要执行此操作的最简单方法是使用 HitTest 方法 （光线投射的细节）。 它将为屏幕坐标 (0.5，0.5 的中心) 的真实世界坐标 (0，0; 0 是第一个帧的位置)。

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

现在我们可以将置于我们点击设备屏幕上具体位置取决于水平表面上变化的：

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

![更改的动画的图平面作为视图移动](urhosharp-images/image4.gif)

### <a name="realistic-lighting"></a>写实照明

现实世界照明条件，根据虚拟场景应该较浅或者较深，若要更好地符合其环境。 ARFrame 包含 LightEstimate 属性，我们可以使用调整 Urho 环境光线，这是如下所示：

```csharp
var ambientIntensity = (float) frame.LightEstimate.AmbientIntensity / 1000f;
var zone = Scene.GetComponent<Zone>();
zone.AmbientColor = Color.White * ambientIntensity;
```

### <a name="beyond-ios---hololens"></a>IOS-HoloLens 超出

UrhoSharp[在所有主要的操作系统上运行](~/graphics-games/urhosharp/platform/index.md)，因此可以重复使用现有代码其他位置。

HoloLens 是其运行的最令人兴奋的平台之一。   这意味着您可以轻松地切换之间 iOS 和 HoloLens 构建使用 UrhoSharp 的出色增强现实应用程序。

可以找到 MutantDemo 源码[github.com/EgorBo/ARKitXamarinDemo](https://github.com/EgorBo/ARKitXamarinDemo)。


## <a name="related-links"></a>相关链接

- [UrhoSharp](~/graphics-games/urhosharp/index.md)
- [（使用 UrhoSharp) ARKitXamarinDemo （示例）](https://github.com/EgorBo/ARKitXamarinDemo)
