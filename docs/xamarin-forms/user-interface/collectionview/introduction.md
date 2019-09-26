---
title: Xamarin CollectionView 简介
description: CollectionView 是一种灵活且高性能的视图，可使用不同的布局规范呈现数据列表。
ms.prod: xamarin
ms.assetid: 5C08F687-B9E6-4CE4-8726-F287F6D0B6A7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/24/2019
ms.openlocfilehash: 14abf2e7eff64d2e3e9656bf1ca76f4cee615408
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "69986076"
---
# <a name="xamarinforms-collectionview-introduction"></a>Xamarin CollectionView 简介

![此 API 当前为预发行版](~/media/shared/preview.png)

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView) 是使用不同布局规范显示数据列表的视图。 它旨在提供更灵活、更高的性能替代方法[`ListView`](xref:Xamarin.Forms.ListView)。 例如，下面的屏幕截图显示了`CollectionView`一个使用两个垂直网格网格的，它允许多个选择：

[![IOS 和 Android 上的 CollectionView 垂直网格布局的屏幕截图](introduction-images/verticalgrid-multipleselection.png "具有多个选定内容的 CollectionView 垂直网格布局")](introduction-images/verticalgrid-multipleselection-large.png#lightbox "具有多个选定内容的 CollectionView 垂直网格布局")

[`CollectionView`](xref:Xamarin.Forms.CollectionView)在 Xamarin. Forms 4.0 中提供。 但是，它当前是实验性的，只能通过将以下代码行添加到`AppDelegate` iOS 上的类，或者添加`MainActivity`到 Android 上的类，然后再调用`Forms.Init`：

```csharp
Forms.SetFlags("CollectionView_Experimental");
```

> [!IMPORTANT]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView)在 iOS 和 Android 上可用，但在通用 Windows 平台仅[部分可用](https://gist.github.com/hartez/7d0edd4182dbc7de65cebc6c67f72e14)。

## <a name="collectionview-and-listview-differences"></a>CollectionView 和 ListView 差异

[`CollectionView`](xref:Xamarin.Forms.CollectionView)尽管和[`ListView`](xref:Xamarin.Forms.ListView) api 相似，但有一些明显的区别：

- [`CollectionView`](xref:Xamarin.Forms.CollectionView)具有灵活的布局模型，允许在列表或网格中垂直或水平显示数据。
- [`CollectionView`](xref:Xamarin.Forms.CollectionView)支持单个和多个选择。
- [`CollectionView`](xref:Xamarin.Forms.CollectionView)没有单元的概念。 相反，数据模板用于定义列表中每个数据项的外观。
- [`CollectionView`](xref:Xamarin.Forms.CollectionView)自动利用基础本机控件提供的虚拟化。
- [`CollectionView`](xref:Xamarin.Forms.CollectionView)减少的 API 图面[`ListView`](xref:Xamarin.Forms.ListView)。 中的很多属性[`ListView`](xref:Xamarin.Forms.ListView)和事件都不`CollectionView`存在于中。
- [`CollectionView`](xref:Xamarin.Forms.CollectionView)不包含内置分隔符。

## <a name="move-from-listview-to-collectionview"></a>从 ListView 移到 CollectionView

[`ListView`](xref:Xamarin.Forms.ListView)现有 Xamarin 中的实现。窗体实现可以通过[`CollectionView`](xref:Xamarin.Forms.CollectionView)下表的帮助迁移到实现：

| 概念 | ListView API | CollectionView |
|---|---|---|
| 数据 | `ItemsSource` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) 通过`ItemsSource`设置的属性来填充。 有关详细信息，请参阅[使用数据填充 CollectionView](populate-data.md#populate-a-collectionview-with-data)。 |
| 项外观 | `ItemTemplate` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)可以通过[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)将属性设置为来定义中每个项的外观。 `ItemTemplate` 有关详细信息，请参阅[定义项外观](populate-data.md#define-item-appearance)。 |
| 单元 | `TextCell`, `ImageCell`, `ViewCell` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)没有单元的概念。 相反，数据模板用于定义列表中每个数据项的外观。 |
| 行分隔符 | `SeparatorColor`， `SeparatorVisibility` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)不包含内置分隔符。 如果需要，可在项模板中提供这些项。 |
| 选择 | `SelectionMode`， `SelectedItem` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)支持单个和多个选择。 有关详细信息，请参阅[Xamarin CollectionView 选择](selection.md)。 |
| 行高 | `HasUnevenRows`， `RowHeight` | 在中`ItemSizingStrategy` ，每个项的行高由属性确定。 `CollectionView` 有关详细信息，请参阅[项大小调整](layout.md#item-sizing)。|
| 缓存 | `CachingStrategy` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)自动使用基础本机控件提供的虚拟化。 |
| 页眉和页脚 | `Header`, `HeaderElement`, `HeaderTemplate`, `Footer`, `FooterElement`, `FooterTemplate` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)`Header`可以通过`Footer` 、、和`FooterTemplate`属性，提供滚动列表中的项的页眉和页脚。 `HeaderTemplate` 有关详细信息，请参阅[页眉和页脚](layout.md#headers-and-footers)。 |
| 分组 | `GroupDisplayBinding`, `GroupHeaderTemplate`, `GroupShortNameBinding`, `IsGroupingEnabled` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)通过将其`IsGrouped`属性设置为， `true`显示正确分组的数据。 可以通过将`GroupHeaderTemplate`和`GroupFooterTemplate`属性设置为[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)对象来自定义组头和组尾。 有关详细信息，请参阅[Xamarin CollectionView 分组](grouping.md)。 |
| 请求刷新 | `IsPullToRefreshEnabled`, `IsRefreshing`, `RefreshAllowed`, `RefreshCommand`, `RefreshControlColor`, `BeginRefresh()`, `EndRefresh()` | 当前不支持`CollectionView`请求刷新，但会在将来的版本中添加。 |
| 上下文操作 | `ContextActions` | 当前不支持`CollectionView`上下文操作，但将在未来版本中添加。 |
| 滚动 | `ScrollTo()` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)定义`ScrollTo`将项滚动到视图中的方法。 有关详细信息，请参阅[滚动](scrolling.md)。 |

## <a name="related-links"></a>相关链接

- [CollectionView （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
