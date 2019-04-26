---
title: 第 13 章的摘要。 位图
description: 使用 Xamarin.Forms 创建移动应用：第 13 章的摘要。 位图
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 5D153857-B6B7-4A14-8FB9-067DE198C2C7
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: 737e242e14778f38405845541b2ca30d27c3cf5a
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61334454"
---
# <a name="summary-of-chapter-13-bitmaps"></a>第 13 章的摘要。 位图

[![下载示例](~/media/shared/download.png)下载示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13)

> [!NOTE] 
> 此页上的说明表明其中 Xamarin.Forms 已脱离一书中介绍的内容的区域。

Xamarin.Forms [ `Image` ](xref:Xamarin.Forms.Image)元素显示位图。 所有 Xamarin.Forms 平台都支持的 JPEG、 PNG、 GIF 和 BMP 文件格式。

在 Xamarin.Forms 中的位图来自四个位置：

- 通过指定 URL 的 web
- 作为在共享库中的资源嵌入
- 作为资源嵌入在平台应用程序项目
- 从任何位置可由.NET 引用`Stream`对象，其中包括 `MemoryStream`

共享库中的位图资源是独立于平台的而平台项目中的位图资源是特定于平台的。

> [!NOTE] 
> 本书的文本，就对可移植类库，它已替换为.NET Standard 库的引用。 本书中的所有示例代码已都转换为使用.NET 标准库。

通过设置指定的位图[ `Source` ](xref:Xamarin.Forms.Image.Source)的属性`Image`对象的类型[ `ImageSource` ](xref:Xamarin.Forms.ImageSource)，一个带有三个派生类抽象类：

- [`UriImageSource`](xref:Xamarin.Forms.UriImageSource) 用于通过基于 web 访问位图`Uri`对象设置为其[ `Uri` ](xref:Xamarin.Forms.UriImageSource.Uri)属性
- [`FileImageSource`](xref:Xamarin.Forms.FileImageSource) 用于访问存储在平台应用程序项目中的位图基于文件夹和文件路径设置为其[ `File` ](xref:Xamarin.Forms.FileImageSource.File)属性
- [`StreamImageSource`](xref:Xamarin.Forms.StreamImageSource) 用于加载使用.NET 的位图`Stream`返回由指定的对象`Stream`从`Func`设置为其[ `Stream` ](xref:Xamarin.Forms.StreamImageSource.Stream)属性

或者 （和更常见的） 可以使用的以下静态方法`ImageSource`类中，将返回的所有`ImageSource`对象：

