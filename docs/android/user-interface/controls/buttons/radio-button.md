---
title: RadioButton
ms.prod: xamarin
ms.assetid: 3C32EA3F-D917-C988-72C5-A17354DA791E
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: 9f51adcbd1accb4f780318cc0853e612ed8e5bed
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029305"
---
# <a name="radiobutton"></a>RadioButton

在本部分中，你将创建两个互相排斥的单选按钮（启用一个禁用另一个），并使用[`RadioGroup`](xref:Android.Widget.RadioGroup)
和[`RadioButton`](xref:Android.Widget.RadioButton)
小组件. 按任意单选按钮时，将显示 toast 消息。

打开**Resources/layout/main.axml**文件并添加两个[`RadioButton`](xref:Android.Widget.RadioButton)，嵌套在[`RadioGroup`](xref:Android.Widget.RadioGroup)中（在[`LinearLayout`](xref:Android.Widget.LinearLayout)内）：

```xml
<RadioGroup
  android:layout_width="fill_parent"
  android:layout_height="wrap_content"
  android:orientation="vertical">
  <RadioButton android:id="@+id/radio_red"
      android:layout_width="wrap_content"
      android:layout_height="wrap_content"
      android:text="Red" />
  <RadioButton android:id="@+id/radio_blue"
      android:layout_width="wrap_content"
      android:layout_height="wrap_content"
      android:text="Blue" />
</RadioGroup>
```

[`RadioButton`](xref:Android.Widget.RadioButton)按[`RadioGroup`](xref:Android.Widget.RadioGroup)元素组合在一起，以便一次只能选择一个，这一点很重要。 此逻辑由 Android 系统自动处理。 一[`RadioButton`](xref:Android.Widget.RadioButton)
选定组后，将自动取消选择所有其他组。

若要在选择每个[`RadioButton`](xref:Android.Widget.RadioButton)时执行操作，需要编写一个事件处理程序：

```csharp
private void RadioButtonClick (object sender, EventArgs e)
{
    RadioButton rb = (RadioButton)sender;
    Toast.MakeText (this, rb.Text, ToastLength.Short).Show ();
}
```

首先，将传入的发送方转换为单选按钮。
Then [`Toast`](xref:Android.Widget.Toast)
"消息" 显示选定的单选按钮的文本。

现在，在[`OnCreate()`](xref:Android.App.Activity.OnCreate*)的底部
方法，添加以下内容：

```csharp
RadioButton radio_red = FindViewById<RadioButton>(Resource.Id.radio_red);
RadioButton radio_blue = FindViewById<RadioButton>(Resource.Id.radio_blue);

radio_red.Click += RadioButtonClick;
radio_blue.Click += RadioButtonClick;
```

这会从布局捕获每个[`RadioButton`](xref:Android.Widget.RadioButton)，并将新创建的事件 handlerto。

运行该应用程序。

> [!TIP]
> 如果需要自行更改状态（例如加载保存的[`CheckBoxPreference`](xref:Android.Preferences.CheckBoxPreference)时），请使用[`Checked`](xref:Android.Widget.CompoundButton.Checked)
> 属性 setter 或[`Toggle()`](xref:Android.Widget.CompoundButton.Toggle)
> 方法。

*此页面的某些部分是基于 Android 开源项目创建和共享的工作的修改，并根据*
[*创造性 Commons 2.5 归属许可证*](https://creativecommons.org/licenses/by/2.5/)中所述的术语使用。 
