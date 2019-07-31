---
title: 访问 SkiaSharp 位图像素位
description: 了解用于访问和修改 SkiaSharp 位图的像素位的各种技术。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: DBB58522-F816-4A8C-96A5-E0236F16A5C6
author: davidbritch
ms.author: dabritch
ms.date: 07/11/2018
ms.openlocfilehash: 6c066f89dc8f558a9154138bf38ad4326fe21291
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68642519"
---
# <a name="accessing-skiasharp-bitmap-pixel-bits"></a>访问 SkiaSharp 位图像素位

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

本文中所示[**到文件的保存 SkiaSharp 位图**](saving.md)，位图通常存储在文件中的压缩格式，如 JPEG 或 PNG。 在与此相反，不压缩 SkiaSharp 位图存储在内存中。 已存储为一系列连续的像素为单位。 此压缩的格式便于将位图转移到显示图面。

SkiaSharp 位图占用的内存块以非常简单的方式进行组织:它从从左到右的第一行开始, 然后继续第二行。 对于全彩色位图，每个像素由四个字节，这意味着位图所需的总内存空间是四次其宽度和高度的产品。

本指南介绍了如何应用程序有权访问这些像素，通过访问位图的像素的内存块，直接或间接。 在某些情况下，程序可能想要分析的图像的像素为单位，并构造某种类型的直方图。 更常见的是，应用程序可以通过从算法上创建构成位图像素构造唯一映像：

![像素位样本](pixel-bits-images/PixelBitsSample.png "像素位示例")

## <a name="the-techniques"></a>技术

SkiaSharp 提供几种方法用于访问位图的像素位。 你选择哪一个通常是编码 （这与维护和易用性调试相关） 的方便和性能权衡。 在大多数情况下，你将使用以下方法之一和属性的`SKBitmap`用于访问位图的像素为单位：

- `GetPixel`和`SetPixel`方法允许您获取或设置单个像素的颜色。
- `Pixels`属性获取整个位图的像素颜色的数组，或设置颜色的数组。
- `GetPixels` 返回所使用的位图的像素内存地址。
- `SetPixels` 将替换所使用的位图的像素内存的地址。

您可以将为"高级别"的前两个技术和第二个两个作为"低级别。 有一些其他方法和属性，您可以使用，但这些是最有价值。

若要允许你查看这些技术之间的性能差异[ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)应用程序包含一个名为页**渐变位图**，创建使用组合创建渐变的红色和蓝色阴影的像素的位图。 程序创建八个不同副本，此位图，所有使用的不同的方法用于设置位图像素。 每个这些八个位图创建在单独的方法，还设置该技术的简短文本说明，并计算所需设置所有像素为单位的时间。 每个方法循环访问的像素设置逻辑 100 次若要更好地估计的性能。

### <a name="the-setpixel-method"></a>SetPixel 方法

如果您只需设置或获取多个单个像素[ `SetPixel` ](xref:SkiaSharp.SKBitmap.SetPixel(System.Int32,System.Int32,SkiaSharp.SKColor))并[ `GetPixel` ](xref:SkiaSharp.SKBitmap.GetPixel(System.Int32,System.Int32))方法是理想之选。 对于每个这两种方法，您指定的整数列和行。 无论像素格式，这两种方法让你获取或设置作为像素`SKColor`值：

```csharp
bitmap.SetPixel(col, row, color);

SKColor color = bitmap.GetPixel(col, row);
```

`col`参数必须介于 0 和一个不会早于`Width`属性的位图，并`row`范围是从 0 到一个不会早于`Height`属性。

下面是该方法中**渐变位图**设置的位图使用内容`SetPixel`方法。 位图为 256 x 256 像素和`for`循环是硬编码值的范围内：

```csharp
public class GradientBitmapPage : ContentPage
{
    const int REPS = 100;

    Stopwatch stopwatch = new Stopwatch();
    ···
    SKBitmap FillBitmapSetPixel(out string description, out int milliseconds)
    {
        description = "SetPixel";
        SKBitmap bitmap = new SKBitmap(256, 256);

        stopwatch.Restart();

        for (int rep = 0; rep < REPS; rep++)
            for (int row = 0; row < 256; row++)
                for (int col = 0; col < 256; col++)
                {
                    bitmap.SetPixel(col, row, new SKColor((byte)col, 0, (byte)row));
                }

        milliseconds = (int)stopwatch.ElapsedMilliseconds;
        return bitmap;
    }
    ···
}
```

