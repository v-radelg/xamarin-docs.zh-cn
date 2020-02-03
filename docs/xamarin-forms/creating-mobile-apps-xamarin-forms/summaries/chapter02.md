---
title: 第 2 章的摘要。 应用剖析
description: 使用 Xamarin.Forms 创建移动应用： 第 2 章的摘要。 应用剖析
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 8764EB7D-8331-4CF7-9BE1-26D0DEE9E0BB
author: davidbritch
ms.author: dabritch
ms.date: 07/17/2018
ms.openlocfilehash: f900cb1532ba4415127c95b07e777881e1d74994
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76724983"
---
# <a name="summary-of-chapter-2-anatomy-of-an-app"></a>第 2 章的摘要。 应用剖析

[![下载示例](~/media/shared/download.png) 下载示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02)

> [!NOTE]
> 此页上的说明表明其中 Xamarin.Forms 已脱离一书中介绍的内容的区域。

在 Xamarin 窗体应用程序中，占用屏幕空间的对象称为*视觉元素*，由[`VisualElement`](xref:Xamarin.Forms.VisualElement)类封装。 可视元素可以拆分为三个类别对应于这些类：

- [第](xref:Xamarin.Forms.Page)
- [布局](xref:Xamarin.Forms.Layout)
- [视图](xref:Xamarin.Forms.View)

`Page` 导数占用整个屏幕，或几乎是整个屏幕。 通常，页面的子页面是用于组织子视觉对象的 `Layout` 导数。 `Layout` 的子级可以是其他 `Layout` 类或 `View` 派生对象（通常称为*元素*），这些对象是熟悉的对象（如文本、位图、滑块、按钮、列表框等）。

本章演示如何通过将焦点置于[`Label`](xref:Xamarin.Forms.Label)上来创建应用程序，这是显示文本的 `View` 导数。

## <a name="say-hello"></a>假设你好

使用安装 Xamarin 平台，你可以创建新的 Xamarin.Forms 解决方案在 Visual Studio 或 Visual Studio for mac。 [**Hello**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello)解决方案使用适用于通用代码的可移植类库。

> [!NOTE]
> .NET Standard 库已替换为可移植类库。 本书中的所有示例代码已都转换为使用.NET 标准库。

此示例演示在 Visual Studio 中创建不作任何修改的 Xamarin.Forms 解决方案。 此解决方案由四个项目组成：

- [**你好**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello)，由其他项目共享的可移植类库（PCL）
- [**Droid**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.Droid)，适用于 Android 的应用程序项目
- [**你好**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.iOS)，适用于 ios 的应用程序项目
- [**你好**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.UWP)，通用 Windows 平台的应用程序项目（windows 10 和 Windows 10 移动版）

> [!NOTE]
> Xamarin.Forms 不再支持 Windows 8.1、 Windows Phone 8.1 或 Windows 10 移动版，但在 Windows 10 桌面版上运行的 Xamarin.Forms 应用程序。

您可以进行任何这些应用程序项目启动项目，然后生成并在设备或模拟器上运行该程序。

在许多 Xamarin.Forms 程序，您不会修改应用程序项目。 这些通常保持小的存根，只是为了启动该程序。 您的大部分将是侧重点的普遍适用于所有应用程序库。

## <a name="inside-the-files"></a>文件内

**Hello**程序显示的视觉对象在[`App`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello/App.cs)类的构造函数中定义。 `App` 派生自 Xamarin 类[`Application`](xref:Xamarin.Forms.Application)。

> [!NOTE]
> Visual Studio 解决方案模板适用于 Xamarin.Forms 使用 XAML 文件创建一个页面。 在[第7章](chapter07.md)之前，不会在此书籍中涉及到 XAML。

**Hello** PCL 项目的 "**引用**" 部分包括以下 Xamarin 程序集：

- **Xamarin. Core**
- **Xamarin. .Xaml**
- **Xamarin. 平台**

