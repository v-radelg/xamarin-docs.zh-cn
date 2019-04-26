---
title: 在 Xamarin.iOS 影像框架
description: 本文档介绍如何使用 iOS 11 影像框架在 Xamarin.iOS 中。 具体而言，它讨论了矩形检测和人脸检测。
ms.prod: xamarin
ms.assetid: 7273ED68-7B7D-4252-B3A0-02DB2E357A8C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 08/31/2017
ms.openlocfilehash: 291cbdb93cfb6ac123d740e98065ba877bb44da5
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61159643"
---
# <a name="vision-framework-in-xamarinios"></a>在 Xamarin.iOS 影像框架

影像框架添加新图像处理功能到 iOS 11 中，包括了许多：

- [矩形检测](#rectangles)
- [人脸检测](#faces)
- 机器学习图像分析 (中所述[CoreML](~/ios/platform/introduction-to-ios11/coreml.md))
- 条形码检测
- 图像对齐方式分析
- 文本检测
- 即将推出检测
- 对象检测和跟踪

![使用检测到的三个矩形拍照](vision-images/found-rectangles-tiny.png) ![拍摄与检测到的两张脸](vision-images/xamarin-home-faces-tiny.png)

下面更详细地讨论了矩形检测和人脸检测。

<a name="rectangles" />

## <a name="rectangle-detection"></a>矩形检测

[VisionRects 示例](https://developer.xamarin.com/samples/monotouch/ios11/VisionRectangles/)演示如何处理图像并在其上绘制检测到的矩形。

### <a name="1-initialize-the-vision-request"></a>1.初始化视觉请求

在中`ViewDidLoad`，创建`VNDetectRectanglesRequest`引用`HandleRectangles`将每个请求结束时调用的方法：

`MaximumObservations`还应设置属性，否则为它将默认为 1 并将返回单个结果。

```csharp
RectangleRequest = new VNDetectRectanglesRequest(HandleRectangles);
RectangleRequest.MaximumObservations = 10;
```

### <a name="2-start-the-vision-processing"></a>2.开始视觉处理

以下代码将开始处理请求。 在中**VisionRects**示例，此代码运行后用户所选映像：

```csharp
// Run the rectangle detector
var handler = new VNImageRequestHandler(ciImage, uiImage.Orientation.ToCGImagePropertyOrientation(), new VNImageOptions());
DispatchQueue.DefaultGlobalQueue.DispatchAsync(()=>{
  handler.Perform(new VNRequest[] {RectangleRequest}, out NSError error);
});
```

此处理程序将传递`ciImage`影像框架到`VNDetectRectanglesRequest`在步骤 1 中创建。

### <a name="3-handle-the-results-of-vision-processing"></a>3.处理视觉处理的结果

矩形检测完成后，框架将执行`HandleRectangles`方法，如下所示的摘要：

```csharp
private void HandleRectangles(VNRequest request, NSError error){
  var observations = request.GetResults<VNRectangleObservation>();
  // ... omitted error handling ...
  bool atLeastOneValid = false;
  foreach (var o in observations){
    if (InputImage.Extent.Contains(boundingBox)) {
      atLeastOneValid |= true;
    }
  }
  if (!atLeastOneValid) return;
  // Show the pre-processed image
  DispatchQueue.MainQueue.DispatchAsync(() =>
  {
    ClassificationLabel.Text = summary;
    ImageView.Image = OverlayRectangles(RawImage, imageSize, observations);
  });
}
```

### <a name="4-display-the-results"></a>4.显示结果

`OverlayRectangles`中的方法**VisionRectangles**示例具有三个函数：

- 呈现源映像
- 绘制一个矩形以指示每个检测到其中，和
- 添加使用 CoreGraphics 每个矩形的文本标签。

视图[示例的源](https://developer.xamarin.com/samples/monotouch/ios11/VisionRectangles/)确切 CoreGraphics 方法。

![使用检测到的三个矩形拍照](vision-images/found-rectangles-phone-sml.png)

### <a name="5-further-processing"></a>5.进一步的处理

矩形检测是通常只是第一步链中的操作，如使用[CoreMLVision 本例](~/ios/platform/introduction-to-ios11/coreml.md#coremlvision)，其中矩形传递给要分析手写的数字的 CoreML 模型。


<a name="faces" />

## <a name="face-detection"></a>人脸检测

[VisionFaces 示例](https://developer.xamarin.com/samples/monotouch/ios11/VisionFaces/)适用于以类似的方式**VisionRectangles**示例，使用不同的视觉请求类。

### <a name="1-initialize-the-vision-request"></a>1.初始化视觉请求

在中`ViewDidLoad`，创建`VNDetectFaceRectanglesRequest`引用`HandleRectangles`将每个请求结束时调用的方法。

```csharp
FaceRectangleRequest = new VNDetectFaceRectanglesRequest(HandleRectangles);
```

### <a name="2-start-the-vision-processing"></a>2.开始视觉处理

以下代码将开始处理请求。 在中**VisionFaces**此代码运行后用户所选映像的示例：

```csharp
// Run the face detector
var handler = new VNImageRequestHandler(ciImage, uiImage.Orientation.ToCGImagePropertyOrientation(), new VNImageOptions());
DispatchQueue.DefaultGlobalQueue.DispatchAsync(()=>{
  handler.Perform(new VNRequest[] {FaceRectangleRequest}, out NSError error);
});
```

此处理程序将传递`ciImage`影像框架到`VNDetectFaceRectanglesRequest`在步骤 1 中创建。

### <a name="3-handle-the-results-of-vision-processing"></a>3.处理视觉处理的结果

人脸检测完成后，在处理程序执行`HandleRectangles`方法进行错误处理，并显示检测到的人脸，并调用边界`OverlayRectangles`原始图片上绘制边框：

```csharp
private void HandleRectangles(VNRequest request, NSError error){
  var observations = request.GetResults<VNFaceObservation>();
  // ... omitted error handling...
  var summary = "";
  var imageSize = InputImage.Extent.Size;
  bool atLeastOneValid = false;
  Console.WriteLine("Faces:");
  summary += "Faces:";
  foreach (var o in observations) {
    // Verify detected rectangle is valid. omitted
    var boundingBox = o.BoundingBox.Scaled(imageSize);
    if (InputImage.Extent.Contains(boundingBox)) {
      atLeastOneValid |= true;
    }
  }
  // Show the pre-processed image (on UI thread)
  DispatchQueue.MainQueue.DispatchAsync(() =>
  {
    ClassificationLabel.Text = summary;
    ImageView.Image = OverlayRectangles(RawImage, imageSize, observations);
  });
}
```

### <a name="4-display-the-results"></a>4.显示结果

`OverlayRectangles`中的方法**VisionFaces**示例具有三个函数：

- 呈现源映像
- 绘制检测到，每张人脸矩形和
- 添加使用 CoreGraphics 每张人脸的文本标签。

视图[示例的源](https://developer.xamarin.com/samples/monotouch/ios11/VisionFaces/)确切 CoreGraphics 方法。

![拍摄与检测到的两张脸](vision-images/found-faces-phone-sml.png)

### <a name="5-further-processing"></a>5.进一步的处理

影像框架包括附加功能，用于检测面部识别功能，例如眼睛和嘴巴。 使用`VNDetectFaceLandmarksRequest`类型，它将返回`VNFaceObservation`结果，如上面，步骤 3 中所示，但与其他`VNFaceLandmark`数据。


## <a name="related-links"></a>相关链接

- [视觉矩形 （示例）](https://developer.xamarin.com/samples/monotouch/ios11/VisionRectangles/)
- [愿景的人脸 （示例）](https://developer.xamarin.com/samples/monotouch/ios11/VisionFaces/)
- [在核心映像-筛选器、 裸机、 视觉和多 (WWDC) （视频） 方面的改进](https://developer.apple.com/videos/play/wwdc2017/510/)
