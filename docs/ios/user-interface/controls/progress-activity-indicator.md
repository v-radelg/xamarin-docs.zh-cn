---
title: Xamarin 中的进度和活动指示器
description: 本文档介绍如何在 Xamarin 中使用进度和活动指示器。 本文介绍如何以编程方式和使用情节提要来使用它们。
ms.prod: xamarin
ms.assetid: 7AA887E4-51F7-4867-82C5-A8D2EA48AE07
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 07/11/2017
ms.openlocfilehash: a2197a1ff9c37546fd97eb5a2459764ec05d4412
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70768918"
---
# <a name="progress-and-activity-indicators-in-xamarinios"></a>Xamarin 中的进度和活动指示器

您的应用程序很可能需要执行长时间运行的任务（例如加载或处理数据），这种延迟可能会导致更新 UI 的延迟。 在此期间，应始终使用进度指示器来再次向用户正在执行的工作。 这为用户控件提供了应用程序处理其请求的操作，该控件不会等待其输入，并提供一种方法来详细地说明它们必须等待多长时间。

iOS 提供两种主要方法来在应用程序中提供此进度指示：活动指示器（包括特定_网络_活动指示器）和进度栏。

## <a name="activity-indicator"></a>活动指示器

当应用程序运行较长时间，但不知道任务需要的确切时间长度时，应显示活动指示器。

Apple 对于使用活动指示器有以下建议：

- **请尽可能使用进度栏，** 因为活动指示器为用户提供了对运行进程所需时间的反馈，如果长度已知（例如，要在文件中下载的字节数），请始终使用进度栏。
- **使指示器具有动画效果**-用户将静止活动指示器与停用的应用保持联系，因此，在显示指示器时，始终应动画指示器。
- **描述正在处理的任务**-只需自行显示活动指示器并不足够，用户需要知道他们正在等待的进程。 包含明确定义任务的有意义的标签（通常是单个完整的句子）。

### <a name="implementing-an-activity-indicator"></a>实现活动指示器

活动指示器通过[`UIActivityIndictorView`](xref:UIKit.UIActivityIndicatorView)类实现以指示`UIActivity`正在进行。

### <a name="activity-indicators-and-storyboards"></a>活动指示器和情节提要

如果使用 iOS 设计器创建用户界面，则可以从 "工具箱" 将活动指示器添加到布局。 以下属性可以从 Properties Pad 进行调整：

![Properties Pad](progress-activity-indicator-images/progress-indicator1.png)

### <a name="managing-activity-indicator-behavior"></a>管理活动指示器行为

`StartAnimating()`使用和`StopAnimating()`方法可以启动和停止活动指示器动画。

将属性设置为`true` ，以使活动指示器在调用`StopAnimating()`后消失。 `HidesWhenStopped` 默认情况下， `true`此值设置为。 在任何时候，都可以通过检查`IsAnimating`属性来查看活动指示器是否正在运行其旋转动画。 

### <a name="managing-activity-indicator-appearances"></a>管理活动指示器外观

在`UIActivityIndicatorViewStyle`实例化活动指示器时，可以将枚举作为参数传递。 可以使用它将视觉样式设置为`Gray`、 `White`或`WhiteLarge`，例如：

```csharp
activitySpinner = new UIActivityIndicatorView(UIActivityIndicatorViewStyle.WhiteLarge);
```

可以`UIActivityIndicatorViewStyle` 通过`Color`设置属性来重写提供的颜色。

## <a name="progress-bar"></a>进度栏

进度栏显示为一条线条，其中填充了颜色以指示耗时任务的状态和时间长度。 当任务的长度知道或可以计算时，应始终使用进度栏。

Apple 对于使用进度条有以下建议：

- **准确报告进度**-进度栏应始终准确表示完成任务所需的时间。 请不要将应用程序显示为忙的时间。
- **使用定义良好的持续**时间-进度栏不仅应显示长时间的任务，还应为用户提供完成任务的时间和估计剩余时间。

### <a name="implementing-an-progress-bar"></a>实现进度栏

通过实例化[`UIProgressView`](xref:UIKit.UIProgressView)

### <a name="progress-bars-and-storyboards"></a>进度栏和情节提要

使用 iOS 设计器时，还可以将进度栏添加到 UI。 在 "**工具箱**" 中搜索**进度视图**，并将其拖动到您的视图。

可以在 "属性" 板上调整以下属性：

![Properties Pad](progress-activity-indicator-images/progress-indicator3.png)

### <a name="managing-progress-bar-behavior"></a>管理进度栏行为

通过使用`Progress`属性，可以最初设置条形的进度：

```csharp
ProgressBar.Progress = 0f;
```

可以通过使用`SetProgress`方法调整进度，并在需要对更改进行动画处理时传递布尔声明。

```csharp
ProgressBar.SetProgress(1.0f, true);
```

有关使用进度栏的详细信息，请参阅[报告进度](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/networking/download_progress)食谱和[UICatalog tvOS 示例](https://docs.microsoft.com/samples/xamarin/ios-samples/tvos-uicatalog)。

### <a name="managing-progress-bar-appearance"></a>管理进度栏外观

与活动指示器类似，在实例`UIProgressViewStyle`化进度栏时，可以将枚举作为参数传递。

可以使用以下属性调整进度和跟踪图像及色调颜色：

```csharp
progressBar = new UIProgressView(UIProgressViewStyle.Default)
            {
                ProgressImage = UIImage.FromBundle("TrackImage"),
                ProgressTintColor = UIColor.Cyan,
                TrackImage = UIImage.FromBundle("TrackImage"),
                TrackTintColor = UIColor.Magenta
            }; 
```
