---
title: 将 SkiaSharp 位图保存到文件
description: 了解 SkiaSharp 将位图存储在用户的照片库中支持的各种文件格式。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 2D696CB6-B31B-42BC-8D3B-11D63B1E7D9C
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: 4500714ae28a6cd26fe7f0dc8657f26131bef53d
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2019
ms.locfileid: "70197797"
---
# <a name="saving-skiasharp-bitmaps-to-files"></a>将 SkiaSharp 位图保存到文件

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

SkiaSharp 应用程序具有创建或修改位图后，应用程序可能想要将位图保存到用户的照片库：

![将位图](saving-images/SavingSample.png "将位图")

此任务包含两个步骤：

- 将 SkiaSharp 位图转换为数据中的特定文件格式，如 JPEG 或 PNG。
- 将结果保存到使用特定于平台的代码的照片库。

## <a name="file-formats-and-codecs"></a>文件格式和编解码器

当今流行的位图文件格式使用压缩来减少存储空间的大多数。 两个主要类别的压缩技术被称为_率有损_并_无损_。 这些项中所指示的压缩算法导致数据丢失。

最受欢迎的有损格式由联合图像专家组，称为 JPEG。 JPEG 压缩算法会分析使用称为的数学工具的映像_离散的余弦值转换_，并尝试删除不是保留图像的视觉保真度至关重要的数据。 可以使用通常称为的设置进行控制的压缩程度_质量_。 较高的质量设置导致较大的文件。

与此相反，无损压缩算法分析图像可查看重复和模式可以减少数据但不会导致丢失任何信息的方式编码的像素为单位。 可以从压缩的文件中完全还原原始位图数据。 使用的主无损压缩的文件格式现在是可移植网络图形 (PNG)。

通常情况下，JPEG 用于照片，而 PNG 用于已手动或通过算法生成的映像。 任何无损压缩算法，可减少某些文件的大小必须一定增加的其他人的大小。 幸运的是，这种增加大小通常仅发生在包含很多随机 （或看似随机） 信息的数据。

压缩算法都足够复杂以至需要两个术语描述了压缩及解压缩过程：

- _解码_&mdash;读取位图文件格式并将它解压缩
- _编码_&mdash;压缩位图和写入位图文件格式

[ `SKBitmap` ](xref:SkiaSharp.SKBitmap)类包含多个方法名为`Decode`的创建`SKBitmap`压缩源中。 只需是提供文件名、 流或字节数组。 解码器可以确定文件格式，并将其提交给正确的内部解码函数。

