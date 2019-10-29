---
title: CheckBox
ms.prod: xamarin
ms.assetid: A884AF10-D5EA-72CA-2301-B80CEC7FFBE7
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: 06908ad8993eb6d47476006b23865fa1c7fe694f
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029347"
---
# <a name="checkbox"></a>CheckBox

在本部分中，你将创建一个用于选择项目的复选框，并使用[`CheckBox`](xref:Android.Widget.CheckBox)小组件。 按下该复选框时，toast 消息将指示复选框的当前状态。

打开**Resources/layout/main.axml**文件并添加[`CheckBox`](xref:Android.Widget.CheckBox)元素（在[`LinearLayout`](xref:Android.Widget.LinearLayout)中）：

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

这会从布局捕获[`CheckBox`](xref:Android.Widget.CheckBox)元素，然后处理单击事件，该事件定义单击复选框时要执行的操作。 单击时，将调用[`Checked`](xref:Android.Widget.CompoundButton.Checked)属性来检查复选框的新状态。 如果已选中该选项，则[`Toast`](xref:Android.Widget.Toast)显示消息 "selected"，否则显示 "未选择"。 [`CheckBox`](xref:Android.Widget.CheckBox)处理其自身的状态更改，因此你只需查询当前状态。

运行该示例。

> [!TIP]
> 如果需要自行更改状态（例如加载保存的[`CheckBoxPreference`](xref:Android.Preferences.CheckBoxPreference)，请使用[`Checked`](xref:Android.Widget.CompoundButton.Checked)属性 setter 或[`Toggle()`](xref:Android.Widget.CompoundButton.Toggle)方法。

*此页面的某些部分是基于 Android 开源项目创建和共享的工作的修改，并根据*[*创造性 Commons 2.5 归属许可证*](https://creativecommons.org/licenses/by/2.5/)中所述的条款使用。
