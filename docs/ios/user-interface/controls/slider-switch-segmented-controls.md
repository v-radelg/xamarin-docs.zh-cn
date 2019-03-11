---
title: 滑块、 开关和分段的控件中 Xamarin.iOS
description: 本文档讨论幻灯片、 开关和分段的控件在 Xamarin.iOS 中，描述如何以编程方式和 iOS 设计器使用它们。
ms.prod: xamarin
ms.assetid: 85BF0EC8-E581-49CD-B9E7-98BE4C5A0F6B
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 2ed14752cc5906b68d277b4f492875f7e281b053
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57671450"
---
# <a name="sliders-switches-and-segmented-controls-in-xamarinios"></a>滑块、 开关和分段的控件中 Xamarin.iOS

<a name="Sliders" />

## <a name="sliders"></a>滑块

滑块控件允许的范围内的数字值的简单选择。 控件的默认值为 0 和 1 之间的值，但可以自定义这些限制。

 [![](slider-switch-segmented-controls-images/image25a.png "滑块")](slider-switch-segmented-controls-images/image25a.png#lightbox)

下面的屏幕截图显示了在设计器中可编辑的属性：

 [![](slider-switch-segmented-controls-images/image26a.png "滑块属性")](slider-switch-segmented-controls-images/image25a.png#lightbox)

可以在代码中设置这些值，如下所示，其中包括要显示中的当前所选的值的处理程序绑定`UILabel`控件：

```csharp
slider1.MinValue = -1;
slider1.MaxValue = 2;
slider1.Value = 0.5f; // the current value
slider1.ValueChanged += (sender,e) => label1.Text = ((UISlider)sender).Value.ToString ();
```

此外可以自定义设置滑块的可视外观

```csharp
slider1.ThumbTintColor = UIColor.Blue;
slider1.MinimumTrackTintColor = UIColor.Gray;
slider1.MaximumTrackTintColor = UIColor.Green;
```

自定义的滑块外观如下所示：

 [![](slider-switch-segmented-controls-images/image27a.png "自定义滑块")](slider-switch-segmented-controls-images/image28a.png#lightbox)

> [!IMPORTANT]
> 目前尚[bug](https://stackoverflow.com/a/19496179)导致`ThumbTint`按预期方式，在运行时不呈现。 可以添加以下代码行**之前**上面的代码作为一种解决方法。 [Source](https://stackoverflow.com/a/21396794)]:
>
> `slider1.SetThumbImage(UIImage.FromBundle("thumb.png"),UIControlState.Normal);`
> 
> 可以使用任何图像，因为它将被重写，但请确保将其放_在_Resources 目录并在代码中调用。

<a name="Switch" />

## <a name="switch"></a>开关

iOS 使用`UISwitch`作为一个布尔值输入的可能由其他平台上的单选按钮。 用户可以通过移动操作控件*thumb*之间**开/关**位置。

 [![](slider-switch-segmented-controls-images/image28a.png "开关")](slider-switch-segmented-controls-images/image28a.png#lightbox)

可以在自定义的交换机的外观**Properties Pad**的设计器中，它可以帮助您控制默认状态，**开/关色彩**颜色和**on/off 映像**. 下图所示：

 [![](slider-switch-segmented-controls-images/image29a.png "交换机属性")](slider-switch-segmented-controls-images/image29a.png#lightbox)

此外可以在代码中设置的交换机的属性，例如下面的代码将显示默认值为开关`On`:

```csharp
switch1.On = true;
```

 <a name="Segmented_Controls" />


## <a name="segmented-controls"></a>分段的控件

分段控件是有序的方式，以允许用户交互较少的选项。 它为水平布局和每个段函数作为一个单独的按钮。 当使用设计器，可在分段控件下**工具箱 > 控件**，并应如图所示：

 [![](slider-switch-segmented-controls-images/segmentedcontrol.png "分段的控件")](slider-switch-segmented-controls-images/segmentedcontrol.png#lightbox)

一种独特的设计器的功能允许要在设计图面上，单独选择每个段如下所示：

 [![](slider-switch-segmented-controls-images/segmentedcontrolselection.png "分段的控件")](slider-switch-segmented-controls-images/segmentedcontrolselection.png#lightbox)

这样，属性面板，以用于更精确地控制每个段的属性。 您可以看到以下屏幕截图中可编辑的属性：

 [![](slider-switch-segmented-controls-images/segmentedcontrolproperties.png "分段的控件")](slider-switch-segmented-controls-images/segmentedcontrolproperties.png#lightbox)

应注意，iOS7 中, 已弃用分段控件样式，因此，调整此 iOS7 应用程序中的选项将不起作用。

## <a name="related-links"></a>相关链接

- [控件 （示例）](https://developer.xamarin.com/samples/Controls/)
- [警报控制器](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/alertcontroller)
