---
title: RadioButton
ms.prod: xamarin
ms.assetid: 3C32EA3F-D917-C988-72C5-A17354DA791E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: ea8af3c84bb53434489d230d159a27b6b9d500d8
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67830255"
---
# <a name="radiobutton"></a>RadioButton

在本部分中，您将创建两个互斥的单选按钮 （启用一个禁用的其他），使用 [`RadioGroup`](https://developer.xamarin.com/api/type/Android.Widget.RadioGroup/)
和 [`RadioButton`](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/)
小组件。 按下任意单选按钮时，将显示的 toast 消息。


打开**Resources/layout/Main.axml**文件，并添加两个[ `RadioButton` ](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/)s，嵌套在[ `RadioGroup` ](https://developer.xamarin.com/api/type/Android.Widget.RadioGroup/) (内[ `LinearLayout`](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)):

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

非常重要的[ `RadioButton` ](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/)s 组合在一起由[ `RadioGroup` ](https://developer.xamarin.com/api/type/Android.Widget.RadioGroup/)元素，以便一次可以选择不超过一个。 由 Android 系统自动处理此逻辑。 当一个 [`RadioButton`](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/)
在选择了一个组，所有其他用户已取消自动选择。

若要执行某些操作时每个[ `RadioButton` ](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/)是选择，我们需要编写一个事件处理程序：

```csharp
private void RadioButtonClick (object sender, EventArgs e)
{
    RadioButton rb = (RadioButton)sender;
    Toast.MakeText (this, rb.Text, ToastLength.Short).Show ();
}
```

首先，传递在发件人是转换为单选按钮。
然后 [`Toast`](https://developer.xamarin.com/api/type/Android.Widget.Toast/)
选定的单选按钮的文本将显示消息。

现在，在底部 [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/Android.OS.PersistableBundle)
方法中，添加以下：

```csharp
RadioButton radio_red = FindViewById<RadioButton>(Resource.Id.radio_red);
RadioButton radio_blue = FindViewById<RadioButton>(Resource.Id.radio_blue);

radio_red.Click += RadioButtonClick;
radio_blue.Click += RadioButtonClick;
```

这会将每个捕获[ `RadioButton`](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/)布局中，并将添加每个新创建的事件 handlerto。

运行该应用程序。

> [!TIP]
> 如果你需要自行更改的状态 (例如，当加载一个已保存[ `CheckBoxPreference` ](https://developer.xamarin.com/api/type/Android.Preferences.CheckBoxPreference/))，使用 [`Checked`](https://developer.xamarin.com/api/property/Android.Widget.CompoundButton.Checked/)
> 属性 setter 或 [`Toggle()`](https://developer.xamarin.com/api/member/Android.Widget.CompoundButton.Toggle/)
> 方法。

*此页的部分是基于工作创建和共享通过 Android 的开放源项目和使用中所述的条款的修改*
[*Creative Commons 2.5 Attribution 许可证*](http://creativecommons.org/licenses/by/2.5/). 
