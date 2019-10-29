---
title: MonoGame 中的三维坐标
description: 了解3D 坐标系统是开发3D 游戏的一个重要步骤。 MonoGame 提供了许多类，用于在三维空间中定位、定向和缩放对象。
ms.prod: xamarin
ms.assetid: A4130995-48FD-4E2E-9C2B-ADCEFF35BE3A
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: a38f4b81f684d6d416e6abe017bc463e3097c6b1
ms.sourcegitcommit: 93697a20e6fc7da547a8714ac109d7953b61d63f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/28/2019
ms.locfileid: "72980859"
---
# <a name="3d-coordinates-in-monogame"></a>MonoGame 中的三维坐标

_了解3D 坐标系统是开发3D 游戏的一个重要步骤。MonoGame 提供了许多类，用于在三维空间中定位、定向和缩放对象。_

开发三维游戏需要了解如何在3D 坐标系中操作对象。 本演练将介绍如何操作视觉对象（特别是模型）。 我们将在控制模型以创建3D 相机类的概念上进行构建。

所提供的概念源自线性代数，但我们将采取一种实用的方法，使没有强数学背景的任何用户都可以在自己的游戏中应用这些概念。

我们将涵盖以下主题：

- 创建项目
- 创建机器人实体
- 移动机器人实体
- 矩阵相乘
- 创建相机实体
- 移动带输入的相机

完成后，我们将获得一个项目，其中包含一个机器人，可通过触摸输入来控制相机和照相机：

![](part3-images/image1.gif "Once finished, the app will include a project with a robot moving in a circle and a camera which can be controlled by touch input")

## <a name="creating-a-project"></a>创建项目

