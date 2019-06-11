---
title: 自定义 ListView 的外观
ms.prod: xamarin
ms.assetid: B09AD282-2C4F-D71E-6806-9B1EF05C2CD4
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/26/2018
ms.openlocfilehash: c8a6e7a9abf9e8430253b9402d69f43744bf93c0
ms.sourcegitcommit: 2eb8961dd7e2a3e06183923adab6e73ecb38a17f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2019
ms.locfileid: "66827665"
---
# <a name="customizing-a-listviews-appearance"></a>自定义 ListView 的外观


## <a name="overview"></a>概述

正在显示的行的布局由指定 ListView 的外观。 若要更改的外观`ListView`，使用不同的行的布局。


## <a name="built-in-row-views"></a>内置行视图

有十二个视图，可以使用引用**Android.Resource.Layout**:

- **TestListItem** &ndash;单一具有最少的格式的文本的行。

- **SimpleListItem1** &ndash;的单行文本。

- **SimpleListItem2** &ndash;两行文本。

- **SimpleSelectableListItem** &ndash;单一的支持 （API 级别 11 中已加入） 的单个或多个项目选择的文本行。

- **SimpleListItemActivated1** &ndash; SimpleListItem1，与相似，但背景颜色指示当选择行 （API 级别 11 中添加）。

- **SimpleListItemActivated2** &ndash; SimpleListItem2，与相似，但背景颜色指示当选择行 （API 级别 11 中添加）。

- **SimpleListItemChecked** &ndash;显示复选标记以指示选定内容。

- **SimpleListItemMultipleChoice** &ndash;显示复选框以指示多重选择。

- **SimpleListItemSingleChoice** &ndash;显示单选按钮，以指示排斥的选定内容。

- **TwoLineListItem** &ndash;两行文本。

- **ActivityListItem** &ndash;的单行文本与图像。

- **SimpleExpandableListItem** &ndash;可以展开或折叠组按类别和每个组的行。

每个内置行视图有与之关联的内置的样式。 这些屏幕截图显示了每个视图的显示方式：

