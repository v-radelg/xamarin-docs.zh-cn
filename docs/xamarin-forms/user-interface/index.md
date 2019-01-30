---
title: Xamarin.Forms 用户界面视图
description: 本文介绍了控件和 Xamarin.Forms 应用程序中创建用户界面时可使用的概念。
ms.prod: xamarin
ms.assetid: 391B4A77-7CAC-42D2-9E77-BD8E170E9BE6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/14/2018
ms.openlocfilehash: ecf8a1e6d7c248c7141039844324d45d28700854
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/07/2018
ms.locfileid: "53049337"
---
# <a name="xamarinforms-user-interface-views"></a>Xamarin.Forms 用户界面视图

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/FormsGallery/)

_如何使用 Xamarin.Forms 提供的视图_

## <a name="animationanimationindexmd"></a>[动画](animation/index.md)

Xamarin.Forms 具有自己的动画基础结构直接用于创建简单动画，而且还多样化足以创建复杂动画。

## <a name="boxviewboxviewmd"></a>[BoxView](boxview.md)

`BoxView`只是一个简单有色的矩形，但装饰性项，基本图形，以及用于获取交互式触摸输入，可以使用它。

## <a name="buttonbuttonmd"></a>[Button](button.md)

`Button`响应点击或单击，将定向的应用程序来执行特定任务。

## <a name="collectionviewcollectionviewmd"></a>[CollectionView](collectionview.md)

`CollectionView`是灵活且高性能的视图来显示数据使用不同的布局规范的列表。

## <a name="colorscolorsmd"></a>[颜色](colors.md)

每个平台都有其自己的标准和默认值时，定义和跨平台使用的颜色可能比较棘手。

## <a name="controls-referencecontrolsindexmd"></a>[控件引用](controls/index.md)

本文档是如组成 Xamarin.Forms 框架的 UI 视图的快速参考[Pages](~/xamarin-forms/user-interface/controls/pages.md)，[布局](~/xamarin-forms/user-interface/controls/layouts.md)，[视图](~/xamarin-forms/user-interface/controls/views.md)和[单元格](~/xamarin-forms/user-interface/controls/cells.md).

## <a name="datapagesdatapagesindexmd"></a>[DataPages](datapages/index.md)

DataPages 提供一个 API，用于快速、 轻松地将数据源绑定到预建的视图。 列表项和详细信息页将自动呈现数据，并使用主题自定义。

## <a name="datepickerdatepickermd"></a>[DatePicker](datepicker.md)

`DatePicker`使用户可以选择指定范围内的一个日期。 实现使用日期选取器受特定平台上运行应用程序。

## <a name="graphics-with-skiasharpgraphicsskiasharpindexmd"></a>[使用 SkiaSharp 处理图形](graphics/skiasharp/index.md)

如何将图形合并到使用 SkiaSharp 的 Xamarin.Forms 应用程序。

## <a name="imagesimagesmd"></a>[图像](images.md)

可以使用 Xamarin.Forms 跨平台共享映像、 可以专门为每个平台，加载它们或它们可以为显示下载。

## <a name="imagebuttonimagebuttonmd"></a>[ImageButton](imagebutton.md)

`ImageButton`显示图像和响应点击或单击，将定向的应用程序来执行特定任务。

## <a name="layoutslayoutsindexmd"></a>[布局](layouts/index.md)

Xamarin.Forms 具有用于组织屏幕内容的多个布局。 `StackLayout``Grid`， `FlexLayout`， `AbsoluteLayout`， `ScrollView`，和`RelativeLayout`每个可用来创建美观、 响应迅速的用户界面。

## <a name="listviewlistviewindexmd"></a>[ListView](listview/index.md)

Xamarin.Forms 提供了显示的数据的滚动行的列表视图控件。 控件包括上下文操作`HasUnevenRows`自动调整大小、 自定义分隔符、 下拉刷新和页眉和页脚。

## <a name="mapsmapmd"></a>[地图](map.md)

添加映射需要额外的 NuGet 包下载和一些特定于平台的配置。 完成配置后，可以在只需几行代码中添加地图和 pin 标记。

## <a name="pickerpickerindexmd"></a>[Picker](picker/index.md)

[ `Picker` ](xref:Xamarin.Forms.Picker)视图是一个用于选择文本项中的数据列表控件。

## <a name="sliderslidermd"></a>[Slider](slider.md)

`Slider`允许用户从连续范围选择的数字值。

## <a name="steppersteppermd"></a>[Stepper](stepper.md)

`Stepper`允许用户从一系列值中选择的数字值。 它包含两个按钮带有负号和加号。 以增量方式将操作的两个按钮更改所选的值。

## <a name="stylesstylesindexmd"></a>[样式](styles/index.md)

字体、 颜色和其他属性可以分组为样式，它们可以在控件、 布局或使用 ResourceDictionaries 的整个应用程序之间共享。

## <a name="tableviewtableviewmd"></a>[TableView](tableview.md)

表视图是类似于列表视图中，但而不是正在设计的数据的长列表它适用于数据条目样式的滚动控件或简单滚动菜单的屏幕。

## <a name="texttextindexmd"></a>[文本](text/index.md)

Xamarin.Forms 有多个视图来显示和接收文本。 可以进行格式化并针对平台自定义文本视图。 特定字体设置可以启用与辅助功能的兼容性。

## <a name="themesthemesindexmd"></a>[主题](themes/index.md)

Xamarin.Forms 主题定义特定的可视化外观，对标准控件。 一旦应用程序的资源字典中添加了一个主题，将更改标准控件的外观。

## <a name="timepickertimepickermd"></a>[TimePicker](timepicker.md)

`TimePicker`允许用户选择的时间。 它使用特定平台上运行应用程序的支持时间选取器实现。

## <a name="visualvisualmd"></a>[视觉对象](visual.md)

本文介绍了 Xamarin.Forms 视觉对象，在 iOS 和 Android 相同，或很大程度上相同，将视图呈现。

## <a name="visual-state-managervisual-state-managermd"></a>[可视状态管理器](visual-state-manager.md)

视觉状态管理器提供的结构化的方法来触发从代码中，包括布局可适应更改设备方向或大小更改的用户界面中的更改。

## <a name="webviewwebviewmd"></a>[WebView](webview.md)

Xamarin.Forms 使用每个平台上的本机 web 浏览器控件，并可以显示网站、 本地资源和生成的 Html 字符串。

## <a name="related-links"></a>相关链接

- [Xamarin.Forms 简介](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md)
- [Xamarin.Forms 库（示例）](https://developer.xamarin.com/samples/FormsGallery/)