五个应用程序项目的 "**引用**" 部分包括适用于各个平台的其他程序集：

- **Xamarin. Platform.string**
- **Xamarin. Platform.string**
- **Xamarin. node.js**
- **Xamarin. Platform.string**
- **Xamarin. node.js。**
- **Xamarin. e e**

> [!NOTE]
> 这些项目的 "**引用**" 部分不再列出程序集。 相反，项目文件包含引用 Xamarin **PackageReference** NuGet 包的标记。 Visual Studio 中的 "**引用**" 部分列出了**xamarin. forms**包，而不是 xamarin 程序集。

每个应用程序项目都包含对 `Xamarin.Forms` 命名空间中静态 `Forms.Init` 方法的调用。 此初始化 Xamarin.Forms 库。 为每个平台定义了不同版本的 `Forms.Init`。 调用此方法可在以下类：

- iOS： [`AppDelegate`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.iOS/AppDelegate.cs)
- Android： [`MainActivity`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)
- UWP： [`App` 类 `OnLaunched` 方法](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)

此外，每个平台都必须实例化共享库中的 `App` 类位置。 这会在以下类的 `LoadApplication` 调用中发生：

- iOS： [`AppDelegate`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.iOS/AppDelegate.cs)
- Android： [`MainActivity`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)
- UWP： [`MainPage`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.UWP/MainPage.xaml.cs)

否则，这些应用程序项目是正常的"执行任何操作"的程序。

## <a name="pcl-or-sap"></a>PCL 或 SAP？

就可以使用可移植类库 (PCL) 或共享资产项目 (SAP) 中的常见代码创建 Xamarin.Forms 解决方案。 若要创建的 SAP 解决方案，请在 Visual Studio 中选择共享选项。 [**HelloSap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/HelloSap)解决方案演示了没有修改的 SAP 模板。

> [!NOTE]
> .NET Standard 库已替换为可移植类库。 本书中的所有示例代码已都转换为使用.NET 标准库。 否则，PCL 和.NET Standard 库在概念上非常类似。

所有通用平台应用程序项目引用的库项目中的都代码库方法捆绑包。 使用 SAP 方法时，公共代码有效地在所有平台应用程序项目中存在并在它们之间共享。

大多数 Xamarin.Forms 开发人员更喜欢库方法。 在本书中，大部分解决方案使用一个库。 使用 SAP 的人员在项目名称中包括**SAP**后缀。

使用 SAP 方法，共享项目中的代码可以通过使用C#预处理器指令（`#if`、#`elif`和 `#endif`），使用这些预定义的标识符对各种平台执行不同的代码：

- iOS： `__IOS__`
- Android： `__ANDROID__`
- UWP： `WINDOWS_UWP`

在共享库中，可以确定在运行时, 运行哪种平台，您将看到更高版本中这一章。

## <a name="labels-for-text"></a>标签文本

[**问候语**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Greetings)解决方案演示了如何将新C#文件添加到**问候语**项目。 此文件定义从 `ContentPage`派生的名为 `GreetingsPage` 的类。 在本书中，大多数项目都包含一个 `ContentPage` 导数，其 name 为追加 `Page` 后缀的项目名称。

`GreetingsPage` 构造函数实例化一个[`Label`](xref:Xamarin.Forms.Label)视图，它是显示文本的 Xamarin 视图。 [`Text`](xref:Xamarin.Forms.Label.Text)属性设置为 `Label`显示的文本。 此程序将 `Label` 设置为 `ContentPage`的 `Content` 属性。 然后，`App` 类的构造函数实例化 `GreetingsPage` 并将其设置为其 `MainPage` 属性。

在页面的左上角显示的文本。 在 iOS 上，这意味着它与重叠页面的状态栏。 有几种解决方案，此问题：

### <a name="solution-1-include-padding-on-the-page"></a>解决方案 1。 包括在页上的填充

