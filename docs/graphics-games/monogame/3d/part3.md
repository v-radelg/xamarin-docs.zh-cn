---
title: 在 MonoGame 的 3D 坐标
description: 了解三维坐标系统是在开发 3D 游戏的一个重要步骤。 MonoGame 提供了许多用于定位、 确定方向，和缩放在 3D 空间中的对象的类。
ms.prod: xamarin
ms.assetid: A4130995-48FD-4E2E-9C2B-ADCEFF35BE3A
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: dc21228f1daa74a90ff8f0ea346bc01b109f0987
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61229293"
---
# <a name="3d-coordinates-in-monogame"></a>在 MonoGame 的 3D 坐标

_了解三维坐标系统是在开发 3D 游戏的一个重要步骤。MonoGame 提供了许多用于定位、 确定方向，和缩放在 3D 空间中的对象的类。_

开发 3D 游戏，需要了解如何操作 3D 坐标系中的对象。 本演练将介绍如何操作视觉对象 （特别是模型）。 我们将构建一个模型来创建一个三维照相机类控制的概念。

介绍的概念源自线性代数，但我们将采取切实可行的方法，以便任何用户，而无需强数学背景可以应用这些概念进行了自己的游戏。

我们将讨论以下主题：

- 创建项目
- 创建机器人实体
- 移动机器人实体
- 矩阵乘法
- 创建照相机实体
- 移动输入摄像头

完成后，我们将具有移动中一个圆形和照相机可以通过触摸输入受控制的机器人项目：

![](part3-images/image1.gif "完成后，应用与移动中一个圆形和照相机可以通过触摸输入受控制的机器人中包含一个项目")


## <a name="creating-a-project"></a>创建项目

