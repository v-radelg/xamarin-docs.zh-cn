---
title: CheckBox
ms.prod: xamarin
ms.assetid: A884AF10-D5EA-72CA-2301-B80CEC7FFBE7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: b947217706fc8ef7ce7945bf4c88349f4367ffcd
ms.sourcegitcommit: cf56d2bae34dc0f8e94c2d3d28d5f460d59807bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2019
ms.locfileid: "70985674"
---
# <a name="checkbox"></a>CheckBox

在本部分中，将创建一个用[`CheckBox`](xref:Android.Widget.CheckBox)小组件选择项的复选框。 按下该复选框时，toast 消息将指示复选框的当前状态。

打开**Resources/layout/main.axml**文件并添加[`CheckBox`](xref:Android.Widget.CheckBox) [`LinearLayout`](xref:Android.Widget.LinearLayout)元素（在中）：

```xml
<CheckBox android:id="@+id/checkbox"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="check it out" />
```

若要在状态更改时执行操作，请将以下代码添加到[`OnCreate()`](xref:Android.App.Activity.OnCreate*)方法的末尾：

```csharp
CheckBox checkbox = FindViewById<CheckBox>(Resource.Id.checkbox);

checkbox.Click += (o, e) => {
    if (checkbox.Checked)
        Toast.MakeText (this, "Selected", ToastLength.Short).Show ();
    else
        Toast.MakeText (this, "Not selected", ToastLength.Short).Show ();
};
```

这会从[`CheckBox`](xref:Android.Widget.CheckBox)布局捕获元素，然后处理单击事件，该事件定义单击复选框时要执行的操作。 单击[`Checked`](xref:Android.Widget.CompoundButton.Checked)此属性将调用以检查复选框的新状态。 如果已选中，则[`Toast`](xref:Android.Widget.Toast)显示消息 "选定"，否则显示 "未选择"。 [`CheckBox`](xref:Android.Widget.CheckBox)处理其自身状态更改，因此你只需要查询当前状态。

运行该示例。

> [!TIP]
> 如果需要自行更改状态（例如加载保存[`CheckBoxPreference`](xref:Android.Preferences.CheckBoxPreference)的时），请[`Checked`](xref:Android.Widget.CompoundButton.Checked)使用属性 setter 或[`Toggle()`](xref:Android.Widget.CompoundButton.Toggle)方法。

*此页面的某些部分是基于 Android 开源项目创建和共享的工作的修改，并根据*[*创造性 Commons 2.5 归属许可证*](http://creativecommons.org/licenses/by/2.5/)。
