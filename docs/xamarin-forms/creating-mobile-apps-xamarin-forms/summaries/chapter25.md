---
title: 第 25 章的摘要。 页类型
description: 用 Xamarin 创建移动应用程序：第 25 章的摘要。 页类型
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: D1D348F2-6A44-4781-ADCE-A0B7BB9AEF89
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: b86f2d7216a6344b14fc4d8c538ea68871eda5ae
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70760535"
---
# <a name="summary-of-chapter-25-page-varieties"></a>第 25 章的摘要。 页类型

[![下载示例](~/media/shared/download.png)下载示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25)

到目前为止您已了解两个类派生自`Page`:`ContentPage`和`NavigationPage`。 本章提供了其他两个：

- [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) 管理两个页面、 一个母版和详细信息
- [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 管理多个通过选项卡访问的子页面

这些类型的页面提供更高级的导航选项比`NavagationPage`中所述[第 24 章。页面导航](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter24.md)。

## <a name="master-and-detail"></a>母版和详细信息

[ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage)定义的类型的两个属性`Page`: [ `Master` ](xref:Xamarin.Forms.MasterDetailPage.Master)并[ `Detail` ](xref:Xamarin.Forms.MasterDetailPage.Detail)。 通常设置为这些属性的每个`ContentPage`。 `MasterDetailPage`显示，并将这两页之间进行切换。

有两种基本方法可用来切换这两页：

- *拆分*母版和详细信息所在的并排显示
- *弹出框*其中的详细信息页介绍了或部分介绍了主页面上

有多个变体*弹出框*方法 (*幻灯片*，*重叠*，以及*交换*)，但它们通常是平台依赖。 可以设置[ `MasterDetailBehavior` ](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior)的属性`MasterDetailPage`成员[ `MasterBehavior` ](xref:Xamarin.Forms.MasterBehavior)枚举：

- [`Default`](xref:Xamarin.Forms.MasterBehavior.Default)
- [`Split`](xref:Xamarin.Forms.MasterBehavior.Split)
- [`SplitOnLandscape`](xref:Xamarin.Forms.MasterBehavior.SplitOnLandscape)
- [`SplitOnPortrait`](xref:Xamarin.Forms.MasterBehavior.SplitOnPortrait)
- [`Popover`](xref:Xamarin.Forms.MasterBehavior.Popover)

但是，此属性不起在手机上。 手机始终具有一个弹出框行为。 仅平板电脑和桌面 windows 可以拆分行为。

### <a name="exploring-the-behaviors"></a>浏览行为

[ **MasterDetailBehaviors** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MasterDetailBehaviors)示例，可试验不同的设备上的默认行为。 该程序包含两个单独`ContentPage`派生类用于母版和详细信息 (与`Title`属性设置的同时)，和另一个类派生自`MasterDetailPage`，将它们合并。 详细信息页括在`NavigationPage`因为 UWP 程序没有它不会起作用。

在 Windows 8.1 和 Windows Phone 8.1 平台需要一个位图，设置为`Icon`主控页的属性。

### <a name="back-to-school"></a>重返校园

[ **SchoolAndDetail** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/SchoolAndDetail)示例采用稍有不同的方法，构造程序，用于显示来自学生[ **SchoolOfFineArt**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt)库。

`Master`并`Detail`与可视化树中定义属性[SchoolAndDetailPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/SchoolAndDetail/SchoolAndDetail/SchoolAndDetail/SchoolAndDetailPage.xaml)文件，它派生自`MasterDetailPage`。 此安排可进行数据绑定来设置之间的母版和详细信息页。

XAML 文件还设置[ `IsPresented` ](xref:Xamarin.Forms.MasterDetailPage.IsPresented)的属性`MasterDetailPage`到`True`。 这将导致在启动时; 要显示的母版页默认情况下显示的详细信息页。 [SchoolAndDetailPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/SchoolAndDetail/SchoolAndDetail/SchoolAndDetail/SchoolAndDetailPage.xaml.cs)文件中设置`IsPresented`到`false`从选定的项时`ListView`母版页中。 随即显示的详细信息页：

[![学校和详细信息的三个屏幕截图](images/ch25fg09-small.png "详细信息页上，从 MasterDetailPage")](images/ch25fg09-large.png#lightbox "从 MasterDetailPage 的详细信息页")

### <a name="your-own-user-interface"></a>用户界面

Xamarin.Forms 提供的用户界面，用于在母版和详细信息视图之间切换，尽管你可以提供您自己。 为此，请执行以下操作：

- 设置[ `IsGestureEnabled` ](xref:Xamarin.Forms.MasterDetailPage.IsGestureEnabled)属性设置为`false`禁用轻扫
- 重写[ `ShouldShowToolbarButton` ](xref:Xamarin.Forms.MasterDetailPage.ShouldShowToolbarButton)方法并返回`false`隐藏在 Windows 8.1 和 Windows Phone 8.1 上的工具栏按钮。

然后，必须提供一种方法来切换 master 和详细信息页中，如所示[ **ColorsDetail** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/ColorsDetails)示例。

[ **MasterDetailTaps** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MasterDetailTaps)示例演示了如何在另一种方法使用`TapGestureRecognizer`的母版和详细信息页上。

## <a name="tabbedpage"></a>TabbedPage

[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)是可以在使用选项卡之间切换的页的集合。 它派生`MultiPage<Page>`并定义任何公共属性或其自己的方法。 [`MultiPage<T>`](xref:Xamarin.Forms.MultiPage`1)但是，未定义属性：

- [`Children`](xref:Xamarin.Forms.MultiPage`1.Children) 类型的属性 `IList<T>`

填写此`Children`页面对象的集合。

另一种方法允许你定义`TabbedPage`子级非常像`ListView`使用自动生成的选项卡式的页这两个属性：

- [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) 类型 `IEnumerable`
- [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) 类型 `DataTemplate`

但是，这种方法不会不好在 iOS 上运行时集合中包含多个几个项。

`MultiPage<T>` 定义两个让你跟踪当前查看的页的多个属性：

- [`CurrentPage`](xref:Xamarin.Forms.MultiPage`1.CurrentPage) 类型的`T`，用于引用页面
- [`SelectedItem`](xref:Xamarin.Forms.MultiPage`1.SelectedItem) 类型的`Object`，用于引用中的对象`ItemsSource`集合

`MultiPage<T>` 此外定义了两个事件：

- [`PagesChanged`](xref:Xamarin.Forms.MultiPage`1.PagesChanged) 当`ItemsSource`集合更改
- [`CurrentPageChanged`](xref:Xamarin.Forms.MultiPage`1.CurrentPageChanged) 查看的页发生更改时

### <a name="discrete-tab-pages"></a>离散的选项卡页

[ **DiscreteTabbedColors** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/DiscreteTabbedColors)示例包含三个不同的方式显示颜色的三个选项卡式页面。 每个选项卡`ContentPage`派生类，然后`TabbedPage`衍生作品[DiscreteTabbedColorsPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/DiscreteTabbedColors/DiscreteTabbedColors/DiscreteTabbedColors/DiscreteTabbedColorsPage.xaml)结合了三个页面。

每个页面中显示的`TabbedPage`，则`Title`选项卡中，指定的文本所需的属性和 Apple 应用商店要求，使用的图标因此`Icon`为 iOS 设置属性：

[![离散选项卡式颜色的三个屏幕截图](images/ch25fg13-small.png "TabbedPage")](images/ch25fg13-large.png#lightbox "TabbedPage")

[ **StudentNotes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/StudentNotes)示例有列出了所有学生的主页。 点击一名学生，这会导航到`TabbedPage`派生类， [ `StudentNotesDataPage` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/StudentNotes/StudentNotes/StudentNotes/StudentNotesDataPage.xaml)，包含三个`ContentPage`对象其可视化树中的其中一个允许该学生输入一些说明。

### <a name="using-an-itemtemplate"></a>使用 ItemTemplate

[ **MultiTabbedColor** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MultiTabbedColors)的示例使用[ `NamedColor` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColor.cs)类[ **Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)库。 [MultiTabbedColorsPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/MultiTabbedColors/MultiTabbedColors/MultiTabbedColors/MultiTabbedColorsPage.xaml)文件中设置`DataTemplate`的属性`TabbedPage`为一个可视化树开头`ContentPage`，其中包含绑定到的属性`NamedColor`（包括绑定到`Title`属性)。

但是，这是在 iOS 上有问题。 可以显示只有几个项，并且没有好方法来为他们提供的图标。

## <a name="related-links"></a>相关链接

- [第 25 章全文 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch25-Apr2016.pdf)
- [第 25 章示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25)
- [母版-详细信息页](~/xamarin-forms/app-fundamentals/navigation/master-detail-page.md)
- [选项卡式的页面](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md)
