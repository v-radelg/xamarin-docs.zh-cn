---
title: Xamarin 中的 watchOS 图像控件
description: 本文档介绍如何在使用 Xamarin 生成的 watchOS 应用程序中使用图像控件。 它讨论了 WKInterfaceImage 控件、SetImage 方法、将图像添加到监视扩展、动画等。
ms.prod: xamarin
ms.assetid: B741C207-3427-46F3-9C90-A52BF8933FA4
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/17/2017
ms.openlocfilehash: f9367eda7651ca61a8a3cb0928ad11cb320faab6
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "70769961"
---
# <a name="watchos-image-controls-in-xamarin"></a>Xamarin 中的 watchOS 图像控件

watchOS 提供了一个用于显示图像和简单动画的[`WKInterfaceImage`](xref:WatchKit.WKInterfaceImage)控件。 某些控件还可以有背景图像（如按钮、组和界面控制器）。

![](image-images/image-walkway.png "显示图片的 Apple Watch") ![](image-images/image-animation.png "带有简单动画的 Apple Watch")
<!-- watch image courtesy of http://infinitapps.com/bezel/ -->

使用资产目录映像将图像添加到观看工具包应用。
仅 **@2x** 版本是必需的，因为所有监视设备都 Retina 显示。

![](image-images/asset-universal-sml.png "Only 2x versions are required, since all watch devices have Retina displays")

最好确保图像本身的大小适合于监视显示。 *避免*使用不正确大小的图像（尤其是大图像），并缩放以在手表上显示它们。

您可以使用资产目录图像中的手表套件大小（38mm 和42mm）来指定每个显示大小的不同图像。

![](image-images/asset-watch-sml.png "You can use the Watch Kit sizes 38mm and 42mm in an asset catalog image to specify different images for each display size")

## <a name="images-on-the-watch"></a>监视上的图像

显示图像最有效的方法是将*其包含在 "监视应用程序" 项目中*，并使用 `SetImage(string imageName)` 方法显示这些图像。

例如， [WatchKitCatalog](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog/)示例包含多个添加到 "监视应用程序" 项目中的资产目录的图像：

![](image-images/asset-whale-sml.png "The WatchKitCatalog sample has a number of images added to an asset catalog in the watch app project")

使用带有 string name 参数的 `SetImage` 可以有效地加载并显示这些内容：

```csharp
myImageControl.SetImage("Whale");
myOtherImageControl.SetImage("Worry");
```

### <a name="background-images"></a>背景图像

相同的逻辑适用于 `Button`、`Group` 和 `InterfaceController` 类的 `SetBackgroundImage (string imageName)`。 通过将图像存储在手表应用本身中来实现最佳性能。

## <a name="images-in-the-watch-extension"></a>监视扩展中的图像

除了加载存储在 "监视应用程序" 本身中的图像外，还可以将扩展捆绑中的映像发送到 "监视" 应用以显示（或者可以从远程位置下载图像并显示这些图像）。

若要从监视扩展加载图像，请创建 `UIImage` 实例，然后调用 `UIImage` 对象的 `SetImage`。

例如， [WatchKitCatalog](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)示例在 "监视扩展" 项目中有一个名为**Bumblebee**的映像：

![](image-images/asset-bumblebee-sml.png "The WatchKitCatalog sample has an image named Bumblebee in the watch extension project")

下面的代码将生成：

- 要加载到内存中的映像，以及
- 显示在监视上。

```csharp
using (var image = UIImage.FromBundle ("Bumblebee")) {
    myImageControl.SetImage (image);
}
```

## <a name="animations"></a>动画

若要对一组图像进行动画处理，它们应该以相同的前缀开头并且具有数字后缀。

[WatchKitCatalog](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)示例在具有**总线**前缀的 watch 应用项目中包含一系列已编号的图像：

![](image-images/asset-bus-animation-sml.png "The WatchKitCatalog sample has a series of numbered images in the watch app project with the Bus prefix")

若要以动画形式显示这些图像，请首先使用带有前缀名称的 `SetImage` 加载图像，然后调用 `StartAnimating`：

```csharp
animatedImage.SetImage ("Bus");
animatedImage.StartAnimating ();
```

对图像控件调用 `StopAnimating` 以停止动画循环：

```csharp
animatedImage.StopAnimating ();
```

<a name="cache" />

## <a name="appendix-caching-images-watchos-1"></a>附录：缓存图像（watchOS 1）

> [!IMPORTANT]
> watchOS 3 应用完全在设备上运行。 以下信息仅适用于 watchOS 1 应用。

如果应用程序重复使用存储在扩展中的映像（或已下载），则可以在监视的存储中缓存该映像，以提高后续显示的性能。

使用 `WKInterfaceDevice`s `AddCachedImage` 方法将图像传输到手表，然后将 `SetImage` 与 image name 参数一起使用，以便显示它：

```csharp
var device = WKInterfaceDevice.CurrentDevice;
using (var image = UIImage.FromBundle ("Bumblebee")) {
    if (!device.AddCachedImage (image, "Bumblebee")) {
            Console.WriteLine ("Image cache full.");
        } else {
            cachedImage.SetImage ("Bumblebee");
        }
    }
}
```

可以使用 `WKInterfaceDevice.CurrentDevice.WeakCachedImages` 在代码中查询图像缓存的内容。

### <a name="managing-the-cache"></a>管理缓存

大约 20 MB 的缓存大小。 它在应用程序重新启动时保持不变，并在填满后，你将负责使用 `RemoveCachedImage` 或 `RemoveAllCachedImages` 方法在 `WKInterfaceDevice.CurrentDevice` 对象上清除文件。

## <a name="related-links"></a>相关链接

- [WatchKitCatalog （示例）](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Apple 的图像文档](https://developer.apple.com/documentation/watchkit/wkinterfaceimage)