- [`ImageSource.FromUri`](xref:Xamarin.Forms.ImageSource.FromUri(System.Uri)) 用于通过基于 web 访问位图`Uri`对象
- [`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource*) 用于访问存储为 PCL; 在应用程序中嵌入的资源的位图[ `ImageSource.FromResource` ](xref:Xamarin.Forms.ImageSource.FromResource(System.String,System.Type))或[ `ImageSource.FromResource` ](xref:Xamarin.Forms.ImageSource.FromResource(System.String,System.Reflection.Assembly))访问另一个源程序集中的位图
- [`ImageSource.FromFile`](xref:Xamarin.Forms.ImageSource.FromFile(System.String)) 用于访问从平台应用程序项目的位图
- [`ImageSource.FromStream`](xref:Xamarin.Forms.ImageSource.FromStream(System.Func{System.IO.Stream})) 用于加载位图基于`Stream`对象

没有类等效项的`Image.FromResource`方法。 `UriImageSource`类是很有用，如果您需要缓存进行控制。 `FileImageSource`类是在 XAML 中很有用。 `StreamImageSource` 对于异步加载很有用`Stream`对象，而`ImageSource.FromStream`是同步的。

## <a name="platform-independent-bitmaps"></a>独立于平台的位图

[ **WebBitmapCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/WebBitmapCode)项目将位图加载通过 web 使用`ImageSource.FromUri`。 `Image`元素设置为`Content`属性的`ContentPage`，因此其被限制到页的大小。 而不考虑位图的大小，受约束`Image`元素拉伸到其容器的大小，并且在其最大大小显示位图`Image`元素同时保持位图的长宽比。 区域`Image`更高版本可以具有颜色位图[ `BackgroundColor` ](xref:Xamarin.Forms.VisualElement.BackgroundColor)。

[ **WebBitmapXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/WebBitmapXaml)示例类似，但只需设置`Source`到 URL 的属性。 就会处理转换[ `ImageSourceConverter` ](xref:Xamarin.Forms.ImageSourceConverter)类。

### <a name="fit-and-fill"></a>配合和填充

您可以控制如何通过设置拉伸位图[ `Aspect` ](xref:Xamarin.Forms.Image.Aspect)的属性`Image`的以下成员之一[ `Aspect` ](xref:Xamarin.Forms.Aspect)枚举：

- [`AspectFit`](xref:Xamarin.Forms.Aspect.AspectFit)： 尊重纵横比 （默认值）
- [`Fill`](xref:Xamarin.Forms.Aspect.Fill)： 填充区域，不会接受纵横比
- [`AspectFill`](xref:Xamarin.Forms.Aspect.AspectFill)： 填充区域，但会考虑通过裁剪位图的一部分来实现的纵横比，

### <a name="embedded-resources"></a>嵌入的资源

可以将位图文件添加到 PCL，或在 pcl 中的文件夹。 为其提供**生成操作**的**EmbeddedResource**。 [ **ResourceBitmapCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ResourceBitmapCode)示例演示如何使用`ImageSource.FromResource`加载文件。 传递给方法的资源名称包含的程序集名称后, 跟句点后, 跟可选的文件夹名称和句点后, 跟文件名。

程序集`VerticalOptions`并`HorizontalOptions`的属性`Image`到`LayoutOptions.Center`，这使得`Image`不受约束的元素。 `Image`和位图都相同的大小：

- 在 iOS 和 Android，`Image`是位图的像素大小。 没有位图像素和屏幕像素之间的一对一映射。
- 在通用 Windows 平台上，`Image`是以与设备无关单位位图的像素大小。 大多数设备上，每个位图像素占用多个屏幕像素。

[ **StackedBitmap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/StackedBitmap)示例 put`Image`中垂直`StackLayout`在 XAML 中。 名为标记扩展[ `ImageResourceExtension` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter13/StackedBitmap/StackedBitmap/StackedBitmap/ImageResourceExtension.cs)有助于引用 XAML 中嵌入的资源。 此类只从它的程序集加载资源在虚拟机所在，因此它不能放置在库中。

### <a name="more-on-sizing"></a>有关调整大小的详细信息

通常是希望在所有平台之间一致地大小位图。
试验**StackedBitmap**，可以设置`WidthRequest`上`Image`元素中垂直`StackLayout`使大小保持一致之间平台，但您只能减少使用这种方法的大小。

您还可以设置`HeightRequest`以使图像大小一致的平台上，但约束位图的宽度将限制此技术的用途广泛。 图像的垂直`StackLayout`，`HeightRequest`应避免使用。

最好的方法是开始使用比电话宽度以与设备无关单位更广的位图，并将设置`WidthRequest`到所需的宽度以与设备无关单位。 了这一点[ **DeviceIndBitmapSize** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/DeviceIndBitmapSize)示例。

[ **MadTeaParty** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/MadTeaParty)显示 Lewis Carroll 的第 7 章*Wonderland Alice 的冒险*与 John Tenniel 通过原始图例：

[![Mad 茶参与方的三个屏幕截图](images/ch13fg16-small.png "Mad Hatters 茶方该书的文本")](images/ch13fg16-large.png#lightbox "Mad Hatters 茶方该书的文本")

### <a name="browsing-and-waiting"></a>浏览和等待

[ **ImageBrowser** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ImageBrowser)示例允许用户浏览存储在 Xamarin 网站上的库存映像。 它使用.NET [ `WebRequest` ](xref:System.Net.WebRequest)类下载包含位图的列表的 JSON 文件。

> [!NOTE]
> Xamarin.Forms 程序应使用[ `HttpClient` ](xref:System.Net.Http.HttpClient)而非[ `WebRequest` ](xref:System.Net.WebRequest)用于通过 internet 访问的文件。 

该程序使用[ `ActivityIndicator` ](xref:Xamarin.Forms.ActivityIndicator)以指示内容怎么回事。 加载每个位图，只读[ `IsLoading` ](xref:Xamarin.Forms.Image.IsLoading)的属性`Image`是`true`。 `IsLoading`属性由可绑定的属性，因此支持`PropertyChanged`该属性发生更改时触发事件。 该程序将一个处理程序附加到此事件，并使用当前的设置`IsLoaded`若要设置[ `IsRunning` ](https://api/property/Xamarin.Forms.ActivityIndicator.IsRunning/)属性`ActivityIndicator`。

## <a name="streaming-bitmaps"></a>流式处理位图

`ImageSource.FromStream`方法创建`ImageSource`基于.NET `Stream`。 必须传递方法`Func`对象，它返回`Stream`对象。

### <a name="accessing-the-streams"></a>访问流

[ **BitmapStreams** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/BitmapStreams)示例演示如何使用`ImaageSource.FromStream`方法来加载位图存储为嵌入的资源，并通过 web 加载位图。

### <a name="generating-bitmaps-at-run-time"></a>在运行时生成的位图

所有 Xamarin.Forms 平台都支持未压缩的 BMP 文件格式，这是轻松地在代码中构造，然后将存储在`MemoryStream`。 此方法允许从算法上在运行时，创建位图中实现[ `BmpMaker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BmpMaker.cs)类**Xamrin.FormsBook.Toolkit**库。

