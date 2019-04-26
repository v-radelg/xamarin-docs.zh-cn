---
title: 第 24 章的摘要。 页导航
description: 使用 Xamarin.Forms 创建移动应用：第 24 章的摘要。 页导航
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: DDCDB49C-6008-4F72-B095-463EE21D7C23
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 7210cffb3cd348a6bdbf6cee80c4b6cd55553c58
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61333535"
---
# <a name="summary-of-chapter-24-page-navigation"></a>第 24 章的摘要。 页导航

[![下载示例](~/media/shared/download.png)下载示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24)

许多应用程序包含多个用户在其之间导航的页面。 应用程序始终都*主要*页或*家庭*页上，并从那里用户导航到其他页面，用于向后导航堆栈中维护。 中包括其他导航选项[**第 25 章。页上品种**](chapter25.md)。

## <a name="modal-pages-and-modeless-pages"></a>模式页面和无模式页

`VisualElement` 定义[ `Navigation` ](xref:Xamarin.Forms.NavigableElement.Navigation)类型的属性[ `INavigation` ](xref:Xamarin.Forms.INavigation)，其中包括以下两种方法，以导航到新页面：

- [`PushAsync`](xref:Xamarin.Forms.INavigation.PushAsync(Xamarin.Forms.Page))
- [`PushModalAsync`](xref:Xamarin.Forms.INavigation.PushModalAsync(Xamarin.Forms.Page))

这两种方法接受`Page`实例作为自变量和返回`Task`对象。 以下两种方法导航回前一页：

- [`PopAsync`](xref:Xamarin.Forms.INavigation.PopAsync)
- [`PopModalAsync`](xref:Xamarin.Forms.INavigation.PopModalAsync)

如果用户界面具有其自己**回**按钮，则它不是必需的应用程序来调用这些方法 （如 Android 和 Windows 手机执行操作）。

尽管这些方法可从任何`VisualElement`，通常从调用`Navigation`属性与当前`Page`实例。

当用户需要提供的页上某些信息，然后返回到前一页时，应用程序通常使用模式页面。 不是模式对话框的页有时也称为无模式或*分层*。 在页面本身中为 nothing 使它作为模式对话框或无模式;它受到改为用来导航到它的方法。 若要在所有平台上工作，模式页面必须提供其自己的用户界面导航回前一页。

[ **ModelessAndModal** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ModelessAndModal)示例，您可以浏览无模式和模式页之间的差异。 任何使用页面导航应用程序必须将传递到其主页[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)构造函数中，通常在程序的`App`类。 一个好处就是不再需要设置`Padding`适用于 iOS 的页上。

你会发现，对于无模式的页，页上的[ `Title` ](xref:Xamarin.Forms.Page.Title)显示属性。 在 iOS、 Android 和 Windows 平板电脑和桌面平台所有提供的用户界面元素导航回前一页。 课程、 Android 和 Windows phone 设备有一个标准**回**按钮可返回。

模式页面，页面`Title`未显示，并提供没有用户界面元素以返回到前一页。 尽管可以使用 Android 和 Windows phone 标准**回**按钮返回到前一页，在其他平台上的模式页面必须提供其自身的机制返回去。

### <a name="animated-page-transitions"></a>经过动画处理的页面过渡

第二个的布尔参数设置为提供的各种导航方法的替代版本`true`如果所需页面过渡来包含动画：

- [PushAsync](xref:Xamarin.Forms.INavigation.PushAsync(Xamarin.Forms.Page,System.Boolean))
- [PushModalAsync](xref:Xamarin.Forms.INavigation.PushModalAsync(Xamarin.Forms.Page,System.Boolean))
- [PopAsync](xref:Xamarin.Forms.INavigation.PopAsync(System.Boolean))
- [PopModalAsync](xref:Xamarin.Forms.INavigation.PopModalAsync(System.Boolean))

但是，标准的页面导航方法包括动画默认情况下，因此，这些是仅有价值的导航到上启动的特定页 （如结束时的这一章所述） 或 （如中所述提供自己的 entrance 动画时[ **Chapter22。动画**](chapter22.md))。

### <a name="visual-and-functional-variations"></a>可视化和功能的变体

`NavigationPage` 包括两个实例化中的类时，可以设置的属性在`App`方法：

- [`BarBackgroundColor`](xref:Xamarin.Forms.NavigationPage.BarBackgroundColor)
- [`BarTextColor`](xref:Xamarin.Forms.NavigationPage.BarTextColor)

`NavigationPage` 此外包括四个影响特定页在其设置附加可绑定属性：