设置页面上的[`Padding`](xref:Xamarin.Forms.Page.Padding)属性。 `Padding` 的类型为[`Thickness`](xref:Xamarin.Forms.Thickness)，这是具有四个属性的结构：

- [`Left`](xref:Xamarin.Forms.Thickness.Left)
- [`Top`](xref:Xamarin.Forms.Thickness.Top)
- [`Right`](xref:Xamarin.Forms.Thickness.Right)
- [`Bottom`](xref:Xamarin.Forms.Thickness.Bottom)

`Padding` 定义排除内容的页面内的区域。 这允许 `Label` 避免覆盖 iOS 状态栏。

### <a name="solution-2-include-padding-just-for-ios-sap-only"></a>解决方案 2。 包括只适用于 iOS (仅适用于 SAP) 的填充

只能在使用与 SAP 在 iOS 上设置填充属性C#预处理器指令。 [**GreetingsSap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/GreetingsSap)解决方案中演示了这一点。

### <a name="solution-3-include-padding-just-for-ios-pcl-or-sap"></a>解决方案 3。 包括只适用于 iOS （PCL 和 SAP） 的填充

在用于该书的 Xamarin 版本中，可以使用[`Device.OnPlatform`](xref:Xamarin.Forms.Device.OnPlatform(System.Action,System.Action,System.Action,System.Action))或[`Device.OnPlatform<T>`](xref:Xamarin.Forms.Device.OnPlatform*)静态方法选择特定于 PCL 或 SAP 中的 iOS 的 `Padding` 属性。 这些方法现已弃用

`Device.OnPlatform` 方法用于运行特定于平台的代码或选择平台特定的值。 在内部，它们使用[`Device.OS`](xref:Xamarin.Forms.Device.OS)的静态只读属性，该属性将返回[`TargetPlatform`](xref:Xamarin.Forms.TargetPlatform)枚举的成员：

- [`iOS`](xref:Xamarin.Forms.TargetPlatform.iOS)
- [`Android`](xref:Xamarin.Forms.TargetPlatform.Android)
- UWP 设备的[`Windows`](xref:Xamarin.Forms.TargetPlatform.Windows) 。

现在已弃用 `Device.OnPlatform` 方法、`Device.OS` 属性和 `TargetPlatform` 枚举。 请改用[`Device.RuntimePlatform`](xref:Xamarin.Forms.Device.RuntimePlatform)属性，并将 `string` 返回值与以下静态字段进行比较：

- [`iOS`](xref:Xamarin.Forms.Device.iOS)，则字符串 "iOS"
- [`Android`](xref:Xamarin.Forms.Device.Android)，则字符串 "Android"
- [`UWP`](xref:Xamarin.Forms.Device.UWP)，该字符串为 "UWP"，引用通用 Windows 平台

[`Device.Idiom`](xref:Xamarin.Forms.Device.Idiom)静态只读属性是相关的。 这会返回[`TargetIdiom`](xref:Xamarin.Forms.TargetIdiom)的成员，其中包含以下成员：

- [`Desktop`](xref:Xamarin.Forms.TargetIdiom.Desktop)
- [`Tablet`](xref:Xamarin.Forms.TargetIdiom.Tablet)
- [`Phone`](xref:Xamarin.Forms.TargetIdiom.Phone)
- [`Unsupported`](xref:Xamarin.Forms.TargetIdiom.Unsupported)未使用

对于 iOS 和 Android，`Tablet` 和 `Phone` 之间的截止是纵向宽度600单位。 对于 Windows 平台，`Desktop` 指示在 Windows 10 下运行的 UWP 应用程序，`Phone` 指示在 Windows 10 应用程序下运行的 UWP 应用程序。

## <a name="solution-3a-set-margin-on-the-label"></a>解决方案 3a。 标签上的设置距

此[`Margin`](xref:Xamarin.Forms.View.Margin)属性的引入时间太晚，无法包括在本书中，但它也属于类型 `Thickness`，你可以在 `Label` 上对其进行设置，以便定义视图布局的计算中所包含的一个区域。

