---
title: Xamarin 中的大纲视图
description: 本文介绍如何在 Xamarin. Mac 应用程序中使用大纲视图。 它介绍了如何在 Xcode 和 Interface Builder 中创建和维护大纲视图，以及如何以编程方式使用它们。
ms.prod: xamarin
ms.assetid: 043248EE-11DA-4E96-83A3-08824A4F2E01
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: dd710f13d9324ce31e08641f214241e0433fe809
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73004334"
---
# <a name="outline-views-in-xamarinmac"></a>Xamarin 中的大纲视图

_本文介绍如何在 Xamarin. Mac 应用程序中使用大纲视图。它介绍了如何在 Xcode 和 Interface Builder 中创建和维护大纲视图，以及如何以编程方式使用它们。_

在 Xamarin 应用C# *程序中使用*和 .net 时，可以访问在*Xcode 和*中工作的开发人员所使用的相同大纲视图。 由于 Xamarin 与 Xcode 直接集成，因此可以使用 Xcode 的_Interface Builder_来创建和维护大纲视图（或者在代码中C#直接创建）。

大纲视图是一种允许用户展开或折叠分层数据行的表。 与表视图类似，大纲视图显示一组相关项的数据，行表示各个项，列表示这些项的属性。 与表视图不同，大纲视图中的项不在简单列表中，它们在层次结构中进行组织，如硬盘驱动器上的文件和文件夹。

