---
title: Xamarin. Forms ProgressBar
description: Xamarin ProgressBar 是一个控件，它以直观的方式将进度表示为基于 float 属性填充的水平栏。
ms.prod: xamarin
ms.assetId: C2F85FED-797C-466B-A0FD-E73CFB79B267
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/09/2019
ms.openlocfilehash: 40f3c9a5af29d1782249775b9f3166698eb6221a
ms.sourcegitcommit: 7acff5c5a03a0351962c05beebfc347503d83fe6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2019
ms.locfileid: "73201942"
---
# <a name="xamarinforms-progressbar"></a>Xamarin. Forms ProgressBar
[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-progressbardemos/)

Xamarin [`ProgressBar`](xref:Xamarin.Forms.ProgressBar)控件将进度直观地表示为水平条，其填充方式为 `float` 值所表示的百分比。 `ProgressBar` 类继承自[`View`](xref:Xamarin.Forms.View)。

以下屏幕截图显示了 iOS 和 Android 上的 `ProgressBar`：

![IOS 和 Android 上 ProgressBar 的屏幕截图](progressbar-images/progressbars-default.png "IOS 和 Android 上的 ProgressBar")

`ProgressBar` 控件定义了两个属性：

* [`Progress`](xref:Xamarin.Forms.ProgressBar.Progress)是一个 `float` 值，该值表示从0到1之间的当前进度。 小于 0 `Progress` 值将限制为0，大于1的值将限制为1。
* [`ProgressColor`](xref:Xamarin.Forms.ProgressBar.ProgressColor)是影响当前进度的内部条形颜色的 `Color`。

这些属性是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)对象支持的，这意味着 `ProgressBar` 可以是样式化的，并且是数据绑定的目标。

`ProgressBar` 控件还定义了一个 `ProgressTo` 方法，该方法将从其当前值到指定值的栏进行动画处理。 有关详细信息，请参阅对[ProgressBar 进行动画处理](#animate-a-progressbar)。

> [!NOTE]
> `ProgressBar` 不接受用户操作，因此当使用 Tab 键选择控件时，将跳过该操作。

## <a name="create-a-progressbar"></a>创建 ProgressBar

可以在 XAML 中实例化 `ProgressBar`。 它的 `Progress` 属性确定内部颜色栏的填充百分比。 默认 `Progress` 属性值为0。 下面的示例演示如何使用可选的 `Progress` 属性集在 XAML 中实例化 `ProgressBar`：

```xaml
<ProgressBar Progress="0.5" />
```

还可以在代码中创建 `ProgressBar`：

```csharp
ProgressBar progressBar = new ProgressBar { Progress = 0.5f };
```

> [!WARNING]
> 不要使用不受约束的水平布局选项，如 `Center`、`Start`或 `End` 与 `ProgressBar`。 在 UWP 上，`ProgressBar` 折叠到宽度为零的一条。 保持默认 `HorizontalOptions` 值为 `Fill`，在 `Grid` 布局中放置 `ProgressBar` 时不要使用 `Auto` 的宽度。

## <a name="progressbar-appearance-properties"></a>ProgressBar 外观属性

当 `Progress` 属性大于零时，`ProgressColor` 属性定义内部条形颜色。 下面的示例演示如何使用 `ProgressColor` 属性集在 XAML 中实例化 `ProgressBar`：

```xaml
<ProgressBar ProgressColor="Orange" />
```

在代码中创建 `ProgressBar` 时，还可以设置 `ProgressColor` 属性：

```csharp
ProgressBar progressBar = new ProgressBar { ProgressColor = Color.Orange };
```

以下屏幕截图显示了在 iOS 和 Android 上将 `ProgressColor` 属性设置为 "`Color.Orange`" 的 `ProgressBar`：

![IOS 和 Android 上的样式化 ProgressBar 的屏幕截图](progressbar-images/progressbars-styled.png "IOS 和 Android 上的 ProgressBar 样式")

## <a name="animate-a-progressbar"></a>对 ProgressBar 进行动画处理

`ProgressTo` 方法会在一段时间后将 `ProgressBar` 从其当前 `Progress` 值动画处理为提供的值。 方法接受 `float` 的进度值、`uint` 持续时间（以毫秒为单位）、`Easing` 枚举值并返回 `Task<bool>`。 下面的代码演示如何对 `ProgressBar`进行动画处理：

```csharp
// animate to 75% progress over 500 milliseconds with linear easing
await progressBar.ProgressTo(0.75, 500, Easing.Linear);
```

有关 `Easing` 枚举的详细信息，请参阅[Xamarin 中的缓动函数](~/xamarin-forms/user-interface/animation/easing.md)。

## <a name="related-links"></a>相关链接

* [ProgressBar 演示](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-progressbardemos/)
