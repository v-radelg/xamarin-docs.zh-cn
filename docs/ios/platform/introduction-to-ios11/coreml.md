---
title: Xamarin 中的 CoreML 简介
description: 本文档介绍 CoreML, 它在 iOS 上启用机器学习。 本文档讨论了如何开始使用 CoreML, 以及如何将其与远景框架一起使用。
ms.prod: xamarin
ms.assetid: BE1E2CA1-E3AE-4C90-914C-CFDBD1DCB82B
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 08/30/2017
ms.openlocfilehash: c2092cd9e7beb233c9478869ebff91d85b5b30c0
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68649608"
---
# <a name="introduction-to-coreml-in-xamarinios"></a>Xamarin 中的 CoreML 简介

CoreML 将机器学习带入 iOS –应用可以利用训练有素的机器学习模型来执行各种任务, 从问题解决到图像识别。

本简介涵盖以下内容:

- [入门与 CoreML](#coreml)
- [结合使用 CoreML 和远景框架](#coremlvision)

<a name="coreml" />

## <a name="getting-started-with-coreml"></a>入门与 CoreML

以下步骤介绍如何将 CoreML 添加到 iOS 项目。 有关实际示例, 请参阅[Mars Habitat Pricer 示例](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-marshabitatcoremltimer/)。

![Mars Habitat 价格预测器示例屏幕快照](coreml-images/marspricer-heading.png)

### <a name="1-add-the-coreml-model-to-the-project"></a>1.将 CoreML 模型添加到项目

将 CoreML 模型 (扩展名为**mlmodel**的文件) 添加到项目的**资源**目录。 

在模型文件的属性中, 其**生成操作**设置为**CoreMLModel**。 这意味着在构建应用程序时, 它将编译到**mlmodelc**文件中。

### <a name="2-load-the-model"></a>2.加载模型

使用`MLModel.Create`静态方法加载模型:

```csharp
var assetPath = NSBundle.MainBundle.GetUrlForResource("NameOfModel", "mlmodelc");
model = MLModel.Create(assetPath, out NSError error1);
```

### <a name="3-set-the-parameters"></a>3.设置参数

使用实现`IMLFeatureProvider`的容器类传入和传出模型参数。

功能提供程序类的行为类似于字符串和`MLFeatureValue`s 的字典, 其中每个功能值可以是简单的字符串或数字、数组或数据或包含图像的像素缓冲区。

单值功能提供程序的代码如下所示:

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

使用这样的类, 可以通过 CoreML 理解的方式提供输入参数。 功能的名称 (如`myParam`代码示例中所示) 必须与模型所需的名称相匹配。

### <a name="4-run-the-model"></a>4.运行模型

使用模型需要实例化功能提供程序和参数集, 然后`GetPrediction`调用方法:

```csharp
var input = new MyInput {MyParam = 13};
var outFeatures = model.GetPrediction(inputFeatures, out NSError error2);
```

### <a name="5-extract-the-results"></a>5.提取结果

预测结果`outFeatures`也是`IMLFeatureProvider`实例; 可以通过每个输出参数的`theResult`名称 ( `GetFeatureValue`例如) 来访问输出值, 如下例所示:

```csharp
var result = outFeatures.GetFeatureValue("theResult").DoubleValue; // eg. 6227020800
```

<a name="coremlvision" />

## <a name="using-coreml-with-the-vision-framework"></a>结合使用 CoreML 和远景框架

CoreML 还可以与远景框架结合使用来对图像执行操作, 如形状识别、对象标识和其他任务。

以下步骤说明了如何在[CoreMLVision 示例](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-coremlvision)中结合使用 CoreML 和视觉。 该示例将远景[识别](~/ios/platform/introduction-to-ios11/vision.md#rectangles)与_MNINSTClassifier_ CoreML 模型组合在一起, 以在照片中标识手写数字。

![数字3的图像识别](coreml-images/vision3.png) ![数字5的图像识别](coreml-images/vision5.png)

### <a name="1-create-a-vision-coreml-model"></a>1.创建远景 CoreML 模型

加载 CoreML 模型_MNISTClassifier_ , 并将`VNCoreMLModel`其包装在中, 使模型可用于视觉任务。 此代码还会创建两个远景请求: 首先查找图像中的矩形, 然后使用 CoreML 模型处理矩形:

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

类仍需要为视觉请求实现`HandleRectangles`和`HandleClassification`方法, 如下面的步骤3和4中所示。

### <a name="2-start-the-vision-processing"></a>2.开始视觉处理

下面的代码开始处理请求。 在**CoreMLVision**示例中, 此代码在用户选择了图像后运行:

```csharp
// Run the rectangle detector, which upon completion runs the ML classifier.
var handler = new VNImageRequestHandler(ciImage, uiImage.Orientation.ToCGImagePropertyOrientation(), new VNImageOptions());
DispatchQueue.DefaultGlobalQueue.DispatchAsync(()=>{
  handler.Perform(new VNRequest[] {RectangleRequest}, out NSError error);
});
```

此处理程序将`ciImage`传递到在步骤`VNDetectRectanglesRequest` 1 中创建的远景框架。

### <a name="3-handle-the-results-of-vision-processing"></a>3.处理视觉处理的结果

完成矩形检测后, 它将执行`HandleRectangles`方法, 该方法将裁剪图像以提取第一个矩形, 将该矩形图像转换为灰度, 并将其传递给 CoreML 模型进行分类。

传递给此方法的`GetResults<VNRectangleObservation>()` 参数包含远景请求的详细信息,并使用方法返回图像中找到的矩形列表。`request` 提取第一个`observations[0]`矩形, 并将其传递给 CoreML 模型:

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

在步骤1中初始化, `HandleClassification`使用下一步中定义的方法。 `ClassificationRequest`

### <a name="4-handle-the-coreml"></a>4.处理 CoreML

传递给此方法的`GetResults<VNClassificationObservation>()` 参数包含CoreML请求的详细信息,并使用方法,它返回由置信度排序的可能结果的列表(首先是最高置信度):`request`

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

有三个 CoreML 示例可供尝试:

* [Mars Habitat 价格预测器示例](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-marshabitatcoremltimer/)具有简单的数值输入和输出。

* [远景 & CoreML 示例](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-coremlvision)接受 image 参数, 并使用远景框架标识图像中的正方形区域, 这些区域被传递到识别单个数字的 CoreML 模型。

* 最后, [CoreML 图像识别示例](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-coremlimagerecognition)使用 CoreML 标识照片中的功能。 默认情况下, 它使用较小的**SqueezeNet**模型 (5mb), 但已编写它, 以便您可以下载和合并更大的**VGG16**模型 (553MB)。 有关详细信息, 请参阅[示例的自述文件](https://github.com/xamarin/ios-samples/blob/master/ios11/CoreMLImageRecognition/CoreMLImageRecognition/README.md)。

## <a name="related-links"></a>相关链接

- [机器学习 (Apple)](https://developer.apple.com/machine-learning/)
- [CoreML 示例 (Mars Habitat) (示例)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-marshabitatcoremltimer/)
- [CoreML 和远景 (数字识别) (示例)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-coremlvision)
- [CoreML 图像识别 (示例)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-coremlimagerecognition)
- [与 Azure 自定义视觉 CoreML (示例)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-coremlazuremodel)
- [引入 CoreML (WWDC) (视频)](https://developer.apple.com/videos/play/wwdc2017/703/)