`Padding` 属性仅可用于[`Layout`](xref:Xamarin.Forms.Layout)和[`Page`](xref:Xamarin.Forms.Page)派生。 `Margin` 属性可用于所有[`View`](xref:Xamarin.Forms.View)派生。

## <a name="solution-4-center-the-label-within-the-page"></a>解决方案 4。 在页面内的使标签居中

通过将 `Label` 的[`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions)和[`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions)属性设置为类型[`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions)的值，你可以将 `Label` 集中到 `Page` （或将其放置在其他八个位置之一）。 `LayoutOptions` 结构定义了两个属性：

- [`LayoutAlignment`](xref:Xamarin.Forms.LayoutAlignment)类型的[`Alignment`](xref:Xamarin.Forms.LayoutOptions.Alignment)属性，即具有四个成员的枚举： [`Start`](xref:Xamarin.Forms.LayoutAlignment.Start)，这意味着左侧或顶部取决于方向， [`Center`](xref:Xamarin.Forms.LayoutAlignment.Center)， [`End`](xref:Xamarin.Forms.LayoutAlignment.End)，这意味着向右或向下，具体取决于方向和[`Fill`](xref:Xamarin.Forms.LayoutAlignment.Fill)。

- `bool`类型的[`Expands`](xref:Xamarin.Forms.LayoutOptions.Expands)属性。

通常不直接使用这些属性。 相反，这两个属性的组合由 `LayoutOptions`类型的8个静态只读属性提供：

- [`LayoutOptions.Start`](xref:Xamarin.Forms.LayoutOptions.Start)
- [`LayoutOptions.Center`](xref:Xamarin.Forms.LayoutOptions.Center)
- [`LayoutOptions.End`](xref:Xamarin.Forms.LayoutOptions.End)
- [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)
- [`LayoutOptions.StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand)
- [`LayoutOptions.CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand)
- [`LayoutOptions.EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand)
- [`LayoutOptions.FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand)

`HorizontalOptions` 和 `VerticalOptions` 是 Xamarin 中最重要的属性，详见[**第4章。滚动堆栈**](chapter04.md)。

下面是 `Label` 的 `HorizontalOptions` 和 `VerticalOptions` 属性设置为 `LayoutOptions.Center`的结果：

[![问候语程序的三个屏幕截图](images/ch02fg05-small.png "水平和垂直居中标签")](images/ch02fg05-large.png#lightbox "水平和垂直居中标签")

## <a name="solution-5-center-the-text-within-the-label"></a>解决方案 5。 在标签中的文本居中

您还可以通过将 `Label` 的[`HorizontalTextAlignment`](xref:Xamarin.Forms.Label.HorizontalTextAlignment)和[`VerticalTextAlignment`](xref:Xamarin.Forms.Label.VerticalTextAlignment)属性设置为[`TextAlignment`](xref:Xamarin.Forms.TextAlignment)枚举的成员来使文本居中（或将其放在页面的其他八个位置）：

- [`Start`](xref:Xamarin.Forms.TextAlignment.Start)，即左侧或顶部（取决于方向）
- [`Center`](xref:Xamarin.Forms.TextAlignment.Center)
- [`End`](xref:Xamarin.Forms.TextAlignment.End)，即 "向右" 或 "底部" （取决于方向）

这两个属性仅由 `Label`定义，而 `HorizontalAlignment` 和 `VerticalAlignment` 属性由 `View` 定义，由所有 `View` 派生派生。 下的视觉效果可能看起来类似，但它们有很大差异，如下一章中所示。

## <a name="related-links"></a>相关链接

- [第2章完整文本（PDF）](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch02-Apr2016.pdf)
- [第2章示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02)
- [第 2 F#章示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/FS)
- [用 Xamarin 入门](~/get-started/index.yml)
