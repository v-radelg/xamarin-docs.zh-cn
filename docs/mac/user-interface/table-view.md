---
title: Xamarin 中的表视图
description: 本文介绍如何在 Xamarin. Mac 应用程序中使用表视图。 它介绍了如何在 Xcode 中创建表视图，并 Interface Builder 在代码中与它们进行交互。
ms.prod: xamarin
ms.assetid: 3B55B858-4769-4331-966A-7F53B3B7C720
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 36bed05c1e60004125406c3ed2df66fcfe2be10b
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73008330"
---
# <a name="table-views-in-xamarinmac"></a>Xamarin 中的表视图

_本文介绍如何在 Xamarin. Mac 应用程序中使用表视图。它介绍了如何在 Xcode 中创建表视图，并 Interface Builder 在代码中与它们进行交互。_

在 Xamarin 应用C# *程序中使用*和 .net 时，您可以访问与在*Xcode 和*中工作的开发人员相同的表视图。 因为 Xamarin 与 Xcode 直接集成，你可以使用 Xcode 的_Interface Builder_来创建和维护你的表视图（或者可以选择直接在代码中C#创建）。

表格视图以表格格式显示数据，其中包含多个行中的一列或多列信息。 根据所创建的表视图的类型，用户可以按列排序、重新组织列、添加列、删除列或编辑表中包含的数据。

