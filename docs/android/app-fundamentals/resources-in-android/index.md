---
title: Android 资源
description: 本文介绍了 Android 资源在 Xamarin.Android 中的概念，并将介绍如何使用它们。 它介绍了如何在 Android 应用程序中使用的资源以支持应用程序本地化和多个设备，包括不同的屏幕大小和密度。
ms.prod: xamarin
ms.assetid: C0DCC856-FA36-04CD-443F-68D26075649E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/01/2018
ms.openlocfilehash: f14b3fd31fdda200f51f429367465677d389b1ca
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57666335"
---
# <a name="android-resources"></a>Android 资源

_本文介绍了 Android 资源在 Xamarin.Android 中的概念，并将介绍如何使用它们。它介绍了如何在 Android 应用程序中使用的资源以支持应用程序本地化和多个设备，包括不同的屏幕大小和密度。_


## <a name="overview"></a>概述

Android 应用程序很少是只需源代码。 通常有多个组成应用程序的其他文件： 视频、 图像、 字体和音频文件，只是为了仅举几例。 总体来说，这些非源代码文件称为资源和编译 （以及源代码） 在生成过程并打包为分发和安装到设备上的 APK:

![打包关系图](images/packaging-diagram.png)

资源到 Android 应用程序有几个优点：

-  **代码分离**&ndash;将源代码与图像、 字符串、 菜单、 动画、 颜色等。这种情况下的资源可帮助显著本地化时。

-  **面向多个设备**&ndash;提供更简单的无需更改代码的不同设备配置的支持。

-  **编译时检查**&ndash;资源是静态的编译到应用程序。 这允许在编译时，当它很容易就可以捕获并更正错误，而不是运行时更难找到并更正而且成本很高时，要检查的资源的使用情况。

启动新的 Xamarin.Android 项目时，被创建一个名为资源的特殊目录，以及一些子目录：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![资源文件夹及其内容](images/resources-folder-vs.png)

在上图中，应用程序资源被按类型组织到以下子目录中：图像进入 **drawable** 目录；视图进入 **layout** 子目录，依此类推。
 
# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![资源文件夹及其内容](images/resources-folder-xs.png)

在上图中，应用程序资源被按类型组织到以下子目录中：图像进入 **mipmap** 目录；视图进入 **layout** 子目录，依此类推。
 
-----

有两种方法访问这些资源在 Xamarin.Android 应用程序中：*以编程方式*代码中和*以声明方式*中使用特殊的 XML 语法的 XML。

这些资源称为*默认资源*，除非另有指定更具体的匹配项由所有设备。 此外，还可以具有每种类型的资源*备用资源*Android 可用于针对特定的设备。 例如，可能会提供资源以面向用户的区域设置，屏幕大小或设备是否从纵向向横向，旋转 90 度等。在每个这种情况下，Android 将加载应用程序而无需任何额外编码工作由开发人员使用的资源。

通过添加一个短字符串，调用指定备用资源*限定符*，到末尾的给定的类型的资源的目录。

例如，**resources/drawable-de** 将为设置为德语区域设置的设备指定图像，而 **resources/drawable-fr** 则会为设置为法语区域设置的设备保存图像。 下图可以看到一个提供备用资源的示例，其中运行的是同一应用程序，只更改了设备的区域设置：

![不同的区域设置的示例屏幕](images/localized-screenshots.png)

本文将全面看一下使用资源，并涵盖以下主题：

-  **Android 资源基础知识**&ndash;使用的默认资源以编程方式或声明的方式，如图像和字体的资源类型添加到应用程序。

-  **设备特定的配置**&ndash;应用程序中支持的不同屏幕分辨率和密度。

-  **本地化**&ndash;使用资源来支持应用程序可能使用不同的区域。


## <a name="related-links"></a>相关链接

- [使用 Android 资产](~/android/app-fundamentals/resources-in-android/android-assets.md)
- [应用程序基础知识](https://developer.android.com/guide/topics/fundamentals.html)
- [应用程序资源](https://developer.android.com/guide/topics/resources/index.html)
- [支持多个屏幕](https://developer.android.com/guide/practices/screens_support.html)