本演练重点介绍如何在三维空间中移动对象。 我们将从用于呈现模型和顶点数组的项目开始，[可在此处找到](https://docs.microsoft.com/samples/xamarin/mobile-samples/modelsandvertsmg/)。 下载后，解压缩并打开项目以确保其运行，我们应该会看到以下内容：

![](part3-images/image2.png "Once downloaded, unzip and open the project to make sure it runs and this view should be displayed")

## <a name="creating-a-robot-entity"></a>创建机器人实体

在开始移动机器人之前，我们将创建一个 `Robot` 类，以包含用于绘图和移动的逻辑。 游戏开发人员将此逻辑和数据封装称为*实体*。

将新的空类文件添加到**MonoGame3D**可移植类库（而不是特定于平台的 ModelAndVerts）。 将其命名为**机器人**，并单击 "**新建**"：

![](part3-images/image3.png "Name it Robot and click New")

按如下所示修改 `Robot` 类：

```csharp
using System;
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Graphics;
using Microsoft.Xna.Framework.Content;

namespace MonoGame3D
{
    public class Robot
    {
        Model model;

        public void Initialize(ContentManager contentManager)
        {
            model = contentManager.Load<Model> ("robot");

        }

        // For now we'll take these values in, eventually we'll
        // take a Camera object
        public void Draw(Vector3 cameraPosition, float aspectRatio)
        {
            foreach (var mesh in model.Meshes)
            {
                foreach (BasicEffect effect in mesh.Effects)
                {
                    effect.EnableDefaultLighting ();
                    effect.PreferPerPixelLighting = true;

                    effect.World = Matrix.Identity; 
                    var cameraLookAtVector = Vector3.Zero;
                    var cameraUpVector = Vector3.UnitZ;

                    effect.View = Matrix.CreateLookAt (
                        cameraPosition, cameraLookAtVector, cameraUpVector);

                    float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
                    float nearClipPlane = 1;
                    float farClipPlane = 200;

                    effect.Projection = Matrix.CreatePerspectiveFieldOfView(
                        fieldOfView, aspectRatio, nearClipPlane, farClipPlane);

                }

                // Now that we've assigned our properties on the effects we can
                // draw the entire mesh
                mesh.Draw ();
            }
        }
    }
}
```

`Robot` 代码实质上是用于绘制 `Model`的 `Game1` 中的相同代码。 有关 `Model` 加载和绘制的评审，请参阅[有关如何使用模型的此指南](~/graphics-games/monogame/3d/part1.md)。 现在，我们可以从 `Game1`中删除所有 `Model` 加载和呈现代码，并将其替换为 `Robot` 实例：

```csharp
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Graphics;

namespace MonoGame3D
{
    public class Game1 : Game
    {
        GraphicsDeviceManager graphics;

        VertexPositionNormalTexture[] floorVerts;

        BasicEffect effect;

        Texture2D checkerboardTexture;

        Vector3 cameraPosition = new Vector3(15, 10, 10);

        Robot robot;

        public Game1()
        {
            graphics = new GraphicsDeviceManager(this);
            graphics.IsFullScreen = true;

            Content.RootDirectory = "Content";
        }

        protected override void Initialize ()
        {
            floorVerts = new VertexPositionNormalTexture[6];

            floorVerts [0].Position = new Vector3 (-20, -20, 0);
            floorVerts [1].Position = new Vector3 (-20,  20, 0);
            floorVerts [2].Position = new Vector3 ( 20, -20, 0);

            floorVerts [3].Position = floorVerts[1].Position;
            floorVerts [4].Position = new Vector3 ( 20,  20, 0);
            floorVerts [5].Position = floorVerts[2].Position;

            int repetitions = 20;

            floorVerts [0].TextureCoordinate = new Vector2 (0, 0);
            floorVerts [1].TextureCoordinate = new Vector2 (0, repetitions);
            floorVerts [2].TextureCoordinate = new Vector2 (repetitions, 0);

            floorVerts [3].TextureCoordinate = floorVerts[1].TextureCoordinate;
            floorVerts [4].TextureCoordinate = new Vector2 (repetitions, repetitions);
            floorVerts [5].TextureCoordinate = floorVerts[2].TextureCoordinate;

            effect = new BasicEffect (graphics.GraphicsDevice);

            robot = new Robot ();
            robot.Initialize (Content);

            base.Initialize ();
        }

        protected override void LoadContent()
        {
            using (var stream = TitleContainer.OpenStream ("Content/checkerboard.png"))
            {
                checkerboardTexture = Texture2D.FromStream (this.GraphicsDevice, stream);
            }
        }

        protected override void Update(GameTime gameTime)
        {
            base.Update(gameTime);
        }

        protected override void Draw(GameTime gameTime)
        {
            GraphicsDevice.Clear(Color.CornflowerBlue);

            DrawGround ();

            float aspectRatio = 
                graphics.PreferredBackBufferWidth / (float)graphics.PreferredBackBufferHeight;
            robot.Draw (cameraPosition, aspectRatio);

            base.Draw(gameTime);
        }

        void DrawGround()
        {
            var cameraLookAtVector = Vector3.Zero;
            var cameraUpVector = Vector3.UnitZ;

            effect.View = Matrix.CreateLookAt (
                cameraPosition, cameraLookAtVector, cameraUpVector);

            float aspectRatio = 
                graphics.PreferredBackBufferWidth / (float)graphics.PreferredBackBufferHeight;
            float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
            float nearClipPlane = 1;
            float farClipPlane = 200;

            effect.Projection = Matrix.CreatePerspectiveFieldOfView(
                fieldOfView, aspectRatio, nearClipPlane, farClipPlane);

            effect.TextureEnabled = true;
            effect.Texture = checkerboardTexture;

            foreach (var pass in effect.CurrentTechnique.Passes)
            {
                pass.Apply ();

                graphics.GraphicsDevice.DrawUserPrimitives (
                            PrimitiveType.TriangleList,
                    floorVerts,
                    0,
                    2);
            }
        }
    }
}
```

如果我们运行代码，我们将有一个只包含一个机器人的场景，该机器人通常在地面下绘制：

![](part3-images/image4.png "If the code is run now, the app will display a scene with only one robot which is drawn mostly under the floor")

## <a name="moving-the-robot"></a>移动机器人

现在我们有了一个 `Robot` 类，接下来可以将移动逻辑添加到机器人。 在这种情况下，我们只会根据游戏时间使机器人进入一个圆圈。 这对于实际游戏来说是一种不切实际的实现，因为字符通常会响应输入或人工智能，但它为我们提供了一个环境，供我们探索3D 定位和旋转。

在 `Robot` 类之外，我们所需的唯一信息是当前游戏时间。 我们将添加一个 `Update` 方法，该方法将采用 `GameTime` 参数。 此 `GameTime` 参数将用于递增角度变量，我们将用它来确定机器人的最终位置。

首先，将 angle 字段添加到 `model` 字段下的 `Robot` 类：

```csharp
public class Robot
{
    public Model model;

    // new code:
    float angle;
    ...
```

 现在，我们可以在 `Update` 函数中增加此值：

```csharp
public void Update(GameTime gameTime)
{
    // TotalSeconds is a double so we need to cast to float
    angle += (float)gameTime.ElapsedGameTime.TotalSeconds;
}
```

我们需要确保从 `Game1.Update`调用 `Update` 方法：

```csharp
protected override void Update(GameTime gameTime)
{
    robot.Update (gameTime);
    base.Update(gameTime);
}
```

当然，此时 angle 字段不会执行任何操作–我们需要编写代码来使用它。 我们将修改 `Draw` 方法，以便可以在专用方法中计算世界 `Matrix`： 

```csharp
public void Draw(Vector3 cameraPosition, float aspectRatio)
{
    foreach (var mesh in model.Meshes)
    {
        foreach (BasicEffect effect in mesh.Effects)
        {
            effect.EnableDefaultLighting ();
            effect.PreferPerPixelLighting = true;
            // We’ll be doing our calculations here...
            effect.World = GetWorldMatrix();

            var cameraLookAtVector = Vector3.Zero;
            var cameraUpVector = Vector3.UnitZ;

            effect.View = Matrix.CreateLookAt (
                cameraPosition, cameraLookAtVector, cameraUpVector);

            float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
            float nearClipPlane = 1;
            float farClipPlane = 200;

            effect.Projection = Matrix.CreatePerspectiveFieldOfView(
                fieldOfView, aspectRatio, nearClipPlane, farClipPlane);
        }

        mesh.Draw ();
    }
}
```

接下来，我们将在 `Robot` 类中实现 `GetWorldMatrix` 方法：

```csharp
Matrix GetWorldMatrix()
{
    const float circleRadius = 8;
    const float heightOffGround = 3;

    // this matrix moves the model "out" from the origin
    Matrix translationMatrix = Matrix.CreateTranslation (
        circleRadius, 0, heightOffGround);

    // this matrix rotates everything around the origin
    Matrix rotationMatrix = Matrix.CreateRotationZ (angle);

    // We combine the two to have the model move in a circle:
    Matrix combined = translationMatrix * rotationMatrix;

    return combined;
}
```

运行此代码的结果会使机器人在圆圈中移动：

![](part3-images/image5.gif "Running this code results in the robot moving in a circle")

## <a name="matrix-multiplication"></a>矩阵相乘

上面的代码通过在 `GetWorldMatrix` 方法中创建 `Matrix` 来旋转机器人。 `Matrix` 结构包含16个可用于转换（设置位置）、旋转和缩放的浮点值（设置大小）。 当我们分配 "`effect.World`" 属性时，我们会告诉基础呈现系统如何定位、调整大小和定位我们所要绘制的任何内容（顶点的 `Model` 或几何）。 

幸运的是，`Matrix` 结构包含多个方法，可简化常见类型的矩阵的创建。 以上代码中的第一个使用 `Matrix.CreateTranslation`。 数学术语*转换*指的是一项操作，该操作将导致一个点（或在我们的示例模型中）从一个位置移到另一个位置，而无需进行任何其他修改（如旋转或调整大小）。 函数使用 X、Y 和 Z 值进行转换。 如果我们从上到下查看场景，则 `CreateTranslation` 方法（隔离）将执行以下操作：

![](part3-images/image6.png "The CreateTranslation method in isolation performs this action")

我们创建的第二个矩阵是使用 `CreateRotationZ` 矩阵的轮换矩阵。 这是可用于创建旋转的三种方法之一：

- `CreateRotationX`
- `CreateRoationY`
- `CreateRotationZ`

每个方法通过旋转给定轴来创建旋转矩阵。 在我们的示例中，我们将围绕 Z 轴旋转，这将指向 "向上"。 以下内容可帮助直观显示基于轴的旋转的工作方式：

![](part3-images/image7.png "This can help visualize how axis-based rotation works")

我们还将 `CreateRotationZ` 方法与 angle 字段结合使用，该字段随着时间的推移而随 `Update` 时间而递增。 结果是，`CreateRotationZ` 方法会使机器人围绕时间段围绕原点进行轨迹。

最后一行代码将两个矩阵合并为一个矩阵：

```csharp
Matrix combined = translationMatrix * rotationMatrix;
```

这称为矩阵乘法，其工作方式与常规乘法略有不同。 *乘法的交换律*规定，乘法运算中数字的顺序不会更改结果。 也就是说，3 \* 4 等效于 4 \* 3。 矩阵乘法的不同之处在于它不是可交换的。 也就是说，可以将上述行读为 "应用 translationMatrix 来移动模型，然后通过应用 rotationMatrix 旋转所有内容"。 可以按如下所示直观显示上述行对位置和旋转的影响：

![](part3-images/image8.png "A visualization pf the way that the above line affects the position and rotation")

为了帮助理解矩阵相乘的顺序对结果产生的影响，请考虑以下各项：

```csharp
Matrix combined = rotationMatrix * translationMatrix;
```

上面的代码首先旋转模型，然后对模型进行转换：

![](part3-images/image9.png "The code above would first rotate the model in-place, then translate it")

如果我们以反转的乘法运行代码，就会注意到，因为旋转首先适用，它只会影响模型的方向，模型的位置保持不变。 换句话说，模型将就地旋转：

![](part3-images/image10.gif "The model rotates in place")

## <a name="creating-the-camera-entity"></a>创建相机实体

`Camera` 实体将包含执行基于输入的移动所需的所有逻辑，并提供用于在 `BasicEffect` 类上分配属性的属性。

首先，我们将实现一个静态照相机（无基于输入的移动），并将其集成到现有项目中。 将新类添加到**MonoGame3D**可移植类库（`Robot.cs`的同一项目）并将其命名为**相机**。 将此文件的内容替换为以下代码：

```csharp
using System;
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Graphics;

namespace MonoGame3D
{
    public class Camera
    {
        // We need this to calculate the aspectRatio
        // in the ProjectionMatrix property.
        GraphicsDevice graphicsDevice;

        Vector3 position = new Vector3(15, 10, 10);

        public Matrix ViewMatrix
        {
            get
            {
                var lookAtVector = Vector3.Zero;
                var upVector = Vector3.UnitZ;

                return Matrix.CreateLookAt (
                    position, lookAtVector, upVector);
            }
        }

        public Matrix ProjectionMatrix
        {
            get
            {
                float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
                float nearClipPlane = 1;
                float farClipPlane = 200;
                float aspectRatio = graphicsDevice.Viewport.Width / (float)graphicsDevice.Viewport.Height;

                return Matrix.CreatePerspectiveFieldOfView(
                    fieldOfView, aspectRatio, nearClipPlane, farClipPlane);
            }
        }

        public Camera(GraphicsDevice graphicsDevice)
        {
            this.graphicsDevice = graphicsDevice;
        }

        public void Update(GameTime gameTime)
        {
            // We'll be doing some input-based movement here
        }
    }
}
```

上面的代码与 `Game1` 中的代码和 `Robot` 分配 `BasicEffect`上的矩阵的代码非常相似。 

现在，我们可以将新的 `Camera` 类集成到现有项目中。 首先，我们将修改 `Robot` 类以在其 `Draw` 方法中采用 `Camera` 实例，这将消除大量重复的代码。 将 `Robot.Draw` 方法替换为以下内容：

```csharp
public void Draw(Camera camera)
{
    foreach (var mesh in model.Meshes)
    {
        foreach (BasicEffect effect in mesh.Effects)
        {
            effect.EnableDefaultLighting ();
            effect.PreferPerPixelLighting = true;

            effect.World = GetWorldMatrix();
            effect.View = camera.ViewMatrix;
            effect.Projection = camera.ProjectionMatrix;
        }

        mesh.Draw ();
    }
}
```

接下来，修改 `Game1.cs` 文件：

```csharp
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Graphics;

namespace MonoGame3D
{
    public class Game1 : Game
    {
        GraphicsDeviceManager graphics;

        VertexPositionNormalTexture[] floorVerts;

        BasicEffect effect;

        Texture2D checkerboardTexture;

        // New camera code
        Camera camera;

        Robot robot;

        public Game1()
        {
            graphics = new GraphicsDeviceManager(this);
            graphics.IsFullScreen = true;

            Content.RootDirectory = "Content";
        }

        protected override void Initialize ()
        {
            floorVerts = new VertexPositionNormalTexture[6];

            floorVerts [0].Position = new Vector3 (-20, -20, 0);
            floorVerts [1].Position = new Vector3 (-20,  20, 0);
            floorVerts [2].Position = new Vector3 ( 20, -20, 0);

            floorVerts [3].Position = floorVerts[1].Position;
            floorVerts [4].Position = new Vector3 ( 20,  20, 0);
            floorVerts [5].Position = floorVerts[2].Position;

            int repetitions = 20;

            floorVerts [0].TextureCoordinate = new Vector2 (0, 0);
            floorVerts [1].TextureCoordinate = new Vector2 (0, repetitions);
            floorVerts [2].TextureCoordinate = new Vector2 (repetitions, 0);

            floorVerts [3].TextureCoordinate = floorVerts[1].TextureCoordinate;
            floorVerts [4].TextureCoordinate = new Vector2 (repetitions, repetitions);
            floorVerts [5].TextureCoordinate = floorVerts[2].TextureCoordinate;

            effect = new BasicEffect (graphics.GraphicsDevice);

            robot = new Robot ();
            robot.Initialize (Content);

            // New camera code
            camera = new Camera (graphics.GraphicsDevice);

            base.Initialize ();
        }

        protected override void LoadContent()
        {
            using (var stream = TitleContainer.OpenStream ("Content/checkerboard.png"))
            {
                checkerboardTexture = Texture2D.FromStream (this.GraphicsDevice, stream);
            }
        }

        protected override void Update(GameTime gameTime)
        {
            robot.Update (gameTime);
            // New camera code
            camera.Update (gameTime);
            base.Update(gameTime);
        }

        protected override void Draw(GameTime gameTime)
        {
            GraphicsDevice.Clear(Color.CornflowerBlue);

            DrawGround ();

            // New camera code
            robot.Draw (camera);

            base.Draw(gameTime);
        }

        void DrawGround()
        {
            // New camera code
            effect.View = camera.ViewMatrix;
            effect.Projection = camera.ProjectionMatrix;

            effect.TextureEnabled = true;
            effect.Texture = checkerboardTexture;

            foreach (var pass in effect.CurrentTechnique.Passes)
            {
                pass.Apply ();

                graphics.GraphicsDevice.DrawUserPrimitives (
                            PrimitiveType.TriangleList,
                    floorVerts,
                    0,
                    2);
            }
        }
    }
}
```

对以前版本中的 `Game1` 的修改（通过 `// New camera code` 标识）是：

- `Game1` 中的 `Camera` 字段
- `Camera` 在 `Game1.Initialize` 中实例化
- `Game1.Update` 中的 `Camera.Update` 调用
- `Robot.Draw` 现在采用 `Camera` 参数
- `Game1.Draw` 现在使用 `Camera.ViewMatrix` 和 `Camera.ProjectionMatrix`

## <a name="moving-the-camera-with-input"></a>移动带输入的相机

到目前为止，我们添加了一个 `Camera` 实体，但没有执行任何操作来更改运行时行为。 我们将添加允许用户执行以下操作的行为：

- 触摸屏幕的左侧，使相机向左旋转
- 触摸屏幕的右侧，使相机向右旋转
- 触摸屏幕中心来向前移动相机

### <a name="making-lookat-relative"></a>使 lookAt 相对

首先，我们将更新 `Camera` 类，以包含一个 `angle` 字段，该字段将用于设置 `Camera` 的方向。 目前，我们的 `Camera` 通过本地 `lookAtVector`确定其方向，该方向分配给 `Vector3.Zero`。 换句话说，我们的 `Camera` 始终查找源。 如果移动了相机，则照相机的正面还会变化：

![](part3-images/image11.gif "If the Camera moves, then the angle that the camera is facing will also change")

我们想要 `Camera` 的方向朝同一方向，而不考虑其位置–至少直到我们实现使用输入来旋转 `Camera` 的逻辑。 第一次更改是将 `lookAtVector` 变量调整为基于当前位置，而不是查看绝对位置：

```csharp
public class Camera
{
    GraphicsDevice graphicsDevice;

    // Let's start at X = 0 so we're looking at things head-on
    Vector3 position = new Vector3(0, 20, 10);

    public Matrix ViewMatrix
    {
        get
        {
            var lookAtVector = new Vector3 (0, -1, -.5f);
            lookAtVector += position;

            var upVector = Vector3.UnitZ;

            return  Matrix.CreateLookAt (
                position, lookAtVector, upVector);
        }
    }
    ...
```

这会导致 `Camera` 直接观看世界。 请注意，初始 `position` 值已修改为 `(0, 20, 10)` 以便 `Camera` 在 X 轴上居中。 运行游戏显示：

![](part3-images/image12.png "Running the game displays this view")

### <a name="creating-an-angle-variable"></a>创建角度变量

`lookAtVector` 变量控制相机正在查看的角度。 目前，它是固定的，以便沿负 Y 轴向下查看，稍微向下倾斜（从 `-.5f` Z 值开始）。 我们将创建一个 `angle` 变量，该变量将用于调整 `lookAtVector` 属性。 

在本演练的前面部分中，我们介绍了可以使用矩阵来旋转对象的绘制方式。 使用 `Vector3.Transform` 方法，还可以使用矩阵来旋转向量，如 `lookAtVector`。 

添加一个 `angle` 字段，并按如下所示修改 `ViewMatrix` 属性：

```csharp
public class Camera
{
    GraphicsDevice graphicsDevice;

    Vector3 position = new Vector3(0, 20, 10);

    float angle;

    public Matrix ViewMatrix
    {
        get
        {
            var lookAtVector = new Vector3 (0, -1, -.5f);
            // We'll create a rotation matrix using our angle
            var rotationMatrix = Matrix.CreateRotationZ (angle);
            // Then we'll modify the vector using this matrix:
            lookAtVector = Vector3.Transform (lookAtVector, rotationMatrix);
            lookAtVector += position;

            var upVector = Vector3.UnitZ;

            return  Matrix.CreateLookAt (
                position, lookAtVector, upVector);
        }
    }
    ...
```

### <a name="reading-input"></a>正在读取输入

现在，我们的 `Camera` 实体可以通过其位置和角度变量进行完全控制，只需根据输入更改它们即可。

首先，我们将获得 `TouchPanel` 状态，查找用户触摸屏幕的位置。 有关使用 `TouchPanel` 类的详细信息，请参阅[触摸屏 API 参考](http://www.monogame.net/documentation/?page=T_Microsoft_Xna_Framework_Input_Touch_TouchPanel)。

如果用户在第三方触摸，我们将调整 `angle` 值，以便 `Camera` 向左旋转，如果用户在第三方触摸，则会旋转其他方法。 如果用户在屏幕的中间第三方触摸，则将 `Camera` 向前移动。

首先，添加 using 语句以在 `Camera.cs`中限定 `TouchPanel` 和 `TouchCollection` 类：

```csharp
using Microsoft.Xna.Framework.Input.Touch; 
```

接下来，修改 `Update` 方法以读取触控面板，并适当地调整 `angle` 和 `position` 变量：

```csharp
public void Update(GameTime gameTime)
{
    TouchCollection touchCollection = TouchPanel.GetState();

    bool isTouchingScreen = touchCollection.Count > 0;
    if (isTouchingScreen)
    {
        var xPosition = touchCollection [0].Position.X;

        float xRatio = xPosition / (float)graphicsDevice.Viewport.Width;

        if (xRatio < 1 / 3.0f)
        {
            angle += (float)gameTime.ElapsedGameTime.TotalSeconds;
        }
        else if (xRatio < 2 / 3.0f)
        {
            var forwardVector = new Vector3 (0, -1, 0);

            var rotationMatrix = Matrix.CreateRotationZ (angle);
            forwardVector = Vector3.Transform (forwardVector, rotationMatrix);

            const float unitsPerSecond = 3;

            this.position += forwardVector * unitsPerSecond *
                (float)gameTime.ElapsedGameTime.TotalSeconds ;
        }
        else
        {
            angle -= (float)gameTime.ElapsedGameTime.TotalSeconds;
        }
    }
}
```

现在，`Camera` 将响应触摸输入：

![](part3-images/image1.gif "Now the Camera will respond to touch input")

更新方法首先调用 `TouchPanel.GetState`，这会返回一组接触。 尽管 `TouchPanel.GetState` 可以返回多个触控点，但对于简单起见，我们只需考虑第一个点。

如果用户正在触摸屏幕，则代码将进行检查以确定第一个触摸是位于屏幕的左侧、中间还是右第三。 向左和右指根据 `TotalSeconds` 值增加或减少 `angle` 变量来旋转相机（这样，无论帧速率如何），游戏的行为都是相同的。

如果用户正在触摸屏幕的第三个屏幕，则照相机会向前移动。 首先通过获取正向向量（最初定义为指向负 Y 轴），然后通过使用 `Matrix.CreateRotationZ` 和 `angle` 值创建的矩阵旋转来完成此操作。 最后，`forwardVector` 使用 `unitsPerSecond` 系数应用于 `position`。

## <a name="summary"></a>总结

本演练介绍如何使用 `Matrices` 和 `BasicEffect.World` 属性在三维空间中移动和旋转 `Models`。 这种形式的移动为在三维游戏中移动对象提供了基础。 本演练还介绍了如何实现 `Camera` 实体，以便从任何位置和角度查看世界。

## <a name="related-links"></a>相关链接

- [MonoGame API 链接](http://www.monogame.net/documentation/?page=api)
- [已完成的项目（示例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/monogame3dcamera)
