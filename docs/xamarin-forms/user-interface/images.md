---
title: 在 Xamarin.Forms 中的图像
description: 可以使用 Xamarin.Forms 跨平台共享映像、 可以专门为每个平台，加载它们或它们可以为显示下载。
ms.prod: xamarin
ms.assetid: C025AB53-05CC-49BA-9815-75D6DF9E40B7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/04/2019
ms.openlocfilehash: 255d3f2f532e4899b1a890405af942a7ca2da8ea
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75490131"
---
# <a name="images-in-xamarinforms"></a>在 Xamarin.Forms 中的图像

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithimages)

_可以使用 Xamarin.Forms 跨平台共享映像、 可以专门为每个平台，加载它们或它们可以为显示下载。_

映像是应用程序导航、 可用性和品牌的一个重要部分。 Xamarin.Forms 应用程序需要能够在所有平台之间共享映像，但也有可能每个平台上显示不同的图像。

特定于平台的映像还所需的图标和初始屏幕;这些需要根据每个平台配置。

## <a name="display-images"></a>显示图像

使用 Xamarin.Forms [ `Image` ](xref:Xamarin.Forms.Image)视图，以在页面上显示图像。 它具有两个重要属性：

- [`Source`](xref:Xamarin.Forms.Image.Source) -An [ `ImageSource` ](xref:Xamarin.Forms.ImageSource)实例、 文件、 Uri 或资源，设置要显示的图像。
- [`Aspect`](xref:Xamarin.Forms.Image.Aspect) -如何调整大小的映像 （无论是为 stretch、 裁剪或 letterbox） 中显示的边界内。

[`ImageSource`](xref:Xamarin.Forms.ImageSource) 可以为每种类型的图像源使用的静态方法获取实例：

- [`FromFile`](xref:Xamarin.Forms.ImageSource.FromFile(System.String)) -需要文件名或可以解决每个平台的文件路径。
- [`FromUri`](xref:Xamarin.Forms.ImageSource.FromUri(System.Uri)) -需要一个 Uri 对象，例如。  `new Uri("http://server.com/image.jpg")` .
- [`FromResource`](xref:Xamarin.Forms.ImageSource.FromResource*) -需要嵌入到应用程序或.NET Standard 类库项目中，使用某个图像文件的资源标识符**生成操作： EmbeddedResource**。
- [`FromStream`](xref:Xamarin.Forms.ImageSource.FromStream(System.Func{System.IO.Stream})) -需要提供图像数据的流。

[ `Aspect` ](xref:Xamarin.Forms.Image.Aspect)属性确定将如何缩放图像以适合显示区域：

- [`Fill`](xref:Xamarin.Forms.Aspect.Fill) -拉伸图像以完全且完全填充的显示区域。 这可能会导致被扭曲图像。
- [`AspectFill`](xref:Xamarin.Forms.Aspect.AspectFill) -裁剪图像，以便它同时还能保留方面填充显示区域 (即。 无扭曲)。
- [`AspectFit`](xref:Xamarin.Forms.Aspect.AspectFit) -上下黑边映像 （如果需要），以便整个图像适合的显示区域的空白区域添加到顶部/底部或边，具体取决于图像是高或宽。

