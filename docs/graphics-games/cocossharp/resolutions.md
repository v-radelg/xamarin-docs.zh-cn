---
title: 处理 CocosSharp 中的多个解决方法
description: 本指南演示如何使用 CocosSharp 开发游戏的不同分辨率的设备正确显示。
ms.prod: xamarin
ms.assetid: 859ABF98-2646-431A-A4A8-3E7E48DA5A43
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 6803dc2668b89ee2d037da8b34e202191dd5465d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61307688"
---
# <a name="handling-multiple-resolutions-in-cocossharp"></a>处理 CocosSharp 中的多个解决方法

_本指南演示如何使用 CocosSharp 开发游戏的不同分辨率的设备正确显示。_

CocosSharp 提供标准化对象维度，而不考虑物理设备的显示器上的像素数在游戏中的方法。 一直以来，游戏开发的电脑和游戏控制台可以作为目标的单个分辨率。 现代游戏 – 和尤其适用于移动设备的游戏 – 必须生成以适应各种设备。 本指南演示如何实现标准化 CocosSharp 而不考虑物理显示器的分辨率特征的游戏。

CocosSharp 的默认解决行为是以物理像素符合游戏中的坐标。 下表显示了各种设备就会导致背景环境精灵宽度和高度 368 x 240。 第一行是 sprite 的从技术上讲不是 sprite 的实际设备，但而不是 sprite 的预期，而不考虑设备分辨率呈现：


| **设备** | **显示分辨率** | **示例屏幕截图** |
|--- | --- |--- |
|所需的显示|368 x 240 （与纵横比为黑色长条）| ![368 x 240 （与纵横比为黑色长条）](resolutions-images/image1.png) |
|iPhone 4s|960x640| ![iPhone 4s 960x640](resolutions-images/image2.png) |
|iPhone 6 Plus|1920x1080| ![iPhone 6 Plus 1920x1080](resolutions-images/image3.png) |

本文档介绍如何使用 CocosSharp 来解决该问题上表中所示。 也就是说，我们将介绍如何使呈现的第一行 – 而不考虑屏幕分辨率中所示的任何设备。


## <a name="working-with-setdesignresolutionsize"></a>使用 SetDesignResolutionSize

`CCScene`类通常用作根容器的所有视觉对象，但它还提供了一个静态方法`SetDesignResolutionSize`用于指定所有场景的默认大小。 换而言之`SetDesignResolutionSize`方法允许开发人员可以开发游戏将正确显示在不同的硬件分辨率。 CocosSharp 项目模板使用此方法设置默认项目大小为 1024 x 768，如下面的代码中所示：


```csharp
public override void ApplicationDidFinishLaunching (CCApplication application, CCWindow mainWindow)
{
    application.PreferMultiSampling = false;
    application.ContentRootDirectory = "Content";
    application.ContentSearchPaths.Add ("animations");
    application.ContentSearchPaths.Add ("fonts");
    application.ContentSearchPaths.Add ("sounds");
    CCSize windowSize = mainWindow.WindowSizeInPixels;
    float desiredWidth = 1024.0f;
    float desiredHeight = 768.0f;
    
    // This will set the world bounds to (0,0, w, h)
    // CCSceneResolutionPolicy.ShowAll will ensure that the aspect ratio is preserved
    CCScene.SetDesignResolutionSize (desiredWidth, desiredHeight, CCSceneResolutionPolicy.ShowAll);
    ...
```

您可以更改`desiredWidth`和`desiredHeight`变量更高版本，若要修改显示效果以匹配您的游戏的所需的分辨率。 例如，可以按如下所示修改上面的代码来为全屏显示 368 x 240 背景：


```csharp
public override void ApplicationDidFinishLaunching (CCApplication application, CCWindow mainWindow)
{
    application.PreferMultiSampling = false;
    application.ContentRootDirectory = "Content";
    application.ContentSearchPaths.Add ("animations");
    application.ContentSearchPaths.Add ("fonts");
    application.ContentSearchPaths.Add ("sounds");
    CCSize windowSize = mainWindow.WindowSizeInPixels;
    float desiredWidth = 368.0f;
    float desiredHeight = 240.0f;
    
    // This will set the world bounds to(0,0, w, h)
    // CCSceneResolutionPolicy.ShowAll will ensure that the aspect ratio is preserved
    CCScene.SetDesignResolutionSize (desiredWidth, desiredHeight, CCSceneResolutionPolicy.ShowAll);
    ...
```