设置每个像素的颜色具有的红色组件等于位图列和行的蓝色组件等于。 在左上角，红色在右上角，在左下角和右下角，使用其他位置渐变在洋红色蓝黑色合成的位图。

`SetPixel`方法是调用 65,536 时间，而不考虑此方法可能是如何有效地，它通常不是提供一种替代方法时进行，许多 API 调用一个好办法。 幸运的是，有多种替代方法。

### <a name="the-pixels-property"></a>像素属性

`SKBitmap` 定义[ `Pixels` ](xref:SkiaSharp.SKBitmap.Pixels)返回的数组的属性`SKColor`整个位图的值。 此外可以使用`Pixels`设置位图的颜色值的数组：

```csharp
SKColor[] pixels = bitmap.Pixels;

bitmap.Pixels = pixels;
```

像素排列开始的数组中的第一行中，从左到右，然后第二行，等等。 数组中的总数等于位图宽度和高度的产品。

虽然此属性显示为有效，但请记住，像素所复制到数组，位图从和恢复到在位图中，数组并将像素转换从 / 向`SKColor`值。

下面是该方法中`GradientBitmapPage`类，用于设置位图使用`Pixels`属性。 方法分配`SKColor`数组所需的大小，但它也可以使用`Pixels`属性以创建该数组：

```csharp
SKBitmap FillBitmapPixelsProp(out string description, out int milliseconds)
{
    description = "Pixels property";
    SKBitmap bitmap = new SKBitmap(256, 256);

    stopwatch.Restart();

    SKColor[] pixels = new SKColor[256 * 256];

    for (int rep = 0; rep < REPS; rep++)
        for (int row = 0; row < 256; row++)
            for (int col = 0; col < 256; col++)
            {
                pixels[256 * row + col] = new SKColor((byte)col, 0, (byte)row);
            }

    bitmap.Pixels = pixels;

    milliseconds = (int)stopwatch.ElapsedMilliseconds;
    return bitmap;
}
```

请注意的索引`pixels`数组需要从计算`row`和`col`变量。 行乘以每个行 (在此情况下的 256) 中的像素数，然后添加列。

`SKBitmap` 此外定义了一个类似`Bytes`属性，这会返回整个位图的字节数组，但较为繁琐的全彩色位图。

### <a name="the-getpixels-pointer"></a>GetPixels 指针

可能是功能最强大的技术来访问的位图像素[ `GetPixels` ](xref:SkiaSharp.SKBitmap.GetPixels)，不会与混淆`GetPixel`方法或`Pixels`属性。 将立即注意到与差异`GetPixels`，它将返回不在 C# 编程中非常常见的内容：

```csharp
IntPtr pixelsAddr = bitmap.GetPixels();
```

.NET [ `IntPtr` ](xref:System.IntPtr)类型表示的指针。 它称为`IntPtr`因为它是本机处理器的程序正在运行的通常 32 位或 64 位的长度在计算机上的一个整数的长度。 `IntPtr`的`GetPixels`返回是实际的位图对象正在使用它来存储其像素为单位的内存块的地址。

可以将转换`IntPtr`为 C# 指针类型，并使用[ `ToPointer` ](xref:System.IntPtr.ToPointer)方法。 C# 指针语法是 C 和 c + + 相同：

```csharp
byte* ptr = (byte*)pixelsAddr.ToPointer();
```

`ptr`变量的类型是_字节的指针_。 这`ptr`变量，可以访问用于存储位图的像素为单位的各个字节的内存。 使用类似下面的代码从此内存中读取一个字节或写入一个字节的内存：

```csharp
byte pixelComponent = *ptr;

*ptr = pixelComponent;
```

在此上下文中，星号是 C#_间接寻址运算符_，用于引用指向的内存内容`ptr`。 最初，`ptr`指向第一个像素的位图，但您的第一行的第一个字节可以对执行算术`ptr`变量以将其移到位图中的其他位置。

有一个缺点是，可以使用这`ptr`仅在代码块中的变量标记为`unsafe`关键字。 此外，该程序集必须标记为允许不安全的块。 这是在项目的属性。

在 C# 中使用指针是功能非常强大，但也非常危险。 需要小心，不要访问超出指针是要引用的内存。 这就是原因指针使用程序关联以单词"不安全。"

下面是该方法中`GradientBitmapPage`类，该类使用`GetPixels`方法。 请注意`unsafe`包含所有使用的字节指针的代码块：

