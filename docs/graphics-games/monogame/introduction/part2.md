---
title: 第二部分-实现 WalkingGame
description: 本演练演示如何添加游戏逻辑和到空 MonoGame 项目创建与移动动画子画面的演示内容触摸屏输入。
ms.prod: xamarin
ms.assetid: F0622A01-DE7F-451A-A51F-129876AB6FFD
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 941b88f9109cf2f3a3485311c52b1250bd08e53f
ms.sourcegitcommit: 2ee36611ef667affee7d417db947fbb614d75315
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2019
ms.locfileid: "54479766"
---
# <a name="part-2--implementing-the-walkinggame"></a>第二部分-实现 WalkingGame

_本演练演示如何添加游戏逻辑和到空 MonoGame 项目创建与移动动画子画面的演示内容触摸屏输入。_

本演练前面部分介绍了如何创建空 MonoGame 项目。 通过进行简单的游戏演示，我们将生成这些前面部分上。 本文包含以下各节：

- 解压缩我们游戏的内容
- MonoGame 类概述
- 呈现我们第一个子画面
- 创建 CharacterEntity
- 向游戏添加 CharacterEntity
- 创建动画类
- 将第一个动画添加到 CharacterEntity
- 添加移动的字符
- 匹配的移动和动画


## <a name="unzipping-our-game-content"></a>解压缩我们游戏的内容

我们开始编写代码之前，我们想要将我们的游戏中解压缩*内容*。 游戏开发人员通常使用术语*内容*来引用其通常由 visual 艺术家、 游戏设计器或音频设计器创建非代码文件。 常见类型的内容包括用于显示视觉对象、 播放声音，或控制人工智能 (AI) 行为的文件。 从游戏开发团队的角度来看内容通常由非程序员创建。

