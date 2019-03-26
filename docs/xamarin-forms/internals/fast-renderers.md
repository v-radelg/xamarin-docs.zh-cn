---
title: Xamarin.Forms 快速呈现器
description: 本文介绍了快速呈现器，减少的通货膨胀和呈现成本在 Android 上 Xamarin.Forms 控件通过平展生成的本机控件层次结构。
ms.prod: xamarin
ms.assetid: 097f87f2-d891-4f3c-be02-fb7d195a481a
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2017
ms.openlocfilehash: 9a40644df6abcbbcc327b1b0c2dcb26c2dbc4db5
ms.sourcegitcommit: 247a6d00a95fd7f4cf918d923e5f357c8db56761
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/25/2019
ms.locfileid: "58420205"
---
# <a name="xamarinforms-fast-renderers"></a>Xamarin.Forms 快速呈现器

![预览](~/media/shared/preview.png)

_本文介绍了快速的输出，这些程序 （Xamarin.Forms 2.4 中添加），可将减少的通货膨胀和呈现成本在 Android 上 Xamarin.Forms 控件平展生成的本机控件层次结构。_

一直以来，大部分在 Android 上的原始控件呈现器组成两个视图：

- 一个本机控件，如`Button`或`TextView`。
- 容器`ViewGroup`用于处理某些布局工作、 手势处理和其他任务。

但是，此方法具有性能暗示，为每个逻辑控件，这会导致更复杂的可视化树需要更多内存和处理，以在屏幕上呈现的详细信息创建两个视图。

快速呈现器减少到一个视图的通货膨胀和呈现成本的 Xamarin.Forms 控件。 因此，而不被创建两个视图，并将它们添加到视图树，创建只有一个。 通过创建更少的对象，这又意味着不太复杂的视图树，和更低的内存使用情况 （这也会导致较少的垃圾回收暂停），这可以提高性能。

快速呈现器是可用于在 Android 上 Xamarin.Forms 2.4 中的以下控件：

- [`Button`](xref:Xamarin.Forms.Button)
- [`Image`](xref:Xamarin.Forms.Image)
- [`Label`](xref:Xamarin.Forms.Label)
- [`Frame`](xref:Xamarin.Forms.Frame)

就功能而言，这些快速呈现器是对原始的呈现器没有什么不同。 但是，当前处在试验阶段，仅可以通过添加以下代码行来使用你`MainActivity`类，然后调用`Forms.Init`:

```csharp
Forms.SetFlags("FastRenderers_Experimental");
```

> [!NOTE]
> 快速呈现器只是适用于应用程序兼容性 Android 后端，因此将忽略此设置之前应用程序兼容性活动。

对于每个应用程序，取决于布局的复杂性而异的性能改进。 例如，在滚动浏览时可能有性能提升了 x2 [ `ListView` ](xref:Xamarin.Forms.ListView)包含数千行数据，其中每个行中的单元格的使用快速呈现器的控件构成，这会导致以可视方式更顺畅地滚动。


## <a name="related-links"></a>相关链接

- [自定义呈现器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
