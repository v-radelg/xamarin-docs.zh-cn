---
title: ToggleButton
ms.prod: xamarin
ms.assetid: 9ADA8FCF-63ED-897A-DD56-D7D86535A92C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: fe444d255beb9c08b4b5bcf5de36a8740e503b55
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029293"
---
# <a name="togglebutton"></a>ToggleButton

在本部分中，你将创建一个按钮，用于在两个状态之间使用[`ToggleButton`](xref:Android.Widget.ToggleButton)小组件进行切换。 如果有两个互相排斥的简单状态（例如 "打开" 和 "关闭"），则此小组件是单选按钮的极佳替代项。 Android 4.0 （API 级别14）引入了切换按钮的替代方法，称为[`Switch`](xref:Android.Widget.Switch)。

可以在左侧的图像对中显示切换**按钮**的示例，而右侧图像则提供一个**开关**示例：

![处于开启和关闭状态的开关和 ToggleButtons 的示例](toggle-button-images/togglebutton-switch.png)  

应用程序使用哪个控件是一种样式。 这两个小组件在功能上是等效的。

打开**Resources/layout/main.axml**文件并添加[`ToggleButton`](xref:Android.Widget.ToggleButton)元素（在[`LinearLayout`](xref:Android.Widget.LinearLayout)中）：

若要在状态更改时执行操作，请将以下代码添加到[`OnCreate()`](xref:Android.App.Activity.OnCreate*)的末尾
付款方式

```csharp
ToggleButton togglebutton = FindViewById<ToggleButton>(Resource.Id.togglebutton);

togglebutton.Click += (o, e) => {
    // Perform action on clicks
    if (togglebutton.Checked)
        Toast.MakeText(this, "Checked", ToastLength.Short).Show ();
    else
        Toast.MakeText(this, "Not checked", ToastLength.Short).Show ();
};
```

这会从布局中捕获[`ToggleButton`](xref:Android.Widget.ToggleButton)元素，并处理单击事件，该事件定义在单击按钮时要执行的操作。 在此示例中，方法检查按钮的新状态，然后显示一条指示当前状态的[`Toast`](xref:Android.Widget.Toast)消息。

请注意， [`ToggleButton`](xref:Android.Widget.ToggleButton)在选中和未选中的情况下处理其自己的状态更改，因此您只需要询问哪个状态。

运行该应用程序。

> [!TIP]
> 如果需要自行更改状态（例如加载保存的[`CheckBoxPreference`](xref:Android.Preferences.CheckBoxPreference)时），请使用[`Checked`](xref:Android.Widget.CompoundButton.Checked)
> 属性 setter 或[`Toggle()`](xref:Android.Widget.CompoundButton.Toggle)
> 方法。

## <a name="related-links"></a>相关链接

- [按钮](https://developer.android.com/reference/android/widget/ToggleButton.html)
- [切换](https://developer.android.com/reference/android/widget/Switch.html)
