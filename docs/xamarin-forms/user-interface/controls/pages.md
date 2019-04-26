---
title: Xamarin.Forms 页面
description: Xamarin.Forms 页呈现跨平台移动应用程序屏幕。 本文列出了在 Xamarin.Forms 中包含的页。
ms.prod: xamarin
ms.assetid: 9C8C710F-E312-420B-9324-A7A20CEDB7EC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
ms.openlocfilehash: ce10a2e6ac91f67f8b4f664c232de2a3c97a5ba6
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61176405"
---
# <a name="xamarinforms-pages"></a>Xamarin.Forms 页面

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/FormsGallery/)

_Xamarin.Forms 页呈现跨平台移动应用程序屏幕。_

如下所述的所有页类型都派生自 Xamarin.Forms [ `Page` ](xref:Xamarin.Forms.Page)类。 这些可视元素占用全部或大部分屏幕。 一个`Page`对象表示`ViewController`在 iOS 和`Page`通用 Windows 平台中。 在 Android 上，每个页面将占用所示的屏幕`Activity`，但按 Xamarin.Forms 页*不*`Activity`对象。

[ ![](pages-images/pages-sml.png "Xamarin.Forms 页面类型")](pages-images/pages.png#lightbox "Xamarin.Forms 页面类型")

## <a name="pages"></a>Pages

Xamarin.Forms 支持以下的页类型：

<a name="contentPage" />

### <a name="contentpage"></a>ContentPage

|     |     |
| --- | --- |
| [`ContentPage`](xref:Xamarin.Forms.ContentPage) 是页面的最简单且最常见的类型。 设置[ `Content` ](xref:Xamarin.Forms.ContentPage.Content)属性设置为单个[ `View` ](views.md)对象，它是最常[ `Layout` ](layouts.md)如[ `StackLayout`](layouts.md#stackLayout)， [ `Grid` ](layouts.md#grid)，或[ `ScrollView` ](layouts.md#scrollView)。<br /><br />[API 文档](xref:Xamarin.Forms.ContentPage) | [![内容页示例](pages-images/ContentPage.png "ContentPage 示例")](pages-images/ContentPage-Large.png#lightbox "ContentPage 示例")<br />[此页的 C# 代码](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ContentPageDemoPage.cs) / [XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ContentPageDemoPage.xaml) |
|     |     |

### <a name="masterdetailpage"></a>MasterDetailPage

|     |     |
| --- | --- |
| 一个[ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage)管理信息的两个窗格。 设置[ `Master` ](xref:Xamarin.Forms.MasterDetailPage.Master)属性设置为一个页面，通常显示为列表或菜单。 设置[ `Detail` ](xref:Xamarin.Forms.MasterDetailPage.Detail)到一个页面，显示来自母版页的选定的项的属性。 [ `IsPresented` ](xref:Xamarin.Forms.MasterDetailPage.IsPresented)属性控制 master 或详细信息页上是否可见。<br /><br />[API 文档](xref:Xamarin.Forms.MasterDetailPage) / [指南](~/xamarin-forms/app-fundamentals/navigation/master-detail-page.md) / [示例](https://developer.xamarin.com/samples/xamarin-forms/Navigation/MasterDetailPage/) | [![MasterDetailPage 示例](pages-images/MasterDetailPage.png "MasterDetailPage 示例")](pages-images/MasterDetailPage-Large.png#lightbox "MasterDetailPage 示例")<br />[此页的 C# 代码](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/MasterDetailPageDemoPage.cs) / [XAML 页面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MasterDetailPageDemoPage.xaml)与[代码隐藏](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MasterDetailPageDemoPage.xaml.cs) |
|     |     |

### <a name="navigationpage"></a>NavigationPage

|     |     |
| --- | --- |
| [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)管理在使用基于堆栈的体系结构的其他页面之间导航。 主页上的实例时应用程序中使用页面导航，应传递给构造函数的`NavigationPage`对象。<br /><br />[API 文档](xref:Xamarin.Forms.NavigationPage) / [指南](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md) / [示例 1](https://developer.xamarin.com/samples/xamarin-forms/Navigation/Hierarchical/)， [2](https://developer.xamarin.com/samples/xamarin-forms/Navigation/PassingData/)，和[3](https://developer.xamarin.com/samples/xamarin-forms/Navigation/LoginFlow/)  | [![NavigationPage 示例](pages-images/NavigationPage.png "NavigationPage 示例")](pages-images/NavigationPage-Large.png#lightbox "NavigationPage 示例")<br />[此页的 C# 代码](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/NavigationPageDemoPage.cs) / [XAML 页面](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/NavigationPageDemoPage.xaml)与[代码背后 =](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/NavigationPageDemoPage.xaml.cs) |
|     |     |

### <a name="tabbedpage"></a>TabbedPage

|     |     |
| --- | --- |
| [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 派生自抽象[ `MultiPage` ](xref:Xamarin.Forms.MultiPage`1)类，并允许使用选项卡页的子之间导航。 设置[ `Children` ](xref:Xamarin.Forms.MultiPage`1.Children)属性设置为一系列页或一组[ `ItemsSource` ](xref:Xamarin.Forms.MultiPage`1.ItemsSource)属性设置为数据对象的集合和[ `ItemTemplate` ](xref:Xamarin.Forms.MultiPage`1.ItemTemplate)属性设置为[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)描述每个对象的直观表示方式。<br /><br />[API 文档](xref:Xamarin.Forms.TabbedPage) / [指南](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md) / [示例 1](https://developer.xamarin.com/samples/xamarin-forms/Navigation/TabbedPage/)和[2](https://developer.xamarin.com/samples/xamarin-forms/Navigation/TabbedPageWithNavigationPage) | [![TabbedPage 示例](pages-images/TabbedPage.png "TabbedPage 示例")](pages-images/TabbedPage-Large.png#lightbox "TabbedPage 示例")<br />[此页的 C# 代码](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TabbedPageDemoPage.cs) / [XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TabbedPageDemoPage.xaml) |
|     |     |

### <a name="carouselpage"></a>CarouselPage

|     |     |
| --- | --- |
| [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) 派生自抽象[ `MultiPage` ](xref:Xamarin.Forms.MultiPage`1)类，并允许在子之间的导航手指轻扫中翻页。 设置[ `Children` ](xref:Xamarin.Forms.MultiPage`1.Children)属性设置为一系列[ `ContentPage` ](#contentPage)对象或一组[ `ItemsSource` ](xref:Xamarin.Forms.MultiPage`1.ItemsSource)属性设置为数据对象的集合和[ `ItemTemplate` ](xref:Xamarin.Forms.MultiPage`1.ItemTemplate)属性设置为[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)描述每个对象的直观表示方式。<br /><br />[API 文档](xref:Xamarin.Forms.CarouselPage) / [指南](~/xamarin-forms/app-fundamentals/navigation/carousel-page.md) / [示例 1](https://developer.xamarin.com/samples/xamarin-forms/Navigation/CarouselPage/)和[2](https://developer.xamarin.com/samples/xamarin-forms/Navigation/CarouselPageTemplate/) | [![CarouselPage 示例](pages-images/CarouselPage.png "CarouselPage 示例")](pages-images/CarouselPage-Large.png#lightbox "CarouselPage 示例")<br />[此页的 C# 代码](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CarouselPageDemoPage.cs) / [XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CarouselPageDemoPage.xaml) |
|     |     |

### <a name="templatedpage"></a>TemplatedPage

|     |     |
| --- | --- |
| [`TemplatedPage`](xref:Xamarin.Forms.TemplatedPage) 显示使用控件模板的全屏幕内容，并为类的基类[ `ContentPage` ](#contentPage)。<br /><br />[API 文档](xref:Xamarin.Forms.TemplatedPage) / [指南](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md) | [![TemplatedPage 示例](pages-images/TemplatedPage.png "TemplatedPage 示例")](pages-images/TemplatedPage.png "TemplatedPage 示例") |
|     |     |

## <a name="related-links"></a>相关链接

- [Xamarin.Forms FormsGallery 示例](https://developer.xamarin.com/samples/FormsGallery/)
- [Xamarin.Forms 示例](https://developer.xamarin.com/samples/xamarin-forms/all/)
- [Xamarin.Forms API 文档](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