```csharp
SKBitmap FillBitmapBytePtr(out string description, out int milliseconds)
{
    description = "GetPixels byte ptr";
    SKBitmap bitmap = new SKBitmap(256, 256);

    stopwatch.Restart();

    IntPtr pixelsAddr = bitmap.GetPixels();

    unsafe
    {
        for (int rep = 0; rep < REPS; rep++)
        {
            byte* ptr = (byte*)pixelsAddr.ToPointer();

            for (int row = 0; row < 256; row++)
                for (int col = 0; col < 256; col++)
                {
                    *ptr++ = (byte)(col);   // red
                    *ptr++ = 0;             // green
                    *ptr++ = (byte)(row);   // blue
                    *ptr++ = 0xFF;          // alpha
                }
        }
    }

    milliseconds = (int)stopwatch.ElapsedMilliseconds;
    return bitmap;
}
```

当`ptr`从第一次获取变量`ToPointer`方法，它指向最左侧像素的位图的第一行的第一个字节。 `for`的循环`row`并`col`设置，以便`ptr`可以使用递增`++`运算符后设置每个像素的每个字节。 有关通过像素，其他 99 循环`ptr`必须重新设置为位图的开头。

每个像素都是内存的 4 个字节，因此必须单独设置每个字节。 下面的代码假定字节为顺序红色、 绿色、 蓝色和 alpha，这是与一致`SKColorType.Rgba8888`颜色类型。 你可能还记得这是适用于 iOS 和 Android，而不是通用 Windows 平台的默认颜色类型。 默认情况下，UWP 创建的位图`SKColorType.Bgra8888`颜色类型。 出于此原因，会在该平台上看到一些不同的结果 ！

可以从返回的值强制转换`ToPointer`到`uint`指针而不是一个`byte`指针。 这允许要在一个语句中访问整个像素。 将应用`++`到该指针的运算符将它加 4 个字节，以指向下一个像素：

```csharp
public class GradientBitmapPage : ContentPage
{
    ···
    SKBitmap FillBitmapUintPtr(out string description, out int milliseconds)
    {
        description = "GetPixels uint ptr";
        SKBitmap bitmap = new SKBitmap(256, 256);

        stopwatch.Restart();

        IntPtr pixelsAddr = bitmap.GetPixels();

        unsafe
        {
            for (int rep = 0; rep < REPS; rep++)
            {
                uint* ptr = (uint*)pixelsAddr.ToPointer();

                for (int row = 0; row < 256; row++)
                    for (int col = 0; col < 256; col++)
                    {
                        *ptr++ = MakePixel((byte)col, 0, (byte)row, 0xFF);
                    }
            }
        }

        milliseconds = (int)stopwatch.ElapsedMilliseconds;
        return bitmap;
    }
    ···
    [MethodImpl(MethodImplOptions.AggressiveInlining)]
    uint MakePixel(byte red, byte green, byte blue, byte alpha) =>
            (uint)((alpha << 24) | (blue << 16) | (green << 8) | red);
    ···
}
```

使用设置像素`MakePixel`方法，构造从红色、 绿色、 蓝色和 alpha 组件整数像素。 请记住，`SKColorType.Rgba8888`格式具有像素字节顺序如下：

RR GG BB AA

但这些字节相对应的整数：

AABBGGRR

根据小字节序体系结构首先存储最低有效字节的整数。 这`MakePixel`方法将无法正常使用的位图的`Bgra8888`颜色类型。

`MakePixel`方法将标记与[ `MethodImplOptions.AggressiveInlining` ](xref:System.Runtime.CompilerServices.MethodImplOptions)鼓励编译器避免使此单独的方法，而要编译的代码调用该方法的选项。 这应提高性能。

有趣的是，`SKColor`结构定义的显式转换`SKColor`为无符号整数，这意味着`SKColor`可以创建值，并转换为`uint`可用而不是`MakePixel`:

```csharp
SKBitmap FillBitmapUintPtrColor(out string description, out int milliseconds)
{
    description = "GetPixels SKColor";
    SKBitmap bitmap = new SKBitmap(256, 256);

    stopwatch.Restart();

    IntPtr pixelsAddr = bitmap.GetPixels();

    unsafe
    {
        for (int rep = 0; rep < REPS; rep++)
        {
            uint* ptr = (uint*)pixelsAddr.ToPointer();

            for (int row = 0; row < 256; row++)
                for (int col = 0; col < 256; col++)
                {
                    *ptr++ = (uint)new SKColor((byte)col, 0, (byte)row);
                }
        }
    }

    milliseconds = (int)stopwatch.ElapsedMilliseconds;
    return bitmap;
}
```

