---
title: 使用模型类
description: 与传统的 3D 图形渲染方法相比，模型类极大地简化了复杂 3D 对象的渲染。 模型对象由内容文件创建而来，可轻松集成内容而无需自定义代码。
ms.prod: xamarin
ms.assetid: AD0A7971-51B1-4E38-B412-7907CE43CDDF
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: c5702780b6a0f0732d846a2cd4226aec5e49fc21
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70766823"
---
# <a name="using-the-model-class"></a>使用模型类

_与传统的 3D 图形渲染方法相比，Model 类极大地简化了渲染复杂 3D 对象的过程。Model 对象是从内容文件创建的，允许轻松集成内容而无需自定义代码。_

MonoGame API 包含一个 `Model` 类，可用于存储从内容文件加载的数据并执行渲染。 模型文件可以非常简单（例如纯色三角形），也可以包含用于复杂渲染的信息，包括纹理和明暗。

本演练使用[机器人 3D 模型](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/Content.zip?raw=true)，其中涵盖以下内容：

- 开始一个新的游戏项目
- 为模型和其纹理创建 XNBs
- 在游戏项目中包括 XNB
- 绘制3D 模型
- 绘制多个模型

完成后，项目将如下所示：

![已完成示例，显示六个机器人](part1-images/image1.png)

## <a name="creating-an-empty-game-project"></a>创建空游戏项目

我们需要首先设置一个名为 MonoGame3D 的游戏项目。 有关创建新 MonoGame 项目的信息，请参阅[有关如何创建跨平台 Monogame 项目的演练](~/graphics-games/monogame/introduction/part1.md)。

在继续之前，应验证项目是否打开并正确部署。 一旦部署完成，应会看到一个蓝色屏幕：

![黑屏蓝游戏屏幕](part1-images/image2.png)

## <a name="including-the-xnbs-in-the-game-project"></a>在游戏项目中包括 XNB

