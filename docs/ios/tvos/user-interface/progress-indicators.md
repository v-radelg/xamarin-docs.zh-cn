---
title: 使用 tvOS 在 Xamarin 中的进度指示器
description: 本文档介绍如何使用在 tvOS 应用程序中使用 Xamarin 生成的进度指示器等。 它讨论了进度条和活动指示器。
ms.prod: xamarin
ms.assetid: 582B6D0C-1F16-4299-A9A6-5651E76009FE
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/25/2018
ms.openlocfilehash: cbd2b2de237a5bb22d1dc0242569b96b12bca070
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61180517"
---
# <a name="working-with-tvos-progress-indicators-in-xamarin"></a>使用 tvOS 在 Xamarin 中的进度指示器

_本文介绍如何设计和使用 Xamarin.tvOS 应用内的进度指示器。_

可能有 Xamarin.tvOS 应用需要加载新内容或执行耗时较长的处理操作的时间。 在这些时段内应存在一个活动指示器或一个进度栏，让用户知道应用程序仍在运行并为其提供一些指明正在运行的任务的长度。

![示例进度指示器](progress-indicators-images/intro01.png "示例进度指示器")

## <a name="about-activity-indicators"></a>有关活动指示器

活动指示器显示旋转齿轮图标，并且，用于表示不确定长度的任务。 指示器会显示在任务启动并完成此任务将消失。

Apple 已使用活动指示器的以下建议：

- **只要有可能，请改为使用进度条**-因为如果长度已知 （例如，若要下载的文件中的字节数） 后，活动指示器提供任何反馈方式长正在运行的进程，用户将执行，将始终使用一个进度栏。
- **保留经过动画处理的指示器**-用户与相关的静态活动指示器已停止的应用程序，因此应始终进行动画处理的指示符显示它的同时。
- **描述任务正在处理**-本身显示活动指示器以外还不够; 用户需要了解在其等待的过程。 包含有意义的标签 （通常一个单一的完整句子），以明确定义的任务。

## <a name="about-progress-bars"></a>有关进度栏

进度栏显示为一条线的填充颜色来表示的状态和长度的耗时的任务。 任务的长度已知或可计算时，应始终使用进度条。

Apple 已使用进度条的以下建议：

- **准确地报告进度**-进度栏应始终存在的准确表示形式来完成任务所需的时间。 永远不会误报使应用显示繁忙的时间。
- **用于定义完善的持续时间**-进度条，不应仅显示较长的任务正在放置，但在为用户和指示完成的任务和估计剩余时间。

## <a name="progress-indicators-and-storyboards"></a>进度指示器和情节提要

使用 Xamarin.tvOS 应用中的进度指示器的最简单方法是将其添加到应用程序的 UI 使用 iOS 设计器。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)
    
1. 在中**Solution Pad**，双击**Main.storyboard**文件，然后打开进行编辑。

2. 拖动**活动指示器**从**工具箱**并将其放置在视图上： 

    ![活动指示器](progress-indicators-images/activity01.png "活动指示器")

3. 在中**小组件**选项卡**Properties Pad**，您可以调整活动指示器的多个属性，如其**样式**，**行为**，并**名称**: 

    ![小组件选项卡为活动指示器](progress-indicators-images/activity02.png "活动指示器的小组件选项卡")
    
    **名称**确定表示中的活动指示符的属性的名称C#代码。

4. 拖动**进度视图**从**工具箱**并将其放置在视图上： 

    ![进度视图](progress-indicators-images/activity03.png "进度视图")

5. 在中**小组件**选项卡**属性资源管理器**，您可以调整进度视图的多个属性，如其**样式**，**进度**（完成百分比），并**名称**: 

    ![进度图小组件选项卡](progress-indicators-images/activity04.png "小组件选项卡进度视图")
    
    **名称**确定表示进度视图中的属性的名称C#代码。

6. 保存更改。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)
    
1. 在中**解决方案资源管理器**，双击**Main.storyboard**文件，然后打开进行编辑。

2. 拖动**活动指示器**从**工具箱**并将其放置在视图上： 

    ![活动指示器](progress-indicators-images/activity01-vs.png
    "活动指示器")

3. 在中**小组件**选项卡**属性资源管理器**，您可以调整活动指示器的多个属性，如其**样式**，**行为**，并**名称**: 

    ![小组件选项卡为活动指示器](progress-indicators-images/activity02-vs.png "活动指示器的小组件选项卡")

    **名称**确定表示中的活动指示符的属性的名称C#代码。

4. 拖动**进度视图**从**工具箱**并将其放置在视图上： 

   ![进度视图](progress-indicators-images/activity03-vs.png "进度视图")

5. 在中**小组件**选项卡**属性资源管理器**，您可以调整进度视图的多个属性，如其**样式**，**进度**（完成百分比），并**名称**: 

    ![进度图小组件选项卡](progress-indicators-images/activity04-vs.png "小组件选项卡进度视图")
    
    **名称**确定表示进度视图中的属性的名称C#代码。

6. 保存更改。

-----

使用情节提要的详细信息，请参阅我们[你好，tvOS 快速入门指南](~/ios/tvos/get-started/hello-tvos.md)。 

## <a name="working-with-activity-indicators"></a>使用活动指示器

如上面所述，在您的应用程序正在运行不确定的长度在长进程时，应显示活动指示器。

在任何时候，您可以看到活动指示器具有动画效果通过检查其`IsAnimating`属性。 如果`HidesWhenStopped`属性是`true`，停止动画时，将自动隐藏活动指示器。

下面的代码可用于启动动画： 

```csharp
ActivityIndicator.StartAnimating();
```

并且，以下将停止动画：

```csharp
ActivityIndicator.StopAnimating();
```

> [!NOTE]
> 这些代码段假定活动指示器**名称**已设置为**ActivityIndicator**中**小组件**iOS 设计器选项卡。

## <a name="working-with-progress-bars"></a>使用进度条

同样，一个进度栏，应使用您的应用程序正在执行长时间运行任务已知的持续任何的时间时间。 

`Progress`属性用于设置已完成从 0%到 100%(0.0 到 1.0) 的任务的量。 使用`ProgressTintColor`属性来设置已完成的量条的颜色和`TrackTintColor`属性来设置背景色 （未完成量）。

## <a name="summary"></a>总结

本文只讨论了设计和使用 Xamarin.tvOS 应用内的进度指示器。

## <a name="related-links"></a>相关链接

- [tvOS 示例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人机接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [适用于 tvOS 应用编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
