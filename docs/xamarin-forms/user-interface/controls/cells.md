---
title: Xamarin.Forms 单元格
description: Xamarin.Forms 单元格可以添加到 Listview 和 TableViews。 本文列出了在 Xamarin.Forms 中包含的单元格。
ms.prod: xamarin
ms.assetid: 77DA0C89-35D6-4C09-A072-3ADE53FD56CF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
ms.openlocfilehash: 1e003a80b58f783829f5af3b74801fc3c91c88e9
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68655608"
---
# <a name="xamarinforms-cells"></a>Xamarin.Forms 单元格

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)

_Xamarin.Forms 单元格可以添加到 Listview 和 TableViews。_

一个*单元格*是一个专用的元素，用于在表中的项，描述了应如何呈现列表中的每个项。 [ `Cell` ](xref:Xamarin.Forms.Cell)类派生自[ `Element` ](xref:Xamarin.Forms.Element)，从其[ `VisualElement` ](xref:Xamarin.Forms.Element)也派生。 单元格本身不是可视元素;而是用于创建可视元素的模板。

`Cell` 使用专用于[ `ListView` ](views.md#listView)并[ `TableView` ](views.md#tableView)控件。 若要了解如何使用和自定义单元格，请参阅[ `ListView` ](~/xamarin-forms/user-interface/listview/index.md)并[ `TableView` ](~/xamarin-forms/user-interface/tableview.md)文档。

## <a name="cells"></a>单元格

Xamarin.Forms 支持以下的单元格类型：

<a name="textCell" />

### <a name="textcell"></a>TextCell

|     |     |
| --- | --- |
| 一个[ `TextCell` ](xref:Xamarin.Forms.TextCell)显示一个或两个文本字符串。 设置[ `Text` ](xref:Xamarin.Forms.TextCell.Text)属性和 （可选） [ `Detail` ](xref:Xamarin.Forms.TextCell.Detail)属性设置为这些文本字符串。<br /><br />[API 文档](xref:Xamarin.Forms.TextCell) / [指南](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#TextCell) | [![TextCell 示例](cells-images/TextCell.png "TextCell 示例")](cells-images/TextCell-Large.png#lightbox "TextCell 示例")<br />[此页的 C# 代码](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TextCellDemoPage.cs) / [XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TextCellDemoPage.xaml) |
|     |     |

### <a name="imagecell"></a>ImageCell

|     |     |
| --- | --- |
| [ `ImageCell` ](xref:Xamarin.Forms.ImageCell)显示相同的信息[ `TextCell` ](#textCell)包括与设置一个位图，但[ `Source` ](xref:Xamarin.Forms.Image.Source)属性。<br /><br />[API 文档](xref:Xamarin.Forms.ImageCell) / [指南](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#ImageCell) | [![ImageCell 示例](cells-images/ImageCell.png "ImageCell 示例")](cells-images/ImageCell-Large.png#lightbox "ImageCell 示例")<br />[此页的 C# 代码](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageCellDemoPage.cs) / [XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageCellDemoPage.xaml) |
|     |     |

### <a name="switchcell"></a>SwitchCell

|     |     |
| --- | --- |
| 包含带有属性的[`Text`](xref:Xamarin.Forms.SwitchCell.Text)文本集, 以及最初使用布尔值[`On`](xref:Xamarin.Forms.SwitchCell.On)属性设置的打开/关闭开关。 [`SwitchCell`](xref:Xamarin.Forms.SwitchCell) 处理[ `OnChanged` ](xref:Xamarin.Forms.SwitchCell.OnChanged)事件时通知`On`属性更改。<br /><br />[API 文档](xref:Xamarin.Forms.SwitchCell) / [指南](~/xamarin-forms/user-interface/tableview.md#switchcell) | [![SwitchCell 示例](cells-images/SwitchCell.png "SwitchCell 示例")](cells-images/SwitchCell-Large.png#lightbox "SwitchCell 示例")<br />[此页的 C# 代码](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SwitchCellDemoPage.cs) / [XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwitchCellDemoPage.xaml) |
|     |     |

### <a name="entrycell"></a>EntryCell

|     |     |
| --- | --- |
| [ `EntryCell` ](xref:Xamarin.Forms.EntryCell)定义[ `Label` ](xref:Xamarin.Forms.EntryCell.Label)属性，用于标识该单元格和单行中的可编辑文本[ `Text` ](xref:Xamarin.Forms.EntryCell.Text)属性。 处理[ `Completed` ](xref:Xamarin.Forms.EntryCell.Completed)事件以在用户已完成的文本输入时得到通知。<br /><br />[API 文档](xref:Xamarin.Forms.EntryCell) / [指南](~/xamarin-forms/user-interface/tableview.md#entrycell) | [![EntryCell 示例](cells-images/EntryCell.png "EntryCell 示例")](cells-images/EntryCell-Large.png#lightbox "EntryCell 示例")<br />[此页的 C# 代码](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EntryCellDemoPage.cs) / [XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EntryCellDemoPage.xaml) |
|     |     |


## <a name="related-links"></a>相关链接

- [Xamarin.Forms FormsGallery 示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Xamarin.Forms 示例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Xamarin.Forms API 文档](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
