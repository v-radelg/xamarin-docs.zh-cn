---
title: 默认资源
ms.prod: xamarin
ms.assetid: 762572F0-173A-D994-0510-8F36BEF3D487
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/13/2018
ms.openlocfilehash: 20865b71cce16f57b84a1c54986bd84180d3e190
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61013130"
---
# <a name="default-resources"></a>默认资源

默认资源是不特定于任何特定设备或外形规格，因此可以通过 Android OS 的默认选项如果没有更具体的项可以找到资源。 在这种情况下，它们是要创建资源的最常见类型。 它们到的子目录中的结构**资源**目录根据它们的资源类型：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![默认资源文件](default-resources-images/01-resource-files-vs.png)

在上图中，该项目具有可绘制资源、 布局和值 （包含简单值的 XML 文件） 的默认值。

下面提供了资源类型的完整列表：

-  **animator**&ndash;描述属性动画的 XML 文件。
   属性动画在 API 级别 11 (Android 3.0) 中引入，并提供用于对某个对象的属性的动画。 属性动画是对象的更加灵活和强大的方式来描述在任何类型上的动画。

-  **anim** &ndash;描述 XML 文件*tween*动画。 Tween 动画是文本的一系列的动画说明图像或不断增加大小的视图对象或示例中，旋转内容执行的转换。 Tween 动画被限制为只能查看对象。

-  **color**&ndash;描述颜色的状态列表的 XML 文件。 若要了解颜色状态列表，请考虑如按钮的 UI 小组件。
   它可能具有不同的状态，如按下或禁用，和按钮可能会随每个状态更改颜色。 列表表示状态列表中。

-  **drawable**&ndash;可绘制资源是用于图形可编译到应用程序，然后访问 API 调用或引用的其他 XML 资源的常规概念。
   绘图的一些示例是位图文件 （.png、.gif、.jpg）、 名为的特殊可调整大小的位图[包含 9 个修补程序](https://developer.android.com/guide/topics/graphics/2d-graphics.html#nine-patch)，列出了状态，泛型在 XML 等中定义的形状。
 
-  **layout**&ndash;描述用户界面布局，如活动或列表中的行的 XML 文件。

-  **menu** &ndash; XML 文件，如描述应用程序菜单*选项菜单*，*上下文菜单*，和*子菜单*。 菜单的示例，请参阅[弹出菜单演示](https://developer.xamarin.com/samples/monodroid/PopupMenuDemo/)或[标准控件](https://developer.xamarin.com/samples/mobile/StandardControls/)示例。

-  **raw**&ndash;保存在其原始的、 二进制形式的任意文件。 这些文件编译为二进制格式中的 Android 应用程序。

-  **values**&ndash;包含简单值的 XML 文件。 值目录中的 XML 文件不会定义单个资源，而您可以定义多个资源。 例如一个 XML 文件可能持有字符串值的列表，而另一个 XML 文件可能持有的颜色值列表。

-  **xml** &ndash;在函数类似于.NET 配置文件的 XML 文件。 这些是由应用程序可以在运行时中读取的任意 XML。


# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![默认资源文件](default-resources-images/01-resource-files-xs.png)

在上图中，该项目具有可绘制资源、 布局和值 （包含简单值的 XML 文件） 的默认值。

下面提供了资源类型的完整列表：

-  **animator**&ndash;描述属性动画的 XML 文件。
   属性动画在 API 级别 11 (Android 3.0) 中引入，并提供用于对某个对象的属性的动画。 属性动画是对象的更加灵活和强大的方式来描述在任何类型上的动画。

-  **anim** &ndash;描述 XML 文件*tween*动画。 Tween 动画是文本的一系列的动画说明图像或不断增加大小的视图对象或示例中，旋转内容执行的转换。 Tween 动画被限制为只能查看对象。

-  **color**&ndash;描述颜色的状态列表的 XML 文件。 若要了解颜色状态列表，请考虑如按钮的 UI 小组件。
   它可能会具有不同的状态，如按下或禁用，和按钮可能会随每个状态更改颜色。 列表表示状态列表中。

-  **font**&ndash;从 API 级别 26 年开始，可在 Android 应用程序中作为资源嵌入字体。 支持库 26 将向后移植到 API 级别 14 的字体。 通过嵌入字体，若要直接从 XML 布局加载自定义字体，而无需将其作为资产使用之前导入的应用程序。

-  **mipmap** &ndash;可绘制资源是可编译到应用程序，然后访问的 API 调用或引用的其他 XML 资源的图形的一般概念。
   绘图的一些示例是位图文件 （.png、.gif、.jpg）、 名为的特殊可调整大小的位图[包含 9 个修补程序](https://developer.android.com/guide/topics/graphics/2d-graphics.html#nine-patch)，列出了状态，泛型在 XML 等中定义的形状。

-  **layout**&ndash;描述用户界面布局，如活动或列表中的行的 XML 文件。

-  **menu** &ndash; XML 文件，如描述应用程序菜单*选项菜单*，*上下文菜单*，和*子菜单*。 菜单的示例，请参阅[弹出菜单演示](https://developer.xamarin.com/samples/monodroid/PopupMenuDemo/)或[标准控件](https://developer.xamarin.com/samples/mobile/StandardControls/)示例。

-  **raw**&ndash;保存在其原始的、 二进制形式的任意文件。 这些文件编译为二进制格式中的 Android 应用程序。

-  **values**&ndash;包含简单值的 XML 文件。 值目录中的 XML 文件不会定义单个资源，而您可以定义多个资源。 例如一个 XML 文件可能持有字符串值的列表，而另一个 XML 文件可能持有的颜色值列表。

-  **xml** &ndash;在函数类似于.NET 配置文件的 XML 文件。 这些是由应用程序可以在运行时中读取的任意 XML

-----
