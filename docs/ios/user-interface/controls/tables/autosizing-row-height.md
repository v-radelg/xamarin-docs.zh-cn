---
title: 在 Xamarin.iOS 中的自动调整行高
description: 本文档介绍如何将其高度改变基于的内容的表视图行添加到 Xamarin.iOS 应用程序。 它讨论了 iOS 设计器中的单元格布局和启用自动调整大小的高度。
ms.prod: xamarin
ms.assetid: CE45A385-D40A-482A-90A0-E8382C2BFFB9
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: e4446abc73817eb0672cd10a69ff6f738de0c1e1
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61029033"
---
# <a name="auto-sizing-row-height-in-xamarinios"></a>在 Xamarin.iOS 中的自动调整行高

从 iOS 8 开始，Apple 添加创建表视图的功能 (`UITableView`)，可以自动扩展和收缩使用自动布局、 大小类和约束其内容的大小基于某一给定行的高度。

iOS 11 已添加行以自动将其扩展的功能。 标头、 页脚和单元格可以现在自动调整根据其内容。 但是，如果在 iOS 设计器中，Interface Builder 中创建表，或者如果它具有固定行高则必须手动启用自动调整单元格，如本指南中所述。

## <a name="cell-layout-in-the-ios-designer"></a>IOS 设计器中的单元格布局

打开你想要在 iOS 设计器中，有的行的自动调整表视图的情节提要选择单元格的*原型*和设计的单元格的布局。 例如：

[![](autosizing-row-height-images/table01.png "单元格的原型设计")](autosizing-row-height-images/table01.png#lightbox)

对于每个元素在原型中，将约束添加到表视图的旋转或不同的 iOS 设备的屏幕大小调整大小时将元素保留在正确的位置。 例如，固定`Title`上、 左和右侧的单元格*内容视图*:

[![](autosizing-row-height-images/table02.png "固定到顶端、 左侧和右侧的单元格内容视图的标题")](autosizing-row-height-images/table02.png#lightbox)

在示例表中，较小的情况下`Label`(下`Title`) 是可以缩小和增大以增大或减小行的高度的字段。 若要实现此效果，添加以下约束固定左侧、 右侧、 顶部和底部标签：

[![](autosizing-row-height-images/table03.png "这些约束固定左侧、 右侧、 顶部和底部的标签")](autosizing-row-height-images/table03.png#lightbox)

现在，我们已完全受限制的单元中的元素，我们需要明确哪些元素应拉伸。 若要执行此操作，设置**内容拥抱优先级**并**内容压缩阻力优先级**根据需要在**布局**部分中的属性面板：

[![](autosizing-row-height-images/table03a.png "布局部分中的属性面板")](autosizing-row-height-images/table03a.png#lightbox)

设置你想要展开此项可以具有的元素**较低**拥抱优先级值和一个**低**压缩阻力优先级值。

接下来，我们需要选择单元格原型并为其提供一个唯一**标识符**:

[![](autosizing-row-height-images/table04.png "为单元格原型提供的唯一标识符")](autosizing-row-height-images/table04.png#lightbox)

对于本示例中， `GrowCell`。 当我们填充表时，我们将更高版本使用此值。

> [!IMPORTANT]
> 如果表中包含多个单元格类型 (**原型**)，您需要确保每个类型都有自己的唯一`Identifier`自动行调整大小，若要运行的。

我们的单元格原型的每个元素，将分配**名称**公开到C#代码。 例如：

[![](autosizing-row-height-images/table05.png "将分配一个名称以公开到C#代码")](autosizing-row-height-images/table05.png#lightbox)

接下来，添加的自定义类`UITableViewController`，则`UITableView`和`UITableCell`（原型）。 例如： 

[![](autosizing-row-height-images/table06.png "对 UITableViewController、 UITableView 和 UITableCell 添加自定义类")](autosizing-row-height-images/table06.png#lightbox)

最后，若要确保所有预期的内容显示在我们的标签，设置**线条**属性设置为`0`:

[![](autosizing-row-height-images/table06.png "行属性设置为 0")](autosizing-row-height-images/table06a.png#lightbox)

定义用户界面，让我们来添加代码以启用自动行高度的调整大小。

## <a name="enabling-auto-resizing-height"></a>启用自动调整高度

在表视图的数据源 (`UITableViewDatasource`) 或源 (`UITableViewSource`)，当我们取消排队的单元格，我们需要使用`Identifier`我们在设计器中定义。 例如：

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

默认情况下，表视图将设置为自动调整行高。 若要确保此操作，请`RowHeight`属性应设置为`UITableView.AutomaticDimension`。 我们还需要设置`EstimatedRowHeight`属性中的我们`UITableViewController`。 例如：

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

此估计值不一定是准确的只需在表视图中每个行的平均高度的粗略估计。

利用此代码，当应用运行时，每一行将收缩和增长根据单元格原型中的最后一个标签的高度。 例如：

[![](autosizing-row-height-images/table07.png "运行一个示例表")](autosizing-row-height-images/table07.png#lightbox)


## <a name="related-links"></a>相关链接

- [GrowRowTable （示例）](https://developer.xamarin.com/samples/monotouch/GrowRowTable/)
