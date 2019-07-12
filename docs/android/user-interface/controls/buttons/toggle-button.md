---
title: ToggleButton
ms.prod: xamarin
ms.assetid: 9ADA8FCF-63ED-897A-DD56-D7D86535A92C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: a22d274feb5539164663ac0c48e5a84bdf5d2c66
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67830231"
---
# <a name="togglebutton"></a>ToggleButton

在本部分中，您将创建专用于使用的两个状态之间切换按钮[ `ToggleButton` ](https://developer.xamarin.com/api/type/Android.Widget.ToggleButton/)小组件。 此小组件是单选按钮的绝佳替代方法，如果您有两个互相排斥的简单状态 ("on"和"关闭"，例如)。 Android 4.0 （API 级别 14） 中引入了名为的切换按钮的替代方法[ `Switch` ](https://developer.xamarin.com/api/type/Android.Widget.Switch/)。

左边的一对图像为 **ToggleButton** 的示例，而右边的一对图像则展示的是 **Switch** 的示例：

![在这种打开和关闭状态的开关和 ToggleButtons 示例](toggle-button-images/togglebutton-switch.png)  

应用程序使用哪个控件是个风格问题。 这两个小组件在功能上等效。

打开**Resources/layout/Main.axml**文件，并添加[ `ToggleButton` ](https://developer.xamarin.com/api/type/Android.Widget.ToggleButton/)元素 (内[ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)):

若要更改状态时执行某些操作，请将以下代码添加到末尾 [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/Android.OS.PersistableBundle)
方法：

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

这会将捕获[ `ToggleButton` ](https://developer.xamarin.com/api/type/Android.Widget.ToggleButton/)元素在布局，然后处理单击事件，用于定义要在单击按钮时执行的操作。 在此示例中，该方法将检查新状态的按钮，然后显示[ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/)条消息，指示当前状态。

注意， [`ToggleButton`](https://developer.xamarin.com/api/type/Android.Widget.ToggleButton/)在选中和未选中之间处理自己的状态更改，因此你只需要获取当前是哪一种状态。

运行该应用程序。


> [!TIP]
> 如果你需要自行更改的状态 (例如，当加载一个已保存[ `CheckBoxPreference` ](https://developer.xamarin.com/api/type/Android.Preferences.CheckBoxPreference/))，使用 [`Checked`](https://developer.xamarin.com/api/property/Android.Widget.CompoundButton.Checked/)
> 属性 setter 或 [`Toggle()`](https://developer.xamarin.com/api/member/Android.Widget.CompoundButton.Toggle/)
> 方法。


## <a name="related-links"></a>相关链接

- [ToggleButton](https://developer.android.com/reference/android/widget/ToggleButton.html)
- [切换](https://developer.android.com/reference/android/widget/Switch.html)
