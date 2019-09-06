---
title: Xamarin 中的 watchOS 表控件
description: 本文档介绍如何在 Xamarin 中使用 watchOS 表控件。 它讨论了如何添加表、添加行控制器、创建和填充行、响应点击等。
ms.prod: xamarin
ms.assetid: 7C14126D-9591-4387-A588-3C4521F11C55
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/17/2017
ms.openlocfilehash: d1e5602643ba5578c4bc2a26e6db2d9f49033469
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291690"
---
# <a name="watchos-table-controls-in-xamarin"></a>Xamarin 中的 watchOS 表控件

WatchOS `WKInterfaceTable`控件比它的 iOS 副本简单得多，但会执行类似的角色。 它创建可具有自定义布局以及响应触控事件的行的滚动列表。

![](table-images/table-list-sml.png "监视表列表")![](table-images/table-detail-sml.png)
<!-- watch image courtesy of http://infinitapps.com/bezel/ -->

## <a name="adding-a-table"></a>添加表

将 "**表**" 控件拖到场景中。 默认情况下，它将如下所示（显示单个未指定的行布局）：

[![](table-images/add-table-sml.png "添加表")](table-images/add-table.png#lightbox)

在**Properties** pad 的 "**名称**" 框中为该表命名，以便在代码中引用它。

## <a name="add-a-row-controller"></a>添加行控制器

该表自动包含单个行，由默认情况下包含**组**控件的行控制器表示。

若要设置行控制器的**类**，请在**文档大纲**中选择该行，并在**Properties** pad 中键入类名：

[![](table-images/add-row-controller-sml.png "在 Properties pad 中输入类名")](table-images/add-row-controller.png#lightbox)

设置行控制器的类后，IDE 将在项目中创建相应C#的文件。 将控件（如标签）拖到行上，并为其指定名称，以便可以在代码中引用它们。




## <a name="create-and-populate-rows"></a>创建和填充行

`SetNumberOfRows`为每行创建行控制器类，使用`Identifier`选择正确的类。 如果为行控制器提供自定义`Identifier`，请将以下代码片段中的**默认值**更改为所用标识符。 当调用并显示该表时`SetNumberOfRows` ，将创建*每一行*的。 `RowController`

```csharp
myTable.SetNumberOfRows ((nint)rows.Count, "default");
    // loads row controller by identifier
```

> [!IMPORTANT]
> 表中的行不会像在 iOS 中那样进行虚拟化。 尝试限制行数（Apple 建议小于20）。

创建行后，需要填充每个单元（就像`GetCell`在 iOS 中执行的操作）。 [WatchTables 示例](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchtables)中的此代码片段更新每行中的标签

```csharp
for (var i = 0; i < rows.Count; i++) {
    var elementRow = (RowController)myTable.GetRowController (i);
    elementRow.myRowLabel.SetText (rows [i]);
}
```

> [!IMPORTANT]
> 使用`SetNumberOfRows` ，然后通过使用`GetRowController`循环，将整个表发送到手表。 在表的后续视图中，如果需要添加或删除特定的行，请`InsertRowsAt`使用`RemoveRowsAt`和以获得更好的性能。


## <a name="respond-to-taps"></a>响应点击

您可以通过两种不同的方式响应行选择：

- 在接口控制器上实现方法，或`DidSelectRow`
- 在情节提要上创建 segue，并`GetContextForSegue`在需要行选择以打开另一个场景时实现。

### <a name="didselectrow"></a>DidSelectRow

若要以编程方式处理行选择`DidSelectRow` ，请实现方法。 若要打开新的场景， `PushController`请使用和传递场景的标识符和要使用的数据上下文：

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

将情节提要上的 segue 从表行拖至另一个场景（拖动时按住**控制**键）。
请确保选择 segue 并在**Properties** pad 中为其指定一个标识符（如`secondLevel`以下示例中所示）。

在接口控制器中，实现`GetContextForSegue`方法并返回应提供给 segue 提供的场景的数据上下文。

```csharp
public override NSObject GetContextForSegue (string segueIdentifier, WKInterfaceTable table, nint rowIndex)
{
    if (segueIdentifier == "secondLevel") {
        return new NSString (rows[(int)rowIndex]);
    }
    return null;
}
```

此数据将以其`Awake`方法传递给目标情节提要场景。

## <a name="multiple-row-types"></a>多行类型

默认情况下，table 控件具有可设计的单行类型。 若要添加更多行 "模板"，请使用**Properties** pad 中的 "**行**" 框创建更多行控制器：

![](table-images/prototype-rows1.png "设置原型行的数目")

将**Rows**属性设置为**3**将创建额外的行占位符，以便您将控件拖放到中。 对于每一行，在**Properties** pad 中设置**类**名称，以确保行控制器类已创建。

![](table-images/prototype-rows2.png "设计器中的原型行")

若要填充具有不同行类型的表， `SetRowTypes`请使用方法来指定要用于表中每一行的行控制器类型。 使用行的标识符来指定应将哪个行控制器用于每一行。

此数组中的元素数应与表中预期的行数相匹配：

```csharp
myTable.SetRowTypes (new [] {"type1", "default", "default", "type2", "default"});
```

当使用多个行控制器填充表时，需要在填充 UI 时跟踪所需的类型：

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


## <a name="vertical-detail-paging"></a>垂直详细信息分页

watchOS 3 为表引入了一项新功能：滚动浏览与每行相关的详细信息页，无需返回到表并选择其他行。 详细信息屏幕可以通过向上和向下轻扫，或使用 Digital Crown 进行滚动。

![](table-images/table-scroll-sml.png "垂直详细信息分页示例")![](table-images/table-detail-sml.png)

> [!IMPORTANT]
> 此功能当前仅可通过在 Xcode Interface Builder 中编辑情节提要来使用。

若要启用此功能，请`WKInterfaceTable`在设计图面上选择并勾选**垂直详细信息分页**选项：

![](table-images/vertical-detail-paging-sml.png "选择垂直详细信息分页选项")

如[Apple 所述](https://developer.apple.com/reference/watchkit/wkinterfacetable#1682023)，表导航必须使用 segue 才能使用分页功能。 改`PushController`为重新编写使用 segue 的所有现有代码。

<a name="add_row_controller" />

## <a name="appendix-row-controller-code-example"></a>附录：行控制器代码示例

在设计器中创建行控制器时，IDE 将自动创建两个代码文件。 下面显示了这些生成的文件中的代码以供参考。

首先将为类命名，例如**RowController.cs**，如下所示：

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

另一个**designer.cs**文件是分部类定义，其中包含在设计器图面上创建的插座和操作，例如，具有一个`WKInterfaceLabel`控件的示例：

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

然后，可以在代码中引用此处声明的插座和操作，但不应直接编辑**designer.cs**文件。



## <a name="related-links"></a>相关链接

- [WatchTables （示例）](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchtables)
- [WatchKitCatalog （示例）](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Apple 的表文档](https://developer.apple.com/reference/watchkit/wkinterfacetable)
