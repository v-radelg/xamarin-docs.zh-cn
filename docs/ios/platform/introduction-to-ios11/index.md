---
title: iOS 11 简介
description: 本文档所链接到各种指南描述的 iOS 11 中，包括 ARKit 和 CoreML，MapKit、 PDFKit、 SiriKit，影像框架中，等功能。
ms.prod: xamarin
ms.assetid: 22C38EA6-6DA9-4B92-B41B-814E589033F6
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/19/2017
ms.openlocfilehash: 5e75a7872081d82c289db0312bbc7d84bce77b4e
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61170983"
---
# <a name="introduction-to-ios-11"></a>iOS 11 简介

_请尝试新的 iOS 11 Api 与 Xamarin。_

> [!NOTE]
> 在开始之前，请查看[入门](get-started.md)页说明如何安装 Xcode 9。

![ARKit 示例](images/arkit.png) ![AR 放置对象](images/arkit2.png) ![CoreML 示例](images/coreml.png) ![MapKit 示例](images/mapkit.png) ![影像矩形示例](images/vision1.png) ![影像的人脸示例](images/vision2.png) ![拖放到示例](images/drag-drop.png) ![拖放到示例](images/drag-drop2.png) ![SiriKit 示例](images/sirikit.png)

iOS 11 跨多个框架包含许多全新的功能和增强功能：

## <a name="preparing-your-app-for-ios-11updating-your-appindexmd"></a>[适用于 iOS 11 准备您的应用程序](updating-your-app/index.md)

Apple 引入了体系结构更新、 新的可视更改和 iOS 11 更新的 iTunes Connect 过程。 使用本指南以确保你的 Xamarin.iOS 应用程序已准备好进行新的版本。

## <a name="arkitarkitindexmd"></a>[ARKit](arkit/index.md)

ARKit 赋予增强现实 iOS，允许用户通过设备的摄像机与世界进行交互。
借助 Xamarin，您还可以使用[ARKit 使用 UrhoSharp](arkit/urhosharp.md)。

## <a name="coremlcoremlmd"></a>[CoreML](coreml.md)

机器学习模型可以集成到使用 CoreML iOS 11 应用程序。 CoreML framework 提供了简单的 API 来将现有模型合并到应用程序项目以允许要分析的问题直接在应用程序中的。

## <a name="corenfccorenfcmd"></a>[CoreNFC](corenfc.md)

iPhone 7 和更高版本的设备可以读取近场通信 (NFC) 标记，因而应用程序可以检测标记的产品、 位置或在其周围世界物流的。

## <a name="drag-and-dropdrag-and-dropmd"></a>[拖放](drag-and-drop.md)

拖放框架提供了 iOS 范围内支持通过触摸移动数据。 在 iPad 上您可以将内部和之间不同的应用程序;在 iPhone 上，您可以拖动仅在同一应用程序中。 没有为许多类型的自定义，包括丰富的数据类型、 动画和处理多点触控手势支持。

## <a name="mapkitmapkitmd"></a>[MapKit](mapkit.md)

MapKit 具有大量改进，包括对自动标记分组和向视图添加指南针的支持。

## <a name="pdfkit"></a>PDFKit

PDFKit 是现在适用于 iOS 11 中，将带 PDF 创建和编辑您的应用程序的功能。

## <a name="sirikitsirikitmd"></a>[SiriKit](sirikit.md)

使用 Siri 现在支持更多的交互，包括列表和说明和其他增强功能，例如备用应用名称。

## <a name="visionvisionmd"></a>[愿景](vision.md)

为 iOS、 包括人脸检测和识别、 CoreML 模型、 新条形码检测 Api、 文本和即将推出检测，以及更多常规对象检测和跟踪提供了各种图像处理和分析功能。

## <a name="samples"></a>示例

我们有大量的 C#[示例](https://developer.xamarin.com/samples/ios/iOS11/)为了便于您开始：

* [ARKit 示例](https://developer.xamarin.com/samples/monotouch/ios11/ARKitSample/)
* [ARKit 放置对象](https://developer.xamarin.com/samples/monotouch/ios11/ARKitPlacingObjects/)
* [ARKit 和 UrhoSharp](arkit/urhosharp.md)
* [CoreML 示例](https://developer.xamarin.com/samples/monotouch/ios11/CoreML)
* [CoreML 图像识别示例](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLImageRecognition)
* [使用 Azure 自定义模型的 CoreML](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLAzureModel)
* [CoreNFC 标记读取器示例](https://developer.xamarin.com/samples/monotouch/ios11/NFCTagReader/)
* [拖放表视图](https://developer.xamarin.com/samples/monotouch/ios11/DragAndDropTableView)
* [拖放集合视图](https://developer.xamarin.com/samples/monotouch/ios11/DragAndDropCollectionView)
* [拖放自定义视图](https://developer.xamarin.com/samples/monotouch/ios11/DragAndDropCustomView)
* [DragBoard 拖放示例](https://developer.xamarin.com/samples/monotouch/ios11/DragAndDropDragBoard)
* [MapKit 示例](https://developer.xamarin.com/samples/monotouch/ios11/MapKitSample)
* [SiriKit 示例](https://developer.xamarin.com/samples/monotouch/ios11/SiriKitSample/)
* [已更新的照片 framework 示例](https://developer.xamarin.com/samples/monotouch/ios11/SamplePhotoApp/)
* [构想和 CoreML 示例](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLVision)
* [视觉矩形检测示例](https://developer.xamarin.com/samples/monotouch/ios11/VisionRects)
* [愿景的人脸检测示例](https://developer.xamarin.com/samples/monotouch/ios11/VisionFaces)
* [PDKFit 小组件示例](https://developer.xamarin.com/samples/monotouch/ios11/PDFAnnotationWidgetsAdvanced)
* [PDFKit 水印示例](https://developer.xamarin.com/samples/monotouch/ios11/PDFDocumentWatermark)

## <a name="more-information"></a>详细信息

IOS 11 的详细信息，请访问 Apple [What's New iOS 11 中](https://developer.apple.com/ios/)文档。


## <a name="related-links"></a>相关链接

- [What's New iOS 11 (Apple) 中](https://developer.apple.com/ios/)
- [Xamarin iOS 11 示例](https://developer.xamarin.com/samples/ios/iOS11/)
