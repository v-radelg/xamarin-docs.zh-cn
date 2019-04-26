---
title: watchOS 在 Xamarin 中的用户界面控件
description: 本文档描述了可用于在 watchOS 用户界面中使用的各种控件。 它提供标签、 按钮、 开关、 滑块、 映像、 分隔符、 映射和的详细的说明。
ms.prod: xamarin
ms.assetid: EDFAD203-02EA-4A74-9CE2-7B8513BC90E1
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/19/2016
ms.openlocfilehash: c8ef76f24b017f5e3e6bec9d39534f3626e79147
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60892930"
---
# <a name="watchos-user-interface-controls-in-xamarin"></a>watchOS 在 Xamarin 中的用户界面控件

[ **WatchKitCatalog** ](https://github.com/xamarin/monotouch-samples/tree/master/watchOS/WatchKitCatalog)示例演示各种 watchOS 控件。 应用程序的情节提要是如下所示 （点击放大）：

[![](images/storyboard-sml.png "示例 watchOS 布局")](images/storyboard.png#lightbox)

所有控件的编程名称前缀与`WKInterface`（例如。 `WKInterfaceLabel`, `WKInterfaceButton`).

|控件|描述|屏幕快照|
|---|---|---|
|Label|使用`SetText`和其他属性，以控制标签控件中文本的外观。 `NSAttributedString` 也支持。<br />[目录代码](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/LabelDetailController.cs)|![](Images/label.png)|
|Button|创建并在情节提要中设置属性。 Ctrl + 拖动以添加`Action`以实现单击时的处理程序。<br />[目录代码](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ButtonDetailController.cs)|![](Images/button.png)|
|开关|使用`SetOn`来控制的开关状态。<br />[目录代码](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SwitchDetailController.cs)|![](Images/switch.png)|
|Slider|可能会出现许多不同的样式。<br />[目录代码](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SliderDetailController.cs)|![](Images/slider.png)|
|图像|使用`myImage.SetImage("MyWatchImage")`加载 watch 上的图像或`WKInterfaceDevice.CurrentDevice.AddCachedImage`来缓存它们以便重复使用的手表上。<br />[图像控件文档](~/ios/watchos/user-interface/image.md)<br />[目录代码](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ImageDetailController.cs)|![](Images/image.png)|
|Separator|使用分隔符来帮助创建吸引人观看 Ui。<br />[目录代码](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SeparatorDetailController.cs)|![](Images/separator.png)| 
|映射|地图图像以静态方式显示受监视，但可以控制其外观，包括添加固定内容的许多方面。<br />[目录代码](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/MapDetailController.cs)|![](Images/map.png)|
|Movie & InlineMove|电影可以代表自身，打开或内联<br />[目录代码](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/MovieDetailController.cs)|![](Images/movie.png)|
|Group|使用组来帮助创建吸引人观看 Ui。<br />[目录代码](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/GroupDetailController.cs)|![](Images/group.png)|
|表|在 iOS 上的表的简化的版本。 实现`DidSelectRow`响应用户选择 （或使用 segue）。<br />[表控件文档](~/ios/watchos/user-interface/table.md)<br />[目录代码](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/Table%20Detail%20Controller/TableDetailController.cs)|![](Images/table.png)|
|设备|`WKInterfaceDevice.CurrentDevice` 包括属性，如`ScreenBounds`， `ScreenScale`，和`PreferredContentSizeCategory`。<br />[目录代码](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/DeviceDetailController.cs)|![](Images/device.png)|
|[菜单](~/ios/watchos/user-interface/menu.md)|在情节提要中定义的强制按菜单和在代码中实现每个按钮的操作。<br />[菜单控件 (Force Touch) 文档](~/ios/watchos/user-interface/menu.md)<br />[目录代码](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ControllerDetailController.cs)|![](Images/controller.png)|
|文本输入|使用`PresentTextInputController`和`WKTextInputMode`枚举。<br />[文本输入文档](~/ios/watchos/user-interface/text-input.md)<br />[目录代码](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/TextInputController.cs)|![](Images/textinput.png)|
|数字 Crown|可以使用数字 Crown 来驱动选取器，或可以在代码中跟踪它的旋转。<br />[目录代码](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/CrownDetailController.cs)|![](Images/digital-crown.png)|
|笔势|有四种类型的手势识别，可以添加到一个场景：点击、 轻扫、 平移和 LongPress。<br />[目录代码](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/GestureDetailController.cs)|![](Images/gestures.png)|


## <a name="related-links"></a>相关链接

- [WatchKitCatalog （示例）](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [观看工具包 API 参考](xref:WatchKit)
