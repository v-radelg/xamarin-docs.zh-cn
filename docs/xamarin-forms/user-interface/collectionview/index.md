---
title: Xamarin. Forms CollectionView
description: CollectionView 是一种灵活且高性能的视图, 可使用不同的布局规范呈现数据列表。
ms.prod: xamarin
ms.assetid: 2BC9B223-2D5C-4B09-849C-B9D578954557
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/24/2019
ms.openlocfilehash: b3841ed1287c980212ce37078f38f4984393c414
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2019
ms.locfileid: "69888596"
---
# <a name="xamarinforms-collectionview"></a>Xamarin. Forms CollectionView

![](~/media/shared/preview.png "此 API 当前为预发布版本")

## <a name="introductionintroductionmd"></a>[介绍](introduction.md)

[`CollectionView`](xref:Xamarin.Forms.CollectionView)是一个灵活且高性能的视图, 可使用不同的布局规范呈现数据列表。

## <a name="datapopulate-datamd"></a>[Data](populate-data.md)

使用数据填充数据, 方法[`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) `IEnumerable`是将其属性设置为任何实现的集合。 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 可以通过将[`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate)属性设置[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)为来定义列表中每个项的外观。

## <a name="layoutlayoutmd"></a>[布局](layout.md)

默认情况下, [`CollectionView`](xref:Xamarin.Forms.CollectionView)会在垂直列表中显示其项。 但是, 可以指定垂直和水平列表以及网格。

## <a name="selectionselectionmd"></a>[选择](selection.md)

默认情况下[`CollectionView`](xref:Xamarin.Forms.CollectionView) , 选定内容处于禁用状态。 但是, 可以启用单个和多个选择。

## <a name="empty-viewsemptyviewmd"></a>[空视图](emptyview.md)

在[`CollectionView`](xref:Xamarin.Forms.CollectionView)中, 可以指定一个空视图, 在没有可显示的数据时向用户提供反馈。 空视图可以是字符串、视图或多个视图。

## <a name="scrollingscrollingmd"></a>[滚动](scrolling.md)

当用户 swipes 启动滚动时, 可以控制滚动的结束位置, 以便完全显示项。 此外, [`CollectionView`](xref:Xamarin.Forms.CollectionView)还定义了[`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*)两种方法, 这些方法以编程方式将项滚动到视图中。 其中一个重载将指定索引处的项滚动到视图中, 而另一个重载将指定项滚动到视图中。

## <a name="groupinggroupingmd"></a>[分组](grouping.md)

[`CollectionView`](xref:Xamarin.Forms.CollectionView)可以通过将其`IsGrouped`属性设置为, `true`来显示正确分组的数据。
