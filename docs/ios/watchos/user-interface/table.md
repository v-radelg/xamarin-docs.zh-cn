---
title: watchOS 在 Xamarin 中的表控件
description: 本文档介绍如何在 Xamarin 中使用 watchOS 表控件。 它讨论添加表、 添加行控制器、 创建和填充行，响应点击，和的详细信息。
ms.prod: xamarin
ms.assetid: 7C14126D-9591-4387-A588-3C4521F11C55
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: cd5e7299874bbfb1b652315a549b9d067d58e9a0
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60881255"
---
# <a name="watchos-table-controls-in-xamarin"></a>watchOS 在 Xamarin 中的表控件

WatchOS`WKInterfaceTable`控件比其 iOS 对应的要简单得多，但执行类似的角色。 它会创建滚动列表中，可能会有自定义布局，并且该响应触摸事件中的行。

![](table-images/table-list-sml.png "监视表列表") ![](table-images/table-detail-sml.png)
<!-- watch image courtesy of http://infinitapps.com/bezel/ -->

## <a name="adding-a-table"></a>添加表

拖动**表**到场景的控件。 默认情况下它将类似此 （显示单个未指定的行布局）：

[![](table-images/add-table-sml.png "添加表")](table-images/add-table.png#lightbox)

为表提供一个名称**属性**填充的**名称**框，以便可以在代码中引用。

## <a name="add-a-row-controller"></a>添加行控制器

该表自动包含单个行，由一个包含的行控制器**组**默认情况下的控件。

若要设置**类**对于行控制器，请选择中的行**文档大纲**并键入中的类名称**属性**板：

[![](table-images/add-row-controller-sml.png "在属性面板中输入类名")](table-images/add-row-controller.png#lightbox)

一旦设置行的控制器类，IDE 将创建相应的C#项目文件中的。 控件 （如标签） 拖至行，因此它们可以在代码中引用为其提供名称。




## <a name="create-and-populate-rows"></a>创建并填充的行

`SetNumberOfRows` 创建行控制器类为每个行，使用`Identifier`选择正确的订阅。 如果你为行控制器提供自定义`Identifier`，更改**默认**中所使用的标识符为以下代码段。 `RowController` *的每一行*时创建`SetNumberOfRows`称为和显示的表。

```csharp
myTable.SetNumberOfRows ((nint)rows.Count, "default");
        // loads row controller by identifier
```

> [!IMPORTANT]
> 像在 iOS 中，未虚拟化表行。 尝试限制 （Apple 推荐小于 20） 的行数。

一旦已创建的行，就必须填充每个单元 (如`GetCell`像在 iOS 中)。 来自此代码片段[WatchTables 示例](https://developer.xamarin.com/samples/monotouch/watchOS/WatchTables/)更新每个行中的标签

```csharp
for (var i = 0; i < rows.Count; i++) {
    var elementRow = (RowController)myTable.GetRowController (i);
    elementRow.myRowLabel.SetText (rows [i]);
}
```

> [!IMPORTANT]
> 使用`SetNumberOfRows`，然后遍历使用`GetRowController`导致要发送到所监视的整个表。 在随后的表的视图，如果您需要添加或删除特定行使用`InsertRowsAt`和`RemoveRowsAt`以提高性能。


## <a name="respond-to-taps"></a>响应的分流点

您可以响应行选择两个不同的方式：

- 实现`DidSelectRow`方法接口在控制器上，或
- 创建情节提要 segue 并实现`GetContextForSegue`如果想要行选择要打开另一个场景。

### <a name="didselectrow"></a>DidSelectRow

若要以编程方式处理行选择，实现`DidSelectRow`方法。 若要打开新的场景，请使用`PushController`，并传递场景的标识符和要使用的数据上下文：

```csharp
public override void DidSelectRow (WKInterfaceTable table, nint rowIndex)
{
    var rowData = rows [(int)rowIndex];
    Console.WriteLine ("Row selected:" + rowData);
    // if selection should open a new scene
    PushController ("secondInterface", rows[(int)rowIndex]);
}
```

### <a name="getcontextforsegue"></a>GetContextForSegue

将 segue 情节提要上从表行向另一个场景 (按住**控制**密钥拖动指针时)。
请务必选择 segue 并为其提供标识符**属性**板 (如`secondLevel`在下面的示例)。

在界面控制器中，实现`GetContextForSegue`方法并返回应提供的数据上下文的场景的 segue 提供。

```csharp
public override NSObject GetContextForSegue (string segueIdentifier, WKInterfaceTable table, nint rowIndex)
{
    if (segueIdentifier == "secondLevel") {
        return new NSString (rows[(int)rowIndex]);
    }
    return null;
}
```

此数据将传递给目标情节提要场景其`Awake`方法。

## <a name="multiple-row-types"></a>多个行类型

默认情况下 table 控件具有您可以设计的单个行类型。 若要添加更多行模板使用**行**框中**属性**板来创建更多行控制器：

![](table-images/prototype-rows1.png "设置原型行数")

设置**行**属性设置为**3**将创建其他行占位符，您可以拖动到控件。 对于每一行，设置**类**名称**属性**板，以确保创建行控制器类。

![](table-images/prototype-rows2.png "在设计器中的原型行")

若要填充的表使用不同的行类型使用`SetRowTypes`方法，以指定要用于表中的每一行的行控制器类型。 使用的行标识符以指定哪些行控制器应该用于每个行。

此数组中元素的数目应与匹配您希望在表中的行数：

```csharp
myTable.SetRowTypes (new [] {"type1", "default", "default", "type2", "default"});
```

当填充表具有多个行控制器，将需要跟踪的填充 UI 如预期的类型：

```csharp
for (var i = 0; i < rows.Count; i++) {
    if (i == 0) {
        var elementRow = (Type1RowController)myTable.GetRowController (i);
        // populate UI controls
    } else if (i == 3) {
        var elementRow = (Type2RowController)myTable.GetRowController (i);
        // populate UI controls
    } else {
        var elementRow = (DefaultRowController)myTable.GetRowController (i);
        // populate UI controls
    }
}
```


## <a name="vertical-detail-paging"></a>垂直的详细信息分页

watchOS 3 引入了表的新功能： 滚动浏览的详细信息页的功能与每个行，而无需返回到表并选择另一个行。 可通过向上和向下轻扫或使用数字 Crown 滚动的详细信息屏幕。

![](table-images/table-scroll-sml.png "垂直的详细信息分页示例") ![](table-images/table-detail-sml.png)

> [!IMPORTANT]
> 此功能目前仅通过编辑在 Xcode Interface Builder 情节提要。

若要启用此功能，请选择`WKInterfaceTable`上的设计图面和刻度线**垂直详细信息分页**选项：

![](table-images/vertical-detail-paging-sml.png "选择垂直详细信息分页选项")

作为[通过 Apple 解释](https://developer.apple.com/reference/watchkit/wkinterfacetable#1682023)表导航必须使用 segue 为了使用分页功能。 重新编写使用的任何现有代码`PushController`使用请 segue 相反。

<a name="add_row_controller" />

## <a name="appendix-row-controller-code-example"></a>附录：行控制器的代码示例

在设计器中创建行控制器时，IDE 将自动创建两个代码文件。 有关参考，这些生成的文件中的代码所示。

第一个将被命名为对于类，例如**RowController.cs**，如下所示：

```csharp
using System;
using Foundation;

namespace WatchTablesExtension
{
    public partial class RowController : NSObject
    {
        public RowController ()
        {
        }
    }
}
```

另 **。 designer.cs**文件是包含输出口和操作的设计器图面，如其中一个此示例创建一个分部类定义`WKInterfaceLabel`控件：

```csharp
using Foundation;
using System;
using System.CodeDom.Compiler;
using UIKit;

namespace WatchTables.OnWatchExtension
{
    [Register ("RowController")]
    partial class RowController
    {
        [Outlet]
        [GeneratedCode ("iOS Designer", "1.0")]
        public WatchKit.WKInterfaceLabel MyLabel { get; set; }

        void ReleaseDesignerOutlets ()
        {
            if (MyLabel != null) {
                MyLabel.Dispose ();
                MyLabel = null;
            }
        }
    }
}
```

输出口和操作此处声明，可以引用在代码中-但是 **。 designer.cs**不应直接编辑文件。



## <a name="related-links"></a>相关链接

- [WatchTables （示例）](https://developer.xamarin.com/samples/monotouch/watchOS/WatchTables/)
- [WatchKitCatalog （示例）](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [Apple 的表文档](https://developer.apple.com/reference/watchkit/wkinterfacetable)
