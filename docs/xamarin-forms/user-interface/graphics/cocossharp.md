---
title: 在 Xamarin.Forms 中使用 CocosSharp
description: CocosSharp 可用于将精确形状、 图像和文本呈现添加到用于高级可视化效果的应用程序
ms.prod: xamarin
ms.assetid: E0F404D5-5C6B-4288-92EC-78996C674E4E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/03/2016
ms.openlocfilehash: 8ba9e4b119384db401fc631f58c37a28cd2b8004
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76724422"
---
# <a name="using-cocossharp-in-xamarinforms"></a>在 Xamarin.Forms 中使用 CocosSharp

[![下载示例](~/media/shared/download.png) 下载示例](https://github.com/xamarin/xamarin-forms-samples/tree/master/CocosSharpForms)

_CocosSharp 可用于将精确的形状、图像和文本呈现添加到应用程序以实现高级可视化_

> [!VIDEO https://youtube.com/embed/eYCx63FeqVU]

**演化2016： Xamarin 中的科科斯 #**

## <a name="overview"></a>概述

CocosSharp 是一种灵活且功能强大的技术，用于显示图形、 读取触摸输入、 播放音频和管理内容。 本指南介绍如何将 CocosSharp 添加到 Xamarin.Forms 应用程序。 它涵盖以下产品：

- [什么是 CocosSharp？](#what)
- [添加 CocosSharp NuGet 包](#nuget)
- [演练：向 Xamarin 应用程序添加 CocosSharp](#add)

<a name="what" />

## <a name="what-is-cocossharp"></a>CocosSharp 是什么？

[CocosSharp](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/index.md)是一个开源游戏引擎，可在 Xamarin 平台上使用。
CocosSharp 是一个高效运行时库，其中包括以下功能：

- 使用 `CCSprite` 类的图像呈现
- 使用 `CCDrawNode` 类的形状呈现
- 使用 `CCNode.Schedule` 类的每帧逻辑
- 使用 `CCTextureCache` 的内容管理（加载和卸载资源，如 .png 文件）
- 使用 `CCAction` 类的动画

CocosSharp 的主要重点是简化的跨平台 2D 游戏; 创建但是，它也可以是到 Xamarin 窗体应用程序的极好补充。 由于游戏通常需要的高效绘制和视觉对象的精确控制，可以使用 CocosSharp 向非游戏应用程序添加功能强大的可视化和效果。

Xamarin.Forms 是本机、 特定于平台的 UI 系统根据生成的。 例如， [`Button`s](xref:Xamarin.Forms.Button)在 IOS 和 Android 上以不同的方式显示，甚至可能因操作系统版本而异。 与此相反，CocosSharp 不使用任何特定于平台的视觉对象，因此所有视觉对象会显示在所有平台上完全相同。 当然，解决方法和纵横比不同设备之间，这可能会影响 CocosSharp 如何呈现其视觉对象。 本指南后面将讨论这些详细信息。

可在[CocosSharp 部分](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/index.md)找到更多详细信息。

<a name="nuget" />

## <a name="adding-the-cocossharp-nuget-packages"></a>添加 CocosSharp NuGet 包

然后再使用 CocosSharp，开发人员需要进行一些添加到自己的 Xamarin.Forms 项目。
本指南假定一个 Xamarin.Forms 项目，与 iOS、 Android 和.NET Standard 类库项目。
.NET Standard 库项目中; 将写入的所有代码但是，库必须添加到 iOS 和 Android 项目。

CocosSharp NuGet 包包含创建 CocosSharp 对象所需的所有对象。
CocosSharp NuGet 包包含用于在 Xamarin 中承载 CocosSharp 的 `CocosSharpView` 类。
添加**CocosSharp** NuGet 和**CocosSharp**也会自动添加。
为此，请右键单击 .NET Standard 库项目中的 "**包**" 文件夹，然后选择 "**添加包 ...** "。输入搜索词**CocosSharp**，**为 Xamarin 选择 CocosSharp**，然后单击 "**添加包**"。

![](cocossharp-images/image1.png "Add Packages Dialog")

**CocosSharp**和**CocosSharp** NuGet 包都将添加到项目中：

![](cocossharp-images/image2.png "Packages Folder")

对于特定于平台的项目 （如 iOS 和 Android） 重复上述步骤。

<a name="add" />

## <a name="walkthrough-adding-cocossharp-to-a-xamarinforms-app"></a>演练： 向 Xamarin.Forms 应用添加 CocosSharp

请按照下列步骤将简单 CocosSharp 视图添加到 Xamarin.Forms 应用：

1. [创建 Xamarin 窗体页](#1)
1. [添加 CocosSharpView](#2)
1. [创建 GameScene](#3)
1. [添加圆形](#4)
1. [与 CocosSharp 交互](#5)

将 CocosSharp 视图成功添加到 Xamarin 应用后，请访问[CocosSharp 文档](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/index.md)以了解有关使用 CocosSharp 创建内容的详细信息。

<a name="1" />

### <a name="1-creating-a-xamarin-forms-page"></a>1. 创建 Xamarin 窗体页

CocosSharp 可以托管在任何 Xamarin.Forms 容器。 此页面的示例使用名为 `HomePage`的页面。 `HomePage` 按 `Grid` 分为两半，以显示如何在同一页上同时呈现 Xamarin 和 CocosSharp。

首先，设置页面，使其包含 `Grid` 和两个 `Button` 实例：

```csharp
public class HomePage : ContentPage
{
public HomePage ()
    {
        // This is the top-level grid, which will split our page in half
        var grid = new Grid ();
        this.Content = grid;
        grid.RowDefinitions = new RowDefinitionCollection {
            // Each half will be the same size:
            new RowDefinition{ Height = new GridLength(1, GridUnitType.Star)},
            new RowDefinition{ Height = new GridLength(1, GridUnitType.Star)},
        };
        CreateTopHalf (grid);
        CreateBottomHalf (grid);
    }
    void CreateTopHalf(Grid grid)
    {
        // We'll be adding our CocosSharpView here:
    }
    void CreateBottomHalf(Grid grid)
    {
        // We'll use a StackLayout to organize our buttons
        var stackLayout = new StackLayout();
        // The first button will move the circle to the left when it is clicked:
        var moveLeftButton = new Button {
            Text = "Move Circle Left"
        };
        stackLayout.Children.Add (moveLeftButton);

        // The second button will move the circle to the right when clicked:
        var moveCircleRight = new Button {
            Text = "Move Circle Right"
        };
        stackLayout.Children.Add (moveCircleRight);
        // The stack layout will be in the bottom half (row 1):

        grid.Children.Add (stackLayout, 0, 1);
    }
}
```

在 iOS 上，`HomePage` 如下图所示：

![](cocossharp-images/image3.png "HomePage Screenshot")

<a name="2" />

### <a name="2-adding-a-cocossharpview"></a>2. 添加 CocosSharpView

`CocosSharpView` 类用于将 CocosSharp 嵌入 Xamarin 应用程序。 由于 `CocosSharpView` 继承自[Xamarin](xref:Xamarin.Forms.View)类，因此它为布局提供熟悉的界面，可在布局容器（如[Xamarin](xref:Xamarin.Forms.Grid)）中使用。 通过完成 `CreateTopHalf` 方法，向项目添加新的 `CocosSharpView`：

```csharp
void CreateTopHalf(Grid grid)
{
    // This hosts our game view.
    var gameView = new CocosSharpView () {
        // Notice it has the same properties as other XamarinForms Views
        HorizontalOptions = LayoutOptions.FillAndExpand,
        VerticalOptions = LayoutOptions.FillAndExpand,
        // This gets called after CocosSharp starts up:
        ViewCreated = HandleViewCreated
    };
    // We'll add it to the top half (row 0)
    grid.Children.Add (gameView, 0, 0);
}
```

CocosSharp 初始化不是即时的，因此在 `CocosSharpView` 完成创建时，为注册事件。 请在 `HandleViewCreated` 方法中执行此操作：

```csharp
void HandleViewCreated (object sender, EventArgs e)
{
    var gameView = sender as CCGameView;
    if (gameView != null)
    {
        // This sets the game "world" resolution to 100x100:
        gameView.DesignResolution = new CCSizeI (100, 100);
        // GameScene is the root of the CocosSharp rendering hierarchy:
        gameScene = new GameScene (gameView);
        // Starts CocosSharp:
        gameView.RunWithScene (gameScene);
    }
}
```

`HandleViewCreated` 方法有两个要查看的重要详细信息。 第一个是 `GameScene` 类，将在下一节中创建。 需要注意的是，在创建 `GameScene` 和解析 `gameScene` 实例引用之前，应用不会进行编译。

第二个重要的详细信息是 `DesignResolution` 属性，该属性定义游戏的 CocosSharp 对象的可见区域。 创建 `GameScene`后，将查看 `DesignResolution` 属性。

<a name="3" />

### <a name="3-creating-the-gamescene"></a>3. 创建 GameScene

`GameScene` 类继承自 CocosSharp 的 `CCScene`。 `GameScene` 是我们仅处理 CocosSharp 的第一个点。 `GameScene` 中包含的代码将在任何 CocosSharp 应用程序中运行，无论它是否位于 Xamarin 项目中。

`CCScene` 类是所有 CocosSharp 呈现的视觉对象根。 任何可见的 CocosSharp 对象都必须包含在 `CCScene`中。 更具体地说，必须将视觉对象添加到 `CCLayer` 实例，并且必须将这些 `CCLayer` 实例添加到 `CCScene`中。

下图可帮助直观显示典型 CocosSharp 层次结构：

![](cocossharp-images/image4.png "Typical CocosSharp Hierarchy")

一次只能有一个 `CCScene` 处于活动状态。 大多数游戏使用多个 `CCLayer` 实例对内容进行排序，但我们的应用程序只使用一个。 同样，大多数游戏程序使用多个视觉对象，但我们将仅有一个在我们的应用程序中。 有关 CocosSharp 可视层次结构的更详细的讨论，请参阅[BouncingGame 演练](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/bouncing-game.md)。

最初，`GameScene` 类将为空，只需创建它来满足 `HomePage`中的引用。 将新类添加到名为 `GameScene`的 .NET Standard 库项目。 它应继承自 `CCScene` 类，如下所示：

```csharp
public class GameScene : CCScene
{
    public GameScene (CCGameView gameView) : base(gameView)
    {

    }
}
```

定义 `GameScene` 后，可以返回到 `HomePage` 并添加一个字段：

```csharp
// Keep the GameScene at class scope
// so the button click events can access it:
GameScene gameScene;
```

我们现在可以编译我们的项目，并运行它以查看 CocosSharp 运行。 我们尚未将任何内容添加到我们的 `GameScene,`，因此我们的页面的上半部分为黑色– CocosSharp 场景的默认颜色：

![](cocossharp-images/image5.png "Blank GameScene")

<a name="4" />

### <a name="4-adding-a-circle"></a>4. 添加圆圈

应用当前具有 CocosSharp 引擎的运行实例，并显示空 `CCScene`。 接下来，我们将添加视觉对象： 一个圆圈。 `CCDrawNode` 类可用于绘制各种几何形状，如[使用 CCDrawNode 的绘图几何图形](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/ccdrawnode.md)中所述。

将一个圆圈添加到 `GameScene` 类，并在构造函数中对其进行实例化，如以下代码所示：

```csharp
public class GameScene : CCScene
{
    CCDrawNode circle;
    public GameScene (CCGameView gameView) : base(gameView)
    {
        var layer = new CCLayer ();
        this.AddLayer (layer);
        circle = new CCDrawNode ();
        layer.AddChild (circle);
        circle.DrawCircle (
            // The center to use when drawing the circle,
            // relative to the CCDrawNode:
            new CCPoint (0, 0),
            radius:15,
            color:CCColor4B.White);
        circle.PositionX = 20;
        circle.PositionY = 50;
    }
}
```

现在运行该应用程序的 CocosSharp 显示区域的左侧显示一个圆圈：

![](cocossharp-images/image6.png "Circle in GameScene")

#### <a name="understanding-designresolution"></a>了解 DesignResolution

由于显示了一个 visual CocosSharp 对象，我们可以调查 `DesignResolution` 属性。

`DesignResolution` 表示用于放置和调整对象大小的 CocosSharp 区域的宽度和高度。 区域的实际分辨率以*像素*为单位进行度量，而 `DesignResolution` 是以世界*单位*度量的。 下图显示的视图显示在 iPhone 5 640 x 1136 像素的屏幕分辨率的不同部件的解决方法：

![](cocossharp-images/image7.png "iPhone 5s Design Resolution")

上面的关系图上以黑色文本屏幕的外部显示像素尺寸。 单位显示白色文本中的关系图的内侧。 下面是一些重要的详细信息，如上所示：

- CocosSharp 显示原点位于左下角。 向右移动可增加的 X 值，并向上移动可增加的 Y 值。 请注意反转的 Y 值与某些其他 2D 布局引擎相比，(0，0) 是在画布左上方。
- CocosSharp 的默认行为是保持其视图的纵横比。 由于在网格中的第一行是宽度大于高度，CocosSharp 未填充其单元格的整个宽度以点分隔的白色矩形所示。 此行为可以更改，如在[CocosSharp 中处理多个解决方案中](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/resolutions.md)所述。
- 在此示例中，CocosSharp 将保留 100 个单位的宽度和高度而不考虑大小的显示区域或其设备的纵横比。 这意味着，代码可以假定，X = 100 表示最右侧绑定 CocosSharp 的显示，请保留布局在所有设备上保持一致。

#### <a name="ccdrawnode-details"></a>CCDrawNode 的详细信息

我们的简单应用使用 `CCDrawNode` 类来绘制一个圆。 此类可以是对业务应用程序非常有用，因为它提供基于矢量的几何图形渲染-Xamarin.Forms 中缺少的功能。 除了圆圈外，`CCDrawNode` 类还可用于绘制矩形、样条、直线和自定义多边形。 `CCDrawNode` 也易于使用，因为它不需要使用图像文件（如 .png）。 有关 CCDrawNode 的更详细讨论，请参阅[使用 CCDrawNode 指南绘制几何](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/ccdrawnode.md)。

<a name="5" />

### <a name="5-interacting-with-cocossharp"></a>5. 与 CocosSharp 交互

CocosSharp 视觉元素（如 `CCDrawNode`）继承自 `CCNode` 类。 `CCNode` 提供了两个属性，这些属性可用于相对于其父级定位对象： `PositionX` 和 `PositionY`。 我们的代码当前使用这两个属性来定位的中心圆，此代码片段中所示：

```csharp
circle.PositionX = 20;
circle.PositionY = 50;
```

请务必注意 CocosSharp 对象位于通过显式位置值，而不是大多数 Xamarin.Forms 视图，可自动定位根据其父布局控件的行为。

我们将添加代码以允许用户单击其中一个由 10 个单位 （不是以像素，因为该圆形绘制 CocosSharp 世界单位空间中） 向左或向右移动该圆圈的两个按钮。 首先，我们将在 `GameScene` 类中创建两个公共方法：

```csharp
public void MoveCircleLeft()
{
    circle.PositionX -= 10;
}

public void MoveCircleRight()
{
    circle.PositionX += 10;
}
```

接下来，将处理程序添加到 `HomePage` 中的两个按钮，以响应单击。 完成后，我们的 `CreateBottomHalf` 方法包含以下代码：

```csharp
void CreateBottomHalf(Grid grid)
{
    // We'll use a StackLayout to organize our buttons
    var stackLayout = new StackLayout();

    // The first button will move the circle to the left when it is clicked:
    var moveLeftButton = new Button {
        Text = "Move Circle Left"
    };
    moveLeftButton.Clicked += (sender, e) => gameScene.MoveCircleLeft ();
    stackLayout.Children.Add (moveLeftButton);

    // The second button will move the circle to the right when clicked:
    var moveCircleRight = new Button {
        Text = "Move Circle Right"
    };
    moveCircleRight.Clicked += (sender, e) => gameScene.MoveCircleRight ();
    stackLayout.Children.Add (moveCircleRight);

    // The stack layout will be in the bottom half (row 1):
    grid.Children.Add (stackLayout, 0, 1);
}
```

现在单击响应移动 CocosSharp 圆圈。 我们可以通过将圆形移到左侧或右侧的足够的距离也清楚地看到 CocosSharp 画布的边界：

![](cocossharp-images/image8.png "GameScene with Moving Circle")

## <a name="summary"></a>摘要

本指南演示如何将 CocosSharp 添加到现有 Xamarin.Forms 项目，如何创建 Xamarin.Forms 与 CocosSharp，之间的交互，并讨论了各种注意事项，在 CocosSharp 中创建布局时。

CocosSharp 游戏引擎提供了大量功能和深度，因此，本指南仅讲述了 CocosSharp 可以执行的操作。 对详细了解 CocosSharp 感兴趣的开发人员可以在[CocosSharp 存档](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/)中找到很多文章。

## <a name="related-links"></a>相关链接

- [CocosSharpForms （示例）](https://github.com/xamarin/xamarin-forms-samples/tree/master/CocosSharpForms)