[![](outline-view-images/populate03.png "An example app run")](outline-view-images/populate03.png#lightbox)

在本文中，我们将介绍在 Xamarin. Mac 应用程序中使用大纲视图的基础知识。 强烈建议您先完成[Hello，Mac](~/mac/get-started/hello-mac.md)一文，特别是[Xcode 和 Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder)及[输出口和操作](~/mac/get-started/hello-mac.md#outlets-and-actions)部分的简介，因为它涵盖了我们将在本文。

你可能想要查看[Xamarin](~/mac/internals/how-it-works.md)示例文档的 " C# [公开C#类/方法到目标-c](~/mac/internals/how-it-works.md) " 部分，并说明用于将类连接到目标的`Register`和`Export`命令-c对象和 UI 元素。

<a name="Introduction_to_Outline_Views" />

## <a name="introduction-to-outline-views"></a>大纲视图简介

大纲视图是一种允许用户展开或折叠分层数据行的表。 与表视图类似，大纲视图显示一组相关项的数据，行表示各个项，列表示这些项的属性。 与表视图不同，大纲视图中的项不在简单列表中，它们在层次结构中进行组织，如硬盘驱动器上的文件和文件夹。

如果大纲视图中的项包含其他项，则可以由用户展开或折叠。 可展开的项会显示一个公开三角形，该三角形在项目折叠时指向右侧，并在展开项时向下指向。 单击泄露三角会导致项目展开或折叠。

大纲视图（`NSOutlineView`）是表视图（`NSTableView`）的子类，因此从其父类继承了很多这类行为。 因此，表视图支持的许多操作（如选择行或列、通过拖动列标题重定位列等）也受大纲视图的支持。 Xamarin 应用程序可以控制这些功能，并且可以将大纲视图的参数（在代码或 Interface Builder 中）配置为允许或禁止特定操作。

大纲视图不存储它自己的数据，而是依赖于数据源（`NSOutlineViewDataSource`）根据需要提供所需的行和列。

大纲视图的行为可通过提供大纲视图委托的子类（`NSOutlineViewDelegate`）来自定义，以支持大纲列管理、键入以选择功能、行选择和编辑、自定义跟踪以及单个列的自定义视图和行。

由于大纲视图在表视图中共享了很多这种行为和功能，因此，您可能想要浏览[表视图](~/mac/user-interface/table-view.md)文档，然后再继续阅读本文。

<a name="Creating_and_Maintaining_Outline_Views_in_Xcode" />

## <a name="creating-and-maintaining-outline-views-in-xcode"></a>在 Xcode 中创建和维护大纲视图

创建新的 Xamarin Cocoa 应用程序时，默认情况下会获得一个标准空白窗口。 此窗口在项目中自动包含的 `.storyboard` 文件中定义。 若要编辑 windows 设计，请在 "**解决方案资源管理器**中，双击 `Main.storyboard` 文件：

[![](outline-view-images/edit01.png "Selecting the main storyboard")](outline-view-images/edit01.png#lightbox)

这将在 Xcode 的 Interface Builder 中打开窗口设计：

[![](outline-view-images/edit02.png "Editing the UI in Xcode")](outline-view-images/edit02.png#lightbox)

将 `outline` 键入到**库检查器的**搜索框中，以便更轻松地查找大纲视图控件：

[![](outline-view-images/edit03.png "Selecting an Outline View from the Library")](outline-view-images/edit03.png#lightbox)

将大纲视图拖到 "**界面编辑器**" 中的视图控制器上，使其填充视图控制器的内容区域，并将其设置为在 "**约束编辑器**" 中的窗口缩小和增长的位置：

[![](outline-view-images/edit04.png "Editing the constraints")](outline-view-images/edit04.png#lightbox)

选择**接口层次结构**中的大纲视图，**属性检查器**中提供以下属性：

[![](outline-view-images/edit05.png "The Attribute Inspector")](outline-view-images/edit05.png#lightbox)

- **大纲列**-显示层次结构数据的表列。
- **自动保存大纲列**-如果 `true`，大纲列将自动保存并在应用程序运行之间还原。
- **缩进**-扩展项下的列缩进量。
- **单元格后缩进**-如果 `true`，缩进标记将随单元一起缩进。
- **自动保存展开的项**-如果 `true`，项的展开/折叠状态将自动保存并在应用程序运行之间还原。
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
> 除非维护的是旧的 Xamarin Mac 应用程序，否则应在基于 `NSCell` 的表视图上使用基于 `NSView` 的大纲视图。 `NSCell` 被视为旧的，可能不会受到支持。

选择**接口层次结构**中的表列，"**属性检查器**" 中提供以下属性：

[![](outline-view-images/edit06.png "The Attribute Inspector")](outline-view-images/edit06.png#lightbox)

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

[![](outline-view-images/edit07.png "The Attribute Inspector")](outline-view-images/edit07.png#lightbox)

这些是标准视图的所有属性。 你还可以选择在此处调整此列的行的大小。

选择表视图单元（默认情况下，这是 `NSTextField`）在**接口层次结构**中，**特性检查器**中提供以下属性：

[![](outline-view-images/edit08.png "The Attribute Inspector")](outline-view-images/edit08.png#lightbox)

你将在此处设置标准文本字段的所有属性。 默认情况下，标准文本字段用于显示列中单元格的数据。

在**接口层次结构**中选择一个表单元视图（`NSTableFieldCell`），**属性检查器**中提供以下属性：

[![](outline-view-images/edit09.png "The Attribute Inspector")](outline-view-images/edit09.png#lightbox)

最重要的设置如下：

- **布局**-选择此列中的单元格的布局方式。
- **使用单行模式**-如果 `true`，则单元格限制为单独的一行。
- **第一次运行时布局宽度**-如果 `true`，则首次运行应用程序时，该单元格将更喜欢它的宽度设置（手动或自动）。
- **操作**-控制向单元格发送编辑**操作**的时间。
- **行为**-定义单元是否可选择或可编辑。
- **格式文本**-如果 `true`，则单元格可以显示已设置格式的文本。
- **撤消**-如果 `true`，单元将承担其撤消行为的责任。

选择**接口层次结构**中表列底部的表单元视图（`NSTableFieldCell`）：

[![](outline-view-images/edit11.png "Selecting the table cell view")](outline-view-images/edit10.png#lightbox)

这允许您编辑作为给定列创建的所有单元格的基本_模式_的表单元视图。

<a name="Adding_Actions_and_Outlets" />

### <a name="adding-actions-and-outlets"></a>添加操作和插座

就像任何其他 Cocoa UI 控件一样，我们需要公开大纲视图，将其列和单元格用C# **操作**和**插座**进行编码（基于所需的功能）。

对于想要公开的任何大纲视图元素，此过程是相同的：

1. 切换到 "**助手编辑器**" 并确保选中 `ViewController.h` 文件：

    [![](outline-view-images/edit11.png "Selecting the correct .h file")](outline-view-images/edit11.png#lightbox)
2. 从**接口层次结构**中选择 "大纲" 视图，然后单击并拖动到 `ViewController.h` 文件中。
3. 为大纲视图创建一个名为 `ProductOutline`的**插座**：

    [![](outline-view-images/edit13.png "Configuring an Outlet")](outline-view-images/edit13.png#lightbox)
4. 为表列创建**插座**，并将其称为 `ProductColumn` 和 `DetailsColumn`：

    [![](outline-view-images/edit14.png "Configuring an Outlet")](outline-view-images/edit14.png#lightbox)
5. 保存更改并返回到 Visual Studio for Mac 以与 Xcode 同步。

接下来，我们将编写代码，以便在应用程序运行时显示大纲的一些数据。

<a name="Populating_the_Table_View" />

## <a name="populating-the-outline-view"></a>填充大纲视图

通过 Interface Builder 中设计的大纲视图，并通过**插座**公开，接下来需要创建C#代码来填充它。

首先，让我们创建一个新的 `Product` 类来保存子产品的各个行和组的信息。 在**解决方案资源管理器**中，右键单击项目，然后选择 "**添加** > **新文件 ...** "选择 "**常规**" > **空类**"，在"**名称**"中输入 `Product`，然后单击"**新建**"按钮：

[![](outline-view-images/populate01.png "Creating an empty class")](outline-view-images/populate01.png#lightbox)

使 `Product.cs` 文件如下所示：

```csharp
using System;
using Foundation;
using System.Collections.Generic;

namespace MacOutlines
{
    public class Product : NSObject
    {
        #region Public Variables
        public List<Product> Products = new List<Product>();
        #endregion

        #region Computed Properties
        public string Title { get; set;} = "";
        public string Description { get; set;} = "";
        public bool IsProductGroup {
            get { return (Products.Count > 0); }
        }
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

接下来，我们需要创建一个 `NSOutlineDataSource` 的子类，以便为大纲提供所请求的数据。 在**解决方案资源管理器**中，右键单击项目，然后选择 "**添加** > **新文件 ...** "选择 "**常规**" > **空类**"，在"**名称**"中输入 `ProductOutlineDataSource`，然后单击"**新建**"按钮。

编辑 `ProductTableDataSource.cs` 文件，使其类似于以下内容：

```csharp
using System;
using AppKit;
using CoreGraphics;
using Foundation;
using System.Collections;
using System.Collections.Generic;

namespace MacOutlines
{
    public class ProductOutlineDataSource : NSOutlineViewDataSource
    {
        #region Public Variables
        public List<Product> Products = new List<Product>();
        #endregion

        #region Constructors
        public ProductOutlineDataSource ()
        {
        }
        #endregion

        #region Override Methods
        public override nint GetChildrenCount (NSOutlineView outlineView, NSObject item)
        {
            if (item == null) {
                return Products.Count;
            } else {
                return ((Product)item).Products.Count;
            }

        }

        public override NSObject GetChild (NSOutlineView outlineView, nint childIndex, NSObject item)
        {
            if (item == null) {
                return Products [childIndex];
            } else {
                return ((Product)item).Products [childIndex];
            }

        }

        public override bool ItemExpandable (NSOutlineView outlineView, NSObject item)
        {
            if (item == null) {
                return Products [0].IsProductGroup;
            } else {
                return ((Product)item).IsProductGroup;
            }

        }
        #endregion
    }
}
```

此类具有大纲视图项的存储，并覆盖 `GetChildrenCount` 以返回表中的行数。 `GetChild` 返回特定的父项或子项（由大纲视图请求），并且 `ItemExpandable` 将指定项定义为父级或子级。

最后，我们需要创建 `NSOutlineDelegate` 的子类，为大纲提供行为。 在**解决方案资源管理器**中，右键单击项目，然后选择 "**添加** > **新文件 ...** "选择 "**常规**" > **空类**"，在"**名称**"中输入 `ProductOutlineDelegate`，然后单击"**新建**"按钮。

编辑 `ProductOutlineDelegate.cs` 文件，使其类似于以下内容：

```csharp
using System;
using AppKit;
using CoreGraphics;
using Foundation;
using System.Collections;
using System.Collections.Generic;

namespace MacOutlines
{
    public class ProductOutlineDelegate : NSOutlineViewDelegate
    {
        #region Constants
        private const string CellIdentifier = "ProdCell";
        #endregion

        #region Private Variables
        private ProductOutlineDataSource DataSource;
        #endregion

        #region Constructors
        public ProductOutlineDelegate (ProductOutlineDataSource datasource)
        {
            this.DataSource = datasource;
        }
        #endregion

        #region Override Methods

        public override NSView GetView (NSOutlineView outlineView, NSTableColumn tableColumn, NSObject item) {
            // This pattern allows you reuse existing views when they are no-longer in use.
            // If the returned view is null, you instance up a new view
            // If a non-null view is returned, you modify it enough to reflect the new data
            NSTextField view = (NSTextField)outlineView.MakeView (CellIdentifier, this);
            if (view == null) {
                view = new NSTextField ();
                view.Identifier = CellIdentifier;
                view.BackgroundColor = NSColor.Clear;
                view.Bordered = false;
                view.Selectable = false;
                view.Editable = false;
            }

            // Cast item
            var product = item as Product;

            // Setup view based on the column selected
            switch (tableColumn.Title) {
            case "Product":
                view.StringValue = product.Title;
                break;
            case "Details":
                view.StringValue = product.Description;
                break;
            }

            return view;
        }
        #endregion
    }
}
```

当我们创建 `ProductOutlineDelegate`的实例时，我们还会传入为大纲提供数据的 `ProductOutlineDataSource` 的实例。 `GetView` 方法负责返回一个视图（数据），以显示给定列和行的单元格。 如果可能，如果不创建新视图，则将重复使用现有视图来显示该单元。

若要填充轮廓，请编辑 `MainWindow.cs` 文件，使 `AwakeFromNib` 方法如下所示：

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Create data source and populate
    var DataSource = new ProductOutlineDataSource ();

    var Vegetables = new Product ("Vegetables", "Greens and Other Produce");
    Vegetables.Products.Add (new Product ("Cabbage", "Brassica oleracea - Leaves, axillary buds, stems, flowerheads"));
    Vegetables.Products.Add (new Product ("Turnip", "Brassica rapa - Tubers, leaves"));
    Vegetables.Products.Add (new Product ("Radish", "Raphanus sativus - Roots, leaves, seed pods, seed oil, sprouting"));
    Vegetables.Products.Add (new Product ("Carrot", "Daucus carota - Root tubers"));
    DataSource.Products.Add (Vegetables);

    var Fruits = new Product ("Fruits", "Fruit is a part of a flowering plant that derives from specific tissues of the flower");
    Fruits.Products.Add (new Product ("Grape", "True Berry"));
    Fruits.Products.Add (new Product ("Cucumber", "Pepo"));
    Fruits.Products.Add (new Product ("Orange", "Hesperidium"));
    Fruits.Products.Add (new Product ("Blackberry", "Aggregate fruit"));
    DataSource.Products.Add (Fruits);

    var Meats = new Product ("Meats", "Lean Cuts");
    Meats.Products.Add (new Product ("Beef", "Cow"));
    Meats.Products.Add (new Product ("Pork", "Pig"));
    Meats.Products.Add (new Product ("Veal", "Young Cow"));
    DataSource.Products.Add (Meats);

    // Populate the outline
    ProductOutline.DataSource = DataSource;
    ProductOutline.Delegate = new ProductOutlineDelegate (DataSource);

}
```

如果运行该应用程序，将显示以下内容：

[![](outline-view-images/populate02.png "The collapsed view")](outline-view-images/populate02.png#lightbox)

如果展开大纲视图中的节点，它将如下所示：

[![](outline-view-images/populate03.png "The expanded view")](outline-view-images/populate03.png#lightbox)

<a name="Sorting_by_Column" />

## <a name="sorting-by-column"></a>按列排序

允许用户通过单击列标题对大纲中的数据进行排序。 首先，双击 `Main.storyboard` 文件以将其打开，以便在 Interface Builder 中进行编辑。 选择 "`Product`" 列，输入 "**排序关键字**" `Title`，为**选择器**`compare:`，然后选择**订单**`Ascending`：

[![](outline-view-images/sort01.png "Setting the sort key order")](outline-view-images/sort01.png#lightbox)

保存更改并返回到 Visual Studio for Mac 以与 Xcode 同步。

现在，让我们编辑 `ProductOutlineDataSource.cs` 文件，并添加以下方法：

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
    }
}

public override void SortDescriptorsChanged (NSOutlineView outlineView, NSSortDescriptor[] oldDescriptors)
{
    // Sort the data
    Sort (oldDescriptors [0].Key, oldDescriptors [0].Ascending);
    outlineView.ReloadData ();
}
```

使用 `Sort` 方法，我们可以基于给定 `Product` 类字段按升序或降序对数据源中的数据进行排序。 每次单击列标题时，都将调用重写的 `SortDescriptorsChanged` 方法。 它将被传递到 Interface Builder 中设置的**键值**和该列的排序顺序。

如果运行应用程序，并单击列标题中的行，则将按该列对行进行排序：

[![](outline-view-images/sort02.png "Example of sorted output")](outline-view-images/sort02.png#lightbox)

<a name="Row_Selection" />

## <a name="row-selection"></a>行选择

如果要允许用户选择单个行，请双击 `Main.storyboard` 文件以将其打开，以便在 Interface Builder 中进行编辑。 选择**接口层次结构**中的大纲视图，并取消选中 "**属性检查器**" 中的 "**多个**" 复选框：

[![](outline-view-images/select01.png "The Attribute Inspector")](outline-view-images/select01.png#lightbox)

保存更改并返回到 Visual Studio for Mac 以与 Xcode 同步。

接下来，编辑 `ProductOutlineDelegate.cs` 文件并添加以下方法：

```csharp
public override bool ShouldSelectItem (NSOutlineView outlineView, NSObject item)
{
    // Don't select product groups
    return !((Product)item).IsProductGroup;
}
```

这将允许用户在大纲视图中选择任何单个行。 如果你不希望用户能够选择任何项，则为你不希望用户能够为每个项目选择或 `false` 的任何项的 `ShouldSelectItem` 返回 `false`。

<a name="Multiple_Row_Selection" />

## <a name="multiple-row-selection"></a>多行选择

如果要允许用户选择多个行，请双击 `Main.storyboard` 文件以将其打开，以便在 Interface Builder 中进行编辑。 选择**接口层次结构**中的 "大纲" 视图，并选中 "**属性检查器**" 中的 "**多个**" 复选框：

[![](outline-view-images/select02.png "The Attribute Inspector")](outline-view-images/select02.png#lightbox)

保存更改并返回到 Visual Studio for Mac 以与 Xcode 同步。

接下来，编辑 `ProductOutlineDelegate.cs` 文件并添加以下方法：

```csharp
public override bool ShouldSelectItem (NSOutlineView outlineView, NSObject item)
{
    // Don't select product groups
    return !((Product)item).IsProductGroup;
}
```

这将允许用户在大纲视图中选择任何单个行。 如果你不希望用户能够选择任何项，则为你不希望用户能够为每个项目选择或 `false` 的任何项的 `ShouldSelectRow` 返回 `false`。

<a name="Type_to_Select_Row" />

## <a name="type-to-select-row"></a>键入以选择行

如果要允许用户在选中大纲视图的情况下键入字符，并选择包含该字符的第一行，请双击 `Main.storyboard` 文件以将其打开，以便在 Interface Builder 中进行编辑。 选择**接口层次结构**中的 "大纲" 视图，并选中 "**属性检查器**" 中的 "**类型选择**" 复选框：

[![](outline-view-images/type01.png "Editing the row type")](outline-view-images/type01.png#lightbox)

保存更改并返回到 Visual Studio for Mac 以与 Xcode 同步。

现在，让我们编辑 `ProductOutlineDelegate.cs` 文件，并添加以下方法：

```csharp
public override NSObject GetNextTypeSelectMatch (NSOutlineView outlineView, NSObject startItem, NSObject endItem, string searchString)
{
    foreach(Product product in DataSource.Products) {
        if (product.Title.Contains (searchString)) {
            return product;
        }
    }

    // Not found
    return null;
}
```

`GetNextTypeSelectMatch` 方法获取给定的 `searchString` 并返回其 `Title`中包含该字符串的第一个 `Product` 的项。

<a name="Reordering_Columns" />

## <a name="reordering-columns"></a>重新排序列

如果要允许用户在大纲视图中拖动重新排序列，请双击 `Main.storyboard` 文件以将其打开，以便在 Interface Builder 中进行编辑。 选择**接口层次结构**中的大纲视图，并选中 "**属性检查器**" 中的 "重新**排序**" 复选框：

[![](outline-view-images/reorder01.png "The Attribute Inspector")](outline-view-images/reorder01.png#lightbox)

如果我们为 "**自动保存**" 属性提供一个值，并选中 "**列信息**" 字段，则对该表的布局所做的任何更改都将自动保存，并在下次运行应用程序时还原。

保存更改并返回到 Visual Studio for Mac 以与 Xcode 同步。

现在，让我们编辑 `ProductOutlineDelegate.cs` 文件，并添加以下方法：

```csharp
public override bool ShouldReorder (NSOutlineView outlineView, nint columnIndex, nint newColumnIndex)
{
    return true;
}
```

`ShouldReorder` 方法应为要允许拖放到 `newColumnIndex`中的任何列返回 `true`，否则返回 `false`;

如果运行应用程序，我们可以拖动列标题以对列进行重新排序：

[![](outline-view-images/reorder02.png "Example of reordering columns")](outline-view-images/reorder02.png#lightbox)

<a name="Editing_Cells" />

## <a name="editing-cells"></a>编辑单元格

如果希望允许用户编辑给定单元的值，请编辑 `ProductOutlineDelegate.cs` 文件，并按如下所示更改 `GetViewForItem` 方法：

```csharp
public override NSView GetView (NSOutlineView outlineView, NSTableColumn tableColumn, NSObject item) {
    // Cast item
    var product = item as Product;

    // This pattern allows you reuse existing views when they are no-longer in use.
    // If the returned view is null, you instance up a new view
    // If a non-null view is returned, you modify it enough to reflect the new data
    NSTextField view = (NSTextField)outlineView.MakeView (tableColumn.Title, this);
    if (view == null) {
        view = new NSTextField ();
        view.Identifier = tableColumn.Title;
        view.BackgroundColor = NSColor.Clear;
        view.Bordered = false;
        view.Selectable = false;
        view.Editable = !product.IsProductGroup;
    }

    // Tag view
    view.Tag = outlineView.RowForItem (item);

    // Allow for edit
    view.EditingEnded += (sender, e) => {

        // Grab product
        var prod = outlineView.ItemAtRow(view.Tag) as Product;

        // Take action based on type
        switch(view.Identifier) {
        case "Product":
            prod.Title = view.StringValue;
            break;
        case "Details":
            prod.Description = view.StringValue;
            break;
        }
    };

    // Setup view based on the column selected
    switch (tableColumn.Title) {
    case "Product":
        view.StringValue = product.Title;
        break;
    case "Details":
        view.StringValue = product.Description;
        break;
    }

    return view;
}
```

现在，如果我们运行应用程序，则用户可以在表视图中编辑单元：

[![](outline-view-images/editing01.png "An example of editing cells")](outline-view-images/editing01.png#lightbox)

<a name="Using_Images_in_Outline_Views" />

## <a name="using-images-in-outline-views"></a>在大纲视图中使用图像

若要在 `NSOutlineView` 中包括图像作为单元的一部分，需要更改大纲视图的 `NSTableViewDelegate's` `GetView` 方法返回数据的方式，以使用 `NSTableCellView` 而不是典型的 `NSTextField`。 例如:

```csharp
public override NSView GetView (NSOutlineView outlineView, NSTableColumn tableColumn, NSObject item) {
    // Cast item
    var product = item as Product;

    // This pattern allows you reuse existing views when they are no-longer in use.
    // If the returned view is null, you instance up a new view
    // If a non-null view is returned, you modify it enough to reflect the new data
    NSTableCellView view = (NSTableCellView)outlineView.MakeView (tableColumn.Title, this);
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
        view.TextField.Editable = !product.IsProductGroup;
    }

    // Tag view
    view.TextField.Tag = outlineView.RowForItem (item);

    // Allow for edit
    view.TextField.EditingEnded += (sender, e) => {

        // Grab product
        var prod = outlineView.ItemAtRow(view.Tag) as Product;

        // Take action based on type
        switch(view.Identifier) {
        case "Product":
            prod.Title = view.TextField.StringValue;
            break;
        case "Details":
            prod.Description = view.TextField.StringValue;
            break;
        }
    };

    // Setup view based on the column selected
    switch (tableColumn.Title) {
    case "Product":
        view.ImageView.Image = NSImage.ImageNamed (product.IsProductGroup ? "tags.png" : "tag.png");
        view.TextField.StringValue = product.Title;
        break;
    case "Details":
        view.TextField.StringValue = product.Description;
        break;
    }

    return view;
}
```

有关详细信息，请参阅使用[图像](~/mac/app-fundamentals/image.md)文档中的[使用带有大纲视图的图像](~/mac/app-fundamentals/image.md)部分。

<a name="Data_Binding_Outline_Views" />

## <a name="data-binding-outline-views"></a>数据绑定大纲视图

通过在 Xamarin 应用程序中使用键/值编码和数据绑定技术，可以极大地减少需要编写和维护的代码量，以填充和处理 UI 元素。 您还可以从您的前端用户界面（_模型-视图-控制器_）进一步分离您的备份数据（_数据模型_），从而更易于维护，更灵活的应用程序设计。

键-值编码（KVC）是一种用于间接访问对象属性的机制，使用键（特殊格式的字符串）来标识属性，而不是通过实例变量或访问器方法（`get/set`）来访问这些属性。 通过在 Xamarin 应用程序中实现符合键值的代码访问器，你可以访问其他 macOS 的功能，例如键-值观察（KVO）、数据绑定、核心数据、Cocoa 绑定和 scriptability。

有关详细信息，请参阅[数据绑定和键-值编码](~/mac/app-fundamentals/databinding.md)文档的[大纲视图数据绑定](~/mac/app-fundamentals/databinding.md#Outline_View_Data_Binding)部分。

<a name="Summary" />

## <a name="summary"></a>总结

本文详细介绍了如何在 Xamarin. Mac 应用程序中使用大纲视图。 我们看到了不同的类型和使用大纲视图，如何在 Xcode 的 Interface Builder 中创建和维护大纲视图，以及如何在代码中C#使用大纲视图。

## <a name="related-links"></a>相关链接

- [MacOutlines （示例）](https://docs.microsoft.com/samples/xamarin/mac-samples/macoutlines)
- [MacImages（示例）](https://docs.microsoft.com/samples/xamarin/mac-samples/macimages)
- [了解 Mac](~/mac/get-started/hello-mac.md)
- [表视图](~/mac/user-interface/table-view.md)
- [源列表](~/mac/user-interface/source-list.md)
- [数据绑定和键值编码](~/mac/app-fundamentals/databinding.md)
- [OS X 人机界面指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [大纲视图简介](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/OutlineView/OutlineView.html#//apple_ref/doc/uid/10000023i)
- [NSOutlineView](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSOutlineView_Class/index.html#//apple_ref/doc/uid/TP40004079)
- [NSOutlineViewDataSource](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Protocols/NSOutlineViewDataSource_Protocol/index.html#//apple_ref/doc/uid/TP40004175)
- [NSOutlineViewDelegate](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/NSOutlineViewDelegate_Protocol/index.html#//apple_ref/doc/uid/TP40008609)
