---
title: ToggleButton
ms.prod: xamarin
ms.assetid: 9ADA8FCF-63ED-897A-DD56-D7D86535A92C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 56372bb643cab545529d6a4a89c804471f3344bc
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70762446"
---
# <a name="togglebutton"></a>ToggleButton

在本部分中，将创建一个按钮，用于在两个状态之间使用[`ToggleButton`](xref:Android.Widget.ToggleButton)小组件进行切换。 如果有两个互相排斥的简单状态（例如 "打开" 和 "关闭"），则此小组件是单选按钮的极佳替代项。 Android 4.0 （API 级别14）引入了一个替代切换按钮[`Switch`](xref:Android.Widget.Switch)的替代方法。

左边的一对图像为 **ToggleButton** 的示例，而右边的一对图像则展示的是 **Switch** 的示例：

![处于开启和关闭状态的开关和 ToggleButtons 的示例](toggle-button-images/togglebutton-switch.png)  

应用程序使用哪个控件是一种样式。 这两个小组件在功能上是等效的。

打开**Resources/layout/main.axml**文件并添加[`ToggleButton`](xref:Android.Widget.ToggleButton) [`LinearLayout`](xref:Android.Widget.LinearLayout)元素（在中）：

若要在状态更改时执行操作，请将以下代码添加到[`OnCreate()`](xref:Android.App.Activity.OnCreate*)
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

这会从[`ToggleButton`](xref:Android.Widget.ToggleButton)布局中捕获元素，并处理单击事件，该事件定义在单击按钮时要执行的操作。 在此示例中，方法检查按钮的新状态，然后显示一[`Toast`](xref:Android.Widget.Toast)条指示当前状态的消息。

注意， [`ToggleButton`](xref:Android.Widget.ToggleButton)在选中和未选中之间处理自己的状态更改，因此你只需要获取当前是哪一种状态。

运行该应用程序。

> [!TIP]
> 如果需要自行更改状态（例如加载保存[`CheckBoxPreference`](xref:Android.Preferences.CheckBoxPreference)的时），请使用[`Checked`](xref:Android.Widget.CompoundButton.Checked)
> 属性 setter 或[`Toggle()`](xref:Android.Widget.CompoundButton.Toggle)
> 方法。

## <a name="related-links"></a>相关链接

- [ToggleButton](https://developer.android.com/reference/android/widget/ToggleButton.html)
- [切换](https://developer.android.com/reference/android/widget/Switch.html)
