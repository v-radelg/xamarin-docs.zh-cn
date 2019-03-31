---
title: 在 Xamarin.iOS 堆栈视图
description: 本文介绍如何使用 Xamarin.iOS 应用程序中的新 UIStackView 控件管理的子视图中的一组水平或垂直排列的堆栈。
ms.prod: xamarin
ms.assetid: 20246E87-2A49-438A-9BD7-756A1B50A617
ms.technology: xamarin-ios
ms.custom: xamu-video
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: 6d5be72a9329675a65b0d6873d13894b314b50e7
ms.sourcegitcommit: 946ce514fd6575aa6b93ff24181e02a60b24b106
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2019
ms.locfileid: "58677841"
---
# <a name="stack-views-in-xamarinios"></a>在 Xamarin.iOS 堆栈视图

_本文介绍如何使用 Xamarin.iOS 应用程序中的新 UIStackView 控件管理的子视图中的一组水平或垂直排列的堆栈。_

> [!IMPORTANT]
> 请注意，尽管 StackView 支持在 iOS 设计器中，您可能会遇到可用性错误时使用稳定通道。 Beta 版或 Alpha 通道切换应可解决此问题。 我们已决定提供本演练中使用 Xcode，直到在稳定通道中实现所需的修补程序。

堆栈视图控件 (`UIStackView`) 利用自动布局和大小类水平或垂直，管理的子视图，堆栈的强大，但该功能可动态响应的 iOS 设备的方向和屏幕大小。

附加到堆栈视图的所有子视图的布局受它基于等轴、 分发、 对齐方式和间距的开发人员定义属性：

