---
title: 在 Xamarin.Android 中使用 ListView
description: ListView 是 Android 应用程序; 的一个重要的 UI 组件它用于无处不在从菜单选项的短列表到联系人或 internet 收藏夹的长列表。 它提供了显示的行，可以使用内置样式格式化，也可以广泛地自定义滚动列表的简单方法。
ms.prod: xamarin
ms.assetid: C2BA2705-9B20-01C2-468D-860BDFEDC157
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/25/2018
ms.openlocfilehash: a30256722647bbea482970d0c4a751954810d99e
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61170779"
---
# <a name="listview"></a>ListView

_ListView 是 Android 应用程序; 的一个重要的 UI 组件它用于无处不在从菜单选项的短列表到联系人或 internet 收藏夹的长列表。它提供了显示的行，可以使用内置样式格式化，也可以广泛地自定义滚动列表的简单方法。_


## <a name="overview"></a>概述

列表视图和适配器包含在 Android 应用程序的最基本的构建基块。 `ListView`类提供了灵活的方式来呈现数据是否短菜单或长的滚动列表。 它提供可用性功能，如快速滚动，索引和单个或多个选择，以帮助您构建您的应用程序便于移动访问的用户界面。 `ListView` 实例需要*适配器*，以向它馈送行视图中包含的数据。

本指南介绍如何实现`ListView`和各种`Adapter`在 Xamarin.Android 中的类。 它还演示了如何自定义的外观`ListView`，并讨论了行重复使用以减少内存消耗的重要性。 此外，还有一些活动生命周期的影响方式的讨论`ListView`和`Adapter`使用。 如果您正在从事跨平台应用程序通过 Xamarin.iOS`ListView`控件是结构上类似于 iOS `UITableView` (和 Android`Adapter`类似于`UITableViewSource`)。

首先，简短的教程介绍了`ListView`一个基本代码的示例。 接下来，提供更高级的主题的链接可帮助你使用`ListView`中实际的应用程序。


> [!NOTE]
> `RecyclerView`小组件是更高级、 更灵活的版本`ListView`。 因为`RecyclerView`设计为的后继`ListView`(和`GridView`)，我们建议你使用`RecyclerView`而非`ListView`新的应用程序开发。 有关详细信息，请参阅[RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)。



## <a name="listview-tutorial"></a>ListView 教程

