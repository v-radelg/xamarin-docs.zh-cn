---
title: 在 Xamarin.iOS 中的核心图形
description: 本文将讨论的核心图形 iOS 框架。 它演示如何使用核心图形绘制几何图形、 图像和 Pdf。
ms.prod: xamarin
ms.assetid: 4A30F480-0723-4B8A-9049-7CEB6211304A
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: f3fe22e56a2c45524923a316ef28e54e5a3cc3f8
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61372380"
---
# <a name="core-graphics-in-xamarinios"></a>在 Xamarin.iOS 中的核心图形

_本文将讨论的核心图形 iOS 框架。它演示如何使用核心图形绘制几何图形、 图像和 Pdf。_

iOS 包括[*核心图形*](https://developer.apple.com/library/prerelease/ios/documentation/CoreGraphics/Reference/CoreGraphics_Framework/index.html)框架可以提供低级别图形支持。 这些框架是什么启用 UIKit 中丰富的图形功能。 

核心图形是允许绘制与设备无关的图形的低级别的 2D 图形框架。 所有 2D 绘图在 UIKit 中在内部都使用核心图形。

核心图形支持多种方案，包括绘制：

-  [绘制到屏幕中，通过`UIView` ](#Drawing_in_a_UIView_Subclass) 。
-  [在内存中或在屏幕上绘制图像](#Drawing_Images_and_Text)。
-  创建和绘制到 PDF。
-  读取和绘制现有 PDF。


## <a name="geometric-space"></a>几何空间

无论何种方案，通过核心图形的所有绘制都完成几何空间中，这意味着它适用于抽象点而不是像素为单位。 描述要绘制几何和绘制状态，如颜色、 线型等方面和核心图形句柄将所有内容转换为像素。 此类状态添加到您可以将像绘画器的画布的图形上下文。

有这种方法的几个优点：

-  绘制代码变得动态的并随后可以修改在运行时的图形。
-  减少应用程序捆绑包中的静态图像的需要，可以减少应用程序大小。
-  图形成为跨设备分辨率更改为更具弹性。

<a name="Drawing_in_a_UIView_Subclass"/>

## <a name="drawing-in-a-uiview-subclass"></a>UIView 子类中绘制

每个`UIView`具有`Draw`需要绘制时由系统调用的方法。 若要将绘制代码添加到视图，子类`UIView`并重写`Draw`:

```csharp
public class TriangleView : UIView
{
    public override void Draw (CGRect rect)
    {
        base.Draw (rect);
    }
}
```

描述应永远不会直接调用。 它是由系统调用，在运行的循环处理过程。 第一次循环运行后视图添加到视图层次结构，其`Draw`调用方法。 对后续调用`Draw`出现在视图标记为无需通过调用绘制`SetNeedsDisplay`或`SetNeedsDisplayInRect`在视图上。

### <a name="pattern-for-graphics-code"></a>图形代码模式

中的代码`Draw`实现应描述它想绘制。 绘制代码遵循它设置一些绘图状态并调用一个方法以请求它绘制模式。 可以按如下所示通用化这种模式：

1. 获取图形上下文。

2. 设置绘制特性。

3. 创建一些几何绘图基元。

4. 调用绘图或笔划方法。

### <a name="basic-drawing-example"></a>基本的绘图示例

例如，考虑下面的代码段：

```csharp
//get graphics context
using (CGContext g = UIGraphics.GetCurrentContext ()) {
            
    //set up drawing attributes
    g.SetLineWidth (10);
    UIColor.Blue.SetFill ();
    UIColor.Red.SetStroke ();

    //create geometry
    var path = new CGPath ();

    path.AddLines (new CGPoint[]{
    new CGPoint (100, 200),
    new CGPoint (160, 100), 
    new CGPoint (220, 200)});

    path.CloseSubpath ();

    //add geometry to graphics context and draw it
    g.AddPath (path);       
    g.DrawPath (CGPathDrawingMode.FillStroke);
}
```

让我们分解这段代码：

```csharp
using (CGContext g = UIGraphics.GetCurrentContext ()) {
...
}
```
用下面这行，它首先获取用于绘制的当前图形上下文。 可以将图形上下文作为画布的绘图上发生的情况，其中包含有关该绘图，如笔划和填充颜色，以及要绘制的几何图形的所有状态。

```csharp
g.SetLineWidth (10);
UIColor.Blue.SetFill ();
UIColor.Red.SetStroke ();
``` 

获取图形上下文后代码设置一些属性用于绘制，上面所示。 在这种情况下设置线条的宽度、 笔划和填充颜色。 中的图形上下文状态进行维护，因此，任何后续的绘图然后将使用这些属性。

若要创建几何图形的代码使用`CGPath`，它允许进行描述从直线和曲线的图形路径。 在这种情况下，路径将添加连接点，以组成一个三角形的数组的线条。 如下所示核心图形使用一个坐标系统，用于绘制的视图，其中源是在左上角，与正 x-直接发送到正确的和正 y 方向向下：

```csharp
var path = new CGPath ();

path.AddLines (new CGPoint[]{
new CGPoint (100, 200),
new CGPoint (160, 100), 
new CGPoint (220, 200)});

path.CloseSubpath ();
``` 

创建路径后，它添加到图形上下文，以便调用`AddPath`和`DrawPath`分别可以绘制它。

生成的视图如下所示：

 ![](core-graphics-images/00-bluetriangle.png "示例输出三角形")

## <a name="creating-gradient-fills"></a>创建渐变填充

此外，还提供更丰富的窗体的绘制。 例如，核心图形，创建渐变填充和应用剪切路径。 若要从前面的示例绘制路径内渐变填充，请首先路径需要设置剪切路径为：

```csharp
// add the path back to the graphics context so that it is the current path
g.AddPath (path);
// set the current path to be the clipping path
g.Clip ();
```

它可设置的当前路径，如剪切路径限制所有后续绘制的路径，如以下代码中，几何内绘制的线性渐变：

```csharp
// the color space determines how Core Graphics interprets color information
    using (CGColorSpace rgb = CGColorSpace.CreateDeviceRGB()) {
        CGGradient gradient = new CGGradient (rgb, new CGColor[] {
        UIColor.Blue.CGColor,
        UIColor.Yellow.CGColor
    });

// draw a linear gradient
    g.DrawLinearGradient (
        gradient, 
        new CGPoint (path.BoundingBox.Left, path.BoundingBox.Top), 
        new CGPoint (path.BoundingBox.Right, path.BoundingBox.Bottom), 
        CGGradientDrawingOptions.DrawsBeforeStartLocation);
    }
```

这些更改生成渐变填充，如下所示：

 ![](core-graphics-images/01-gradient-fill.png "使用渐变填充示例")

## <a name="modifying-line-patterns"></a>修改行模式

此外可以使用核心图形修改行的绘制特性。 这包括更改线条的宽度和笔划颜色，以及在行模式本身，如下面的代码中所示：

```csharp
//use a dashed line
g.SetLineDash (0, new nfloat[] { 10, 4 * (nfloat)Math.PI });
```

此前添加代码的任何绘图操作结果以虚线描边 10 单位长，具有 4 个短划线，之间的间距的单位，如下所示：

 ![](core-graphics-images/02-dashed-stroke.png "任何绘图操作结果之前将此代码添加在虚线描边")
 
请注意，在使用时 Unified API Xamarin.iOS，数组类型需要为`nfloat`，并且还需要显式强制转换为 Math.PI。

<a name="Drawing_Images_and_Text"/>

## <a name="drawing-images-and-text"></a>绘制图像和文本

除了在视图的图形上下文中绘制路径，核心图形还支持绘制的图像和文本。 若要绘制图像，只需创建`CGImage`并将其传递给`DrawImage`调用：

```csharp
public override void Draw (CGRect rect)
{
    base.Draw (rect);

    using(CGContext g = UIGraphics.GetCurrentContext ()){
        g.DrawImage (rect, UIImage.FromFile ("MyImage.png").CGImage);
    }
}
```

但是，这将产生图像绘制成上下颠倒，如下所示：

 ![](core-graphics-images/03-upside-down-monkey.png "绘制成上下颠倒图像")

这样做的原因是映像绘图的核心图形原点位于左下角，而视图左上角的原点。 因此，若要正确显示图像，原点需要进行修改，这可以通过修改来实现*当前变换矩阵* *(CTM)*。 CTM 定义点所在，也称为*用户空间*。 反转沿 y 方向 CTM 和边界的高度，以负 y 方向的转换可以翻转图像。

图形上下文具有用于转换 CTM 的帮助器方法。 在这种情况下， `ScaleCTM` "翻转"绘图和`TranslateCTM`会将它移动到左上方，如下所示：

```csharp
public override void Draw (CGRect rect)
{
    base.Draw (rect);
    
    using (CGContext g = UIGraphics.GetCurrentContext ()) {

        // scale and translate the CTM so the image appears upright
        g.ScaleCTM (1, -1);
        g.TranslateCTM (0, -Bounds.Height);
        g.DrawImage (rect, UIImage.FromFile ("MyImage.png").CGImage);
}   
```

然后显示生成的图像竖直的情况下：

 ![](core-graphics-images/04-upright-monkey.png "示例图像显示竖直")

> [!IMPORTANT]
> 对图形上下文更改适用于所有后续的绘图操作。 因此，CTM 转换时，它会影响任何其他绘图。 例如，如果你绘制 CTM 转换后的三角形，它显示正面朝下。

### <a name="adding-text-to-the-image"></a>将文本添加到映像

作为具有路径和图像，与核心图形绘制文本包括设置一些图形状态并调用某种方法来绘制相同的基本的模式。 对于文本，用于显示文本的方法是`ShowText`。 添加到图像绘制示例时，下面的代码绘制一些文本使用核心图形：

```csharp
public override void Draw (RectangleF rect)
{
    base.Draw (rect);
    
    // image drawing code omitted for brevity ...

    // translate the CTM by the font size so it displays on screen
    float fontSize = 35f;
    g.TranslateCTM (0, fontSize);

    // set general-purpose graphics state
    g.SetLineWidth (1.0f);
    g.SetStrokeColor (UIColor.Yellow.CGColor);
    g.SetFillColor (UIColor.Red.CGColor);
    g.SetShadow (new CGSize (5, 5), 0, UIColor.Blue.CGColor);

    // set text specific graphics state
    g.SetTextDrawingMode (CGTextDrawingMode.FillStroke);
    g.SelectFont ("Helvetica", fontSize, CGTextEncoding.MacRoman);

    // show the text
    g.ShowText ("Hello Core Graphics");
}
```

正如您所看到的正在绘制的文本的图形状态设置为类似于绘制几何图形。 但是绘制文本，以及应用模式和字体绘制的文本。 在这种情况下，还应用阴影，但应用阴影的工作方式对于绘制的路径。

生成的文本将显示与映像，如下所示：

 ![](core-graphics-images/05-text-on-image.png "生成的文本显示与映像")

## <a name="memory-backed-images"></a>内存支持图像

除了绘制到视图的图形上下文，核心图形支持绘制内存备份映像，也称为绘制到屏幕之外。 执行此操作需要：

-  创建图形上下文由内存中的位图
-  设置绘图状态并发出的绘图命令
-  从上下文获取图像
-  删除上下文


与不同`Draw`方法，其中上下文提供的视图，在这种情况下在创建上下文中有两种：

1. 通过调用`UIGraphics.BeginImageContext`(或`BeginImageContextWithOptions`)

2. 通过创建一个新 `CGBitmapContextInstance`

 `CGBitmapContextInstance` 你正在直接使用的图像位，如的情况下，需要使用自定义映像操作算法时很有用。 在所有其他情况下，应使用`BeginImageContext`或`BeginImageContextWithOptions`。

图像上下文后，添加绘制代码就像它是在一样`UIView`子类。 例如，使用前面用来绘制三角形的代码示例而不是在内存中的图像绘制`UIView`，如下所示：

```csharp
UIImage DrawTriangle ()
{
    UIImage triangleImage;

    //push a memory backed bitmap context on the context stack
    UIGraphics.BeginImageContext (new CGSize (200.0f, 200.0f));

    //get graphics context
    using(CGContext g = UIGraphics.GetCurrentContext ()){

        //set up drawing attributes
        g.SetLineWidth(4);
        UIColor.Purple.SetFill ();
        UIColor.Black.SetStroke ();

        //create geometry
        path = new CGPath ();

        path.AddLines(new CGPoint[]{
            new CGPoint(100,200),
            new CGPoint(160,100), 
            new CGPoint(220,200)});

        path.CloseSubpath();

        //add geometry to graphics context and draw it
        g.AddPath(path);
        g.DrawPath(CGPathDrawingMode.FillStroke);

        //get a UIImage from the context
        triangleImage = UIGraphics.GetImageFromCurrentImageContext ();
    }
    
    return triangleImage;
}
```

对支持内存的位图绘制的一个常见用途是从任何捕获映像`UIView`。 例如，下面的代码呈现为位图上下文视图的层，并创建`UIImage`从它：

```csharp
UIGraphics.BeginImageContext (cellView.Frame.Size);

//render the view's layer in the current context
anyView.Layer.RenderInContext (UIGraphics.GetCurrentContext ());

//get a UIImage from the context
UIImage anyViewImage = UIGraphics.GetImageFromCurrentImageContext ();
UIGraphics.EndImageContext ();
```

## <a name="drawing-pdfs"></a>绘制 Pdf

除了映像，核心图形支持 PDF 绘图。 如映像，您可以呈现在内存中的 PDF，以及读取以呈现 PDF `UIView`。

### <a name="pdf-in-a-uiview"></a>UIView 中的 PDF

核心图形还支持从文件读取 PDF 和呈现在视图中使用`CGPDFDocument`类。 `CGPDFDocument`类表示在代码中，PDF，可用于读取和绘制页面。

例如，以下代码中`UIView`子类从到的文件读取 PDF `CGPDFDocument`:

```csharp
public class PDFView : UIView
{
    CGPDFDocument pdfDoc;

    public PDFView ()
    {
        //create a CGPDFDocument from file.pdf included in the main bundle
        pdfDoc = CGPDFDocument.FromFile ("file.pdf");
    }
  
     public override void Draw (Rectangle rect)
    {
        ...
    }
}
```

`Draw`方法可以然后使用`CGPDFDocument`读取到一个页面`CGPDFPage`，并使其通过调用`DrawPDFPage`，如下所示：

```csharp
public override void Draw (CGRect rect)
{
    base.Draw (rect);
        
    //flip the CTM so the PDF will be drawn upright
    using (CGContext g = UIGraphics.GetCurrentContext ()) {
        g.TranslateCTM (0, Bounds.Height);
        g.ScaleCTM (1, -1);
        
        // render the first page of the PDF
        using (CGPDFPage pdfPage = pdfDoc.GetPage (1)) {
            
        //get the affine transform that defines where the PDF is drawn
        CGAffineTransform t = pdfPage.GetDrawingTransform (CGPDFBox.Crop, rect, 0, true);
        
        //concatenate the pdf transform with the CTM for display in the view
        g.ConcatCTM (t);
        
        //draw the pdf page
        g.DrawPDFPage (pdfPage);
        }
    }
}
```

### <a name="memory-backed-pdf"></a>内存支持 PDF

对于内存中 PDF，需要通过调用创建 PDF 上下文`BeginPDFContext`。 绘制到 PDF 是精确到页。 每一页开始通过调用`BeginPDFPage`并通过调用完成`EndPDFContent`，使用图形的代码之间。 此外，如与图像绘制内存支持 PDF 绘制使用左下方，可以考虑通过只修改 CTM origin 喜欢个映像。

下面的代码演示如何将文本绘制到 PDF:

```csharp
//data buffer to hold the PDF
NSMutableData data = new NSMutableData ();

//create a PDF with empty rectangle, which will configure it for 8.5x11 inches
UIGraphics.BeginPDFContext (data, CGRect.Empty, null);

//start a PDF page
UIGraphics.BeginPDFPage ();
       
using (CGContext g = UIGraphics.GetCurrentContext ()) {
    g.ScaleCTM (1, -1);
    g.TranslateCTM (0, -25);      
    g.SelectFont ("Helvetica", 25, CGTextEncoding.MacRoman);
    g.ShowText ("Hello Core Graphics");
    }
    
//complete a PDF page
UIGraphics.EndPDFContent ();
```

生成的文本绘制到 pdf 文件，然后包含在`NSData`可以进行保存上, 传、 通过电子邮件发送，等等。


## <a name="summary"></a>总结

在本文中我们介绍了通过所提供的图形功能*核心图形*框架。 我们已了解如何使用核心图形的上下文中绘制几何图形、 图像和 Pdf`UIView,`并与支持内存的图形上下文。

## <a name="related-links"></a>相关链接

- [核心图形示例](https://developer.xamarin.com/samples/monotouch/GraphicsAndAnimation/)
- [图形和动画演练](~/ios/platform/graphics-animation-ios/graphics-animation-walkthrough.md)
- [核心动画](~/ios/platform/graphics-animation-ios/core-animation.md)
- [核心动画方案](https://github.com/xamarin/recipes/tree/master/Recipes/ios/animation/coreanimation)
