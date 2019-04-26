---
title: 布局压缩
description: 布局压缩从可视化树中删除指定的布局，以试图提升页面呈现性能。 本文介绍如何启用布局压缩和可以带来的好处。
ms.prod: xamarin
ms.assetid: da9e1b26-9d31-4762-94c3-4039f306b7f2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/13/2017
ms.openlocfilehash: 4609593337cc0d003c5b35ef4570f11e27b14f7b
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61371716"
---
# <a name="layout-compression"></a>布局压缩

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/userinterface/layoutcompression/)

_布局压缩从可视化树中删除指定的布局，以试图提升页面呈现性能。本文介绍如何启用布局压缩和可以带来的好处。_

## <a name="overview"></a>概述

Xamarin.Forms 执行使用递归方法调用的两个序列的布局：

- 在页上，使用的可视化树的顶部开始布局，并将经历的可视化树，以包含每个可视元素在页面上的所有分支。 其他元素的父元素负责大小和位置相对于本身及其子级。
- 失效是依据中的元素在页面上的更改将触发新的布局循环过程。 当他们不再具有正确的大小或位置时，元素将被视为无效。 当它的一个子级发生更改时大小，具有子级的可视化树中的每个元素是向你发出警报。 因此，在可视化树中的元素大小的更改可能导致 ripple 上一级树的更改。

有关 Xamarin.Forms 布局的执行方式的详细信息，请参阅[创建自定义布局](~/xamarin-forms/user-interface/layouts/custom.md)。

布局过程的结果是层次结构的本机控件。 但是，此层次结构包含其他容器呈现器和包装对于平台呈现器，进一步以下嵌套的视图层次结构。 嵌套级别越高，Xamarin.Forms 具有执行，以显示页的工作的值就越大。 对于复杂的布局的视图层次结构可以是嵌套的深度和广泛，具有多个级别。

有关示例，请登录到 Facebook 的示例应用程序中的以下按钮：

![](layout-compression-images/facebook-button.png "Facebook 按钮")

此按钮指定为以下 XAML 视图层次结构的自定义控件：

```xaml
<ContentView ...>
    <StackLayout>
        <StackLayout ...>
            <AbsoluteLayout ...>
                <Button ... />    
                <Image ... />
                <Image ... />
                <BoxView ... />
                <Label ... />
                <Button ... />
            </AbsoluteLayout>
        </StackLayout>
        <Label ... />
    </StackLayout>    
</ContentView>
```

可以检查生成的嵌套的视图层次结构，与[Xamarin Inspector](~/tools/inspector/index.md)。 在 Android 上，嵌套的视图层次结构包含 17 视图：

![](layout-compression-images/no-compression.png "视图层次结构为 Facebook 按钮")

布局压缩，这是适用于 iOS 和 Android 平台上的 Xamarin.Forms 应用程序，旨在来平展嵌套方法从可以提高页面呈现性能的可视化树中删除指定的布局的视图。 传送的性能优势因页面、 正在使用的操作系统的版本和在其运行应用程序的设备的复杂性而异。 不过，在旧设备上实现的性能提升最大。

> [!NOTE]
> 虽然这篇文章重点介绍在 Android 上应用布局压缩的结果，但这同样适用于 iOS。

## <a name="layout-compression"></a>布局压缩

在 XAML 中，可以通过设置启用布局压缩`CompressedLayout.IsHeadless`附加属性设置为`true`布局类上：

```xaml
<StackLayout CompressedLayout.IsHeadless="true">
  ...
</StackLayout>   
```

或者，它可以启用在 C# 中的第一个参数为指定的布局实例`CompressedLayout.SetIsHeadless`方法：

```csharp
CompressedLayout.SetIsHeadless(stackLayout, true);
```

> [!IMPORTANT]
> 布局压缩从可视化树中删除一个布局，因为它不适合布局提供的可视外观，或获取触控输入。 因此，该设置的布局[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)属性 (如[ `BackgroundColor` ](xref:Xamarin.Forms.VisualElement.BackgroundColor)， [ `IsVisible` ](xref:Xamarin.Forms.VisualElement.IsVisible)， [ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation)， [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale)， [ `TranslationX` ](xref:Xamarin.Forms.VisualElement.TranslationX)并[ `TranslationY` ](xref:Xamarin.Forms.VisualElement.TranslationY)或的接受手势，不适合布局压缩。 但是，启用布局压缩布局的设置可视外观的属性，或接受手势，不会导致生成或运行时错误。 相反，将应用布局压缩和可视外观属性和手势识别将以静默方式失败。

对于 Facebook 按钮，可以在三个布局类上启用布局压缩：

```xaml
<StackLayout CompressedLayout.IsHeadless="true">
    <StackLayout CompressedLayout.IsHeadless="true" ...>
        <AbsoluteLayout CompressedLayout.IsHeadless="true" ...>
            ...
        </AbsoluteLayout>
    </StackLayout>
    ...
</StackLayout>  
```

在 Android 上，这会导致 14 视图的嵌套的视图层次结构：

![](layout-compression-images/layout-compression.png "视图层次结构为 Facebook 按钮布局压缩")

与原始的嵌套的视图层次结构的 17 视图相比，这表示 17%的次数减少。 虽然这种减少可能出现不重要，减少对整个页面的视图可以更重要。

### <a name="fast-renderers"></a>快速呈现器

快速呈现器减少的通货膨胀和呈现成本 Android 上 Xamarin.Forms 控件通过平展生成的本机视图层次结构。 通过创建更少的对象，这反过来会导致不太复杂的可视化树和内存使用率，这进一步提高性能。 有关快速呈现器的详细信息，请参阅[快速呈现器](~/xamarin-forms/internals/fast-renderers.md)。

示例应用程序中的 Facebook 按钮，结合使用布局压缩和快速呈现器生成 8 视图的嵌套的视图层次的结构：

![](layout-compression-images/layout-compression-with-fast-renderers.png "Facebook 的按钮与布局压缩和快速呈现器的视图层次结构")

与原始的嵌套的视图层次结构的 17 视图相比，这表示减少了 52%。

示例应用程序包含从实际的应用程序中提取的页。 如果没有布局压缩和快速呈现器，页面将生成 130 视图在 Android 上的嵌套的视图层次结构。 启用快速呈现器和适当的布局类上的布局压缩到 70 视图，降低了 46%的减少的嵌套的视图层次结构。

## <a name="summary"></a>总结

布局压缩从可视化树中删除指定的布局，以试图提升页面呈现性能。 这带来的性能优势因页面复杂性、要使用的操作系统版本以及运行应用的设备而异。 不过，在旧设备上实现的性能提升最大。


## <a name="related-links"></a>相关链接

- [创建自定义布局](~/xamarin-forms/user-interface/layouts/custom.md)
- [快速呈现器](~/xamarin-forms/internals/fast-renderers.md)
- [LayoutCompression （示例）](https://developer.xamarin.com/samples/xamarin-forms/userinterface/layoutcompression/)
