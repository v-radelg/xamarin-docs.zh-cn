---
title: 使用 Xamarin.iOS 中的数据填充表
description: 本文档介绍如何用 Xamarin.iOS 应用程序中的数据填充表。 它讨论了 UITableViewSource，单元格重用添加索引，页眉和页脚。
ms.prod: xamarin
ms.assetid: 6FE64DDF-1029-EB9B-6EEC-1C7DFDFDF3AF
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: 5363e3a2210bdcf1efb870ac808ecb37584de6a7
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57668915"
---
# <a name="populating-a-table-with-data-in-xamarinios"></a>使用 Xamarin.iOS 中的数据填充表

若要将行添加到`UITableView`您需要实现`UITableViewSource`子类并重写表查看方法调用来填充本身。

本指南介绍了：

- 子类化 UITableViewSource
- 单元格重复使用
- 添加索引
- 添加页眉和页脚


<a name="Subclassing_UITableViewSource" />

## <a name="subclassing-uitableviewsource"></a>子类化 UITableViewSource

一个`UITableViewSource`子类分配到每个`UITableView`。 表视图查询的源类，以确定如何呈现自身 （例如，所需的行数和如果不同于默认值每个行的高度）。 最重要的是，源提供了使用数据填充每个单元格视图。

有只有两个生成显示数据的表所需的必需方法：

-   **RowsInSection** – 返回[ `nint` ](https://developer.xamarin.com/guides/cross-platform/macios/nativetypes/)表应显示的数据行的总数的计数。
-   **GetCell** – 返回`UITableCellView`填充传递给方法的相应行索引的数据。


BasicTable 示例文件**TableSource.cs**已有的最简单的可能实现`UITableViewSource`。 在以下代码片段中，您可以看到它接受要在表中显示的字符串数组并返回包含每个字符串的默认单元格样式：

```csharp
public class TableSource : UITableViewSource {

        string[] TableItems;
        string CellIdentifier = "TableCell";

        public TableSource (string[] items)
        {
            TableItems = items;
        }

        public override nint RowsInSection (UITableView tableview, nint section)
        {
            return TableItems.Length;
        }

        public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
        {
            UITableViewCell cell = tableView.DequeueReusableCell (CellIdentifier);
            string item = TableItems[indexPath.Row];

            //---- if there are no cells to reuse, create a new one
            if (cell == null)
            { cell = new UITableViewCell (UITableViewCellStyle.Default, CellIdentifier); }

            cell.TextLabel.Text = item;

            return cell;
        }
}
```

一个`UITableViewSource`可以使用任何数据结构，从简单的字符串数组 （此示例中所示） 对列表 <> 或其他集合。 实现`UITableViewSource`方法将从基础数据结构表中隔离出来。

若要使用此子类，创建一个字符串数组以构造源，然后将其分配给实例`UITableView`:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();
    table = new UITableView(View.Bounds); // defaults to Plain style
    string[] tableItems = new string[] {"Vegetables","Fruits","Flower Buds","Legumes","Bulbs","Tubers"};
    table.Source = new TableSource(tableItems);
    Add (table);
}
```

生成的表如下所示：

 [![](populating-a-table-with-data-images/image3.png "运行示例表")](populating-a-table-with-data-images/image3.png#lightbox)

大多数表允许用户接触的行，以选择它并执行一些其他操作 （如播放一首歌曲，或调用一个联系人，或显示另一个屏幕）。 若要实现此目的，有几件事我们要做。 首先，让我们创建显示一条消息，当用户单击的行添加到以下 AlertController`RowSelected`方法：

```csharp
public override void RowSelected (UITableView tableView, NSIndexPath indexPath)
{
    UIAlertController okAlertController = UIAlertController.Create ("Row Selected", tableItems[indexPath.Row], UIAlertControllerStyle.Alert);
    okAlertController.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));
    ...

    tableView.DeselectRow (indexPath, true);
}
```

接下来，创建视图控制器的实例：

```csharp
HomeScreen owner;
```
将一个构造函数添加到你 UITableViewSource 类，它将作为参数的视图控制器，并将其保存在一个字段：

```csharp
public TableSource (string[] items, HomeScreen owner)
{
    ...
    this.owner = owner;

}
```
修改 ViewDidLoad 方法，其中创建 UITableViewSource 类传递`this`引用：

```csharp
table.Source = new TableSource(tableItems, this);
```
最后，返回你`RowSelected`方法中，调用`PresentViewController`上缓存的字段：

```csharp
public override void RowSelected (UITableView tableView, NSIndexPath indexPath)
{
    ...
    owner.PresentViewController (okAlertController, true, null);

    ...
}
```


用户现在可以触摸某一行，并将会出现警告：



 [![](populating-a-table-with-data-images/image4.png "行所选的警报")](populating-a-table-with-data-images/image4.png#lightbox)


## <a name="cell-reuse"></a>单元格重复使用

在此示例中有仅六个项，因此不需要的单元格重复使用。 在显示时数百或数千个行，但是，它是在浪费的内存来创建数百或数千个`UITableViewCell`对象时一次仅有少数适合屏幕。

若要避免这种情况下，当从其视图放到队列中以重复使用的屏幕消失后一个单元格。 当用户滚动时，表调用`GetCell`以请求新视图以显示 – 若要重复使用现有单元格 （这当前未显示） 只需调用`DequeueReusableCell`方法。 如果单元格以供重复使用它将返回可用，否则为返回 null 和你的代码必须创建新的单元格实例。

此示例中的代码段演示了的模式：

```csharp
// request a recycled cell to save memory
UITableViewCell cell = tableView.DequeueReusableCell (cellIdentifier);
// if there are no cells to reuse, create a new one
if (cell == null)
    cell = new UITableViewCell (UITableViewCellStyle.Default, cellIdentifier);
