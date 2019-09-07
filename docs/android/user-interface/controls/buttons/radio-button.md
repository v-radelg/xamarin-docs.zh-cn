---
title: RadioButton
ms.prod: xamarin
ms.assetid: 3C32EA3F-D917-C988-72C5-A17354DA791E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: c1dabfcd481dccf50075c02c54019ee27499769f
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70758822"
---
# <a name="radiobutton"></a>RadioButton

在本部分中，你将创建两个互相排斥的单选按钮（启用一个禁用另一个），并使用[`RadioGroup`](xref:Android.Widget.RadioGroup)
与[`RadioButton`](xref:Android.Widget.RadioButton)
小组件. 按任意单选按钮时，将显示 toast 消息。

打开**Resources/layout/main.axml**文件并添加两个[`RadioButton`](xref:Android.Widget.RadioButton) [`RadioGroup`](xref:Android.Widget.RadioGroup) ，其中嵌套在内（在中[`LinearLayout`](xref:Android.Widget.LinearLayout)）：

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

重要的是， [`RadioButton`](xref:Android.Widget.RadioButton)将[`RadioGroup`](xref:Android.Widget.RadioGroup)由元素组合在一起，以便一次只能选择一个。 此逻辑由 Android 系统自动处理。 如果一个[`RadioButton`](xref:Android.Widget.RadioButton)
选定组后，将自动取消选择所有其他组。

若要在每个[`RadioButton`](xref:Android.Widget.RadioButton)选定时执行操作，需要编写一个事件处理程序：

```csharp
private void RadioButtonClick (object sender, EventArgs e)
{
    RadioButton rb = (RadioButton)sender;
    Toast.MakeText (this, rb.Text, ToastLength.Short).Show ();
}
```

首先，将传入的发送方转换为单选按钮。
然后是[`Toast`](xref:Android.Widget.Toast)
"消息" 显示选定的单选按钮的文本。

现在，在[`OnCreate()`](xref:Android.App.Activity.OnCreate*)
方法，添加以下内容：

```csharp
RadioButton radio_red = FindViewById<RadioButton>(Resource.Id.radio_red);
RadioButton radio_blue = FindViewById<RadioButton>(Resource.Id.radio_blue);

radio_red.Click += RadioButtonClick;
radio_blue.Click += RadioButtonClick;
```

这会从布局捕获[`RadioButton`](xref:Android.Widget.RadioButton)每个，并添加新创建的事件 handlerto。

运行该应用程序。

> [!TIP]
> 如果需要自行更改状态（例如加载保存[`CheckBoxPreference`](xref:Android.Preferences.CheckBoxPreference)的时），请使用[`Checked`](xref:Android.Widget.CompoundButton.Checked)
> 属性 setter 或[`Toggle()`](xref:Android.Widget.CompoundButton.Toggle)
> 方法。

*此页面的某些部分是基于 Android 开源项目创建和共享的工作的修改，并根据*
[*创造性 Commons 2.5 归属许可证*](http://creativecommons.org/licenses/by/2.5/)中所述的条款使用。 
