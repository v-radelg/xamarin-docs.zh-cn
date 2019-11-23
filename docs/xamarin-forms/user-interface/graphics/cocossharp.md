---
title: 在 Xamarin.Forms 中使用 CocosSharp
description: CocosSharp 可用于将精确形状、 图像和文本呈现添加到用于高级可视化效果的应用程序
ms.prod: xamarin
ms.assetid: E0F404D5-5C6B-4288-92EC-78996C674E4E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/03/2016
ms.openlocfilehash: 7b465391958a6e862bfed9fde8d9da1fdd52bee5
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70759755"
---
# <a name="using-cocossharp-in-xamarinforms"></a>在 Xamarin.Forms 中使用 CocosSharp

[![下载示例](~/media/shared/download.png) 下载示例](h https://github.com/xamarin/xamarin-forms-samples/tree/master/CocosSharpForms)

_CocosSharp 可用于将精确形状、 图像和文本呈现添加到用于高级可视化效果的应用程序_

> [!VIDEO https://youtube.com/embed/eYCx63FeqVU]

**Evolve 2016： 科科斯 # 在 Xamarin.Forms 中**

## <a name="overview"></a>概述

CocosSharp 是一种灵活且功能强大的技术，用于显示图形、 读取触摸输入、 播放音频和管理内容。 本指南介绍如何将 CocosSharp 添加到 Xamarin.Forms 应用程序。 它涵盖以下产品：

- [CocosSharp 是什么？](#what)
- [添加 CocosSharp Nuget 包](#nuget)
- [演练： 向 Xamarin.Forms 应用添加 CocosSharp](#add)

<a name="what" />

## <a name="what-is-cocossharp"></a>CocosSharp 是什么？

[CocosSharp](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/index.md)是一个开放源代码游戏引擎，可在 Xamarin 平台上。
CocosSharp 是一个高效运行时库，其中包括以下功能：

- 使用 `CCSprite` 类的图像呈现
- 使用 `CCDrawNode` 类的形状呈现
- 使用 `CCNode.Schedule` 类的每帧逻辑
- 使用 `CCTextureCache` 的内容管理（加载和卸载资源，如 .png 文件）
- 使用 `CCAction` 类的动画

CocosSharp 的主要重点是简化的跨平台 2D 游戏; 创建但是，它也可以是到 Xamarin 窗体应用程序的极好补充。 由于游戏通常需要的高效绘制和视觉对象的精确控制，可以使用 CocosSharp 向非游戏应用程序添加功能强大的可视化和效果。

Xamarin.Forms 是本机、 特定于平台的 UI 系统根据生成的。 例如， [ `Button`s](xref:Xamarin.Forms.Button)以不同的方式出现在 iOS 和 Android，并甚至可能因操作系统版本。 与此相反，CocosSharp 不使用任何特定于平台的视觉对象，因此所有视觉对象会显示在所有平台上完全相同。 当然，解决方法和纵横比不同设备之间，这可能会影响 CocosSharp 如何呈现其视觉对象。 本指南后面将讨论这些详细信息。

更多详细的信息可在[CocosSharp 部分](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/index.md)。

<a name="nuget" />

## <a name="adding-the-cocossharp-nuget-packages"></a>添加 CocosSharp Nuget 包

然后再使用 CocosSharp，开发人员需要进行一些添加到自己的 Xamarin.Forms 项目。
本指南假定一个 Xamarin.Forms 项目，与 iOS、 Android 和.NET Standard 类库项目。
.NET Standard 库项目中; 将写入的所有代码但是，库必须添加到 iOS 和 Android 项目。

CocosSharp Nuget 程序包中包含的所有创建 CocosSharp 对象所需对象。
CocosSharp.Forms nuget 包包含`CocosSharpView`类，该类用于托管 CocosSharp 在 Xamarin.Forms 中。
添加**CocosSharp.Forms** NuGet 和**CocosSharp**将也会自动添加。
为此，请右键单击 .NET Standard 库项目中的 "**包**" 文件夹，然后选择 "**添加包 ...** "。输入搜索词**CocosSharp**，**为 Xamarin 选择 CocosSharp**，然后单击 "**添加包**"。

![](cocossharp-images/image1.png "添加包对话框")

这两**CocosSharp**并**CocosSharp.Forms** NuGet 包将添加到项目：

![](cocossharp-images/image2.png "包文件夹")

对于特定于平台的项目 （如 iOS 和 Android） 重复上述步骤。

<a name="add" />

## <a name="walkthrough-adding-cocossharp-to-a-xamarinforms-app"></a>演练： 向 Xamarin.Forms 应用添加 CocosSharp

请按照下列步骤将简单 CocosSharp 视图添加到 Xamarin.Forms 应用：

1. [创建 Xamarin 窗体页](#1)
1. [添加 CocosSharpView](#2)
1. [创建 GameScene](#3)
1. [添加一个圆圈](#4)
1. [使用 CocosSharp 交互](#5)

一旦你已成功向 Xamarin.Forms 应用添加 CocosSharp 视图，请访问[CocosSharp 文档](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/index.md)若要了解有关创建与 cocossharp 结合使用的内容的详细信息。

<a name="1" />

### <a name="1-creating-a-xamarin-forms-page"></a>1. 创建 Xamarin 窗体页

CocosSharp 可以托管在任何 Xamarin.Forms 容器。 此示例的此页所使用的页称为`HomePage`。 `HomePage` 拆分了一半的`Grid`显示 Xamarin.Forms 和 CocosSharp 可以呈现方式同时在同一页上。

首先，设置页面，以便它包含`Grid`和两个`Button`实例：

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

在 iOS 上，`HomePage`显示在下图中所示：

![](cocossharp-images/image3.png "主页屏幕快照")

<a name="2" />

### <a name="2-adding-a-cocossharpview"></a>2. 添加 CocosSharpView

`CocosSharpView`类用于将 CocosSharp 嵌入到 Xamarin.Forms 应用。 由于`CocosSharpView`继承自[Xamarin.Forms.View](xref:Xamarin.Forms.View)类，它提供熟悉的界面的布局，并可以在布局容器内如[Xamarin.Forms.Grid](xref:Xamarin.Forms.Grid)。 添加一个新`CocosSharpView`项目，方法是完成到`CreateTopHalf`方法：

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

CocosSharp 初始化不是立即，因此注册事件，以便当`CocosSharpView`已完成创建。 在执行此操作`HandleViewCreated`方法：

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

`HandleViewCreated`方法具有两个我们会考虑的重要详细信息。 第一个是`GameScene`类，该类将在下一节中创建。 务必要注意，该应用程序将不会编译直到`GameScene`创建和`gameScene`实例引用被解析。

第二个重要的细节是`DesignResolution`属性，定义游戏的可见区域的 CocosSharp 对象。 `DesignResolution`属性在创建后要查找`GameScene`。

<a name="3" />

### <a name="3-creating-the-gamescene"></a>3. 创建 GameScene

`GameScene`类继承自 CocosSharp 的`CCScene`。 `GameScene` 是我们完全使用 CocosSharp 的处理其中的第一个点。 中包含代码`GameScene`或不存放 Xamarin.Forms 项目中是否将在任何 CocosSharp 应用中，函数。

`CCScene`类是所有 CocosSharp 呈现的根 visual。 任何可见 CocosSharp 对象必须包含在`CCScene`。 具体而言，必须将视觉对象添加到`CCLayer`实例，以及`CCLayer`必须将实例添加到`CCScene`。

下图可帮助直观显示典型 CocosSharp 层次结构：

![](cocossharp-images/image4.png "典型 CocosSharp 层次结构")

只有一个`CCScene`一次可处于活动状态。 大多数游戏使用多个`CCLayer`到排序内容，但我们的应用程序实例仅使用一个。 同样，大多数游戏程序使用多个视觉对象，但我们将仅有一个在我们的应用程序中。 更详细讨论的可视化层次结构可在 CocosSharp [BouncingGame 演练](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/bouncing-game.md)。

最初`GameScene`类将是几乎是空-我们只需将创建它来满足中的引用`HomePage`。 将新类添加到名为.NET Standard 库项目`GameScene`。 它应继承自`CCScene`类，如下所示：

```csharp
public class GameScene : CCScene
{
    public GameScene (CCGameView gameView) : base(gameView)
    {

    }
}
```

既然`GameScene`是定义，我们可以返回到`HomePage`和添加字段：

```csharp
// Keep the GameScene at class scope
// so the button click events can access it:
GameScene gameScene;
```

我们现在可以编译我们的项目，并运行它以查看 CocosSharp 运行。 我们尚未添加任何内容到我们`GameScene,`使我们的页面的上半部分是黑色 – CocosSharp 场景的默认颜色：

![](cocossharp-images/image5.png "空白 GameScene")

<a name="4" />

### <a name="4-adding-a-circle"></a>4. 添加圆圈

应用当前具有正在运行的 CocosSharp 引擎，显示一个空实例`CCScene`。 接下来，我们将添加视觉对象： 一个圆圈。 `CCDrawNode`类可用于绘制各种几何形状，如中所述[CCDrawNode 指南绘制几何图形](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/ccdrawnode.md)。

添加到一个圆形我们`GameScene`类并将其实例的构造函数中，在下面的代码所示：

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

![](cocossharp-images/image6.png "GameScene 中的圆圈")

#### <a name="understanding-designresolution"></a>了解 DesignResolution

现在，将显示一个可视化 CocosSharp 对象，我们可以调查`DesignResolution`属性。

`DesignResolution`表示的宽度和高度的放置和调整对象大小的 CocosSharp 的区域。 实际的分辨率的区域大小的单位*像素*虽然`DesignResolution`以世界单位*单位*。 下图显示的视图显示在 iPhone 5 640 x 1136 像素的屏幕分辨率的不同部件的解决方法：

![](cocossharp-images/image7.png "iPhone 5 秒设计解析")

上面的关系图上以黑色文本屏幕的外部显示像素尺寸。 单位显示白色文本中的关系图的内侧。 下面是一些重要的详细信息，如上所示：

- CocosSharp 显示原点位于左下角。 向右移动可增加的 X 值，并向上移动可增加的 Y 值。 请注意反转的 Y 值与某些其他 2D 布局引擎相比，(0，0) 是在画布左上方。
- CocosSharp 的默认行为是保持其视图的纵横比。 由于在网格中的第一行是宽度大于高度，CocosSharp 未填充其单元格的整个宽度以点分隔的白色矩形所示。 可以更改此行为，如中所述[处理 CocosSharp 中的多个解决方法指导](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/resolutions.md)。
- 在此示例中，CocosSharp 将保留 100 个单位的宽度和高度而不考虑大小的显示区域或其设备的纵横比。 这意味着，代码可以假定，X = 100 表示最右侧绑定 CocosSharp 的显示，请保留布局在所有设备上保持一致。

#### <a name="ccdrawnode-details"></a>CCDrawNode 的详细信息

我们简单的应用程序使用`CCDrawNode`类来绘制一个圆。 此类可以是对业务应用程序非常有用，因为它提供基于矢量的几何图形渲染-Xamarin.Forms 中缺少的功能。 除了圆圈，`CCDrawNode`类可用于绘制矩形、 自由绘制曲线、 线条和自定义的多边形。 `CCDrawNode` 也是易于使用由于不需要使用图像文件 （例如.png)。 可在 CCDrawNode 的更多详细的讨论[CCDrawNode 指南绘制几何图形](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/ccdrawnode.md)。

<a name="5" />

### <a name="5-interacting-with-cocossharp"></a>5. 与 CocosSharp 交互

CocosSharp 可视元素 (如`CCDrawNode`) 继承`CCNode`类。 `CCNode` 提供两个属性用于确定相对于其父对象的位置：`PositionX`和`PositionY`。 我们的代码当前使用这两个属性来定位的中心圆，此代码片段中所示：

```csharp
circle.PositionX = 20;
circle.PositionY = 50;
```

请务必注意 CocosSharp 对象位于通过显式位置值，而不是大多数 Xamarin.Forms 视图，可自动定位根据其父布局控件的行为。

我们将添加代码以允许用户单击其中一个由 10 个单位 （不是以像素，因为该圆形绘制 CocosSharp 世界单位空间中） 向左或向右移动该圆圈的两个按钮。 首先，我们将创建两个公共方法中的`GameScene`类：

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

接下来，我们将添加到中的两个按钮的处理程序`HomePage`以响应的单击。 完成后，我们`CreateBottomHalf`方法包含以下代码：

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

![](cocossharp-images/image8.png "与移动圆 GameScene")

## <a name="summary"></a>摘要

本指南演示如何将 CocosSharp 添加到现有 Xamarin.Forms 项目，如何创建 Xamarin.Forms 与 CocosSharp，之间的交互，并讨论了各种注意事项，在 CocosSharp 中创建布局时。

CocosSharp 游戏引擎提供了大量功能和深度，因此，本指南仅讲述了 CocosSharp 可以执行的操作。 对详细了解 CocosSharp 感兴趣的开发人员可以在[CocosSharp 存档](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/)中找到很多文章。

## <a name="related-links"></a>相关链接

- [CocosSharpForms （示例）](https://github.com/xamarin/xamarin-forms-samples/tree/master/CocosSharpForms)
