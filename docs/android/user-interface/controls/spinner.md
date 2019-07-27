---
title: Xamarin Android 微调框
ms.prod: xamarin
ms.assetid: 004089E9-7C1D-2285-765A-B69143091F2A
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 2c7f0de2347e614b8c24de32bf3f88362a212a94
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68510411"
---
# <a name="xamarinandroid-spinner"></a>Xamarin Android 微调框

[`Spinner`](xref:Android.Widget.Spinner)是一个小组件, 用于显示用于选择项目的下拉列表。 本指南说明如何创建一个简单的应用程序, 该应用程序在微调框中显示选项列表, 然后是用于显示与所选选择关联的其他值的修改。

## <a name="basic-spinner"></a>基本微调框

在本教程的第一部分中, 你将创建一个简单的微调小组件, 用于显示行星列表。 选择了地球后, toast 消息会显示所选项目:

[![HelloSpinner 应用的示例屏幕截图](spinner-images/01-example-screenshots-sml.png)](spinner-images/01-example-screenshots.png#lightbox)

启动名为**HelloSpinner**的新项目。

打开**Resources/Layout/main.axml**并插入以下 XML:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:padding="10dip"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content">
    <TextView
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="10dip"
        android:text="@string/planet_prompt"
    />
    <Spinner
        android:id="@+id/spinner"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:prompt="@string/planet_prompt"
    />
</LinearLayout>
```

请注意, [`TextView`](xref:Android.Widget.TextView) `android:text`的属性[`Spinner`](xref:Android.Widget.Spinner)和`android:prompt`的属性都引用相同的字符串资源。 此文本将表现为小组件的标题。 当应用于[`Spinner`](xref:Android.Widget.Spinner)时, 标题文本将显示在选择小组件时显示的选择对话框中。

编辑**资源/值/字符串 .xml** , 并修改该文件, 如下所示:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
  <string name="app_name">HelloSpinner</string>
  <string name="planet_prompt">Choose a planet</string>
  <string-array name="planets_array">
    <item>Mercury</item>
    <item>Venus</item>
    <item>Earth</item>
    <item>Mars</item>
    <item>Jupiter</item>
    <item>Saturn</item>
    <item>Uranus</item>
    <item>Neptune</item>
  </string-array>
</resources>
```

第二`<string>`个元素定义[`TextView`](xref:Android.Widget.TextView)和[`Spinner`](xref:Android.Widget.Spinner)在上面的布局中引用的标题字符串。
元素定义将显示为[`Spinner`](xref:Android.Widget.Spinner)小组件中的列表的字符串列表。 `<string-array>`

现在, 打开**MainActivity.cs**并添加以下`using`语句:

```csharp
using System;
```

接下来, 为的[`OnCreate()`](xref:Android.App.Activity.OnCreate*)方法插入以下代码:

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    // Set our view from the "Main" layout resource
    SetContentView (Resource.Layout.Main);

    Spinner spinner = FindViewById<Spinner> (Resource.Id.spinner);

    spinner.ItemSelected += new EventHandler<AdapterView.ItemSelectedEventArgs> (spinner_ItemSelected);
    var adapter = ArrayAdapter.CreateFromResource (
            this, Resource.Array.planets_array, Android.Resource.Layout.SimpleSpinnerItem);

    adapter.SetDropDownViewResource (Android.Resource.Layout.SimpleSpinnerDropDownItem);
    spinner.Adapter = adapter;
}
```

布局设置为内容视图后[`Spinner`](xref:Android.Widget.Spinner) , 将从布局中[`FindViewById<>(int)`](xref:Android.App.Activity.FindViewById*)捕获小组件。 `Main.axml`
此[`CreateFromResource()`](xref:Android.Widget.ArrayAdapter.CreateFromResource*)
然后, 方法会创建[`ArrayAdapter`](xref:Android.Widget.ArrayAdapter)一个新的, 它将字符串数组中的每一项绑定到[`Spinner`](xref:Android.Widget.Spinner)的初始外观 (这是每个项在选择时在微调框中的显示方式)。 Id 引用上面定义`string-array`的, 并且`Android.Resource.Layout.SimpleSpinnerItem` id 引用了平台定义的标准微调框的布局。 `Resource.Array.planets_array`
[`SetDropDownViewResource`](xref:Android.Widget.ArrayAdapter.SetDropDownViewResource*)
调用以定义打开小组件时每个项的外观。 最后, [`ArrayAdapter`](xref:Android.Widget.ArrayAdapter) [`Spinner`](xref:Android.Widget.Spinner)通过设置[`Adapter`](xref:Android.Widget.ArrayAdapter)属性, 将设置为将其所有项与相关联。

现在提供一个回调方法, 该方法在从中[`Spinner`](xref:Android.Widget.Spinner)选择某项时 notifys 该应用程序。 此方法应如下所示:

```csharp
private void spinner_ItemSelected (object sender, AdapterView.ItemSelectedEventArgs e)
{
    Spinner spinner = (Spinner)sender;
    string toast = string.Format ("The planet is {0}", spinner.GetItemAtPosition (e.Position));
    Toast.MakeText (this, toast, ToastLength.Long).Show ();
}
```

选择项后, 会将发送方强制转换为[`Spinner`](xref:Android.Widget.Spinner) , 以便可以访问项。 使用上的`Position`属性[`Toast`](xref:Android.Widget.Toast), 可以找出所选对象的文本, 并使用它来显示。 `ItemEventArgs`

运行应用程序;其外观应如下所示:

[![将 Mars 选为地球的微调框示例](spinner-images/02-basic-example-sml.png)](spinner-images/02-basic-example.png#lightbox)

## <a name="spinner-using-keyvalue-pairs"></a>使用键/值对进行微调

通常, 必须使用`Spinner`来显示与应用使用的某种数据相关联的键值。 由于`Spinner`不能直接使用键/值对, 因此必须单独存储键/值对, `Spinner`用键值填充, 然后使用微调框中所选键的位置查找关联的数据值。 

在以下步骤中, 将修改**HelloSpinner**应用以显示所选行星的平均温度:

将以下`using`语句添加到**MainActivity.cs**:

```csharp
using System.Collections.Generic;
```

将以下实例变量添加到`MainActivity`类。
此列表将保存行星的键/值对及其平均温度:

```csharp
private List<KeyValuePair<string, string>> planets;
```

在方法中, 在声明之前`adapter`添加以下代码: `OnCreate`

```csharp
planets = new List<KeyValuePair<string, string>>
{
    new KeyValuePair<string, string>("Mercury", "167 degrees C"),
    new KeyValuePair<string, string>("Venus", "464 degrees C"),
    new KeyValuePair<string, string>("Earth", "15 degrees C"),
    new KeyValuePair<string, string>("Mars", "-65 degrees C"),
    new KeyValuePair<string, string>("Jupiter" , "-110 degrees C"),
    new KeyValuePair<string, string>("Saturn", "-140 degrees C"),
    new KeyValuePair<string, string>("Uranus", "-195 degrees C"),
    new KeyValuePair<string, string>("Neptune", "-200 degrees C")
};
```

此代码会创建一个简单的行星商店, 并为其关联的平均温度。 (在实际应用中, 数据库通常用于存储密钥及其关联的数据。)

紧跟在上述代码后面, 添加以下行以提取关键字并将其放入列表中 (按顺序):

```csharp
List<string> planetNames = new List<string>();
foreach (var item in planets)
    planetNames.Add (item.Key);