"执行它自己" [ **DiyGradientBitmap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/DiyGradientBitmap)示例演示如何使用`BmpMaker`渐变映像创建一个位图。

## <a name="platform-specific-bitmaps"></a>特定于平台的位图

所有 Xamarin.Forms 平台都允许将位图存储在平台应用程序程序集。 这些平台位图时检索到的 Xamarin.Forms 应用程序，属于类型[ `FileImageSource` ](xref:Xamarin.Forms.FileImageSource)。 可将其用于：

- [ `Icon` ](xref:Xamarin.Forms.MenuItem.Icon)属性 [`MenuItem`](xref:Xamarin.Forms.MenuItem)
- [ `Icon` ](xref:Xamarin.Forms.MenuItem.Icon)属性 [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem)
- [ `Image` ](xref:Xamarin.Forms.Button)属性 `Button`

平台程序集已经包含图标和初始屏幕的位图：

- 在 iOS 项目中，在**资源**文件夹
- 在 Android 项目中的子文件夹**资源**文件夹
- 在 Windows 项目中，在**资产**文件夹 （尽管 Windows 平台不到该文件夹会限制位图）

[ **PlatformBitmaps** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/PlatformBitmaps)示例使用代码以显示来自平台应用程序项目的图标。

### <a name="bitmap-resolutions"></a>位图的解决方法

所有平台都允许存储不同的设备分辨率的位图图像的多个版本。 在运行时，正确的版本加载基于设备屏幕的分辨率。

在 iOS 上，通过在文件名上后缀区分这些位图：

- 没有后缀为 160 DPI 设备 （1 像素到独立于设备的的单元） 的
- @2x后缀为 320 DPI 设备 （DIU 到 2 的像素为单位）
- @3x后缀为 480 DPI 设备 （DIU 到 3 个像素为单位）

应显示为一个平方英寸的位图将存在于三个版本：

- 在 160 像素正方形 MyImage.jpg
- MyImage@2x.jpg 在为 320 像素正方形
- MyImage@3x.jpg 在 480 像素正方形

程序将引用此位图作为 MyImage.jpg，但在基于屏幕的分辨率的运行时检索的适当版本。 不受约束，位图将始终呈现在 160 设备无关的单位。

对于 Android，位图存储中的各个子文件夹**资源**文件夹：

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

