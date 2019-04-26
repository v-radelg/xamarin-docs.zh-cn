---
title: 性能和使用 CCRenderTexture 视觉效果
description: CCRenderTexture 使开发人员能够通过减少的绘图调用，提高其 CocosSharp 游戏的性能和可用于创建视觉效果。 本指南附带 CCRenderTexture 示例提供如何有效地使用此类的实际操作示例。
ms.prod: xamarin
ms.assetid: F02147C2-754B-4FB4-8BE0-8261F1C5F574
author: conceptdev
ms.author: crdun
ms.date: 03/27/2017
ms.openlocfilehash: 95227689303a8367785202956a6aaef921c1c593
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61085171"
---
# <a name="performance-and-visual-effects-with-ccrendertexture"></a>性能和使用 CCRenderTexture 视觉效果

_CCRenderTexture 使开发人员能够通过减少的绘图调用，提高其 CocosSharp 游戏的性能和可用于创建视觉效果。本指南附带 CCRenderTexture 示例提供如何有效地使用此类的实际操作示例。_

`CCRenderTexture`类提供用于呈现为单个的纹理的多个 CocosSharp 对象的功能。 创建之后，`CCRenderTexture`来高效地呈现图形以及实现视觉效果，可以使用实例。 `CCRenderTexture` 允许多个对象将呈现给单纹理一次。 然后，该纹理可以重复使用每个帧，减少绘图调用的总数。

