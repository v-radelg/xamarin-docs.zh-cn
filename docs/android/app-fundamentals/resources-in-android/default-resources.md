---
title: 默认资源
ms.prod: xamarin
ms.assetid: 762572F0-173A-D994-0510-8F36BEF3D487
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/13/2018
ms.openlocfilehash: e8277ff92c2ac1891c60b8da385a904af1bd299d
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69526292"
---
# <a name="default-resources"></a>默认资源

默认资源是指不特定于任何特定设备或外形规格的项, 因此, 如果找不到更多特定的资源, 则 Android OS 会默认选择。 因此, 它们是要创建的最常见的资源类型。 根据资源类型, 将它们组织到**Resources**目录的子目录中:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![默认资源文件](default-resources-images/01-resource-files-vs.png)

在上面的图像中, 项目具有可绘制资源、布局和值的默认值 (包含简单值的 XML 文件)。

下面提供了资源类型的完整列表:

- **animator**&ndash;描述属性动画的 XML 文件。
   在 API 级别 11 (Android 3.0) 中引入了属性动画, 并为对象的属性提供了动画。 属性动画是一种更灵活、更有效的方式来描述任意类型的对象上的动画。

- **anim**描述补间动画的 XML 文件。 &ndash; 补间动画是一系列动画说明, 用于对视图对象的内容执行转换, 例如, 旋转图像或增加文本大小。 补间动画仅限视图对象。

- **color**&ndash;描述颜色的状态列表的 XML 文件。 若要理解颜色状态列表, 请考虑 UI 小组件, 如按钮。
   它可能具有不同的状态 (如按下或禁用), 该按钮可能会改变状态中每个更改的颜色。 列表以状态列表表示。

- **drawable**&ndash;可绘制资源是用于图形可编译到应用程序，然后访问 API 调用或引用的其他 XML 资源的常规概念。
   绘图的一些示例包括位图文件 (.png、.gif、.jpg)、特殊大小的特殊位图 (称为[9 修补程序](https://developer.android.com/guide/topics/graphics/2d-graphics.html#nine-patch))、状态列表、XML 中定义的通用形状等。
 
- **布局**&ndash;描述用户界面布局的 XML 文件, 如活动或列表中的行。

- **menu** &ndash; XML 文件，如描述应用程序菜单*选项菜单*，*上下文菜单*，和*子菜单*。 有关菜单的示例, 请参阅[弹出菜单演示](https://docs.microsoft.com/samples/xamarin/monodroid-samples/popupmenudemo)或[标准控件](https://docs.microsoft.com/samples/xamarin/mobile-samples/standardcontrols/)示例。

- **raw**&ndash;保存在其原始的、 二进制形式的任意文件。 这些文件将编译为采用二进制格式的 Android 应用程序。

- **values**&ndash;包含简单值的 XML 文件。 值目录中的 XML 文件不定义单个资源, 而是可以定义多个资源。 例如, 一个 XML 文件可以保存一个字符串值列表, 而另一个 XML 文件可能包含一个颜色值列表。

- 在 .net 配置文件中具有相似功能的**xml** &ndash; xml 文件。 这些是可在运行时由应用程序读取的任意 XML。


# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![默认资源文件](default-resources-images/01-resource-files-xs.png)

在上面的图像中, 项目具有可绘制资源、布局和值的默认值 (包含简单值的 XML 文件)。

下面提供了资源类型的完整列表:

- **animator**&ndash;描述属性动画的 XML 文件。
   在 API 级别 11 (Android 3.0) 中引入了属性动画, 并为对象的属性提供了动画。 属性动画是一种更灵活、更有效的方式来描述任意类型的对象上的动画。

- **anim**描述补间动画的 XML 文件。 &ndash; 补间动画是一系列动画说明, 用于对视图对象的内容执行转换, 例如, 旋转图像或增加文本大小。 补间动画仅限视图对象。

- **color**&ndash;描述颜色的状态列表的 XML 文件。 若要理解颜色状态列表, 请考虑 UI 小组件, 如按钮。
   它可能具有不同的状态 (如按下或禁用), 该按钮可能会改变状态中每个更改的颜色。 列表以状态列表表示。

- **font**&ndash;从 API 级别 26 年开始，可在 Android 应用程序中作为资源嵌入字体。 支持库26将向 API 级别14向后移植字体。 嵌入字体允许应用程序直接从 XML 布局加载自定义字体, 无需在使用之前将其作为资产导入。

- **mipmap**&ndash;可绘制资源是图形的一般概念, 可编译到应用程序中, 然后通过 API 调用进行访问, 也可由其他 XML 资源引用。
   绘图的一些示例包括位图文件 (.png、.gif、.jpg)、特殊大小的特殊位图 (称为[9 修补程序](https://developer.android.com/guide/topics/graphics/2d-graphics.html#nine-patch))、状态列表、XML 中定义的通用形状等。

- **布局**&ndash;描述用户界面布局的 XML 文件, 如活动或列表中的行。

- **menu** &ndash; XML 文件，如描述应用程序菜单*选项菜单*，*上下文菜单*，和*子菜单*。 有关菜单的示例, 请参阅[弹出菜单演示](https://docs.microsoft.com/samples/xamarin/monodroid-samples/popupmenudemo)或[标准控件](https://docs.microsoft.com/samples/xamarin/mobile-samples/standardcontrols/)示例。

- **raw**&ndash;保存在其原始的、 二进制形式的任意文件。 这些文件将编译为采用二进制格式的 Android 应用程序。

- **values**&ndash;包含简单值的 XML 文件。 值目录中的 XML 文件不定义单个资源, 而是可以定义多个资源。 例如, 一个 XML 文件可以保存一个字符串值列表, 而另一个 XML 文件可能包含一个颜色值列表。

- 在 .net 配置文件中具有相似功能的**xml** &ndash; xml 文件。 这些是可在运行时由应用程序读取的任意 XML

-----
