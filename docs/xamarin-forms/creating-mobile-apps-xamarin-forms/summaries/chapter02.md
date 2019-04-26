---
title: 第 2 章的摘要。 应用剖析
description: 使用 Xamarin.Forms 创建移动应用：第 2 章的摘要。 应用剖析
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 8764EB7D-8331-4CF7-9BE1-26D0DEE9E0BB
author: davidbritch
ms.author: dabritch
ms.date: 07/17/2018
ms.openlocfilehash: 95defd11a9e568d1089cb2f262cb323045b6c247
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61334305"
---
# <a name="summary-of-chapter-2-anatomy-of-an-app"></a>第 2 章的摘要。 应用剖析

[![下载示例](~/media/shared/download.png)下载示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02)

> [!NOTE]
> 此页上的说明表明其中 Xamarin.Forms 已脱离一书中介绍的内容的区域。

在 Xamarin.Forms 应用程序中，占用屏幕空间的对象称为*可视元素*、 通过封装[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)类。 可视元素可以拆分为三个类别对应于这些类：

- [页](xref:Xamarin.Forms.Page)
- [布局](xref:Xamarin.Forms.Layout)
- [视图](xref:Xamarin.Forms.View)

一个`Page`派生占据整个屏幕或几乎整个屏幕。 通常情况下，一个页面的子级是`Layout`派生类来组织子可视元素。 子级`Layout`可以是其他`Layout`类或`View`派生类 (通常称为*元素*)，这是熟悉的对象，如文本、 位图、 滑块、 按钮、 列表框等。

本章演示如何通过在创建应用程序[ `Label` ](xref:Xamarin.Forms.Label)，这是`View`显示文本的派生类。

## <a name="say-hello"></a>假设你好

使用安装 Xamarin 平台，你可以创建新的 Xamarin.Forms 解决方案在 Visual Studio 或 Visual Studio for mac。 [ **Hello** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello)解决方案使用可移植类库的通用代码。

> [!NOTE]
> .NET Standard 库已替换为可移植类库。 本书中的所有示例代码已都转换为使用.NET 标准库。

此示例演示在 Visual Studio 中创建不作任何修改的 Xamarin.Forms 解决方案。 此解决方案由六个项目组成：

- [**Hello**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello)，由其他项目共享的可移植类库 (PCL)
- [**Hello.Droid**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.Droid)，适用于 Android 的应用程序项目
- [**Hello.iOS**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.iOS)，适用于 iOS 的应用程序项目
- [**Hello.UWP**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.UWP)，通用 Windows 平台 （Windows 10 和 Windows 10 移动版） 的应用程序项目
- [**Hello.Windows**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.Windows)，用于 Windows 8.1 的应用程序项目
- [**Hello.WinPhone**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.WinPhone)，适用于 Windows Phone 8.1 应用程序项目

> [!NOTE]
> Xamarin.Forms 不再支持 Windows 8.1、 Windows Phone 8.1 或 Windows 10 移动版，但在 Windows 10 桌面版上运行的 Xamarin.Forms 应用程序。

您可以进行任何这些应用程序项目启动项目，然后生成并在设备或模拟器上运行该程序。

在许多 Xamarin.Forms 程序，您不会修改应用程序项目。 这些通常保持小的存根，只是为了启动该程序。 您的大部分将是侧重点的普遍适用于所有应用程序库。

## <a name="inside-the-files"></a>文件内

显示的视觉对象**Hello**程序定义的构造函数中[ `App` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello/App.cs)类。 `App` 派生自 Xamarin.Forms 类[ `Application` ](xref:Xamarin.Forms.Application)。

> [!NOTE]
> Visual Studio 解决方案模板适用于 Xamarin.Forms 使用 XAML 文件创建一个页面。 直到本书中未涵盖 XAML[第 7 章](chapter07.md)。

**引用**一部分**Hello** PCL 项目中包括以下 Xamarin.Forms 程序集：

