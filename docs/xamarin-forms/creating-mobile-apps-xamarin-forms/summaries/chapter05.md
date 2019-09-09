---
title: 第 5 章的摘要。 处理大小
description: 用 Xamarin 创建移动应用程序：第 5 章的摘要。 处理大小
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 486800E9-C09F-4B95-9AC2-C0F8FE563BCF
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: c082bdb10732e42b37511cf050e50f46990a5b5b
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70771145"
---
# <a name="summary-of-chapter-5-dealing-with-sizes"></a>第 5 章的摘要。 处理大小

[![下载示例](~/media/shared/download.png)下载示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05)

> [!NOTE]
> 此页上的说明表明其中 Xamarin.Forms 已脱离一书中介绍的内容的区域。

到目前为止已经遇到在 Xamarin.Forms 中的多个大小：

- IOS 状态条的高度为 20
- `BoxView`默认宽度和高度为 40
- 默认值`Padding`在`Frame`为 20
- 默认值`Spacing`上`StackLayout`为 6
- `Device.GetNamedSize`方法将返回数字的字体大小

这些大小不是像素。 相反，它们是独立识别每个平台的设备无关单位。

## <a name="pixels-points-dps-dips-and-dius"></a>像素、 点、 分发点、 Dip 和 DIUs

在 Apple Mac 和 Microsoft Windows 的历史记录，早期程序员的工作以像素为单位。 但是，更高分辨率显示出现所需的屏幕坐标的更多虚拟化和抽象方法。 在 Mac 领域中，程序员的工作单位*点*、 传统上 1/72 英寸，而使用的 Windows 开发人员*设备无关的单位*(DIUs) 基于 1/96 英寸。

移动设备，但是，通常保留即将对人脸，并且具有较高的分辨率比桌面的屏幕，这意味着可以容忍较高的像素密度。

程序员面向 Apple iPhone 和 iPad 设备继续工作单位*点*，但有 160 个点/英寸。 具体取决于该设备，可能有 1、 2 或 3 个像素到点。

Android 是类似的。 程序员工作单位*密度无关的像素*(dps) 和为一英寸 160 dp 上基于 dps 和像素之间的关系。

Windows 手机和移动设备还已建立暗含接近 160 独立于设备的单位为一英寸的缩放比例。

> [!NOTE]
> Xamarin.Forms 不再支持任何基于 Windows 的电话或移动设备。

总之，面向手机和平板电脑的 Xamarin.Forms 程序员可以假定所有的度量单位基于以下条件：

- 160 单位为一英寸，等效于
- 64 单位为厘米

只读[ `Width` ](xref:Xamarin.Forms.VisualElement.Width)并[ `Height` ](xref:Xamarin.Forms.VisualElement.Height)定义的属性`VisualElement`具有"模拟"的值的默认&ndash;1。 仅当大小元素并将其存储在布局时，才将这些属性可以反映设备无关的单位中的元素的实际大小。 此大小包括任何`Padding`元素上设置但不是`Margin`。

可视元素激发[ `SizeChanged` ](xref:Xamarin.Forms.VisualElement.SizeChanged)事件时其`Width`或`Height`已更改。 [ **WhatSize** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/WhatSize)示例使用此事件来显示该程序的屏幕的大小。

## <a name="metrical-sizes"></a>度量的大小

[ **MetricalBoxView** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/MetricalBoxView)使用[ `WidthRequest` ](xref:Xamarin.Forms.VisualElement.WidthRequest)并[ `HeightRequest` ](xref:Xamarin.Forms.VisualElement.HeightRequest)显示`BoxView`一英寸高，另一个厘米宽。

## <a name="estimated-font-sizes"></a>估计的字体大小

[**纸张**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FontSizes)示例演示如何使用 160 单位为英寸规则中点为单位指定字体大小。 在使用此技术平台的 visual 一致性优于`Device.GetNamedSize`。

## <a name="fitting-text-to-available-size"></a>调整的可用大小的文本

可以通过计算来适应特定矩形内的文本块`FontSize`的`Label`使用以下条件：

- 行距是 120%的字体大小 （在 Windows 平台上 130%)。
- 平均字符宽度为字体大小的 50%。

[ **EstimatedFontSize** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/EstimatedFontSize)示例演示此技术。 此程序已编写之前[ `Margin` ](xref:Xamarin.Forms.View.Margin)属性都不可用，因此它使用[ `ContentView` ](xref:Xamarin.Forms.ContentView)与[ `Padding` ](xref:Xamarin.Forms.Layout.Padding)设置，以模拟边距。

[![估计的字体大小的三个屏幕截图](images/ch05fg07-small.png "文本适应可用大小")](images/ch05fg07-large.png#lightbox "文本适应可用大小")

## <a name="a-fit-to-size-clock"></a>适合大小时钟

[ **FitToSizeClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FitToSizeClock)示例演示如何使用[ `Device.StartTimer` ](xref:Xamarin.Forms.Device.StartTimer(System.TimeSpan,System.Func{System.Boolean}))启动一个计时器，用于定期更新时钟的时间时通知应用程序。 字体大小设置为六分之一页宽，以使显示一样大。

## <a name="accessibility-issues"></a>辅助功能问题

**EstimatedFontSize**程序和**FitToSizeClock**程序都包含一个微妙的缺陷：如果用户在 Android 或 Windows 10 移动版上更改了手机的辅助功能设置，则程序将不再能够根据字号来估算文本的呈现大小。 [ **AccessibilityTest** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/AccessibilityTest)示例展示了此问题。

## <a name="empirically-fitting-text"></a>根据经验调整文本

另一种方法，以适应一个矩形的文本是凭经验计算呈现的文本大小和向上或向下对其进行调整。 中的通讯簿调用程序[ `GetSizeRequest` ](xref:Xamarin.Forms.VisualElement.GetSizeRequest(System.Double,System.Double))上可视元素来获取该元素的所需的大小。 方法已被弃用，并应改为调用程序[ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags))。

有关`Label`，第一个参数应为 （以允许换行） 的容器的宽度，同时第二个参数应设置到`Double.PositiveInfinity`进行不受约束的高度。 [ **EmpiricalFontSize** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/EmpiricalFontSize)示例演示此技术。

## <a name="related-links"></a>相关链接

- [第 5 章全文 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch05-Apr2016.pdf)
- [第 5 章： 示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05)
- [第 5 章：F#示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FS)
