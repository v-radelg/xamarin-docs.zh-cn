---
title: 使用数据填充 ListView
ms.prod: xamarin
ms.assetid: AC4F95C8-EC3F-D960-7D44-8D55D0E4F1B6
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/21/2017
ms.openlocfilehash: 57c69223a01074ed15714026b7e9ec4e995808e0
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61171083"
---
# <a name="populating-a-listview-with-data"></a>使用数据填充 ListView


## <a name="overview"></a>概述

若要将行添加到`ListView`你需要将其添加到你的布局和实现`IListAdapter`方法的`ListView`调用来填充本身。 Android 包括内置`ListActivity`和`ArrayAdapter`可以不定义任何自定义布局 XML 或代码的情况下使用的类。 `ListActivity`类会自动创建`ListView`，并公开`ListAdapter`提供行视图以显示通过适配器的属性。

内置适配器采用视图资源 ID 作为参数，获取用于每个行。 你可以使用内置的资源，如中`Android.Resource.Layout`以便您无需编写您自己。


## <a name="using-listactivity-and-arrayadapterltstringgt"></a>使用 ListActivity 和 ArrayAdapter&lt;字符串&gt;

该示例**BasicTable/HomeScreen.cs**演示了如何使用这些类以显示`ListView`中只有少量的代码行：

```csharp
[Activity(Label = "BasicTable", MainLauncher = true, Icon = "@drawable/icon")]
public class HomeScreen : ListActivity {
   string[] items;
   protected override void OnCreate(Bundle bundle)
   {
       base.OnCreate(bundle);
       items = new string[] { "Vegetables","Fruits","Flower Buds","Legumes","Bulbs","Tubers" };
       ListAdapter = new ArrayAdapter<String>(this, Android.Resource.Layout.SimpleListItem1, items);
   }
   protected override void OnListItemClick(ListView l, View v, int position, long id)
}
```


### <a name="handling-row-clicks"></a>处理行单击

通常`ListView`还使用户有机会接触的行执行某些操作 （如播放一首歌曲，或调用一个联系人，或显示另一个屏幕）。 以响应用户收尾工作了需要有一个更多方法中实现`ListActivity` &ndash; `OnListItemClick` &ndash;如下所示：

