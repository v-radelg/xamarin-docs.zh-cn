---
title: Xamarin.Forms 布局
description: Xamarin.Forms 布局用于将用户界面控件组合到可视结构。 本文列出了包含在 Xamarin.Forms 中的布局。
ms.prod: xamarin
ms.assetid: F4180997-BA21-453A-9958-D1E2940DF050
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/21/2018
ms.openlocfilehash: 4747ce6555a6440c687dc3d239d75307f68683ca
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76724480"
---
# <a name="xamarinforms-layouts"></a>Xamarin.Forms 布局

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)

_Xamarin. 窗体布局用于将用户界面控件组成可视结构。_

Xamarin 中的[`Layout`](xref:Xamarin.Forms.Layout)和[`Layout<T>`](xref:Xamarin.Forms.Layout`1)类是用作视图和其他布局的容器的视图的专用化子类型。 `Layout` 类本身派生自[`View`](views.md)。 `Layout` 导数通常包含在 Xamarin. Forms 应用程序中设置子元素位置和大小的逻辑。

[![Xamarin Forms 布局类型](layouts-images/layouts-sml.png "Xamarin Forms 布局类型")](layouts-images/layouts.png#lightbox "Xamarin Forms 布局类型")

派生自 `Layout` 的类可以分为两个类别：

## <a name="layouts-with-single-content"></a>具有单一内容布局

这些类派生自[`Layout`](xref:Xamarin.Forms.Layout)，后者定义[`Padding`](xref:Xamarin.Forms.Layout.Padding)和[`IsClippedToBounds`](xref:Xamarin.Forms.Layout.IsClippedToBounds)属性。

<a name="contentView" />

### <a name="contentview"></a>ContentView

|     |     |
| --- | --- |
| [`ContentView`](xref:Xamarin.Forms.ContentView)包含使用[`Content`](xref:Xamarin.Forms.ContentView.Content)属性设置的单个子级。 `Content` 属性可以设置为任何 `View` 导数，包括其他 `Layout` 派生。 `ContentView` 主要用作结构元素，用作[`Frame`](#frame)的基类。<br /><br />[API 文档](xref:Xamarin.Forms.ContentView) / [指南](~/xamarin-forms/user-interface/layouts/contentview.md) / [示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-contentviewdemos/) | [![ContentView 示例](layouts-images/ContentView.png "ContentView 示例")](layouts-images/ContentView-Large.png#lightbox "ContentView 示例")<br />此页的代码 / [XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ContentViewDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ContentViewDemoPage.cs) |
|     |     |

<a named="frame" />

### <a name="frame"></a>帧

|     |     |
| --- | --- |
| [`Frame`](xref:Xamarin.Forms.Frame)类派生自[`ContentView`](#contentView)并在其子级周围显示一个边框或框架。 `Frame` 类的[`Padding`](xref:Xamarin.Forms.Layout.Padding)默认值为20，还定义[`BorderColor`](xref:Xamarin.Forms.Frame.BorderColor)、 [`CornerRadius`](xref:Xamarin.Forms.Frame.CornerRadius)和[`HasShadow`](xref:Xamarin.Forms.Frame.HasShadow)属性。<br /><br />[API 文档](xref:Xamarin.Forms.Frame) / [指南](~/xamarin-forms/user-interface/layouts/frame.md) / [示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-frame/) | [![框架示例](layouts-images/Frame.png "框架示例")](layouts-images/Frame-Large.png#lightbox "框架示例")<br />此页的代码 / [XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/FrameDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/FrameDemoPage.cs) |
|     |     |

<a name="scrollView" />

### <a name="scrollview"></a>ScrollView

|     |     |
| --- | --- |
| [`ScrollView`](xref:Xamarin.Forms.ScrollView)可以滚动其内容。 将[`Content`](xref:Xamarin.Forms.ScrollView.Content)属性设置为视图或布局太大，无法在屏幕上容纳。 （`ScrollView` 的内容通常是一个[`StackLayout`](#stackLayout)。）设置[`Orientation`](xref:Xamarin.Forms.ScrollView.Orientation)属性，以指示滚动应为垂直、水平还是同时为两者。<br /><br />[API 文档](xref:Xamarin.Forms.ScrollView) / [指南](~/xamarin-forms/user-interface/layouts/scroll-view.md) / [示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout) | [![ScrollView 示例](layouts-images/ScrollView.png "ScrollView 示例")](layouts-images/ScrollView-Large.png#lightbox "ScrollView 示例")<br />此页的代码 / [XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ScrollViewDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ScrollViewDemoPage.cs) |
|     |     |

### <a name="templatedview"></a>TemplatedView

|     |     |
| --- | --- |
| [`TemplatedView`](xref:Xamarin.Forms.TemplatedView)使用控件模板显示内容，并且是[`ContentView`](#contentView)的基类。<br /><br />[API 文档](xref:Xamarin.Forms.TemplatedView) / [指南](~/xamarin-forms/app-fundamentals/templates/control-template.md) | [![TemplatedView 示例](layouts-images/TemplatedView.png "TemplatedView 示例")](layouts-images/TemplatedView.png#lightbox "TemplatedView 示例") |
|     |     |

### <a name="contentpresenter"></a>ContentPresenter

|     |     |
| --- | --- |
| [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter)是模板化视图的布局管理器，用于在[`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate)中标记要显示的内容。<br /><br />[API 文档](xref:Xamarin.Forms.ContentPresenter) / [指南](~/xamarin-forms/app-fundamentals/templates/control-template.md) | [![System.windows.controls.contentpresenter> 示例](layouts-images/ContentPresenter.png "System.windows.controls.contentpresenter> 示例")](layouts-images/ContentPresenter.png#lightbox "System.windows.controls.contentpresenter> 示例") |
|     |     |

## <a name="layouts-with-multiple-children"></a>使用多个子级的布局

这些类派生自[`Layout<View>`](xref:Xamarin.Forms.Layout`1)。

<a name="stackLayout" />

### <a name="stacklayout"></a>StackLayout

|     |     |
| --- | --- |
| [`StackLayout`](xref:Xamarin.Forms.StackLayout)基于[`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation)属性水平或垂直地定位堆栈中的子元素。 [`Spacing`](xref:Xamarin.Forms.StackLayout.Spacing)属性控制子级之间的间距，默认值为6。<br /><br />[API 文档](xref:Xamarin.Forms.StackLayout) / [指南](~/xamarin-forms/user-interface/layouts/stack-layout.md) / [示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)| [![StackLayout 示例](layouts-images/StackLayout.png "StackLayout 示例")](layouts-images/StackLayout-Large.png#lightbox "StackLayout 示例")<br />此页的代码 / [XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/StackLayoutDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/StackLayoutDemoPage.cs) |
|     |     |

<a name="grid" />

### <a name="grid"></a>网格

|     |     |
| --- | --- |
| [`Grid`](xref:Xamarin.Forms.Grid)将其子元素置于行和列的网格中。 使用[附加属性](~/xamarin-forms/xaml/attached-properties.md) [`Row`](xref:Xamarin.Forms.Grid.RowProperty)、 [`Column`](xref:Xamarin.Forms.Grid.ColumnProperty)、 [`RowSpan`](xref:Xamarin.Forms.Grid.RowSpanProperty)和[`ColumnSpan`](xref:Xamarin.Forms.Grid.ColumnSpanProperty)来指示子位置。<br /><br />[API 文档](xref:Xamarin.Forms.Grid) / [指南](~/xamarin-forms/user-interface/layouts/grid.md) / [示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout) | [![网格示例](layouts-images/Grid.png "网格示例")](layouts-images/Grid-Large.png#lightbox "网格示例")<br />此页的代码 / [XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/GridDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/GridDemoPage.cs) |
|     |     |

### <a name="absolutelayout"></a>AbsoluteLayout

|     |     |
| --- | --- |
| [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)相对于其父元素定位位于特定位置的子元素。 使用[附加属性](~/xamarin-forms/xaml/attached-properties.md)来指示子位置[`LayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty)和[`LayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty)。 `AbsoluteLayout` 可用于对视图的位置进行动画处理。<br /><br />[API 文档](xref:Xamarin.Forms.AbsoluteLayout) / [指南](~/xamarin-forms/user-interface/layouts/absolute-layout.md) / [示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout) | [![AbsoluteLayout 示例](layouts-images/AbsoluteLayout.png "AbsoluteLayout 示例")](layouts-images/AbsoluteLayout-Large.png#lightbox "AbsoluteLayout 示例")<br />此页的代码 / 具有[代码隐藏](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/AbsoluteLayoutDemoPage.xaml.cs)的[XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/AbsoluteLayoutDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/AbsoluteLayoutDemoPage.cs) |
|     |     |

### <a name="relativelayout"></a>RelativeLayout

|     |     |
| --- | --- |
| [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)相对于 `RelativeLayout` 本身或同级定位子元素。 使用设置为[`Constraint`](xref:Xamarin.Forms.Constraint)和[`BoundsConstraint`](xref:Xamarin.Forms.Constraint)类型的对象的[附加属性](~/xamarin-forms/xaml/attached-properties.md)来指示子的位置。<br /><br />[API 文档](xref:Xamarin.Forms.RelativeLayout) / [指南](~/xamarin-forms/user-interface/layouts/relative-layout.md) / [示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout) | [![RelativeLayout 示例](layouts-images/RelativeLayout.png "RelativeLayout 示例")](layouts-images/RelativeLayout-Large.png#lightbox "RelativeLayout 示例")<br />此页的代码 / [XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/RelativeLayoutDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/RelativeLayoutDemoPage.cs) |
|     |     |

### <a name="flexlayout"></a>FlexLayout

|     |     |
| --- | --- |
| [`FlexLayout`](xref:Xamarin.Forms.FlexLayout)基于 "CSS[挠性 Box" 布局模块](https://www.w3.org/TR/css-flexbox-1/)，通常称为 " _flex 布局_" 或 _"弹性"_ 。 `FlexLayout` 定义了六个可绑定属性和五个附加的可绑定属性，这些属性允许用多个对齐和方向选项来堆叠或包装子元素。<br /><br />[API 文档](xref:Xamarin.Forms.FlexLayout) / [指南](~/xamarin-forms/user-interface/layouts/flex-layout.md) / [示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos) | [![FlexLayout 示例](layouts-images/FlexLayout.png "FlexLayout 示例")](layouts-images/FlexLayout-Large.png#lightbox "FlexLayout 示例")<br />此页的代码 / [XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/FlexLayoutDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/FlexLayoutDemoPage.cs) |
|     |     |

## <a name="related-links"></a>相关链接

- [Xamarin FormsGallery 示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Xamarin.Forms 示例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Xamarin.Forms API 文档](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
