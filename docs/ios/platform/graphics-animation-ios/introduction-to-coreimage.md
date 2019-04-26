---
title: 在 Xamarin.iOS core 映像
description: 核心映像是 ios 5，提供图像处理和实时视频的增强功能引入了一个新框架。 本文介绍了 Xamarin.iOS 示例使用这些功能。
ms.prod: xamarin
ms.assetid: 91E0780B-FF8A-E70D-9CD4-419119612B2D
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: d71f14c26865b71eca991910df4a68f2540f9715
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61085540"
---
# <a name="core-image-in-xamarinios"></a>在 Xamarin.iOS core 映像

_核心映像是 ios 5，提供图像处理和实时视频的增强功能引入了一个新框架。本文介绍了 Xamarin.iOS 示例使用这些功能。_

核心映像是在提供大量内置筛选器和要应用于图像和视频，包括人脸检测效果的 iOS 5 中引入的新框架。

本文档包含简单的示例：

-  人脸检测。
-  应用筛选器的图像
-  列出可用的筛选器。


这些示例应帮助你开始将核心映像功能合并到 Xamarin.iOS 应用程序。

## <a name="requirements"></a>要求

必须使用 Xcode 的最新版本。

## <a name="face-detection"></a>人脸检测

核心映像人脸检测功能的作用正如其名，它尝试识别照片中的人脸并返回其所识别的任何人脸的坐标。 此信息可用于在图像中的用户数目进行计数，（例如在图像上绘制指标。 标记人在照片），或您可以将任何其他内容。

从 CoreImage\SampleCode.cs 此代码演示如何创建和使用嵌入的图像上的人脸检测：

```csharp
var image = new UIImage("photoFace.JPG");
var context = CIContext.FromOptions(null);
var detector = CIDetector.CreateFaceDetector (context, true);
var ciImage = CIImage.FromCGImage(image.CGImage);
CIFeature[] features = detector.FeaturesInImage(ciImage);
```

将使用填充功能数组`CIFaceFeature`对象 （如果检测到任何人脸）。 没有`CIFaceFeature`每张人脸。 `CIFaceFeature` 具有以下属性：

-  HasMouthPosition – 味觉上是否检测到此张人脸。
-  HasLeftEyePosition – 是否为此人脸检测到左侧的关注。
-  HasRightEyePosition – 这张人脸是否检测到正确的关注。 
-  MouthPosition – 此人脸嘴巴的坐标。
-  LeftEyePosition – 此人脸左关注的坐标。
-  RightEyePosition – 此人脸右眼的坐标。


所有这些属性的坐标的左下角 – 与使用左上角的原点的 UIKit 不同具有及其来源位置。 在使用坐标时`CIFaceFeature`务必翻转它们。 在 CoreImage\CoreImageViewController.cs 此非常基本的自定义映像视图演示如何在图像上绘制人脸指示器三角形 (请注意`FlipForBottomOrigin`方法):

```csharp
public class FaceDetectImageView : UIView
{
    public Xamarin.iOS.CoreImage.CIFeature[] Features;
    public UIImage Image;
    public FaceDetectImageView (RectangleF rect) : base(rect) {}
    CGPath path;
    public override void Draw (RectangleF rect) {
        base.Draw (rect);
        if (Image != null)
            Image.Draw(rect);

        using (var context = UIGraphics.GetCurrentContext()) {
            context.SetLineWidth(4);
            UIColor.Red.SetStroke ();
            UIColor.Clear.SetFill ();
            if (Features != null) {
                foreach (var feature in Features) { // for each face
                    var facefeature = (CIFaceFeature)feature;
                    path = new CGPath ();
                    path.AddLines(new PointF[]{ // assumes all 3 features found
                        FlipForBottomOrigin(facefeature.LeftEyePosition, 200),
                        FlipForBottomOrigin(facefeature.RightEyePosition, 200),
                        FlipForBottomOrigin(facefeature.MouthPosition, 200)
                    });
                    path.CloseSubpath();
                    context.AddPath(path);
                    context.DrawPath(CGPathDrawingMode.FillStroke);
                }
            }
        }
    }
    /// <summary>
    /// Face recognition coordinates have their origin in the bottom-left
    /// but we are drawing with the origin in the top-left, so "flip" the point
    /// </summary>
    PointF FlipForBottomOrigin (PointF point, int height)
    {
        return new PointF(point.X, height - point.Y);
    }
}
```

然后 SampleCode.cs 文件中的图像和功能分配之前重绘图像：

```csharp
faceView.Image = image;
faceView.Features = features;
faceView.SetNeedsDisplay();
```

屏幕截图显示了示例输出： UITextView 中显示和绘制到源映像使用 CoreGraphics 上检测到的面部识别功能的位置。

由于面部识别它的工作的方式会偶尔检测 （如这些玩具放弃 ！） 的人脸除了的内容。

## <a name="filters"></a>筛选器

没有为 50 多个不同内置筛选器，并且该框架是可扩展的可以实现新的筛选器。

## <a name="using-filters"></a>使用筛选器