[![SimpleListItem 的屏幕截图](populating-images/simplelistitem1.png)](populating-images/simplelistitem1.png#lightbox)

```csharp
protected override void OnListItemClick(ListView l, View v, int position, long id)
{
   var t = items[position];
   Android.Widget.Toast.MakeText(this, t, Android.Widget.ToastLength.Short).Show();
}
```

现在，用户可以触摸行和`Toast`将会出现警告：

[![屏幕截图的 toast 通知时访问某行时显示](populating-images/basictable2.png)](populating-images/basictable2.png#lightbox)


## <a name="implementing-a-listadapter"></a>实现 ListAdapter

`ArrayAdapter<string>` 非常适合它很简单，但由于严格限制。 但是，通常情况下，有一系列业务实体，而不是只是想要绑定的字符串。
例如，如果你的数据包含的员工类的集合，您可能想要只显示每个雇员的名称的列表。 若要自定义的行为`ListView`来控制显示的数据必须实现的子类`BaseAdapter`重写的以下四个项目：

-   **计数**&ndash;告诉该控件的数据中有多少行。

-   **GetView** &ndash;返回每个行，一个视图填充数据。
    此方法具有一个参数为`ListView`以便重复使用现有的未使用的行中传递。

-   **GetItemId** &ndash;返回的行标识符 （通常行数字，但也可以是任何您喜欢的长整型值）。

-   **此 [int]** 索引器&ndash;以返回与特定行号关联的数据。

中的示例代码**BasicTableAdapter/HomeScreenAdapter.cs**演示了如何创建子类`BaseAdapter`:

```csharp
public class HomeScreenAdapter : BaseAdapter<string> {
   string[] items;
   Activity context;
   public HomeScreenAdapter(Activity context, string[] items) : base() {
       this.context = context;
       this.items = items;
   }
   public override long GetItemId(int position)
  {
       return position;
   }
   public override string this[int position] {  
       get { return items[position]; }
   }
   public override int Count {
       get { return items.Length; }
   }
   public override View GetView(int position, View convertView, ViewGroup parent)
   {
       View view = convertView; // re-use an existing view, if one is available
      if (view == null) // otherwise create a new one
           view = context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleListItem1, null);
       view.FindViewById<TextView>(Android.Resource.Id.Text1).Text = items[position];
       return view;
   }
}
```


### <a name="using-a-custom-adapter"></a>使用自定义适配器

使用自定义适配器是类似于内置`ArrayAdapter`，并传入`context`和`string[]`要显示的值：

```csharp
ListAdapter = new HomeScreenAdapter(this, items);
```

因为此示例使用相同的行布局 (`SimpleListItem1`) 生成的应用程序看起来与前面的示例相同。


### <a name="row-view-re-use"></a>重复使用行视图

在此示例中有仅六个项。 由于屏幕可容纳八个，因此没有行重复使用必需。 在显示时数百或数千个行，但是，它是在浪费的内存来创建数百或数千个`View`对象时仅包括八种每次都适合在屏幕上。 若要避免这种情况下，行从其视图放到队列中以重复使用的屏幕上消失时。 当用户滚动时，`ListView`调用`GetView`若要请求显示新视图&ndash;如果可用它将传递中的未使用的视图`convertView`参数。 如果此值为 null，则你的代码应创建新的视图实例，否则为可以重新设置该对象的属性并重新使用它。

`GetView`方法应遵循此模式重复使用行视图：

```csharp
public override View GetView(int position, View convertView, ViewGroup parent)
{
   View view = convertView; // re-use an existing view, if one is supplied
   if (view == null) // otherwise create a new one
       view = context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleListItem1, null);
   // set view properties to reflect data for the given row
   view.FindViewById<TextView>(Android.Resource.Id.Text1).Text = items[position];
   // return the view, populated with data, for display
   return view;
}
```

自定义适配器实现应该*始终*重复使用`convertView`对象之前创建新视图，以确保它们不会运行内存不足时显示较长的列表。

某些适配器实现 (如`CursorAdapter`) 没有`GetView`方法，而不是它们需要两种不同方法`NewView`并`BindView`其中强制执行重复使用行分隔的职责`GetView`为两个方法。 没有`CursorAdapter`文档后面的示例。


## <a name="enabling-fast-scrolling"></a>启用快速滚动

快速滚动可帮助用户通过提供的其他句柄，它就像直接访问列表的一部分的滚动条来滚动查看较长的列表。 此屏幕截图显示了快速滚动句柄：

[![与滚动句柄快速滚动的屏幕截图](populating-images/fastscroll.png)](populating-images/fastscroll.png#lightbox)

导致要显示的快速滚动句柄就像设置一样简单`FastScrollEnabled`属性设置为`true`:

```csharp
ListView.FastScrollEnabled = true;
```


### <a name="adding-a-section-index"></a>添加部分索引

部分索引为用户提供其他反馈，当它们是快速滚动浏览一长串&ndash;显示他们已滚动到哪些 section。 若要使其显示适配器子类必须实现的部分索引`ISectionIndexer`接口提供具体取决于所显示的行索引文本：

[![屏幕截图的 H H 开头的部分上方显示](populating-images/sectionindex.png)](populating-images/sectionindex.png#lightbox)

若要实现`ISectionIndexer`需要三个方法添加到适配器：

-   **GetSections** &ndash;提供索引可能会显示的标题部分的完整列表。 此方法需要 Java 对象的数组，因此该代码需要创建`Java.Lang.Object[]`从.NET 集合。 在本示例中它将返回一组在列表中的初始字符`Java.Lang.String`。

-   **GetPositionForSection** &ndash;返回给定的部分索引的第一个行位置。

-   **GetSectionForPosition** &ndash;返回部分索引，要为某一给定行显示。


该示例`SectionIndex/HomeScreenAdapter.cs`文件的构造函数中实现这些方法和一些附加代码。 构造函数生成的节索引循环通过每个行并提取的标题 （项必须已进行排序为实现此目的） 的第一个字符。

```csharp
alphaIndex = new Dictionary<string, int>();
for (int i = 0; i < items.Length; i++) { // loop through items
   var key = items[i][0].ToString();
   if (!alphaIndex.ContainsKey(key))
       alphaIndex.Add(key, i); // add each 'new' letter to the index
}
sections = new string[alphaIndex.Keys.Count];
alphaIndex.Keys.CopyTo(sections, 0); // convert letters list to string[]

// Interface requires a Java.Lang.Object[], so we create one here
sectionsObjects = new Java.Lang.Object[sections.Length];
for (int i = 0; i < sections.Length; i++) {
   sectionsObjects[i] = new Java.Lang.String(sections[i]);
}
```

使用创建的数据结构`ISectionIndexer`方法都是非常简单：

```csharp
public Java.Lang.Object[] GetSections()
{
   return sectionsObjects;
}
public int GetPositionForSection(int section)
{
   return alphaIndexer[sections[section]];
}
public int GetSectionForPosition(int position)
{   // this method isn't called in this example, but code is provided for completeness
    int prevSection = 0;
    for (int i = 0; i < sections.Length; i++)
    {
        if (GetPositionForSection(i) > position)
        {
            break;
        }
        prevSection = i;
    }
    return prevSection;
}
```

在部分索引标题不需要将 1 对 1 映射到实际部分。 这就是为什么`GetPositionForSection`方法存在。
`GetPositionForSection` 使你有机会将映射到任何部分都是在列表视图中的索引列表中的任何索引。 例如，您可能必须在索引中的"z"，但你可能不具有用于每个字母的表节，因此可能会映射到而不是"z"映射到 26，25 或 24，或其他任何部分索引"z"应映射到。



## <a name="related-links"></a>相关链接

- [BasicTableAndroid (sample)](https://developer.xamarin.com/samples/BasicTableAndroid/)
- [BasicTableAdapter （示例）](https://developer.xamarin.com/samples/BasicTableAdapter/)
- [FastScroll （示例）](https://developer.xamarin.com/samples/FastScroll/)
