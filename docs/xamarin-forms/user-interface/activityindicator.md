---
title: 在 Xamarin.Forms 中的活动指示器
description: 在 ActivityIndicator 控件向用户指示应用程序所从事的耗时较长的活动，而无需授予任何可能的进度。 本文介绍如何使用 XAML 和代码中的 ActivityIndicator。
ms.prod: xamarin
ms.assetid: 4CEED02D-5CA3-4C3A-B7ED-3193FC272261
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/10/2019
ms.openlocfilehash: abd8150e3aa4ec347c8d956004993340630208bf
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67837057"
---
# <a name="xamarinforms-activityindicator"></a>Xamarin.Forms ActivityIndicator
[![下载示例](~/media/shared/download.png) 下载示例](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ActivityIndicatorDemos)

Xamarin.Forms[ `ActivityIndicator` ](xref:Xamarin.Forms.ActivityIndicator)是显示动画，以显示应用程序将执行相应耗时较长的活动中的控件。 与不同[ `ProgressBar` ](xref:Xamarin.Forms.ProgressBar)，则`ActivityIndicator`没有正在进行的指示。 `ActivityIndicator`继承自[ `View` ](xref:Xamarin.Forms.View)。

以下屏幕截图显示`ActivityIndicator`iOS 和 Android 上的控件：

![IOS 和 Android 上的屏幕截图的 ActivityIndicator](activityindicator-images/activityindicators-default.png "iOS 和 Android 上的 ActivityIndicator 屏幕快照")

`ActivityIndicator`控件定义以下属性：

* [`IsRunning`](xref:Xamarin.Forms.ActivityIndicator.IsRunning) 是`bool`值，该值指示是否`ActivityIndicator`应为可见和进行动画处理，还是隐藏。 值是何时`false``ActivityIndicator`不会显示。
* [`Color`](xref:Xamarin.Forms.ActivityIndicator.Color) 是`Color`值，该值定义显示颜色的`ActivityIndicator`。

这些属性受到[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)对象，这意味着`ActivityIndicator`可以设置的样式和作为目标的数据绑定。

## <a name="create-an-activityindicator"></a>创建 ActivityIndicator

`ActivityIndicator`可以在 XAML 中实例化。 其`IsRunning`可以设置属性以确定控件是否可见且具有动画效果。 如果`IsRunning`属性未设置，则默认为`false`和`ActivityIndicator`不会显示。 下面的示例演示如何实例化`ActivityIndicator`中使用可选的 XAML`IsRunning`属性集：

```xaml
<ActivityIndicator IsRunning="true" />
```

`ActivityIndicator`还可以在代码中创建：

```csharp
ActivityIndicator activityIndicator = new ActivityIndicator { IsRunning = true };
```

## <a name="activityindicator-appearance-properties"></a>ActivityIndicator 外观属性

`Color`可以设置属性来定义`ActivityIndicator`颜色。 下面的示例演示如何实例化`ActivityIndicator`中使用的 XAML`Color`属性集：

```xaml
<ActivityIndicator Color="Orange" />
```

`Color`创建时，还可以设置属性`ActivityIndicator`在代码中：

```csharp
ActivityIndicator activityIndicator = new ActivityIndicator { Color = Color.Orange };
```

以下屏幕截图显示`ActivityIndicator`与`Color`属性设置为`Color.Orange`iOS 和 Android 上：

![带样式的 ActivityIndicator 在 iOS 和 Android 的屏幕截图](activityindicator-images/activityindicators-styled.png "样式的 ActivityIndicator 在 iOS 和 Android 的屏幕截图")

## <a name="related-links"></a>相关链接

* [ActivityIndicator 演示](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ActivityIndicatorDemos)
* [进度栏](~/xamarin-forms/user-interface/progressbar.md)