此外， [ `SKCodec` ](xref:SkiaSharp.SKCodec)类有两个方法名为`Create`，可以创建`SKCodec`从压缩源对象，并允许在解码过程中获取更多地涉及应用程序。 (`SKCodec`类在本文中所示[**进行动画处理 SkiaSharp 位图**](animating.md#gif-animation)与解码动画的 GIF 文件。)

对位图进行编码时, 需要更多信息:编码器必须知道应用程序要使用的特定文件格式 (JPEG 或 PNG 或其他内容)。 如果需要有损格式，则编码还必须知道所需的质量级别。

`SKBitmap`类定义了一个[ `Encode` ](xref:SkiaSharp.SKBitmap.Encode(SkiaSharp.SKWStream,SkiaSharp.SKEncodedImageFormat,System.Int32))方法具有以下语法：

```csharp
public Boolean Encode (SKWStream dst, SKEncodedImageFormat format, Int32 quality)
```

稍后在更详细地介绍了这些方法。 编码的位图将写入可写流。 (中的 W`SKWStream`代表"可写"。)第二个和第三个参数指定的文件格式和 （适用于有损格式） 所需的质量，范围从 0 到 100。

此外， [ `SKImage` ](xref:SkiaSharp.SKImage)并[ `SKPixmap` ](xref:SkiaSharp.SKPixmap)类还定义`Encode`的某种程度上更灵活，且可能更喜欢的方法。 您可以轻松地创建`SKImage`对象从`SKBitmap`对象使用静态[ `SKImage.FromBitmap` ](xref:SkiaSharp.SKImage.FromBitmap(SkiaSharp.SKBitmap))方法。 你可以获取`SKPixmap`对象从`SKBitmap`对象使用[ `PeekPixels` ](xref:SkiaSharp.SKBitmap.PeekPixels)方法。

之一[ `Encode` ](xref:SkiaSharp.SKImage.Encode)定义的方法`SKImage`没有参数，会自动保存为 PNG 格式。 该无参数的方法是非常易于使用。

## <a name="platform-specific-code-for-saving-bitmap-files"></a>用于保存位图文件的特定于平台的代码

当编码`SKBitmap`对象到特定文件格式，通常您会得到某种类型的流对象或数组的数据。 一些`Encode`方法 (包括不带任何参数定义的一个`SKImage`) 返回[ `SKData` ](xref:SkiaSharp.SKData)对象，将其转换为使用的字节数组[ `ToArray` ](xref:SkiaSharp.SKData.ToArray)方法。 然后，此数据必须保存到文件中。

正在保存到应用程序本地存储中的文件是非常简单，因为您可以使用标准`System.IO`类和方法，此任务。 在本文中演示此技术[**进行动画处理 SkiaSharp 位图**](animating.md#bitmap-animation)与对一系列的 mandelbrot 位图进行动画处理。

如果你想要由其他应用程序共享的文件，它必须保存到用户的照片库中。 此任务需要特定于平台的代码以及如何使用 Xamarin.Forms [ `DependencyService` ](xref:Xamarin.Forms.DependencyService)。

**SkiaSharpFormsDemo**项目中[ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)应用程序定义`IPhotoLibrary`接口用于`DependencyService`类。 这将定义的语法`SavePhotoAsync`方法：

```csharp
public interface IPhotoLibrary
{
    Task<Stream> PickPhotoAsync();

    Task<bool> SavePhotoAsync(byte[] data, string folder, string filename);
}
```

此接口还定义`PickPhotoAsync`方法，用于打开特定于平台的文件选取器设备的照片库。

有关`SavePhotoAsync`，第一个参数是包含已编码为特定文件格式，如 JPEG 或 PNG 的位图的字节数组。 很可能应用程序可能想要隔离到特定的文件夹，在下一个参数后, 接文件名称中指定它创建的所有位图。 该方法返回一个布尔值，指示成功与否。

以下各节讨论如何`SavePhotoAsync`每个平台上实现。

### <a name="the-ios-implementation"></a>IOS 实现

IOS 实现`SavePhotoAsync`使用[ `SaveToPhotosAlbum` ](xref:UIKit.UIImage.SaveToPhotosAlbum*)方法`UIImage`:

```csharp
public class PhotoLibrary : IPhotoLibrary
{
    ···
    public Task<bool> SavePhotoAsync(byte[] data, string folder, string filename)
    {
        NSData nsData = NSData.FromArray(data);
        UIImage image = new UIImage(nsData);
        TaskCompletionSource<bool> taskCompletionSource = new TaskCompletionSource<bool>();

        image.SaveToPhotosAlbum((UIImage img, NSError error) =>
        {
            taskCompletionSource.SetResult(error == null);
        });

        return taskCompletionSource.Task;
    }
}
```

遗憾的是，没有方法来指定文件名或文件夹的图像。

**Info.plist** iOS 项目中的文件所需，该值指示它将图像添加到照片库的密钥：

```xml
<key>NSPhotoLibraryAddUsageDescription</key>
<string>SkiaSharp Forms Demos adds images to your photo library</string>
```

小心！ 只需访问照片库的权限键是非常相似，但不是相同：

```xml
<key>NSPhotoLibraryUsageDescription</key>
<string>SkiaSharp Forms Demos accesses your photo library</string>
```

### <a name="the-android-implementation"></a>Android 实现

Android 的实现`SavePhotoAsync`首先检查是否`folder`自变量是`null`或空字符串。 如果是这样，位图被保存在照片库的根目录。 否则为获得文件夹，并不存在，如果创建：

```csharp
public class PhotoLibrary : IPhotoLibrary
{
    ···
    public async Task<bool> SavePhotoAsync(byte[] data, string folder, string filename)
    {
        try
        {
            File picturesDirectory = Environment.GetExternalStoragePublicDirectory(Environment.DirectoryPictures);
            File folderDirectory = picturesDirectory;

            if (!string.IsNullOrEmpty(folder))
            {
                folderDirectory = new File(picturesDirectory, folder);
                folderDirectory.Mkdirs();
            }

            using (File bitmapFile = new File(folderDirectory, filename))
            {
                bitmapFile.CreateNewFile();

                using (FileOutputStream outputStream = new FileOutputStream(bitmapFile))
                {
                    await outputStream.WriteAsync(data);
                }

                // Make sure it shows up in the Photos gallery promptly.
                MediaScannerConnection.ScanFile(MainActivity.Instance,
                                                new string[] { bitmapFile.Path },
                                                new string[] { "image/png", "image/jpeg" }, null);
            }
        }
        catch
        {
            return false;
        }

        return true;
    }
}
```

对调用`MediaScannerConnection.ScanFile`并非是严格要求，但如果您要立即查看照片库的测试程序，它可帮助更通过更新库库视图。

**AndroidManifest.xml**文件所需的以下权限标记：

```xml
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

### <a name="the-uwp-implementation"></a>UWP 实现

UWP 实现`SavePhotoAsync`结构中非常类似于 Android 的实现：

```csharp
public class PhotoLibrary : IPhotoLibrary
{
    ···
    public async Task<bool> SavePhotoAsync(byte[] data, string folder, string filename)
    {
        StorageFolder picturesDirectory = KnownFolders.PicturesLibrary;
        StorageFolder folderDirectory = picturesDirectory;

        // Get the folder or create it if necessary
        if (!string.IsNullOrEmpty(folder))
        {
            try
            {
                folderDirectory = await picturesDirectory.GetFolderAsync(folder);
            }
            catch
            { }

            if (folderDirectory == null)
            {
                try
                {
                    folderDirectory = await picturesDirectory.CreateFolderAsync(folder);
                }
                catch
                {
                    return false;
                }
            }
        }

        try
        {
            // Create the file.
            StorageFile storageFile = await folderDirectory.CreateFileAsync(filename,
                                                CreationCollisionOption.GenerateUniqueName);

            // Convert byte[] to Windows buffer and write it out.
            IBuffer buffer = WindowsRuntimeBuffer.Create(data, 0, data.Length, data.Length);
            await FileIO.WriteBufferAsync(storageFile, buffer);
        }
        catch
        {
            return false;
        }

        return true;
    }
}
```

**功能**一部分**Package.appxmanifest**文件所需**图片库**。

## <a name="exploring-the-image-formats"></a>浏览图像格式

下面是[ `Encode` ](xref:SkiaSharp.SKBitmap.Encode(SkiaSharp.SKWStream,SkiaSharp.SKEncodedImageFormat,System.Int32))方法的`SKImage`试：

```csharp
public Boolean Encode (SKWStream dst, SKEncodedImageFormat format, Int32 quality)
```

[`SKEncodedImageFormat`](xref:SkiaSharp.SKEncodedImageFormat) 是一个具有十一个位图文件格式，其中一些相当晦涩引用的成员的枚举：

- `Astc` &mdash; 自适应可缩放的纹理压缩
- `Bmp` &mdash; Windows 位图
- `Dng` &mdash; Adobe 数码底片
- `Gif` &mdash; 图形交换格式
- `Ico` &mdash; Windows 图标图像
- `Jpeg` &mdash; 联合图像专家组
- `Ktx` &mdash; Opengl Khronos 纹理格式
- `Pkm` &mdash; 将文件另存 Pokémon
- `Png` &mdash; 可移植网络图形
- `Wbmp` &mdash; 无线应用协议位图格式 （每像素 1 位）
- `Webp` &mdash; Google WebP 格式

稍后将看到，只有三种文件格式 (`Jpeg`， `Png`，和`Webp`) 实际受 SkiaSharp。

若要保存`SKBitmap`名为对象`bitmap`到用户的照片库中，您还需要属于`SKEncodedImageFormat`枚举命名`imageFormat`和 （适用于有损格式） 整数`quality`变量。 可以使用以下代码以将该位图保存到具有名称的文件`filename`在`folder`文件夹：

```csharp
using (MemoryStream memStream = new MemoryStream())
using (SKManagedWStream wstream = new SKManagedWStream(memStream))
{
    bitmap.Encode(wstream, imageFormat, quality);
    byte[] data = memStream.ToArray();

    // Check the data array for content!

    bool success = await DependencyService.Get<IPhotoLibrary>().SavePhotoAsync(data, folder, filename);

    // Check return value for success!
}
```

`SKManagedWStream`类派生自`SKWStream`（这表示"可写流"）。 `Encode`方法写入该流编码的位图文件。 该代码中的注释是指一些错误检查可能需要执行。

**保存的文件格式**页面[ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)应用程序使用类似的代码，以便您可以试验各种格式保存位图。

XAML 文件包含`SKCanvasView`显示位图时，应用程序页的其余部分包含的所有内容时需要调用`Encode`方法的`SKBitmap`。 它具有`Picker`的成员`SKEncodedImageFormat`枚举，`Slider`质量参数有损位图格式的两个`Entry`输入文件名和文件夹名称时，视图和一个`Button`来保存文件。

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Bitmaps.SaveFileFormatsPage"
             Title="Save Bitmap Formats">

    <StackLayout Margin="10">
        <skiaforms:SKCanvasView PaintSurface="OnCanvasViewPaintSurface"
                                VerticalOptions="FillAndExpand" />

        <Picker x:Name="formatPicker"
                Title="image format"
                SelectedIndexChanged="OnFormatPickerChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKEncodedImageFormat}">
                    <x:Static Member="skia:SKEncodedImageFormat.Astc" />
                    <x:Static Member="skia:SKEncodedImageFormat.Bmp" />
                    <x:Static Member="skia:SKEncodedImageFormat.Dng" />
                    <x:Static Member="skia:SKEncodedImageFormat.Gif" />
                    <x:Static Member="skia:SKEncodedImageFormat.Ico" />
                    <x:Static Member="skia:SKEncodedImageFormat.Jpeg" />
                    <x:Static Member="skia:SKEncodedImageFormat.Ktx" />
                    <x:Static Member="skia:SKEncodedImageFormat.Pkm" />
                    <x:Static Member="skia:SKEncodedImageFormat.Png" />
                    <x:Static Member="skia:SKEncodedImageFormat.Wbmp" />
                    <x:Static Member="skia:SKEncodedImageFormat.Webp" />
                </x:Array>
            </Picker.ItemsSource>
        </Picker>

        <Slider x:Name="qualitySlider"
                Maximum="100"
                Value="50" />

        <Label Text="{Binding Source={x:Reference qualitySlider},
                              Path=Value,
                              StringFormat='Quality = {0:F0}'}"
               HorizontalTextAlignment="Center" />

        <StackLayout Orientation="Horizontal">
            <Label Text="Folder Name: "
                   VerticalOptions="Center" />

            <Entry x:Name="folderNameEntry"
                   Text="SaveFileFormats"
                   HorizontalOptions="FillAndExpand" />
        </StackLayout>

        <StackLayout Orientation="Horizontal">
            <Label Text="File Name: "
                   VerticalOptions="Center" />

            <Entry x:Name="fileNameEntry"
                   Text="Sample.xxx"
                   HorizontalOptions="FillAndExpand" />
        </StackLayout>

        <Button Text="Save"
                Clicked="OnButtonClicked">
            <Button.Triggers>
                <DataTrigger TargetType="Button"
                             Binding="{Binding Source={x:Reference formatPicker},
                                               Path=SelectedIndex}"
                             Value="-1">
                    <Setter Property="IsEnabled" Value="False" />
                </DataTrigger>

                <DataTrigger TargetType="Button"
                             Binding="{Binding Source={x:Reference fileNameEntry},
                                               Path=Text.Length}"
                             Value="0">
                    <Setter Property="IsEnabled" Value="False" />
                </DataTrigger>
            </Button.Triggers>
        </Button>

        <Label x:Name="statusLabel"
               Text="OK"
               Margin="10, 0" />
    </StackLayout>
</ContentPage>
```

代码隐藏文件加载位图资源，并使用`SKCanvasView`显示它。 位图永远不会更改。 `SelectedIndexChanged`处理程序`Picker`修改与该枚举成员相同的扩展名的文件名：

```csharp
public partial class SaveFileFormatsPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(typeof(SaveFileFormatsPage),
        "SkiaSharpFormsDemos.Media.MonkeyFace.png");

    public SaveFileFormatsPage ()
    {
        InitializeComponent ();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        args.Surface.Canvas.DrawBitmap(bitmap, args.Info.Rect, BitmapStretch.Uniform);
    }

    void OnFormatPickerChanged(object sender, EventArgs args)
    {
        if (formatPicker.SelectedIndex != -1)
        {
            SKEncodedImageFormat imageFormat = (SKEncodedImageFormat)formatPicker.SelectedItem;
            fileNameEntry.Text = Path.ChangeExtension(fileNameEntry.Text, imageFormat.ToString());
            statusLabel.Text = "OK";
        }
    }

    async void OnButtonClicked(object sender, EventArgs args)
    {
        SKEncodedImageFormat imageFormat = (SKEncodedImageFormat)formatPicker.SelectedItem;
        int quality = (int)qualitySlider.Value;

        using (MemoryStream memStream = new MemoryStream())
        using (SKManagedWStream wstream = new SKManagedWStream(memStream))
        {
            bitmap.Encode(wstream, imageFormat, quality);
            byte[] data = memStream.ToArray();

            if (data == null)
            {
                statusLabel.Text = "Encode returned null";
            }
            else if (data.Length == 0)
            {
                statusLabel.Text = "Encode returned empty array";
            }
            else
            {
                bool success = await DependencyService.Get<IPhotoLibrary>().
                    SavePhotoAsync(data, folderNameEntry.Text, fileNameEntry.Text);

                if (!success)
                {
                    statusLabel.Text = "SavePhotoAsync return false";
                }
                else
                {
                    statusLabel.Text = "Success!";
                }
            }
        }
    }
}
```

`Clicked`处理程序`Button`所有实际的都工作原理。 获取两个参数`Encode`从`Picker`并`Slider`，然后使用代码来创建`SKManagedWStream`为`Encode`方法。 这两个`Entry`视图提供的文件夹和文件名称`SavePhotoAsync`方法。

此方法的大多数都专门处理问题或错误。 如果`Encode`创建一个空数组，它表示特定文件格式不受支持。 如果`SavePhotoAsync`返回`false`，则文件不是已成功保存。

下面是运行的程序：

[![保存文件格式](saving-images/SaveFileFormats.png "保存文件格式")](saving-images/SaveFileFormats-Large.png#lightbox)

该屏幕截图显示了这些平台受支持的只有三个格式：

- JPEG
- PNG
- WebP

对于所有其他格式，`Encode`方法可写入到流中执行任何操作和结果字节数组为空。

位图的**保存文件格式**网页保存为 600 像素正方形。 使用每像素 4 个字节，这就是 1,440,000 内存中的字节总数。 下表显示了文件格式和质量的各种组合的文件大小：

|格式|质量|大小|
|------|------:|---:|
| PNG | 不可用 | 492 K |
| JPEG | 0 | 为 2.95 K |
|      | 50 | 22.1 K |
|      | 100 | 206 K |
| WebP | 0 | 2.71 K |
|      | 50 | 11.9 K |
|      | 100 | 101 K |

可以试验各种质量设置，并检查结果。

## <a name="saving-finger-paint-art"></a>正在保存 finger-paint 艺术

位图的一个常见用途是在绘图程序，其中它按照那个叫做_卷影位图_。 上的位图，然后显示该程序不会保留所有绘图。 位图也非常有用的将绘图保存。

[**手指绘画中 SkiaSharp** ](../paths/finger-paint.md)文章演示了如何使用跟踪来实现基元手指绘制程序触摸屏输入。 该程序支持只有一个颜色和只有一个笔划宽度，但它保留整个集合中的绘制`SKPath`对象。

**手指绘制与保存**页面[ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)示例还会保留整个集合中的绘制`SKPath`对象，但它还呈现位图，它可以将其保存到照片库上的绘图。

此程序大部分都是类似于原始**手指绘制**程序。 一个增强功能是 XAML 文件现在实例化标记按钮**清晰**并**保存**:

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             xmlns:tt="clr-namespace:TouchTracking"
             x:Class="SkiaSharpFormsDemos.Bitmaps.FingerPaintSavePage"
             Title="Finger Paint Save">

    <StackLayout>
        <Grid BackgroundColor="White"
              VerticalOptions="FillAndExpand">
            <skia:SKCanvasView x:Name="canvasView"
                               PaintSurface="OnCanvasViewPaintSurface" />
            <Grid.Effects>
                <tt:TouchEffect Capture="True"
                                TouchAction="OnTouchEffectAction" />
            </Grid.Effects>
        </Grid>

        <Grid>
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="*" />
                <ColumnDefinition Width="*" />
            </Grid.ColumnDefinitions>
        </Grid>

        <Button Text="Clear"
                Grid.Row="0"
                Margin="50, 5"
                Clicked="OnClearButtonClicked" />

        <Button Text="Save"
                Grid.Row="1"
                Margin="50, 5"
                Clicked="OnSaveButtonClicked" />

    </StackLayout>
</ContentPage>
```

代码隐藏文件维护类型的字段`SKBitmap`名为`saveBitmap`。 创建或重新创建此位图`PaintSurface`处理程序时的显示大小呈现更改。 如果需要重新创建位图，现有的位图的内容复制到新的位图，以便无论显示图面中大小的更改保留的所有内容：

```csharp
public partial class FingerPaintSavePage : ContentPage
{
    ···
    SKBitmap saveBitmap;

    public FingerPaintSavePage ()
    {
        InitializeComponent ();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        // Create bitmap the size of the display surface
        if (saveBitmap == null)
        {
            saveBitmap = new SKBitmap(info.Width, info.Height);
        }
        // Or create new bitmap for a new size of display surface
        else if (saveBitmap.Width < info.Width || saveBitmap.Height < info.Height)
        {
            SKBitmap newBitmap = new SKBitmap(Math.Max(saveBitmap.Width, info.Width),
                                              Math.Max(saveBitmap.Height, info.Height));

            using (SKCanvas newCanvas = new SKCanvas(newBitmap))
            {
                newCanvas.Clear();
                newCanvas.DrawBitmap(saveBitmap, 0, 0);
            }

            saveBitmap = newBitmap;
        }

        // Render the bitmap
        canvas.Clear();
        canvas.DrawBitmap(saveBitmap, 0, 0);
    }
    ···
}
```

通过绘制`PaintSurface`处理程序结束时，会发生，仅呈现位图。

触摸处理是类似于早期版本的程序。 程序维护两个集合`inProgressPaths`和`completedPaths`，其中包含用户已绘制自上次显示已清除的所有内容。 对于每个触控事件，`OnTouchEffectAction`处理程序调用`UpdateBitmap`:

```csharp
public partial class FingerPaintSavePage : ContentPage
{
    Dictionary<long, SKPath> inProgressPaths = new Dictionary<long, SKPath>();
    List<SKPath> completedPaths = new List<SKPath>();

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Blue,
        StrokeWidth = 10,
        StrokeCap = SKStrokeCap.Round,
        StrokeJoin = SKStrokeJoin.Round
    };
    ···
    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        switch (args.Type)
        {
            case TouchActionType.Pressed:
                if (!inProgressPaths.ContainsKey(args.Id))
                {
                    SKPath path = new SKPath();
                    path.MoveTo(ConvertToPixel(args.Location));
                    inProgressPaths.Add(args.Id, path);
                    UpdateBitmap();
                }
                break;

            case TouchActionType.Moved:
                if (inProgressPaths.ContainsKey(args.Id))
                {
                    SKPath path = inProgressPaths[args.Id];
                    path.LineTo(ConvertToPixel(args.Location));
                    UpdateBitmap();
                }
                break;

            case TouchActionType.Released:
                if (inProgressPaths.ContainsKey(args.Id))
                {
                    completedPaths.Add(inProgressPaths[args.Id]);
                    inProgressPaths.Remove(args.Id);
                    UpdateBitmap();
                }
                break;

            case TouchActionType.Cancelled:
                if (inProgressPaths.ContainsKey(args.Id))
                {
                    inProgressPaths.Remove(args.Id);
                    UpdateBitmap();
                }
                break;
        }
    }

    SKPoint ConvertToPixel(Point pt)
    {
        return new SKPoint((float)(canvasView.CanvasSize.Width * pt.X / canvasView.Width),
                            (float)(canvasView.CanvasSize.Height * pt.Y / canvasView.Height));
    }

    void UpdateBitmap()
    {
        using (SKCanvas saveBitmapCanvas = new SKCanvas(saveBitmap))
        {
            saveBitmapCanvas.Clear();

            foreach (SKPath path in completedPaths)
            {
                saveBitmapCanvas.DrawPath(path, paint);
            }

            foreach (SKPath path in inProgressPaths.Values)
            {
                saveBitmapCanvas.DrawPath(path, paint);
            }
        }

        canvasView.InvalidateSurface();
    }
    ···
}
```

`UpdateBitmap`方法可重绘`saveBitmap`通过创建一个新`SKCanvas`，清除它，然后再呈现位图上的所有路径。 它使最后`canvasView`，以便可以显示在绘制位图。

以下是两个按钮的处理程序。 **清晰**按钮将清除这两个路径的集合，更新`saveBitmap`（这会导致清除位图），并使失效`SKCanvasView`:

```csharp
public partial class FingerPaintSavePage : ContentPage
{
    ···
    void OnClearButtonClicked(object sender, EventArgs args)
    {
        completedPaths.Clear();
        inProgressPaths.Clear();
        UpdateBitmap();
        canvasView.InvalidateSurface();
    }

    async void OnSaveButtonClicked(object sender, EventArgs args)
    {
        using (SKImage image = SKImage.FromBitmap(saveBitmap))
        {
            SKData data = image.Encode();
            DateTime dt = DateTime.Now;
            string filename = String.Format("FingerPaint-{0:D4}{1:D2}{2:D2}-{3:D2}{4:D2}{5:D2}{6:D3}.png",
                                            dt.Year, dt.Month, dt.Day, dt.Hour, dt.Minute, dt.Second, dt.Millisecond);

            IPhotoLibrary photoLibrary = DependencyService.Get<IPhotoLibrary>();
            bool result = await photoLibrary.SavePhotoAsync(data.ToArray(), "FingerPaint", filename);

            if (!result)
            {
                await DisplayAlert("FingerPaint", "Artwork could not be saved. Sorry!", "OK");
            }
        }
    }
}
```

**保存**按钮处理程序使用简化[ `Encode` ](xref:SkiaSharp.SKImage.Encode)方法从`SKImage`。 此方法将编码使用 PNG 格式。 `SKImage`对象会根据创建`saveBitmap`，和`SKData`对象包含已编码的 PNG 文件。

`ToArray`方法的`SKData`获取字节数组。 这是传递给`SavePhotoAsync`方法，以及一个固定的文件夹名称和唯一的文件名构造从当前日期和时间。

下面是该程序的操作：

[![指保存画图](saving-images/FingerPaintSave.png "手指画图保存")](saving-images/FingerPaintSave-Large.png#lightbox)

在使用非常类似的技术[ **SpinPaint** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-spinpaint)示例。 这也是一个手指绘制的程序，不同之处在于用户绘制然后重现上其其他四个象限的设计的旋转磁盘上。 旋转为磁盘的手指绘制更改颜色：

[![启动画图](saving-images/SpinPaint.png "启动画图")](saving-images/SpinPaint-Large.png#lightbox)

**保存**的按钮`SpinPaint`类是类似于**手指绘制**在于，它将图像保存到固定的文件夹名称 (**SpainPaint**) 和从构造文件名日期和时间。

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [SpinPaint （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-spinpaint)