唯一的问题是:`SKColor`值的整数格式是以`SKColorType.Rgba8888` `SKColorType.Bgra8888`颜色类型的顺序或颜色类型, 还是完全是其他内容？ 应很快显示该问题的答案。

### <a name="the-setpixels-method"></a>SetPixels 方法

`SKBitmap` 此外定义了一个名为方法[ `SetPixels` ](xref:SkiaSharp.SKBitmap.SetPixels(System.IntPtr))，调用如下：

```csharp
bitmap.SetPixels(intPtr);
```

请记住，`GetPixels`获取`IntPtr`引用用于存储其像素的位图的内存块。 `SetPixels`调用_替换_该块内存引用的内存块`IntPtr`指定为`SetPixels`参数。 位图将释放以前使用的内存块。 下一次`GetPixels`是调用，获取与设置的内存块`SetPixels`。

首先，它看起来像`SetPixels`没有更多电源和性能而不为您提供了`GetPixels`而且太方便。 使用`GetPixels`获取位图的内存块并对其进行访问。 使用`SetPixels`分配和访问一些内存，然后将其设为位图的内存块。

但使用`SetPixels`提供了独特的语法优势:它允许你使用数组访问位图像素位。 下面是该方法中`GradientBitmapPage`演示此技术。 该方法首先定义对应的位图的像素的字节的多维度的字节数组。 第一个维度是行，第二个维度列和第三个维度对应于每个像素的四个组件：

```csharp
SKBitmap FillBitmapByteBuffer(out string description, out int milliseconds)
{
    description = "SetPixels byte buffer";
    SKBitmap bitmap = new SKBitmap(256, 256);

    stopwatch.Restart();

    byte[,,] buffer = new byte[256, 256, 4];

    for (int rep = 0; rep < REPS; rep++)
        for (int row = 0; row < 256; row++)
            for (int col = 0; col < 256; col++)
            {
                buffer[row, col, 0] = (byte)col;   // red
                buffer[row, col, 1] = 0;           // green
                buffer[row, col, 2] = (byte)row;   // blue
                buffer[row, col, 3] = 0xFF;        // alpha
            }

    unsafe
    {
        fixed (byte* ptr = buffer)
        {
            bitmap.SetPixels((IntPtr)ptr);
        }
    }

    milliseconds = (int)stopwatch.ElapsedMilliseconds;
    return bitmap;
}
```

然后，在该数组已填充的像素之后,`unsafe`块和一个`fixed`语句用于获取指向此数组的字节指针。 然后，该字节的指针可以转换为`IntPtr`要传递给`SetPixels`。

您创建的数组不需要的字节数组。 它可以是具有行和列只有两个维度的整数数组：

```csharp
SKBitmap FillBitmapUintBuffer(out string description, out int milliseconds)
{
    description = "SetPixels uint buffer";
    SKBitmap bitmap = new SKBitmap(256, 256);

    stopwatch.Restart();

    uint[,] buffer = new uint[256, 256];

    for (int rep = 0; rep < REPS; rep++)
        for (int row = 0; row < 256; row++)
            for (int col = 0; col < 256; col++)
            {
                buffer[row, col] = MakePixel((byte)col, 0, (byte)row, 0xFF);
            }

    unsafe
    {
        fixed (uint* ptr = buffer)
        {
            bitmap.SetPixels((IntPtr)ptr);
        }
    }

    milliseconds = (int)stopwatch.ElapsedMilliseconds;
    return bitmap;
}
```

`MakePixel`方法再次用于将颜色组件组合到 32 位像素。

为了保持完整性，下面是相同的代码，但与`SKColor`值强制转换为无符号整数：

```csharp
SKBitmap FillBitmapUintBufferColor(out string description, out int milliseconds)
{
    description = "SetPixels SKColor";
    SKBitmap bitmap = new SKBitmap(256, 256);

    stopwatch.Restart();

    uint[,] buffer = new uint[256, 256];

    for (int rep = 0; rep < REPS; rep++)
        for (int row = 0; row < 256; row++)
            for (int col = 0; col < 256; col++)
            {
                buffer[row, col] = (uint)new SKColor((byte)col, 0, (byte)row);
            }

    unsafe
    {
        fixed (uint* ptr = buffer)
        {
            bitmap.SetPixels((IntPtr)ptr);
        }
    }

    milliseconds = (int)stopwatch.ElapsedMilliseconds;
    return bitmap;
}
```