- **Xamarin.Forms.Core**
- **Xamarin.Forms.Xaml**
- **Xamarin.Forms.Platform**

**引用**节的五个应用程序项目包括适用于各个平台的其他程序集：

- **Xamarin.Forms.Platform.Android**
- **Xamarin.Forms.Platform.iOS**
- **Xamarin.Forms.Platform.UWP**
- **Xamarin.Forms.Platform.WinRT**
- **Xamarin.Forms.Platform.WinRT.Tablet**
- **Xamarin.Forms.Platform.WinRT.Phone**

> [!NOTE]
> **引用**不再列出程序集，这些项目的部分。 相反，该项目文件包含**PackageReference**标记引用的 Xamarin.Forms NuGet 包。 **引用**Visual Studio 列表中的部分**Xamarin.Forms**包而不是 Xamarin.Forms 程序集。

每个应用程序项目包含调用静态`Forms.Init`中的方法`Xamarin.Forms`命名空间。 此初始化 Xamarin.Forms 库。 不同版本的`Forms.Init`定义为每个平台。 调用此方法可在以下类：

- iOS: [`AppDelegate`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.iOS/AppDelegate.cs)
- Android: [`MainActivity`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)
- UWP: [ `App`类，`OnLaunched`方法](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)

此外，每个平台必须实例化`App`类共享库中的位置。 此错误出现在调用`LoadApplication`为以下几类：

- iOS: [`AppDelegate`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.iOS/AppDelegate.cs)
- Android: [`MainActivity`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)
- UWP: [`MainPage`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.UWP/MainPage.xaml.cs)

否则，这些应用程序项目是正常的"执行任何操作"的程序。

## <a name="pcl-or-sap"></a>PCL 或 SAP？

就可以使用可移植类库 (PCL) 或共享资产项目 (SAP) 中的常见代码创建 Xamarin.Forms 解决方案。 若要创建的 SAP 解决方案，请在 Visual Studio 中选择共享选项。 [ **HelloSap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/HelloSap)解决方案演示了不作任何修改的 SAP 模板。

> [!NOTE]
> .NET Standard 库已替换为可移植类库。 本书中的所有示例代码已都转换为使用.NET 标准库。 否则，PCL 和.NET Standard 库在概念上非常类似。

所有通用平台应用程序项目引用的库项目中的都代码库方法捆绑包。 使用 SAP 方法时，公共代码有效地在所有平台应用程序项目中存在并在它们之间共享。

大多数 Xamarin.Forms 开发人员更喜欢库方法。 在本书中，大部分解决方案使用一个库。 包括那些使用 SAP **Sap**项目名称中的后缀。

共享项目中的代码可以通过使用为各种平台的不同代码执行与 SAP 方法C#预处理器指令 (`#if`，#`elif`，并`#endif`) 使用这些预定义的标识符：

- iOS: `__IOS__`
- Android: `__ANDROID__`
- UWP: `WINDOWS_UWP`

在共享库中，可以确定在运行时, 运行哪种平台，您将看到更高版本中这一章。

## <a name="labels-for-text"></a>标签文本

[ **Greetings** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Greetings)解决方案展示了如何添加新C#的文件**Greetings**项目。 此文件定义一个名为`GreetingsPage`派生`ContentPage`。 在此书中，大多数项目都包含单个`ContentPage`派生其名称是带有后缀的项目名称`Page`追加。

`GreetingsPage`构造函数实例化[ `Label` ](xref:Xamarin.Forms.Label)视图，它是显示文本的 Xamarin.Forms 视图。 [ `Text` ](xref:Xamarin.Forms.Label.Text)属性设置为显示的文本`Label`。 此程序设置`Label`到`Content`属性的`ContentPage`。 构造函数`App`类，然后实例化`GreetingsPage`并将其设置为其`MainPage`属性。

在页面的左上角显示的文本。 在 iOS 上，这意味着它与重叠页面的状态栏。 有几种解决方案，此问题：