本演练重点介绍在 3D 空间中移动对象。 我们将首先处理的项目呈现模型和顶点数组[这可在此处找到](https://developer.xamarin.com/samples/mobile/ModelsAndVertsMG/)。 下载完成后，解压缩，然后打开项目，以确保它在运行并且应该会看到以下：

![](part3-images/image2.png "下载完成后，解压缩，然后打开项目，以确保它在运行，并且应显示此视图")


## <a name="creating-a-robot-entity"></a>创建机器人实体

在开始移动围绕我们机器人之前，我们将创建`Robot`类，以包含移动和绘制逻辑。 游戏开发人员参考的逻辑和数据作为此封装*实体*。

添加到新的空类文件**MonoGame3D**可移植类库 (不特定于平台的 ModelAndVerts.Android)。 其命名为**机器人**然后单击**新建**:

![](part3-images/image3.png "机器人将其命名并单击新建")

修改`Robot`类，如下所示：

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

`Robot`代码是在实质上是相同的代码`Game1`绘图`Model`。 在评审`Model`加载和绘图，请参阅[本指南使用模型](~/graphics-games/monogame/3d/part1.md)。 我们现在可以删除的所有`Model`加载和呈现代码从`Game1`，并将其替换为`Robot`实例：

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

如果我们运行的代码现在将具有与只有一个机器人这主要是在基底下绘制场景：

![](part3-images/image4.png "如果现在运行代码，则应用将显示，一个场景并只有一个机器人主要是在基底下绘制而成")

## <a name="moving-the-robot"></a>移动机器人

现在，我们已经`Robot`类中，我们可以将移动逻辑添加到机器人。 在这种情况下，我们只需将按照游戏时间以圆圈移动机器人。 由于字符可能通常响应输入或人工智能，但它提供一个环境，用于我们，了解三维定位和旋转，这是进行实际的游戏有点不切实际实现。

我们将需要从之外的唯一信息`Robot`类是当前游戏时间。 我们将添加`Update`方法，这需要`GameTime`参数。 这`GameTime`参数将用于增加角度变量，我们将使用以确定机器人的最后一个位置。

首先，我们将添加到角度字段`Robot`类下`model`字段：

```csharp
public class Robot
{
    public Model model;

    // new code:
    float angle;
    ...
```

 现在，我们可以增加此值在`Update`函数：

```csharp
public void Update(GameTime gameTime)
{
    // TotalSeconds is a double so we need to cast to float
    angle += (float)gameTime.ElapsedGameTime.TotalSeconds;
}
```

我们需要确保`Update`方法从调用`Game1.Update`:

```csharp
protected override void Update(GameTime gameTime)
{
    robot.Update (gameTime);
    base.Update(gameTime);
}
```

当然，此时角度字段不执行任何操作 – 我们需要编写代码即可使用它。 我们将修改`Draw`方法，以便我们可以计算世界`Matrix`专用方法中： 

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

接下来，我们将实现`GetWorldMatrix`中的方法`Robot`类：

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

运行此代码的结果会导致在一个圆周中移动机器人：

![](part3-images/image5.gif "在一个圆周中移动机器人中运行此代码会生成")

## <a name="matrix-multiplication"></a>矩阵乘法

上面的代码来创建旋转机器人`Matrix`在`GetWorldMatrix`方法。 `Matrix`结构包含可用于转换 （集位置）、 旋转和缩放 16 的浮点值 （设置大小）。 当我们将分配`effect.World`属性，我们将指示基础呈现系统如何定位、 大小和定位任何我们碰巧绘制 (`Model`或顶点中的几何)。 

幸运的是，`Matrix`结构包括多种方法，从而简化了常见的矩阵类型创建。 在上面的代码中使用的第一个是`Matrix.CreateTranslation`。 数学术语*翻译*指的是一个操作，它会在一个点 （或在我们的示例一个模型） 从一个位置移动到另一个没有任何其他修改 （例如旋转和调整大小）。 该函数采用翻译的 X、 Y 和 Z 值。 如果我们查看从自上而下的场景我们`CreateTranslation`方法 （在隔离） 执行以下：

![](part3-images/image6.png "在隔离的 CreateTranslation 方法执行此操作")

我们创建第二个矩阵的旋转矩阵就是使用`CreateRotationZ`矩阵。 这是用于创建旋转的三种方法之一：

- `CreateRotationX`
- `CreateRoationY`
- `CreateRotationZ`

每个方法通过有关给定轴旋转创建旋转矩阵。 在本例中，我们要轮替围绕 Z 轴，"向上"箭头。 以下可帮助直观显示如何基于轴的旋转的工作原理：

![](part3-images/image7.png "这有助于直观显示如何基于轴的旋转的工作原理")

我们还在使用`CreateRotationZ`方法替换角度字段中，由于不断递增我们`Update`被调用方法。 结果是，`CreateRotationZ`方法使我们的机器人到随着时间的推移轨迹围绕原点。

最后一行代码将两个矩阵合并成一个：

```csharp
Matrix combined = translationMatrix * rotationMatrix;
```

这称为矩阵乘法，其工作方式与常规乘法稍有不同。 *乘法的可交换属性*规定的乘法运算中的数字顺序不会更改结果。 也就是说，3 * 4 相当于 4 * 3。 矩阵乘法不同之处在于它不是可交换性。 也就是说，上面的行可以读取为"应用 translationMatrix 移动模型，然后通过应用 rotationMatrix 旋转的所有内容"。 我们无法实现上述代码行影响的位置和旋转，如下所示的方式可视化效果：

![](part3-images/image8.png "可视化效果 pf 的位置和旋转，上述代码行，会影响的方式")

若要帮助您了解矩阵乘法的顺序可能会如何影响结果，请考虑的以下内容，其中反转矩阵乘法：

```csharp
Matrix combined = rotationMatrix * translationMatrix;
```

上面的代码首先会旋转模型中的位置，然后将其转换：

![](part3-images/image9.png "上面的代码首先会旋转模型中的位置，然后将其转换")

如果我们运行倒排乘法的代码，我们会注意到，因为旋转会首先将应用，只会影响模型的方向和模型的位置保持不变。 换而言之，模型将就地旋转：

![](part3-images/image10.gif "就地旋转模型")

## <a name="creating-the-camera-entity"></a>创建照相机实体

`Camera`实体将包含所有所需执行基于输入的移动，并提供将属性分配上的属性的逻辑`BasicEffect`类。

首先我们将实现静态相机 （无基于输入的移动），并将其集成到我们的现有项目。 添加新类中的，以便**MonoGame3D**可移植类库 (具有相同项目`Robot.cs`) 并将其命名**照相机**。 将此文件的内容替换为以下代码：

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

上面的代码是非常类似于中的代码`Game1`并`Robot`其上分配矩阵`BasicEffect`。 

现在，我们可以将集成新`Camera`到我们的现有项目的类。 首先，我们将修改`Robot`类才能`Camera`实例在其`Draw`方法，将消除大量的重复代码。 替换为`Robot.Draw`使用以下方法：

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

接下来，修改`Game1.cs`文件：

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

对修改`Game1`以前的版本 (其中带有`// New camera code`) 是：

- `Camera` 中的字段 `Game1`
- `Camera` 在实例化 `Game1.Initialize`
- `Camera.Update` 在中调用 `Game1.Update`
- `Robot.Draw` 现在只需`Camera`参数
- `Game1.Draw` 现在使用`Camera.ViewMatrix`和 `Camera.ProjectionMatrix`

## <a name="moving-the-camera-with-input"></a>移动输入摄像头

到目前为止，我们已添加了`Camera`实体但尚未执行任何操作来更改运行时行为。 允许用户的行为，我们将添加到：

- 触摸屏幕打开左侧的照相机的左侧
- 触摸屏幕打开右侧照相机的右侧
- 触摸屏幕以向前移动的照相机的中心

### <a name="making-lookat-relative"></a>使 lookAt 相对

首先我们将更新`Camera`类，以包含`angle`字段将用于该字段的方向设置`Camera`面向。 目前，我们`Camera`确定它通过本地对着的方向`lookAtVector`，该值将赋给`Vector3.Zero`。 换而言之，我们`Camera`始终查看源。 如果照相机移动，将还会更改对着相机的角度：

![](part3-images/image11.gif "如果照相机移动，然后面向相机的角度也会更改")

我们希望`Camera`要面临相同方向而不考虑其位置 – 至少直到我们实现逻辑，将旋转`Camera`使用的输入。 第一次更改将调整`lookAtVector`变量来基于我们当前的位置，而不是看看是绝对位置：

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

这会导致`Camera`查看世界直接上。 请注意，初始`position`值已修改为`(0, 20, 10)`因此`Camera`X 轴上居中。 运行游戏显示：

![](part3-images/image12.png "运行该游戏将显示此视图")

### <a name="creating-an-angle-variable"></a>创建变量的角度

`lookAtVector`变量控制查看我们相机的角度。 目前它是固定的以便向负 Y 轴下, 查看，略有向下倾斜 (从`-.5f`Z 值)。 我们将创建`angle`变量，用于调整`lookAtVector`属性。 

在本演练的前面几节中介绍了矩阵可用于旋转如何绘制对象。 我们还可以使用矩阵来旋转等矢量`lookAtVector`使用`Vector3.Transform`方法。 

添加`angle`字段并修改`ViewMatrix`属性，如下所示：

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

### <a name="reading-input"></a>读取输入

我们`Camera`通过其位置和角度变量，实体可以现在完全控制 – 只需根据输入更改它们。

首先，我们将获取`TouchPanel`状态以查找用户触摸屏幕的位置。 有关使用的详细信息`TouchPanel`类，请参阅[触摸屏 API 参考](http://www.monogame.net/documentation/?page=T_Microsoft_Xna_Framework_Input_Touch_TouchPanel)。

如果用户触摸左侧第三次，则我们将调整`angle`值，因此`Camera`左侧、 旋转和用户触摸上正确的第三，如果我们将旋转另一种方法。 如果用户触摸中的中间的第三个屏幕上，则我们来看看`Camera`转发。

首先，添加 using 语句以限定`TouchPanel`并`TouchCollection`中的类`Camera.cs`:

```csharp
using Microsoft.Xna.Framework.Input.Touch; 
```

接下来，修改`Update`方法来读取触摸屏并调整`angle`和`position`变量正确：

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

现在`Camera`将响应触摸屏输入：

![](part3-images/image1.gif "现在，照相机将响应触摸屏输入")

更新方法首先调用`TouchPanel.GetState`，它返回一系列收尾工作了。 尽管`TouchPanel.GetState`可以返回多个触摸点，我们将只考虑第一个为简单起见。

如果用户触摸屏幕，然后将查看第一次触摸如果位于左侧、 中间或右侧屏幕的第三个代码。 左侧和右侧折来增大或减小旋转照相机`angle`根据变量`TotalSeconds`值 （以便游戏的行为相同，与帧速率无关）。

如果用户第三个触摸中心的屏幕，然后照相机将向前移动。 这通过首先获取的向前向量，其最初定义为指向负 Y 轴，然后使用创建的矩阵的旋转`Matrix.CreateRotationZ`和`angle`值。 最后`forwardVector`应用于`position`使用`unitsPerSecond`系数。

## <a name="summary"></a>总结

本演练介绍如何移动和旋转`Models`在 3D 空间使用`Matrices`和`BasicEffect.World`属性。 移动此窗体的 3D 游戏中移动对象提供基础。 本演练还介绍了如何实现`Camera`用于查看世界各地任何位置和角度的实体。

## <a name="related-links"></a>相关链接

- [MonoGame API 链接](http://www.monogame.net/documentation/?page=api)
- [完成的项目 （示例）](https://developer.xamarin.com/samples/monodroid/MonoGame3DCamera/)