### <a name="comparing-the-techniques"></a>比较方法

构造函数**渐变颜色**页调用所有八个，如上所示的方法，并将结果保存：

```csharp
public class GradientBitmapPage : ContentPage
{
    ···
    string[] descriptions = new string[8];
    SKBitmap[] bitmaps = new SKBitmap[8];
    int[] elapsedTimes = new int[8];

    SKCanvasView canvasView;

    public GradientBitmapPage ()
    {
        Title = "Gradient Bitmap";

        bitmaps[0] = FillBitmapSetPixel(out descriptions[0], out elapsedTimes[0]);
        bitmaps[1] = FillBitmapPixelsProp(out descriptions[1], out elapsedTimes[1]);
        bitmaps[2] = FillBitmapBytePtr(out descriptions[2], out elapsedTimes[2]);
        bitmaps[4] = FillBitmapUintPtr(out descriptions[4], out elapsedTimes[4]);
        bitmaps[6] = FillBitmapUintPtrColor(out descriptions[6], out elapsedTimes[6]);
        bitmaps[3] = FillBitmapByteBuffer(out descriptions[3], out elapsedTimes[3]);
        bitmaps[5] = FillBitmapUintBuffer(out descriptions[5], out elapsedTimes[5]);
        bitmaps[7] = FillBitmapUintBufferColor(out descriptions[7], out elapsedTimes[7]);

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }
    ···
}
```

构造函数创建最后`SKCanvasView`要显示的结果位图。 `PaintSurface`处理程序将其图面划分为八个矩形和调用`Display`以显示每个：

```csharp
public class GradientBitmapPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        int width = info.Width;
        int height = info.Height;

        canvas.Clear();

        Display(canvas, 0, new SKRect(0, 0, width / 2, height / 4));
        Display(canvas, 1, new SKRect(width / 2, 0, width, height / 4));
        Display(canvas, 2, new SKRect(0, height / 4, width / 2, 2 * height / 4));
        Display(canvas, 3, new SKRect(width / 2, height / 4, width, 2 * height / 4));
        Display(canvas, 4, new SKRect(0, 2 * height / 4, width / 2, 3 * height / 4));
        Display(canvas, 5, new SKRect(width / 2, 2 * height / 4, width, 3 * height / 4));
        Display(canvas, 6, new SKRect(0, 3 * height / 4, width / 2, height));
        Display(canvas, 7, new SKRect(width / 2, 3 * height / 4, width, height));
    }

    void Display(SKCanvas canvas, int index, SKRect rect)
    {
        string text = String.Format("{0}: {1:F1} msec", descriptions[index],
                                    (double)elapsedTimes[index] / REPS);

        SKRect bounds = new SKRect();

        using (SKPaint textPaint = new SKPaint())
        {
            textPaint.TextSize = (float)(12 * canvasView.CanvasSize.Width / canvasView.Width);
            textPaint.TextAlign = SKTextAlign.Center;
            textPaint.MeasureText("Tly", ref bounds);

            canvas.DrawText(text, new SKPoint(rect.MidX, rect.Bottom - bounds.Bottom), textPaint);
            rect.Bottom -= bounds.Height;
            canvas.DrawBitmap(bitmaps[index], rect, BitmapStretch.Uniform);
        }
    }
}
```

若要允许编译器以优化的代码，此页已在运行**版本**模式。 下面是在 MacBook Pro、 Nexus 5 Android 手机和运行 Windows 10 Surface Pro 3 iPhone 8 模拟器上运行该页面。 由于存在硬件差异，避免比较设备之间的性能时间，但改为查看相对时间，每个设备上：

