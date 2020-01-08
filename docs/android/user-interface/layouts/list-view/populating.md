---
title: 使用数据填充 Xamarin ListView ListView
ms.prod: xamarin
ms.assetid: AC4F95C8-EC3F-D960-7D44-8D55D0E4F1B6
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/21/2017
ms.openlocfilehash: 7ec5537345536884e2dc3da02ab54a3ca00f760e
ms.sourcegitcommit: 6f09bc2b760e76a61a854f55d6a87c4f421ac6c8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/02/2020
ms.locfileid: "75607966"
---
# <a name="populating-a-xamarinandroid-listview-with-data"></a>使用数据填充 Xamarin ListView ListView

若要将行添加到 `ListView` 需要将行添加到布局，并使用 `ListView` 调用来填充自身的方法来实现 `IListAdapter`。 Android 包括内置 `ListActivity` 和 `ArrayAdapter` 类，您可以在不定义任何自定义布局 XML 或代码的情况下使用这些类。 `ListActivity` 类将自动创建一个 `ListView`，并公开一个 `ListAdapter` 属性以提供要通过适配器显示的行视图。

内置适配器采用视图资源 ID 作为参数，用于每行。 您可以使用内置资源（如 `Android.Resource.Layout` 中的资源），这样就无需编写您自己的资源。

## <a name="using-listactivity-and-arrayadapterltstringgt"></a>使用 ListActivity 和 ArrayAdapter&lt;字符串&gt;

示例**BasicTable/HomeScreen**演示如何使用这些类只在几行代码中显示 `ListView`：

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
}
```

### <a name="handling-row-clicks"></a>处理行单击

通常情况下，`ListView` 还会允许用户触摸一行来执行某些操作（例如播放歌曲，或调用联系人或显示其他屏幕）。 若要响应用户的接触，需要在 `ListActivity` 中实现一种更多的方法 &ndash; `OnListItemClick` &ndash; 如下所示：

[SimpleListItem 的 ![屏幕快照](populating-images/simplelistitem1.png)](populating-images/simplelistitem1.png#lightbox)

```csharp
protected override void OnListItemClick(ListView l, View v, int position, long id)
{
   var t = items[position];
   Android.Widget.Toast.MakeText(this, t, Android.Widget.ToastLength.Short).Show();
}
```

现在用户可以触摸一行，此时将显示 `Toast` 警报：

[触摸行时显示的 Toast 的 ![屏幕截图](populating-images/basictable2.png)](populating-images/basictable2.png#lightbox)

## <a name="implementing-a-listadapter"></a>实现 ListAdapter

`ArrayAdapter<string>` 很简单，因为它很简单，但却非常有限。 不过，通常会有一系列业务实体，而不只是要绑定的字符串。
例如，如果您的数据包含 Employee 类的集合，则您可能希望该列表只显示每个雇员的姓名。 若要自定义 `ListView` 的行为来控制要显示的数据，您必须实现 `BaseAdapter` 的子类覆盖以下四项：

- **计数**&ndash; 来告知控件数据中的行数。

- **GetView** &ndash; 返回每个行的视图，并用数据填充。
    此方法有一个参数，该参数用于将 `ListView` 传入未使用的现有行以供重用。

- **GetItemId** &ndash; 返回行标识符（通常为行号，尽管它可以是你喜欢的任意长值）。

- **此 [int]** 索引器 &ndash; 返回与特定行号关联的数据。

**BasicTableAdapter/HomeScreenAdapter**中的示例代码演示了如何 `BaseAdapter`子类：

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

使用自定义适配器与内置 `ArrayAdapter`类似，传入 `context` 和要显示的值的 `string[]`：

```csharp
ListAdapter = new HomeScreenAdapter(this, items);
```

因为此示例使用相同的行布局（`SimpleListItem1`），所以，生成的应用程序将与前面的示例类似。

### <a name="row-view-re-use"></a>行视图重复使用

在此示例中，只有六个项。 由于屏幕可以容纳8个，因此不需要任何行重复使用。 但是，在显示几百行或数千行时，如果一次只在屏幕上容纳8个，则创建数百或数千个 `View` 对象会浪费内存。 若要避免这种情况，当行从屏幕上消失时，其视图将放置在队列中供重复使用。 当用户滚动时，`ListView` 调用 `GetView` 以请求新视图显示 &ndash; 如果可用，它将在 `convertView` 参数中传递未使用的视图。 如果此值为 null，你的代码应创建新的视图实例，否则你可以重新设置该对象的属性并重新使用它。

`GetView` 方法应遵循此模式以重新使用行视图：

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

自定义适配器实现应*始终*在创建新视图之前重复使用 `convertView` 对象，以确保它们在显示长列表时不会用尽内存。

某些适配器实现（如 `CursorAdapter`）没有 `GetView` 方法，而是需要两种不同的方法 `NewView` 和 `BindView`，通过将 `GetView` 的责任划分为两个方法，从而强制执行行重复使用。 本文档后面有一个 `CursorAdapter` 示例。

## <a name="enabling-fast-scrolling"></a>启用快速滚动

快速滚动可提供一个额外的 "句柄" 来帮助用户滚动长列表，以直接访问列表的一部分。 此屏幕截图显示快速滚动手柄：

[使用滚动图柄快速滚动的 ![屏幕截图](populating-images/fastscroll.png)](populating-images/fastscroll.png#lightbox)

导致快速滚动图柄显示起来非常简单，只是将 `FastScrollEnabled` 属性设置为 `true`：

```csharp
ListView.FastScrollEnabled = true;
```

### <a name="adding-a-section-index"></a>添加节索引

节索引为用户提供了更多的反馈，当用户在长列表中快速滚动时，&ndash; 它显示其滚动到的 "部分"。 若要使节索引显示，适配器子类必须实现 `ISectionIndexer` 接口，才能根据要显示的行来提供索引文本：

[以 H 开头的第一节中显示的 H ![屏幕截图](populating-images/sectionindex.png)](populating-images/sectionindex.png#lightbox)

若要实现 `ISectionIndexer` 需要将三个方法添加到适配器：

- **GetSections** &ndash; 提供可显示的部分索引标题的完整列表。 此方法需要 Java 对象的数组，因此代码需要从 .NET 集合创建 `Java.Lang.Object[]`。 在本示例中，它将列表中的初始字符列表作为 `Java.Lang.String` 返回。

- **GetPositionForSection** &ndash; 返回给定节索引的第一个行位置。

- **GetSectionForPosition** &ndash; 返回要为给定行显示的节索引。

示例 `SectionIndex/HomeScreenAdapter.cs` 文件实现了这些方法，并在构造函数中实现了一些其他代码。 构造函数通过循环遍历每一行并提取标题的第一个字符来生成节索引（必须已对项进行排序才能使其正常工作）。

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

创建数据结构后，`ISectionIndexer` 方法非常简单：

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

部分索引标题不需要将1:1 映射到实际部分。 这就是 `GetPositionForSection` 方法存在的原因。
`GetPositionForSection` 使您有机会将索引列表中的任何索引映射到列表视图中的任何部分。 例如，在索引中可能有一个 "z"，但对于每个字母，可能没有表节，因此，它可能会映射到25或24，或者应映射到的任何节索引 "z"。

## <a name="related-links"></a>相关链接

- [BasicTableAndroid （示例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/basictableandroid)
- [BasicTableAdapter （示例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/basictableadapter)
- [FastScroll （示例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/fastscroll)
