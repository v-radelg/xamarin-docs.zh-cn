---
title: 第 13 章的摘要。 位图
description: 使用 Xamarin.Forms 创建移动应用： 摘要的第 13 章。 位图
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 5D153857-B6B7-4A14-8FB9-067DE198C2C7
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: e4746ed94a008d382ce15bb9cd7c52365d9ba574
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76725526"
---
# <a name="summary-of-chapter-13-bitmaps"></a>第 13 章的摘要。 位图

[![下载示例](~/media/shared/download.png) 下载示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13)

> [!NOTE]
> 此页上的说明表明其中 Xamarin.Forms 已脱离一书中介绍的内容的区域。

Xamarin [`Image`](xref:Xamarin.Forms.Image)元素显示位图。 所有 Xamarin.Forms 平台都支持的 JPEG、 PNG、 GIF 和 BMP 文件格式。

在 Xamarin.Forms 中的位图来自四个位置：

- 通过指定 URL 的 web
- 作为在共享库中的资源嵌入
- 作为资源嵌入在平台应用程序项目
- 从 .NET `Stream` 对象可引用的任何位置，包括 `MemoryStream`

共享库中的位图资源是独立于平台的而平台项目中的位图资源是特定于平台的。

> [!NOTE]
> 本书的文本，就对可移植类库，它已替换为.NET Standard 库的引用。 本书中的所有示例代码已都转换为使用.NET 标准库。

通过将 `Image` 的[`Source`](xref:Xamarin.Forms.Image.Source)属性设置为类型[`ImageSource`](xref:Xamarin.Forms.ImageSource)的对象（具有三个导数的抽象类）来指定位图：

- [`UriImageSource`](xref:Xamarin.Forms.UriImageSource)根据设置为其[`Uri`](xref:Xamarin.Forms.UriImageSource.Uri)属性的 `Uri` 对象，在 web 上访问位图
- 用于访问存储在平台应用程序项目中的位图的[`FileImageSource`](xref:Xamarin.Forms.FileImageSource) ，该位图基于文件夹和文件路径设置为其[`File`](xref:Xamarin.Forms.FileImageSource.File)属性
- [`StreamImageSource`](xref:Xamarin.Forms.StreamImageSource)通过将 `Stream` 从 `Func` 集返回到其[`Stream`](xref:Xamarin.Forms.StreamImageSource.Stream)属性，使用指定的 .net `Stream` 对象加载位图

另外，您可以使用 `ImageSource` 类的以下静态方法，所有这些方法都返回 `ImageSource` 对象：

- [`ImageSource.FromUri`](xref:Xamarin.Forms.ImageSource.FromUri(System.Uri))基于 `Uri` 对象访问基于 web 的位图
- [`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource*)用于访问存储为应用程序 PCL 中的嵌入资源的位图;[`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource(System.String,System.Type))或[`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource(System.String,System.Reflection.Assembly))以访问其他源程序集中的位图
- 用于从平台应用程序项目访问位图的[`ImageSource.FromFile`](xref:Xamarin.Forms.ImageSource.FromFile(System.String))
- 用于基于 `Stream` 对象加载位图的[`ImageSource.FromStream`](xref:Xamarin.Forms.ImageSource.FromStream(System.Func{System.IO.Stream}))

没有 `Image.FromResource` 方法的类等效项。 如果需要控制缓存，则 `UriImageSource` 类非常有用。 `FileImageSource` 类在 XAML 中很有用。 `StreamImageSource` 对 `Stream` 对象的异步加载很有用，而 `ImageSource.FromStream` 是同步的。

## <a name="platform-independent-bitmaps"></a>独立于平台的位图

[**WebBitmapCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/WebBitmapCode)项目使用 `ImageSource.FromUri`在 web 上加载位图。 `Image` 元素设置为 `ContentPage`的 `Content` 属性，因此它被限制为页的大小。 无论位图大小如何，受约束的 `Image` 元素都延伸到其容器的大小，位图在 `Image` 元素内的最大大小中显示，同时保持位图的纵横比。 位图之外的 `Image` 区域可以通过[`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor)着色。

[**WebBitmapXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/WebBitmapXaml)示例类似，只是将 `Source` 属性设置为 URL。 转换由[`ImageSourceConverter`](xref:Xamarin.Forms.ImageSourceConverter)类处理。

### <a name="fit-and-fill"></a>配合和填充

