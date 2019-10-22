---
title: Xamarin. Forms CarouselView
description: CarouselView 是一个视图，用于以可滚动的布局显示数据，用户可在此查看一系列项目中的数据。
ms.prod: xamarin
ms.assetid: 5b673347-cdba-4532-820f-fb5f070c86bc
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/08/2019
ms.openlocfilehash: 816c1b6e4ab497d0ada0f80fa3ad4800912587c3
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "72696989"
---
# <a name="xamarinforms-carouselview"></a>Xamarin. Forms CarouselView

![](~/media/shared/preview.png "This API is currently pre-release")

## <a name="introductionintroductionmd"></a>[介绍](introduction.md)

[@No__t_1](xref:Xamarin.Forms.CarouselView)是一种用于在可滚动的布局中呈现数据的视图，用户可在此视图中通过一系列项进行轻扫。

## <a name="datapopulate-datamd"></a>[Data](populate-data.md)

通过将数据的[`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource)属性设置为实现 `IEnumerable` 的任何集合，使用数据填充[`CarouselView`](xref:Xamarin.Forms.CarouselView) 。 可以通过将[`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate)属性设置为[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)来定义每个项的外观。

## <a name="layoutlayoutmd"></a>[布局](layout.md)

默认情况下， [`CarouselView`](xref:Xamarin.Forms.CarouselView)会在水平列表中显示其项。 但是，它还可以访问 CollectionView 的相同布局，包括垂直方向。

## <a name="interactioninteractionmd"></a>[交换](interaction.md)

可以通过 `CurrentItem` 和 `Position` 属性访问[`CarouselView`](xref:Xamarin.Forms.CarouselView)中当前显示的项。

## <a name="empty-viewsemptyviewmd"></a>[空视图](emptyview.md)

在[`CarouselView`](xref:Xamarin.Forms.CarouselView)中，可以指定一个空视图，在没有可显示的数据时向用户提供反馈。 空视图可以是字符串、视图或多个视图。

## <a name="scrollingscrollingmd"></a>[滚动](scrolling.md)

当用户 swipes 启动滚动时，可以控制滚动的结束位置，以便完全显示项。 此外， [`CarouselView`](xref:Xamarin.Forms.CarouselView)还定义了两个[`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*)方法，这些方法以编程方式将项滚动到视图中。 其中一个重载将指定索引处的项滚动到视图中，而另一个重载将指定项滚动到视图中。
