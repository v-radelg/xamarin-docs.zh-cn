---
title: Xamarin CarouselView 简介
description: CarouselView 是一种用于在类似于传送带的布局中呈现数据的视图。
ms.prod: xamarin
ms.assetid: 2a96e4bd-c29b-4658-bb4c-ab00872b0f8f
ms.technology: xamarin-forms
author: pauldipietro
ms.author: padipi
ms.date: 09/09/2019
ms.openlocfilehash: 7979f6ed362c580d9cf80f19b3bc0ea7550ca70c
ms.sourcegitcommit: cf56d2bae34dc0f8e94c2d3d28d5f460d59807bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2019
ms.locfileid: "70985977"
---
# <a name="xamarinforms-carouselview-introduction"></a>Xamarin CarouselView 简介

![](~/media/shared/preview.png "此 API 当前为预发布版本")

[`CarouselView`](xref:Xamarin.Forms.CarouselView)是以类似于传送带的方式呈现信息的视图。

[`CarouselView`](xref:Xamarin.Forms.CarouselView)在 Xamarin. Forms 4.3 中提供。 但是，它当前是实验性的，只能通过将以下代码行添加到`AppDelegate` iOS 上的类，或者添加`MainActivity`到 Android 上的类，然后再调用`Forms.Init`：

```csharp
Forms.SetFlags("CollectionView_Experimental");
```

_注意：在撰写本文时，CarouselView 仍使用 CollectionView 标志来实现其功能。_

> [!IMPORTANT]
> [`CarouselView`](xref:Xamarin.Forms.CarouselView)在 iOS 和 Android 上提供，但某些功能可能仅部分可用通用 Windows 平台。

## <a name="when-to-use-carouselview"></a>何时使用 CarouselView

尽管 CarouselView 是 CollectionView 的许多实现，但其用途更侧重。 尽管 CollectionView 和 CarouselView 的使用由你自行决定，但应用中的传送带通常用于突出显示信息，并且仅限于使用的项目总数。
