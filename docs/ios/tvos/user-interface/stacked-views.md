---
title: 使用 tvOS 堆积在 Xamarin 中视图
description: 本文档介绍如何结合使用 tvOS 堆积的视图中使用 Xamarin 生成的应用。 它提供堆积视图的高级概述，并讨论了自动布局、 定位和调整大小堆积的视图、 常见用途，集成使用演示图板，和的详细信息。
ms.prod: xamarin
ms.assetid: 00B07F85-F30B-4DD4-8664-A61D0A1CDB0E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: f51ed3d6dbbfc8a7e430c2949485838a7471e545
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61161220"
---
# <a name="working-with-tvos-stacked-views-in-xamarin"></a>使用 tvOS 堆积在 Xamarin 中视图

堆栈视图控件 (`UIStackView`) 利用自动布局和大小类水平或垂直，管理的子视图，堆栈的强大，但该功能可动态响应的内容更改和 Apple TV 设备屏幕大小。

附加到堆栈视图的所有子视图的布局受它基于等轴、 分发、 对齐方式和间距的开发人员定义属性：

[![](stacked-views-images/stacked01.png "子视图布局关系图")](stacked-views-images/stacked01.png#lightbox)

使用时`UIStackView`在 Xamarin.tvOS 应用中，开发人员可以定义子视图是在情节提要设计器中，在 iOS 中或通过添加和删除子视图中的C#代码。

## <a name="about-stacked-view-controls"></a>有关堆积的视图控件 

`UIStackView`专为非呈现容器视图并在这种情况下，不绘制到等其他类的子类画布`UIView`。 设置属性，如`BackgroundColor`或重写`DrawRect`将具有任何可视化效果。

有几个属性，用于控制如何堆栈视图时，将排列其子视图的集合：

- **轴**– 确定是否堆栈视图排列了子视图既**水平**或**垂直**。
- **对齐方式**– 控制了子视图堆栈视图内的对齐方式。
- **分发**– 控制如何了子视图的大小将调整堆栈视图中。
- **间距**– 控制每个堆栈视图中的子视图之间的最小空间。
- **基线相对**– 如果`true`，每个子视图的垂直间距将派生自它的基线。
- **布局边距相对**– 相对于标准布局边距中放置了子视图。

通常将使用堆栈视图来排列子视图的一小部分。 可以通过嵌套在每个其他的一个或多个堆栈视图来创建更复杂的用户界面。

通过将其他约束添加到了子视图 （例如到控件的高度或宽度），可以进一步微调 Ui 外观。 但是，应格外小心，不以包括对这些引入的堆栈视图本身的冲突约束。

<a name="Auto-Layout-and-Size-Classes" />

## <a name="auto-layout-and-size-classes"></a>自动布局和大小类

子视图添加到堆栈视图时由使用自动布局和大小类的位置和大小排列的视图该堆栈视图完全控制其布局。

堆栈视图将_pin_到其集合中的第一个和最后一个子视图**顶部**并**底部**边缘的垂直堆栈视图或**留**并**右**边缘的水平堆栈视图。 如果您设置`LayoutMarginsRelativeArrangement`属性设置为`true`，视图固定到相关的边距，而不是边缘子视图。

堆栈视图使用子视图的`IntrinsicContentSize`属性时计算沿所定义的子视图大小`Axis`(除`FillEqually Distribution`)。 `FillEqually Distribution`调整大小的所有子视图，以便它们都具有相同的大小，从而填堆栈视图沿`Axis`。

除`Fill Alignment`，在 Stack 视图使用子视图的`IntrinsicContentSize`用于计算与垂直视图的大小属性给定`Axis`。 有关`Fill Alignment`，所有子视图，以便它们填充到垂直堆栈视图大小将调整给定`Axis`。

<a name="Positioning-and-Sizing-the-Stack-View" />

## <a name="positioning-and-sizing-the-stack-view"></a>定位和调整堆栈视图

虽然在 Stack 视图具有完全控制权的任何子视图布局 (如根据属性对`Axis`和`Distribution`)，您仍需要定位堆栈视图 (`UIStackView`) 在其父视图中使用自动布局和大小类。

通常情况下，这意味着固定堆栈视图进行扩展和收缩，从而定义它的位置的至少两个边缘。 而无需任何附加约束，在 Stack 视图将自动调整大小以适合所有其子视图，如下所示：

* 此过程的大小及其`Axis`将为所有子视图大小加上任何已定义每个子视图之间的空间之和。
* 如果`LayoutMarginsRelativeArrangement`属性是`true`，堆栈视图大小还将包括边距的空间。
* 与垂直大小`Axis`将设置为集合中最大的子视图。

此外，可以指定约束的堆栈视图**高度**并**宽度**。 在这种情况下，子视图将布局 （大小） 来填充指定的堆栈视图由空间`Distribution`和`Alignment`属性。

如果`BaselineRelativeArrangement`属性是`true`，了子视图将布局基于第一个或最后一个子视图的基线，而不是使用**顶部**，**底部**或 **Center*-  **Y**位置。 这些计算堆栈视图的内容，如下所示：

* 垂直堆栈视图将返回第一个基线的第一个子视图和最后一个的最后一个。 如果任一这些子视图本身堆栈视图，则将使用其第一个或最后一个基线。
* 第一个和最后一个基线，水平堆栈视图将使用其最高的子视图。 如果最高视图也是堆栈视图，它将使用最高子视图作为基线。

> [!IMPORTANT]
> 基线对齐方式不会无法拉伸或压缩子视图大小按基线将计算到错误的位置。 对于基线对齐方式，请确保子视图的**高度**内部内容视图匹配**高度**。




<a name="Common-Stack-View-Uses" />

## <a name="common-stack-view-uses"></a>常见堆栈视图用法

有几种很好地配合堆栈视图控件的布局类型。 根据 Apple，下面是几个更常见的用途：

- **定义轴大小沿**– 通过固定堆栈视图沿两个边缘`Axis`和一条相邻边设置其位置，视图将沿轴以适合其子视图定义的空间增长的堆栈。
*  **定义子视图的位置，** – 这两个维度，以适合包含子视图固定到的父视图到在 Stack 视图相邻边缘，堆栈视图会增长。
- **定义的大小和位置堆栈的**– 堆栈视图固定到父视图堆栈视图的所有四个边，排列了子视图基于堆栈视图中定义的空间。
*  **定义大小垂直轴**– 通过固定为堆栈视图这两个边缘垂直`Axis`，另一个轴设置其位置，视图将增长以适应空间由定义的轴与垂直堆栈边缘其子视图。

<a name="Stack-Views-and-Storyboards" />

## <a name="stack-views-and-storyboards"></a>堆栈视图和情节提要

使用 Xamarin.tvOS 应用中的堆栈视图的最简单方法是将它们添加到应用程序的 UI 使用 iOS 设计器。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 在中**Solution Pad**，双击`Main.storyboard`文件，然后打开进行编辑。
1. 设计你要将添加到在 Stack 视图的各个元素的布局： 

    [![](stacked-views-images/layout01.png "元素布局示例")](stacked-views-images/layout01.png#lightbox)
1. 将任何所需的约束添加到的元素，以确保它们正确缩放。 该元素添加到在 Stack 视图后，此步骤非常重要。
1. 进行所需的数量的副本 （在此情况下需要四个）： 

    [![](stacked-views-images/layout02.png "所需的数量的副本")](stacked-views-images/layout02.png#lightbox)
1. 拖动**堆栈视图**从**工具箱**并将其放在视图上： 

    [![](stacked-views-images/layout03.png "堆栈视图")](stacked-views-images/layout03.png#lightbox)
1. 在中选择堆栈视图中，**小组件选项卡**的**Properties Pad**选择**填充**有关**对齐**，**填充同样**有关**分发**，然后输入`25`有关**间距**: 

    [![](stacked-views-images/layout04.png "小组件选项卡")](stacked-views-images/layout04.png#lightbox)
1. 放置在希望它和添加约束，以将其保存在所需位置的屏幕上的堆栈视图。
1. 选择单个元素并将其拖到在 Stack 视图： 

    [![](stacked-views-images/layout05.png "在 Stack 视图中的各个元素")](stacked-views-images/layout05.png#lightbox)
1. 将调整布局，并将在基于上述设置的属性在 Stack 视图中排列元素。
1. 将分配**名称**中**小组件选项卡**的**属性资源管理器**用于在 UI 控件C#的代码。
1. 保存更改。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 在中**解决方案资源管理器**，双击`Main.storyboard`文件，然后打开进行编辑。
1. 设计你要将添加到在 Stack 视图的各个元素的布局： 

    [![](stacked-views-images/layout01.png "元素布局示例")](stacked-views-images/layout01.png#lightbox)
1. 将任何所需的约束添加到的元素，以确保它们正确缩放。 该元素添加到在 Stack 视图后，此步骤非常重要。
1. 进行所需的数量的副本 （在此情况下需要四个）： 

    [![](stacked-views-images/layout02.png "所需的数量的副本")](stacked-views-images/layout02.png#lightbox)
1. 拖动**堆栈视图**从**工具箱**并将其放在视图上： 

    [![](stacked-views-images/layout03-vs.png "堆栈视图")](stacked-views-images/layout03-vs.png#lightbox)
1. 在中选择堆栈视图中，**小组件选项卡**的**属性资源管理器**选择**填充**有关**对齐**，**填充同样**有关**分发**，然后输入`25`有关**间距**: 

    [![](stacked-views-images/layout04-vs.png "小组件选项卡")](stacked-views-images/layout04-vs.png#lightbox)
1. 放置在希望它和添加约束，以将其保存在所需位置的屏幕上的堆栈视图。
1. 选择单个元素并将其拖到在 Stack 视图： 

    [![](stacked-views-images/layout05-vs.png "在 Stack 视图中的各个元素")](stacked-views-images/layout05-vs.png#lightbox)
1. 将调整布局，并将在基于上述设置的属性在 Stack 视图中排列元素。
1. 将分配**名称**中**小组件选项卡**的**属性资源管理器**用于在 UI 控件C#的代码。
1. 保存更改。

-----

> [!IMPORTANT]
> 虽然可以将分配操作，如`TouchUpInside`到用户界面元素 (如`UIButton`) 在 iOS 设计器创建事件处理程序时，它将永远不会调用，因为 Apple TV 没有触摸屏输入屏幕上或支持触控事件。 应始终使用默认值`Action Type`时创建 tvOS 用户界面元素的操作。

使用情节提要的详细信息，请参阅我们[你好，tvOS 快速入门指南](~/ios/tvos/get-started/hello-tvos.md)。

对于本示例中，我们已公开的输出口和操作段控件，并为每个"播放机卡"上的电源插座。 在代码中，我们可以隐藏和显示播放机基于当前段。 例如：

```csharp
partial void PlayerCountChanged (Foundation.NSObject sender) {

    // Take Action based on the segment
    switch(PlayerCount.SelectedSegment) {
    case 0:
        Player1.Hidden = false;
        Player2.Hidden = true;
        Player3.Hidden = true;
        Player4.Hidden = true;
        break;
    case 1:
        Player1.Hidden = false;
        Player2.Hidden = false;
        Player3.Hidden = true;
        Player4.Hidden = true;
        break;
    case 2:
        Player1.Hidden = false;
        Player2.Hidden = false;
        Player3.Hidden = false;
        Player4.Hidden = true;
        break;
    case 3:
        Player1.Hidden = false;
        Player2.Hidden = false;
        Player3.Hidden = false;
        Player4.Hidden = false;
        break;
    }
}
```

当应用运行时，四个元素将均衡分配在 Stack 视图中：

[![](stacked-views-images/layout06.png "当应用运行时，四个元素同样将分布在堆栈视图")](stacked-views-images/layout06.png#lightbox)

如果减小的播放机数目，未使用的视图隐藏的在 Stack 视图调整布局以适合：

[![](stacked-views-images/layout07.png "如果减小的播放机数目，未使用的视图隐藏的在 Stack 视图调整布局以适应")](stacked-views-images/layout07.png#lightbox)

<a name="Populate-a-Stack-View-from-Code" />

### <a name="populate-a-stack-view-from-code"></a>填充中代码的堆栈视图

除了完全 iOS 设计器中定义的内容和堆栈视图的布局，可以创建和删除其动态地从C#代码。

执行下面的示例使用堆栈视图处理"星"中查看 (1 到 5):

```csharp
public int Rating { get; set;} = 0;
...

partial void IncreaseRating (Foundation.NSObject sender) {

    // Maximum of 5 "stars"
    if (++Rating > 5 ) {
        // Abort
        Rating = 5;
        return;
    }

    // Create new rating icon and add it to stack
    var icon = new UIImageView (new UIImage("icon.png"));
    icon.ContentMode = UIViewContentMode.ScaleAspectFit;
    RatingView.AddArrangedSubview(icon);

    // Animate stack
    UIView.Animate(0.25, ()=>{
        // Adjust stack view
        RatingView.LayoutIfNeeded();
    });

}

partial void DecreaseRating (Foundation.NSObject sender) {

    // Minimum of zero "stars"
    if (--Rating < 0) {
        // Abort
        Rating =0;
        return;
    }

    // Get the last subview added
    var icon = RatingView.ArrangedSubviews[RatingView.ArrangedSubviews.Length-1];

    // Remove from stack and screen
    RatingView.RemoveArrangedSubview(icon);
    icon.RemoveFromSuperview();

    // Animate stack
    UIView.Animate(0.25, ()=>{
        // Adjust stack view
        RatingView.LayoutIfNeeded();
    });
}
```

让我们看看此代码的几条中详细信息。 首先，我们使用`if`语句来检查不超过五个"星"或小于零。

若要添加新的"star"我们加载其图像并设置其**内容模式**到**缩放方面适合**:

```csharp
var icon = new UIImageView (new UIImage("icon.png"));
icon.ContentMode = UIViewContentMode.ScaleAspectFit;
```

这可以防止"星形"图标添加到在 Stack 视图时被扭曲。

接下来，我们将新的"星形"图标添加到在 Stack 视图的集合的子视图：

```csharp
RatingView.AddArrangedSubview(icon);
```

您会注意到我们添加了`UIImageView`到`UIStackView`的`ArrangedSubviews`属性，而非`SubView`。 必须将你想要控制其布局的堆栈视图任何视图添加到`ArrangedSubviews`属性。

若要从堆栈视图中删除子视图，首先我们获取子视图若要删除：

```csharp
var icon = RatingView.ArrangedSubviews[RatingView.ArrangedSubviews.Length-1];
```

然后我们需要先删除该从这两个`ArrangedSubviews`集合和超级视图：

```csharp
// Remove from stack and screen
RatingView.RemoveArrangedSubview(icon);
icon.RemoveFromSuperview();
```

只需从删除子视图`ArrangedSubviews`集合采用出堆栈视图的控件，但不会从屏幕上删除它。

<a name="Dynamically-Changing-Content" />

## <a name="dynamically-changing-content"></a>动态地更改内容

每当添加、 删除或隐藏子视图时，堆栈视图将自动调整了子视图的布局。 如果调整堆栈视图的任何属性，也进行调整布局 (如其`Axis`)。

布局更改可以进行动画处理，从而例如在动画块中，将它们：

```csharp
// Animate stack
UIView.Animate(0.25, ()=>{
    // Adjust stack view
    RatingView.LayoutIfNeeded();
});
```

可以使用情节提要的大小类指定许多堆栈视图的属性。 这些属性将自动经过动画处理的是响应的大小或方向更改。

<a name="Summary" />

## <a name="summary"></a>总结

本文只讨论了设计和在 Xamarin.tvOS 应用内使用堆积图视图。



## <a name="related-links"></a>相关链接

- [tvOS 示例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人机接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [适用于 tvOS 应用编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
