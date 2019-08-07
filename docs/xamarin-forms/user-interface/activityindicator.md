---
title: Xamarin 中的活动指示器
description: ActivityIndicator 控件向用户指出, 应用程序参与了长时间的活动, 而不提供任何进度指示。 本文介绍如何使用 XAML 和代码中的 ActivityIndicator。
ms.prod: xamarin
ms.assetid: 4CEED02D-5CA3-4C3A-B7ED-3193FC272261
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/10/2019
ms.openlocfilehash: e13a46e1022f4e33ace6f9f19bb5cea5d1ac784b
ms.sourcegitcommit: c6e56545eafd8ff9e540d56aba32aa6232c5315f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2019
ms.locfileid: "68739169"
---
# <a name="xamarinforms-activityindicator"></a>Xamarin. Forms ActivityIndicator
[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-activityindicatordemos/)

Xamarin[`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator)是一个控件, 它显示一个动画来显示应用程序正在忙于一个冗长的活动。 与不同[`ProgressBar`](xref:Xamarin.Forms.ProgressBar)的是`ActivityIndicator` , 不提供进度指示。 `ActivityIndicator`继承[自`View`](xref:Xamarin.Forms.View)。

以下屏幕截图显示了`ActivityIndicator` iOS 和 Android 上的控件:

![IOS 和 Android 上 ActivityIndicator 的屏幕截图](activityindicator-images/activityindicators-default.png "IOS 和 Android 上 ActivityIndicator 的屏幕截图")

`ActivityIndicator`控件定义以下属性:

* [`IsRunning`](xref:Xamarin.Forms.ActivityIndicator.IsRunning)一个`bool`值, 该值指示`ActivityIndicator`是否应显示、动画显示或隐藏。 如果值为`false` , 则`ActivityIndicator`将不可见。
* [`Color`](xref:Xamarin.Forms.ActivityIndicator.Color)定义的显示颜色`ActivityIndicator`的值。`Color`

这些属性是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)对象支持的, 这意味着`ActivityIndicator`可以对进行样式化, 并使其成为数据绑定的目标。

## <a name="create-an-activityindicator"></a>创建 ActivityIndicator

`ActivityIndicator`可以在 XAML 中实例化。 可以`IsRunning`设置其属性以确定控件是否可见并进行动画处理。 如果未`IsRunning`设置该属性, 则默认为`false`并且`ActivityIndicator`不可见。 下面的示例演示如何使用可选`ActivityIndicator` `IsRunning`的属性集在 XAML 中实例化:

```xaml
<ActivityIndicator IsRunning="true" />
```

也`ActivityIndicator`可以在代码中创建:

```csharp
ActivityIndicator activityIndicator = new ActivityIndicator { IsRunning = true };
```

## <a name="activityindicator-appearance-properties"></a>ActivityIndicator 外观属性

属性可以设置为`ActivityIndicator`定义颜色。 `Color` 下面的示例演示如何`ActivityIndicator` `Color`使用属性集在 XAML 中实例化:

```xaml
<ActivityIndicator Color="Orange" />
```

`ActivityIndicator`在`Color`代码中创建时, 还可以设置属性:

```csharp
ActivityIndicator activityIndicator = new ActivityIndicator { Color = Color.Orange };
```

以下屏幕截图显示了`ActivityIndicator`在 iOS `Color`和 Android 上`Color.Orange`的属性设置为的:

![IOS 和 Android 上的样式化 ActivityIndicator 的屏幕截图](activityindicator-images/activityindicators-styled.png "IOS 和 Android 上的样式化 ActivityIndicator 的屏幕截图")

## <a name="related-links"></a>相关链接

* [ActivityIndicator 演示](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-activityindicatordemos/)
* [进度栏](~/xamarin-forms/user-interface/progressbar.md)