[![](uistackview-images/stacked01.png "堆栈视图布局关系图")](uistackview-images/stacked01.png#lightbox)

当使用`UIStackView`在 Xamarin.iOS 应用中，开发人员可以定义子视图是在情节提要设计器中，在 iOS 中或通过添加和删除子视图中的 C# 代码。

本文档包括两个部分： 快速入门可帮助你实现您的第一个堆栈查看，且然后有关其工作原理一些更多技术详细信息。

> [!VIDEO https://youtube.com/embed/p3po6507Ip8]

**UIStackView，也可由[Xamarin 学院课程](https://university.xamarin.com/)**

## <a name="uistackview-quickstart"></a>UIStackView 快速入门

作为的快速简介`UIStackView`控件中，我们将要创建一个简单的界面，允许用户输入 1 到 5 分级。 我们将使用两个堆栈视图： 一个用于排列垂直方向上设备的屏幕，另一个用于在屏幕上水平排列 1-5 分级图标的接口。

### <a name="define-the-ui"></a>用于定义 UI

启动一个新的 Xamarin.iOS 项目和编辑**Main.storyboard** Xcode 的 Interface Builder 中的文件。 首先，将单个**垂直堆栈视图**上**视图控制器**:

[![](uistackview-images/quick01.png "将视图控制器上的单个垂直堆栈视图")](uistackview-images/quick01.png#lightbox)

在中**属性检查器**，设置以下选项：

[![](uistackview-images/quick02.png "设置堆栈视图选项")](uistackview-images/quick02.png#lightbox)

其中：

- **轴**– 确定是否堆栈视图排列了子视图既**水平**或**垂直**。
- **对齐方式**– 控制了子视图堆栈视图内的对齐方式。
- **分发**– 控制如何了子视图的大小将调整堆栈视图中。
- **间距**– 控制每个堆栈视图中的子视图之间的最小空间。
- **基线相对**– 如果选中，将从其基线派生每个子视图的垂直间距。
- **布局边距相对**– 相对于标准布局边距中放置了子视图。

在使用堆栈视图，您可以将**对齐**作为**X**并**Y**子视图的位置和**分发**为**高度**并**宽度**。

> [!IMPORTANT]
> `UIStackView` 专为非呈现容器视图并在这种情况下，不绘制到等其他类的子类画布`UIView`。 因此，如设置属性`BackgroundColor`或重写`DrawRect`将具有任何可视化效果。

继续到布局，添加一个标签、 ImageView、 两个按钮和水平堆栈视图，使其类似于以下应用程序的接口：

[![](uistackview-images/quick03.png "对堆栈视图 UI 进行布局")](uistackview-images/quick03.png#lightbox)

使用以下选项配置水平堆栈视图：

[![](uistackview-images/quick04.png "配置水平堆栈视图选项")](uistackview-images/quick04.png#lightbox)

因为我们不希望代表每个"点"的图标中要拉伸的分级时添加到水平堆栈视图中，我们设置了**对齐**到**Center**和**分发**到**同样填充**。

最后，接通以下**输出口**并**操作**:

[![](uistackview-images/quick05.png "堆栈视图输出口和操作")](uistackview-images/quick05.png#lightbox)

### <a name="populate-a-uistackview-from-code"></a>填充代码从 UIStackView

返回到 Visual Studio for Mac 和编辑**ViewController.cs**文件，并添加以下代码：

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

### <a name="testing-the-ui"></a>测试 UI

包含所有所需的 UI 元素和位置中的代码，现在可以运行并测试该接口。 显示用户界面时，所有垂直堆栈视图中的元素将均匀分布从上到下。

当用户点击**提升评级**按钮，另一个"星型"添加到屏幕 （最多 5）：

[![](uistackview-images/intro01.png "运行示例应用")](uistackview-images/intro01.png#lightbox)

将自动居中和均匀分布在水平堆栈视图"星"。 当用户点击**降低评级**按钮，"星型"将删除 （直到无保留）。

## <a name="stack-view-details"></a>堆栈查看详细信息

现在，我们已经大概的什么`UIStackView`控件是和它的工作原理，让我们深入探讨的一些其功能和详细信息。

### <a name="auto-layout-and-size-classes"></a>自动布局和大小类

如上面看到的当子视图添加到堆栈视图其布局由使用自动布局和大小类的位置和大小排列的视图该堆栈视图完全控制。

堆栈视图将_pin_到其集合中的第一个和最后一个子视图**顶部**并**底部**边缘的垂直堆栈视图或**留**并**右**边缘的水平堆栈视图。 如果您设置`LayoutMarginsRelativeArrangement`属性设置为`true`，视图固定到相关的边距，而不是边缘子视图。

堆栈视图使用子视图的`IntrinsicContentSize`属性时计算沿所定义的子视图大小`Axis`(除`FillEqually Distribution`)。 `FillEqually Distribution`调整大小的所有子视图，以便它们都具有相同的大小，从而填堆栈视图沿`Axis`。

除`Fill Alignment`，在 Stack 视图使用子视图的`IntrinsicContentSize`用于计算与垂直视图的大小属性给定`Axis`。 有关`Fill Alignment`，所有子视图，以便它们填充到垂直堆栈视图大小将调整给定`Axis`。

### <a name="positioning-and-sizing-the-stack-view"></a>定位和调整堆栈视图

虽然在 Stack 视图具有完全控制权的任何子视图布局 (如根据属性对`Axis`和`Distribution`)，您仍需要定位堆栈视图 (`UIStackView`) 在其父视图中使用自动布局和大小类。

通常情况下，这意味着固定堆栈视图进行扩展和收缩，从而定义它的位置的至少两个边缘。 而无需任何附加约束，在 Stack 视图将自动调整大小以适合所有其子视图，如下所示：

 - 此过程的大小及其`Axis`将为所有子视图大小加上任何已定义每个子视图之间的空间之和。
 - 如果`LayoutMarginsRelativeArrangement`属性是`true`，堆栈视图大小还将包括边距的空间。
 - 与垂直大小`Axis`将设置为集合中最大的子视图。

此外，可以指定约束的堆栈视图**高度**并**宽度**。 在这种情况下，子视图将布局 （大小） 来填充指定的堆栈视图由空间`Distribution`和`Alignment`属性。

如果`BaselineRelativeArrangement`属性是`true`，了子视图将布局基于第一个或最后一个子视图的基线，而不是使用**顶部**，**底部**或**Center**-  **Y**位置。 这些计算堆栈视图的内容，如下所示：

 - 垂直堆栈视图将返回第一个基线的第一个子视图和最后一个的最后一个。 如果任一这些子视图本身堆栈视图，则将使用其第一个或最后一个基线。
 - 第一个和最后一个基线，水平堆栈视图将使用其最高的子视图。 如果最高视图也是堆栈视图，它将使用最高子视图作为基线。

> [!IMPORTANT]
> 基线对齐方式不会无法拉伸或压缩子视图大小按基线将计算到错误的位置。 对于基线对齐方式，请确保子视图的**高度**内部内容视图匹配**高度**。

### <a name="common-stack-view-uses"></a>常见堆栈视图用法

有几种很好地配合堆栈视图控件的布局类型。 根据 Apple，下面是几个更常见的用途：

- **定义轴大小沿**– 通过固定堆栈视图沿两个边缘`Axis`和一条相邻边设置其位置，视图将沿轴以适合其子视图定义的空间增长的堆栈。
 -  **定义子视图的位置，** – 这两个维度，以适合包含子视图固定到的父视图到在 Stack 视图相邻边缘，堆栈视图会增长。
- **定义的大小和位置堆栈的**– 堆栈视图固定到父视图堆栈视图的所有四个边，排列了子视图基于堆栈视图中定义的空间。
 -  **定义大小垂直轴**– 通过固定为堆栈视图这两个边缘垂直`Axis`，另一个轴设置其位置，视图将增长以适应空间由定义的轴与垂直堆栈边缘其子视图。

### <a name="managing-the-appearance"></a>管理外观

`UIStackView`专为非呈现容器视图并在这种情况下，不绘制到等其他类的子类画布`UIView`。 设置属性，如`BackgroundColor`或重写`DrawRect`将具有任何可视化效果。

有几个属性，用于控制如何堆栈视图时，将排列其子视图的集合：

- **轴**– 确定是否堆栈视图排列了子视图既**水平**或**垂直**。
- **对齐方式**– 控制了子视图堆栈视图内的对齐方式。
- **分发**– 控制如何了子视图的大小将调整堆栈视图中。
- **间距**– 控制每个堆栈视图中的子视图之间的最小空间。
- **基线相对**– 如果`true`，每个子视图的垂直间距将派生自它的基线。
- **布局边距相对**– 相对于标准布局边距中放置了子视图。

通常将使用堆栈视图来排列子视图的一小部分。 可以通过嵌套在每个其他的一个或多个堆栈视图来创建更复杂的用户界面 (正如我们做[UIStackView 快速入门](#uistackview-quickstart)上面)。

通过将其他约束添加到了子视图 （例如到控件的高度或宽度），可以进一步微调 Ui 外观。 但是，应格外小心，不以包括对这些引入的堆栈视图本身的冲突约束。

### <a name="maintaining-arranged-views-and-sub-views-consistency"></a>维护排列视图和子视图一致性

堆栈视图将确保其`ArrangedSubviews`属性始终是的子集其`Subviews`属性使用以下规则：

 - 如果子视图添加到`ArrangedSubviews`集合，它将自动添加到`Subviews`集合 （除非它已是该集合的一部分）。
 - 如果从删除子视图`Subviews`（从显示中删除） 的集合，它也会从删除`ArrangedSubviews`集合。
 - 删除从子视图`ArrangedSubviews`集合不会删除从`Subviews`集合。 因此，它将无法再进行布局由堆栈视图，但仍会在屏幕上可见。

`ArrangedSubviews`集合始终是的子集`Subview`集合，但是每个集合中单个子视图的顺序是单独且可控制通过以下方法：

 - 中子视图的顺序`ArrangedSubviews`集合确定堆栈内的其显示顺序。
 - 中子视图的顺序`Subview`集合后至前视图中确定其 Z 顺序 （或分层）。

### <a name="dynamically-changing-content"></a>动态地更改内容

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

## <a name="summary"></a>总结

本文只讨论了新`UIStackView`控制 （适用于 iOS 9) 管理的任一水平或垂直排列在 Xamarin.iOS 应用程序堆栈中的子视图集。
它使用堆栈视图创建的 UI 的一个简单的示例开始，便完成了更详细地查看堆栈视图及其属性和功能。



## <a name="related-links"></a>相关链接

- [iOS 9 示例](https://developer.xamarin.com/samples/ios/iOS9/)
- [面向开发人员的 iOS 9](https://developer.apple.com/ios/pre-release/)
- [What's New iOS 9.0 中](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [UIStackView 引用](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIStackView_Class_Reference/)
- [引入 UIStackView （视频）](https://university.xamarin.com/lightninglectures/introducing-uistackview-on-ios9)