仅某些百分比都有效。 本书的示例程序包含仅与图像**规模 200**后缀，但当前的 Xamarin.Forms 解决方案模板包括**缩放 100**，**规模 125**，**规模 150**，并**规模 400**。

将位图添加到平台项目中，当**生成操作**应为：

- iOS:**BundleResource**
- Android:**AndroidResource**
- UWP:**Content**

[ **ImageTap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ImageTap)示例创建两个按钮类似对象组成`Image`元素与`TapGestureRecognizer`安装。 适用对象是一个英寸正方形。 `Source`的属性`Image`使用设置`OnPlatform`和`On`对象引用的平台上的文件名可能不同。 位图图像包括数字，指示其像素大小，因此大家可以检索和呈现的大小的位图。

### <a name="toolbars-and-their-icons"></a>工具栏和它们的图标

特定于平台的位图的主要用途之一是 Xamarin.Forms 工具栏中，通过添加构造[ `ToolbarItem` ](xref:Xamarin.Forms.ToolbarItem)对象添加到[ `ToolbarItems` ](xref:Xamarin.Forms.Page.ToolbarItems) 所定义集合`Page`. `ToobarItem` 派生自[ `MenuItem` ](xref:Xamarin.Forms.MenuItem)从它继承了某些属性。

最重要`ToolbarItem`属性是：

- [`Text`](xref:Xamarin.Forms.MenuItem.Text) 根据平台可能会出现的文本和 `Order`
- [`Icon`](xref:Xamarin.Forms.MenuItem.Icon) 类型的`FileImageSource`可能会出现因平台而异的图像和 `Order`
- [`Order`](xref:Xamarin.Forms.ToolbarItem.Order) 类型的[ `ToolbarItemOrder` ](xref:Xamarin.Forms.ToolbarItemOrder)，一个包含三个成员的枚举[ `Default` ](xref:Xamarin.Forms.ToolbarItemOrder.Default)， [ `Primary` ](xref:Xamarin.Forms.ToolbarItemOrder.Primary)，并[ `Secondary` ](xref:Xamarin.Forms.ToolbarItemOrder.Secondary).

数`Primary`应限制为三个或四个项。 应包括`Text`设置的所有项。 对于大多数平台，仅`Primary`项需要`Icon`，但 Windows 8.1 需要`Icon`的所有项。 图标应为 32 个与设备无关单位正方形。 `FileImageSource`类型指示它们是特定于平台的。

`ToolbarItem`激发[ `Clicked` ](xref:Xamarin.Forms.MenuItem.Clicked)事件时点击，非常类似于`Button`。 `ToolbarItem` 此外支持[ `Command` ](xref:Xamarin.Forms.MenuItem.Command)并[ `CommandParameter` ](xref:Xamarin.Forms.MenuItem.CommandParameter)属性通常与 MVVM 结合使用。 (请参阅[第 18 章、 MVVM](chapter18.md))。

IOS 和 Android 都需要页将显示一个工具栏[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)或导航到的页`NavigationPage`。 [ **ToolbarDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ToolbarDemo)程序集`MainPage`属性及其`App`类[`NavigationPage`构造函数](xref:Xamarin.Forms.NavigationPage.%23ctor(Xamarin.Forms.Page))与`ContentPage`自变量，并演示了工具栏的构造和事件处理程序。

### <a name="button-images"></a>按钮图像

此外可以使用特定于平台的位图设置[ `Image` ](xref:Xamarin.Forms.Button.Image)的属性`Button`到 32 个与设备无关单位正方形，如所示的位图[ **ButtonImage**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ButtonImage)示例。

> [!NOTE]
> 使用按钮上的图像已得到增强。 请参阅[位图使用按钮](~/xamarin-forms/user-interface/button.md#using-bitmaps-with-buttons)。

## <a name="related-links"></a>相关链接

- [第 13 章全文 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch13-Apr2016.pdf)
- [第 13 章示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13)
- [使用图像](~/xamarin-forms/user-interface/images.md)
- [使用位图按钮](~/xamarin-forms/user-interface/button.md#using-bitmaps-with-buttons)