- [`SetHasBackButton`](xref:Xamarin.Forms.NavigationPage.SetHasBackButton(Xamarin.Forms.Page,System.Boolean)) 和 [`GetHasBackButton`](xref:Xamarin.Forms.NavigationPage.GetHasBackButton(Xamarin.Forms.Page))
- [`SetHasNavigationBar`](xref:Xamarin.Forms.NavigationPage.SetHasNavigationBar(Xamarin.Forms.BindableObject,System.Boolean)) 和 [`GetHasNavigationBar`](xref:Xamarin.Forms.NavigationPage.GetHasNavigationBar(Xamarin.Forms.BindableObject))
- [`SetBackButtonTitle`](xref:Xamarin.Forms.NavigationPage.SetBackButtonTitle(Xamarin.Forms.BindableObject,System.String)) 并[ `GetBackButtonTitle` ](xref:Xamarin.Forms.NavigationPage.GetBackButtonTitle(Xamarin.Forms.BindableObject))适用于仅限 iOS
- [`SetTitleIcon`](xref:Xamarin.Forms.NavigationPage.SetTitleIcon(Xamarin.Forms.BindableObject,Xamarin.Forms.FileImageSource)) 并[ `GetTitleIcon` ](xref:Xamarin.Forms.NavigationPage.GetTitleIcon(Xamarin.Forms.BindableObject))适用于仅 iOS 和 Android

### <a name="exploring-the-mechanics"></a>探索机制

页面导航方法全都是异步，应能与`await`。 完成并不表示页面导航已完成，但仅限安全检查页面导航堆栈。

当一个页面导航到另一个时，第一页通常获取调用其[ `OnDisappearing` ](xref:Xamarin.Forms.Page.OnDisappearing)方法，并第二页获取调用其[ `OnAppearing` ](xref:Xamarin.Forms.Page.OnAppearing)方法。 同样，当一页返回到另一个时，第一页获取调用其`OnDisappearing`方法，并第二页通常获取调用其`OnAppearing`方法。 这些调用 （和调用导航时的异步方法完成） 的顺序是依赖于平台。 使用"一般"中的两个上述语句字是由于 Android 模式页面导航，这些方法调用不会发生。

此外，调用`OnAppearing`和`OnDisappearing`方法不一定表示页面导航。

`INavigation`接口包含两个集合属性，可用于检查导航堆栈：

- [`NavigationStack`](xref:Xamarin.Forms.INavigation.NavigationStack) 类型的`IReadOnlyList<Page>`无模式堆栈
- [`ModalStack`](xref:Xamarin.Forms.INavigation.ModalStack) 类型的`IReadOnlyList<Page>`模式堆栈

它是最安全的方式访问从这些堆栈`Navigation`的属性`NavigationPage`(它应该是`App`类的[ `MainPage` ](xref:Xamarin.Forms.Application.MainPage)属性)。 才是安全的异步页面导航方法完成后检查这些堆栈。 [ `CurrentPage` ](xref:Xamarin.Forms.NavigationPage.CurrentPage)属性的`NavigationPage`不指示当前页的当前页是否模式页面，但指示改为无模式的最后一页。

[ **SinglePageNavigation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/SinglePageNavigation)示例，您可以浏览页面导航和堆栈、 和的页面导航的法律类型：

- 无模式页面可以导航到另一个无模式页面或模式页面
- 模式页面可以仅向另一个模式页面导航

### <a name="enforcing-modality"></a>强制模式

需要从用户获取某些信息时，应用程序使用模式的页面。 用户应禁止前提供该信息返回到以前的页面。 在 iOS 上，很容易地提供**回**按钮，然后仅当用户完成与页时，才启用它。 适用于 Android 和 Windows phone 设备，应用程序应重写，但[ `OnBackButtonPressed` ](xref:Xamarin.Forms.Page.OnBackButtonPressed)方法并返回`true`如果程序已处理**回**按钮本身，如中所示[ **ModalEnforcement** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ModalEnforcement)示例。

[ **MvvmEnforcement** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/MvvmEnforcement)示例演示工作方式的 MVVM 方案。

## <a name="navigation-variations"></a>导航变体

如果特定的模式页面可以导航到多个时间，它应保留的信息，以便用户可以编辑信息，而不用键入所有在再次。 可以通过保留的模式页面，该特定实例来处理此但更好的方法 （尤其是在 iOS 上） 保留视图模型中的信息。

### <a name="making-a-navigation-menu"></a>使导航菜单

[ **ViewGalleryType** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ViewGalleryType)示例演示如何使用`TableView`列表菜单项。 每个项相关联`Type`特定页对象。 选择该项目时，程序实例化页并导航到它。

