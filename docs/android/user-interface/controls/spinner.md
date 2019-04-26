---
title: Spinner
ms.prod: xamarin
ms.assetid: 004089E9-7C1D-2285-765A-B69143091F2A
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 90b4755cdb4b8248c2b731d070d720076d4dda40
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60946005"
---
# <a name="spinner"></a>Spinner

[`Spinner`](https://developer.xamarin.com/api/type/Android.Widget.Spinner/) 是一个小组件，提供了用于选择项的下拉列表。 本指南介绍如何创建简单的应用程序中微调控件，显示与所选的选择相关联的其他值的修改后跟显示选项列表。

## <a name="basic-spinner"></a>基本的微调框

在本教程的第一部分，你将创建一个简单的微调控件小组件，显示行星的列表。 选中一颗后的 toast 消息显示所选的项：

[![HelloSpinner 应用的示例屏幕快照](spinner-images/01-example-screenshots-sml.png)](spinner-images/01-example-screenshots.png#lightbox)

启动一个名为的新项目**HelloSpinner**。

打开**Resources/Layout/Main.axml**并插入以下 XML:

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

请注意， [ `TextView`](https://developer.xamarin.com/api/type/Android.Widget.TextView/)的`android:text`属性并[ `Spinner`](https://developer.xamarin.com/api/type/Android.Widget.Spinner/)的`android:prompt`属性这两个引用相同的字符串资源。 此文本表现为小组件的标题。 当应用于[ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/)，会在选择小组件后，会显示的选择对话框中显示的标题文本。

编辑**Resources/Values/Strings.xml**和修改该文件应如下所示：

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

第二个`<string>`元素定义引用的标题字符串[ `TextView` ](https://developer.xamarin.com/api/type/Android.Widget.TextView/)并[ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/)上述布局中。
`<string-array>`元素定义将显示为列表中的字符串的列表[ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/)小组件。

现在，打开**MainActivity.cs**并添加以下`using`语句：

```csharp
using System;
```

接下来，插入以下代码 [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/(Android.OS.Bundle))
方法：

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

之后`Main.axml`布局设置为内容视图中， [ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/)小组件捕获从与布局[ `FindViewById<>(int)` ](https://developer.xamarin.com/api/member/Android.App.Activity.FindViewById/p/System.Int32/)。
的 [`CreateFromResource()`](https://developer.xamarin.com/api/member/Android.Widget.ArrayAdapter.CreateFromResource/p/Android.Content.Context/System.Int32/System.Int32/)
方法然后创建一个新[ `ArrayAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter/)，其中将每个项的字符串数组中绑定到的初始外观[ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/) （这是每个项中选择时旋转图标的显示方式）. `Resource.Array.planets_array` ID 引用`string-array`上面定义和`Android.Resource.Layout.SimpleSpinnerItem`ID 引用由平台定义的标准的微调控件外观的布局。
[`SetDropDownViewResource`](https://developer.xamarin.com/api/member/Android.Widget.ArrayAdapter.SetDropDownViewResource/p/System.Int32/)
调用以打开小组件时定义的每个项的外观。 最后， [ `ArrayAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter/)设置，使关联的所有与项[ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/)通过设置[ `Adapter` ](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter)属性。

现在提供 notifys 应用程序中选择了某个项时的回调方法[ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/)。 下面是此方法应如下所示：

```csharp
private void spinner_ItemSelected (object sender, AdapterView.ItemSelectedEventArgs e)
{
    Spinner spinner = (Spinner)sender;
    string toast = string.Format ("The planet is {0}", spinner.GetItemAtPosition (e.Position));
    Toast.MakeText (this, toast, ToastLength.Long).Show ();
}
```

当选中某个项时，发件人被强制转换为[ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/) ，以便可以访问项。 使用`Position`上的属性`ItemEventArgs`，可以找出所选对象的文本并使用它来显示[ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/)。

运行该应用程序;它应如下所示：

[![使用 Mars 选定为地球旋转图标的屏幕截图示例](spinner-images/02-basic-example-sml.png)](spinner-images/02-basic-example.png#lightbox)

## <a name="spinner-using-keyvalue-pairs"></a>使用键/值对的微调框

通常很有必要将使用`Spinner`以显示与某种类型的应用使用的数据相关联的密钥值。 因为`Spinner`不起作用，你必须直接与键/值对单独存储键/值对，填充`Spinner`与密钥值，然后使用在旋转图标中的所选密钥位置中查找关联的数据值。 

在以下步骤中， **HelloSpinner**应用修改为显示所选的地球平均温度：

添加以下`using`语句**MainActivity.cs**:

```csharp
using System.Collections.Generic;
```

添加以下实例变量`MainActivity`类。
此列表将包含键/值对的行星和其平均温度：

```csharp
private List<KeyValuePair<string, string>> planets;
```

在中`OnCreate`方法中，添加以下代码之前`adapter`声明：

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

此代码创建简单的存储的行星和其关联的平均温度。 （在实际应用中，数据库通常用于存储密钥和其关联的数据。）

上述代码中后立即, 添加以下行，以提取密钥，并将它们放入列表 （按顺序）：

```csharp
List<string> planetNames = new List<string>();
foreach (var item in planets)
    planetNames.Add (item.Key);
```

传递到此列表`ArrayAdapter`构造函数 (而不是`planets_array`资源):

```csharp
var adapter = new ArrayAdapter<string>(this,
    Android.Resource.Layout.SimpleSpinnerItem, planetNames);
```

修改`spinner_ItemSelected`，以便使用所选的位置以查找与所选的全球关联的值 （温度）：

```csharp
private void spinner_ItemSelected(object sender, AdapterView.ItemSelectedEventArgs e)
{
    Spinner spinner = (Spinner)sender;
    string toast = string.Format("The mean temperature for planet {0} is {1}",
        spinner.GetItemAtPosition(e.Position), planets[e.Position].Value);
    Toast.MakeText(this, toast, ToastLength.Long).Show();
}
```

运行该应用程序;toast 应如下所示：

[![全球选择显示温度的示例](spinner-images/03-keyvalue-example-sml.png)](spinner-images/03-keyvalue-example.png#lightbox)
   
  

## <a name="resources"></a>资源

-   [`Resource.Layout`](https://developer.xamarin.com/api/type/Android.Resource+Layout/) 
-   [`ArrayAdapter`](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter/) 
-   [`Spinner`](https://developer.xamarin.com/api/type/Android.Widget.Spinner/) 

*此页的部分是基于工作创建和共享通过 Android 的开放源项目和使用中所述的条款的修改*
[*Creative Commons 2.5 Attribution 许可证*](http://creativecommons.org/licenses/by/2.5/).
