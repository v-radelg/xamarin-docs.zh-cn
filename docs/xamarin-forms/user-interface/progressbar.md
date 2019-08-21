---
title: Xamarin. Forms ProgressBar
description: Xamarin ProgressBar 是一个控件, 它以直观的方式将进度表示为基于 float 属性填充的水平栏。
ms.prod: xamarin
ms.assetId: C2F85FED-797C-466B-A0FD-E73CFB79B267
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/09/2019
ms.openlocfilehash: a4cfc6c54eb2864707f328106761af029e0734cf
ms.sourcegitcommit: 9178e2e689f027212ea3e623b556b312985d79fe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2019
ms.locfileid: "69658084"
---
# <a name="xamarinforms-progressbar"></a>Xamarin. Forms ProgressBar
[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-progressbardemos/)

Xamarin [`ProgressBar`](xref:Xamarin.Forms.ProgressBar)控件以直观的方式将进度表示为由`float`值表示的百分比填充的水平栏。 类继承自[`View`。](xref:Xamarin.Forms.View) `ProgressBar`

以下屏幕截图显示了`ProgressBar` iOS 和 Android 上的:

![IOS 和 Android 上 ProgressBar 的屏幕截图](progressbar-images/progressbars-default.png "IOS 和 Android 上的 ProgressBar")

`ProgressBar`控件定义了两个属性:

* [`Progress`](xref:Xamarin.Forms.ProgressBar.Progress)表示当前进度的值,该值为0到1之间的值。`float` `Progress`小于0的值将被限制为 0, 大于1的值将限制为1。
* [`ProgressColor`](xref:Xamarin.Forms.ProgressBar.ProgressColor)`Color`影响表示当前进度的内部条形颜色的。

这些属性是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)对象支持的, 这意味着`ProgressBar`可以对进行样式化, 并使其成为数据绑定的目标。

该`ProgressBar`控件还定义了`ProgressTo`一个方法, 该方法将从其当前值到指定值的一种动画效果。 有关详细信息, 请参阅对[ProgressBar 进行动画处理](#animate-a-progressbar)。

> [!NOTE]
> `ProgressBar`不接受用户操作, 因此当使用 Tab 键选择控件时, 将跳过该操作。

## <a name="create-a-progressbar"></a>创建 ProgressBar

`ProgressBar`可以在 XAML 中实例化。 它`Progress`的属性确定内部颜色栏的填充百分比。 默认`Progress`属性值为0。 下面的示例演示如何使用可选`ProgressBar` `Progress`的属性集在 XAML 中实例化:

```xaml
<ProgressBar Progress="0.5" />
```

也`ProgressBar`可以在代码中创建:

```csharp
ProgressBar progressBar = new ProgressBar { Progress = 0.5f };
```

> [!WARNING]
> 不要将不受约束的水平布局选项`Center`( `Start`如、 `End`或`ProgressBar`) 用于。 在 UWP 上, `ProgressBar`折叠到宽度为零的一条。 `ProgressBar`在`HorizontalOptions` 布局中`Grid`放置`Auto`时`Fill` , 请保留的默认值, 不要使用的宽度。

## <a name="progressbar-appearance-properties"></a>ProgressBar 外观属性

属性在`Progress`属性大于零时定义内部条形颜色。 `ProgressColor` 下面的示例演示如何`ProgressBar` `ProgressColor`使用属性集在 XAML 中实例化:

```xaml
<ProgressBar OnColor="Orange" />
```

`ProgressBar`在`ProgressColor`代码中创建时, 还可以设置属性:

```csharp
ProgressBar progressBar = new ProgressBar { ProgressColor = Color.Orange };
```

以下屏幕截图显示`ProgressBar` `ProgressColor`了在 iOS 和 Android 上`Color.Orange`的属性设置为的:

![IOS 和 Android 上的样式化 ProgressBar 的屏幕截图](progressbar-images/progressbars-styled.png "IOS 和 Android 上的 ProgressBar 样式")

## <a name="animate-a-progressbar"></a>对 ProgressBar 进行动画处理

方法将`ProgressBar`从其当前`Progress`值到提供的值在一段时间内进行动画处理。 `ProgressTo` 方法接受一个`float`进度值`uint` (以毫秒为单位), `Easing`并返回一个`Task<bool>`。 下面的代码演示如何对`ProgressBar`执行动画处理:

```csharp
// animate to 75% progress over 500 milliseconds with linear easing
await progressBar.ProgressTo(0.75, 500, Easing.Linear);
```

有关`Easing`枚举的详细信息, 请参阅[Xamarin 中的缓动函数](~/xamarin-forms/user-interface/animation/easing.md)。

## <a name="related-links"></a>相关链接

* [ProgressBar 演示](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-progressbardemos/)