可以通过将 `Image` 的[`Aspect`](xref:Xamarin.Forms.Image.Aspect)属性设置为[`Aspect`](xref:Xamarin.Forms.Aspect)枚举的以下成员之一来控制如何拉伸位图：

- [`AspectFit`](xref:Xamarin.Forms.Aspect.AspectFit)：尊重纵横比（默认值）
- [`Fill`](xref:Xamarin.Forms.Aspect.Fill)：填充区域，不遵循纵横比
- [`AspectFill`](xref:Xamarin.Forms.Aspect.AspectFill)：填充区域，但遵循纵横比，通过裁剪部分位图完成

### <a name="embedded-resources"></a>嵌入的资源

可以将位图文件添加到 PCL，或在 pcl 中的文件夹。 为其生成**EmbeddedResource**的**生成操作**。 [**ResourceBitmapCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ResourceBitmapCode)示例演示如何使用 `ImageSource.FromResource` 加载文件。 传递给方法的资源名称包含的程序集名称后, 跟句点后, 跟可选的文件夹名称和句点后, 跟文件名。

程序将 `Image` 的 `VerticalOptions` 和 `HorizontalOptions` 属性设置为 `LayoutOptions.Center`，这使得 `Image` 元素不受约束。 `Image` 和位图大小相同：

- 在 iOS 和 Android 上，`Image` 是位图的像素大小。 没有位图像素和屏幕像素之间的一对一映射。
- 在通用 Windows 平台上，`Image` 是位图的像素大小，以与设备无关的单位表示。 大多数设备上，每个位图像素占用多个屏幕像素。

[**StackedBitmap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/StackedBitmap)示例在 XAML 的垂直 `StackLayout` 中放置 `Image`。 名为[`ImageResourceExtension`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter13/StackedBitmap/StackedBitmap/StackedBitmap/ImageResourceExtension.cs)的标记扩展有助于在 XAML 中引用嵌入的资源。 此类只从它的程序集加载资源在虚拟机所在，因此它不能放置在库中。

### <a name="more-on-sizing"></a>有关调整大小的详细信息

通常是希望在所有平台之间一致地大小位图。
使用**StackedBitmap**，可以在垂直 `StackLayout` 中 `Image` 元素上设置 `WidthRequest`，使其在平台之间保持一致，但只能使用此方法减小大小。

您还可以将 `HeightRequest` 设置为使图像大小在平台上保持一致，但位图的约束宽度将限制此技术的通用性。 对于垂直 `StackLayout`中的图像，应避免使用 `HeightRequest`。

最佳方法是以与设备无关的单位中的手机宽度更宽的位图开始，并在与设备无关的单位中将 `WidthRequest` 设置为所需的宽度。 [**DeviceIndBitmapSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/DeviceIndBitmapSize)示例演示了这一点。

[**MadTeaParty**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/MadTeaParty)显示 Lewis Carroll 的*Alice 冒险 in Wonderland*的第7章，其中的原始插图按 John Tenniel：

