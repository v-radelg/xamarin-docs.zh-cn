---
title: Xamarin.Forms 快速呈现器
description: 本文介绍了快速呈现器，减少的通货膨胀和呈现成本在 Android 上 Xamarin.Forms 控件通过平展生成的本机控件层次结构。
ms.prod: xamarin
ms.assetid: 097f87f2-d891-4f3c-be02-fb7d195a481a
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/09/2019
ms.openlocfilehash: 861d9e3f898dcd61015d9aca27ae66c3fe72d1a9
ms.sourcegitcommit: 482aef652bdaa440561252b6a1a1c0a40583cd32
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2019
ms.locfileid: "65970725"
---
# <a name="xamarinforms-fast-renderers"></a>Xamarin.Forms 快速呈现器

一直以来，大部分在 Android 上的原始控件呈现器组成两个视图：

- 一个本机控件，如`Button`或`TextView`。
- 容器`ViewGroup`用于处理某些布局工作、 手势处理和其他任务。

但是，此方法具有性能暗示，为每个逻辑控件，这会导致更复杂的可视化树需要更多内存和处理，以在屏幕上呈现的详细信息创建两个视图。

快速呈现器减少到一个视图的通货膨胀和呈现成本的 Xamarin.Forms 控件。 因此，而不被创建两个视图，并将它们添加到视图树，创建只有一个。 通过创建更少的对象，这又意味着不太复杂的视图树，和更低的内存使用情况 （这也会导致较少的垃圾回收暂停），这可以提高性能。

快速呈现器是可用于在 Android 上 Xamarin.Forms 中的以下控件：

- [`Button`](xref:Xamarin.Forms.Button)
- [`Image`](xref:Xamarin.Forms.Image)
- [`Label`](xref:Xamarin.Forms.Label)
- [`Frame`](xref:Xamarin.Forms.Frame)

就功能而言，这些快速呈现器是到旧的呈现器没有什么不同。 及更高版本，Xamarin.Forms 4.0 面向所有应用程序从`FormsAppCompatActivity`默认情况下将使用这些快速呈现器。 呈现器为所有新控件，包括[ `ImageButton` ](xref:Xamarin.Forms.ImageButton)并[ `CollectionView` ](xref:Xamarin.Forms.CollectionView)，使用快速呈现器的方法。

使用快速呈现器时的性能改进而异的每个应用程序，取决于布局的复杂性。 例如，在滚动浏览时可能有性能提升了 x2 [ `ListView` ](xref:Xamarin.Forms.ListView)包含数千行数据，其中每个行中的单元格的使用快速呈现器的控件构成，这会导致以可视方式更顺畅地滚动。

> [!NOTE]
> 为快速呈现器针对旧的呈现器使用所用的相同的方法，可以创建自定义呈现器。 有关详细信息，请参阅[自定义呈现器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)。

## <a name="backwards-compatibility"></a>向后兼容性

快速呈现器可以通过以下方法重写：

1. 通过添加以下代码行来启用旧的呈现器你`MainActivity`类，然后调用`Forms.Init`:

    ```csharp
    Forms.SetFlags("UseLegacyRenderers");
    ```

1. 使用旧的呈现器为目标的自定义呈现器。 任何现有的自定义呈现器将继续使用旧的呈现器函数。
1. 指定一个不同`View.Visual`，如`Material`，使用不同呈现器。 有关 Visual 材料的详细信息，请参阅[Xamarin.Forms 材料 Visual](~/xamarin-forms/user-interface/visual/material-visual.md)。

## <a name="related-links"></a>相关链接

- [自定义呈现器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