## <a name="ccsceneresolutionpolicy"></a>CCSceneResolutionPolicy

`SetDesignResolutionSize` 可用于指定如何在游戏窗口调整到所需的分辨率。 以下各节演示如何使用不同显示 500 x 500 图像`CCSceneResolutonPolicy`值传递给`SetDesignResolutionSize`方法。 通过提供以下值`CCSceneResolutionPolicy`枚举：

 - `ShowAll` – 显示整个请求解决方法，保持纵横比。
 - `ExactFit` – 显示整个请求解决方法，但不是保持长宽比。
 - `FixedWidth` – 显示请求的整个宽度，保持纵横比。
 - `FixedHeight` – 显示整个请求的高度，保持纵横比。
 - `NoBorder` – 显示的全部高度或整个宽度，保持纵横比。 如果设备的纵横比大于所需的分辨率的长宽比，会显示整个宽度。 如果设备的纵横比小于所需的分辨率的长宽比，会显示整个高度。
 - `Custom` -显示依赖于`Viewport`属性与当前`CCScene`。

所有屏幕截图中横向方向的 iPhone 4 秒分辨率 (960 x 640) 生成，并使用该映像：

![](resolutions-images/image4.png "所有屏幕截图分辨率 iPhone 4 秒 960 x 640 以横向方式生成和使用此映像")


### <a name="ccsceneresolutionpolicyshowall"></a>CCSceneResolutionPolicy.ShowAll

`ShowAll` 指定整个游戏解析会显示屏幕上，但可能会显示*设置*（黑色长条） 以进行调整的纵横比不同。 因为它可以保证整个游戏视图将会显示在屏幕上没有任何失真，通常使用此策略。

代码示例：


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.ShowAll);
```

设置都可以看到左侧和右侧图像以考虑物理纵横比要比所需的分辨率更广：

![](resolutions-images/image5.png "设置都可以看到左侧和右侧图像以考虑物理要比所需的分辨率更广的纵横比")


### <a name="ccsceneresolutionpolicyexactfit"></a>CCSceneResolutionPolicy.ExactFit

`ExactFit` 指定将与不设置屏幕上可见的整个游戏分辨率。 可视区域可能会扭曲 （可能不保留纵横比） 根据硬件纵横比。

代码示例：


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.ExactFit);
```

没有设置可见，但由于设备分辨率是矩形游戏视图被曲解：

![](resolutions-images/image6.png "没有设置为可见，但由于设备分辨率是矩形扭曲游戏视图")


### <a name="ccsceneresolutionpolicyfixedwidth"></a>CCSceneResolutionPolicy.FixedWidth

`FixedWidth` 指定视图的宽度将匹配的宽度值传递给`SetDesignResolutionSize`，但可查看的高度取决于物理设备的纵横比。 传递给的高度值`SetDesignResolutionSize`被忽略，因为它将计算在运行时根据物理设备的纵横比。 这意味着，计算所得的高度可能小于所需的高度 （这会导致游戏视图正在屏外的部分） 或计算所得的高度可能会大于所需的高度 （这会导致多个要显示的游戏视图）。 因为这可能会导致多个显示游戏，那么它可能会出现像发生设置;但是，额外的空间并不一定是如果有任何视觉对象显示黑色。 

代码示例：


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.FixedWidth);
```

IPhone 4 秒有纵横比为 3:2，因此计算所得的高度为大约 333 单位：

![](resolutions-images/image7.png "IPhone 4 秒有纵横比为 3:2，因此计算所得的高度为大约 333 单位")


### <a name="ccsceneresolutionpolicyfixedheight"></a>CCSceneResolutionPolicy.FixedHeight

从概念上讲，`FixedHeight`行为类似于`FixedWidth`– 游戏都将遵循在传递给的高度值`SetDesignResolutionSize,`，但将计算在运行时基于物理分辨率的宽度。 如上所述，这意味着，显示的宽度是小于或大于所需的宽度，从而导致的游戏正在一部分关闭屏幕或多个正在显示，分别游戏。

代码示例：


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.FixedHeight);
```

下面的屏幕截图使用在横向模式下运行的应用程序创建的因为计算所得的宽度大于 500 – 专门 750。 此策略保留为 0 的 X 值左对齐，因此额外的解决方法是在屏幕右侧可查看。

