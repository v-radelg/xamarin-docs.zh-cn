---
title: Xamarin 中的 watchOS 用户界面控件
description: 本文档介绍可在 watchOS 用户界面中使用的各种控件。 它提供标签、按钮、开关、滑块、图像、分隔符、地图等的说明。
ms.prod: xamarin
ms.assetid: EDFAD203-02EA-4A74-9CE2-7B8513BC90E1
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 09/19/2016
ms.openlocfilehash: 7ac96bce706d42d4334004e62762ff21231f0162
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70766869"
---
# <a name="watchos-user-interface-controls-in-xamarin"></a>Xamarin 中的 watchOS 用户界面控件

[**WatchKitCatalog**](https://github.com/xamarin/monotouch-samples/tree/master/watchOS/WatchKitCatalog)示例演示各种 watchOS 控件。 此处显示了应用的情节提要（单击以缩放）：

[![](images/storyboard-sml.png "示例 watchOS 布局")](images/storyboard.png#lightbox)

所有控件的编程名称都带有`WKInterface`前缀（例如 `WKInterfaceLabel`, `WKInterfaceButton`).

|控件|描述|屏幕快照|
|---|---|---|
|Label|使用`SetText`和其他属性可控制标签控件中文本的外观。 `NSAttributedString`也支持。<br />[目录代码](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/LabelDetailController.cs)|![](Images/label.png)|
|Button|在情节提要中创建和设置属性。 按住 Ctrl 并拖动以添加`Action` ，以便在单击时实现处理程序。<br />[目录代码](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ButtonDetailController.cs)|![](Images/button.png)|
|开关|使用`SetOn`控制开关状态。<br />[目录代码](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SwitchDetailController.cs)|![](Images/switch.png)|
|Slider|可能有许多不同的样式。<br />[目录代码](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SliderDetailController.cs)|![](Images/slider.png)|
|图像|使用`myImage.SetImage("MyWatchImage")`在手表上加载图像，或`WKInterfaceDevice.CurrentDevice.AddCachedImage`将其缓存以在手表上重复使用。<br />[图像控件文档](~/ios/watchos/user-interface/image.md)<br />[目录代码](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ImageDetailController.cs)|![](Images/image.png)|
|Separator|使用分隔符帮助创建引人注目的监视 Ui。<br />[目录代码](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SeparatorDetailController.cs)|![](Images/separator.png)| 
|映射|地图图像在手表上静态显示，但您可以控制其外观的许多方面，包括添加 pin。<br />[目录代码](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/MapDetailController.cs)|![](Images/map.png)|
|电影 & InlineMove|电影可以自行打开，也可以以内联方式打开<br />[目录代码](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/MovieDetailController.cs)|![](Images/movie.png)|
|Group|使用组来帮助创建引人注目的监视 Ui。<br />[目录代码](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/GroupDetailController.cs)|![](Images/group.png)|
|表|IOS 上的表的简化版本。 实现`DidSelectRow`以响应用户选择（或使用 segue）。<br />[表控件文档](~/ios/watchos/user-interface/table.md)<br />[目录代码](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/Table%20Detail%20Controller/TableDetailController.cs)|![](Images/table.png)|
|设备|`WKInterfaceDevice.CurrentDevice`包括`ScreenBounds`、 `ScreenScale`和等属性。`PreferredContentSizeCategory`<br />[目录代码](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/DeviceDetailController.cs)|![](Images/device.png)|
|[菜单](~/ios/watchos/user-interface/menu.md)|在情节提要中定义强制新闻菜单，并为代码中的每个按钮实现操作。<br />[菜单控件（Force Touch）文档](~/ios/watchos/user-interface/menu.md)<br />[目录代码](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ControllerDetailController.cs)|![](Images/controller.png)|
|文本输入|`PresentTextInputController` 使用`WKTextInputMode`和枚举。<br />[文本输入文档](~/ios/watchos/user-interface/text-input.md)<br />[目录代码](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/TextInputController.cs)|![](Images/textinput.png)|
|Digital Crown|Digital Crown 可用于驱动选取器，也可在代码中跟踪旋转。<br />[目录代码](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/CrownDetailController.cs)|![](Images/digital-crown.png)|
|笔势|有四种类型的手势识别可添加到场景：点击、刷平、平移和 LongPress。<br />[目录代码](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/GestureDetailController.cs)|![](Images/gestures.png)|

## <a name="related-links"></a>相关链接

- [WatchKitCatalog （示例）](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [观看工具包 API 参考](xref:WatchKit)
