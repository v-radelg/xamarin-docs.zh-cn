---
title: 在 Xamarin.iOS CoreML 简介
description: 本文档介绍 CoreML，在 iOS 进行机器学习。 本文档介绍了如何开始使用 CoreML 和如何使用影像框架。
ms.prod: xamarin
ms.assetid: BE1E2CA1-E3AE-4C90-914C-CFDBD1DCB82B
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 08/30/2017
ms.openlocfilehash: 3a00a7256cace9cbcff3478d866646d48cfdc50b
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61385037"
---
# <a name="introduction-to-coreml-in-xamarinios"></a>在 Xamarin.iOS CoreML 简介

CoreML 带来了机器学习来 iOS – 应用程序可以充分利用定型的机器学习模型来执行各种类型的任务，从图像识别到解决问题。

本简介涵盖以下产品：

- [开始使用 CoreML](#coreml)
- [CoreML 使用影像框架](#coremlvision)

<a name="coreml" />

## <a name="getting-started-with-coreml"></a>开始使用 CoreML

以下步骤介绍如何将 CoreML 添加到 iOS 项目。 请参阅[Mars Habitat Pricer 示例](https://developer.xamarin.com/samples/monotouch/ios11/CoreML/)有关实际的示例。

![Mars Habitat 预测器示例屏幕截图](coreml-images/marspricer-heading.png)

### <a name="1-add-the-coreml-model-to-the-project"></a>1.添加到项目的 CoreML 模型

添加 CoreML 模型 (具有的文件 **.mlmodel**扩展) 到**资源**项目的目录。 

在模型文件的属性中，其**生成操作**设置为**CoreMLModel**。 这意味着将编译到 **.mlmodelc**文件时生成应用程序。

### <a name="2-load-the-model"></a>2.加载模型

加载时，模型使用`MLModel.Create`静态方法：

```csharp
var assetPath = NSBundle.MainBundle.GetUrlForResource("NameOfModel", "mlmodelc");
model = MLModel.Create(assetPath, out NSError error1);
```

### <a name="3-set-the-parameters"></a>3.设置参数

使用容器实现的类，传递模型参数`IMLFeatureProvider`。

功能提供程序类的行为类似于字典的字符串和`MLFeatureValue`s，其中每个功能值可能是一个简单的字符串或数字、 数组或数据或包含图像的像素缓冲区。

对于单值功能提供程序的代码如下所示：

```csharp
public class MyInput : NSObject, IMLFeatureProvider
{
  public double MyParam { get; set; }
  public NSSet<NSString> FeatureNames => new NSSet<NSString>(new NSString("myParam"));
  public MLFeatureValue GetFeatureValue(string featureName)
  {
    if (featureName == "myParam")
      return MLFeatureValue.FromDouble(MyParam);
    return MLFeatureValue.FromDouble(0); // default value
  }
```

使用此类的类，可以理解的 CoreML 的方式提供输入的参数。 功能名称 (如`myParam`代码示例中) 必须与该模型的预期匹配。

### <a name="4-run-the-model"></a>4.运行模型

使用模型要求的功能提供程序要实例化和参数集，然后，`GetPrediction`调用方法：

```csharp
var input = new MyInput {MyParam = 13};
var outFeatures = model.GetPrediction(inputFeatures, out NSError error2);
```

### <a name="5-extract-the-results"></a>5.提取结果

预测结果`outFeatures`也是实例`IMLFeatureProvider`; 输出值可以使用访问`GetFeatureValue`与每个输出参数的名称 (如`theResult`)，如下例所示：

```csharp
var result = outFeatures.GetFeatureValue("theResult").DoubleValue; // eg. 6227020800
```

<a name="coremlvision" />

## <a name="using-coreml-with-the-vision-framework"></a>CoreML 使用影像框架

CoreML 还可与影像框架结合使用映像，如形状识别、 对象标识和其他任务上执行操作。

下面的步骤介绍如何 CoreML 和视觉中一起使用[CoreMLVision 示例](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLVision/)。 该示例将合并[矩形识别](~/ios/platform/introduction-to-ios11/vision.md#rectangles)从使用影像框架_MNINSTClassifier_ CoreML 模型来识别照片中的手写的数字。

![图像识别的数字 3](coreml-images/vision3.png) ![图像识别的数字 5](coreml-images/vision5.png)

### <a name="1-create-a-vision-coreml-model"></a>1.创建远景 CoreML 模型

CoreML 模型_MNISTClassifier_加载并在稍后被包装在`VNCoreMLModel`这使模型可用于构想任务。 此代码还创建两个视觉请求： 第一次用于显示图像，查找矩形，然后处理 CoreML 模型的矩形：

```csharp
// Load the ML model
var bundle = NSBundle.MainBundle;
var assetPath = bundle.GetUrlForResource("MNISTClassifier", "mlmodelc");
NSError mlErr, vnErr;
var mlModel = MLModel.Create(assetPath, out mlErr);
var model = VNCoreMLModel.FromMLModel(mlModel, out vnErr);

// Initialize Vision requests
RectangleRequest = new VNDetectRectanglesRequest(HandleRectangles);
ClassificationRequest = new VNCoreMLRequest(model, HandleClassification);
```

类仍需要实现`HandleRectangles`和`HandleClassification`在步骤 3 和 4 下面所示的视觉请求的方法。

### <a name="2-start-the-vision-processing"></a>2.开始视觉处理

以下代码将开始处理请求。 在中**CoreMLVision**示例，此代码运行后用户所选映像：

```csharp
// Run the rectangle detector, which upon completion runs the ML classifier.
var handler = new VNImageRequestHandler(ciImage, uiImage.Orientation.ToCGImagePropertyOrientation(), new VNImageOptions());
DispatchQueue.DefaultGlobalQueue.DispatchAsync(()=>{
  handler.Perform(new VNRequest[] {RectangleRequest}, out NSError error);
});
```

此处理程序将传递`ciImage`影像框架到`VNDetectRectanglesRequest`在步骤 1 中创建。

### <a name="3-handle-the-results-of-vision-processing"></a>3.处理视觉处理的结果

矩形检测完成后，它将执行`HandleRectangles`方法，裁剪该图像中提取的第一个矩形，该方法将矩形图像转换为灰度，并将其传递给分类的 CoreML 模型。

`request`参数传递给此方法包含视觉请求的详细信息和使用`GetResults<VNRectangleObservation>()`方法，它将返回图像中找到的矩形的列表。 第一个矩形`observations[0]`是提取并传递给 CoreML 模型：

```csharp
void HandleRectangles(VNRequest request, NSError error) {
  var observations = request.GetResults<VNRectangleObservation>();
  // ... omitted error handling ...
  var detectedRectangle = observations[0]; // first rectangle
  // ... omitted cropping and greyscale conversion ...
  // Run the Core ML MNIST classifier -- results in handleClassification method
  var handler = new VNImageRequestHandler(correctedImage, new VNImageOptions());
  DispatchQueue.DefaultGlobalQueue.DispatchAsync(() => {
    handler.Perform(new VNRequest[] {ClassificationRequest}, out NSError err);
  });
}
```

`ClassificationRequest`已在步骤 1 以使用初始化`HandleClassification`下一步中定义的方法。

### <a name="4-handle-the-coreml"></a>4.句柄 CoreML

`request`参数传递给此方法包含 CoreML 请求的详细信息和使用`GetResults<VNClassificationObservation>()`方法，它将返回一系列可能的结果按置信度排序 (最高置信度第一个):

```csharp
void HandleClassification(VNRequest request, NSError error){
  var observations = request.GetResults<VNClassificationObservation>();
  // ... omitted error handling ...
  var best = observations[0]; // first/best classification result
  // render in UI
  DispatchQueue.MainQueue.DispatchAsync(()=>{
    ClassificationLabel.Text = $"Classification: {best.Identifier} Confidence: {best.Confidence * 100f:#.00}%";
  });
}
```

## <a name="samples"></a>示例

有三个 CoreML 示例，请尝试：

* [Mars Habitat 预测器示例](https://developer.xamarin.com/samples/monotouch/ios11/CoreML/)具有简单的数值输入和输出。

* [构想和 CoreML 示例](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLVision/)接受映像参数，并使用影像框架来识别在图中，传递给可识别的一位数字的 CoreML 模型的方形区域。

* 最后， [CoreML 图像识别示例](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLImageRecognition/)使用 CoreML 来识别照片中的功能。 默认情况下，它使用较小**SqueezeNet**模型 (5 MB)，但是它写入的以便您可以下载并包含较大**VGG16**模型 (553 MB)。 有关详细信息，请参阅[示例的自述文件](https://github.com/xamarin/ios-samples/blob/master/ios11/CoreMLImageRecognition/CoreMLImageRecognition/README.md)。

## <a name="related-links"></a>相关链接

- [机器学习 (Apple)](https://developer.apple.com/machine-learning/)
- [CoreML 示例 (Mars Habitat) （示例）](https://developer.xamarin.com/samples/monotouch/ios11/CoreML/)
- [CoreML 和视觉 （数字识别） （示例）](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLVision/)
- [CoreML 图像识别 （示例）](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLImageRecognition/)
- [CoreML，使用 Azure 自定义视觉 （示例）](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLAzureModel)
- [引入 CoreML (WWDC) （视频）](https://developer.apple.com/videos/play/wwdc2017/703/)