本指南探讨如何使用`CCRenderTexture`对象来提高可收集的纸牌游戏 (CCG) 中呈现卡片的性能。 它还演示了如何`CCRenderTexture`可用于将整个实体设置为透明。 本指南引用`CCRenderTexture`[示例项目](https://developer.xamarin.com/samples/mobile/CCRenderTexture/)。

![](ccrendertexture-images/image1.png "本指南引用 CCRenderTexture 示例项目")


## <a name="card--a-typical-entity"></a>卡 – 典型实体

之前看一下如何使用`CCRenderTexture`对象，我们将首先了解了相关与我们自己`Card`实体，我们将使用在此项目整个浏览`CCRenderTexture`类。 `Card`类是一个典型的实体，以下实体模式中所述[实体指南](~/graphics-games/cocossharp/entities.md)。 卡类具有的所有可视化组件 (的实例`CCSprite`和`CCLabel`) 作为字段列出：


```csharp
class Card : CCNode
{
    bool usesRenderTexture;
    List<CCNode> visualComponents = new List<CCNode>();
    CCSprite background;
    CCSprite colorIcon;
    CCSprite monsterSprite;
    CCLabel monsterNameDisplay;
    CCLabel hpDisplay;
    CCLabel descriptionDisplay;
```

可以通过使用呈现卡实例`CCRenderTexture`，或通过单独绘制每个可视组件。 尽管每个组件是一个独立的对象，但`CCNode`作为父级在实体中使用系统使`Card`为单个对象 – 至少在大多数情况的行为。 例如，如果`Card`实体进行重新定位、 调整大小，或旋转，则所有包含的视觉对象会受到影响，以使卡片的似乎是单个对象。 若要查看数据卡为单个对象的行为，我们可以修改`GameLayer.AddedToScene`方法以设置`useRenderTextures`变量`false`:

    
```csharp
protected override void AddedToScene ()
{
    base.AddedToScene();
    GameView.Stats.Enabled = true;
    const bool useRenderTextures = false;
    ...
}
```

`GameLayer`代码不会移动每个可视元素独立，尚未在每个可视元素`Card`正确定位实体：

![](ccrendertexture-images/image1.png "GameLayer 代码不会移动每个可视元素独立，但正确定位卡实体中的每个可视元素")

该示例进行编码以公开每个可视组件呈现自身时可能发生的两个问题：

- 由于多个绘图调用的性能可能会受到影响
- 某些视觉效果，如透明，不能准确地说，实现，如我们将探讨更高版本


### <a name="card-draw-calls"></a>卡绘图调用

我们的代码是完全可能找到的信息的简化*可收集的纸牌游戏*(CCG) 如"Magic:正在收集"或"Hearthstone"。 我们的游戏中仅在一次显示三张数据卡，并具有较少的可能单元 （蓝色、 绿色和橙色）。 与此相反，完整的游戏可能有超过 20 种卡屏幕在给定时间，并且播放机可能会有数百个可供创建其卡片组时选择的卡。 即使我们的游戏中不会不当前会遇到性能问题，可能会使用类似的实现完全游戏。

CocosSharp 提供了一些见解通过公开的绘图调用的呈现性能执行每个帧。 我们`GameLayer.AddedToScene`方法设置`GameView.Stats.Enabled`到`true`，从而导致性能信息的屏幕的左下角所示：

![](ccrendertexture-images/image2.png "GameLayer.AddedToScene 方法将 GameView.Stats.Enabled 设置为 true，从而导致在屏幕的左下角显示的性能信息")

请注意，尽管三张数据卡在屏幕上，我们有十九个绘图调用 （每个卡结果在六个绘图调用，一个详细显示性能信息帐户的文本）。 绘图调用对游戏的性能具有重大影响，因此 CocosSharp 提供了多种方法来减少此类。 一种方法中所述[ccspritesheet 提高帧速率指南](~/graphics-games/cocossharp/ccspritesheet.md)。 另一种方法是使用`CCRenderTexture`来减小下一次调用，每个实体，因为我们将在本指南中介绍。


### <a name="card-transparency"></a>卡透明度

我们`Card`实体包含`Opacity`属性控制透明度，如下面的代码段中所示：


```csharp
public override byte Opacity
{
    get
    {
        return base.Opacity;
    }
    set
    {
        base.Opacity = value;
        if (usesRenderTexture)
        {
            this.renderTexture.Sprite.Opacity = value;
        }
        else
        {
            foreach (var component in visualComponents)
            {
                component.Opacity = value;
            }
        }
    }
}
```

请注意资源库支持使用呈现纹理或单独呈现每个组件。 若要查看其效果，请更改`opacity`值设为`127`（大约一半的不透明度） 在`GameLayer.AddedToScene`，并将导致每个组件都有`Opacity`的值`127`:


```csharp
protected override void AddedToScene ()
{
    base.AddedToScene();
    GameView.Stats.Enabled = true;
    const bool useRenderTextures = false;
    const byte opacity = 127;
    ...
}
```

游戏现在将显示具有一些透明度，从而导致出现较暗这是因为背景为黑色卡：

![](ccrendertexture-images/image3.png "游戏现在将呈现具有一些透明度，从而导致出现较暗这是因为背景为黑色卡")

乍一看它可能看起来像我们的卡正确进行了透明。 但是，屏幕截图显示视觉问题的数。

由于我们背景为黑色，我们希望我们卡的每个部分将变暗由于透明度。 也就是说，变得更加透明卡片，颜色越深得。 在不透明度 0，`Card`将是完全透明 （完全黑色）。 但是，我们的卡中的某些部分未变暗时不透明度已更改为`127`。 更糟糕的是，我们的卡中的某些部分实际上时变得更亮变得更加透明。 让我们看一下我们的卡的部分是黑色*之前*它们是透明的 – 这头怪兽图黑色边框和专门的详细信息文本。 如果我们将这些并排显示，我们可以看到应用透明度的影响：

![](ccrendertexture-images/image4.png "如果并排放置，则可以看到应用透明度的影响")

如上所述，卡片的所有部分应都变暗时变得更加透明，, 但在许多领域中这不是这种情况：

- 机器人的大纲变得较浅 （转从黑色到灰色）
- 说明文本变得较浅 （转从黑色到灰色）
- 机器人的绿色部分变为不太满负荷状态，但不会变暗

为帮助直观地为何发生这种情况，我们需要记住每个可视组件是单独绘制，每个部分透明。 绘制第一个可视组件是卡的背景。 接下来的透明元素将绘制在卡上，将受卡背景。 如果我们从我们的卡上删除一些文本，并向下移动机器人图，我们可以看到卡背景如何影响机器人。 请注意，可以在机器人上看到从顶部的框中的橙色线和机器人的重叠蓝色的条带化在卡的中心的区域绘制深：

![](ccrendertexture-images/image5.png "请注意，可以在机器人上看到从顶部的框中的橙色线和机器人的重叠蓝色的条带化在卡的中心的区域绘制深")

使用`CCRenderTexture`使我们能够使整个卡片的透明而不会影响在卡内的单独组件的呈现我们会在本指南后面。


## <a name="using-ccrendertexture"></a>使用 CCRenderTexture

现在，我们已识别了单独呈现每个组件的问题，我们将开启呈现到`CCRenderTexture`和比较行为。

若要启用到呈现`CCRenderTexture`，更改`userRenderTextures`变量`true`中`GameLayer.AddedToScene`:


```csharp
protected override void AddedToScene ()
{
    base.AddedToScene();
    GameView.Stats.Enabled = true;
    const bool useRenderTextures = true;
```


### <a name="card-draw-calls"></a>卡绘图调用

如果我们现在运行游戏，我们将看到从里面有 19 个到四个绘图调用 （每个卡减少到一个六）：

![](ccrendertexture-images/image6.png "如果现在运行游戏时，绘图调用减少从里面有 19 个到四个每张卡片减少到一个六")

如前文所述，这种类型的减少会对使用更多 visual 实体在屏幕上的游戏很大的影响。


### <a name="card-transparency"></a>卡透明度

一次`useRenderTextures`设置为`true`，透明卡将以不同的方式呈现：

![](ccrendertexture-images/image7.png "一旦 useRenderTextures 设置为 true 且透明的卡将以不同的方式呈现")

让我们比较透明机器人卡使用呈现纹理 （左） 与而无需 （右）：

![](ccrendertexture-images/image8.png "比较使用而无需 （右） 呈现器 （左） 的纹理 vs 的透明机器人卡")

在详细信息文本 （而不是浅灰色的黑色） 和机器人子画面 （而不是 light 深色和去除饱和度） 是最明显的差异。


## <a name="ccrendertexture-details"></a>CCRenderTexture 详细信息

现在，我们已了解使用的好处`CCRenderTexture`，让我们看看如何在中使用`Card`实体。

`CCRenderTexture`是可以的呈现目标的画布。 它具有两个主要的区别与游戏的屏幕相比：

1. `CCRenderTexture`仍然存在中间帧。 这意味着，`CCRenderTexture`需要发生更改时仅呈现。 在本例中，`Card`实体永远不会发生更改，所以它只呈现一次。 如果有的话`Card`组件发生更改，将卡需要重绘自身到其`CCRenderTexture`。 例如，如果攻击时，将更改 HP 值 （运行状况磅为单位），然后在卡需要来呈现自身以反映新的 HP 值。
1. `CCRenderTexture`像素大小不受限于屏幕。 一个`CCRenderTexture`可以是大于或小于设备的分辨率。 `Card`代码将创建`CCRenderTexture`使用其背景子画面的大小。 卡片包含对引用`CCRenderTexture`调用`renderTexture`:


```csharp
CCRenderTexture renderTexture;
```

`renderTexture`实例保持`null`直到`UseRenderTexture`分配属性为 true 时，它调用`SwitchToRenderTexture`:


```csharp
private void SwitchToRenderTexture()
{
    // The card needs to be moved to the origin (0,0) so it's rendered on the render target. 
    // After it's rendered to the CCRenderTexture, it will be moved back to its old position
    var oldPosition = this.Position;
    // Make sure visuals are part of the card so they get rendered
    bool areVisualComponentsAlreadyAdded = this.Children != null && this.Children.Contains(visualComponents[0]);
    if (!areVisualComponentsAlreadyAdded)
    {
        // Temporarily add them so we can render the object:
        foreach (var component in visualComponents)
        {
            this.AddChild(component);
        }
    }
    // Create the render texture if it hasn't yet been made:
    if (renderTexture == null)
    {
        // Even though the game is zoomed in to create a pixellated look, we are using
        // high-resolution textures. Therefore, we want to have our canvas be 2x as big as 
        // the background so fonts don't appear pixellated
        var pixelResolution = background.ContentSize * 2;
        var unitResolution = background.ContentSize;
        renderTexture = new CCRenderTexture(unitResolution, pixelResolution);
        //renderTexture.Sprite.Scale = .5f;
    }
    // We don't want the render target to be a child of this when we update it:
    if (this.Children != null && this.Children.Contains(renderTexture.Sprite))
    {
        this.Children.Remove(renderTexture.Sprite);
    }
    // Move this instance back to the origin so it is rendered inside the render texture:
    this.Position = CCPoint.Zero;
    // Clears the CCRenderTexture
    renderTexture.BeginWithClear(CCColor4B.Transparent);
    // Visit renders this object and all of its children
    this.Visit();
    // Ends the rendering, which means the CCRenderTexture's Sprite can be used
    renderTexture.End();
    // We no longer want the individual components to be drawn, so remove them:
    foreach (var component in visualComponents)
    {
        this.RemoveChild(component);
    }
    // Move this back to its original position:
    this.Position = oldPosition;
    // add the render texture sprite to this:
    renderTexture.Sprite.AnchorPoint = CCPoint.Zero;
    this.AddChild(renderTexture.Sprite);
}
```

`SwitchToRenderTexture`纹理需要进行刷新时，可以调用方法。 它是否已在使用卡就可以调用其`CCRenderTexture`，或切换到`CCRenderTexture`第一次。

以下各部分介绍`SwitchToRenderTexture`方法。 


### <a name="ccrendertexture-size"></a>CCRenderTexture 大小

CCRenderTexture 构造函数需要两个集的维度。 第一个控件的大小`CCRenderTexture`时，对其绘制和第二个指定的像素宽度和高度其内容。 `Card`实体实例化其`CCRenderTexture`使用背景[ContentSize](https://developer.xamarin.com/api/property/CocosSharp.CCSprite.ContentSize/)。 我们的游戏中具有`DesignResolution`512 通过 384 个，如中所示`ViewController.LoadGame`在 iOS 上和`MainActivity.LoadGame`在 Android 上：


```csharp
int width = 512;
int height = 384;
// Set world dimensions
gameView.DesignResolution = new CCSizeI(width, height);
```

`CCRenderTexture`与调用构造函数`background.ContentSize`的第一个参数，该值指示`CCRenderTexture`应在后台一样大`CCSprite`。 由于卡背景`CCSprite`、 高 200 像素，卡将占用大约一半的屏幕的垂直高度。

第二个参数传递给`CCRenderTexture`构造函数指定的像素的分辨率`CCRenderTexture`。 如中所述[CocosSharp 解析指南](~/graphics-games/cocossharp/resolutions.md)，宽度和高度的可视区域以游戏单位通常不是像素的分辨率屏幕的相同。 同样，CCRenderTexture 可能使用较大的分辨率比其大小，使视觉对象显示高分辨率设备上更鲜艳。

像素的分辨率是 CCRenderTexture 以防止从美观像素化文本的大小的两倍：


```csharp
var unitResolution = background.ContentSize;
var pixelResolution = background.ContentSize * 2;
renderTexture = new CCRenderTexture(unitResolution, pixelResolution);
```

若要进行比较，我们可以更改要匹配的 pixelResolution 值`background.ContentSize`（不带正在增加一倍） 并将结果进行比较： 


```csharp
var unitResolution = background.ContentSize;
var pixelResolution = background.ContentSize;
renderTexture = new CCRenderTexture(unitResolution, pixelResolution);
```

![](ccrendertexture-images/image9.png "若要进行比较，可以更改要匹配在后台的 pixelResolution 值。ContentSize 而无需正在增加一倍，并比较结果")


### <a name="rendering-to-a-ccrendertexture"></a>呈现到 CCRenderTexture

通常情况下，CocosSharp 中的视觉对象不显式会呈现。 相反，将视觉对象添加到`CCLayer`一部分的`CCScene`。 CocosSharp 自动呈现`CCScene`中每一帧不调用任何呈现代码及其可视层次结构。 

与此相反，`CCRenderTexture`必须显式绘制到。 此呈现可以分解为三个步骤：

1. `CCRenderTexture.BeginWithClear` 称为，该值指示所有后续呈现都将针对调用`CCRenderTexture`。
1. 对象继承自`CCNode`(如`Card`实体) 呈现给`CCRenderTexture`通过调用`Visit`。
1. `CCRenderTexture.End` 调用时，指示该呈现到`CCRenderTexture`已完成。

可以将任意数量的对象呈现给`CCRenderTexture`之间其`Begin`和`End`调用。 在呈现之前，作为子级添加所有必要的可见对象：


```csharp
bool areVisualComponentsAlreadyAdded = this.Children != null && this.Children.Contains(visualComponents[0]);
if (!areVisualComponentsAlreadyAdded)
{
    // Temporarily add them so we can render the object:
    foreach (var component in visualComponents)
    {
        this.AddChild(component);
    }
}
```

`renderTexture`不应在卡的一部分时呈现，以便它将随：


```csharp
// We don't want the render texture to be a child of the card 
// when we call Visit
if (this.Children != null && this.Children.Contains(renderTexture.Sprite))
{
    this.RemoveChild(renderTexture.Sprite);
}
```

现在`Card`实例可以呈现自身到`CCRenderTexture`实例：


```csharp
// Clears the CCRenderTexture
renderTexture.BeginWithClear(CCColor4B.Transparent);
// Visit renders this object and all of its children
this.Visit();
// Ends the rendering, which means the CCRenderTexture's Sprite can be used
renderTexture.End();
```

呈现完成后，删除了单独的组件和`CCRenderTexture`重新添加。


```csharp
// We no longer want the individual components to be drawn, so remove them:
foreach (var component in visualComponents)
{
    this.RemoveChild(component);
}
// add the render target sprite to this:
this.AddChild(renderTexture.Sprite);
```

## <a name="summary"></a>总结

本指南介绍如何`CCRenderTexture`类使用`Card`实体可能在可回收的纸牌游戏中使用。 它介绍了如何使用`CCRenderTexture`类，以提高帧速率和正确实现实体级透明度。

尽管使用此指南`CCRenderTexture`包含在实体中，此类可以是用于呈现多个实体，或甚至整个`CCLayer`屏幕范围效果和性能改进的实例。

## <a name="related-links"></a>相关链接

- [CCRenderTexture API 参考](https://developer.xamarin.com/api/type/CocosSharp.CCRenderTexture/)
- [完整的项目 （示例）](https://developer.xamarin.com/samples/mobile/CCRenderTexture/)