[![TestListItem、 SimpleSelectableListItem、 SimpleListitem1 和 SimpleListItem2 的屏幕快照](customizing-appearance-images/builtinviews.png)](customizing-appearance-images/builtinviews.png#lightbox)

[![SimpleListItemActivated1、 SimpleListItemActivated2、 SimpleListItemChecked 和 SimpleListItemMultipleChecked 的屏幕快照](customizing-appearance-images/builtinviews-2.png)](customizing-appearance-images/builtinviews-2.png#lightbox)

[![SimpleListItemSingleChoice、 TwoLineListItem、 ActivityListItem 和 SimpleExpandableListItem 的屏幕快照](customizing-appearance-images/builtinviews-3.png)](customizing-appearance-images/builtinviews-3.png#lightbox)

**BuiltInViews/HomeScreenAdapter.cs**示例文件 (在**BuiltInViews**解决方案) 包含用于生成不可展开列表项屏幕的代码。 在视图中设置`GetView`方法如下：

```csharp
view = context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleListItem1, null);
```

然后可以通过引用标准控件标识符来设置视图的属性`Text1`，`Text2`并`Icon`下`Android.Resource.Id`（未设置属性的视图不包含，否则将引发异常）：

```csharp
view.FindViewById<TextView>(Android.Resource.Id.Text1).Text = item.Heading;
view.FindViewById<TextView>(Android.Resource.Id.Text2).Text = item.SubHeading;
view.FindViewById<ImageView>(Android.Resource.Id.Icon).SetImageResource(item.ImageResourceId); // only use with ActivityListItem
```

**BuiltInExpandableViews/ExpandableScreenAdapter.cs**示例文件 (在**BuiltInViews**解决方案) 包含用于生成 SimpleExpandableListItem 屏幕的代码。 在中设置的组视图`GetGroupView`方法如下：

```csharp
view = context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleExpandableListItem1, null);
```

在中设置子视图`GetChildView`方法如下：

```csharp
view = context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleExpandableListItem2, null);
```

然后可以通过引用标准来设置的组视图和子视图的属性`Text1`和`Text2`控制标识符，如上所示。 SimpleExpandableListItem 屏幕截图 （如上所示） 提供了一个行组视图 (SimpleExpandableListItem1) 和两行代码子视图 (SimpleExpandableListItem2) 的示例。 或者，可以为两行 (SimpleExpandableListItem2) 配置的组视图和子视图可以配置为一个行 (SimpleExpandableListItem1)，或这两组视图和子视图可以具有相同数目的行。 



## <a name="accessories"></a>附件

行可以具有附件添加到视图的右侧，以指示选择状态：

- **SimpleListItemChecked** &ndash;创建单项选择列表，其中检查作为指示器。

- **SimpleListItemSingleChoice** &ndash;创建只有一个选择是可能的单选按钮类型列表。

- **SimpleListItemMultipleChoice** &ndash;创建多个选项是可能的复选框类型列表。

前面提到的附件其各自的顺序中的以下屏幕所示：

[![屏幕截图的 SimpleListItemChecked、 SimpleListItemSingleChoice 和具有附件 SimpleListItemMultipleChoice](customizing-appearance-images/accessories.png)](customizing-appearance-images/accessories.png#lightbox)

若要显示一个这些附件传递到适配器的必需的布局资源 ID 然后手动设置所需的行的选择状态。 这行代码显示了如何创建和分配`Adapter`使用这些布局之一：

```csharp
ListAdapter = new ArrayAdapter<String>(this, Android.Resource.Layout.SimpleListItemChecked, items);
```

`ListView`本身支持不同的选择模式，而不考虑所显示的附件。 若要避免混淆，请使用`Single`使用的选择模式`SingleChoice`附件和`Checked`或`Multiple`模式`MultipleChoice`样式。 选择模式受`ChoiceMode`属性的`ListView`。


### <a name="handling-api-level"></a>处理 API 级别

早期版本的 Xamarin.Android 实现为整数属性的枚举。 最新版本引入了合适的.NET 枚举类型从而得更轻松地发现潜在的选项。

具体取决于哪个 API 级别为目标，`ChoiceMode`是一个整数或枚举。 示例文件**AccessoryViews/HomeScreen.cs**已块注释掉如果你想要面向 Gingerbread API:

```csharp
// For targeting Gingerbread the ChoiceMode is an int, otherwise it is an
// enumeration.

lv.ChoiceMode = Android.Widget.ChoiceMode.Single; // 1
//lv.ChoiceMode = Android.Widget.ChoiceMode.Multiple; // 2
//lv.ChoiceMode = Android.Widget.ChoiceMode.None; // 0

// Use this block if targeting Gingerbread or lower
/*
lv.ChoiceMode = 1; // Single
//lv.ChoiceMode = 0; // none
//lv.ChoiceMode = 2; // Multiple
//lv.ChoiceMode = 3; // MultipleModal
*/
```


### <a name="selecting-items-programmatically"></a>以编程方式选择项

手动将设置为哪些项选择通过`SetItemChecked`方法 （它可以多次调用了多重选择）：

```csharp
// Set the initially checked row ("Fruits")
lv.SetItemChecked(1, true);
```

该代码还需要检测以不同的方式从多个选择选择一个选项。 若要确定在选择了哪一行`Single`模式下使用`CheckedItemPosition`整数属性：

```csharp
FindViewById<ListView>(Android.Resource.Id.List).CheckedItemPosition
```

若要确定在选择了哪些行`Multiple`模式下需要遍历`CheckedItemPositions` `SparseBooleanArray`。 稀疏数组就像一个字典，其中只包含条目的值已更改，因此您必须遍历整个数组寻找`true`值要知道什么具有已选择列表中，如下面的代码段中所示：

```csharp
var sparseArray = FindViewById<ListView>(Android.Resource.Id.List).CheckedItemPositions;
for (var i = 0; i < sparseArray.Size(); i++ )
{
   Console.Write(sparseArray.KeyAt(i) + "=" + sparseArray.ValueAt(i) + ",");
}
Console.WriteLine();
```


## <a name="creating-custom-row-layouts"></a>创建自定义行布局

四个内置行视图是非常简单。 要显示更复杂的布局 （如电子邮件或推文或联系信息的列表） 将需要自定义视图。 自定义视图通常声明为 AXML 文件**资源/布局**目录，然后再加载，使用其资源的自定义适配器的 Id。 该视图可以包含任意数量的显示类 （如 TextViews、 ImageViews 和其他控件） 使用自定义颜色、 字体和布局。

此示例与前面的示例通过多种方式不同：

-  继承自`Activity`，而不`ListActivity`。 你可以为任何自定义行`ListView`，但也可以在包含其他控件`Activity`布局 （如标题、 按钮或其他用户界面元素）。 此示例将上面的标题`ListView`来说明。

-  屏幕; 需要一个 AXML 布局文件在前面的示例`ListActivity`不需要的布局文件。 包含此 AXML`ListView`控制声明。

-  需要一个 AXML 布局文件，可呈现每个行。 此 AXML 文件包含自定义字体和颜色设置的文本和图像控件。

-  使用可选的自定义选择器 XML 文件选择此项时设置的行的外观。

-  `Adapter`实现返回从自定义布局`GetView`重写。

-  `ItemClick` 必须以不同的方式声明 (事件处理程序附加到`ListView.ItemClick`而不是重写`OnListItemClick`中`ListActivity`)。


这些更改下面详细介绍，开始创建活动的视图和自定义行视图，然后介绍对适配器和活动来呈现它们的修改。


### <a name="adding-a-listview-to-an-activity-layout"></a>将 ListView 添加到活动布局

因为`HomeScreen`不再继承`ListActivity`它没有默认视图中，因此必须为 HomeScreen 的视图创建布局 AXML 文件。 对于此示例，该视图将有一个标题 (使用`TextView`) 和一个`ListView`以显示数据。 在中定义布局**Resources/Layout/HomeScreen.axml**文件如下所示：

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
   android:orientation="vertical"
   android:layout_width="fill_parent"
   android:layout_height="fill_parent">
    <TextView android:id="@+id/Heading"
        android:text="Vegetable Groups"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:background="#00000000"
        android:textSize="30dp"
        android:textColor="#FF267F00"
        android:textStyle="bold"
        android:padding="5dp"
    />
    <ListView android:id="@+id/List"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:cacheColorHint="#FFDAFF7F"
    />
</LinearLayout>
```

使用的好处`Activity`与自定义布局 (而不是`ListActivity`) 在于能够将其他控件添加到屏幕上，如标题`TextView`在此示例中。


### <a name="creating-a-custom-row-layout"></a>创建自定义行布局

另一个 AXML 布局文件需包含会在列表视图中显示每个行的自定义布局。 在此示例中的行，将有绿色背景、 棕色、 文本和右对齐图像。 Android XML 标记来声明此布局中所述**Resources/Layout/CustomView.axml**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout  xmlns:android="http://schemas.android.com/apk/res/android"
   android:layout_width="fill_parent"
   android:layout_height="wrap_content"
   android:background="#FFDAFF7F"
   android:padding="8dp">
    <LinearLayout android:id="@+id/Text"
       android:orientation="vertical"
       android:layout_width="wrap_content"
       android:layout_height="wrap_content"
       android:paddingLeft="10dip">
        <TextView
         android:id="@+id/Text1"
         android:layout_width="wrap_content"
         android:layout_height="wrap_content"
         android:textColor="#FF7F3300"
         android:textSize="20dip"
         android:textStyle="italic"
         />
        <TextView
         android:id="@+id/Text2"
         android:layout_width="wrap_content"
         android:layout_height="wrap_content"
         android:textSize="14dip"
         android:textColor="#FF267F00"
         android:paddingLeft="100dip"
         />
    </LinearLayout>
    <ImageView
        android:id="@+id/Image"
        android:layout_width="48dp"
        android:layout_height="48dp"
        android:padding="5dp"
        android:src="@drawable/icon"
        android:layout_alignParentRight="true" />
</RelativeLayout >
```

而自定义行布局可以包含许多不同的控件，滚动性能可能受到复杂的设计和使用映像 （尤其是如果他们有要通过网络加载）。 有关解决滚动性能问题，请参阅 Google 的文章了解详细信息。


### <a name="referencing-a-custom-row-view"></a>引用自定义行视图

自定义适配器示例的实现`HomeScreenAdapter.cs`。 关键方法是`GetView`，它会加载自定义 AXML 使用的资源 ID `Resource.Layout.CustomView`，然后在每个视图，然后再返回它在控件上设置属性。 完整的适配器类所示：

```csharp
public class HomeScreenAdapter : BaseAdapter<TableItem> {
   List<TableItem> items;
   Activity context;
   public HomeScreenAdapter(Activity context, List<TableItem> items)
       : base()
   {
       this.context = context;
       this.items = items;
   }
   public override long GetItemId(int position)
   {
       return position;
   }
   public override TableItem this[int position]
   {
       get { return items[position]; }
   }
   public override int Count
   {
       get { return items.Count; }
   }
   public override View GetView(int position, View convertView, ViewGroup parent)
   {
       var item = items[position];
       View view = convertView;
       if (view == null) // no view to re-use, create new
           view = context.LayoutInflater.Inflate(Resource.Layout.CustomView, null);
       view.FindViewById<TextView>(Resource.Id.Text1).Text = item.Heading;
       view.FindViewById<TextView>(Resource.Id.Text2).Text = item.SubHeading;
       view.FindViewById<ImageView>(Resource.Id.Image).SetImageResource(item.ImageResourceId);
       return view;
   }
}
```


### <a name="referencing-the-custom-listview-in-the-activity"></a>引用活动中自定义的 ListView

因为`HomeScreen`类现在继承自`Activity`、`ListView`要保存对 AXML 中声明的控件的引用的类中声明字段：

```csharp
ListView listView;
```

然后，类必须加载活动的自定义布局 AXML 使用`SetContentView`方法。 然后，它可以找到`ListView`布局中的控件然后创建并分配该适配器并分配的单击处理程序。 OnCreate 方法中的代码如下所示：

```csharp
SetContentView(Resource.Layout.HomeScreen); // loads the HomeScreen.axml as this activity's view
listView = FindViewById<ListView>(Resource.Id.List); // get reference to the ListView in the layout

// populate the listview with data
listView.Adapter = new HomeScreenAdapter(this, tableItems);
listView.ItemClick += OnListItemClick;  // to be defined
```

最后`ItemClick`必须定义处理程序; 在这种情况下它只是显示`Toast`消息：

```csharp
void OnListItemClick(object sender, AdapterView.ItemClickEventArgs e)
{
   var listView = sender as ListView;
   var t = tableItems[e.Position];
   Android.Widget.Toast.MakeText(this, t.Heading, Android.Widget.ToastLength.Short).Show();
}
```

生成屏幕如下所示：

[![生成 CustomRowView 的屏幕截图](customizing-appearance-images/customrowview.png)](customizing-appearance-images/customrowview.png#lightbox)



### <a name="customizing-the-row-selector-color"></a>自定义的行选择器颜色

触摸某一行时它应会突出显示用户反馈。 自定义视图时指定为作为背景色**CustomView.axml** ，它还重写选择突出显示。 这行代码中**CustomView.axml**背景的浅绿色，但它也意味着没有可视的指示器时接触的行集：

```xml
android:background="#FFDAFF7F"
```

若要重新启用突出显示行为，以及自定义使用的颜色，请改为将背景属性设置为自定义选择器。 选择器将声明的默认背景色以及突出显示颜色。 该文件**Resources/Drawable/CustomSelector.xml**包含以下声明：

```xml
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
<item android:state_pressed="false"
  android:state_selected="false"
  android:drawable="@color/cellback" />
<item android:state_pressed="true" >
  <shape>
     <gradient
      android:startColor="#E77A26"
        android:endColor="#E77A26"
        android:angle="270" />
  </shape>
</item>
<item android:state_selected="true"
  android:state_pressed="false"
  android:drawable="@color/cellback" />
</selector>
```

若要引用自定义选择器，更改中的背景特性**CustomView.axml**到：

```xml
android:background="@drawable/CustomSelector"
```

所选的行和相应`Toast`消息现在如下所示：

[![橙色，并显示所选行的名称的 Toast 消息中的所选的行](customizing-appearance-images/customselectcolor.png)](customizing-appearance-images/customselectcolor.png#lightbox)



### <a name="preventing-flickering-on-custom-layouts"></a>阻止在自定义布局上闪烁

Android 尝试改进的性能`ListView`滚动通过缓存布局信息。 如果您有长时间滚动数据的列表还应设置`android:cacheColorHint`属性上的`ListView`（为相同作为自定义行布局的背景的颜色值） 的活动的 AXML 定义中的声明。 不包括此提示可能会导致闪烁当用户滚动浏览列表自定义行背景色。



## <a name="related-links"></a>相关链接

- [BuiltInViews （示例）](https://developer.xamarin.com/samples/monodroid/BuiltInViews/)
- [AccessoryViews （示例）](https://developer.xamarin.com/samples/monodroid/AccessoryViews/)
- [CustomRowView （示例）](https://developer.xamarin.com/samples/monodroid/CustomRowView/)