[![](table-view-images/intro01.png "An example table")](table-view-images/intro01.png#lightbox)

在本文中，我们将介绍在 Xamarin. Mac 应用程序中使用表视图的基础知识。 强烈建议您先完成[Hello，Mac](~/mac/get-started/hello-mac.md)一文，特别是[Xcode 和 Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder)及[输出口和操作](~/mac/get-started/hello-mac.md#outlets-and-actions)部分的简介，因为它涵盖了我们将在本文。

你可能想要查看[Xamarin](~/mac/internals/how-it-works.md)示例文档的 " C# [公开C#类/方法到目标-c](~/mac/internals/how-it-works.md) " 部分，并说明用于将类连接到目标的`Register`和`Export`命令-c对象和 UI 元素。

<a name="Introduction_to_Table_Views" />

## <a name="introduction-to-table-views"></a>表视图简介

表格视图以表格格式显示数据，其中包含多个行中的一列或多列信息。 表视图显示在滚动视图（`NSScrollView`）中，从 macOS 10.7 开始，可以使用任意 `NSView` 而不是单元格（`NSCell`）显示行和列。 也就是说，您仍然可以使用 `NSCell` 不过，您通常 `NSTableCellView` 并创建自定义行和列。

表视图不存储它自己的数据，而是依赖于数据源（`NSTableViewDataSource`）根据需要提供所需的行和列。

可以通过提供表视图委托（`NSTableViewDelegate`）的子类来自定义表视图的行为，以支持表列管理、键入以选择功能、行选择和编辑、自定义跟踪以及单独的列和行的自定义视图。

创建表视图时，Apple 建议以下内容：

- 允许用户通过单击列标题对表进行排序。
- 创建用名词或短名词短语描述列中显示的数据的列标题。

有关详细信息，请参阅 Apple [OS X 人体学接口准则](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)的[内容视图](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsView.html#//apple_ref/doc/uid/20000957-CH52-SW1)部分。

<a name="Creating-and-Maintaining-Table-Views-in-Xcode" />

## <a name="creating-and-maintaining-table-views-in-xcode"></a>在 Xcode 中创建和维护表视图

创建新的 Xamarin Cocoa 应用程序时，默认情况下会获得一个标准空白窗口。 此窗口在项目中自动包含的 `.storyboard` 文件中定义。 若要编辑 windows 设计，请在 "**解决方案资源管理器**中，双击 `Main.storyboard` 文件：

[![](table-view-images/edit01.png "Selecting the main storyboard")](table-view-images/edit01.png#lightbox)

这将在 Xcode 的 Interface Builder 中打开窗口设计：

[![](table-view-images/edit02.png "Editing the UI in Xcode")](table-view-images/edit02.png#lightbox)

将 `table` 键入到**库检查器的**搜索框中，以便更轻松地查找表视图控件：

[![](table-view-images/edit03.png "Selecting a Table View from the Library")](table-view-images/edit03.png#lightbox)

将表视图拖到 "**界面编辑器**" 中的视图控制器上，使其填充视图控制器的内容区域，并将其设置为在 "**约束编辑器**" 中的窗口缩小和增长的位置：

[![](table-view-images/edit04.png "Editing constraints")](table-view-images/edit04.png#lightbox)

选择**接口层次结构**中的表视图，**属性检查器**中提供以下属性：

[![](table-view-images/edit05.png "The Attribute Inspector")](table-view-images/edit05.png#lightbox)

- **内容模式**-允许使用视图（`NSView`）或单元格（`NSCell`）显示行和列中的数据。 从 macOS 10.7 开始，应使用视图。
- **浮动组行**-如果 `true`，表视图将绘制已分组的单元格，就好像它们是浮动的。
- **列**-定义显示的列数。
- **标头**-如果 `true`，列将具有标头。
- 重新**排序**-如果 `true`，用户将能够在表中拖动列的顺序。
- **调整大小**-如果 `true`，用户将能够拖动列标题以调整列的大小。
- **列大小调整**-控制表自动调整列大小的方式。
- **突出显示**-控制在选择单元格时表使用的突出显示类型。
- **替换行**-如果 `true`，则其他行将具有不同的背景色。
- **水平网格**-选择在单元格之间水平绘制的边框类型。
- **垂直网格**-选择在单元格之间垂直绘制的边框类型。
- **网格颜色**-设置单元格边框颜色。
- **背景**-设置单元格背景色。
- **选择**-允许你控制用户如何选择表中的单元格，如下所示：
  - **多**-如果 `true`，则用户可以选择多个行和列。
  - **列**-如果 `true`，则用户可以选择列。
  - **键入 select** -如果 `true`，则用户可以键入一个字符来选择行。
  - **空**-如果 `true`，则不要求用户选择行或列，表根本不允许任何选择。
- **自动保存-表**格式将自动保存在下面的名称。
- **列信息**-如果 `true`，列的顺序和宽度将自动保存。
- **换行符**-选择单元格如何处理分行符。
- **截断最后一个可见行**-如果 `true`，则数据中的单元格将被截断，而不能容纳在它的边界内。

> [!IMPORTANT]
> 除非您维护的是旧的 Xamarin Mac 应用程序，否则基于 `NSCell` 的表视图应使用基于 `NSView` 的表视图。 `NSCell` 被视为旧的，可能不会受到支持。

选择**接口层次结构**中的表列，"**属性检查器**" 中提供以下属性：

[![](table-view-images/edit06.png "The Attribute Inspector")](table-view-images/edit06.png#lightbox)

- **标题**-设置列的标题。
- **对齐方式**-设置单元格中的文本对齐方式。
- **标题字体**-选择单元格标题文本的字体。
- **排序键**-用于对列中的数据进行排序的键。 如果用户不能对此列进行排序，则保留为空。
- **选择器**-用于执行排序的**操作**。 如果用户不能对此列进行排序，则保留为空。
- **Order** -列数据的排序顺序。
- **调整大小**-选择列的调整大小类型。
- **可编辑**-如果 `true`，用户可以在基于单元格的表中编辑单元格。
- **Hidden** -如果 `true`，则隐藏列。

还可以通过将列的控点拖到左侧或右侧，来调整列的大小。

让我们选择表视图中的每一列，并为第一列指定**标题**`Product` 第二列 `Details`。

在**接口层次结构**中选择一个表单元视图（`NSTableViewCell`），**属性检查器**中提供以下属性：

[![](table-view-images/edit07.png "The Attribute Inspector")](table-view-images/edit07.png#lightbox)

这些是标准视图的所有属性。 你还可以选择在此处调整此列的行的大小。

选择表视图单元（默认情况下，这是 `NSTextField`）在**接口层次结构**中，**特性检查器**中提供以下属性：

[![](table-view-images/edit08.png "The Attribute Inspector")](table-view-images/edit08.png#lightbox)

你将在此处设置标准文本字段的所有属性。 默认情况下，标准文本字段用于显示列中单元格的数据。

在**接口层次结构**中选择一个表单元视图（`NSTableFieldCell`），**属性检查器**中提供以下属性：

[![](table-view-images/edit09.png "The Attribute Inspector")](table-view-images/edit09.png#lightbox)

最重要的设置如下：

- **布局**-选择此列中的单元格的布局方式。
- **使用单行模式**-如果 `true`，则单元格限制为单独的一行。
- **第一次运行时布局宽度**-如果 `true`，则首次运行应用程序时，该单元格将更喜欢它的宽度设置（手动或自动）。
- **操作**-控制向单元格发送编辑**操作**的时间。
- **行为**-定义单元是否可选择或可编辑。
- **格式文本**-如果 `true`，则单元格可以显示已设置格式的文本。
- **撤消**-如果 `true`，单元将承担其撤消行为的责任。

选择**接口层次结构**中表列底部的表单元视图（`NSTableFieldCell`）：

[![](table-view-images/edit10.png "Selecting the Table Cell View")](table-view-images/edit10.png#lightbox)

这允许您编辑作为给定列创建的所有单元格的基本_模式_的表单元视图。

<a name="Adding_Actions_and_Outlets" />

### <a name="adding-actions-and-outlets"></a>添加操作和插座

就像任何其他 Cocoa UI 控件一样，我们需要将表视图及其列和单元格公开给使用C# **操作**和**插座**进行编码（基于所需的功能）。

对于要公开的任何表视图元素，此过程都是相同的：

1. 切换到 "**助手编辑器**" 并确保选中 `ViewController.h` 文件： 

    [![](table-view-images/edit11.png "The Assistant Editor")](table-view-images/edit11.png#lightbox)
2. 从**接口层次结构**中选择表视图，然后单击并拖动到 `ViewController.h` 文件中。
3. 为名为 `ProductTable`的表视图创建**输出口**： 

    [![](table-view-images/edit13.png "Configuring an Outlet")](table-view-images/edit13.png#lightbox)
4. 为表列创建**插座**，并将其称为 `ProductColumn` 和 `DetailsColumn`： 

    [![](table-view-images/edit14.png "Configuring an Outlet")](table-view-images/edit14.png#lightbox)
5. 保存更改并返回到 Visual Studio for Mac 以与 Xcode 同步。

接下来，我们将编写代码，以便在运行应用程序时显示表的一些数据。

<a name="Populating_the_Table_View" />

## <a name="populating-the-table-view"></a>填充表视图

使用在 Interface Builder 中设计的表视图并通过**插座**公开后，接下来需要创建C#代码来填充它。

首先，让我们创建一个新的 `Product` 类来保存各个行的信息。 在**解决方案资源管理器**中，右键单击项目，然后选择 "**添加** > **新文件 ...** "选择 "**常规**" > **空类**"，在"**名称**"中输入 `Product`，然后单击"**新建**"按钮：

[![](table-view-images/populate01.png "Creating an empty class")](table-view-images/populate01.png#lightbox)

使 `Product.cs` 文件如下所示：

```csharp
using System;

namespace MacTables
{
  public class Product
  {
    #region Computed Properties
    public string Title { get; set;} = "";
    public string Description { get; set;} = "";
    #endregion

    #region Constructors
    public Product ()
    {
    }

    public Product (string title, string description)
    {
      this.Title = title;
      this.Description = description;
    }
    #endregion
  }
}

```

接下来，我们需要创建一个 `NSTableDataSource` 的子类，以便在请求表时为其提供数据。 在**解决方案资源管理器**中，右键单击项目，然后选择 "**添加** > **新文件 ...** "选择 "**常规**" > **空类**"，在"**名称**"中输入 `ProductTableDataSource`，然后单击"**新建**"按钮。

编辑 `ProductTableDataSource.cs` 文件，使其类似于以下内容：

```csharp
using System;
using AppKit;
using CoreGraphics;
using Foundation;
using System.Collections;
using System.Collections.Generic;

namespace MacTables
{
  public class ProductTableDataSource : NSTableViewDataSource
  {
    #region Public Variables
    public List<Product> Products = new List<Product>();
    #endregion

    #region Constructors
    public ProductTableDataSource ()
    {
    }
    #endregion

    #region Override Methods
    public override nint GetRowCount (NSTableView tableView)
    {
      return Products.Count;
    }
    #endregion
  }
}

```

此类具有表视图项的存储，并覆盖 `GetRowCount` 以返回表中的行数。

最后，我们需要创建 `NSTableDelegate` 的子类，以提供表的行为。 在**解决方案资源管理器**中，右键单击项目，然后选择 "**添加** > **新文件 ...** "选择 "**常规**" > **空类**"，在"**名称**"中输入 `ProductTableDelegate`，然后单击"**新建**"按钮。

编辑 `ProductTableDelegate.cs` 文件，使其类似于以下内容：

```csharp
using System;
using AppKit;
using CoreGraphics;
using Foundation;
using System.Collections;
using System.Collections.Generic;

namespace MacTables
{
  public class ProductTableDelegate: NSTableViewDelegate
  {
    #region Constants 
    private const string CellIdentifier = "ProdCell";
    #endregion

    #region Private Variables
    private ProductTableDataSource DataSource;
    #endregion

    #region Constructors
    public ProductTableDelegate (ProductTableDataSource datasource)
    {
      this.DataSource = datasource;
    }
    #endregion

    #region Override Methods
    public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
    {
      // This pattern allows you reuse existing views when they are no-longer in use.
      // If the returned view is null, you instance up a new view
      // If a non-null view is returned, you modify it enough to reflect the new data
      NSTextField view = (NSTextField)tableView.MakeView (CellIdentifier, this);
      if (view == null) {
        view = new NSTextField ();
        view.Identifier = CellIdentifier;
        view.BackgroundColor = NSColor.Clear;
        view.Bordered = false;
        view.Selectable = false;
        view.Editable = false;
      }

      // Setup view based on the column selected
      switch (tableColumn.Title) {
      case "Product":
        view.StringValue = DataSource.Products [(int)row].Title;
        break;
      case "Details":
        view.StringValue = DataSource.Products [(int)row].Description;
        break;
      }

      return view;
    }
    #endregion
  }
}
```

当我们创建 `ProductTableDelegate`的实例时，我们还会传入提供表的数据的 `ProductTableDataSource` 的实例。 `GetViewForItem` 方法负责返回一个视图（数据），以显示给定列和行的单元格。 如果可能，如果不创建新视图，则将重复使用现有视图来显示该单元。

若要填充表，请编辑 `ViewController.cs` 文件，使 `AwakeFromNib` 方法如下所示：

```csharp
public override void AwakeFromNib ()
{
  base.AwakeFromNib ();

  // Create the Product Table Data Source and populate it
  var DataSource = new ProductTableDataSource ();
  DataSource.Products.Add (new Product ("Xamarin.iOS", "Allows you to develop native iOS Applications in C#"));
  DataSource.Products.Add (new Product ("Xamarin.Android", "Allows you to develop native Android Applications in C#"));
  DataSource.Products.Add (new Product ("Xamarin.Mac", "Allows you to develop Mac native Applications in C#"));

  // Populate the Product Table
  ProductTable.DataSource = DataSource;
  ProductTable.Delegate = new ProductTableDelegate (DataSource);
}
```

如果运行该应用程序，将显示以下内容：

[![](table-view-images/populate02.png "A sample app run")](table-view-images/populate02.png#lightbox)

<a name="Sorting_by_Column" />

## <a name="sorting-by-column"></a>按列排序

允许用户通过单击列标题来对表中的数据进行排序。 首先，双击 `Main.storyboard` 文件以将其打开，以便在 Interface Builder 中进行编辑。 选择 "`Product`" 列，输入 "**排序关键字**" `Title`，为**选择器**`compare:`，然后选择**订单**`Ascending`：

[![](table-view-images/sort01.png "Setting the sort key")](table-view-images/sort01.png#lightbox)

选择 "`Details`" 列，输入 "**排序关键字**" `Description`，为**选择器**`compare:`，然后选择**订单**`Ascending`：

[![](table-view-images/sort02.png "Setting the sort key")](table-view-images/sort02.png#lightbox)

保存更改并返回到 Visual Studio for Mac 以与 Xcode 同步。

现在，让我们编辑 `ProductTableDataSource.cs` 文件，并添加以下方法：

```csharp
public void Sort(string key, bool ascending) {

  // Take action based on key
  switch (key) {
  case "Title":
    if (ascending) {
      Products.Sort ((x, y) => x.Title.CompareTo (y.Title));
    } else {
      Products.Sort ((x, y) => -1 * x.Title.CompareTo (y.Title));
    }
    break;
  case "Description":
    if (ascending) {
      Products.Sort ((x, y) => x.Description.CompareTo (y.Description));
    } else {
      Products.Sort ((x, y) => -1 * x.Description.CompareTo (y.Description));
    }
    break;
  }

}

public override void SortDescriptorsChanged (NSTableView tableView, NSSortDescriptor[] oldDescriptors)
{
  // Sort the data
  if (oldDescriptors.Length > 0) {
    // Update sort
    Sort (oldDescriptors [0].Key, oldDescriptors [0].Ascending);
  } else {
    // Grab current descriptors and update sort
    NSSortDescriptor[] tbSort = tableView.SortDescriptors; 
    Sort (tbSort[0].Key, tbSort[0].Ascending); 
  }
      
  // Refresh table
  tableView.ReloadData ();
}
```

使用 `Sort` 方法，我们可以基于给定 `Product` 类字段按升序或降序对数据源中的数据进行排序。 每次单击列标题时，都将调用重写的 `SortDescriptorsChanged` 方法。 它将被传递到 Interface Builder 中设置的**键值**和该列的排序顺序。

如果运行应用程序，并单击列标题中的行，则将按该列对行进行排序：

[![](table-view-images/sort03.png "An example app run")](table-view-images/sort03.png#lightbox)

<a name="Row_Selection" />

## <a name="row-selection"></a>行选择

如果要允许用户选择单个行，请双击 `Main.storyboard` 文件以将其打开，以便在 Interface Builder 中进行编辑。 选择**接口层次结构**中的表视图，并取消选中 "**属性检查器**" 中的 "**多个**" 复选框：

[![](table-view-images/select01.png "The Attribute Inspector")](table-view-images/select01.png#lightbox)

保存更改并返回到 Visual Studio for Mac 以与 Xcode 同步。

接下来，编辑 `ProductTableDelegate.cs` 文件并添加以下方法：

```csharp
public override bool ShouldSelectRow (NSTableView tableView, nint row)
{
  return true;
}
```

这将允许用户在表视图中选择任何单个行。 如果你不希望用户能够选择任何行，则为你不希望用户能够为每行选择或 `false` 的任何行返回 `ShouldSelectRow` `false`。

表格视图（`NSTableView`）包含以下用于处理行选择的方法：

- `DeselectRow(nint)` 在表中取消选择给定行。
- `SelectRow(nint,bool)`-选择给定行。 将第二个参数的 `false` 传递给一次只选择一行。
- `SelectedRow`-返回在表中选择的当前行。
- `IsRowSelected(nint)`-如果选择了给定行，则返回 `true`。

<a name="Multiple_Row_Selection" />

## <a name="multiple-row-selection"></a>多行选择

如果要允许用户选择多个行，请双击 `Main.storyboard` 文件以将其打开，以便在 Interface Builder 中进行编辑。 选择**接口层次结构**中的表视图，并选中 "**属性检查器**" 中的 "**多个**" 复选框：

[![](table-view-images/select02.png "The Attribute Inspector")](table-view-images/select02.png#lightbox)

保存更改并返回到 Visual Studio for Mac 以与 Xcode 同步。

接下来，编辑 `ProductTableDelegate.cs` 文件并添加以下方法：

```csharp
public override bool ShouldSelectRow (NSTableView tableView, nint row)
{
  return true;
}
```

这将允许用户在表视图中选择任何单个行。 如果你不希望用户能够选择任何行，则为你不希望用户能够为每行选择或 `false` 的任何行返回 `ShouldSelectRow` `false`。

表格视图（`NSTableView`）包含以下用于处理行选择的方法：

- `DeselectAll(NSObject)`-取消选择表中的所有行。 使用 `this` 在执行选择的对象中发送的第一个参数。 
- `DeselectRow(nint)` 在表中取消选择给定行。
- `SelectAll(NSobject)`-选择表中的所有行。 使用 `this` 在执行选择的对象中发送的第一个参数。
- `SelectRow(nint,bool)`-选择给定行。 为第二个参数传递 `false` 清除所选内容并仅选择单个行，传递 `true` 以扩展选定内容并包含该行。
- `SelectRows(NSIndexSet,bool)`-选择给定的行集。 为第二个参数传递 `false` 清除所选内容并仅选择这些行，传递 `true` 以扩展选定内容并包含这些行。
- `SelectedRow`-返回在表中选择的当前行。
- `SelectedRows`-返回一个 `NSIndexSet`，其中包含所选行的索引。
- `SelectedRowCount`-返回选定行的数目。
- `IsRowSelected(nint)`-如果选择了给定行，则返回 `true`。

<a name="Type_to_Select_Row" />

## <a name="type-to-select-row"></a>键入以选择行

如果要允许用户在选中表视图的情况下键入字符并选择包含该字符的第一行，请双击 `Main.storyboard` 文件以将其打开，以便在 Interface Builder 中进行编辑。 选择**接口层次结构**中的表视图，并选中 "**属性检查器**" 中的 "**类型选择**" 复选框：

[![](table-view-images/type01.png "Setting the selection type")](table-view-images/type01.png#lightbox)

保存更改并返回到 Visual Studio for Mac 以与 Xcode 同步。

现在，让我们编辑 `ProductTableDelegate.cs` 文件，并添加以下方法：

```csharp
public override nint GetNextTypeSelectMatch (NSTableView tableView, nint startRow, nint endRow, string searchString)
{
  nint row = 0;
  foreach(Product product in DataSource.Products) {
    if (product.Title.Contains(searchString)) return row;

    // Increment row counter
    ++row;
  }

  // If not found select the first row
  return 0;
}
```

`GetNextTypeSelectMatch` 方法获取给定的 `searchString` 并返回其 `Title`中包含该字符串的第一个 `Product` 的行。

如果运行应用程序并键入一个字符，则将选择一行：

[![](table-view-images/type02.png "A sample app run")](table-view-images/type02.png#lightbox)

<a name="Reordering_Columns" />

## <a name="reordering-columns"></a>重新排序列

如果要允许用户在表视图中拖动重新排序列，请双击 `Main.storyboard` 文件以将其打开，以便在 Interface Builder 中进行编辑。 选择**接口层次结构**中的表视图，并选中 "**属性检查器**" 中的 "重新**排序**" 复选框：

[![](table-view-images/reorder01.png "The Attribute Inspector")](table-view-images/reorder01.png#lightbox)

如果我们为 "**自动保存**" 属性提供一个值，并选中 "**列信息**" 字段，则对该表的布局所做的任何更改都将自动保存，并在下次运行应用程序时还原。

保存更改并返回到 Visual Studio for Mac 以与 Xcode 同步。

现在，让我们编辑 `ProductTableDelegate.cs` 文件，并添加以下方法：

```csharp
public override bool ShouldReorder (NSTableView tableView, nint columnIndex, nint newColumnIndex)
{
  return true;
}
```

`ShouldReorder` 方法应为要允许拖放到 `newColumnIndex`中的任何列返回 `true`，否则返回 `false`;

如果运行应用程序，我们可以拖动列标题以对列进行重新排序：

[![](table-view-images/reorder02.png "An example of the reordered columns")](table-view-images/reorder02.png#lightbox)

<a name="Editing_Cells" />

## <a name="editing-cells"></a>编辑单元格

如果希望允许用户编辑给定单元的值，请编辑 `ProductTableDelegate.cs` 文件，并按如下所示更改 `GetViewForItem` 方法：

```csharp
public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
{
  // This pattern allows you reuse existing views when they are no-longer in use.
  // If the returned view is null, you instance up a new view
  // If a non-null view is returned, you modify it enough to reflect the new data
  NSTextField view = (NSTextField)tableView.MakeView (tableColumn.Title, this);
  if (view == null) {
    view = new NSTextField ();
    view.Identifier = tableColumn.Title;
    view.BackgroundColor = NSColor.Clear;
    view.Bordered = false;
    view.Selectable = false;
    view.Editable = true;

    view.EditingEnded += (sender, e) => {
          
      // Take action based on type
      switch(view.Identifier) {
      case "Product":
        DataSource.Products [(int)view.Tag].Title = view.StringValue;
        break;
      case "Details":
        DataSource.Products [(int)view.Tag].Description = view.StringValue;
        break; 
      }
    };
  }

  // Tag view
  view.Tag = row;

  // Setup view based on the column selected
  switch (tableColumn.Title) {
  case "Product":
    view.StringValue = DataSource.Products [(int)row].Title;
    break;
  case "Details":
    view.StringValue = DataSource.Products [(int)row].Description;
    break;
  }

  return view;
}
```

现在，如果我们运行应用程序，则用户可以在表视图中编辑单元：

[![](table-view-images/editing01.png "An example of editing a cell")](table-view-images/editing01.png#lightbox)

<a name="Using_Images_in_Table_Views" />

## <a name="using-images-in-table-views"></a>在表视图中使用图像

若要在 `NSTableView` 中包括图像作为单元的一部分，您需要更改表视图的 `NSTableViewDelegate's` `GetViewForItem` 方法返回数据的方式，以使用 `NSTableCellView` 而不是典型的 `NSTextField`。 例如:

```csharp
public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
{

  // This pattern allows you reuse existing views when they are no-longer in use.
  // If the returned view is null, you instance up a new view
  // If a non-null view is returned, you modify it enough to reflect the new data
  NSTableCellView view = (NSTableCellView)tableView.MakeView (tableColumn.Title, this);
  if (view == null) {
    view = new NSTableCellView ();
    if (tableColumn.Title == "Product") {
      view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
      view.AddSubview (view.ImageView);
      view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
    } else {
      view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
    }
    view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
    view.AddSubview (view.TextField);
    view.Identifier = tableColumn.Title;
    view.TextField.BackgroundColor = NSColor.Clear;
    view.TextField.Bordered = false;
    view.TextField.Selectable = false;
    view.TextField.Editable = true;

    view.TextField.EditingEnded += (sender, e) => {

      // Take action based on type
      switch(view.Identifier) {
      case "Product":
        DataSource.Products [(int)view.TextField.Tag].Title = view.TextField.StringValue;
        break;
      case "Details":
        DataSource.Products [(int)view.TextField.Tag].Description = view.TextField.StringValue;
        break; 
      }
    };
  }

  // Tag view
  view.TextField.Tag = row;

  // Setup view based on the column selected
  switch (tableColumn.Title) {
  case "Product":
    view.ImageView.Image = NSImage.ImageNamed ("tags.png");
    view.TextField.StringValue = DataSource.Products [(int)row].Title;
    break;
  case "Details":
    view.TextField.StringValue = DataSource.Products [(int)row].Description;
    break;
  }

  return view;
}
```

有关详细信息，请参阅使用[图像](~/mac/app-fundamentals/image.md)文档中的[使用映像和表视图](~/mac/app-fundamentals/image.md)部分。

<a name="Adding-a-Delete-Button-to-a-Row" />

## <a name="adding-a-delete-button-to-a-row"></a>向行中添加 "删除" 按钮

根据应用程序的要求，在某些情况下，您可能需要为表中的每一行提供一个操作按钮。 作为此示例的示例，让我们展开上面创建的表视图示例，在每一行上包含一个 "**删除**" 按钮。

首先，在 Xcode 的 Interface Builder 中编辑 `Main.storyboard`，选择表视图并将列数增加到三（3）。 接下来，将新列的**标题**更改为 `Action`：

[![](table-view-images/delete01.png "Editing the column name")](table-view-images/delete01.png#lightbox)

保存对情节提要所做的更改并返回到 Visual Studio for Mac 以同步更改。

接下来，编辑 `ViewController.cs` 文件并添加以下公共方法：

```csharp
public void ReloadTable ()
{
  ProductTable.ReloadData ();
}
```

在同一文件中，按如下所示修改在 `ViewDidLoad` 方法中创建新的表视图委托：

```csharp
// Populate the Product Table
ProductTable.DataSource = DataSource;
ProductTable.Delegate = new ProductTableDelegate (this, DataSource);
```

现在，编辑 `ProductTableDelegate.cs` 文件，使其包含与视图控制器的专用连接，并在创建新的委托实例时将控制器作为参数：

```csharp
#region Private Variables
private ProductTableDataSource DataSource;
private ViewController Controller;
#endregion

#region Constructors
public ProductTableDelegate (ViewController controller, ProductTableDataSource datasource)
{
  this.Controller = controller;
  this.DataSource = datasource;
}
#endregion
```

接下来，将以下新的私有方法添加到类：

```csharp
private void ConfigureTextField (NSTableCellView view, nint row)
{
  // Add to view
  view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
  view.AddSubview (view.TextField);

  // Configure
  view.TextField.BackgroundColor = NSColor.Clear;
  view.TextField.Bordered = false;
  view.TextField.Selectable = false;
  view.TextField.Editable = true;

  // Wireup events
  view.TextField.EditingEnded += (sender, e) => {

    // Take action based on type
    switch (view.Identifier) {
    case "Product":
      DataSource.Products [(int)view.TextField.Tag].Title = view.TextField.StringValue;
      break;
    case "Details":
      DataSource.Products [(int)view.TextField.Tag].Description = view.TextField.StringValue;
      break;
    }
  };

  // Tag view
  view.TextField.Tag = row;
}
```

这会将以前在 `GetViewForItem` 方法中完成的所有文本视图配置都置于一个可调用位置（因为表的最后一列不包含文本视图，而是一个按钮）。

最后，编辑 `GetViewForItem` 方法，使其类似于以下内容：

```csharp
public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
{

  // This pattern allows you reuse existing views when they are no-longer in use.
  // If the returned view is null, you instance up a new view
  // If a non-null view is returned, you modify it enough to reflect the new data
  NSTableCellView view = (NSTableCellView)tableView.MakeView (tableColumn.Title, this);
  if (view == null) {
    view = new NSTableCellView ();

    // Configure the view
    view.Identifier = tableColumn.Title;

    // Take action based on title
    switch (tableColumn.Title) {
    case "Product":
      view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
      view.AddSubview (view.ImageView);
      view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
      ConfigureTextField (view, row);
      break;
    case "Details":
      view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
      ConfigureTextField (view, row);
      break;
    case "Action":
      // Create new button
      var button = new NSButton (new CGRect (0, 0, 81, 16));
      button.SetButtonType (NSButtonType.MomentaryPushIn);
      button.Title = "Delete";
      button.Tag = row;

      // Wireup events
      button.Activated += (sender, e) => {
        // Get button and product
        var btn = sender as NSButton;
        var product = DataSource.Products [(int)btn.Tag];

        // Configure alert
        var alert = new NSAlert () {
          AlertStyle = NSAlertStyle.Informational,
          InformativeText = $"Are you sure you want to delete {product.Title}? This operation cannot be undone.",
          MessageText = $"Delete {product.Title}?",
        };
        alert.AddButton ("Cancel");
        alert.AddButton ("Delete");
        alert.BeginSheetForResponse (Controller.View.Window, (result) => {
          // Should we delete the requested row?
          if (result == 1001) {
            // Remove the given row from the dataset
            DataSource.Products.RemoveAt((int)btn.Tag);
            Controller.ReloadTable ();
          }
        });
      };

      // Add to view
      view.AddSubview (button);
      break;
    }

  }

  // Setup view based on the column selected
  switch (tableColumn.Title) {
  case "Product":
    view.ImageView.Image = NSImage.ImageNamed ("tag.png");
    view.TextField.StringValue = DataSource.Products [(int)row].Title;
    view.TextField.Tag = row;
    break;
  case "Details":
    view.TextField.StringValue = DataSource.Products [(int)row].Description;
    view.TextField.Tag = row;
    break;
  case "Action":
    foreach (NSView subview in view.Subviews) {
      var btn = subview as NSButton;
      if (btn != null) {
        btn.Tag = row;
      }
    }
    break;
  }

  return view;
}
```

让我们更详细地了解此代码的多个部分。 首先，如果正在创建新的 `NSTableViewCell`，则会基于列名称执行操作。 对于前两个列（**产品**和**详细信息**），将调用新的 `ConfigureTextField` 方法。

对于 "**操作**" 列，将创建一个新的 `NSButton`，并将其作为子视图添加到单元格中：

```csharp
// Create new button
var button = new NSButton (new CGRect (0, 0, 81, 16));
button.SetButtonType (NSButtonType.MomentaryPushIn);
button.Title = "Delete";
button.Tag = row;
...

// Add to view
view.AddSubview (button);
```

按钮的 `Tag` 属性用于保存当前正在处理的行的编号。 以后当用户请求在按钮的 `Activated` 事件中删除某行时，将使用此数字：

```csharp
// Wireup events
button.Activated += (sender, e) => {
  // Get button and product
  var btn = sender as NSButton;
  var product = DataSource.Products [(int)btn.Tag];

  // Configure alert
  var alert = new NSAlert () {
    AlertStyle = NSAlertStyle.Informational,
    InformativeText = $"Are you sure you want to delete {product.Title}? This operation cannot be undone.",
    MessageText = $"Delete {product.Title}?",
  };
  alert.AddButton ("Cancel");
  alert.AddButton ("Delete");
  alert.BeginSheetForResponse (Controller.View.Window, (result) => {
    // Should we delete the requested row?
    if (result == 1001) {
      // Remove the given row from the dataset
      DataSource.Products.RemoveAt((int)btn.Tag);
      Controller.ReloadTable ();
    }
  });
};
```

在事件处理程序开始时，将获得给定表行上的按钮和产品。 然后向用户显示警报，确认行删除。 如果用户选择删除该行，则从数据源中删除给定行，并重新加载该表：

```csharp
// Remove the given row from the dataset
DataSource.Products.RemoveAt((int)btn.Tag);
Controller.ReloadTable ();
```

最后，如果正在重用表视图单元，而不是创建新的，则以下代码将基于正在处理的列对其进行配置：

```csharp
// Setup view based on the column selected
switch (tableColumn.Title) {
case "Product":
  view.ImageView.Image = NSImage.ImageNamed ("tag.png");
  view.TextField.StringValue = DataSource.Products [(int)row].Title;
  view.TextField.Tag = row;
  break;
case "Details":
  view.TextField.StringValue = DataSource.Products [(int)row].Description;
  view.TextField.Tag = row;
  break;
case "Action":
  foreach (NSView subview in view.Subviews) {
    var btn = subview as NSButton;
    if (btn != null) {
      btn.Tag = row;
    }
  }
  break;
}

```

对于 "**操作**" 列，将扫描所有子视图，直到找到 `NSButton` 为止，然后更新 `Tag` 属性，使其指向当前行。

进行这些更改后，在运行应用时，每行都有一个 "**删除**" 按钮：

[![](table-view-images/delete02.png "The table view with deletion buttons")](table-view-images/delete02.png#lightbox)

当用户单击 "**删除**" 按钮时，将显示一个警报，要求他们删除给定行：

[![](table-view-images/delete03.png "A delete row alert")](table-view-images/delete03.png#lightbox)

如果用户选择 "删除"，则将删除行并重新绘制该表：

[![](table-view-images/delete04.png "The table after the row is deleted")](table-view-images/delete04.png#lightbox)

<a name="Data_Binding_Table_Views" />

## <a name="data-binding-table-views"></a>数据绑定表视图

通过在 Xamarin 应用程序中使用键/值编码和数据绑定技术，可以极大地减少需要编写和维护的代码量，以填充和处理 UI 元素。 您还可以从您的前端用户界面（_模型-视图-控制器_）进一步分离您的备份数据（_数据模型_），从而更易于维护，更灵活的应用程序设计。

键-值编码（KVC）是一种用于间接访问对象属性的机制，使用键（特殊格式的字符串）来标识属性，而不是通过实例变量或访问器方法（`get/set`）来访问这些属性。 通过在 Xamarin 应用程序中实现符合键值的代码访问器，你可以访问其他 macOS 的功能，例如键-值观察（KVO）、数据绑定、核心数据、Cocoa 绑定和 scriptability。

有关详细信息，请参阅[数据绑定和键/值编码](~/mac/app-fundamentals/databinding.md)文档中的[表视图数据绑定](~/mac/app-fundamentals/databinding.md#Table_View_Data_Binding)部分。

<a name="Summary" />

## <a name="summary"></a>总结

本文详细介绍了如何在 Xamarin. Mac 应用程序中使用表视图。 我们看到了不同的表视图类型和用法，如何在 Xcode 的 Interface Builder 中创建和维护表视图，以及如何在代码中C#使用表视图。

## <a name="related-links"></a>相关链接

- [MacTables （示例）](https://docs.microsoft.com/samples/xamarin/mac-samples/mactables)
- [MacImages（示例）](https://docs.microsoft.com/samples/xamarin/mac-samples/macimages)
- [了解 Mac](~/mac/get-started/hello-mac.md)
- [大纲视图](~/mac/user-interface/outline-view.md)
- [源列表](~/mac/user-interface/source-list.md)
- [数据绑定和键值编码](~/mac/app-fundamentals/databinding.md)
- [OS X 人机界面指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [NSTableView](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSTableView_Class/index.html#//apple_ref/doc/uid/TP40004125)
- [NSTableViewDelegate](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/NSTableViewDelegate_Protocol/index.html#//apple_ref/doc/uid/TP40008622)
- [NSTableViewDataSource](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Protocols/NSTableDataSource_Protocol/index.html#//apple_ref/doc/uid/TP40004178)