### <a name="solution-1-include-padding-on-the-page"></a>解决方案 1。 包括在页上的填充

设置[ `Padding` ](xref:Xamarin.Forms.Page.Padding)页上的属性。 `Padding` 类型[ `Thickness` ](xref:Xamarin.Forms.Thickness)，具有四个属性的结构：

- [`Left`](xref:Xamarin.Forms.Thickness.Left)
- [`Top`](xref:Xamarin.Forms.Thickness.Top)
- [`Right`](xref:Xamarin.Forms.Thickness.Right)
- [`Bottom`](xref:Xamarin.Forms.Thickness.Bottom)

`Padding` 定义页内的一个区域，其中排除内容。 这允许`Label`以避免覆盖 iOS 状态栏。

### <a name="solution-2-include-padding-just-for-ios-sap-only"></a>解决方案 2。 包括只适用于 iOS (仅适用于 SAP) 的填充

只能在使用与 SAP 在 iOS 上设置填充属性C#预处理器指令。 了这一点[ **GreetingsSap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/GreetingsSap)解决方案。

### <a name="solution-3-include-padding-just-for-ios-pcl-or-sap"></a>解决方案 3。 包括只适用于 iOS （PCL 和 SAP） 的填充

在用于书的 Xamarin.Forms 版本`Padding`可以使用所选属性特定于 iOS 的 PCL 或 SAP [ `Device.OnPlatform` ](xref:Xamarin.Forms.Device.OnPlatform(System.Action,System.Action,System.Action,System.Action))或[ `Device.OnPlatform<T>` ](xref:Xamarin.Forms.Device.OnPlatform*)静态方法。 这些方法现已弃用

`Device.OnPlatform`方法用来运行特定于平台的代码，或者选择特定于平台的值。 在内部，它们使利用[ `Device.OS` ](xref:Xamarin.Forms.Device.OS)静态只读属性，返回的成员[ `TargetPlatform` ](xref:Xamarin.Forms.TargetPlatform)枚举：

- [`iOS`](xref:Xamarin.Forms.TargetPlatform.iOS)
- [`Android`](xref:Xamarin.Forms.TargetPlatform.Android)
- [`Windows`](xref:Xamarin.Forms.TargetPlatform.Windows) 对于 UWP 设备。

`Device.OnPlatform`方法，`Device.OS`属性，并`TargetPlatform`枚举都现在已弃用。 请改用[ `Device.RuntimePlatform` ](xref:Xamarin.Forms.Device.RuntimePlatform)属性，并比较`string`返回包含以下静态字段的值：

- [`iOS`](xref:Xamarin.Forms.Device.iOS)字符串"iOS"
- [`Android`](xref:Xamarin.Forms.Device.Android)字符串"Android"
- [`UWP`](xref:Xamarin.Forms.Device.UWP)字符串"UWP"，指通用 Windows 平台

[ `Device.Idiom` ](xref:Xamarin.Forms.Device.Idiom)静态只读属性相关联。 这将返回的成员[ `TargetIdiom` ](xref:Xamarin.Forms.TargetIdiom)，其中包括以下成员：

- [`Desktop`](xref:Xamarin.Forms.TargetIdiom.Desktop)
- [`Tablet`](xref:Xamarin.Forms.TargetIdiom.Tablet)
- [`Phone`](xref:Xamarin.Forms.TargetIdiom.Phone)
- [`Unsupported`](xref:Xamarin.Forms.TargetIdiom.Unsupported) 未使用

适用于 iOS 和 Android 之间截止`Tablet`和`Phone`是纵向宽度为 600 的单位。 对于 Windows 平台中，`Desktop`指示在 Windows 10 下运行的 UWP 应用程序和`Phone`指示 Windows 10 应用程序下运行的 UWP 应用程序。

## <a name="solution-3a-set-margin-on-the-label"></a>解决方案 3a。 标签上的设置距

