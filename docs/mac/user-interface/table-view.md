---
title: 在 Xamarin.Mac 中的表视图
description: 本文介绍如何使用 Xamarin.Mac 应用程序中的表视图。 它描述了在 Xcode 和 Interface Builder 和在代码中与它们交互中的创建表视图。
ms.prod: xamarin
ms.assetid: 3B55B858-4769-4331-966A-7F53B3B7C720
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: c6d7971c8418c8038f5fcbfcf0e8f2acd928edcb
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61042364"
---
# <a name="table-views-in-xamarinmac"></a>在 Xamarin.Mac 中的表视图

_本文介绍如何使用 Xamarin.Mac 应用程序中的表视图。它描述了在 Xcode 和 Interface Builder 和在代码中与它们交互中的创建表视图。_

当使用C#和.NET 中的 Xamarin.Mac 应用程序中，您可以访问同一个表视图，使用的开发人员*Objective C*和*Xcode* does。 由于 Xamarin.Mac 与 Xcode 直接集成，可以使用 Xcode 的_Interface Builder_创建和维护表视图 （或选择通过 C# 代码中直接创建）。

表视图中包含一个或多个列的多个行中的信息以表格格式显示数据。 根据要创建的表视图的类型，用户可以按列排序、 重新组织列、 添加列、 删除列或编辑表中包含的数据。

