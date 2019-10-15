---
title: Xamarin CollectionView 简介
description: CollectionView 是一种灵活且高性能的视图，可使用不同的布局规范呈现数据列表。
ms.prod: xamarin
ms.assetid: 5C08F687-B9E6-4CE4-8726-F287F6D0B6A7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/24/2019
ms.openlocfilehash: 89afb0f2bfe93a5f78b0cd162f2a65e585b54b4b
ms.sourcegitcommit: 43423d4018cc0d4b0b8c98a4b3da0704495eb0cf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2019
ms.locfileid: "72303235"
---
# <a name="xamarinforms-collectionview-introduction"></a>Xamarin CollectionView 简介

![此 API 当前为预发行版](~/media/shared/preview.png)

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView) 是使用不同布局规范显示数据列表的视图。 它旨在为 @no__t 提供更灵活、更[高](xref:Xamarin.Forms.ListView)性能的替代方法。 例如，以下屏幕截图显示了一个 `CollectionView`，它使用两个列垂直网格，并允许多个选择：

[(introduction-images/verticalgrid-multipleselection.png "具有多个选择的 iOS 和 Android CollectionView 垂直网格布局")![上的 CollectionView 垂直网格布局屏幕截图]](introduction-images/verticalgrid-multipleselection-large.png#lightbox "具有多个选定内容的 CollectionView 垂直网格布局")

[`CollectionView`](xref:Xamarin.Forms.CollectionView)在 Xamarin. Forms 4.0 中提供。 但是，它当前是实验性的，只能通过将以下代码行添加到 iOS 上的 @no__t 0 类，或添加到 Android 上的 @no__t 类，或在调用 `Forms.Init` 之前添加到 UWP 上的 `App.xaml.cs`：

```csharp
Forms.SetFlags("CollectionView_Experimental");
```

> [!IMPORTANT]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView)在 IOS 和 Android 上可用，但在通用 Windows 平台上仅[部分可用](https://gist.github.com/hartez/7d0edd4182dbc7de65cebc6c67f72e14)。

## <a name="collectionview-and-listview-differences"></a>CollectionView 和 ListView 差异

尽管[`CollectionView`](xref:Xamarin.Forms.CollectionView)和[@no__t 3](xref:Xamarin.Forms.ListView) api 相似，但有一些明显的区别：

- [`CollectionView`](xref:Xamarin.Forms.CollectionView)具有灵活的布局模型，该模型允许在列表或网格中垂直或水平显示数据。
- [`CollectionView`](xref:Xamarin.Forms.CollectionView)支持单个和多个选择。
- [`CollectionView`](xref:Xamarin.Forms.CollectionView)没有单元的概念。 相反，数据模板用于定义列表中每个数据项的外观。
- [`CollectionView`](xref:Xamarin.Forms.CollectionView)自动利用基础本机控件提供的虚拟化。
- [`CollectionView`](xref:Xamarin.Forms.CollectionView)可减少[`ListView`](xref:Xamarin.Forms.ListView)的 API 图面。 [@No__t](xref:Xamarin.Forms.ListView) @no__t 中的许多属性和事件都不存在于-2 中。
- [`CollectionView`](xref:Xamarin.Forms.CollectionView)不包含内置分隔符。

## <a name="move-from-listview-to-collectionview"></a>从 ListView 移到 CollectionView

现有 Xamarin 中[的 @no__t 1](xref:Xamarin.Forms.ListView)实现。窗体实现可以通过下表的帮助迁移到[@no__t 3](xref:Xamarin.Forms.CollectionView)实现：

| 概念 | ListView API | CollectionView |
|---|---|---|
| Data | `ItemsSource` | 通过设置[`CollectionView`](xref:Xamarin.Forms.CollectionView)的 @no__t 属性，使用数据进行填充。 有关详细信息，请参阅[使用数据填充 CollectionView](populate-data.md#populate-a-collectionview-with-data)。 |
| 项外观 | `ItemTemplate` | 可以通过将 `ItemTemplate` 属性设置为[@no__t](xref:Xamarin.Forms.DataTemplate)，来定义[`CollectionView`](xref:Xamarin.Forms.CollectionView)中每个项的外观。 有关详细信息，请参阅[定义项外观](populate-data.md#define-item-appearance)。 |
| 单元格 | `TextCell`, `ImageCell`, `ViewCell` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)没有单元的概念。 相反，数据模板用于定义列表中每个数据项的外观。 |
| 行分隔符 | `SeparatorColor`， `SeparatorVisibility` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)不包含内置分隔符。 如果需要，可在项模板中提供这些项。 |
| Selection | `SelectionMode`， `SelectedItem` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)支持单个和多个选择。 有关详细信息，请参阅[Xamarin CollectionView 选择](selection.md)。 |
| 行高 | `HasUnevenRows`， `RowHeight` | 在 `CollectionView` 中，每个项的行高由 @no__t 属性确定。 有关详细信息，请参阅[项大小调整](layout.md#item-sizing)。|
| 缓存 | `CachingStrategy` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)自动使用基础本机控件提供的虚拟化。 |
| 页眉和页脚 | `Header`, `HeaderElement`, `HeaderTemplate`, `Footer`, `FooterElement`, `FooterTemplate` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)可以通过 `Header`、`Footer`、`HeaderTemplate` 和 @no__t 属性，来显示滚动列表中的项的页眉和页脚。 有关详细信息，请参阅[页眉和页脚](layout.md#headers-and-footers)。 |
| 分组 | `GroupDisplayBinding`, `GroupHeaderTemplate`, `GroupShortNameBinding`, `IsGroupingEnabled` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)通过将其 @no__t 属性设置为 @no__t，来显示正确分组的数据。 可以通过将 @no__t 0 和 @no__t 属性设置为[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)对象来自定义组头和组尾。 有关详细信息，请参阅[Xamarin CollectionView 分组](grouping.md)。 |
| 请求刷新 | `IsPullToRefreshEnabled`, `IsRefreshing`, `RefreshAllowed`, `RefreshCommand`, `RefreshControlColor`, `BeginRefresh()`, `EndRefresh()` | 请求刷新当前不受 `CollectionView` 的支持，但将在未来版本中添加。 |
| 上下文操作 | `ContextActions` | 当前不支持上下文操作 `CollectionView`，但将在未来版本中添加。 |
| 滚动 | `ScrollTo()` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)定义 @no__t 2 方法，将项滚动到视图中。 有关详细信息，请参阅[滚动](scrolling.md)。 |

## <a name="related-links"></a>相关链接

- [CollectionView （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