[ `Margin` ](xref:Xamarin.Forms.View.Margin)太迟引入属性要包含在书中，但它也是类型`Thickness`，并且上设置`Label`来定义的计算中包括视图之外的区域视图的布局。

`Padding`属性上才[ `Layout` ](xref:Xamarin.Forms.Layout)并[ `Page` ](xref:Xamarin.Forms.Page)派生类。 `Margin`属性是可用于所有[ `View` ](xref:Xamarin.Forms.View)派生类。

## <a name="solution-4-center-the-label-within-the-page"></a>解决方案 4。 在页面内的使标签居中

您可以居中`Label`内`Page`（或将其放入其他 8 个位置之一） 通过设置[ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions)并[ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions)属性的`Label`为类型的值[ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions)。 `LayoutOptions`结构定义两个属性：

- [ `Alignment` ](xref:Xamarin.Forms.LayoutOptions.Alignment)类型的属性[ `LayoutAlignment` ](xref:Xamarin.Forms.LayoutAlignment)，具有四个成员的枚举： [ `Start` ](xref:Xamarin.Forms.LayoutAlignment.Start)，这意味着左端或顶端具体取决于方向[ `Center` ](xref:Xamarin.Forms.LayoutAlignment.Center)， [ `End` ](xref:Xamarin.Forms.LayoutAlignment.End)，这意味着右侧或底部根据方向，以及[ `Fill` ](xref:Xamarin.Forms.LayoutAlignment.Fill)。

- [ `Expands` ](xref:Xamarin.Forms.LayoutOptions.Expands)类型的属性`bool`。

通常不直接使用这些属性。 相反，将这两个属性的组合提供的类型的八个静态只读属性`LayoutOptions`:

- [`LayoutOptions.Start`](xref:Xamarin.Forms.LayoutOptions.Start)
- [`LayoutOptions.Center`](xref:Xamarin.Forms.LayoutOptions.Center)
- [`LayoutOptions.End`](xref:Xamarin.Forms.LayoutOptions.End)
- [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)
- [`LayoutOptions.StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand)
- [`LayoutOptions.CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand)
- [`LayoutOptions.EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand)
- [`LayoutOptions.FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand)

`HorizontalOptions` 并`VerticalOptions`是在 Xamarin.Forms 布局中，最重要的属性和中的更详细地讨论[**第 4 章。滚动堆栈**](chapter04.md)。

下面是使用结果`HorizontalOptions`并`VerticalOptions`的属性`Label`都设置为`LayoutOptions.Center`:

[![Greetings 程序的三个屏幕截图](images/ch02fg05-small.png "水平和垂直居中标签")](images/ch02fg05-large.png#lightbox "水平和垂直居中标签")

## <a name="solution-5-center-the-text-within-the-label"></a>解决方案 5。 在标签中的文本居中

此外可以使文本居中 （或将其放在页上的其他 8 个位置中） 通过设置[ `HorizontalTextAlignment` ](xref:Xamarin.Forms.Label.HorizontalTextAlignment)并[ `VerticalTextAlignment` ](xref:Xamarin.Forms.Label.VerticalTextAlignment)的属性`Label`成员[ `TextAlignment` ](xref:Xamarin.Forms.TextAlignment)枚举：

- [`Start`](xref:Xamarin.Forms.TextAlignment.Start)含义左或靠上 （具体取决于方向）
- [`Center`](xref:Xamarin.Forms.TextAlignment.Center)
- [`End`](xref:Xamarin.Forms.TextAlignment.End)这意味着右侧或底部 （取决于方向）

这两个属性定义仅可由`Label`，而`HorizontalAlignment`并`VerticalAlignment`属性定义由`View`并由所有继承`View`派生类。 下的视觉效果可能看起来类似，但它们有很大差异，如下一章中所示。

## <a name="related-links"></a>相关链接

- [第 2 章全文 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch02-Apr2016.pdf)
- [第 2 章示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02)
- [第 2 章F#示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/FS)
- [Xamarin.Forms 入门](~/get-started/index.yml)