[![](table-view-images/intro01.png "示例表")](table-view-images/intro01.png#lightbox)

在本文中，我们将介绍 Xamarin.Mac 应用程序中使用表视图的基础知识。 强烈建议您明确[Hello，Mac](~/mac/get-started/hello-mac.md)文章第一次，具体而言[Xcode 和 Interface Builder 简介](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder)并[输出口和操作](~/mac/get-started/hello-mac.md#outlets-and-actions)部分中的，因为它介绍了关键概念和技术，我们将在本文中使用。

可能想要看一看[公开 C# 类 / 方法添加到 Objective C](~/mac/internals/how-it-works.md)一部分[Xamarin.Mac 内部机制](~/mac/internals/how-it-works.md)文档，它还说明了`Register`和`Export`命令用于布置于 C# 类对 OBJECTIVE-C 对象和 UI 元素。

<a name="Introduction_to_Table_Views" />

## <a name="introduction-to-table-views"></a>表视图简介

表视图中包含一个或多个列的多个行中的信息以表格格式显示数据。 表视图会显示在滚动视图 (`NSScrollView`) 并且从 macOS 10.7 开始，你可以使用任何`NSView`而不是单元格 (`NSCell`) 以显示行和列。 话虽如此，仍可以使用`NSCell`但是，你通常将子类`NSTableCellView`并创建你的自定义行和列。

表视图不会存储其自己的数据，而是依赖数据源 (`NSTableViewDataSource`) 提供的行和按需基于要求的列。

可以通过提供的表视图委托子类自定义表视图的行为 (`NSTableViewDelegate`) 以支持表列管理，键入要选择功能、 行选择和编辑、 自定义跟踪和为各列的自定义视图和行。

创建表视图时，Apple 建议遵照以下：

* 允许用户通过单击列标题排序表。
* 创建列标题的名词或描述该列中显示的数据的简短名词短语。

有关详细信息，请参阅[内容视图](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsView.html#//apple_ref/doc/uid/20000957-CH52-SW1)Apple 的一部分[OS X 人机界面指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)。

<a name="Creating-and-Maintaining-Table-Views-in-Xcode" />

## <a name="creating-and-maintaining-table-views-in-xcode"></a>创建和维护在 Xcode 中的表视图

在创建新的 Xamarin.Mac 的 Cocoa 应用程序时，默认情况下获得标准保留为空，窗口。 在中定义此 windows`.storyboard`自动包含在项目中的文件。 若要编辑您的 windows 设计、 在**解决方案资源管理器**，双击`Main.storyboard`文件：

[![](table-view-images/edit01.png "选择主要情节提要")](table-view-images/edit01.png#lightbox)

这将在 Xcode 的 Interface Builder 中打开的窗口设计：

[![](table-view-images/edit02.png "编辑在 Xcode 中的 UI")](table-view-images/edit02.png#lightbox)

类型`table`成**库检查器**搜索框，可以更轻松地找到表视图控件：

[![](table-view-images/edit03.png "从库中选择表视图")](table-view-images/edit03.png#lightbox)

将表视图拖动到中的视图控制器上**界面编辑器**，使其填充内容区域的视图控制器，并将其设置为其中缩小和中的窗口都在增加**约束编辑器**:

[![](table-view-images/edit04.png "编辑约束")](table-view-images/edit04.png#lightbox)

选择中的表视图**界面层次结构**和以下属性可用于在**属性检查器**:

[![](table-view-images/edit05.png "属性检查器")](table-view-images/edit05.png#lightbox)

- **内容模式**-可以使用任一视图 (`NSView`) 或单元格 (`NSCell`) 若要显示的行和列中的数据。 从 macOS 10.7，应使用视图。
- **浮动的行进行分组**-如果`true`，表视图将绘制分组单元格，如同它们浮动。
- **列**-定义显示的列数。
- **标头**-如果`true`，这些列将具有标题。
- **重新排序**-如果`true`，用户将能够将对表中的列重新排序。
- **重设大小**-如果`true`，用户将能够拖动列标题来调整列的大小。
- **列大小调整**-控制如何在表将自动大小的列。
- **突出显示**-当选择了某个单元时使用的突出显示的表类型的控件。
- **行的备用**-如果`true`、 永远另一行将具有不同的背景色。
- **水平网格**-选择的水平绘制单元格之间的边框类型。
- **垂直网格**-选择的垂直绘制的单元格之间的边框类型。
- **网格颜色**-设置单元格的边框颜色。
- **背景**-设置单元格背景色。
- **选择**-可用于控制如何，用户可以在与表中选择单元格：
    - **多个**-如果`true`，用户可以选择多个行和列。
    - **列**-如果`true`，用户可以选择的列。
    - **键入 Select** -如果`true`，用户可以键入要选择某一行的字符。
    - **空**-如果`true`，用户不需要选择的行或列，表进行任何选择根本。
- **自动保存**-在保存表格式是自动的名称。
- **列信息**-如果`true`的顺序和列的宽度将自动保存。
- **分行符**-选择单元格处理换行符的方式。
- **将最后一个可见行截断**-如果`true`，将不适合它的边界内的数据可以截断该单元格。

> [!IMPORTANT]
> 要保留旧的 Xamarin.Mac 应用程序，除非`NSView`应通过使用基于的表视图`NSCell`基于表视图。 `NSCell` 被视为旧版，可能不支持今后。

选择中的表列**界面层次结构**和以下属性可用于在**属性检查器**:

[![](table-view-images/edit06.png "属性检查器")](table-view-images/edit06.png#lightbox)

- **标题**-设置列的标题。
- **对齐方式**-的单元中设置文本的对齐方式。
- **标题字体**-选择单元格的标头文本的字体。
- **排序关键字**-是用于对列中的数据进行排序的键。 保留为空，如果用户不能对此列进行排序。
- **选择器**-是**操作**用于执行排序。 保留为空，如果用户不能对此列进行排序。
- **顺序**-是列数据的排序顺序。
- **重设大小**-选择调整大小的列的类型。
- **可编辑**-如果`true`，用户可以编辑单元格基于表的单元格。
- **隐藏**-如果`true`，隐藏该列。

您可以通过拖动其句柄 （垂直方向上居中列的右侧） 左或向右来调整列的大小。

让我们来选择表视图中的每个列，并为提供的第一列**标题**的`Product`将第二个`Details`。

选择一个表格单元格视图 (`NSTableViewCell`) 中**界面层次结构**和以下属性可用于在**属性检查器**:

[![](table-view-images/edit07.png "属性检查器")](table-view-images/edit07.png#lightbox)

这些是所有的标准视图的属性。 此外可以调整此列的行的大小。

选择表视图单元格 (默认情况下，这是`NSTextField`) 中**界面层次结构**和以下属性可用于在**属性检查器**:

[![](table-view-images/edit08.png "属性检查器")](table-view-images/edit08.png#lightbox)

您必须要在此处设置的标准文本字段的所有属性。 默认情况下，标准的文本字段用于在列中显示的单元格的数据。

选择一个表格单元格视图 (`NSTableFieldCell`) 中**界面层次结构**和以下属性可用于在**属性检查器**:

[![](table-view-images/edit09.png "属性检查器")](table-view-images/edit09.png#lightbox)

最重要的设置是：

- **布局**-选择此列中的单元格的布局的方式。
- **使用单个行模式**-如果`true`，该单元格被限制为单个行。
- **第一个运行时布局宽度**-如果`true`，该单元格将首选宽度为其设置 （手动或自动） 时显示首次运行该应用程序。
- **操作**-控制何时编辑**操作**发送有关单元格。
- **行为**-定义单元格是否可选或可编辑。
- **格式文本**-如果`true`，该单元格可以显示进行了格式化，带样式的文本。
- **撤消**-如果`true`，该单元格承担的责任，对它的撤消行为。

选择表单元格视图 (`NSTableFieldCell`) 中的表格列底部**界面层次结构**:

[![](table-view-images/edit10.png "选择表单元视图")](table-view-images/edit10.png#lightbox)

这使您可以编辑表单元格视图用作的基础_模式_为给定的列创建的所有单元格。

<a name="Adding_Actions_and_Outlets" />

### <a name="adding-actions-and-outlets"></a>添加操作和输出口

只需像任何其他 Cocoa UI 控件，我们需要公开表视图和列并与 C# 代码使用的单元格**操作**并**输出口**（基于所需的功能）。

该过程是我们想要公开任何表视图元素相同的：

1. 切换到**助手编辑器**并确保`ViewController.h`选择文件： 

    [![](table-view-images/edit11.png "在助手编辑器")](table-view-images/edit11.png#lightbox)
2. 选择从表视图**界面层次结构**、 单击和拖动到`ViewController.h`文件。
3. 创建**输出口**来在表视图名为`ProductTable`: 

    [![](table-view-images/edit13.png "配置输出口")](table-view-images/edit13.png#lightbox)
4. 创建**输出口**对于表列也被称为`ProductColumn`和`DetailsColumn`: 

    [![](table-view-images/edit14.png "配置输出口")](table-view-images/edit14.png#lightbox)
5. 保存更改并返回到 Visual Studio for Mac 与 Xcode 同步。

接下来，我们将编写的代码显示表的某些数据时运行该应用程序。

<a name="Populating_the_Table_View" />

## <a name="populating-the-table-view"></a>填充表视图

使用表视图在 Interface Builder 中设计和通过公开**输出口**，接下来我们需要创建的 C# 代码来填充它。

首先，让我们创建一个新`Product`类来保存单个行的信息。 在中**解决方案资源管理器**，右键单击该项目并选择**添加** > **新文件...** 选择**常规** > **空类**，输入`Product`有关**名称**然后单击**新建**按钮：

[![](table-view-images/populate01.png "创建一个空类")](table-view-images/populate01.png#lightbox)

使`Product.cs`文件外观如下所示：

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

接下来，我们需要创建一个子类`NSTableDataSource`为请求的表格提供数据。 在中**解决方案资源管理器**，右键单击该项目并选择**添加** > **新文件...** 选择**常规** > **空类**，输入`ProductTableDataSource`有关**名称**然后单击**新建**按钮。

编辑`ProductTableDataSource.cs`文件，并使其看起来如下所示：

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

此类采用我们表视图项的存储，并重写`GetRowCount`表中返回的行数。

最后，我们需要创建一个子类`NSTableDelegate`以便为我们的表格提供行为。 在中**解决方案资源管理器**，右键单击该项目并选择**添加** > **新文件...** 选择**常规** > **空类**，输入`ProductTableDelegate`有关**名称**然后单击**新建**按钮。

编辑`ProductTableDelegate.cs`文件，并使其看起来如下所示：

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

我们创建的实例时`ProductTableDelegate`，我们还传递的实例中`ProductTableDataSource`提供表的数据。 `GetViewForItem`方法负责返回视图 （数据） 以显示为列和行的单元格。 如果可能，将重复使用现有视图以显示单元格，如果不是必须创建新视图。

若要填充表，让我们编辑`ViewController.cs`文件，然后进行`AwakeFromNib`方法看起来像以下：

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

如果我们运行该应用程序，将显示以下：

[![](table-view-images/populate02.png "运行示例应用")](table-view-images/populate02.png#lightbox)

<a name="Sorting_by_Column" />

## <a name="sorting-by-column"></a>按列排序

让我们允许用户通过单击列标题对表中的数据进行排序。 首先，双击`Main.storyboard`文件以打开 Interface Builder 中为进行编辑。 选择`Product`列中，输入`Title`有关**排序键**，`compare:`有关**选择器**，然后选择`Ascending`为**顺序**:

[![](table-view-images/sort01.png "设置的排序键")](table-view-images/sort01.png#lightbox)

选择`Details`列中，输入`Description`有关**排序键**，`compare:`有关**选择器**，然后选择`Ascending`为**顺序**:

[![](table-view-images/sort02.png "设置的排序键")](table-view-images/sort02.png#lightbox)

保存所做的更改并返回到 Visual Studio for Mac 与 Xcode 同步。

现在让我们编辑`ProductTableDataSource.cs`文件，并添加以下方法：

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

`Sort`方法使我们能够对基于数据源中的数据排序给定`Product`类字段按升序或降序排序。 重写`SortDescriptorsChanged`每次使用单击列标题上时，将调用方法。 将其传递**密钥**Interface Builder 和该列的排序顺序中设置的值。

如果我们运行该应用程序，并单击列标题中，将按该列排序行：

[![](table-view-images/sort03.png "运行示例应用")](table-view-images/sort03.png#lightbox)

<a name="Row_Selection" />

## <a name="row-selection"></a>行选择

如果你想要允许用户选择的单个行中，双击`Main.storyboard`文件以打开 Interface Builder 中为进行编辑。 选择中的表视图**界面层次结构**并取消选中**多个**中的复选框**属性检查器**:

[![](table-view-images/select01.png "属性检查器")](table-view-images/select01.png#lightbox)

保存所做的更改并返回到 Visual Studio for Mac 与 Xcode 同步。


接下来，编辑`ProductTableDelegate.cs`文件，并添加以下方法：

```csharp
public override bool ShouldSelectRow (NSTableView tableView, nint row)
{
    return true;
}
```

这将允许用户在表视图中选择任何单个行。 返回`false`有关`ShouldSelectRow`对任何行，您不希望用户能够选择或`false`的每一行，如果您不希望用户能够选择任何行。

表视图 (`NSTableView`) 包含用于处理行选择以下方法：

- `DeselectRow(nint)` -取消选择表中的给定的行。
- `SelectRow(nint,bool)` -选择给定的行。 传递`false`来一次选择只包含一行的第二个参数。
- `SelectedRow` -返回表中选择的当前行。
- `IsRowSelected(nint)` -返回`true`如果选择的给定的行。

<a name="Multiple_Row_Selection" />

## <a name="multiple-row-selection"></a>多个行选择

如果你想要允许用户选择多个行中，双击`Main.storyboard`文件以打开 Interface Builder 中为进行编辑。 选择中的表视图**界面层次结构**，并检查**多个**中的复选框**属性检查器**:

[![](table-view-images/select02.png "属性检查器")](table-view-images/select02.png#lightbox)

保存所做的更改并返回到 Visual Studio for Mac 与 Xcode 同步。


接下来，编辑`ProductTableDelegate.cs`文件，并添加以下方法：

```csharp
public override bool ShouldSelectRow (NSTableView tableView, nint row)
{
    return true;
}
```

这将允许用户在表视图中选择任何单个行。 返回`false`有关`ShouldSelectRow`对任何行，您不希望用户能够选择或`false`的每一行，如果您不希望用户能够选择任何行。

表视图 (`NSTableView`) 包含用于处理行选择以下方法：

- `DeselectAll(NSObject)` -取消选择表中的所有行。 使用`this`第一个参数中进行选择的对象发送。 
- `DeselectRow(nint)` -取消选择表中的给定的行。
- `SelectAll(NSobject)` -选择表中的所有行。 使用`this`第一个参数中进行选择的对象发送。
- `SelectRow(nint,bool)` -选择给定的行。 传递`false`对于第二个参数清除所选内容并选择只有一行，传递`true`扩展所选内容并将包含此行。
- `SelectRows(NSIndexSet,bool)` -选择给定的行集。 传递`false`的第二个参数清除所选内容，并选择仅这些行，传递`true`扩展所选内容并将包含这些行。
- `SelectedRow` -返回表中选择的当前行。
- `SelectedRows` -返回`NSIndexSet`包含所选行的索引。
- `SelectedRowCount` -返回所选的行的数。
- `IsRowSelected(nint)` -返回`true`如果选择的给定的行。

<a name="Type_to_Select_Row" />

## <a name="type-to-select-row"></a>要选择的行类型

如果你想要允许用户与所选的表视图中键入字符，然后选择第一行具有该字符，则双击`Main.storyboard`文件以打开 Interface Builder 中为进行编辑。 选择中的表视图**界面层次结构**，并检查**类型选择**中的复选框**属性检查器**:

[![](table-view-images/type01.png "将所选内容类型设置")](table-view-images/type01.png#lightbox)

保存所做的更改并返回到 Visual Studio for Mac 与 Xcode 同步。

现在让我们编辑`ProductTableDelegate.cs`文件，并添加以下方法：

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

`GetNextTypeSelectMatch`方法采用给定`searchString`，并返回第一个行`Product`，该字符串中包含的`Title`。

如果我们运行该应用程序，并键入字符，则选择某一行：

[![](table-view-images/type02.png "运行示例应用")](table-view-images/type02.png#lightbox)

<a name="Reordering_Columns" />

## <a name="reordering-columns"></a>对列重新排序

如果你想要允许用户拖动重新排列列在表视图中的，双击`Main.storyboard`文件以打开 Interface Builder 中为进行编辑。 选择中的表视图**界面层次结构**，并检查**重新排序功能**中的复选框**属性检查器**:

[![](table-view-images/reorder01.png "属性检查器")](table-view-images/reorder01.png#lightbox)

如果我们提供的值**自动保存**属性并检查**的列信息**字段中，我们对表的布局进行任何更改会自动为我们保存和还原应用程序的下一次运行时。

保存所做的更改并返回到 Visual Studio for Mac 与 Xcode 同步。

现在让我们编辑`ProductTableDelegate.cs`文件，并添加以下方法：

```csharp
public override bool ShouldReorder (NSTableView tableView, nint columnIndex, nint newColumnIndex)
{
    return true;
}
```

`ShouldReorder`方法应返回`true`它想要允许为任何列拖动到重新排序`newColumnIndex`，否则返回`false`;

如果我们运行该应用程序，我们可以拖动周围的列标题对列重新排序：

[![](table-view-images/reorder02.png "下面举例说明重排序的列")](table-view-images/reorder02.png#lightbox)

<a name="Editing_Cells" />

## <a name="editing-cells"></a>编辑单元格

如果你想要允许用户编辑给定的单元格的值，请编辑`ProductTableDelegate.cs`文件，并更改`GetViewForItem`方法，如下所示：

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

现在如果我们运行该应用程序，用户可以编辑的表视图中的单元格：

[![](table-view-images/editing01.png "举例说明如何编辑单元格")](table-view-images/editing01.png#lightbox)

<a name="Using_Images_in_Table_Views" />

## <a name="using-images-in-table-views"></a>在表视图中使用映像

用于包含映像中的单元格的一部分`NSTableView`，将需要更改表视图返回的数据是如何`NSTableViewDelegate's``GetViewForItem`要使用的方法`NSTableCellView`而不是典型`NSTextField`。 例如：

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

有关详细信息，请参阅[与表视图中使用图像](~/mac/app-fundamentals/image.md)一部分我们[使用映像](~/mac/app-fundamentals/image.md)文档。

<a name="Adding-a-Delete-Button-to-a-Row" />

## <a name="adding-a-delete-button-to-a-row"></a>将删除按钮添加到行

根据您的应用程序的要求，可能有有时需要提供表中每个行的操作按钮。 作为此示例，让我们展开包含上面创建的表视图示例**删除**在每行上的按钮。

首先，编辑`Main.storyboard`在 Xcode 的 Interface Builder 中，选择表视图，增加到的列数三 （3)。 接下来，更改**标题**将新列`Action`:

[![](table-view-images/delete01.png "编辑列名称")](table-view-images/delete01.png#lightbox)

将所做的更改保存到情节提要并返回到 Visual Studio for Mac 同步所做的更改。

接下来，编辑`ViewController.cs`文件，并添加以下公共方法：

```csharp
public void ReloadTable ()
{
    ProductTable.ReloadData ();
}
```

在同一文件中，修改新的表视图委托内创建`ViewDidLoad`方法，如下所示：

```csharp
// Populate the Product Table
ProductTable.DataSource = DataSource;
ProductTable.Delegate = new ProductTableDelegate (this, DataSource);
```

现在，编辑`ProductTableDelegate.cs`文件以包括与视图控制器的专用连接并创建新的委托的实例时作为参数使控制器：

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

接下来，将以下新专用方法添加到类：

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

这考虑了所有之前已完成中的文本视图配置`GetViewForItem`方法并将它们放在一个单一的可调用位置 （因为表的最后一列不包含文本视图，但按钮）。

最后，编辑`GetViewForItem`方法，并使其如下所示：

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

让我们看一下此代码中更多详细信息的多个部分。 首先，如果新`NSTableViewCell`是正在创建操作基于执行的列的名称。 对于前两个列 (**产品**并**详细信息**)，新`ConfigureTextField`调用方法。

有关**操作**列中，新`NSButton`被创建并添加到单元格作为子视图：

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

按钮的`Tag`属性用来保存当前正在处理的行数。 当用户请求中的按钮删除行时将更高版本使用此号码`Activated`事件：

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

在事件处理程序开始时，我们获取按钮和给定的表行中的产品。 然后向用户确认删除行显示警报。 如果用户选择要删除的行，从数据源中删除给定的行，并重新加载表：

```csharp
// Remove the given row from the dataset
DataSource.Products.RemoveAt((int)btn.Tag);
Controller.ReloadTable ();
```

最后，如果重新使用而不是正在创建新表视图单元格，下面的代码将其配置基于正在处理的列：

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

有关**操作**列中，直到扫描所有子视图`NSButton`找到，则它是`Tag`属性更新为指向当前行。

与这些更改后，当应用运行时的每一行将具有**删除**按钮：

[![](table-view-images/delete02.png "具有删除按钮的表视图")](table-view-images/delete02.png#lightbox)

当用户单击**删除**按钮，将要求他们删除给定的行显示警报：

[![](table-view-images/delete03.png "删除行警报")](table-view-images/delete03.png#lightbox)

如果用户选择删除，将删除行，并将重绘表：

[![](table-view-images/delete04.png "表后删除的行")](table-view-images/delete04.png#lightbox)

<a name="Data_Binding_Table_Views" />

## <a name="data-binding-table-views"></a>数据绑定表视图

通过在 Xamarin.Mac 应用程序中使用的键-值编码和数据绑定技术，您可以极大地减少需要编写和维护来填充和使用 UI 元素的代码量。 此外可以进一步分离你的备份数据的好处 (_数据模型_) 从你前面结束用户界面 (_模型-视图-控制器_)，从而导致更易于维护，更灵活的应用程序设计。

键值对的编码 (KVC) 是一种机制间接访问对象的属性、 使用密钥 （特殊格式字符串） 来标识而不是通过实例变量访问它们的属性或访问器方法 (`get/set`)。 在 Xamarin.Mac 应用程序中实现键 / 值编码符合访问器，可以获得其他 macOS 功能，例如键-值观察 (KVO)、 数据绑定、 核心数据、 Cocoa 绑定和 scriptability 的访问权限。

有关详细信息，请参阅[表视图的数据绑定](~/mac/app-fundamentals/databinding.md#Table_View_Data_Binding)一部分我们[数据绑定和键值编码](~/mac/app-fundamentals/databinding.md)文档。

<a name="Summary" />

## <a name="summary"></a>总结

本文已在 Xamarin.Mac 应用程序中使用表视图的详细的信息。 我们看到了不同类型，并使用的表视图、 如何创建和维护 Xcode 的 Interface Builder 中的表视图以及如何在 C# 代码中使用表视图。

## <a name="related-links"></a>相关链接

- [MacTables （示例）](https://developer.xamarin.com/samples/mac/MacTables/)
- [MacImages（示例）](https://developer.xamarin.com/samples/mac/MacImages/)
- [了解 Mac](~/mac/get-started/hello-mac.md)
- [大纲视图](~/mac/user-interface/outline-view.md)
- [源列表](~/mac/user-interface/source-list.md)
- [数据绑定和键值编码](~/mac/app-fundamentals/databinding.md)
- [OS X 人机界面指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [NSTableView](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSTableView_Class/index.html#//apple_ref/doc/uid/TP40004125)
- [NSTableViewDelegate](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/NSTableViewDelegate_Protocol/index.html#//apple_ref/doc/uid/TP40008622)
- [NSTableViewDataSource](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Protocols/NSTableDataSource_Protocol/index.html#//apple_ref/doc/uid/TP40004178)
