---
title: Xamarin.Forms CollectionView 简介
description: CollectionView 是灵活且高性能视图来显示数据使用不同的布局规范的列表。
ms.prod: xamarin
ms.assetid: 5C08F687-B9E6-4CE4-8726-F287F6D0B6A7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: 23de838a395b68f656f95c5665dc97a1d928bece
ms.sourcegitcommit: 9d90a26cbe13ebd106f55ba4a5445f28d9c18a1a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2019
ms.locfileid: "65054647"
---
# <a name="xamarinforms-collectionview-introduction"></a>Xamarin.Forms CollectionView 简介

![](~/media/shared/preview.png "此 API 是当前预发布版本")

[![下载示例](~/media/shared/download.png)下载示例](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)

`CollectionView` 用于呈现数据的列表视图使用不同的布局规范。 它旨在提供更灵活和高性能替代方法[ `ListView` ](xref:Xamarin.Forms.ListView)。 例如，下面的屏幕截图显示`CollectionView`，它使用两个列的垂直网格，并且允许多个选择：

[![CollectionView 垂直网格布局，在 iOS 和 Android 上的屏幕截图](introduction-images/verticalgrid-multipleselection.png "CollectionView 与多个所选内容的垂直网格布局")](introduction-images/verticalgrid-multipleselection-large.png#lightbox "具有 CollectionView 垂直网格布局多个所选内容")

`CollectionView` 现已推出 Xamarin.Forms 4.0 预发布版本。 但是，它是当前处于实验阶段并仅可以通过添加以下代码行来使用你`AppDelegate`类在 iOS 上，或为你`MainActivity`类在 Android 上，然后才能调用`Forms.Init`:

```csharp
Forms.SetFlags("CollectionView_Experimental");
```

> [!IMPORTANT]
> `CollectionView` 仅在 iOS 和 Android 上提供。

## <a name="collectionview-and-listview-differences"></a>CollectionView 和 ListView 的差异

虽然`CollectionView`和`ListView`Api 类似，有一些明显的差异：

- `CollectionView` 提供了灵活的布局模式，允许列表或网格中显示垂直或水平地数据。
- `CollectionView` 支持单个和多个选择。
- `CollectionView` 有没有单元格的概念。 相反，数据模板用于在列表中定义的数据的每个项的外观。
- `CollectionView` 自动利用虚拟化提供的基础本机控件。
- `CollectionView` 减少的 API 面[ `ListView` ](xref:Xamarin.Forms.ListView)。 很多属性和事件`ListView`中不存在`CollectionView`。
- `CollectionView` 不包括内置的分隔符。

## <a name="move-from-listview-to-collectionview"></a>将 ListView 移动到之间导航

[`ListView`](xref:Xamarin.Forms.ListView) 在现有的 Xamarin.Forms 实现中实现可迁移到`CollectionView`实现的帮助下表：

| 概念 | ListView API | CollectionView |
|---|---|---|
| 数据 | `ItemsSource` | 一个`CollectionView`通过设置使用数据填充其`ItemsSource`属性。 有关详细信息，请参阅[填充数据 CollectionView](populate-data.md#populate-a-collectionview-with-data)。 |
| 项外观 | `ItemTemplate` | 在每个项的外观`CollectionView`可以通过设置定义`ItemTemplate`属性设置为[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)。 有关详细信息，请参阅[定义项外观](populate-data.md#define-item-appearance)。 |
| 单元 | `TextCell`, `ImageCell`, `ViewCell` | `CollectionView` 有没有单元格的概念。 相反，数据模板用于在列表中定义的数据的每个项的外观。 |
| 行分隔符 | `SeparatorColor`， `SeparatorVisibility` | `CollectionView` 不包括内置的分隔符。 这些可以提供，如果需要，在项模板中。 |
| 选择 | `SelectionMode`， `SelectedItem` | `CollectionView` 支持单个和多个选择。 有关详细信息，请参阅[Xamarin.Forms CollectionView 选择](selection.md)。 |
| 行高 | `HasUnevenRows`， `RowHeight` | 在中`CollectionView`，每个项的行高度由`ItemSizingStrategy`属性。 有关详细信息，请参阅[项大小调整](layout.md#item-sizing)。|
| 缓存 | `CachingStrategy` | `CollectionView` 自动使用虚拟化提供的基础本机控件。 |
| 页眉和页脚 | `Header`, `HeaderElement`, `HeaderTemplate`, `Footer`, `FooterElement`, `FooterTemplate` | 页眉和页脚目前不在支持`CollectionView`，但会在将来的版本中添加。|
| 分组 | `GroupDisplayBinding`, `GroupHeaderTemplate`, `GroupShortNameBinding`, `IsGroupingEnabled` | 中当前不支持分组`CollectionView`，但会在将来的版本中添加。 |
| 下拉以刷新 | `IsPullToRefreshEnabled`, `IsRefreshing`, `RefreshAllowed`, `RefreshCommand`, `RefreshControlColor`, `BeginRefresh()`, `EndRefresh()` | 中当前不支持下拉以刷新`CollectionView`，但会在将来的版本中添加。 |
| 上下文操作 | `ContextActions` | 上下文操作中目前不支持`CollectionView`，但会在将来的版本中添加。 |
| 滚动 | `ScrollTo()` | `CollectionView` 定义`ScrollTo`项滚动到视图的方法。 有关详细信息，请参阅[滚动](scrolling.md)。 |

## <a name="related-links"></a>相关链接

- [CollectionView （示例）](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)