```

`cellIdentifier`能有效地创建单独的队列，为不同类型的单元格。 在此示例中的所有单元格看起来相同只有一个硬编码使用标识符。 如果有不同类型的单元格它们应每个具有不同的标识符字符串时将它们实例化，并请求从重复使用队列时。

### <a name="cell-reuse-in-ios-6"></a>IOS 6 + 中的单元格重用

iOS 6 添加类似于一个引入与集合视图单元格重复使用模式。 尽管上面所示的现有重用模式仍然支持向后兼容性，这种新模式是更可取如它无需对单元格的 null 检查。

使用新的模式应用程序注册的单元格类或 xib 用于通过调用`RegisterClassForCellReuse`或`RegisterNibForCellReuse`控制器的构造函数中。 然后，当取消排队的单元格中`GetCell`方法，只需调用`DequeueReusableCell`传递为单元格类或 xib 和索引路径注册的标识符。

例如，下面的代码在 UITableViewController 中注册自定义单元格类：

```csharp
public class MyTableViewController : UITableViewController
{
  static NSString MyCellId = new NSString ("MyCellId");

  public MyTableViewController ()
  {
    TableView.RegisterClassForCellReuse (typeof(MyCell), MyCellId);
  }
  ...
}
```

已注册的 MyCell 类，与单元格可以取消排队中`GetCell`方法的`UITableViewSource`而无需额外 null 检查，如图所示如下：

```csharp
class MyTableSource : UITableViewSource
{
  public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
  {
    // if cell is not available in reuse pool, iOS will create one automatically
    // no need to do null check and create cell manually
    var cell = (MyCell) tableView.DequeueReusableCell (MyCellId, indexPath);

    // do whatever you need to with cell, such as assigning properties, etc.

    return cell;
  }
}
```

请注意，当使用自定义单元格类的新的重复使用模式，需要实现构造函数采用`IntPtr`，如下面的代码段中所示，否则 Objective C 将无法构造单元格类的实例：

```csharp
public class MyCell : UITableViewCell
{
  public MyCell (IntPtr p):base(p)
  {
  }
  ...
}
```

您所见上文中所述的主题的示例**BasicTable**示例链接到此文章。

<a name="Adding_an_Index" />

## <a name="adding-an-index"></a>添加索引

索引可帮助用户滚动浏览较长的列表，通常按字母顺序排序，尽管可以索引需要的任何条件。 **BasicTableIndex**示例更长的项列表从文件加载来演示该索引。 在索引中的每个项对应于表部分。

 [![](populating-a-table-with-data-images/image5.png "索引显示")](populating-a-table-with-data-images/image5.png#lightbox)

若要支持 sections 表中的数据需要进行分组，因此 BasicTableIndex 示例创建`Dictionary<>`从使用每个项的第一个字母作为字典的键的字符串数组：

```csharp
indexedTableItems = new Dictionary<string, List<string>>();
foreach (var t in items) {
    if (indexedTableItems.ContainsKey (t[0].ToString ())) {
        indexedTableItems[t[0].ToString ()].Add(t);
    } else {
        indexedTableItems.Add (t[0].ToString (), new List<string>() {t});
    }
}
keys = indexedTableItems.Keys.ToArray ();
```

`UITableViewSource`子类则需要添加或修改为使用以下方法`Dictionary<>`:

-   **NumberOfSections** – 此方法是可选的默认情况下此表假设一个部分。 显示索引时此方法应返回索引 (例如，26 如果索引包含英文字母表中的所有字母) 中的项的数目。
-   **RowsInSection** -给定部分中返回的行数。
-   **SectionIndexTitles** – 返回将用于显示索引的字符串数组。 示例代码返回数组的字母。


示例文件中的更新的方法**BasicTableIndex/TableSource.cs**如下所示：

```csharp
public override nint NumberOfSections (UITableView tableView)
{
    return keys.Length;
}
public override nint RowsInSection (UITableView tableview, nint section)
{
    return indexedTableItems[keys[section]].Count;
}
public override string[] SectionIndexTitles (UITableView tableView)
{
    return keys;
}
```

普通的表样式通常仅适用于索引。


<a name="Adding_Headers_and_Footers" />

## <a name="adding-headers-and-footers"></a>添加页眉和页脚

页眉和页脚可以用于以可视方式在表中的行进行分组。 所需的数据结构是非常类似于添加索引 –`Dictionary<>`的确非常有效。 而不是使用字母表进行分组单元格，此示例将分组 botanical 类型由蔬菜。
输出如下所示：

 [![](populating-a-table-with-data-images/image6.png "示例标头和页脚")](populating-a-table-with-data-images/image6.png#lightbox)

若要显示组头和表尾`UITableViewSource`子类需要这些其他方法：

-   **TitleForHeader** – 返回要用作标头的文本
-   **TitleForFooter** – 返回要用作页脚的文本。


示例文件中的更新的方法**BasicTableHeaderFooter/Code/TableSource.cs**如下所示：

```csharp
public override string TitleForHeader (UITableView tableView, nint section)
{
    return keys[section];
}
public override string TitleForFooter (UITableView tableView, nint section)
{
    return indexedTableItems[keys[section]].Count + " items";
}
```

可以进一步自定义外观的页眉和页脚的视图对象，使用`GetViewForHeader`并`GetViewForFooter`方法重写上`UITableViewSource`。


## <a name="related-links"></a>相关链接

- [WorkingWithTables （示例）](https://developer.xamarin.com/samples/monotouch/WorkingWithTables)
