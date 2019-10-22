---
title: Xamarin CarouselView 简介
description: CarouselView 是一个视图，用于以可滚动的布局显示数据，用户可在此查看项的集合。
ms.prod: xamarin
ms.assetid: 2a96e4bd-c29b-4658-bb4c-ab00872b0f8f
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/08/2019
ms.openlocfilehash: 2fe4d984f36880493a9a04d99b63876551366477
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "72696977"
---
# <a name="xamarinforms-carouselview-introduction"></a>Xamarin CarouselView 简介

![](~/media/shared/preview.png "This API is currently pre-release")

[`CarouselView`](xref:Xamarin.Forms.CarouselView)是一种用于在可滚动的布局中呈现数据的视图，用户可在此视图中通过一系列项进行切换。 默认情况下，`CarouselView` 会以水平方向显示其项。 屏幕上将显示一项，其中包含滑动手势，导致在项集合中向前和向后导航。

[`CarouselView`](xref:Xamarin.Forms.CarouselView)在 Xamarin. Forms 4.3 中提供。 但是，它当前是实验性的，只能通过将以下代码行添加到 iOS 上的 `AppDelegate` 类，或在调用 `Forms.Init` 之前添加到 Android 上的 `MainActivity` 类：

```csharp
Forms.SetFlags("CarouselView_Experimental");
```

> [!IMPORTANT]
> [`CarouselView`](xref:Xamarin.Forms.CarouselView)在 IOS 和 Android 上可用，但某些功能可能只在通用 Windows 平台部分可用。

[`CarouselView`](xref:Xamarin.Forms.CarouselView)通过[`CollectionView`](xref:Xamarin.Forms.CollectionView)来共享它的许多实现。 但这两个控件具有不同的用例。 `CollectionView` 通常用于显示任意长度的数据列表，而 `CarouselView` 通常用于突出显示有限长度列表中的信息。
