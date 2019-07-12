---
title: Xamarin.Forms ProgressBar
description: Xamarin.Forms 进度条是以可视方式表示为水平条填充的浮点型属性所基于的进度的控件。
ms.prod: xamarin
ms.assetId: C2F85FED-797C-466B-A0FD-E73CFB79B267
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/09/2019
ms.openlocfilehash: 2e2917077575ebc78dbdda8ae55aa230a39a7ba1
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67837007"
---
# <a name="xamarinforms-progressbar"></a>Xamarin.Forms ProgressBar
[![下载示例](~/media/shared/download.png) 下载示例](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ProgressBarDemos)

Xamarin.Forms [ `ProgressBar` ](xref:Xamarin.Forms.ProgressBar)是一个控件，以可视方式表示为水平条填充为表示百分比的进度`float`值。 `ProgressBar`类继承自[ `View` ](xref:Xamarin.Forms.View)。

以下屏幕截图显示`ProgressBar`iOS 和 Android 上：

![IOS 和 Android 上的屏幕截图的 ProgressBar](progressbar-images/progressbars-default.png "iOS 和 Android 上的进度栏")

`ProgressBar`控件定义了两个属性：

* [`Progress`](xref:Xamarin.Forms.ProgressBar.Progress) 是`float`值，该值表示当前进度为的值从 0 到 1。 `Progress` 小于 0 将被限制到 0，大于 1 将被限制为 1 的值的值。
* [`ProgressColor`](xref:Xamarin.Forms.ProgressBar.ProgressColor) 是`Color`影响内部颜色来表示当前进度栏。

这些属性受到[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)对象，这意味着`ProgressBar`可以设置的样式和作为目标的数据绑定。

`ProgressBar`控件还定义了`ProgressTo`之间进行动画处理到指定的值从其当前值栏的方法。 有关详细信息，请参阅[进行动画处理 ProgressBar](#animate-a-progressbar)。

> [!NOTE]
> `ProgressBar`不接受用户操作，因此它会跳过使用 Tab 键来选择控件时。

## <a name="create-a-progressbar"></a>创建进度栏

一个`ProgressBar`可以在 XAML 中实例化。 其`Progress`可以设置属性来确定内部、 彩色条的填充百分比。 如果`Progress`属性未设置，则它默认为 0。 下面的示例演示如何实例化`ProgressBar`中使用可选的 XAML`Progress`属性集：

```xaml
<ProgressBar Progress="0.5" />
```

一个`ProgressBar`还可以在代码中创建：

```csharp
ProgressBar progressBar = new ProgressBar { Progress = 0.5f };
```

> [!WARNING]
> 如不使用不受约束的水平布局选项`Center`， `Start`，或`End`与`ProgressBar`。 在 UWP，`ProgressBar`将折叠为零宽度的条。 保留默认值`HorizontalOptions`的值`Fill`且不使用的宽度`Auto`放置时`ProgressBar`中`Grid`布局。

## <a name="progressbar-appearance-properties"></a>ProgressBar 的外观属性

`ProgressColor`可以设置属性来定义内部栏颜色时`Progress`属性是大于零。 下面的示例演示如何实例化`ProgressBar`中使用的 XAML`ProgressColor`属性集：

```xaml
<ProgressBar OnColor="Orange" />
```

`ProgressColor`创建时，还可以设置属性`ProgressBar`在代码中：

```csharp
ProgressBar progressBar = new ProgressBar { ProgressColor = Color.Orange };
```

以下屏幕截图显示`ProgressBar`与`ProgressColor`属性设置为`Color.Orange`iOS 和 Android 上：

![带样式的进度栏，在 iOS 和 Android 的屏幕截图](progressbar-images/progressbars-styled.png "样式在 iOS 和 Android 上的进度栏")

## <a name="animate-a-progressbar"></a>对 ProgressBar 进行动画处理

`ProgressTo`方法进行动画处理`ProgressBar`从其当前`Progress`段时间内提供的值的值。 该方法接受`float`进度值，`uint`持续时间以毫秒为单位，`Easing`枚举值，并返回`Task<bool>`。 下面的代码演示如何进行动画处理`ProgressBar`:

```csharp
// animate to 75% progress over 500 milliseconds with linear easing
await progressBar.ProgressTo(0.75, 500, Easing.Linear);
```

有关详细信息`Easing`枚举，请参阅[在 Xamarin.Forms 中的缓动函数](~/xamarin-forms/user-interface/animation/easing.md)。

## <a name="related-links"></a>相关链接

* [ProgressBar 演示](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ProgressBarDemos)