[![渐变位图](pixel-bits-images/GradientBitmap.png "渐变位图")](pixel-bits-images/GradientBitmap-Large.png#lightbox)

下面是将合并执行时间以毫秒为单位的表：

| API       | 数据类型 | iOS  | Android | UWP  |
| --------- | --------- | ----:| -------:| ----:|
| SetPixel  |           | 3.17 |   10.77 | 3.49 |
| 像素    |           | 0.32 |    1.23 | 0.07 |
| GetPixels | byte      | 0.09 |    价格为 $0.24 | 0.10 |
|           | uint      | 0.06 |    0.26 之间 | 0.05 |
|           | SKColor   | 0.29 |    0.99 | 0.07 |
| SetPixels | byte      | 1.33 |    6.78 | 0.11 |
|           | uint      | 0.14 |    0.69 | 0.06 |
|           | SKColor   | 0.35 |    1.93 | 0.10 |

按预期方式调用`SetPixel`65,536 情况下是设置位图的像素为单位的最少 effeicient 方法。 填充`SKColor`数组和设置`Pixels`属性是好得多，甚至更具优势的某些`GetPixels`和`SetPixels`技术。 使用`uint`像素值是通常比单独设置更快`byte`组件，并将转换`SKColor`为无符号整数值到进程会增加一些开销。

比较各种渐变也是有意义的:每个平台的最前面几行相同, 并按预期显示渐变。 这意味着`SetPixel`方法和`Pixels`属性正确创建颜色而不考虑基础的像素格式中的像素为单位。

IOS 和 Android 的屏幕截图的接下来两行也是相同的这可确认的一小`MakePixel`方法已正确定义默认值为`Rgba8888`这些平台的像素格式。

IOS 和 Android 的屏幕截图的底部行是向后，这指示无符号的整数获取通过强制转换`SKColor`值是窗体中：

AARRGGBB

字节的顺序：

BB GG RR AA

这是`Bgra8888`排序而不是`Rgba8888`排序。 `Brga8888`格式是通用 Windows 平台，这是为什么该屏幕快照的最后一行上的渐变的第一行与相同的默认值。 但中间两个行是不正确，因为创建这些位图的代码假定`Rgba8888`排序。

如果你想要用于访问每个平台上的像素位使用相同的代码，可以显式创建`SKBitmap`使用`Rgba8888`或`Bgra8888`格式。 如果你想要强制转换`SKColor`位图像素为单位的值使用`Bgra8888`。

## <a name="random-access-of-pixels"></a>随机访问的像素为单位

`FillBitmapBytePtr`并`FillBitmapUintPtr`中的方法**渐变位图**页受益`for`循环按顺序，从顶部到底部的行，并从左到右每行中的行填充位图。 可以使用递增指针在同一语句设置像素。

有时是需要随机而不是按顺序访问像素。 如果您使用的`GetPixels`方法时，您将需要计算基于行和列的指针。 了这一点**彩虹正弦**页上，创建一个周期的正弦曲线的形式显示出喷薄彩虹的位图。

出喷薄彩虹的颜色是最简单的方法创建使用 HSL （色调、 饱和度、 亮度） 颜色模型。 `SKColor.FromHsl`方法创建`SKColor`值使用色调值范围从 0 到 360 之间 （如一个圆圈，但从红色、 绿色和蓝色，并返回到红色的角度） 和饱和度和亮度值范围从 0 到 100 之间。 对于出喷薄彩虹的颜色，饱和度应设置为最多为 100、 50 的中点的亮度。

**出喷薄彩虹正弦**通过循环遍历的位图，行，然后遍历 360 色调值来创建此映像。 从每个的色调值，它会计算也基于正弦值的位图列：

```csharp
public class RainbowSinePage : ContentPage
{
    SKBitmap bitmap;

    public RainbowSinePage()
    {
        Title = "Rainbow Sine";

        bitmap = new SKBitmap(360 * 3, 1024, SKColorType.Bgra8888, SKAlphaType.Unpremul);

        unsafe
        {
            // Pointer to first pixel of bitmap
            uint* basePtr = (uint*)bitmap.GetPixels().ToPointer();

            // Loop through the rows
            for (int row = 0; row < bitmap.Height; row++)
            {
                // Calculate the sine curve angle and the sine value
                double angle = 2 * Math.PI * row / bitmap.Height;
                double sine = Math.Sin(angle);

                // Loop through the hues
                for (int hue = 0; hue < 360; hue++)
                {
                    // Calculate the column
                    int col = (int)(360 + 360 * sine + hue);

                    // Calculate the address
                    uint* ptr = basePtr + bitmap.Width * row + col;

                    // Store the color value
                    *ptr = (uint)SKColor.FromHsl(hue, 100, 50);
                }
            }
        }

        // Create the SKCanvasView
        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        canvas.DrawBitmap(bitmap, info.Rect);
    }
}
```

请注意，该构造函数创建基于位图`SKColorType.Bgra8888`格式：

```csharp
bitmap = new SKBitmap(360 * 3, 1024, SKColorType.Bgra8888, SKAlphaType.Unpremul);
```

这将允许程序使用的转换`SKColor`值到`uint`无需担心的像素为单位。 尽管在此特定的程序，它不起作用，您每次使用`SKColor`转换设置像素为单位，你还应指定`SKAlphaType.Unpremul`因为`SKColor`不会将其颜色组件乘以 alpha 值。

然后，构造函数使用`GetPixels`方法以获取指向第一个像素的位图：

```csharp
uint* basePtr = (uint*)bitmap.GetPixels().ToPointer();
```

对于任何特定行和列，必须将偏移量的值添加到`basePtr`。 此偏移量是时间位图宽度，以及列的行：

```csharp
uint* ptr = basePtr + bitmap.Width * row + col;
```

`SKColor`值存储在内存中使用此指针：

```csharp
*ptr = (uint)SKColor.FromHsl(hue, 100, 50);
```

在中`PaintSurface`处理程序的`SKCanvasView`，位图拉伸以填充显示区域：

[![出喷薄彩虹正弦](pixel-bits-images/RainbowSine.png "出喷薄彩虹正弦值")](pixel-bits-images/RainbowSine-Large.png#lightbox)

## <a name="from-one-bitmap-to-another"></a>从到另一个位图

很多图像处理任务都涉及修改才会传输到另一个从一个位图的像素为单位。 此方法进行了演示**颜色调整**页。 页面加载一个位图资源，然后允许您修改使用三个映像`Slider`视图：

[![颜色调整](pixel-bits-images/ColorAdjustment.png "颜色调整")](pixel-bits-images/ColorAdjustment-Large.png#lightbox)

对于每个像素颜色，第一个`Slider`添加一个值从 0 到 360 之间对色调、 但然后使用取模运算符来保留介于 0 至 360 之间的结果，有效地转移沿系列的颜色 （如 UWP 的屏幕截图所示）。 第二个`Slider`使你可以选择介于 0.5 和 2 对饱和度和第三个应用之间的乘法因子`Slider`执行相同的操作的亮度，Android 屏幕截图中所示。

程序维护两个位图，名为的原始源位图`srcBitmap`和名为调整后的目标位图`dstBitmap`。 每次`Slider`移动，程序会计算中的所有新像素`dstBitmap`。 当然，用户将通过移动进行试验`Slider`视图速度非常快，因此你希望你可以管理的最佳性能。 这涉及到`GetPixels`源和目标位图的方法。

**颜色调整**页上不会控制源和目标位图的颜色格式。 相反，它包含有关略有不同的逻辑`SKColorType.Rgba8888`和`SKColorType.Bgra8888`格式。 源和目标可以是不同的格式，并且程序仍将起作用。

下面是除了重要程序`TransferPixels`传输像素的方法形成源到目标。 构造函数设置`dstBitmap`等于`srcBitmap`。 `PaintSurface`处理程序都会显示`dstBitmap`:

```csharp
public partial class ColorAdjustmentPage : ContentPage
{
    SKBitmap srcBitmap =
        BitmapExtensions.LoadBitmapResource(typeof(FillRectanglePage),
                                            "SkiaSharpFormsDemos.Media.Banana.jpg");
    SKBitmap dstBitmap;

    public ColorAdjustmentPage()
    {
        InitializeComponent();

        dstBitmap = new SKBitmap(srcBitmap.Width, srcBitmap.Height);
        OnSliderValueChanged(null, null);
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        float hueAdjust = (float)hueSlider.Value;
        hueLabel.Text = $"Hue Adjustment: {hueAdjust:F0}";

        float saturationAdjust = (float)Math.Pow(2, saturationSlider.Value);
        saturationLabel.Text = $"Saturation Adjustment: {saturationAdjust:F2}";

        float luminosityAdjust = (float)Math.Pow(2, luminositySlider.Value);
        luminosityLabel.Text = $"Luminosity Adjustment: {luminosityAdjust:F2}";

        TransferPixels(hueAdjust, saturationAdjust, luminosityAdjust);
        canvasView.InvalidateSurface();
    }
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        canvas.DrawBitmap(dstBitmap, info.Rect, BitmapStretch.Uniform);
    }
}
```

`ValueChanged`处理程序`Slider`视图计算调整值和调用`TransferPixels`。

整个`TransferPixels`方法标记为`unsafe`。 它通过获取这两个位图的像素位的字节指针开始，然后循环访问所有行和列。 从源位图，此方法获取每个像素的四个字节。 这些脚本可以在`Rgba8888`或`Bgra8888`顺序。 正在检查颜色类型允许`SKColor`要创建的值。 然后提取、 调整，并用于重新创建 HSL 组件`SKColor`值。 具体取决于是否目标位图`Rgba8888`或`Bgra8888`，目标 bitmp 中存储的字节：

```csharp
public partial class ColorAdjustmentPage : ContentPage
{
    ···
    unsafe void TransferPixels(float hueAdjust, float saturationAdjust, float luminosityAdjust)
    {
        byte* srcPtr = (byte*)srcBitmap.GetPixels().ToPointer();
        byte* dstPtr = (byte*)dstBitmap.GetPixels().ToPointer();

        int width = srcBitmap.Width;       // same for both bitmaps
        int height = srcBitmap.Height;

        SKColorType typeOrg = srcBitmap.ColorType;
        SKColorType typeAdj = dstBitmap.ColorType;

        for (int row = 0; row < height; row++)
        {
            for (int col = 0; col < width; col++)
            {
                // Get color from original bitmap
                byte byte1 = *srcPtr++;         // red or blue
                byte byte2 = *srcPtr++;         // green
                byte byte3 = *srcPtr++;         // blue or red
                byte byte4 = *srcPtr++;         // alpha

                SKColor color = new SKColor();

                if (typeOrg == SKColorType.Rgba8888)
                {
                    color = new SKColor(byte1, byte2, byte3, byte4);
                }
                else if (typeOrg == SKColorType.Bgra8888)
                {
                    color = new SKColor(byte3, byte2, byte1, byte4);
                }

                // Get HSL components
                color.ToHsl(out float hue, out float saturation, out float luminosity);

                // Adjust HSL components based on adjustments
                hue = (hue + hueAdjust) % 360;
                saturation = Math.Max(0, Math.Min(100, saturationAdjust * saturation));
                luminosity = Math.Max(0, Math.Min(100, luminosityAdjust * luminosity));

                // Recreate color from HSL components
                color = SKColor.FromHsl(hue, saturation, luminosity);

                // Store the bytes in the adjusted bitmap
                if (typeAdj == SKColorType.Rgba8888)
                {
                    *dstPtr++ = color.Red;
                    *dstPtr++ = color.Green;
                    *dstPtr++ = color.Blue;
                    *dstPtr++ = color.Alpha;
                }
                else if (typeAdj == SKColorType.Bgra8888)
                {
                    *dstPtr++ = color.Blue;
                    *dstPtr++ = color.Green;
                    *dstPtr++ = color.Red;
                    *dstPtr++ = color.Alpha;
                }
            }
        }
    }
    ···
}
```

很可能此方法的性能可以通过创建单独的源和目标位图的颜色类型的各种组合的方法更多改进，并且避免检查每个像素的类型。 另一个选项是有多个`for`循环的`col`变量根据颜色类型。

## <a name="posterization"></a>海报

涉及到访问像素位的另一个常见作业_海报_。 如果颜色编码以位图的像素为单位数量减少，使结果类似于使用有限的调色板的手绘海报。

**色调分离**页上的 monkey 映像之一执行此过程：

```csharp
public class PosterizePage : ContentPage
{
    SKBitmap bitmap =
        BitmapExtensions.LoadBitmapResource(typeof(FillRectanglePage),
                                            "SkiaSharpFormsDemos.Media.Banana.jpg");
    public PosterizePage()
    {
        Title = "Posterize";

        unsafe
        {
            uint* ptr = (uint*)bitmap.GetPixels().ToPointer();
            int pixelCount = bitmap.Width * bitmap.Height;

            for (int i = 0; i < pixelCount; i++)
            {
                *ptr++ &= 0xE0E0E0FF;
            }
        }

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        canvas.DrawBitmap(bitmap, info.Rect, BitmapStretch.Uniform;
    }
}
```

构造函数中的代码访问每个像素，执行按位与运算，值 0xE0E0E0FF，然后将结果存储在位图中返回。 值 0xE0E0E0FF 保留高 3 位的每个颜色组件，并将较低的 5 位设置为 0。 而不是 2<sup>24</sup>或 16777216 种颜色，位图都会减少到 2<sup>9</sup>或 512 颜色：

[![色调分离](pixel-bits-images/Posterize.png "色调分离")](pixel-bits-images/色调分离-Large.png#lightbox)

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