[`ListView`](https://developer.xamarin.com/api/type/Android.Widget.ListView/) 是 [`ViewGroup`](https://developer.xamarin.com/api/type/Android.Views.ViewGroup/)
创建可滚动项的列表。 列表项会自动插入到列表使用[ `IListAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.IListAdapter/)。

在本教程中，将创建从一个字符串数组，读取的国家/地区名称的可滚动列表。 选择列表项后的 toast 消息将显示在列表中项的位置。


启动一个名为的新项目**HelloListView**。

创建一个名为 XML 文件**list_item.xml**并将其内部保存**资源/布局/** 文件夹。 插入以下：

```xml
<?xml version="1.0" encoding="utf-8"?>
<TextView xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:padding="10dp"
    android:textSize="16sp">
</TextView>
```

此文件定义将被放入每个项的布局[ `ListView` ](https://developer.xamarin.com/api/type/Android.Widget.ListView/)。

打开`MainActivity.cs`并修改类来扩展[ `ListActivity` ](https://developer.xamarin.com/api/type/Android.App.ListActivity/) (而不是[ `Activity` ](https://developer.xamarin.com/api/type/Android.App.Activity/)):

```csharp
public class MainActivity : ListActivity
{
```

插入下面的代码 [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/(Android.OS.Bundle))
方法：

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    ListAdapter = new ArrayAdapter<string> (this, Resource.Layout.list_item, countries);

    ListView.TextFilterEnabled = true;

    ListView.ItemClick += delegate (object sender, AdapterView.ItemClickEventArgs args)
    {
        Toast.MakeText(Application, ((TextView)args.View).Text, ToastLength.Short).Show();
    };
}
```

请注意，这不会为该活动加载布局文件 (通常执行此操作与[ `SetContentView(int)` ](https://developer.xamarin.com/api/member/Android.App.Activity.SetContentView/(System.Int32)))。
相反，设置 [`ListAdapter`](https://developer.xamarin.com/api/property/Android.App.ListActivity.ListAdapter/)
将自动添加属性 [`ListView`](https://developer.xamarin.com/api/type/Android.Widget.ListView/)
若要填充的整个屏幕[ `ListActivity` ](https://developer.xamarin.com/api/type/Android.App.ListActivity/)。
此方法采用[ `ArrayAdapter<T>` ](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter%601/)，用于管理将被放入的列表项的数组[ `ListView` ](https://developer.xamarin.com/api/type/Android.Widget.ListView/)。
的 [`ArrayAdapter<T>`](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter%601/)
构造函数采用应用程序[ `Context` ](https://developer.xamarin.com/api/type/Android.Content.Context/)，每个列表项 （在上一步中创建） 的布局说明和一个`T[]`或 [`Java.Util.IList<T>`](https://developer.xamarin.com/api/type/Java.Util.IList/)
在插入对象的数组 [`ListView`](https://developer.xamarin.com/api/type/Android.Widget.ListView/)
（下一步定义的）。

的 [`TextFilterEnabled`](https://developer.xamarin.com/api/property/Android.Widget.AbsListView.TextFilterEnabled/)
属性启用筛选的文本[ `ListView` ](https://developer.xamarin.com/api/type/Android.Widget.ListView/)，以便当用户开始键入时，将筛选列表。

的 [`ItemClick`](https://developer.xamarin.com/api/event/Android.Widget.AdapterView.ItemClick/)
可以使用事件订阅的单击处理程序。 中的项 [`ListView`](https://developer.xamarin.com/api/type/Android.Widget.ListView/)
是单击，调用该处理程序和一个 [`Toast`](https://developer.xamarin.com/api/type/Android.Widget.Toast/)
显示消息，使用从单击项的文本。

可以使用而不是定义您自己的布局文件的平台提供的列表项设计[ `ListAdapter` ](https://developer.xamarin.com/api/property/Android.App.ListActivity.ListAdapter/)。
例如，请尝试使用`Android.Resource.Layout.SimpleListItem1`而不是`Resource.Layout.list_item`。

以下代码添加到`using`语句：

```csharp
using System;
```
接下来，将以下字符串数组添加为成员`MainActivity`:

```csharp
static readonly string[] countries = new String[] {
    "Afghanistan","Albania","Algeria","American Samoa","Andorra",
    "Angola","Anguilla","Antarctica","Antigua and Barbuda","Argentina",
    "Armenia","Aruba","Australia","Austria","Azerbaijan",
    "Bahrain","Bangladesh","Barbados","Belarus","Belgium",
    "Belize","Benin","Bermuda","Bhutan","Bolivia",
    "Bosnia and Herzegovina","Botswana","Bouvet Island","Brazil","British Indian Ocean Territory",
    "British Virgin Islands","Brunei","Bulgaria","Burkina Faso","Burundi",
    "Cote d'Ivoire","Cambodia","Cameroon","Canada","Cape Verde",
    "Cayman Islands","Central African Republic","Chad","Chile","China",
    "Christmas Island","Cocos (Keeling) Islands","Colombia","Comoros","Congo",
    "Cook Islands","Costa Rica","Croatia","Cuba","Cyprus","Czech Republic",
    "Democratic Republic of the Congo","Denmark","Djibouti","Dominica","Dominican Republic",
    "East Timor","Ecuador","Egypt","El Salvador","Equatorial Guinea","Eritrea",
    "Estonia","Ethiopia","Faeroe Islands","Falkland Islands","Fiji","Finland",
    "Former Yugoslav Republic of Macedonia","France","French Guiana","French Polynesia",
    "French Southern Territories","Gabon","Georgia","Germany","Ghana","Gibraltar",
    "Greece","Greenland","Grenada","Guadeloupe","Guam","Guatemala","Guinea","Guinea-Bissau",
    "Guyana","Haiti","Heard Island and McDonald Islands","Honduras","Hong Kong","Hungary",
    "Iceland","India","Indonesia","Iran","Iraq","Ireland","Israel","Italy","Jamaica",
    "Japan","Jordan","Kazakhstan","Kenya","Kiribati","Kuwait","Kyrgyzstan","Laos",
    "Latvia","Lebanon","Lesotho","Liberia","Libya","Liechtenstein","Lithuania","Luxembourg",
    "Macau","Madagascar","Malawi","Malaysia","Maldives","Mali","Malta","Marshall Islands",
    "Martinique","Mauritania","Mauritius","Mayotte","Mexico","Micronesia","Moldova",
    "Monaco","Mongolia","Montserrat","Morocco","Mozambique","Myanmar","Namibia",
    "Nauru","Nepal","Netherlands","Netherlands Antilles","New Caledonia","New Zealand",
    "Nicaragua","Niger","Nigeria","Niue","Norfolk Island","North Korea","Northern Marianas",
    "Norway","Oman","Pakistan","Palau","Panama","Papua New Guinea","Paraguay","Peru",
    "Philippines","Pitcairn Islands","Poland","Portugal","Puerto Rico","Qatar",
    "Reunion","Romania","Russia","Rwanda","Sqo Tome and Principe","Saint Helena",
    "Saint Kitts and Nevis","Saint Lucia","Saint Pierre and Miquelon",
    "Saint Vincent and the Grenadines","Samoa","San Marino","Saudi Arabia","Senegal",
    "Seychelles","Sierra Leone","Singapore","Slovakia","Slovenia","Solomon Islands",
    "Somalia","South Africa","South Georgia and the South Sandwich Islands","South Korea",
    "Spain","Sri Lanka","Sudan","Suriname","Svalbard and Jan Mayen","Swaziland","Sweden",
    "Switzerland","Syria","Taiwan","Tajikistan","Tanzania","Thailand","The Bahamas",
    "The Gambia","Togo","Tokelau","Tonga","Trinidad and Tobago","Tunisia","Turkey",
    "Turkmenistan","Turks and Caicos Islands","Tuvalu","Virgin Islands","Uganda",
    "Ukraine","United Arab Emirates","United Kingdom",
    "United States","United States Minor Outlying Islands","Uruguay","Uzbekistan",
    "Vanuatu","Vatican City","Venezuela","Vietnam","Wallis and Futuna","Western Sahara",
    "Yemen","Yugoslavia","Zambia","Zimbabwe"
  };
```

这是将被放入的字符串数组[ `ListView` ](https://developer.xamarin.com/api/type/Android.Widget.ListView/)。

运行该应用程序。 可以滚动列表，或键入以筛选它，然后单击要看到一条消息的项。 将显示如下所示的内容：

[![ListView 的国家/地区名称的示例屏幕截图](images/01-listview-example-sml.png)](images/01-listview-example.png#lightbox)

请注意，使用硬编码的字符串数组不是最佳的设计方案。 一个用于为简单起见，本教程中演示 [`ListView`](https://developer.xamarin.com/api/type/Android.Widget.ListView/)
小组件。 更好的做法是定义的外部资源，例如，使用一个字符串数组中引用`string-array`在项目中的资源**Resources/Values/Strings.xml**文件。 例如：

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
  <string name="app_name">HelloListView</string>
  <string-array name="countries_array">
    <item>Bahrain</item>
    <item>Bangladesh</item>
    <item>Barbados</item>
    <item>Belarus</item>
    <item>Belgium</item>
    <item>Belize</item>
    <item>Benin</item>
  </string-array>
</resources>
```

若要使用这些资源字符串[ `ArrayAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter%601/)，替换原始 [`ListAdapter`](https://developer.xamarin.com/api/property/Android.App.ListActivity.ListAdapter/)
使用以下行：

```csharp
string[] countries = Resources.GetStringArray (Resource.Array.countries_array);
ListAdapter = new ArrayAdapter<string> (this, Resource.Layout.list_item, countries);
```
运行该应用程序。 将显示如下所示的内容：

[![使用较小的名称的列表的 ListView 示例屏幕截图](images/02-smaller-example-sml.png)](images/02-smaller-example.png#lightbox)


## <a name="going-further-with-listview"></a>进一步深入了解 ListView

（链接见下） 的其余主题需要全面了解如何使用`ListView`类以及可用于的适配器类型的不同类型。 该结构如下所示：

-   **可视外观**&ndash;组成部分`ListView`控件以及它们如何工作。

-   **类**&ndash;用来显示类概述`ListView`。

-   **在 ListView 中显示数据**&ndash;如何显示的数据的简单列表; 如何实现`ListView's`可用性功能; 如何使用不同的内置行布局; 以及适配器如何通过重复使用行视图节省内存。

-   **自定义外观**&ndash;的样式更改`ListView`与自定义布局、 字体和颜色。

-   **使用 SQLite** &ndash;如何显示与 SQLite 数据库中的数据`CursorAdapter`。

-   **活动生命周期**&ndash;时实现的设计注意事项`ListView`活动，包括其中生命周期中应填充你的数据以及何时释放资源。

讨论 （分解为六个部分） 开头的概述`ListView`类本身之前介绍的如何使用它以渐进方式更复杂的示例。

-   [ListView 部件和功能](~/android/user-interface/layouts/list-view/parts-and-functionality.md)
-   [填充 ListView 的数据](~/android/user-interface/layouts/list-view/populating.md)
-   [自定义 ListView 的外观](~/android/user-interface/layouts/list-view/customizing-appearance.md)
-   [使用 CursorAdapters](~/android/user-interface/layouts/list-view/cursor-adapters.md)
-   [使用 ContentProvider](~/android/user-interface/layouts/list-view/content-provider.md)
-   [ListView 和活动生命周期](~/android/user-interface/layouts/list-view/activity-lifecycle.md)


## <a name="summary"></a>总结

此系列主题引入`ListView`并提供如何使用的内置功能的一些示例`ListActivity`。 它介绍了的自定义实现`ListView`允许的色彩缤纷的布局并使用 SQLite 数据库，并简要谈及活动生命周期的相关性你`ListView`实现。


## <a name="related-links"></a>相关链接

- [AccessoryViews （示例）](https://developer.xamarin.com/samples/AccessoryViews/)
- [BasicTableAndroid (sample)](https://developer.xamarin.com/samples/BasicTableAndroid/)
- [BasicTableAdapter （示例）](https://developer.xamarin.com/samples/BasicTableAdapter/)
- [BuiltInViews （示例）](https://developer.xamarin.com/samples/BuiltInViews/)
- [CustomRowView （示例）](https://developer.xamarin.com/samples/CustomRowView/)
- [FastScroll （示例）](https://developer.xamarin.com/samples/FastScroll/)
- [SectionIndex （示例）](https://developer.xamarin.com/samples/SectionIndex/)
- [SimpleCursorTableAdapter (sample)](https://developer.xamarin.com/samples/SimpleCursorTableAdapter/)
- [CursorTableAdapter （示例）](https://developer.xamarin.com/samples/CursorTableAdapter/)
- [活动生命周期教程](~/android/app-fundamentals/activity-lifecycle/index.md)
- [使用表和单元格 （在 Xamarin.iOS)](~/ios/user-interface/controls/tables/index.md)
- [ListView 类引用](https://developer.xamarin.com/api/type/Android.Widget.ListView/)
- [ListActivity 类引用](https://developer.xamarin.com/api/type/Android.App.ListActivity/)
- [BaseAdapter 类引用](https://developer.xamarin.com/api/type/Android.Widget.BaseAdapter/)
- [ArrayAdapter 类引用](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter/)
- [CursorAdapter 类引用](https://developer.xamarin.com/api/type/Android.Widget.CursorAdapter/)
