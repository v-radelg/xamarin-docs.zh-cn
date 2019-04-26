---
title: 进度和活动指示器在 Xamarin.iOS
description: 本文档讨论了如何在 Xamarin.iOS 中使用进度和活动指示器。 它介绍了如何使用它们以编程方式和使用演示图板。
ms.prod: xamarin
ms.assetid: 7AA887E4-51F7-4867-82C5-A8D2EA48AE07
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/11/2017
ms.openlocfilehash: d39170d0109d7f81d3f02ec36381ebcd46c0143d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61029570"
---
# <a name="progress-and-activity-indicators-in-xamarinios"></a>进度和活动指示器在 Xamarin.iOS

很可能您的应用程序将需要执行长时间运行任务，例如加载或处理数据和这种延迟，可能会延迟导致更新 UI。 在此期间您应该始终使用进度指示器确信用户系统正忙于执行工作。 这使用户可以控制的应用程序正在处理其请求，它不会等待其输入，并可提供一种方法的详细介绍了它们需要等待的确切时间。

iOS 提供了两种主要方法来提供此进度指示应用程序中：活动指示器 (包括特定_网络_活动指示器) 和进度栏。

## <a name="activity-indicator"></a>活动指示器

您的应用程序正在运行一个长的过程，但不知道确切的任务所需的时间长度时，应显示活动指示器。

Apple 已使用活动指示器的以下建议：

- **只要有可能，请改用进度条**-活动指示器为用户不提供任何反馈正在运行的进程将需要多长，因为如果 （例如，若要下载的文件中的字节数） 知道长度始终使用一个进度栏。
- **保留指示器经过动画处理**-用户： 与静态活动指示器相关到已停止的应用程序，因此应始终具有经过动画处理时所显示的指示器。
- **描述任务正在处理**-本身显示活动指示器以外还不够，用户需要了解它们正在等待的过程。 包含有意义的标签 （通常一个单一的完整句子），以明确定义的任务。

### <a name="implementing-an-activity-indicator"></a>实现活动指示器

通过实现活动指示器[ `UIActivityIndictorView` ](xref:UIKit.UIActivityIndicatorView)类，以指示的`UIActivity`正在进行。

### <a name="activity-indicators-and-storyboards"></a>活动指示器和情节提要

如果使用 iOS 设计器创建 UI，可以从工具箱拖到你的布局添加活动指示器。 从属性面板，可以调整以下属性：

![属性面板](progress-activity-indicator-images/progress-indicator1.png)

### <a name="managing-activity-indicator-behavior"></a>管理活动指示器行为

使用`StartAnimating()`和`StopAnimating()`方法来启动和停止活动指示器动画。

设置`HidesWhenStopped`属性设置为`true`使活动指示器消失后`StopAnimating()`已调用。 此值设置为`true`默认情况下。 随时可以查看活动指示器是否通过检查运行其旋转动画`IsAnimating`属性。 


### <a name="managing-activity-indicator-appearances"></a>管理活动指示器的外观

`UIActivityIndicatorViewStyle`在实例化活动指示器时，枚举可以作为参数传递。 可以使用此设置为视觉样式`Gray`， `White`，或`WhiteLarge`，例如：

```csharp
activitySpinner = new UIActivityIndicatorView(UIActivityIndicatorViewStyle.WhiteLarge);
```

您可以重写由提供的颜色`UIActivityIndicatorViewStyle`通过设置`Color`属性。

## <a name="progress-bar"></a>进度栏

进度栏显示为一条线的填充颜色来表示的状态和长度的耗时的任务。 任务的长度是知道或可以计算时，应始终使用进度条。

Apple 已使用进度条的以下建议：

- **准确地报告进度**-进度栏应始终为所需的时间来完成任务的准确表示形式。 永远不会误报使应用显示繁忙的时间。
- **用于 Well-Defined 持续时间**-进度栏，不应仅显示较长的任务正在放置，但在为用户和指示完成的任务和估计剩余时间。

### <a name="implementing-an-progress-bar"></a>实现进度栏

通过实例化来创建一个进度栏 [`UIProgressView`](xref:UIKit.UIProgressView)

### <a name="progress-bars-and-storyboards"></a>进度条和情节提要

使用 iOS 设计器时，还可以向你的 UI 添加一个进度栏。 搜索**进度视图**中**工具箱**并将其拖到视图。

在属性面板上，可以调整以下属性：

![属性面板](progress-activity-indicator-images/progress-indicator3.png)


### <a name="managing-progress-bar-behavior"></a>管理进度栏行为

可以通过使用最初设置进度栏的`Progress`属性：

```csharp
ProgressBar.Progress = 0f;
```

可以使用调整进度`SetProgress`方法并传递声明是否经过动画处理的或不更改一个布尔值。

```csharp
ProgressBar.SetProgress(1.0f, true);
```

有关使用进度栏的详细信息，请参阅[报告进度](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/networking/download_progress)方案，并[UICatalog tvOS 示例](https://developer.xamarin.com/samples/monotouch/tvos/UICatalog/)。

### <a name="managing-progress-bar-appearance"></a>管理进度栏的外观

类似于活动指示器，`UIProgressViewStyle`在实例化进度栏时，枚举可以作为参数传递。

可以通过使用以下属性调整的进度并跟踪图像和着色颜色：

```csharp
progressBar = new UIProgressView(UIProgressViewStyle.Default)
            {
                ProgressImage = UIImage.FromBundle("TrackImage"),
                ProgressTintColor = UIColor.Cyan,
                TrackImage = UIImage.FromBundle("TrackImage"),
                TrackTintColor = UIColor.Magenta
            }; 
```