.xnb 文件格式是构建内容（由 [MonoGame 管道工具](http://www.monogame.net/documentation/?page=Pipeline)创建的内容）的标准扩展。 所有构建的内容都有一个源文件（例如我们的模型的源文件是 .fbx 文件）和目标文件（.xnb 文件）。 .fbx 格式是一种常见的3D模型格式，可以由 [Maya](http://www.autodesk.com/products/maya/overview) 和 [Blender](http://www.blender.org/)等应用程序创建。 

`Model` 类可以通过从含有 3D 几何图形数据的磁盘上加载 .xnb 文件来构造。   这个 .xnb 文件是通过内容项目创建的。 Monogame 模板会自动在 Content 文件夹中包含一个内容项目（扩展名为 .mgcp）。 有关 MonoGame 管道工具的详细讨论，请参阅[内容管道指南](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/content-pipeline/introduction.md)。

本指南将略过 MonoGame 管道工具，并使用此处包含的 .XNB 文件。 请注意，.XNB 文件因平台而异，因此请确保在使用的任何平台上使用正确的 XNB 文件。

将解压缩 [Content.zip 文件](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/Content.zip?raw=true)，以便可以在游戏中使用其中包含的 .xnb 文件。 如果正在开发 Android 项目，请右键单击 **WalkingGame.Android** 项目中的 **Assets** 文件夹。 如果正在开发 iOS 项目，请右键单击 **WalkingGame.iOS** 项目。 选择 **“添加”->“添加文件”...** 并选择与所使用的平台相对应的文件夹下的 2 个 .xnb 文件。

这两个文件现应为项目的一部分：

![包含 xnb 文件的解决方案资源管理器内容文件夹](part1-images/xnbsinxs.png)

Visual Studio for Mac可能不会自动为新添加的 XNB 设置生成操作。 对于iOS，右键单击每个文件，然后选择 **“生成操作”->“BundleResource”** 。 对于 Android，右键单击每个文件并选择**生成操作-> AndroidAsset**。

## <a name="rendering-a-3d-model"></a>渲染 3D 模型

在屏幕上呈现模型的最后一步是添加加载和绘制代码。 具体操作如下：

- 在 `Game1` 类中定义 `Model` 实例
- 在 `Game1.LoadContent` 中加载 `Model` 实例
- 在 `Game1.Draw` 中绘制 `Model` 实例

用以下内容替换 `Game1.cs` 代码文件（位于 **WalkingGame PCL** 中）：

```csharp
public class Game1 : Game
{
    GraphicsDeviceManager graphics;

    // This is the model instance that we'll load
    // our XNB into:
    Model model;

    public Game1()
    {
        graphics = new GraphicsDeviceManager(this);
        graphics.IsFullScreen = true;

        Content.RootDirectory = "Content";
    }
    protected override void LoadContent()
    {
        // Notice that loading a model is very similar
        // to loading any other XNB (like a Texture2D).
        // The only difference is the generic type.
        model = Content.Load<Model> ("robot");
    }

    protected override void Update(GameTime gameTime)
    {
        base.Update(gameTime);
    }

    protected override void Draw(GameTime gameTime)
    {
        GraphicsDevice.Clear(Color.CornflowerBlue);

        // A model is composed of "Meshes" which are
        // parts of the model which can be positioned
        // independently, which can use different textures,
        // and which can have different rendering states
        // such as lighting applied.
        foreach (var mesh in model.Meshes)
        {
            // "Effect" refers to a shader. Each mesh may
            // have multiple shaders applied to it for more
            // advanced visuals. 
            foreach (BasicEffect effect in mesh.Effects)
            {
                // We could set up custom lights, but this
                // is the quickest way to get somethign on screen:
                effect.EnableDefaultLighting ();
                // This makes lighting look more realistic on
                // round surfaces, but at a slight performance cost:
                effect.PreferPerPixelLighting = true;

                // The world matrix can be used to position, rotate
                // or resize (scale) the model. Identity means that
                // the model is unrotated, drawn at the origin, and
                // its size is unchanged from the loaded content file.
                effect.World = Matrix.Identity;

                // Move the camera 8 units away from the origin:
                var cameraPosition = new Vector3 (0, 8, 0);
                // Tell the camera to look at the origin:
                var cameraLookAtVector = Vector3.Zero;
                // Tell the camera that positive Z is up
                var cameraUpVector = Vector3.UnitZ;

                effect.View = Matrix.CreateLookAt (
                    cameraPosition, cameraLookAtVector, cameraUpVector);

                // We want the aspect ratio of our display to match
                // the entire screen's aspect ratio:
                float aspectRatio = 
                    graphics.PreferredBackBufferWidth / (float)graphics.PreferredBackBufferHeight;
                // Field of view measures how wide of a view our camera has.
                // Increasing this value means it has a wider view, making everything
                // on screen smaller. This is conceptually the same as "zooming out".
                // It also 
                float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
                // Anything closer than this will not be drawn (will be clipped)
                float nearClipPlane = 1;
                // Anything further than this will not be drawn (will be clipped)
                float farClipPlane = 200;

                effect.Projection = Matrix.CreatePerspectiveFieldOfView(
                    fieldOfView, aspectRatio, nearClipPlane, farClipPlane);

            }

            // Now that we've assigned our properties on the effects we can
            // draw the entire mesh
            mesh.Draw ();
        }
        base.Draw(gameTime);
    }
}
```

如果运行此代码，会在屏幕上看此模型：

![屏幕上显示的模型](part1-images/image8.png "如果运行此代码，模型将显示在屏幕上")

### <a name="model-class"></a>Model 类

`Model`类是用于从内容文件（例如 .fbx 文件）执行 3D 渲染的核心类。 它包含渲染所需的所有信息，包括 3D 几何体、纹理引用以及负责控制位置、光照和相机参数的 `BasicEffect` 实例。

`Model` 类本身不直接提供位置变量，因为单个模型实例可以在多个不同位置进行渲染，这将在本指南的后面部分中介绍。

每个 `Model` 由一个或多个 `ModelMesh` 实例组成，这些实例通过 `Meshes` 属性公开。 虽然可将 `Model` 视为单个游戏对象（例如一个机器人或一辆汽车），但在绘制每个 `ModelMesh` 时可使用不同的 `BasicEffect` 值。 例如，单个网格部件可以表示机器人的腿或汽车上的轮子，可以设定 `BasicEffect` 值，使轮子旋转或腿部移动。 

### <a name="basiceffect-class"></a>BasicEffect 类

`BasicEffect`类提供用于控制渲染选项的属性。 要对 `BasicEffect` 进行的第一个修改是调用`EnableDefaultLighting` 方法。 顾名思义，这会启用默认照明，这非常适合用于验证`Model`是否按预期方式出现在游戏中。 如果注释掉`EnableDefaultLighting`调用，就会看到模型只是其纹理，但没有着色或反光发光：

```csharp
//effect.EnableDefaultLighting ();
```

![只使用纹理渲染而无底纹或反光效果的模型](part1-images/image9.png "只使用纹理渲染而无底纹或反光效果的模型")

可使用 `World` 属性调整模型的位置、旋转和比例。 上面的代码使用 `Matrix.Identity` 值，这意味着 `Model` 将完全按照 .fbx 文件中指定的方式在游戏中渲染。 [第 3 部分](~/graphics-games/monogame/3d/part3.md)中会更详细地介绍矩阵和 3D 坐标，但在示例中，可以通过更改 `World` 属性来更改 `Model` 的位置，如下所示：

```csharp
// Z is up, so changing Z to 3 moves the object up 3 units:
var modelPosition = new Vector3 (0, 0, 3);
effect.World = Matrix.CreateTranslation (modelPosition); 
```

此代码导致对象向上移动 3个 世界单位：

![此代码导致对象上移 3 个世界单位](part1-images/image10.png "此代码导致对象上移 3 个世界单位")

在 `BasicEffect` 上分配的最后两个属性是 `View` 和 `Projection`。 我们将在[第 3 部分](~/graphics-games/monogame/3d/part3.md)中介绍 3D 相机，但在示例中，可以通过更改局部坐标 `cameraPosition` 变量来修改像机的位置：

```csharp
// The 8 has been changed to a 30 to move the Camera further back
var cameraPosition = new Vector3 (0, 30, 0);
```

可以看到相机已向后移动，导致 `Model` 由于透视而显得更小：

![相机已向后移动，导致模型由于透视而显得更小](part1-images/image11.png "相机已向后移动，导致模型由于透视而显得更小")

## <a name="rendering-multiple-models"></a>渲染多个模型

如上所述，可以多次绘制单个`Model` 。 如上所述，单个 `Model` 可以绘制多次。为简化此过程，可将 `Model` 绘制代码移动到它自己的方法中，该方法将目标模型位置作为参数。 完成后，`Draw` 和 `DrawModel` 方法如下所示：

```csharp
protected override void Draw(GameTime gameTime)
{
    GraphicsDevice.Clear(Color.CornflowerBlue);
    DrawModel (new Vector3 (-4, 0, 0));
    DrawModel (new Vector3 ( 0, 0, 0));
    DrawModel (new Vector3 ( 4, 0, 0));
    DrawModel (new Vector3 (-4, 0, 3));
    DrawModel (new Vector3 ( 0, 0, 3));
    DrawModel (new Vector3 ( 4, 0, 3));
    base.Draw(gameTime);
}
void DrawModel(Vector3 modelPosition)
{
    foreach (var mesh in model.Meshes)
    {
        foreach (BasicEffect effect in mesh.Effects)
        {
            effect.EnableDefaultLighting ();
            effect.PreferPerPixelLighting = true;
            effect.World = Matrix.CreateTranslation (modelPosition);
            var cameraPosition = new Vector3 (0, 10, 0);
            var cameraLookAtVector = Vector3.Zero;
            var cameraUpVector = Vector3.UnitZ;
            effect.View = Matrix.CreateLookAt (
                cameraPosition, cameraLookAtVector, cameraUpVector);
            float aspectRatio = 
                graphics.PreferredBackBufferWidth / (float)graphics.PreferredBackBufferHeight;
            float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
            float nearClipPlane = 1;
            float farClipPlane = 200;
            effect.Projection = Matrix.CreatePerspectiveFieldOfView(
                fieldOfView, aspectRatio, nearClipPlane, farClipPlane);
        }
        // Now that we've assigned our properties on the effects we can
        // draw the entire mesh
        mesh.Draw ();
    }
}
```

这会导致机器人模型被绘制六次：

![这会导致机器人模型被绘制六次](part1-images/image1.png "这会导致机器人模型被绘制六次 ")

## <a name="summary"></a>总结

本演练介绍了 MonoGame 的 `Model` 类。 其中包括将 .fbx 文件转换为 .xnb，可将其加载到 `Model` 类中。 还演示了对 `BasicEffect` 实例的修改会如何影响 `Model` 绘制。

## <a name="related-links"></a>相关链接

- [MonoGame 模型参考](http://www.monogame.net/documentation/?page=T_Microsoft_Xna_Framework_Graphics_Model)
- [Content.zip](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/Content.zip?raw=true)
- [已完成的项目（示例）](https://docs.microsoft.com/samples/xamarin/mobile-samples/modelrenderingmg/)
