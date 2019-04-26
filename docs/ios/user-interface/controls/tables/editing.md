---
title: 编辑表使用 Xamarin.iOS
description: 本文档介绍如何编辑 Xamarin.iOS 中的表。 它讨论了轻扫可删除、 编辑模式，以及行插入操作。
ms.prod: xamarin
ms.assetid: EC197F25-E865-AFA3-E5CF-B33FAB7744A0
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: 1267de341a88130c18254f414d2fbb1c42595a0c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61195883"
---
# <a name="editing-tables-with-xamarinios"></a>编辑表使用 Xamarin.iOS

通过重写方法中的启用表编辑功能`UITableViewSource`子类。 最简单的编辑行为是可以使用单个方法重写实现--删除轻扫手势。
编辑模式中的表，可以完成更复杂的编辑 （包括移动行）。

## <a name="swipe-to-delete"></a>轻扫到 Delete

轻扫以删除功能是在 iOS 中的用户期望自然笔势。 

 [![](editing-images/image10.png "轻扫的要删除的示例")](editing-images/image10.png#lightbox)

有三种方法重写会影响轻扫手势以显示**删除**单元格中的按钮：

-   **CommitEditingStyle** – 如果此方法重写，并且会自动启用--删除轻扫手势，检测到的表源。 方法的实现应调用`DeleteRows`上`UITableView`会导致将消失，并且还可以从您的模型 （例如，数组、 字典或数据库） 删除基础数据的单元格。 
-   **CanEditRow** – 如果 CommitEditingStyle 中被重写中，所有行被都假定为可编辑。 如果此方法实现，并返回 false （对于某些特定的行，或所有行） 然后--删除轻扫手势将不能在该单元格。 
-   **TitleForDeleteConfirmation** – 根据需要指定的文本**删除**按钮。 如果未实现此方法将"删除"按钮文本。 


在实现这些方法`TableSource`类如下所示：

```csharp
public override void CommitEditingStyle (UITableView tableView, UITableViewCellEditingStyle editingStyle, Foundation.NSIndexPath indexPath)
{
    switch (editingStyle) {
        case UITableViewCellEditingStyle.Delete:
            // remove the item from the underlying data source
            tableItems.RemoveAt(indexPath.Row);
            // delete the row from the table
            tableView.DeleteRows (new NSIndexPath[] { indexPath }, UITableViewRowAnimation.Fade);
            break;
        case UITableViewCellEditingStyle.None:
            Console.WriteLine ("CommitEditingStyle:None called");
            break;
    }
}
public override bool CanEditRow (UITableView tableView, NSIndexPath indexPath)
{
    return true; // return false if you wish to disable editing for a specific indexPath or for all rows
}
public override string TitleForDeleteConfirmation (UITableView tableView, NSIndexPath indexPath)
{   // Optional - default text is 'Delete'
    return "Trash (" + tableItems[indexPath.Row].SubHeading + ")";
}
```

对于本例`UITableViewSource`已更新为使用`List<TableItem>`（而不一个字符串数组） 为数据源，因为它支持添加和删除项集合中。


## <a name="edit-mode"></a>编辑模式

表处于编辑模式时用户看到的红色 stop 小组件上每个行，将显示一个删除按钮时接触。 该表还显示一个句柄图标，以指示行可以通过拖动进行更改的顺序。
**TableEditMode**示例所示实现这些功能。

 [![](editing-images/image11.png "TableEditMode 示例实现这些功能，如所示")](editing-images/image11.png#lightbox)

有多种不同的方法上`UITableViewSource`影响表的编辑模式下行为：

-   **CanEditRow** -是否可以编辑每个行。 返回 false，以避免轻扫--删除并在编辑模式中的删除。 
-   **CanMoveRow** – 返回 true 以启用移动句柄或 false 可阻止移动。 
-   **EditingStyleForRow** – 当表是在编辑模式下，此方法的返回值确定是否该单元格显示的红色删除图标或绿色添加图标。 返回`UITableViewCellEditingStyle.None`如果行不应为可编辑。 
-   **MoveRow** – 当行移动，以便可以修改基础数据结构将显示在表中的数据匹配时调用。 


除非你想要使用的前三个方法的实现也是相对非常简单 –`indexPath`若要更改特定行的行为，只需进行硬编码返回值为整个表。

```csharp
public override bool CanEditRow (UITableView tableView, NSIndexPath indexPath)
{
    return true; // return false if you wish to disable editing for a specific indexPath or for all rows
}
public override bool CanMoveRow (UITableView tableView, NSIndexPath indexPath)
{
    return true; // return false if you don't allow re-ordering
}
public override UITableViewCellEditingStyle EditingStyleForRow (UITableView tableView, NSIndexPath indexPath)
{
    return UITableViewCellEditingStyle.Delete; // this example doesn't support Insert
}
```

`MoveRow`实现是稍微复杂一些，因为它需要改变基础的数据结构，以匹配新的顺序。 因为数据作为`List`下面的代码删除数据项在其原位置，并将其插入到新位置。 如果数据已存储在一个 SQLite 数据库表具有一个 order 列 （例如），此方法将改为需要执行一些 SQL 操作重新排序的列中的数字。

```csharp
public override void MoveRow (UITableView tableView, NSIndexPath sourceIndexPath, NSIndexPath destinationIndexPath)
{
    var item = tableItems[sourceIndexPath.Row];
    var deleteAt = sourceIndexPath.Row;
    var insertAt = destinationIndexPath.Row;
    
    // are we inserting 
    if (destinationIndexPath.Row < sourceIndexPath.Row) {
        // add one to where we delete, because we're increasing the index by inserting
        deleteAt += 1;
    } else {
        // add one to where we insert, because we haven't deleted the original yet
        insertAt += 1;
    }
    tableItems.Insert (insertAt, item);
    tableItems.RemoveAt (deleteAt);
}
```

最后，若要获取表到编辑模式，则**编辑**按钮需要调用`SetEditing`如下所示

```csharp
table.SetEditing (true, true);
```

和用户已完成时编辑**完成**按钮应当关闭编辑模式：

```csharp
table.SetEditing (false, true);
```


## <a name="row-insertion-editing-style"></a>行插入编辑样式

从表中的行插入操作的常见用户界面-在标准 iOS 应用中的主要示例是**编辑联系人**屏幕。 此屏幕截图显示的行插入功能的工作原理 – 在编辑模式下没有附加行 （单击） 时将其他行插入到数据。 编辑完成时，临时 **（添加新）** ，删除的行。

 [![](editing-images/image12.png "编辑完成后，新添加的临时删除行")](editing-images/image12.png#lightbox)

有多种不同的方法上`UITableViewSource`影响表的编辑模式行为。 这些方法已在示例代码中实现了，如下所示：

-   **EditingStyleForRow** – 返回`UITableViewCellEditingStyle.Delete`包含数据，并返回的行`UITableViewCellEditingStyle.Insert`的最后一行 （这将添加专门用于作插入按钮）。 
-   **CustomizeMoveTarget** – 虽然用户移动此可选的方法的返回值的单元格可以重写其所选的位置。 这意味着可以防止删除某些位置 – 如防止任何行后移动此示例中的单元格 **（添加新）** 行。 
-   **CanMoveRow** – 返回 true 以启用移动句柄或 false 可阻止移动。 在示例中，最后一行已隐藏的因为它用作向服务器仅插入按钮移动句柄。 


我们还添加两个自定义方法来添加 'insert' 行然后再将其移除不再需要时。 从调用**编辑**并**完成**按钮：

-   **WillBeginTableEditing** – 当**编辑**按钮，接触它调用`SetEditing`将表置于编辑模式。 这会触发 WillBeginTableEditing 方法显示 **（添加新）** 要充当插入按钮的表的结尾处添加一行。 
-   **DidFinishTableEditing** – 当接触完成按钮时`SetEditing`再次调用以关闭编辑模式。 示例代码中删除 **（添加新）** 编辑时在表中的一行不再需要。 


示例文件中实现这些方法重写**TableEditModeAdd/Code/TableSource.cs**:

```csharp
public override UITableViewCellEditingStyle EditingStyleForRow (UITableView tableView, NSIndexPath indexPath)
{
    if (tableView.Editing) {
        if (indexPath.Row == tableView.NumberOfRowsInSection (0) - 1)
            return UITableViewCellEditingStyle.Insert;
        else
            return UITableViewCellEditingStyle.Delete;
    } else // not in editing mode, enable swipe-to-delete for all rows
        return UITableViewCellEditingStyle.Delete;
}
public override NSIndexPath CustomizeMoveTarget (UITableView tableView, NSIndexPath sourceIndexPath, NSIndexPath proposedIndexPath)
{
    var numRows = tableView.NumberOfRowsInSection (0) - 1; // less the (add new) one
    if (proposedIndexPath.Row >= numRows)
        return NSIndexPath.FromRowSection(numRows - 1, 0);
    else
        return proposedIndexPath;
} 
public override bool CanMoveRow (UITableView tableView, NSIndexPath indexPath)
{
    return indexPath.Row < tableView.NumberOfRowsInSection (0) - 1;
}
```

这两个自定义方法可用来添加和删除 **（添加新）** 启用或禁用表的编辑模式时的行：

```csharp
public void WillBeginTableEditing (UITableView tableView)
{
    tableView.BeginUpdates ();
    // insert the 'ADD NEW' row at the end of table display
    tableView.InsertRows (new NSIndexPath[] { 
            NSIndexPath.FromRowSection (tableView.NumberOfRowsInSection (0), 0) 
        }, UITableViewRowAnimation.Fade);
    // create a new item and add it to our underlying data (it is not intended to be permanent)
    tableItems.Add (new TableItem ("(add new)"));
    tableView.EndUpdates (); // applies the changes
}
public void DidFinishTableEditing (UITableView tableView)
{
    tableView.BeginUpdates ();
    // remove our 'ADD NEW' row from the underlying data
    tableItems.RemoveAt ((int)tableView.NumberOfRowsInSection (0) - 1); // zero based :)
    // remove the row from the table display
    tableView.DeleteRows (new NSIndexPath[] { NSIndexPath.FromRowSection (tableView.NumberOfRowsInSection (0) - 1, 0) }, UITableViewRowAnimation.Fade);
    tableView.EndUpdates (); // applies the changes
}
```

最后，此代码实例化**编辑**并**完成**按钮、 与启用或禁用编辑模式时，它们接触的 lambda:

```csharp
done = new UIBarButtonItem(UIBarButtonSystemItem.Done, (s,e)=>{
    table.SetEditing (false, true);
    NavigationItem.RightBarButtonItem = edit;
    tableSource.DidFinishTableEditing(table);
});
edit = new UIBarButtonItem(UIBarButtonSystemItem.Edit, (s,e)=>{
    if (table.Editing)
        table.SetEditing (false, true); // if we've half-swiped a row
    tableSource.WillBeginTableEditing(table);
    table.SetEditing (true, true);
    NavigationItem.LeftBarButtonItem = null;
    NavigationItem.RightBarButtonItem = done;
});
```

但是您可以使用，不非常频繁使用此行插入 UI 模式`UITableView.BeginUpdates`和`EndUpdates`方法进行动画处理的插入或删除任何表中的单元格。 使用这些方法的规则是通过返回值中的差异`RowsInSection`之间`BeginUpdates`并`EndUpdates`的调用必须匹配与添加/删除单元格的 net 数目`InsertRows`和`DeleteRows`方法。 如果基础数据源不会更改以匹配将发生错误的表视图上的插入/删除。


## <a name="related-links"></a>相关链接

- [WorkingWithTables （示例）](https://developer.xamarin.com/samples/monotouch/WorkingWithTables)