将筛选器应用于映像具有四个不同的步骤： 加载映像、 创建筛选器、 应用筛选器并保存 （或显示） 结果。

首先，加载图像成为`CIImage`对象。

```csharp
var uiimage = UIImage.FromFile ("photo.JPG");
var ciimage = new CIImage (uiimage);
```

其次，创建筛选器类，并设置其属性。

```csharp
var sepia = new CISepiaTone();
sepia.Image = ciimage;
sepia.Intensity = 0.8f;
```

第三，访问`OutputImage`属性并调用`CreateCGImage`方法以呈现最终结果。

```csharp
CIImage output = sepia.OutputImage;
var context = CIContext.FromOptions(null);
var cgimage = context.CreateCGImage (output, output.Extent);
```

最后，将图像分配为视图，以查看结果。 在实际应用程序中生成的映像可能会保存到文件系统、 相册、 推文或电子邮件。

```csharp
var ui = UIImage.FromImage (cgimage);
imgview.Image = ui;
```

这些屏幕截图显示的结果`CISepia`和`CIHueAdjust`CoreImage.zip 中演示的筛选器的示例代码。

请参阅[调整协定和映像方案的亮度](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/coreimage/adjust_contrast_and_brightness_of_an_image)有关的示例`CIColorControls`筛选器。

```csharp
var uiimage = UIImage.FromFile("photo.JPG");
var ciimage = new CIImage(uiimage);
var hueAdjust = new CIHueAdjust();   // first filter
hueAdjust.Image = ciimage;
hueAdjust.Angle = 2.094f;
var sepia = new CISepiaTone();       // second filter
sepia.Image = hueAdjust.OutputImage; // output from last filter, input to this one
sepia.Intensity = 0.3f;
CIFilter color = new CIColorControls() { // third filter
    Saturation = 2,
    Brightness = 1,
    Contrast = 3,
    Image = sepia.OutputImage    // output from last filter, input to this one
};
var output = color.OutputImage;
var context = CIContext.FromOptions(null);
// ONLY when CreateCGImage is called do all the effects get rendered
var cgimage = context.CreateCGImage (output, output.Extent);
var ui = UIImage.FromImage (cgimage);
imgview.Image = ui;
```

```csharp
var context = CIContext.FromOptions (null);
```

```csharp
var context = CIContext.FromOptions(new CIContextOptions() {
    UseSoftwareRenderer = true  // CPU
});
var cgimage = context.CreateCGImage (output, output.Extent);
var ui = UIImage.FromImage (cgimage);
imgview.Image = ui;
```

### <a name="listing-filters-and-their-properties"></a>列出筛选器和它们的属性

此代码从 CoreImage\SampleCode.cs 输出的内置筛选器的完整列表和其参数。

```csharp
var filters = CIFilter.FilterNamesInCategories(new string[0]);
foreach (var filter in filters){
   display.Text += filter +"\n";
   var f = CIFilter.FromName (filter);
   foreach (var key in f.InputKeys){
     var attributes = (NSDictionary)f.Attributes[new NSString(key)];
     var attributeClass = attributes[new NSString("CIAttributeClass")];
     display.Text += "   " + key;
     display.Text += "   " + attributeClass + "\n";
   }
}
```

[CIFilter 类引用](https://developer.apple.com/library/prerelease/ios/#documentation/GraphicsImaging/Reference/QuartzCoreFramework/Classes/CIFilter_Class/Reference/Reference.html)描述 50 的内置筛选器和其属性。 使用上面的代码可以查询的筛选器类，包括参数的默认值和最大和最小的允许值 （这可用于在应用筛选器前验证输入）。

列出类别输出在模拟器上看起来像这样 – 您可以滚动浏览列表以查看所有筛选器和其参数。

 [![](introduction-to-coreimage-images/coreimage05.png "列出类别输出在模拟器上如下所示")](introduction-to-coreimage-images/coreimage05.png#lightbox)

列出每个筛选器已公开为类在 Xamarin.iOS 中，因此您还可以浏览程序集浏览器中或使用 Visual Studio for Mac 或 Visual Studio 中的自动完成 Xamarin.iOS.CoreImage API。 

## <a name="summary"></a>总结

本文说明了如何使用某些人脸检测和应用的筛选器的图像的新 iOS 5 Core 映像 framework 功能。 有许多不同的映像筛选器供你使用 framework 中可用。

## <a name="related-links"></a>相关链接

- [Core 映像 （示例）](https://developer.xamarin.com/samples/CoreImage/)
- [调整协定和亮度映像组脚本](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/coreimage/adjust_contrast_and_brightness_of_an_image)
- [使用核心映像筛选器](https://developer.apple.com/library/prerelease/ios/#documentation/GraphicsImaging/Conceptual/CoreImaging/ci_tasks/ci_tasks.html)
- [CIFilter 类引用](https://developer.apple.com/library/prerelease/ios/#documentation/GraphicsImaging/Reference/QuartzCoreFramework/Classes/CIFilter_Class/Reference/Reference.htm)
