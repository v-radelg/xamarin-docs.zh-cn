---
title: watchOS 在 Xamarin 中的图像控件
description: 本文档介绍如何使用 Xamarin 生成的 watchOS 应用中使用图像控件。 它讨论了 WKInterfaceImage 控件，SetImage 方法，将映像添加到监视扩展、 动画和的详细信息。
ms.prod: xamarin
ms.assetid: B741C207-3427-46F3-9C90-A52BF8933FA4
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 6a2b8c99156963ae167aecd29a618d0feeffbdc7
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61228958"
---
# <a name="watchos-image-controls-in-xamarin"></a>watchOS 在 Xamarin 中的图像控件

提供了 watchOS [`WKInterfaceImage`](xref:WatchKit.WKInterfaceImage)控件来显示图像和简单的动画。 某些控件也可以 （例如按钮、 组和界面控制器） 的背景图像。

![](image-images/image-walkway.png "Apple Watch 显示图片") ![](image-images/image-animation.png "Apple Watch 与简单动画")
<!-- watch image courtesy of http://infinitapps.com/bezel/ -->

使用资产目录映像将映像添加到监视工具包应用程序。
仅 **@2x** 版本是必需的因为所有监视具有 Retina 显示的设备。

![](image-images/asset-universal-sml.png "只有 2 x 版本是必需的因为所有监视具有 Retina 显示的设备")

很好的做法，以确保图像本身的监视显示正确的大小。 *避免*使用不正确尺寸的图像 （尤其是大型的） 和缩放来监视上显示它们。

可以使用资产目录图像中的监视包大小 （38mm 和 42 mm） 来指定每个显示大小的不同映像。

![](image-images/asset-watch-sml.png "可用于监视工具包大小 38mm 和 42 mm 资产目录图像中为每个显示大小指定不同图像")


## <a name="images-on-the-watch"></a>Watch 上的图像

若要显示的图像的最有效方法是*将其包括在监视应用程序项目*并将其显示使用`SetImage(string imageName)`方法。

例如， [WatchKitCatalog](https://developer.xamarin.com/samples/WatchKitCatalog/)示例有许多映像添加到资产目录中监视应用程序项目：

![](image-images/asset-whale-sml.png "WatchKitCatalog 示例有许多映像添加到资产目录中监视应用程序项目")

这些可以有效地加载并显示在监视使用`SetImage`与字符串名称参数：

```csharp
myImageControl.SetImage("Whale");
myOtherImageControl.SetImage("Worry");
```

### <a name="background-images"></a>背景图像

相同的逻辑适用于`SetBackgroundImage (string imageName)`上`Button`， `Group`，和`InterfaceController`类。 将映像存储在 watch 应用本身实现最佳性能。


## <a name="images-in-the-watch-extension"></a>监视扩展中的图像

除了加载存储在 watch 应用本身中的图像，可以将扩展包的映像发送到显示 watch 应用 （或可以从远程位置下载映像，并显示这些）。

若要从监视扩展加载图像，请创建`UIImage`实例，然后调用`SetImage`与`UIImage`对象。

例如， [WatchKitCatalog](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)示例包含名为图像**Bumblebee**监视扩展项目中：

![](image-images/asset-bumblebee-sml.png "WatchKitCatalog 示例中的图像监视扩展项目中名为 Bumblebee")

下面的代码将导致：

- 正在加载到内存中，图像和
- 显示受监视。

```csharp
using (var image = UIImage.FromBundle ("Bumblebee")) {
    myImageControl.SetImage (image);
}
```


## <a name="animations"></a>Animations

若要进行动画处理的图像集，它们应所有以相同前缀开头，并有数字后缀。

[WatchKitCatalog](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)示例使用监视应用程序项目中有一系列已编号的图像**总线**前缀：

![](image-images/asset-bus-animation-sml.png "WatchKitCatalog 示例总线前缀与监视应用程序项目中具有一系列已编号的图像")

若要显示这些映像为动画，请首先加载映像使用`SetImage`前缀名称，然后调用`StartAnimating`:

```csharp
animatedImage.SetImage ("Bus");
animatedImage.StartAnimating ();
```

调用`StopAnimating`上要停止动画循环的图像控件：

```csharp
animatedImage.StopAnimating ();
```


<a name="cache" />

## <a name="appendix-caching-images-watchos-1"></a>附录：缓存映像 (watchOS 1)

> [!IMPORTANT]
> watchOS 3 应用程序完全在设备上运行。 以下信息适用于仅限在 watchOS 1 中应用。

如果应用程序重复使用存储的扩展中 （或已下载） 的映像，则可以缓存在监视的存储中，以提高性能的后续显示图像。

使用`WKInterfaceDevice`s`AddCachedImage`方法来将映像传输到监视项目，然后使用`SetImage`映像名称参数为要显示它的字符串的：

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

您可以查询图像缓存在代码中使用的内容`WKInterfaceDevice.CurrentDevice.WeakCachedImages`。


### <a name="managing-the-cache"></a>管理缓存

缓存的大小大约 20 MB。 保留在应用重启后，并且它填满时由您负责清除出使用的文件`RemoveCachedImage`或`RemoveAllCachedImages`上的方法`WKInterfaceDevice.CurrentDevice`对象。



## <a name="related-links"></a>相关链接

- [WatchKitCatalog （示例）](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [Apple 的图像文档](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/Images.html)