![](resolutions-images/image8.png "此策略保留为 0 的 X 值左对齐，因此额外的解决方法是可以在屏幕右侧")


### <a name="ccsceneresolutionpolicynoborder"></a>CCSceneResolutionPolicy.NoBorder

`NoBorder` 尝试显示具有未设置的应用程序，同时保持原始纵横比 （无扭曲）。 如果请求的解析的长宽比匹配设备的物理纵横比，则会不发生任何剪辑。 如果长宽比不匹配，然后剪辑将会发生。

代码示例：


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.FixedHeight);
```

下面的屏幕截图显示剪辑，显示所有 500 像素的显示宽度时显示的顶部和底部组成部分：

![](resolutions-images/image9.png "此屏幕截图显示剪辑，显示所有 500 像素的显示宽度时显示的顶部和底部部分")


### <a name="ccsceneresolutionpolicycustom"></a>CCSceneResolutionPolicy.Custom

`Custom` 使每个`CCScene`若要指定相对于解决方法中指定其自己自定义的视区`SetDesignResolutionSize`。

场景定义其`Viewport`属性通过构造`CCViewport`，这又构造使用`CCRect`。 有关详细信息`CCViewport`并`CCRect`请参阅[CocosSharp API 文档](https://developer.xamarin.com/api/namespace/CocosSharp/)。 在创建的上下文中`CCViewport``CCRect`构造函数的参数指定 （按顺序）：

 - x – 水平偏移量视区，其中每个单元表示一个整个屏幕宽度。 例如，向右侧移动屏幕宽度的一半在场景中使用.5f 结果的值。
 - y – 垂直偏移量视区，其中每个单元表示一个整个屏幕高度。 例如，向下移动屏幕高度的一半在场景中使用.5f 结果的值。
 - 宽度-场景应占用的水平部分。 例如，使用值为 1 / 3.0f 导致水平占用三分之一屏幕的场景。
 - 高度 – 场景应占用的垂直部分。 例如，使用值为 1 / 3.0f 导致垂直占用三分之一屏幕的场景。

代码示例：


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.Custom);
...
float horizontalDisplayPortion = 2 / 3.0f;
float verticalDisplayPortion = 1 / 2.0f;
float displayOffsetInScreenWidths = 0;
float displayOffsetInScreenHeights = .5f;
var rectangle = new CCRect (
    displayOffsetInScreenWidths, 
    displayOffsetInScreenHeights, 
    horizontalDisplayPortion, 
    verticalDisplayPortion);
scene.Viewport = new CCViewport (rectangle);
```

上面的代码产生以下结果：

![](resolutions-images/image10.png "上面的代码会导致此屏幕截图")


## <a name="defaulttexeltocontentsizeratio"></a>DefaultTexelToContentSizeRatio

`DefaultTexelToContentSizeRatio`简化了具有更高分辨率屏幕的设备上使用分辨率更高的纹理。 具体而言，此属性使游戏以使用更高分辨率资产而无需更改的大小或位置的可视元素。 

例如，一个游戏可能包括艺术资产的游戏的字符，这是 200 像素、 高，和游戏的屏幕 (所指定的`SetDesignResolutionSize`) 可能是 400 像素、 高。 在这种情况下，该字符将占用屏幕的一半。 但是，如果 400 像素 tall 资产已用于更高分辨率设备的字符，字符将占用整个显示的高度。 如果目的是要保留相同的大小以充分利用更高分辨率设备的字符，则需要在屏幕的一半高度保留字符 sprite 一些额外的代码。

上面介绍的简单示例表示游戏开发-而无需开发人员以执行每个可视元素根据设备分辨率上调整大小添加更大的资产中常见的问题。 `DefaultTexelToContentSizeRatio` 是一个全局属性用于调整视觉元素。 此值调整大小由所赋的值的特定类型的所有可视化元素。

此属性位于以下类： 

 - `CCApplication`
 - `CCSprite`
 - `CCLabelTtf`
 - `CCLabelBMFont`
 - `CCTMXLayer`

CocosSharp Visual Studio for Mac 模板集`CCSprite.DefaultTexelToContentSizeRatio`根据作为示例，了解如何使用设备的宽度。 下面的代码包含在`CCApplicationDelegate.ApplicationDidFinishLaunching`:


