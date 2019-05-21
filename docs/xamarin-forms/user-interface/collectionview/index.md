---
title: Xamarin.Forms 之间导航
description: CollectionView 是灵活且高性能视图来显示数据使用不同的布局规范的列表。
ms.prod: xamarin
ms.assetid: 2BC9B223-2D5C-4B09-849C-B9D578954557
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: 73e68f29c61661019cfc56f8caa26c6a0b1ce4ba
ms.sourcegitcommit: 482aef652bdaa440561252b6a1a1c0a40583cd32
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2019
ms.locfileid: "65971000"
---
# <a name="xamarinforms-collectionview"></a>Xamarin.Forms 之间导航

## <a name="introductionintroductionmd"></a>[介绍](introduction.md)

[ `CollectionView` ](xref:Xamarin.Forms.CollectionView)是灵活且高性能的视图来显示数据使用不同的布局规范的列表。

## <a name="datapopulate-datamd"></a>[Data](populate-data.md)

一个[ `CollectionView` ](xref:Xamarin.Forms.CollectionView)通过设置使用数据填充其[ `ItemsSource` ](xref:Xamarin.Forms.ItemsView.ItemsSource)属性设置为任何实现的集合`IEnumerable`。 可以通过设置定义在列表中每个项的外观[ `ItemTemplate` ](xref:Xamarin.Forms.ItemsView.ItemTemplate)属性设置为[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)。

## <a name="layoutlayoutmd"></a>[布局](layout.md)

默认情况下[ `CollectionView` ](xref:Xamarin.Forms.CollectionView)将各个项显示垂直列表中。 但是，可以指定垂直和水平列表和网格。

## <a name="selectionselectionmd"></a>[选择](selection.md)

默认情况下[ `CollectionView` ](xref:Xamarin.Forms.CollectionView)禁用所选内容。 但是，可以启用单个和多个选择。

## <a name="empty-viewsemptyviewmd"></a>[空的视图](emptyview.md)

在中[ `CollectionView` ](xref:Xamarin.Forms.CollectionView)，可以指定空的视图，向用户提供反馈，当没有数据可用于显示时。 一个字符串、 视图或多个视图，可以是空的视图。

## <a name="scrollingscrollingmd"></a>[滚动](scrolling.md)

时启动一个滚动用户扫，以便完全显示的项可以控制滚动的结束位置。 此外， [ `CollectionView` ](xref:Xamarin.Forms.CollectionView)定义了两个[ `ScrollTo` ](xref:Xamarin.Forms.ItemsView.ScrollTo*)方法，以编程方式滚动到视图的项。 而其他滚动到视图中指定的项的指定索引处的项的重载之一滚动到视图中中。