可以从[本地文件](#local-images)、[嵌入资源](#embedded-images)、[下载](#download-images)或从流中加载图像。 此外， [`Image`](xref:Xamarin.Forms.Image)视图可以通过在 `FontImageSource` 对象中指定字体图标数据来显示字体图标。 有关详细信息，请参阅[字体](~/xamarin-forms/user-interface/text/fonts.md)指南中的[显示字体图标](~/xamarin-forms/user-interface/text/fonts.md#display-font-icons)。

## <a name="local-images"></a>本地映像

可以添加到每个应用程序项目并从 Xamarin.Forms 共享代码中引用的图像文件。 当图像特定于平台时，例如在不同平台上使用不同分辨率或稍微不同的设计时，需要这种分发图像的方法。

所有应用之间使用的单一映像*必须在每个平台上使用相同的文件名*，并且它应为有效的 Android 资源名称 (即。 允许只包含小写字母、 数字、 下划线和句点)。

- **iOS** -首选方式管理和支持的映像，因为 iOS 9 是使用**资产目录图像集**，其中应包含的所有所需支持各种设备和缩放比例的图像的版本应用程序。 有关详细信息，请参阅[将图像添加到资产目录映像集](~/ios/app-fundamentals/images-icons/displaying-an-image.md)。
- **Android** -将在图像放**资源/drawable**目录**生成操作： AndroidResource**。 此外可以提供的图像的高和低 DPI 版本 (在适当地命名为**资源**如子目录**drawable ldpi**， **drawable hdpi**，和**drawable xhdpi**)。
- **通用 Windows 平台 (UWP)** -将图像放在应用程序的根目录下使用**生成操作： 内容**。

> [!IMPORTANT]
> 在 iOS 9 之前, 映像通常放置在**资源**具有文件夹**生成操作： BundleResource**。 但是，apple 已弃用的 iOS 应用程序中的映像的使用此方法。 有关详细信息，请参阅[图像大小和文件名](~/ios/app-fundamentals/images-icons/displaying-an-image.md)。

遵守这些规则进行文件命名和放置允许以下的 XAML 加载和显示在所有平台上的图像：

```xaml
<Image Source="waterfront.jpg" />
```

等效于C#代码如下所示：

```csharp
var image = new Image { Source = "waterfront.jpg" };
```

以下屏幕截图显示结果的每个平台上显示本地映像：

[![显示本地映像的示例应用程序](images-images/local-sml.png)](images-images/local.png#lightbox)

为提高灵活性`Device.RuntimePlatform`属性可用来选择不同的图像文件或路径对于某些或所有平台，此代码示例中所示：

```csharp
image.Source = Device.RuntimePlatform == Device.Android 
                ? ImageSource.FromFile("waterfront.jpg") 
                : ImageSource.FromFile("Images/waterfront.jpg");
```

> [!IMPORTANT]
> 若要跨所有平台使用相同的映像文件名名称必须在所有平台上有效。 Android 绘图制定了命名限制 – 允许小写字母、 数字、 下划线和句点 – 和跨平台兼容性必须随后出现在所有其他平台上过。 示例文件名**waterfront.png**如下所示的规则，但无效文件名的示例包括"water front.png"，"WaterFront.png"，"water-front.png"和"wåterfront.png"。

### <a name="native-resolutions-retina-and-high-dpi"></a>本机分辨率（retina 和高 DPI）

iOS、 Android 和 UWP 包括针对不同的图像分辨率，其中操作系统选择合适的映像在运行时根据设备的功能的支持。 Xamarin.Forms 使用本机平台的 Api，用于加载本地映像，因此如果正确命名和位于项目文件，它会自动支持备用解决方法。

因为 iOS 9 管理映像的首选的方法是拖动到相应的资产目录图像集所需的每个分辨率的图像。 有关详细信息，请参阅[将图像添加到资产目录映像集](~/ios/app-fundamentals/images-icons/displaying-an-image.md)。

在 iOS 9 之前, retina 版本的映像无法放置在**资源**文件夹的两个和第三次使用的分辨率 **@2x** 或 **@3x** 上的文件扩展名 （例如之前, 的文件名的后缀。 **myimage@2x.png** ). 但是，apple 已弃用的 iOS 应用程序中的映像的使用此方法。 有关详细信息，请参阅[图像大小和文件名](~/ios/app-fundamentals/images-icons/displaying-an-image.md)。

Android 备用分辨率图像应置于[特殊命名目录](https://developer.android.com/guide/practices/screens_support.html)在 Android 项目中，如以下屏幕截图中所示：

[![Android 多分辨率图像位置](images-images/xs-highdpisolution-sml.png)](images-images/xs-highdpisolution.png#lightbox)

UWP 图像文件名称[可以使用作为后缀`.scale-xxx`文件扩展名之前](https://docs.microsoft.com/windows/uwp/app-resources/images-tailored-for-scale-theme-contrast)，其中`xxx`是应用到的资产，例如缩放百分比**myimage.scale 200.png**。 然后可以引用映像以在代码或 XAML 不带小数位数修饰符，例如刚刚**myimage.png**。 该平台将选择最接近的相应资产规模基于显示器的当前 DPI。

### <a name="additional-controls-that-display-images"></a>显示图像的其他控件

某些控件具有显示图像，如的属性：

- [`Button`](xref:Xamarin.Forms.Button)具有[`ImageSource`](xref:Xamarin.Forms.Button.ImageSource)属性，该属性可设置为要在 `Button`上显示的位图图像。 有关详细信息，请参阅将[位图与按钮一起使用](~/xamarin-forms/user-interface/button.md#using-bitmaps-with-buttons)。
- [`ImageButton`](xref:Xamarin.Forms.Button)具有[`Source`](xref:Xamarin.Forms.ImageButton.Source)属性，该属性可以设置为要显示在 `ImageButton`中的图像。 有关详细信息，请参阅[设置图像源](~/xamarin-forms/user-interface/imagebutton.md#setting-the-image-source)。
- [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem)具有[`IconImageSource`](xref:Xamarin.Forms.MenuItem.IconImageSource)属性，该属性可以设置为从文件、嵌入资源、URI 或流加载的图像。
- [`ImageCell`](xref:Xamarin.Forms.ImageCell)具有[`ImageSource`](xref:Xamarin.Forms.ImageCell.ImageSource)属性，该属性可以设置为从文件、嵌入资源、URI 或流中检索的图像。
- [`Page`](xref:Xamarin.Forms.Page)。 派生自 `Page` 的任何页面类型都具有[`IconImageSource`](xref:Xamarin.Forms.Page.IconImageSource)和[`BackgroundImageSource`](xref:Xamarin.Forms.Page.BackgroundImageSource)属性，这些属性可分配文件、嵌入资源、URI 或流。 在某些情况下，例如何时[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)显示[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)，将显示该图标，如果受平台支持。

  > [!IMPORTANT]
  > 在 iOS 上， [ `Page.IconImageSource` ](xref:Xamarin.Forms.Page.IconImageSource)属性不能填充从资产目录映像组中的映像。 而是从文件、嵌入资源、URI 或流加载 `Page.IconImageSource` 属性的图标图像。

## <a name="embedded-images"></a>嵌入图像

嵌入的图像还提供了应用程序 （例如本地映像），但而不是让每个应用程序的文件结构图像中的映像的副本在程序集作为资源嵌入文件。 此方法分发映像时每个平台上使用相同的映像，建议使用，并且是尤其适合于创建组件，如图像与代码捆绑在一起。

若要在项目中嵌入图像，右键单击要添加新项，然后选择你想要添加的图像/秒。 默认情况下该图像将出现**生成操作： 无**; 这需要设置为**生成操作： EmbeddedResource**。

<!-- markdownlint-disable MD001 -->

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![将生成操作设置为嵌入资源](images-images/vs-buildaction-sml.png)](images-images/vs-buildaction.png#lightbox)

**生成操作**可以查看和更改在**属性**窗口中的文件。

在此示例中的资源 ID 是**WorkingWithImages.beach.jpg**。
IDE 已生成此默认值的串联**默认 Namespace**对于此项目包含文件名，使用句点 （.） 之间的每个值。
<!-- https://msdn.microsoft.com/library/ms950960.aspx -->

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![](images-images/xs-buildaction.png "Set Build Action: EmbeddedResource")

**生成操作**还可以查看和更改**属性**的面板来显示文件。
此面板会显示**资源 ID**用于引用在代码中的资源。 在下面的屏幕截图**资源 ID**是**WorkingWithImages.beach.jpg**。
IDE 已生成此默认值的串联**默认 Namespace**对于此项目包含文件名，使用句点 （.） 之间的每个值。
此 ID 可以在中编辑**属性**pad，但对于这些示例值**WorkingWithImages.beach.jpg**将使用。

[![嵌入资源属性板](images-images/xs-embeddedproperties-sml.png)](images-images/xs-embeddedproperties.png#lightbox)

-----

如果你的项目中，您将嵌入的图像放入文件夹，文件夹名称也用句点分隔 （.） 中的资源 id。 移动**beach.jpg**映像到名为的文件夹**MyImages**会导致资源 ID 为**WorkingWithImages.MyImages.beach.jpg**

用于加载嵌入的图像的代码只是将传递**资源 ID**到[ `ImageSource.FromResource` ](xref:Xamarin.Forms.ImageSource.FromResource*)方法，如下所示：

```csharp
var embeddedImage = new Image { 
      Source = ImageSource.FromResource(
        "WorkingWithImages.beach.jpg", 
        typeof(EmbeddedImages).GetTypeInfo().Assembly
      ) };
```

> [!NOTE]
> 若要支持嵌入的图像显示在通用 Windows 平台上的发布模式下，有必要，请使用的重载`ImageSource.FromResource`，它指定要在其中搜索的图像的源程序集。

目前资源标识符没有隐式转换。 相反，必须使用[ `ImageSource.FromResource` ](xref:Xamarin.Forms.ImageSource.FromResource*)或`new ResourceImageSource()`加载嵌入的图像。

以下屏幕截图显示在每个平台上显示的嵌入的图像的结果：

[![显示嵌入图像的示例应用程序](images-images/resource-sml.png)](images-images/resource.png#lightbox)

### <a name="xaml"></a>XAML

因为不没有从任何内置类型转换器`string`到`ResourceImageSource`，这些类型的映像不能以本机方式加载 XAML。 相反，可以编写简单的自定义 XAML 标记扩展来加载使用图像**资源 ID**在 XAML 中指定：

```csharp
[ContentProperty (nameof(Source))]
public class ImageResourceExtension : IMarkupExtension
{
 public string Source { get; set; }

 public object ProvideValue (IServiceProvider serviceProvider)
 {
   if (Source == null)
   {
     return null;
   }

   // Do your translation lookup here, using whatever method you require
   var imageSource = ImageSource.FromResource(Source, typeof(ImageResourceExtension).GetTypeInfo().Assembly);

   return imageSource;
 }
}
```

> [!NOTE]
> 若要支持嵌入的图像显示在通用 Windows 平台上的发布模式下，有必要，请使用的重载`ImageSource.FromResource`，它指定要在其中搜索的图像的源程序集。

若要使用此扩展插件添加一个自定义`xmlns`到 XAML 中，使用正确的命名空间和程序集值的项目。 然后可以使用以下语法设置图像源： `{local:ImageResource WorkingWithImages.beach.jpg}`。 完整的 XAML 示例如下所示：

```xaml
<?xml version="1.0" encoding="UTF-8" ?>
<ContentPage
   xmlns="http://xamarin.com/schemas/2014/forms"
   xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
   xmlns:local="clr-namespace:WorkingWithImages;assembly=WorkingWithImages"
   x:Class="WorkingWithImages.EmbeddedImagesXaml">
 <StackLayout VerticalOptions="Center" HorizontalOptions="Center">
   <!-- use a custom Markup Extension -->
   <Image Source="{local:ImageResource WorkingWithImages.beach.jpg}" />
 </StackLayout>
</ContentPage>
```

### <a name="troubleshoot-embedded-images"></a>排查嵌入图像问题

#### <a name="debug-code"></a>调试代码

因为它是有时难以理解为什么不加载特定的图像资源，可以暂时将以下调试代码添加到应用程序以帮助确认正确配置了资源。 它会输出所有已知的资源嵌入到给定的程序集**控制台**来帮助调试加载问题的资源。

```csharp
using System.Reflection;
// ...
// NOTE: use for debugging, not in released app code!
var assembly = typeof(EmbeddedImages).GetTypeInfo().Assembly;
foreach (var res in assembly.GetManifestResourceNames())
{
    System.Diagnostics.Debug.WriteLine("found resource: " + res);
}
```

#### <a name="images-embedded-in-other-projects"></a>嵌入到其他项目中的图像

默认情况下`ImageSource.FromResource`方法只会与代码调用同一程序集中的映像`ImageSource.FromResource`方法。 使用上面的调试代码可以确定哪些程序集包含特定资源通过更改`typeof()`语句`Type`已知要处于每个程序集。

但是，将搜索的嵌入图像的源程序集指定的参数为`ImageSource.FromResource`方法：

```csharp
var imageSource = ImageSource.FromResource("filename.png", 
            typeof(MyClass).GetTypeInfo().Assembly);
```

## <a name="download-images"></a>下载映像

以下 XAML 所示，可以显示中，为自动下载图像：

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
       x:Class="WorkingWithImages.DownloadImagesXaml">
  <StackLayout VerticalOptions="Center" HorizontalOptions="Center">
    <Label Text="Image UriSource Xaml" />
    <Image Source="https://xamarin.com/content/images/pages/forms/example-app.png" />
    <Label Text="example-app.png gets downloaded from xamarin.com" />
  </StackLayout>
</ContentPage>
```

等效于C#代码如下所示：

```csharp
var webImage = new Image { 
     Source = ImageSource.FromUri(
        new Uri("https://xamarin.com/content/images/pages/forms/example-app.png")
     ) };
```

[ `ImageSource.FromUri` ](xref:Xamarin.Forms.ImageSource.FromUri(System.Uri))方法需要`Uri`对象，并返回一个新[ `UriImageSource` ](xref:Xamarin.Forms.UriImageSource)读取从`Uri`。

因此，下面的示例也将正常运行，也是有 URI 字符串的隐式转换：

```csharp
webImage.Source = "https://xamarin.com/content/images/pages/forms/example-app.png";
```

以下屏幕截图显示在每个平台上显示的远程图像的结果：

[![显示已下载映像的示例应用程序](images-images/download-sml.png)](images-images/download.png#lightbox)

### <a name="downloaded-image-caching"></a>已下载映像缓存

一个[ `UriImageSource` ](xref:Xamarin.Forms.UriImageSource)还支持下载映像，通过以下属性配置的缓存：

- [`CachingEnabled`](xref:Xamarin.Forms.UriImageSource.CachingEnabled) -是否启用缓存 (`true`默认情况下)。
- [`CacheValidity`](xref:Xamarin.Forms.UriImageSource.CacheValidity) -A `TimeSpan` ，它定义将本地存储图像的时间长度。

默认情况下启用缓存，并将该映像存储 24 小时内的本地。 若要禁用特定的图像的缓存，实例化，如下所示图像源：

```csharp
image.Source = new UriImageSource { CachingEnabled = false, Uri = new Uri("http://server.com/image") };
```

若要设置特定缓存期 （例如，5 天） 实例化图像源，如下所示：

```csharp
webImage.Source = new UriImageSource
{
    Uri = new Uri("https://xamarin.com/content/images/pages/forms/example-app.png"),
    CachingEnabled = true,
    CacheValidity = new TimeSpan(5,0,0,0)
};
```

内置缓存可以轻松支持方案，如每个单元中滚动的映像，其中你可以设置 （或绑定） 映像的列表，并允许内置缓存负责重新加载映像时单元格滚动到视图返回。

## <a name="animated-gifs"></a>动画 Gif

Xamarin 支持显示小型动画 Gif。 这是通过将[`Image.Source`](xref:Xamarin.Forms.Image.Source)属性设置为动画 gif 文件来完成的：

```xaml
<Image Source="demo.gif" />
```

> [!IMPORTANT]
> 虽然 Xamarin 中的动画 GIF 支持能够下载文件，但它不支持缓存或流式处理动画 Gif。

默认情况下，加载动画 GIF 后，不会播放它。 这是因为 `IsAnimationPlaying` 属性控制动画 GIF 是播放还是停止，其默认值为 `false`。 此属性的类型 `bool`是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)对象支持的，这意味着它可以是数据绑定的目标和样式。

因此，在加载动画 GIF 后，将不会播放此动画 GIF，直到 `IsAnimationPlaying` 属性设置为 `true`。 然后，可以通过将 `IsAnimationPlaying` 属性设置为 `false`来停止播放。 请注意，在显示非 GIF 图像源时，此属性不起作用。

> [!NOTE]
> 在 Android 上，动态 GIF 支持要求你的应用程序使用快速呈现器，如果你已选择使用旧呈现器，则不能正常工作。
> 在 UWP 上，动态 GIF 支持要求最小版本的 Windows 10 周年更新（版本1607）。

## <a name="icons-and-splash-screens"></a>图标和初始屏幕

虽然不与相关[ `Image` ](xref:Xamarin.Forms.Image)视图、 应用程序图标和初始屏幕也是 Xamarin.Forms 项目中的映像的一个重要用途。

设置图标和初始屏幕的 Xamarin.Forms 应用可在每个应用程序项目中。 这意味着生成正确调整大小的 iOS、 Android 和 UWP 的映像。 这些映像应名为并位于根据每个平台的要求。

## <a name="icons"></a>图标

有关创建这些应用程序资源的详细信息，请参阅[适用于磁贴和图标资产](/windows/uwp/controls-and-patterns/tiles-and-notifications-app-assets/)的[iOS 使用图像](~/ios/app-fundamentals/images-icons/index.md)、 [Google 插图](https://developer.android.com/design/style/iconography.html)和 UWP 指导原则。

此外， [`Image`](xref:Xamarin.Forms.Image)视图可以通过在 `FontImageSource` 对象中指定字体图标数据来显示字体图标。 有关详细信息，请参阅[字体](~/xamarin-forms/user-interface/text/fonts.md)指南中的[显示字体图标](~/xamarin-forms/user-interface/text/fonts.md#display-font-icons)。

## <a name="splash-screens"></a>初始屏幕

只有 iOS 和 UWP 应用程序需要 （也称为启动屏幕或默认映像） 的初始屏幕。

请参阅的文档[iOS 处理图像](~/ios/app-fundamentals/images-icons/index.md)并[初始屏幕](/windows/uwp/launch-resume/splash-screens/)Windows 开发人员中心上。

## <a name="related-links"></a>相关链接

- [WorkingWithImages （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithimages)
- [iOS 处理图像](~/ios/app-fundamentals/images-icons/index.md)
- [Android 插图](https://developer.android.com/design/style/iconography.html)
- [磁贴和图标资产的指导原则](/windows/uwp/controls-and-patterns/tiles-and-notifications-app-assets/)