```

将此列表传递到`ArrayAdapter`构造函数 (而不`planets_array`是资源):

```csharp
var adapter = new ArrayAdapter<string>(this,
    Android.Resource.Layout.SimpleSpinnerItem, planetNames);
```

修改`spinner_ItemSelected` , 以便使用所选位置查找与所选行星关联的值 (温度):

```csharp
private void spinner_ItemSelected(object sender, AdapterView.ItemSelectedEventArgs e)
{
    Spinner spinner = (Spinner)sender;
    string toast = string.Format("The mean temperature for planet {0} is {1}",
        spinner.GetItemAtPosition(e.Position), planets[e.Position].Value);
    Toast.MakeText(this, toast, ToastLength.Long).Show();
}
```

运行应用程序;toast 应如下所示:

[![显示温度的行星选择的示例](spinner-images/03-keyvalue-example-sml.png)](spinner-images/03-keyvalue-example.png#lightbox)

## <a name="resources"></a>资源

- [`Resource.Layout`](xref:Android.Resource.Layout)
- [`ArrayAdapter`](xref:Android.Widget.ArrayAdapter)
- [`Spinner`](xref:Android.Widget.Spinner)

*此页面的某些部分是基于 Android 开源项目创建和共享的工作的修改, 并根据*
[*创造性 Commons 2.5 归属许可证*](http://creativecommons.org/licenses/by/2.5/)中所述的条款使用。