```csharp
public override void ApplicationDidFinishLaunching (CCApplication application, CCWindow mainWindow)
{
    ...
    float desiredWidth = 1024.0f;
    float desiredHeight = 768.0f;
           
    ...
    if (desiredWidth < windowSize.Width)
    {
        application.ContentSearchPaths.Add ("images/hd");
        CCSprite.DefaultTexelToContentSizeRatio = 2.0f;
    }
    else
    {
        application.ContentSearchPaths.Add ("images/ld");
        CCSprite.DefaultTexelToContentSizeRatio = 1.0f;
    }
    ...           
}
```


### <a name="defaulttexeltocontentsizeratio-example"></a>DefaultTexelToContentSizeRatio 示例

若要查看如何`DefaultTexelToContentSizeRatio`影响视觉对象的大小元素，请考虑上面介绍的代码：


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.ShowAll);
```

这将导致在下图中使用 500 x 500 纹理：

![](resolutions-images/image5.png "这将导致使用 500 x 500 纹理此映像")

IPad Retina 运行 2048 x 1536 物理分辨率。 这意味着按照上面显示的游戏会 1536年物理像素上拉伸 500 像素。 游戏可以通过创建什么通常称为充分利用此额外解析*hd*资产 – 用于更高分辨率屏幕上运行的资产。 例如，此游戏可以使用此纹理大小为 1000 x 1000 的 hd 版本。 但是，使用可查看大图像会导致`CCSprite`宽度和高度的 1000 个单位。 由于我们的游戏中将始终显示 500 个单位 (由于`SetDesignResolutioSize`调用)，则我们 1000 x 1000 子画面将太大 （仅的底部左侧的部分它显示）：

![](resolutions-images/image11.png "由于游戏将始终显示由于 SetDesignResolutioSize 调用 500 个单位，1000 x 1000 sprite 是仅的底部左侧的部分它显示太大")

我们可以设置`CCSprite.DefaultTexelToContentSizeRatio`1000 x 1000 纹理，宽度和高度作为原始的 500 x 500 纹理两次正在考虑：


```csharp
CCSprite.DefaultTexelToContentSizeRatio = 2;
```

现在如果我们运行游戏 1000 x 1000 纹理将完全可见：

![](resolutions-images/image12.png "现在如果我们运行游戏 1000 x 1000 纹理将完全可见")


### <a name="defaulttexeltocontentsizeratio-details"></a>DefaultTexelToContentSizeRatio 详细信息

`DefaultTexelToContentSizeRatio`属性是`static,`这意味着在应用程序中的所有子画面将共享相同的值。 对不同分辨率的资产与游戏的典型方法是包含一组完整的资产的每个解决方法类别。 默认情况下 CocosSharp Visual Studio for Mac 模板提供**ld**并**hd**对支持纹理的两个集的游戏都有用的资产的文件夹。 包含内容的示例内容文件夹可能如下所示：

![](resolutions-images/image13.png "包含内容的示例内容文件夹可能如下所示")

请注意，默认模板还包含用于有条件地添加到应用程序的代码`ContentSearchPaths`:


```csharp
public override void ApplicationDidFinishLaunching (CCApplication application, CCWindow mainWindow)
{
    ...
    if (desiredWidth < windowSize.Width)
    {
        application.ContentSearchPaths.Add ("images/hd");
        CCSprite.DefaultTexelToContentSizeRatio = 2.0f;
    }
    else
    {
        application.ContentSearchPaths.Add ("images/ld");
        CCSprite.DefaultTexelToContentSizeRatio = 1.0f;
    }
    ...           
}
```

这意味着应用程序将搜索的路径根据是否在高分辨率或正则分辨率模式下运行中的文件。 例如，如果**hd**并**ld**文件夹包含名为的文件**background.png**然后下面的代码将运行并选择正确的文件进行解析：


```csharp
backgroundSprite  = new CCSprite ("background");
```


## <a name="summary"></a>总结

这篇文章介绍如何创建游戏的而不考虑设备分辨率正确显示。 它显示了使用的示例不同`CCSceneResolutionPolicy`重设大小根据设备分辨率游戏的值。 它还提供了具体的示例`DefaultTexelToContentSizeRatio`可用于容纳多个集的内容，而无需单独调整大小的可视元素。

## <a name="related-links"></a>相关链接

- [CocosSharp 简介](~/graphics-games/cocossharp/index.md)
- [CocosSharp API 文档](https://developer.xamarin.com/api/namespace/CocosSharp/)
