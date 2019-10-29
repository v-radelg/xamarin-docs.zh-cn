---
title: 在 Xamarin 中自动调整行高
description: 本文档介绍如何添加到 Xamarin apps 表视图，这些行的高度因内容而异。 它讨论了 iOS 设计器中的单元布局和启用自动调整大小的高度。
ms.prod: xamarin
ms.assetid: CE45A385-D40A-482A-90A0-E8382C2BFFB9
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: 4129370ecb465340a893e0a7f16703a08cc1db72
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73021927"
---
# <a name="auto-sizing-row-height-in-xamarinios"></a>在 Xamarin 中自动调整行高

从 iOS 8 开始，Apple 增加了创建表格视图（`UITableView`）的功能，可以使用自动布局、大小类和约束，根据其内容的大小自动增长和收缩给定行的高度。

iOS 11 增加了自动扩展行的能力。 现在，标题、页脚和单元格可以根据其内容自动调整大小。 但是，如果你的表是在 iOS 设计器中创建的，Interface Builder，或者它具有固定的行高度，则必须手动启用自行调整大小的单元格，如本指南中所述。

## <a name="cell-layout-in-the-ios-designer"></a>IOS 设计器中的单元布局

打开要在 iOS 设计器中为其自动调整行的表视图的情节提要，选择单元格的*原型*并设计单元格的布局。 例如:

[![](autosizing-row-height-images/table01.png "The Cell's Prototype design")](autosizing-row-height-images/table01.png#lightbox)

对于原型中的每个元素，添加约束以使元素保持正确位置，因为调整表视图的大小以进行旋转或不同的 iOS 设备屏幕大小。 例如，将 `Title` 固定到单元格*内容视图*的顶部、左侧和右侧：

[![](autosizing-row-height-images/table02.png "Pinning the Title to the top, left and right of the Cells Content View")](autosizing-row-height-images/table02.png#lightbox)

对于我们的示例表，小型 `Label` （在 `Title`下）是可以缩小和增大以增加或减小行高的字段。 若要实现此效果，请添加以下约束，以便固定标签的左、右、顶部和底部：

[![](autosizing-row-height-images/table03.png "These constraints to pin the left, right, top and bottom of the label")](autosizing-row-height-images/table03.png#lightbox)

现在，我们已完全限制了单元格中的元素，我们需要阐明应拉伸哪个元素。 为此，请根据 Properties Pad 的**布局**部分中的需要，设置**内容 Hugging 优先级**和**内容压缩电阻优先级**：

[![](autosizing-row-height-images/table03a.png "The Layout section of the Properties Pad")](autosizing-row-height-images/table03a.png#lightbox)

将要展开的元素设置为具有**较低**的 Hugging 优先级值和**较小**的压缩电阻优先级值。

接下来，我们需要选择单元格原型，并为其指定一个唯一**标识符**：

[![](autosizing-row-height-images/table04.png "Giving the Cell Prototype a unique Identifier")](autosizing-row-height-images/table04.png#lightbox)

对于我们的示例，`GrowCell`。 稍后我们将使用此值来填充表。

> [!IMPORTANT]
> 如果您的表包含多个单元格类型（**原型**），则需要确保每个类型都有自己的唯一 `Identifier` 以便自动调整行大小。

为单元原型的每个元素分配一个**名称**以将其公开给C#代码。 例如:

[![](autosizing-row-height-images/table05.png "Assign a Name to expose it to C# code")](autosizing-row-height-images/table05.png#lightbox)

接下来，为 `UITableViewController`、`UITableView` 和 `UITableCell` （原型）添加自定义类。 例如: 

[![](autosizing-row-height-images/table06.png "Adding a custom class for the UITableViewController, the UITableView and the UITableCell")](autosizing-row-height-images/table06.png#lightbox)

最后，为了确保所有预期内容都显示在标签中，请将 "**行**" 属性设置为 `0`：

[![](autosizing-row-height-images/table06.png "The Lines property set to 0")](autosizing-row-height-images/table06a.png#lightbox)

定义 UI 后，让我们添加代码，使自动调整行高。

## <a name="enabling-auto-resizing-height"></a>启用自动调整大小

在表视图的数据源（`UITableViewDatasource`）或源（`UITableViewSource`）中，当我们取消对单元格的排队时，需要使用在设计器中定义的 `Identifier`。 例如:

```csharp
public string CellID {
    get { return "GrowCell"; }
}
...

public override UITableViewCell GetCell (UITableView tableView, Foundation.NSIndexPath indexPath)
{
    var cell = tableView.DequeueReusableCell (CellID, indexPath) as GrowRowTableCell;
    var item = Items [indexPath.Row];

    // Setup
    cell.Image = UIImage.FromFile(item.ImageName);
    cell.Title = item.Title;
    cell.Description = item.Description;

    return cell;
}
```

默认情况下，将设置表视图以便自动调整行高。 为了确保这一点，`RowHeight` 属性应该设置为 `UITableView.AutomaticDimension`。 我们还需要设置 `UITableViewController`中的 `EstimatedRowHeight` 属性。 例如:

```csharp
public override void ViewWillAppear (bool animated)
{
    base.ViewWillAppear (animated);

    // Initialize table
    TableView.DataSource = new GrowRowTableDataSource(this);
    TableView.Delegate = new GrowRowTableDelegate (this);
    TableView.RowHeight = UITableView.AutomaticDimension;
    TableView.EstimatedRowHeight = 40f;
    TableView.ReloadData ();
}
```

此估算不必精确，只是表视图中每行的平均高度的粗略估计。

执行此代码后，在运行应用时，每行都将根据单元原型中最后一个标签的高度进行收缩和增长。 例如:

[![](autosizing-row-height-images/table07.png "A sample table run")](autosizing-row-height-images/table07.png#lightbox)

## <a name="related-links"></a>相关链接

- [GrowRowTable （示例）](https://docs.microsoft.com/samples/xamarin/ios-samples/growrowtable)
