---
title: 在 Xamarin.Mac 中的大纲视图
description: 本文介绍如何使用 Xamarin.Mac 应用程序中的大纲视图。 它介绍了创建和维护在 Xcode 和 Interface Builder 中的大纲视图和以编程方式使用它们。
ms.prod: xamarin
ms.assetid: 043248EE-11DA-4E96-83A3-08824A4F2E01
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: fd97dbbe102c5a755c4a8974cf1a952c0050ac7c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61292528"
---
# <a name="outline-views-in-xamarinmac"></a>在 Xamarin.Mac 中的大纲视图

_本文介绍如何使用 Xamarin.Mac 应用程序中的大纲视图。它介绍了创建和维护在 Xcode 和 Interface Builder 中的大纲视图和以编程方式使用它们。_

当使用C#和.NET 中的 Xamarin.Mac 应用程序中，您可以访问相同的大纲视图的使用的开发人员*Objective C*和*Xcode* does。 由于 Xamarin.Mac 与 Xcode 直接集成，可以使用 Xcode 的_Interface Builder_创建和维护大纲视图 （或选择通过 C# 代码中直接创建）。

大纲视图是一种表，用户可以通过展开或折叠的分层数据的行。 表视图中，如大纲视图显示数据的一组相关项，其中每行表示各个项和列，表示这些项的属性。 与不同的表视图中，在大纲视图中的项不在平面列表中，它们在层次结构中，如文件和文件夹在硬盘上的组织方式。