[![视图库类型的三个屏幕截图](images/ch24fg21-small.png "TableView 列出菜单项")](images/ch24fg21-large.png#lightbox "TableView 列出菜单项")

[ **ViewGalleryInst** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ViewGalleryInst)示例是稍有不同，则菜单包含的每个页面而不是类型的实例。 这可帮助保留每个页面中的信息，但所有页面必须在程序启动时实例都化。

### <a name="manipulating-the-navigation-stack"></a>操作导航堆栈

[**StackManipulation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/StackManipulation)演示几个函数由定义`INavigation`能够让你以结构化的方式处理导航堆栈：

- [`RemovePage`](xref:Xamarin.Forms.INavigation.RemovePage(Xamarin.Forms.Page))
- [`InsertPageBefore`](xref:Xamarin.Forms.INavigation.InsertPageBefore(Xamarin.Forms.Page,Xamarin.Forms.Page))
- [`PopToRootAsync`](xref:Xamarin.Forms.INavigation.PopToRootAsync) 并[ `PopToRootAsync` ](xref:Xamarin.Forms.INavigation.PopToRootAsync(System.Boolean))使用可选的动画

### <a name="dynamic-page-generation"></a>动态页生成

[ **BuildAPage** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/BuildAPage)示例演示如何构建一个在运行时根据用户输入的页。

## <a name="patterns-of-data-transfer"></a>数据传输模式

它通常是页面之间共享数据所需&mdash;将数据传输到导航页，且针对页后，可以将数据返回到调用它的页面。 有几种方法执行此操作。

### <a name="constructor-arguments"></a>构造函数自变量

导航到新页面，时，可以实例化具有允许初始化其自身的页的构造函数自变量的页类。 [ **SchoolAndStudents** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/SchoolAndStudents)示例演示了这。 还有可能用于导航页后，可以有其`BindingContext`通过导航到它的页设置。

### <a name="properties-and-method-calls"></a>属性和方法调用

其余的数据传输示例探索之间页一页导航到另一个页面时来回传递信息的问题。 在这些讨论*家庭*页面会导航到*信息*页，然后必须初始化将信息传输到*信息*页。 *Info*页从用户获取的其他信息，并传输到的信息*家庭*页。

*家庭*中公共方法和属性，可以轻松地访问页面*信息*页上，只要它实例化该页面。 *Info*中公共方法和属性，还可以访问页*家庭*页上，但这可能比较棘手的选择的好时机。 [ **DateTransfer1** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer1)示例执行此任务其`OnDisappearing`重写。 一个缺点是，在*info*页面需要知道的类型*家庭*页。

### <a name="messagingcenter"></a>MessagingCenter

Xamarin.Forms [ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter)类提供了两个页来相互通信的另一种方法。 消息的文本字符串由标识和伴随的任何对象。

想要接收来自特定类型的消息的程序必须订阅它们使用[ `MessagingCenter.Subscribe` ](xref:Xamarin.Forms.MessagingCenter.Subscribe*)和指定的回调函数。 它更高版本可以通过调用取消订阅[ `MessagingCenter.Unsubscribe` ](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*)。 回调函数接收发送从指定类型的具有指定名称通过发送任何消息[ `Send` ](xref:Xamarin.Forms.MessagingCenter.Send*)方法。

[ **DateTransfer2** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer2)程序演示如何使用消息传递的中心，数据传输，但这要求再次*信息*页知道类型*家庭*页。

### <a name="events"></a>事件

该事件是一个类以将信息发送到另一个类，而不必知道该类的类型的历史悠久方法。 在中[ **DateTransfer3** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer3)示例*信息*类定义准备就绪的信息时激发的事件。 但是，没有为方便的位置*家庭*页后，可以分离的事件处理程序。

### <a name="the-app-class-intermediary"></a>应用程序类中介

[ **DateTransfer4** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer4)示例演示如何访问中定义的属性`App`这两类*家庭*页和*信息*页。 这是一个不错的解决方案，但下一节介绍了更好。

### <a name="switching-to-a-viewmodel"></a>切换到 ViewModel

有关的信息，使用 ViewModel*家庭*页并*信息*页后，可以共享信息类的实例。 了这一点[ **DateTransfer5** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer5)示例。

### <a name="saving-and-restoring-page-state"></a>保存和还原页面状态

`App`类中介或 ViewModel 方法非常适用于应用程序必须保存的信息，如果程序进入睡眠状态时*信息*页处于活动状态。 [ **DateTransfer6** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer6)示例演示了这。

## <a name="saving-and-restoring-the-navigation-stack"></a>保存和还原导航堆栈

在一般情况下，进入睡眠状态的多页程序应导航到同一页面时还原。 这意味着此类程序应保存在导航堆栈的内容。 本部分介绍如何自动执行此过程中实现此目的而设计的类。 此类还会调用各个页面，让他们可以保存并还原其页面状态。

[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)库定义一个接口，名为[ `IPersistantPage` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/IPersistentPage.cs)类可实现以保存和还原中项`Properties`字典。

[ `MultiPageRestorableApp` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MultiPageRestorableApp.cs)类**Xamarin.FormsBook.Toolkit**库派生`Application`。 然后可以派生您`App`类从`MultiPageRestorableApp`并执行一些保养工作。

[ **StackRestoreDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/StackRestoreDemo)演示如何使用`MultiPageRestorableApp`。

### <a name="something-like-a-real-life-app"></a>类似于实际的应用

[ **NoteTaker** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/NoteTaker)示例还使用`MultiPageRestorableApp`，并允许进行的输入和编辑的便笺中保存的`Properties`字典。



## <a name="related-links"></a>相关链接

- [第 24 章全文 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch24-Apr2016.pdf)
- [第 24 章示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24)
- [分层导航](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)
- [模式页](~/xamarin-forms/app-fundamentals/navigation/modal.md)