[![Mad 茶参与方的三个屏幕截图](images/ch13fg16-small.png "Mad Hatters 茶一方图书文本")](images/ch13fg16-large.png#lightbox "Mad Hatters 茶一方图书文本")

### <a name="browsing-and-waiting"></a>浏览和等待

[**ImageBrowser**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ImageBrowser)示例允许用户浏览 Xamarin 网站上存储的股票图像。 它使用 .NET [`WebRequest`](xref:System.Net.WebRequest)类下载包含位图列表的 JSON 文件。

> [!NOTE]
> Xamarin. Forms 程序应使用[`HttpClient`](xref:System.Net.Http.HttpClient)而不是[`WebRequest`](xref:System.Net.WebRequest)来通过 internet 访问文件。

程序使用[`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator)来表明发生了某些情况。 加载每个位图时，`Image` 的只读[`IsLoading`](xref:Xamarin.Forms.Image.IsLoading)属性 `true`。 `IsLoading` 属性是由可绑定属性支持的，因此在属性更改时将激发 `PropertyChanged` 事件。 程序将处理程序附加到此事件，并使用 `IsLoaded` 的当前设置来设置 `ActivityIndicator`的[`IsRunning`](xref:Xamarin.Forms.ActivityIndicator.IsRunning)属性。

## <a name="streaming-bitmaps"></a>流式处理位图

`ImageSource.FromStream` 方法基于 .NET `Stream`创建 `ImageSource`。 必须向方法传递返回 `Stream` 对象的 `Func` 对象。

### <a name="accessing-the-streams"></a>访问流

[**BitmapStreams**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/BitmapStreams)示例演示如何使用 `ImaageSource.FromStream` 方法加载存储为嵌入资源的位图，以及如何在 web 上加载位图。

### <a name="generating-bitmaps-at-run-time"></a>在运行时生成的位图

所有 Xamarin 平台都支持未压缩的 BMP 文件格式，它可以在代码中轻松构造，然后存储在 `MemoryStream`中。 此方法允许算法在运行时创建位图，这是在**适用 xamrin**库的[`BmpMaker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BmpMaker.cs)类中实现的。

"亲自动手" [**DiyGradientBitmap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/DiyGradientBitmap)示例演示了如何使用 `BmpMaker` 创建具有渐变图像的位图。

## <a name="platform-specific-bitmaps"></a>特定于平台的位图

所有 Xamarin.Forms 平台都允许将位图存储在平台应用程序程序集。 当由 Xamarin. Forms 应用程序检索时，这些平台位图的类型为[`FileImageSource`](xref:Xamarin.Forms.FileImageSource)。 可将其用于：

- [`MenuItem`](xref:Xamarin.Forms.MenuItem)的[`Icon`](xref:Xamarin.Forms.MenuItem.Icon)属性
- [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem)的[`Icon`](xref:Xamarin.Forms.MenuItem.Icon)属性
- `Button` 的[`Image`](xref:Xamarin.Forms.Button)属性

平台程序集已经包含图标和初始屏幕的位图：

- 在 iOS 项目的 "**资源**" 文件夹中
- 在 Android 项目的 "**资源**" 文件夹的子文件夹中
- 在 Windows 项目的 "**资产**" 文件夹中（尽管 windows 平台不会将位图限制到该文件夹）

[**PlatformBitmaps**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/PlatformBitmaps)示例使用代码从平台应用程序项目中显示一个图标。

### <a name="bitmap-resolutions"></a>位图的解决方法

所有平台都允许存储不同的设备分辨率的位图图像的多个版本。 在运行时，正确的版本加载基于设备屏幕的分辨率。

在 iOS 上，通过在文件名上后缀区分这些位图：

- 没有后缀为 160 DPI 设备 （1 像素到独立于设备的的单元） 的
- 320 DPI 设备的 "@2x" 后缀（DIU 的2个像素）
- 480 DPI 设备的 "@3x" 后缀（DIU 为3像素）

应显示为一个平方英寸的位图将存在于三个版本：

- 在 160 像素正方形 MyImage.jpg
- MyImage@2x.jpg 320 像素正方形
- MyImage@3x.jpg 480 像素正方形

程序将引用此位图作为 MyImage.jpg，但在基于屏幕的分辨率的运行时检索的适当版本。 不受约束，位图将始终呈现在 160 设备无关的单位。

对于 Android，位图存储在**Resources**文件夹的各个子文件夹中：

- drawable-ldpi (低 DPI) 为 120 DPI 设备 （到 DIU 0.75 的像素为单位）
- drawable-mdpi （中） 的 160 DPI 设备 （1 像素到 DIU）
- 可绘制的 hdpi （高） 为 240 DPI 设备 （DIU 到 1.5 的像素为单位）
- drawable-xhdpi （高） 为 320 DPI 设备 （DIU 到 2 的像素为单位）
- drawable-xxhdpi （额外高） 为 480 DPI 设备 （DIU 到 3 个像素为单位）
- drawable-xxxhdpi （三个额外高） 为 640 DPI 设备 （DIU 到 4 的像素为单位）

对于用于呈现一个方形英寸处位图，各种版本的位图将具有相同名称但不同的大小，并存储在这些文件夹中：

- drawable-ldpi/MyImage.jpg 在 120 像素正方形
- drawable-mdpi/MyImage.jpg 在 160 像素正方形
- 可绘制的 hdpi/MyImage.jpg 在 240 像素正方形
- drawable-xhdpi/MyImage.jpg 为 320 像素正方形
- drawable-xxhdpi/MyImage.jpg 在 480 像素正方形
- drawable-xxxhdpi/MyImage.jpg 在 640 像素正方形

位图将始终呈现在 160 设备无关的单位。 （使用标准的 Xamarin.Forms 解决方案模板仅包括 hdpi、 xhdpi 和 xxhdpi 文件夹。）

UWP 项目支持包含以像素为单位，每个与设备无关单位的缩放系数以百分比表示，例如位图命名方案：

- 在为 320 像素正方形的 MyImage.scale 200.jpg

仅某些百分比都有效。 本书的示例程序仅包括带**200**后缀的映像，但当前 Xamarin. Forms 解决方案模板包括**缩放-100**、**缩放-125**、**缩放-150**和**缩放-400**。

向平台项目添加位图时，**生成操作**应为：

- iOS： **BundleResource**
- Android： **AndroidResource**
- UWP：**内容**

[**ImageTap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ImageTap)示例创建两个类似按钮的对象，其中包含安装了 `TapGestureRecognizer` `Image` 元素。 适用对象是一个英寸正方形。 `Image` 的 `Source` 属性使用 `OnPlatform` 和 `On` 对象进行设置，以便在平台上引用可能不同的文件名。 位图图像包括数字，指示其像素大小，因此大家可以检索和呈现的大小的位图。

### <a name="toolbars-and-their-icons"></a>工具栏和它们的图标

平台特定的位图的主要用途之一是 Xamarin. Forms 工具栏，通过将[`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem)对象添加到 `Page`定义的[`ToolbarItems`](xref:Xamarin.Forms.Page.ToolbarItems)集合来构建。 `ToobarItem` 派生自它从中继承某些属性的[`MenuItem`](xref:Xamarin.Forms.MenuItem) 。

最重要的 `ToolbarItem` 属性包括：

- 可能根据平台和 `Order` 显示的文本[`Text`](xref:Xamarin.Forms.MenuItem.Text)
- 可能根据平台和 `Order` 显示的图像的类型 `FileImageSource` [`Icon`](xref:Xamarin.Forms.MenuItem.Icon)
- [`ToolbarItemOrder`](xref:Xamarin.Forms.ToolbarItemOrder)类型的[`Order`](xref:Xamarin.Forms.ToolbarItem.Order) 、包含三个成员的枚举、 [`Default`](xref:Xamarin.Forms.ToolbarItemOrder.Default)、 [`Primary`](xref:Xamarin.Forms.ToolbarItemOrder.Primary)和[`Secondary`](xref:Xamarin.Forms.ToolbarItemOrder.Secondary)。

`Primary` 项的数目应限制为三或四。 应为所有项包含 `Text` 设置。 对于大多数平台，仅 `Primary` 项需要 `Icon`，但 Windows 8.1 要求所有项都有 `Icon`。 图标应为 32 个与设备无关单位正方形。 `FileImageSource` 类型指示它们是特定于平台的。

点击时，`ToolbarItem` 会激发[`Clicked`](xref:Xamarin.Forms.MenuItem.Clicked)事件，这与 `Button`非常类似。 `ToolbarItem` 还支持通常用于连接到 MVVM 的[`Command`](xref:Xamarin.Forms.MenuItem.Command)和[`CommandParameter`](xref:Xamarin.Forms.MenuItem.CommandParameter)属性。 （请参阅第[18 章、MVVM](chapter18.md)）。

IOS 和 Android 都要求显示工具栏的页面是[`NavigationPage`](xref:Xamarin.Forms.NavigationPage)或 `NavigationPage`导航到的页面。 [**ToolbarDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ToolbarDemo)程序使用 `ContentPage` 参数将其 `App` 类的 `MainPage` 属性设置为[`NavigationPage` 构造函数](xref:Xamarin.Forms.NavigationPage.%23ctor(Xamarin.Forms.Page))，并演示了工具栏的构造和事件处理程序。

### <a name="button-images"></a>按钮图像

你还可以使用平台特定的位图将 `Button` 的[`Image`](xref:Xamarin.Forms.Button.Image)属性设置为32与设备无关的单位正方形的位图，如[**ButtonImage**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ButtonImage)示例所示。

> [!NOTE]
> 使用按钮上的图像已得到增强。 请参阅将[位图与按钮配合使用](~/xamarin-forms/user-interface/button.md#using-bitmaps-with-buttons)。

## <a name="related-links"></a>相关链接

- [第13章全文（PDF）](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch13-Apr2016.pdf)
- [第13章示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13)
- [使用图像](~/xamarin-forms/user-interface/images.md)
- [将位图与按钮一起使用](~/xamarin-forms/user-interface/button.md#using-bitmaps-with-buttons)