可在此处使用的内容[github 上](https://github.com/xamarin/mobile-samples/blob/master/WalkingGameMG/Resources/charactersheet.png?raw=true)。 我们将需要这些文件下载到我们将稍后在本演练中访问的位置。

## <a name="monogame-class-overview"></a>MonoGame 类概述

对于初学者而言，我们将探讨基本呈现中使用的 MonoGame 类：

- `SpriteBatch` – 用于在屏幕上绘制 2D 图形。 *Sprite*是二维的可视元素用于在屏幕上显示的图像。 `SpriteBatch`对象之间的一次可以绘制单个 sprite 其`Begin`并`End`方法或多个子画面可以组合在一起，或者*批处理*。
- `Texture2D` – 表示在运行时的图像对象。 `Texture2D` 尽管它们也可以创建动态地在运行时，通常从如.png 或.bmp 文件格式创建实例。 `Texture2D` 与在呈现时，将使用实例`SpriteBatch`实例。
- `Vector2` – 表示通常用于定位视觉对象的二维坐标系统中的位置。 此外包括 MonoGame`Vector3`并`Vector4`但我们将仅使用`Vector2`在本演练中。
- `Rectangle` – 与位置、 宽度和高度的四个边的区域。 我们将使用此定义的哪个部分我们`Texture2D`时开始，我们将使用动画呈现。

我们还应注意，MonoGame 不由 Microsoft 维护 – 尽管其命名空间。 `Microsoft.Xna`命名空间使用 MonoGame 中，若要更加轻松地将现有的 XNA 项目迁移到 MonoGame。

## <a name="rendering-our-first-sprite"></a>呈现我们第一个子画面

接下来我们将单个 sprite 绘制到屏幕以显示如何在 MonoGame 中执行 2D 呈现。

### <a name="creating-a-texture2d"></a>创建 Texture2D

我们需要创建`Texture2D`以呈现我们 sprite 时所使用的实例。 所有游戏内容最终包含在名为的文件夹**内容，** 位于特定于平台的项目。 共享的 MonoGame 项目不能包含的内容，因为内容必须使用特定于平台的生成操作。 CocosSharp 开发人员会发现的 Content 文件夹的熟悉的概念 – 位于 CocosSharp 和 MonoGame 项目中的同一位置。 在 iOS 项目，并在 Android 项目中的资产文件夹内，可以找到的 Content 文件夹。

若要添加我们的游戏中的内容，请右键单击**内容**文件夹，然后选择**添加 > 添加文件...** 导航到已解压缩 content.zip 文件的位置并选择**charactersheet.png**文件。 如果系统询问有关如何将文件添加到文件夹，我们应选择**复制**选项：

![](part2-images/image1.png "如果系统询问有关如何将文件添加到文件夹，选择复制选项")

内容文件夹现在包含 charactersheet.png 文件：

![](part2-images/image2.png "内容文件夹现在包含 charactersheet.png 文件")

接下来，我们将添加代码来加载 charactersheet.png 文件并创建`Texture2D`。 为此，请打开`Game1.cs`文件，并将以下字段添加到 Game1.cs 类：

```csharp
Texture2D characterSheetTexture;
```

接下来，我们将创建`characterSheetTexture`在`LoadContent`方法。 任何修改之前`LoadContent`方法如下所示：

```csharp
protected override void LoadContent()
{
    // Create a new SpriteBatch, which can be used to draw textures.
    spriteBatch = new SpriteBatch(GraphicsDevice);
    // TODO: use this.Content to load your game content here
}
```

需要注意的是已经实例化的默认项目`spriteBatch`为我们的实例。 我们将使用此更高版本，但我们不会添加任何其他代码来准备`spriteBatch`供使用。 但是，我们`spriteSheetTexture`确实需要初始化，因此我们将初始化之后`spriteBatch`创建：

```csharp
protected override void LoadContent()
{
    // Create a new SpriteBatch, which can be used to draw textures.
    spriteBatch = new SpriteBatch(GraphicsDevice);
    using (var stream = TitleContainer.OpenStream ("Content/charactersheet.png"))
    {
        characterSheetTexture = Texture2D.FromStream (this.GraphicsDevice, stream);

    }
}
```

现在，我们已经`SpriteBatch`实例和一个`Texture2D`我们可以添加到我们呈现代码的实例`Game1.Draw`方法：

```csharp
protected override void Draw(GameTime gameTime)
{
    GraphicsDevice.Clear(Color.CornflowerBlue);

    spriteBatch.Begin ();

    Vector2 topLeftOfSprite = new Vector2 (50, 50);
    Color tintColor = Color.White;

    spriteBatch.Draw(characterSheetTexture, topLeftOfSprite, tintColor);

    spriteBatch.End ();

    base.Draw(gameTime);
}
```

现在运行游戏显示单个 sprite 显示 charactersheet.png 从创建的纹理：

![](part2-images/image3.png "现在运行游戏显示单个 sprite 显示 charactersheet.png 从创建的纹理")

## <a name="creating-the-characterentity"></a>创建 CharacterEntity

到目前为止我们已经添加了代码来呈现在屏幕上; 单个 sprite但是，如果我们要开发完整的游戏，而无需创建任何其他类，我们将会出现代码组织问题。

### <a name="what-is-an-entity"></a>实体是什么？

组织的游戏代码的常见模式是创建每个游戏的新类*实体*类型。 游戏开发中的实体是一个对象，它可以包含一些 （并非所有所需） 的以下特征：

- 在可视化元素，如子画面、 文本或三维模型
- 物理引擎或每个帧的行为，例如 patrolling 设置的路径或播放机字符响应任何输入的单元
- 可以创建和销毁动态，如强化显示和收集的播放机

实体组织系统可能很复杂，并且许多游戏引擎提供类，以帮助管理实体。 我们将实现一个非常简单的实体系统，因此值得注意的完整的游戏通常需要在开发人员更有条理。


### <a name="defining-the-characterentity"></a>定义 CharacterEntity

我们的实体，我们将其称为`CharacterEntity`，将具有以下特征：

- 加载其自身的功能 `Texture2D`
- 呈现自身，包括包含调用的方法来更新遍历动画的功能
- `X` 和 Y 属性来控制字符的位置。
- 若要更新本身-具体而言，若要读取值从触摸屏幕上，并相应地调整位置功能。

若要添加`CharacterEntity`我们的游戏中，右键单击或单击上**WalkingGame**项目，然后选择**添加 > 新建文件...**.选择**的空类**选项，然后将新文件命名**CharacterEntity**，然后单击**新建**。

首先，我们将添加的功能`CharacterEntity`加载`Texture2D`并绘制自身。 我们将修改新添加`CharacterEntity.cs`文件，如下所示：

```csharp
using System;
using Microsoft.Xna.Framework.Graphics;
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Input.Touch;


namespace WalkingGame
{
    public class CharacterEntity
    {
        static Texture2D characterSheetTexture;

        public float X
        {
            get;
            set;
        }

        public float Y
        {
            get;
            set;
        }

        public CharacterEntity (GraphicsDevice graphicsDevice)
        {
            if (characterSheetTexture == null)
            {
                using (var stream = TitleContainer.OpenStream ("Content/charactersheet.png"))
                {
                    characterSheetTexture = Texture2D.FromStream (graphicsDevice, stream);
                }
            }
        }

        public void Draw(SpriteBatch spriteBatch)
        {
            Vector2 topLeftOfSprite = new Vector2 (this.X, this.Y);
            Color tintColor = Color.White;
            spriteBatch.Draw(characterSheetTexture, topLeftOfSprite, tintColor);
        }
    }
}
```

上面的代码中添加定位、 呈现、 和加载到内容的责任`CharacterEntity`。 让我们看一段时间来指出的是上面的代码中执行的一些注意事项。

### <a name="why-are-x-and-y-floats"></a>为什么是 X 和 Y 浮点数？

新游戏编程的开发人员可能想知道为什么`float`而不是使用类型`int`或`double`。 原因是最常见的低级呈现代码中定位的 32 位值。 双精度类型占用 64 位精度，很少需要定位对象。 32 位差异可能看起来无意义，许多现代游戏包括图形需要处理数万个位置值 （30 个或每秒 60 次） 的每个帧。 剪切的内存量必须移动通过图形管道的下半部分会对游戏的性能产生重大影响。

`int`数据类型可以是合适的单元的度量进行定位，但它可以放置于实体的方式加以限制。 例如，使用整数值使其更难以实现平滑移动游戏支持放大或三维照相机 (这允许通过`SpriteBatch`)。

最后，我们将看到，后者将人物在屏幕上四处移动的逻辑将该操作使用游戏的计时值。 一个整数，因此我们需要使用将很少造成相乘速度在给定的框架中经过了多少时间的结果`float`有关`X`和`Y`。

### <a name="why-is-charactersheettexture-static"></a>为何 characterSheetTexture 静态？

`characterSheetTexture` `Texture2D`实例是 charactersheet.png 文件的运行时表示形式。 换而言之，它包含每个像素的颜色值从源中提取**charactersheet.png**文件。 如果要创建两个游戏`CharacterEntity`实例，则每个需要访问信息从 charactersheet.png。 在此情况下，我们不希望产生两次，加载 charactersheet.png 的性能成本，也不我们想要存储在 ram 中的重复纹理内存。 使用`static`字段可用于共享相同`Texture2D`跨所有`CharacterEntity`实例。

使用`static`内容的运行时表示的成员是一个简单的解决方案并不能解决时将其从一个级别移动到另一个更大的游戏，例如卸载内容中遇到的问题。 更复杂的解决方案，不在本演练的范围，包括使用 MonoGame 的内容管道或创建自定义内容管理系统。

### <a name="why-is-spritebatch-passed-as-an-argument-instead-of-instantiated-by-the-entity"></a>为什么这 SpriteBatch 传递自变量的 instead Of 实例化的实体。

`Draw`方法，如实施的上述采用`SpriteBatch`自变量，但相比之下`characterSheetTexture`通过实例化`CharacterEntity`。

这样做的原因是因为时最有效的呈现是可能相同`SpriteBatch`实例使用的所有`Draw`调用，以及何时所有`Draw`调用进行一组之间`Begin`和`End`调用。 当然，我们的游戏中将仅包括对单一实体实例，但更复杂的游戏将受益于允许多个实体使用相同的模式`SpriteBatch`实例。


## <a name="adding-characterentity-to-the-game"></a>向游戏添加 CharacterEntity

现在，我们添加了我们`CharacterEntity`，用于呈现自身的代码，我们可以替换中的代码`Game1.cs`以使用此新实体的实例。 若要这样做我们会将为`Texture2D`字段`CharacterEntity`字段中`Game1`:

```csharp
public class Game1 : Game
{
    GraphicsDeviceManager graphics;
    SpriteBatch spriteBatch;
    // New code:
    CharacterEntity character;

    public Game1()
    {
    ...
```

接下来，我们将添加中的此实体的实例化`Game1.Initialize`:

```csharp
protected override void Initialize()
{
    character = new CharacterEntity (this.GraphicsDevice);

    base.Initialize();
}
```

我们还需要删除`Texture2D`创建从`LoadContent`由于现在进行的内部处理我们`CharacterEntity`。 `Game1.LoadContent` 应如下所示：

```csharp
protected override void LoadContent()
{
    // Create a new SpriteBatch, which can be used to draw textures.
    spriteBatch = new SpriteBatch(GraphicsDevice);
}
```

值得注意的是，尽管其名称`LoadContent`方法不是在其中可以加载内容 – 许多游戏实现内容加载在其 Initialize 方法中，或在其更新代码作为内容，甚至可能不需要直到播放机的唯一位置游戏的特定点。

最后我们可以按如下所示修改 Draw 方法：


```csharp
protected override void Draw(GameTime gameTime)
{
    GraphicsDevice.Clear(Color.CornflowerBlue);

    // We'll start all of our drawing here:
    spriteBatch.Begin ();

    // Now we can do any entity rendering:
    character.Draw(spriteBatch);
    // End renders all sprites to the screen:
    spriteBatch.End ();

    base.Draw(gameTime);
}
```

如果我们运行该游戏，我们现在将看到该字符。 X 和 Y 默认值为 0，因为该字符是与屏幕的左上角进行定位：

![](part2-images/image4.png "由于 X 和 Y 默认值为 0，然后该字符位于对屏幕的左上角")

## <a name="creating-the-animation-class"></a>创建动画类

目前我们`CharacterEntity`显示完整**charactersheet.png**文件。 在一个文件中的多个映像的这种安排称为*子画面表单*。 通常情况下，子画面将呈现仅为一部分子画面表单。 我们将修改`CharacterEntity`呈现的这一部分**charactersheet.png**，和此部分会随时间才会显示一个遍历的动画。

我们将创建`Animation`类来控制的逻辑和 CharacterEntity 动画的状态。 动画类将是一个常规类，该类可以用于任何实体，而不仅仅是`CharacterEntity`动画。 Ultimate`Animation`类将提供`Rectangle`其`CharacterEntity`绘制自身时将使用。 我们还会创建`AnimationFrame`类，用于定义动画。


### <a name="defining-animationframe"></a>定义 AnimationFrame

`AnimationFrame` 将不包含与动画相关的任何逻辑。 我们将使用它只用来存储数据。 若要添加`AnimationFrame`类中，右键单击或单击上**WalkingGame**共享项目，然后选择**添加 > 新建文件...** 输入的名称**AnimationFrame**然后单击**新建**按钮。 我们将修改`AnimationFrame.cs`文件，使其包含以下代码：


```csharp
using System;
using Microsoft.Xna.Framework;

namespace WalkingGame
{
    public class AnimationFrame
    {
        public Rectangle SourceRectangle { get; set; }
        public TimeSpan Duration { get; set; }
    }
}
```

`AnimationFrame`类包含两条信息：

- `SourceRectangle` – 定义的区域`Texture2D`这将显示的`AnimationFrame`。 此值以像素为单位，其中顶部左侧是 （0，0）。
- `Duration` – 定义长`AnimationFrame`中使用时显示`Animation`。

### <a name="defining-animation"></a>定义动画

`Animation`类将包含`List<AnimationFrame>`以及切换经过多少时间根据当前显示的帧的逻辑。

若要添加`Animation`类中，右键单击或单击上**WalkingGame**共享项目，然后选择**添加 > 新建文件...**.输入的名称**动画**然后单击**新建**按钮。 我们将修改`Animation.cs`文件使其包含以下代码：


```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using Microsoft.Xna.Framework;

namespace WalkingGame
{
    public class Animation
    {
        List<AnimationFrame> frames = new List<AnimationFrame>();
        TimeSpan timeIntoAnimation;

        TimeSpan Duration
        {
            get
            {
                double totalSeconds = 0;
                foreach (var frame in frames)
                {
                    totalSeconds += frame.Duration.TotalSeconds;
                }

                return TimeSpan.FromSeconds (totalSeconds);
            }
        }

        public void AddFrame(Rectangle rectangle, TimeSpan duration)
        {
            AnimationFrame newFrame = new AnimationFrame()
            {
                SourceRectangle = rectangle,
                Duration = duration
            };

            frames.Add(newFrame);
        }

        public void Update(GameTime gameTime)
        {
            double secondsIntoAnimation = 
                timeIntoAnimation.TotalSeconds + gameTime.ElapsedGameTime.TotalSeconds;


            double remainder = secondsIntoAnimation % Duration.TotalSeconds;

            timeIntoAnimation = TimeSpan.FromSeconds (remainder);
        }
    }
}
```

让我们看一些的详细信息`Animation`类。

### <a name="frames-list"></a>帧列表

`frames`成员是将我们的动画数据的存储。 它会实例化动画的代码将添加`AnimationFrame`到实例`frames`列出通过`AddFrame`方法。 可能会提供的更完整实现`public`方法或属性用于修改`frames`，但我们将仅添加在本演练中的帧的功能。

### <a name="duration"></a>持续时间

持续时间返回的总持续时间`Animation,`从中获取通过添加包含所有的持续时间`AnimationFrame`实例。 无法缓存此值，如果`AnimationFrame`不可变对象，但由于我们 AnimationFrame 作为类实现可添加到动画后更改，因此我们需要计算此值，只要访问的属性。

### <a name="update"></a>更新

`Update`方法应调用每个帧 （即，每次更新整个游戏）。 其目的是提高`timeIntoAnimation`用于返回当前显示的帧的成员。 中的逻辑`Update`可防止`timeIntoAnimation`曾经不大于整个动画的持续时间。

由于我们将使用`Animation`类，以显示遍历动画，则我们想要让它已到达末尾时，重复此动画。 我们可以实现此目的通过检查是否`timeIntoAnimation`大于`Duration`值。 如果是这样它将返回到开始循环，并保留其余部分，从而导致循环的动画。

### <a name="obtaining-the-current-frames-rectangle"></a>获取当前帧的矩形

目的`Animation`类是最终提供`Rectangle`绘制 sprite 时，可以使用的。 目前`Animation`类包含用于更改逻辑`timeIntoAnimation`成员，我们将使用以获取该`Rectangle`应显示。 我们将执行此操作通过创建`CurrentRectangle`中的属性`Animation`类。 将复制到此属性`Animation`类：

```csharp
public Rectangle CurrentRectangle
{
    get
    {
        AnimationFrame currentFrame = null;

        // See if we can find the frame
        TimeSpan accumulatedTime = new TimeSpan();
        foreach(var frame in frames)
        {
            if (accumulatedTime + frame.Duration >= timeIntoAnimation)
            {
                currentFrame = frame;
                break;
            }
            else
            {
                accumulatedTime += frame.Duration;
            }
        }

        // If no frame was found, then try the last frame, 
        // just in case timeIntoAnimation somehow exceeds Duration
        if (currentFrame == null)
        {
            currentFrame = frames.LastOrDefault ();
        }

        // If we found a frame, return its rectangle, otherwise
        // return an empty rectangle (one with no width or height)
        if (currentFrame != null)
        {
            return currentFrame.SourceRectangle;
        }
        else
        {
            return Rectangle.Empty;
        }
    }
}
```

## <a name="adding-the-first-animation-to-characterentity"></a>将第一个动画添加到 CharacterEntity

`CharacterEntity`将包含的每个步骤和位置，以及对当前的引用的动画效果`Animation`显示。

首先，我们将添加第一个`Animation,`该选项将测试的功能，如当前编写。 让我们将以下成员添加到 CharacterEntity 类：

```csharp
Animation walkDown;
Animation currentAnimation;
```

接下来，让我们来定义`walkDown`动画。 若要执行此修改`CharacterEntity`构造函数，如下所示：

```csharp
public CharacterEntity (GraphicsDevice graphicsDevice)
{
    if (characterSheetTexture == null)
    {
        using (var stream = TitleContainer.OpenStream ("Content/charactersheet.png"))
        {
            characterSheetTexture = Texture2D.FromStream (graphicsDevice, stream);
        }
    }

    walkDown = new Animation ();
    walkDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (16, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (32, 0, 16, 16), TimeSpan.FromSeconds (.25));
}
```

前面曾提到，我们需要调用`Animation.Update`的基于时间的动画播放。 我们仍需分配`currentAnimation`。 对于现在我们将进行指派`currentAnimation`到`walkDown`，但我们将会替换这些代码，更高版本时，我们实现我们移动逻辑。 我们将添加`Update`方法`CharacterEntity`，如下所示：


```csharp
public void Update(GameTime gameTime)
{
    // temporary - we'll replace this with logic based off of which way the
    // character is moving when we add movement logic
    currentAnimation = walkDown;

    currentAnimation.Update (gameTime);
}
```

现在，我们已经`currentAnimation`正在分配和更新，我们可以使用它来执行我们绘制。 我们将修改`CharacterEntity.Draw`，如下所示：

```csharp
public void Draw(SpriteBatch spriteBatch)
{
    Vector2 topLeftOfSprite = new Vector2 (this.X, this.Y);
    Color tintColor = Color.White;
    var sourceRectangle = currentAnimation.CurrentRectangle;

    spriteBatch.Draw(characterSheetTexture, topLeftOfSprite, sourceRectangle, Color.White);
}
```

获取的最后一步`CharacterEntity`进行动画处理是调用新添加`Update`方法从`Game1`。 修改`Game1.Update`，如下所示：

```csharp
protected override void Update(GameTime gameTime)
{
    character.Update (gameTime);
    base.Update(gameTime);
}
```

现在`CharacterEntity`将播放其`walkDown`动画：

![](part2-images/image5.gif "现在 CharacterEntity 将播放其 walkDown 动画")

## <a name="adding-movement-to-the-character"></a>添加移动的字符

接下来，我们将为我们字符使用触控控件添加移动。 在用户触摸屏幕，该字符将向其中接触屏幕的点。 如果检测不到任何收尾工作了，该字符将位置内进行构建。

### <a name="defining-getdesiredvelocityfrominput"></a>定义 GetDesiredVelocityFromInput

我们将使用 MonoGame 的`TouchPanel`类，该类提供触摸屏的当前状态有关的信息。 让我们添加一种方法可将检查`TouchPanel`并返回我们字符所需的速度：


```csharp
Vector2 GetDesiredVelocityFromInput()
{
    Vector2 desiredVelocity = new Vector2 ();

    TouchCollection touchCollection = TouchPanel.GetState();

    if (touchCollection.Count > 0)
    {
        desiredVelocity.X = touchCollection [0].Position.X - this.X;
        desiredVelocity.Y = touchCollection [0].Position.Y - this.Y;

        if (desiredVelocity.X != 0 || desiredVelocity.Y != 0)
        {
            desiredVelocity.Normalize();
            const float desiredSpeed = 200;
            desiredVelocity *= desiredSpeed;
        }
    }

    return desiredVelocity;
}
```

`TouchPanel.GetState`方法将返回`TouchCollection`其中包含有关用户触摸屏幕的位置信息。 如果用户不触摸屏幕，则`TouchCollection`将为空，这种情况下，我们不应将字符。

如果用户触摸屏幕，我们将针对第一次触摸，字符换而言之，则`TouchLocation`位于索引 0 处。 最初，我们将设置所需的速度为等于该字符的位置和第一次触摸的位置之间的差异：

```csharp
        desiredVelocity.X = touchCollection [0].Position.X - this.X;
        desiredVelocity.Y = touchCollection [0].Position.Y - this.Y;
```

后面是数学这可以使移动的字符保持相同速度的位。 为了帮助说明为什么这很重要，让我们考虑用户触摸屏幕 500 像素离开该字符所在的位置的其中一种情况。 第一个行`desiredVelocity.X`是组会分配值为 500。 但是，如果用户已触摸屏幕的字符，从 100 个单位距离则`desiredVelocity.X `将设置为 100。 结果将为您的人物的移动速度会响应如何距离遥远的触摸点是从字符。 由于我们想要始终以相同的速度移动的字符，我们需要修改 desiredVelocity。

`if (desiredVelocity.X != 0 || desiredVelocity.Y != 0)`语句的作用检查方向的速度是非-零 – 换句话说，是否它检查以确保用户不接触字符的当前位置为相同的位置。 如果不是，然后我们需要设置人物的速度远为常量而不考虑如何触摸屏输入。 我们完成此操作通过规范化速度向量，这将使其在长度为 1。 1 表示字符将在每秒 1 像素移动速度向量。 我们将加快此速度，通过将值乘以所需的速度为 200。


### <a name="applying-velocity-to-position"></a>应用到的位置的速度

从返回的速度`GetDesiredVelocityFromInput`需要将应用到的字符`X`和`Y`值，以在运行时产生任何影响。 我们将修改`Update`方法，如下所示：

```csharp
public void Update(GameTime gameTime)
{

    var velocity = GetDesiredVelocityFromInput ();

    this.X += velocity.X * (float)gameTime.ElapsedGameTime.TotalSeconds;
    this.Y += velocity.Y * (float)gameTime.ElapsedGameTime.TotalSeconds;

    // temporary - we'll replace this with logic based off of which way the
    // character is moving when we add movement logic
    currentAnimation = walkDown;

    currentAnimation.Update (gameTime);
}
```

我们已实现称为*基于时间*移动 (而不是*基于帧的*移动)。 基于时间的移动乘以速度值 (在本例中的值存储在`velocity`变量) 的上次更新存储在后经过了多少时间`gameTime.ElapsedGameTime.TotalSeconds`。 如果在每秒帧数较少运行游戏时，帧之间经过的时间转到上 – 最终结果是使用基于时间的移动对象始终将以同一速度而不考虑帧速率。

如果我们现在运行我们的游戏中，我们将看到的字符是转移到触摸位置：

![](part2-images/image6.gif "字符位移至触摸位置")

## <a name="matching-movement-and-animation"></a>匹配的移动和动画

人物移动和播放单个动画后，我们可以定义我们动画的其余部分，然后使用它们以反映移动的字符。 当完成我们将有八个动画总数：

- 向上，向下、 左和右的动画效果
- 现有的动画仍和正面朝上向下，左右，

### <a name="defining-the-rest-of-the-animations"></a>定义动画的其余部分

首先，我们将添加`Animation`到实例`CharacterEntity`类的所有相同的位置，我们添加了我们动画`walkDown`。 在我们完成此操作，请`CharacterEntity`将具有以下`Animation`成员：

```csharp
Animation walkDown;
Animation walkUp;
Animation walkLeft;
Animation walkRight;

Animation standDown;
Animation standUp;
Animation standLeft;
Animation standRight;

Animation currentAnimation;
```

现在，我们将定义中的动画`CharacterEntity`构造函数，如下所示：

```csharp
public CharacterEntity (GraphicsDevice graphicsDevice)
{
    if (characterSheetTexture == null)
    {
        using (var stream = TitleContainer.OpenStream ("Content/charactersheet.png"))
        {
            characterSheetTexture = Texture2D.FromStream (graphicsDevice, stream);
        }
    }

    walkDown = new Animation ();
    walkDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (16, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (32, 0, 16, 16), TimeSpan.FromSeconds (.25));

    walkUp = new Animation ();
    walkUp.AddFrame (new Rectangle (144, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkUp.AddFrame (new Rectangle (160, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkUp.AddFrame (new Rectangle (144, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkUp.AddFrame (new Rectangle (176, 0, 16, 16), TimeSpan.FromSeconds (.25));

    walkLeft = new Animation ();
    walkLeft.AddFrame (new Rectangle (48, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkLeft.AddFrame (new Rectangle (64, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkLeft.AddFrame (new Rectangle (48, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkLeft.AddFrame (new Rectangle (80, 0, 16, 16), TimeSpan.FromSeconds (.25));

    walkRight = new Animation ();
    walkRight.AddFrame (new Rectangle (96, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkRight.AddFrame (new Rectangle (112, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkRight.AddFrame (new Rectangle (96, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkRight.AddFrame (new Rectangle (128, 0, 16, 16), TimeSpan.FromSeconds (.25));

    // Standing animations only have a single frame of animation:
    standDown = new Animation ();
    standDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));

    standUp = new Animation ();
    standUp.AddFrame (new Rectangle (144, 0, 16, 16), TimeSpan.FromSeconds (.25));

    standLeft = new Animation ();
    standLeft.AddFrame (new Rectangle (48, 0, 16, 16), TimeSpan.FromSeconds (.25));

    standRight = new Animation ();
    standRight.AddFrame (new Rectangle (96, 0, 16, 16), TimeSpan.FromSeconds (.25));
}
```

我们应注意上面的代码已添加到`CharacterEntity`构造函数，以使本演练中较短。 游戏通常将分隔字符动画添加到其自己的类的定义，或从数据格式，如 XML 或 JSON 加载此信息。

接下来，我们将调整的逻辑，如果字符已只是停止使用根据方向移动的字符，或根据最后一个动画的动画。 若要执行此操作，我们将修改`Update`方法：


```csharp
public void Update(GameTime gameTime)
{
    var velocity = GetDesiredVelocityFromInput ();

    this.X += velocity.X * (float)gameTime.ElapsedGameTime.TotalSeconds;
    this.Y += velocity.Y * (float)gameTime.ElapsedGameTime.TotalSeconds;


    if (velocity != Vector2.Zero)
    {
        bool movingHorizontally = Math.Abs (velocity.X) > Math.Abs (velocity.Y);
        if (movingHorizontally)
        {
            if (velocity.X > 0)
            {
                currentAnimation = walkRight;
            }
            else
            {
                currentAnimation = walkLeft;
            }
        }
        else
        {
            if (velocity.Y > 0)
            {
                currentAnimation = walkDown;
            }
            else
            {
                currentAnimation = walkUp;
            }
        }
    }
    else
    {
        // If the character was walking, we can set the standing animation
        // according to the walking animation that is playing:
        if (currentAnimation == walkRight)
        {
            currentAnimation = standRight;
        }
        else if (currentAnimation == walkLeft)
        {
            currentAnimation = standLeft;
        }
        else if (currentAnimation == walkUp)
        {
            currentAnimation = standUp;
        }
        else if (currentAnimation == walkDown)
        {
            currentAnimation = standDown;
        }
        else if (currentAnimation == null)
        {
            currentAnimation = standDown;
        }

        // if none of the above code hit then the character
        // is already standing, so no need to change the animation.
    }

    currentAnimation.Update (gameTime);
}
```

若要切换动画的代码分解为两个块。 第一个检查方向的速度是否不等于`Vector2.Zero`– 这将告诉我们的字符是否是移动。 如果该字符被移动，则我们可以看看`velocity.X`和`velocity.Y`值，以确定哪些遍历动画播放。

如果字符没有移动，则我们想要设置的字符`currentAnimation`到一个现有的动画 – 但我们仅执行此操作如果`currentAnimation`是遍历动画或如果未设置动画。 如果`currentAnimation`不是一个四个遍历动画，则已站着字符，因此我们无需更改`currentAnimation`。

此代码的结果是字符将正确进行动画处理时的每个步骤，且然后人脸走它停止时的最后一个方向：

![](part2-images/image7.gif "此代码的结果是字符将正确进行动画处理时的每个步骤，且然后人脸走它停止时的最后一个方向")

## <a name="summary"></a>总结

本演练演示了如何使用 MonoGame 创建跨平台游戏使用 sprite、 移动对象、 输入的检测和动画。 它介绍如何创建常规用途的动画类。 它还介绍了如何创建用于组织代码逻辑的字符实体。

## <a name="related-links"></a>相关链接

- [CharacterSheet 图像资源 （示例）](https://github.com/xamarin/mobile-samples/blob/master/WalkingGameMG/Resources/charactersheet.png?raw=true)
- [每个步骤完成游戏 （示例）](https://developer.xamarin.com/samples/mobile/WalkingGameMG/)