[![](outline-view-images/populate03.png "运行示例应用")](outline-view-images/populate03.png#lightbox)

在本文中，我们将介绍 Xamarin.Mac 应用程序中使用大纲视图的基础知识。 强烈建议您明确[Hello，Mac](~/mac/get-started/hello-mac.md)文章第一次，具体而言[Xcode 和 Interface Builder 简介](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder)并[输出口和操作](~/mac/get-started/hello-mac.md#outlets-and-actions)部分中的，因为它介绍了关键概念和技术，我们将在本文中使用。

可能想要看一看[公开 C# 类 / 方法添加到 Objective C](~/mac/internals/how-it-works.md)一部分[Xamarin.Mac 内部机制](~/mac/internals/how-it-works.md)文档，它还说明了`Register`和`Export`命令用于布置于 C# 类对 OBJECTIVE-C 对象和 UI 元素。

<a name="Introduction_to_Outline_Views" />

## <a name="introduction-to-outline-views"></a>大纲视图简介

大纲视图是一种表，用户可以通过展开或折叠的分层数据的行。 表视图中，如大纲视图显示数据的一组相关项，其中每行表示各个项和列，表示这些项的属性。 与不同的表视图中，在大纲视图中的项不在平面列表中，它们在层次结构中，如文件和文件夹在硬盘上的组织方式。

如果大纲视图中的项包含其他项，它可展开或折叠的用户。 可展开的项显示一个明显的三角形，用于指向右侧项折叠和展开项时，向下指时。 单击泄露三角形会导致要展开或折叠的项。

大纲视图 (`NSOutlineView`) 是一个表视图的子类 (`NSTableView`) 并在这种情况下，将其大部分行为继承其父类。 结果是，大纲视图也支持许多操作支持的表视图中，如选择行或列，通过拖动列标题，等等，重新定位列。 Xamarin.Mac 应用程序可以控制这些功能，并可以配置大纲视图的参数 （不管是在代码或 Interface Builder 中） 以允许或禁止某些操作。

大纲视图不会存储其自己的数据，而是依赖数据源 (`NSOutlineViewDataSource`) 提供的行和按需基于要求的列。

可以通过提供的大纲视图委托子类自定义大纲视图的行为 (`NSOutlineViewDelegate`) 若要支持大纲列管理，键入要选择功能、 行选择和编辑、 自定义跟踪和为各个自定义视图列和行。

由于与表视图，大纲视图共享大部分它的行为和功能，你可能想要通过我们[表视图](~/mac/user-interface/table-view.md)文档，然后再继续完成这篇文章。

<a name="Creating_and_Maintaining_Outline_Views_in_Xcode" />

## <a name="creating-and-maintaining-outline-views-in-xcode"></a>创建和维护在 Xcode 中的大纲视图

在创建新的 Xamarin.Mac 的 Cocoa 应用程序时，默认情况下获得标准保留为空，窗口。 在中定义此 windows`.storyboard`自动包含在项目中的文件。 若要编辑您的 windows 设计、 在**解决方案资源管理器**，双击`Main.storyboard`文件：

[![](outline-view-images/edit01.png "选择主要情节提要")](outline-view-images/edit01.png#lightbox)

这将在 Xcode 的 Interface Builder 中打开的窗口设计：

[![](outline-view-images/edit02.png "编辑在 Xcode 中的 UI")](outline-view-images/edit02.png#lightbox)

类型`outline`成**库检查器**搜索框，可以更轻松地找到大纲视图控件：

[![](outline-view-images/edit03.png "从库中选择的大纲视图")](outline-view-images/edit03.png#lightbox)

大纲视图拖至中的视图控制器**界面编辑器**，使其填充内容区域的视图控制器，并将其设置为其中缩小和中的窗口都在增加**约束编辑器**:

[![](outline-view-images/edit04.png "编辑约束")](outline-view-images/edit04.png#lightbox)

选择中的大纲视图**界面层次结构**和以下属性可用于在**属性检查器**:

[![](outline-view-images/edit05.png "属性检查器")](outline-view-images/edit05.png#lightbox)

- **概述列**-在其中显示分层数据的表列。
- **自动保存大纲列**-如果`true`，大纲列将自动保存和还原应用程序运行之间。
- **缩进**-要缩进已展开的项目下的列的量。
- **缩进遵循单元格**-如果`true`，缩进标记将缩进以及单元格。
- **自动保存展开项**-如果`true`，将自动保存和还原应用程序运行之间的项的展开/折叠状态。
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
> 要保留旧的 Xamarin.Mac 应用程序，除非`NSView`应通过使用基于的大纲视图`NSCell`基于表视图。 `NSCell` 被视为旧版，可能不支持今后。

选择中的表列**界面层次结构**和以下属性可用于在**属性检查器**:

[![](outline-view-images/edit06.png "属性检查器")](outline-view-images/edit06.png#lightbox)

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

[![](outline-view-images/edit07.png "属性检查器")](outline-view-images/edit07.png#lightbox)

这些是所有的标准视图的属性。 此外可以调整此列的行的大小。

选择表视图单元格 (默认情况下，这是`NSTextField`) 中**界面层次结构**和以下属性可用于在**属性检查器**:

[![](outline-view-images/edit08.png "属性检查器")](outline-view-images/edit08.png#lightbox)

您必须要在此处设置的标准文本字段的所有属性。 默认情况下，标准的文本字段用于在列中显示的单元格的数据。

选择一个表格单元格视图 (`NSTableFieldCell`) 中**界面层次结构**和以下属性可用于在**属性检查器**:

[![](outline-view-images/edit09.png "属性检查器")](outline-view-images/edit09.png#lightbox)

最重要的设置是：

- **布局**-选择此列中的单元格的布局的方式。
- **使用单个行模式**-如果`true`，该单元格被限制为单个行。
- **第一个运行时布局宽度**-如果`true`，该单元格将首选宽度为其设置 （手动或自动） 时显示首次运行该应用程序。
- **操作**-控制何时编辑**操作**发送有关单元格。
- **行为**-定义单元格是否可选或可编辑。
- **格式文本**-如果`true`，该单元格可以显示进行了格式化，带样式的文本。
- **撤消**-如果`true`，该单元格承担的责任，对它的撤消行为。

选择表单元格视图 (`NSTableFieldCell`) 中的表格列底部**界面层次结构**:

[![](outline-view-images/edit11.png "选择表单元视图")](outline-view-images/edit10.png#lightbox)

这使您可以编辑表单元格视图用作的基础_模式_为给定的列创建的所有单元格。

<a name="Adding_Actions_and_Outlets" />

### <a name="adding-actions-and-outlets"></a>添加操作和输出口

只需像任何其他 Cocoa UI 控件，我们需要公开大纲视图和列并与 C# 代码使用的单元格**操作**并**输出口**（基于所需的功能）。

过程是相同的任何我们想要公开的大纲视图元素：

1. 切换到**助手编辑器**并确保`ViewController.h`选择文件： 

    [![](outline-view-images/edit11.png "选择正确的.h 文件")](outline-view-images/edit11.png#lightbox)
2. 选择从大纲视图**界面层次结构**、 单击和拖动到`ViewController.h`文件。
3. 创建**输出口**为大纲视图名为`ProductOutline`: 

    [![](outline-view-images/edit13.png "配置输出口")](outline-view-images/edit13.png#lightbox)
4. 创建**输出口**对于表列也被称为`ProductColumn`和`DetailsColumn`: 

    [![](outline-view-images/edit14.png "配置输出口")](outline-view-images/edit14.png#lightbox)
5. 保存更改并返回到 Visual Studio for Mac 与 Xcode 同步。

接下来，我们将编写的代码显示大纲的某些数据时运行该应用程序。

<a name="Populating_the_Table_View" />

## <a name="populating-the-outline-view"></a>填充大纲视图

与我们的大纲视图在 Interface Builder 中设计和通过公开**输出口**，接下来我们需要创建的 C# 代码来填充它。

首先，让我们创建一个新`Product`类来保存为单个行的子产品组的信息。 在中**解决方案资源管理器**，右键单击该项目并选择**添加** > **新文件...** 选择**常规** > **空类**，输入`Product`有关**名称**然后单击**新建**按钮：

[![](outline-view-images/populate01.png "创建一个空类")](outline-view-images/populate01.png#lightbox)

使`Product.cs`文件外观如下所示：

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

接下来，我们需要创建一个子类`NSOutlineDataSource`按要求我们轮廓提供的数据。 在中**解决方案资源管理器**，右键单击该项目并选择**添加** > **新文件...** 选择**常规** > **空类**，输入`ProductOutlineDataSource`有关**名称**然后单击**新建**按钮。

编辑`ProductTableDataSource.cs`文件，并使其看起来如下所示：

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

此类采用我们大纲视图项的存储，并重写`GetChildrenCount`表中返回的行数。 `GetChild` （如请求的大纲视图） 将返回一个特定的父级或子级项和`ItemExpandable`定义为父或子元素指定的项。

最后，我们需要创建一个子类`NSOutlineDelegate`以提供有关我们概述的行为。 在中**解决方案资源管理器**，右键单击该项目并选择**添加** > **新文件...** 选择**常规** > **空类**，输入`ProductOutlineDelegate`有关**名称**然后单击**新建**按钮。

编辑`ProductOutlineDelegate.cs`文件，并使其看起来如下所示：

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

我们创建的实例时`ProductOutlineDelegate`，我们还传递的实例中`ProductOutlineDataSource`提供概要中的数据。 `GetView`方法负责返回视图 （数据） 以显示为列和行的单元格。 如果可能，将重复使用现有视图以显示单元格，如果不是必须创建新视图。

若要填充概要中，让我们编辑`MainWindow.cs`文件，然后进行`AwakeFromNib`方法看起来像以下：

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

如果我们运行该应用程序，将显示以下：

[![](outline-view-images/populate02.png "折叠的视图")](outline-view-images/populate02.png#lightbox)

如果我们展开大纲视图中的节点，它将如下所示：

[![](outline-view-images/populate03.png "已展开的视图")](outline-view-images/populate03.png#lightbox)

<a name="Sorting_by_Column" />

## <a name="sorting-by-column"></a>按列排序

让我们允许用户通过单击列标题排序概要中的数据。 首先，双击`Main.storyboard`文件以打开 Interface Builder 中为进行编辑。 选择`Product`列中，输入`Title`有关**排序键**，`compare:`有关**选择器**，然后选择`Ascending`为**顺序**:

[![](outline-view-images/sort01.png "设置排序键顺序")](outline-view-images/sort01.png#lightbox)

保存所做的更改并返回到 Visual Studio for Mac 与 Xcode 同步。

现在让我们编辑`ProductOutlineDataSource.cs`文件，并添加以下方法：

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

`Sort`方法使我们能够对基于数据源中的数据排序给定`Product`类字段按升序或降序排序。 重写`SortDescriptorsChanged`每次使用单击列标题上时，将调用方法。 将其传递**密钥**Interface Builder 和该列的排序顺序中设置的值。

如果我们运行该应用程序，并单击列标题中，将按该列排序行：

[![](outline-view-images/sort02.png "已排序的输出的示例")](outline-view-images/sort02.png#lightbox)

<a name="Row_Selection" />

## <a name="row-selection"></a>行选择

如果你想要允许用户选择的单个行中，双击`Main.storyboard`文件以打开 Interface Builder 中为进行编辑。 选择中的大纲视图**界面层次结构**并取消选中**多个**中的复选框**属性检查器**:

[![](outline-view-images/select01.png "属性检查器")](outline-view-images/select01.png#lightbox)

保存所做的更改并返回到 Visual Studio for Mac 与 Xcode 同步。


接下来，编辑`ProductOutlineDelegate.cs`文件，并添加以下方法：

```csharp
public override bool ShouldSelectItem (NSOutlineView outlineView, NSObject item)
{
    // Don't select product groups
    return !((Product)item).IsProductGroup;
}
```

这将允许用户在大纲视图中选择任何单个行。 返回`false`有关`ShouldSelectItem`有关任一项，您不希望用户能够选择或`false`每一项，如果您不希望用户能够选择任何项。

<a name="Multiple_Row_Selection" />

## <a name="multiple-row-selection"></a>多个行选择

如果你想要允许用户选择多个行中，双击`Main.storyboard`文件以打开 Interface Builder 中为进行编辑。 选择中的大纲视图**界面层次结构**，并检查**多个**中的复选框**属性检查器**:

[![](outline-view-images/select02.png "属性检查器")](outline-view-images/select02.png#lightbox)

保存所做的更改并返回到 Visual Studio for Mac 与 Xcode 同步。


接下来，编辑`ProductOutlineDelegate.cs`文件，并添加以下方法：

```csharp
public override bool ShouldSelectItem (NSOutlineView outlineView, NSObject item)
{
    // Don't select product groups
    return !((Product)item).IsProductGroup;
}
```

这将允许用户在大纲视图中选择任何单个行。 返回`false`有关`ShouldSelectRow`有关任一项，您不希望用户能够选择或`false`每一项，如果您不希望用户能够选择任何项。

<a name="Type_to_Select_Row" />

## <a name="type-to-select-row"></a>要选择的行类型

如果你想要允许用户使用选定的大纲视图中键入字符，然后选择第一行具有该字符，则双击`Main.storyboard`文件以打开 Interface Builder 中为进行编辑。 选择中的大纲视图**界面层次结构**，并检查**类型选择**中的复选框**属性检查器**:

[![](outline-view-images/type01.png "编辑的行类型")](outline-view-images/type01.png#lightbox)

保存所做的更改并返回到 Visual Studio for Mac 与 Xcode 同步。

现在让我们编辑`ProductOutlineDelegate.cs`文件，并添加以下方法：

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

`GetNextTypeSelectMatch`方法采用给定`searchString`，并返回第一个项`Product`，该字符串中包含的`Title`。

<a name="Reordering_Columns" />

## <a name="reordering-columns"></a>对列重新排序

如果你想要允许用户拖动对在大纲视图中的列重新排序，请双击`Main.storyboard`文件以打开 Interface Builder 中为进行编辑。 选择中的大纲视图**界面层次结构**，并检查**重新排序功能**中的复选框**属性检查器**:

[![](outline-view-images/reorder01.png "属性检查器")](outline-view-images/reorder01.png#lightbox)

如果我们提供的值**自动保存**属性并检查**的列信息**字段中，我们对表的布局进行任何更改会自动为我们保存和还原应用程序的下一次运行时。

保存所做的更改并返回到 Visual Studio for Mac 与 Xcode 同步。

现在让我们编辑`ProductOutlineDelegate.cs`文件，并添加以下方法：

```csharp
public override bool ShouldReorder (NSOutlineView outlineView, nint columnIndex, nint newColumnIndex)
{
    return true;
}
```

`ShouldReorder`方法应返回`true`它想要允许为任何列拖动到重新排序`newColumnIndex`，否则返回`false`;

如果我们运行该应用程序，我们可以拖动周围的列标题对列重新排序：

[![](outline-view-images/reorder02.png "重新排序的列的示例")](outline-view-images/reorder02.png#lightbox)

<a name="Editing_Cells" />

## <a name="editing-cells"></a>编辑单元格

如果你想要允许用户编辑给定的单元格的值，请编辑`ProductOutlineDelegate.cs`文件，并更改`GetViewForItem`方法，如下所示：

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

现在如果我们运行该应用程序，用户可以编辑的表视图中的单元格：

[![](outline-view-images/editing01.png "举例说明如何编辑单元格")](outline-view-images/editing01.png#lightbox)

<a name="Using_Images_in_Outline_Views" />

## <a name="using-images-in-outline-views"></a>在大纲视图中使用映像

用于包含映像中的单元格的一部分`NSOutlineView`，将需要更改大纲视图返回的数据是如何`NSTableViewDelegate's``GetView`要使用的方法`NSTableCellView`而不是典型`NSTextField`。 例如：

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

有关详细信息，请参阅[使用大纲视图中使用图像](~/mac/app-fundamentals/image.md)一部分我们[使用映像](~/mac/app-fundamentals/image.md)文档。

<a name="Data_Binding_Outline_Views" />

## <a name="data-binding-outline-views"></a>数据绑定大纲视图

通过在 Xamarin.Mac 应用程序中使用的键-值编码和数据绑定技术，您可以极大地减少需要编写和维护来填充和使用 UI 元素的代码量。 此外可以进一步分离你的备份数据的好处 (_数据模型_) 从你前面结束用户界面 (_模型-视图-控制器_)，从而导致更易于维护，更灵活的应用程序设计。

键值对的编码 (KVC) 是一种机制间接访问对象的属性、 使用密钥 （特殊格式字符串） 来标识而不是通过实例变量访问它们的属性或访问器方法 (`get/set`)。 在 Xamarin.Mac 应用程序中实现键 / 值编码符合访问器，可以获得其他 macOS 功能，例如键-值观察 (KVO)、 数据绑定、 核心数据、 Cocoa 绑定和 scriptability 的访问权限。

有关详细信息，请参阅[大纲视图的数据绑定](~/mac/app-fundamentals/databinding.md#Outline_View_Data_Binding)一部分我们[数据绑定和键值编码](~/mac/app-fundamentals/databinding.md)文档。

<a name="Summary" />

## <a name="summary"></a>总结

本文已在 Xamarin.Mac 应用程序中使用大纲视图的详细的信息。 我们看到了不同类型，并使用大纲视图、 如何创建和维护在 Xcode 的 Interface Builder 中的大纲视图以及如何在 C# 代码中使用大纲视图。

## <a name="related-links"></a>相关链接

- [MacOutlines （示例）](https://developer.xamarin.com/samples/mac/MacOutlines/)
- [MacImages（示例）](https://developer.xamarin.com/samples/mac/MacImages/)
- [了解 Mac](~/mac/get-started/hello-mac.md)
- [表视图](~/mac/user-interface/table-view.md)
- [源列表](~/mac/user-interface/source-list.md)
- [数据绑定和键值编码](~/mac/app-fundamentals/databinding.md)
- [OS X 人机界面指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [大纲视图简介](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/OutlineView/OutlineView.html#//apple_ref/doc/uid/10000023i)
- [NSOutlineView](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSOutlineView_Class/index.html#//apple_ref/doc/uid/TP40004079)
- [NSOutlineViewDataSource](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Protocols/NSOutlineViewDataSource_Protocol/index.html#//apple_ref/doc/uid/TP40004175)
- [NSOutlineViewDelegate](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/NSOutlineViewDelegate_Protocol/index.html#//apple_ref/doc/uid/TP40008609)
