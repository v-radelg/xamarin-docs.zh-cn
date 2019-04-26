---
title: Xamarin.Forms 之间导航
description: CollectionView 是灵活且高性能视图来显示数据使用不同的布局规范的列表。
ms.prod: xamarin
ms.assetid: 2BC9B223-2D5C-4B09-849C-B9D578954557
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/15/2019
ms.openlocfilehash: b22449659d2d4b7791328d53ed2d2b29d405ffc8
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61019991"
---
# <a name="xamarinforms-collectionview"></a>Xamarin.Forms 之间导航

![预览](~/media/shared/preview.png)

> [!IMPORTANT]
> `CollectionView`当前为预览版，并且缺少一些其计划的功能。 此外，API 可能会更改为实现已完成。

`CollectionView` 用于呈现数据的列表视图使用不同的布局规范。 它旨在提供更灵活和高性能替代方法[ `ListView` ](xref:Xamarin.Forms.ListView)。 虽然`CollectionView`和`ListView`Api 类似，有一些明显的差异：

- `CollectionView` 提供了灵活的布局模式，允许列表或网格中显示垂直或水平地数据。
- `CollectionView` 有没有单元格的概念。 相反，数据模板用于在列表中定义的数据的每个项的外观。
- `CollectionView` 自动利用虚拟化提供的基础本机控件。
- `CollectionView` 减少的 API 面[ `ListView` ](xref:Xamarin.Forms.ListView)。 很多属性和事件`ListView`中不存在`CollectionView`。
- `CollectionView` 不包括内置的分隔符。

`CollectionView` 现已推出 Xamarin.Forms 4.0 预发布版本。 但是，它是当前处于实验阶段并仅可以通过添加以下代码行来使用你`AppDelegate`类在 iOS 上，或为你`MainActivity`类在 Android 上，然后才能调用`Forms.Init`:

```csharp
Forms.SetFlags("CollectionView_Experimental");
```

> [!NOTE]
> `CollectionView` 仅在 iOS 和 Android 上提供。

## <a name="populate-collectionview-with-datapopulate-datamd"></a>[用数据填充之间导航](populate-data.md)

一个`CollectionView`通过设置使用数据填充其`ItemsSource`属性设置为任何实现的集合`IEnumerable`。 可以通过设置定义在列表中每个项的外观`ItemTemplate`属性设置为[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)。

## <a name="specify-collectionview-layoutlayoutmd"></a>[指定之间导航布局](layout.md)

默认情况下，`CollectionView`将各个项显示垂直列表中。 但是，可以指定垂直和水平列表和网格。

## <a name="set-collectionview-selection-modeselectionmd"></a>[设置之间导航选择模式](selection.md)

默认情况下，`CollectionView`禁用所选内容。 但是，可以启用单个和多个选择。

## <a name="display-an-emptyview-when-data-is-unavailableemptyviewmd"></a>[数据不可用时显示 EmptyView](emptyview.md)

在`CollectionView`，可以指定空的视图，向用户提供反馈，当没有数据可用于显示时。 一个字符串、 视图或多个视图，可以是空的视图。

## <a name="scroll-an-item-into-viewscrollingmd"></a>[滚动到视图的项](scrolling.md)

时启动一个滚动用户扫，以便完全显示的项可以控制滚动的结束位置。 此外，`CollectionView`定义了两个`ScrollTo`方法，以编程方式滚动到视图的项。 而其他滚动到视图中指定的项的指定索引处的项的重载之一滚动到视图中中。
