---
title: Xamarin. Forms 页面
description: Xamarin. Forms 页面表示跨平台的移动应用程序屏幕。 本文列出了 Xamarin. Forms 中包含的页面。
ms.prod: xamarin
ms.assetid: 9C8C710F-E312-420B-9324-A7A20CEDB7EC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
ms.openlocfilehash: 278256f75d94fe47510ae4d15f12a3ff3a6a2b19
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "69976447"
---
# <a name="xamarinforms-pages"></a>Xamarin. Forms 页面

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery/)

_Xamarin. Forms 页面表示跨平台的移动应用程序屏幕。_

下面描述的所有页面类型都派生自 Xamarin [`Page`](xref:Xamarin.Forms.Page)类。 这些视觉对象占据全部或大部分屏幕。 @No__t_0 对象表示 iOS 中的 `ViewController` 和通用 Windows 平台中的 `Page`。 在 Android 上，每个页面都占用屏幕，如 `Activity`，但 Xamarin. Forms 页面并*不*是 `Activity` 对象。

[![](pages-images/pages-sml.png "Xamarin.Forms Page Types")](pages-images/pages.png#lightbox "Xamarin.Forms Page Types")

## <a name="pages"></a>Pages

Xamarin 支持以下页面类型：

<a name="contentPage" />

### <a name="contentpage"></a>ContentPage

|     |     |
| --- | --- |
| [`ContentPage`](xref:Xamarin.Forms.ContentPage)是最简单且最常见的页面类型。 将[`Content`](xref:Xamarin.Forms.ContentPage.Content)属性设置为单个[`View`](views.md)对象，这通常是[`Layout`](layouts.md) ，例如[`StackLayout`](layouts.md#stackLayout)、 [`Grid`](layouts.md#grid)或[1](layouts.md#scrollView)。<br /><br />[API 文档](xref:Xamarin.Forms.ContentPage) | [![ContentPage 示例](pages-images/ContentPage.png "ContentPage 示例")](pages-images/ContentPage-Large.png#lightbox "ContentPage 示例")<br />此页的代码  / [XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ContentPageDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ContentPageDemoPage.cs) |
|     |     |

### <a name="masterdetailpage"></a>MasterDetailPage

|     |     |
| --- | --- |
| [@No__t_1](xref:Xamarin.Forms.MasterDetailPage)管理信息的两个窗格。 将[`Master`](xref:Xamarin.Forms.MasterDetailPage.Master)属性设置为通常显示列表或菜单的页面。 将[`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail)属性设置为一个页面，该页面显示母版页中选定的项。 [@No__t_1](xref:Xamarin.Forms.MasterDetailPage.IsPresented)属性控制主页面还是详细信息页面是否可见。<br /><br />[API 文档](xref:Xamarin.Forms.MasterDetailPage) / [指南](~/xamarin-forms/app-fundamentals/navigation/master-detail-page.md) / [示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-masterdetailpage) | [![MasterDetailPage 示例](pages-images/MasterDetailPage.png "MasterDetailPage 示例")](pages-images/MasterDetailPage-Large.png#lightbox "MasterDetailPage 示例")<br />此页的代码  /  具有[代码隐藏](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MasterDetailPageDemoPage.xaml.cs)的[XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MasterDetailPageDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/MasterDetailPageDemoPage.cs) |
|     |     |

### <a name="navigationpage"></a>NavigationPage

|     |     |
| --- | --- |
| [@No__t_1](xref:Xamarin.Forms.NavigationPage)使用基于堆栈的体系结构在其他页面之间管理导航。 在应用程序中使用页面导航时，主页的实例应传递到 `NavigationPage` 对象的构造函数。<br /><br />[API 文档](xref:Xamarin.Forms.NavigationPage) / [指南](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md) / [示例 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-hierarchical)、 [2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-passingdata)和[3](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-loginflow)  | [![NavigationPage 示例](pages-images/NavigationPage.png "NavigationPage 示例")](pages-images/NavigationPage-Large.png#lightbox "NavigationPage 示例")<br />此页的代码  /  具有[代码 = 后面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/NavigationPageDemoPage.xaml.cs)的[XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/NavigationPageDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/NavigationPageDemoPage.cs) |
|     |     |

### <a name="tabbedpage"></a>TabbedPage

|     |     |
| --- | --- |
| [`TabbedPage`](xref:Xamarin.Forms.TabbedPage)派生自抽象[`MultiPage`](xref:Xamarin.Forms.MultiPage`1)类，允许使用选项卡在子页之间导航。 将[`Children`](xref:Xamarin.Forms.MultiPage`1.Children)属性设置为一组页面，或将[`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource)属性设置为数据对象的集合，并将[`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate)属性设置为描述如何以可视方式表示每个对象的[`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 。<br /><br />[API 文档](xref:Xamarin.Forms.TabbedPage) / [指南](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md) / [示例 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpage)和[2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpagewithnavigationpage) | [![TabbedPage 示例](pages-images/TabbedPage.png "TabbedPage 示例")](pages-images/TabbedPage-Large.png#lightbox "TabbedPage 示例")<br />此页的代码  / [XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TabbedPageDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TabbedPageDemoPage.cs) |
|     |     |

### <a name="carouselpage"></a>CarouselPage

|     |     |
| --- | --- |
| [`CarouselPage`](xref:Xamarin.Forms.CarouselPage)派生自抽象[`MultiPage`](xref:Xamarin.Forms.MultiPage`1)类，允许通过手指轻扫在子页面之间导航。 将[`Children`](xref:Xamarin.Forms.MultiPage`1.Children)属性设置为[`ContentPage`](#contentPage)对象的集合，或将[`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource)属性设置为数据对象的集合，并将[`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate)属性设置为一个[`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ，用于说明每个对象的视觉对象表示.<br /><br />[API 文档](xref:Xamarin.Forms.CarouselPage) / [指南](~/xamarin-forms/app-fundamentals/navigation/carousel-page.md) / [示例 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-carouselpage)和[2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-carouselpagetemplate) | [![CarouselPage 示例](pages-images/CarouselPage.png "CarouselPage 示例")](pages-images/CarouselPage-Large.png#lightbox "CarouselPage 示例")<br />此页的代码  / [XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CarouselPageDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CarouselPageDemoPage.cs) |
|     |     |

### <a name="templatedpage"></a>TemplatedPage

|     |     |
| --- | --- |
| [`TemplatedPage`](xref:Xamarin.Forms.TemplatedPage)使用控件模板显示全屏内容，并且是[`ContentPage`](#contentPage)的基类。<br /><br />[API 文档](xref:Xamarin.Forms.TemplatedPage) / [指南](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md) | [![TemplatedPage 示例](pages-images/TemplatedPage.png "TemplatedPage 示例")](pages-images/TemplatedPage.png "TemplatedPage 示例") |
|     |     |

## <a name="related-links"></a>相关链接

- [Xamarin FormsGallery 示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Xamarin.Forms 示例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Xamarin.Forms API 文档](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
