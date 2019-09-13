---
title: Xamarin. Forms CarouselView
description: CarouselView 是一个视图，用于在类似于传送带的布局中呈现数据列表。
ms.prod: xamarin
ms.assetid: 5b673347-cdba-4532-820f-fb5f070c86bc
ms.technology: xamarin-forms
author: pauldipietro
ms.author: padipi
ms.date: 09/09/2019
ms.openlocfilehash: 06d20341053c4f77cb72aac9e1abdc85d9f95fdb
ms.sourcegitcommit: e83035c746f165ee6d03f2e9fd0066ee4f20a9fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2019
ms.locfileid: "70908330"
---
# <a name="xamarinforms-carouselview"></a>Xamarin. Forms CarouselView

![](~/media/shared/preview.png "此 API 当前为预发布版本")

> [!IMPORTANT]
> CarouselView 文档正在开发中，某些链接可能引用 CollectionView 文档。 在大多数情况下，信息应该适用，因为 CarouselView 的性质基于 CollectionView。

## <a name="introductionintroductionmd"></a>[介绍](introduction.md)

[`CarouselView`](xref:Xamarin.Forms.CarouselView)是一个视图，用于在类似于传送带的布局中呈现数据。 其实现基于[`CollectionView`](xref:Xamarin.Forms.CollectionView)的。

## <a name="datacollectionviewpopulate-datamd"></a>[Data](../collectionview/populate-data.md)

使用数据填充数据，方法[`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) `IEnumerable`是将其属性设置为任何实现的集合。 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 可以通过将[`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate)属性设置[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)为来定义列表中每个项的外观。

## <a name="layoutlayoutmd"></a>[布局](layout.md)

默认情况下， [`CarouselView`](xref:Xamarin.Forms.CarouselView)将在水平列表中显示其项。 但是，它还可以访问 CollectionView 的相同布局，包括垂直方向。

## <a name="empty-viewscollectionviewemptyviewmd"></a>[空视图](../collectionview/emptyview.md)

在[`CarouselView`](xref:Xamarin.Forms.CarouselView)中，可以指定一个空视图，在没有可显示的数据时向用户提供反馈。 空视图可以是字符串、视图或多个视图。

## <a name="scrollingcollectionviewscrollingmd"></a>[滚动](../collectionview/scrolling.md)

当用户 swipes 启动滚动时，可以控制滚动的结束位置，以便完全显示项。 此外， [`CarouselView`](xref:Xamarin.Forms.CarouselView)还定义了[`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*)两种方法，这些方法以编程方式将项滚动到视图中。 其中一个重载将指定索引处的项滚动到视图中，而另一个重载将指定项滚动到视图中。
