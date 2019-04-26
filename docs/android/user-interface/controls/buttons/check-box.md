---
title: CheckBox
ms.prod: xamarin
ms.assetid: A884AF10-D5EA-72CA-2301-B80CEC7FFBE7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: e03595e8d88a2f12341b9e339d0581c631224848
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61268313"
---
# <a name="checkbox"></a>CheckBox

在本部分中，您将创建一个复选框，用于选择项，使用 [`CheckBox`](https://developer.xamarin.com/api/type/Android.Widget.CheckBox)
小组件。 按下复选框时的 toast 消息将指示复选框的当前状态。

打开**Resources/layout/Main.axml**文件，并添加[ `CheckBox` ](https://developer.xamarin.com/api/type/Android.Widget.CheckBox/)元素 (内[ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout)):

```xml
<CheckBox android:id="@+id/checkbox"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="check it out" />
```

若要更改状态时执行某些操作，请将以下代码添加到末尾 [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/Android.OS.PersistableBundle)
方法：

```csharp
CheckBox checkbox = FindViewById<CheckBox>(Resource.Id.checkbox);

checkbox.Click += (o, e) => {
    if (checkbox.Checked)
        Toast.MakeText (this, "Selected", ToastLength.Short).Show ();
    else
        Toast.MakeText (this, "Not selected", ToastLength.Short).Show ();
};
```

这会将捕获 [`CheckBox`](https://developer.xamarin.com/api/type/Android.Widget.CheckBox/)
元素在布局，然后处理单击事件，用于定义要单击相应的复选框时进行的操作。 单击时， [`Checked`](https://developer.xamarin.com/api/property/Android.Widget.CompoundButton.Checked/)
属性被调用以检查新状态的复选框。 如果选中，则 [`Toast`](https://developer.xamarin.com/api/type/Android.Widget.Toast/)
显示消息"已选"，否则为它将显示"未选择"。 的 [`CheckBox`](https://developer.xamarin.com/api/type/Android.Widget.CheckBox/)
处理它自己的状态更改，因此只需查询的当前状态。

运行它。

**提示：** 如果你需要自行更改的状态 (例如，当加载一个已保存[ `CheckBoxPreference` ](https://developer.xamarin.com/api/type/Android.Preferences.CheckBoxPreference)，使用 [`Checked`](https://developer.xamarin.com/api/property/Android.Widget.CompoundButton.Checked)
属性 setter 或 [`Toggle()`](https://developer.xamarin.com/api/member/Android.Widget.CompoundButton.Toggle)
方法。

*此页的部分是基于工作创建和共享通过 Android 的开放源项目和使用中所述的条款的修改*
[*Creative Commons 2.5 Attribution 许可证*](http://creativecommons.org/licenses/by/2.5/).
