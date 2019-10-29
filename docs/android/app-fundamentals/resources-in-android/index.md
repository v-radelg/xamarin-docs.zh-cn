---
title: Android 资源
description: 本文介绍了 Xamarin 中 Android 资源的概念，并介绍了如何使用这些资源。 其中介绍了如何使用 Android 应用程序中的资源来支持应用程序本地化和多个设备，包括不同的屏幕大小和密度。
ms.prod: xamarin
ms.assetid: C0DCC856-FA36-04CD-443F-68D26075649E
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/01/2018
ms.openlocfilehash: 1ff4d9d896aaa5f290402a49aa4b4bd1f1e00aaf
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73025060"
---
# <a name="android-resources"></a>Android 资源

_本文介绍了 Xamarin 中 Android 资源的概念，并介绍了如何使用这些资源。其中介绍了如何使用 Android 应用程序中的资源来支持应用程序本地化和多个设备，包括不同的屏幕大小和密度。_

## <a name="overview"></a>概述

Android 应用程序很少只是源代码。 通常有许多其他文件组成应用程序：视频、图像、字体和音频文件。 这些非源代码文件统称为资源，并在生成过程中进行编译，并打包为 APK 以便分发和安装到设备上：

![打包关系图](images/packaging-diagram.png)

资源为 Android 应用程序提供了若干优点：

- **代码分离**&ndash; 将源代码与图像、字符串、菜单、动画、颜色等分隔开来。当本地化时，此类资源可以很大的帮助。

- **面向多个设备**&ndash; 提供对不同设备配置的更简单支持，而无需更改代码。

- **编译时检查**&ndash; 资源是静态的，并编译到应用程序中。 这允许在编译时检查资源的使用情况，在这种情况下，可以轻松地捕获和更正错误，而不是在更难找到且更昂贵的时候运行。

当启动新的 Xamarin Android 项目时，将创建一个名为 "资源" 的特殊目录以及一些子目录：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![资源文件夹和内容](images/resources-folder-vs.png)

在上面的图像中，应用程序资源根据其类型组织到这些子目录中：图像将进入可**绘制**目录;视图位于**布局**子目录中，等等。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![资源文件夹和内容](images/resources-folder-xs.png)

在上面的图像中，应用程序资源根据其类型组织到这些子目录中：图像将进入**mipmap**目录;视图位于**布局**子目录中，等等。

-----

可以通过两种方法在 Xamarin Android 应用程序中访问这些资源：*以编程方式*在代码中，并使用特殊的 xml 语法在 XML 中*以声明*方式。

这些资源称为 "*默认资源*"，并由所有设备使用，除非指定了更具体的匹配项。 此外，每种类型的资源可能有可选的*备用资源*，Android 可能会使用这些资源来定位特定设备。 例如，可能会提供资源以面向用户的区域设置、屏幕大小，或者设备是否旋转了90度（从纵向到横向）等。在上述每种情况下，Android 都将加载用于应用程序的资源，而无需开发人员进行任何额外的编码工作。

通过向包含给定资源类型的目录的末尾添加一个名为*限定符*的短字符串来指定备用资源。

例如，**资源/可绘制的 de**将为设置为德语区域设置的设备指定映像，而**资源/可绘制的**将为设置为法语区域设置的设备保留图像。 在下图中，提供备用资源的示例如下所示：在运行同一应用程序时，只需更改设备区域设置：

![不同区域设置的示例屏幕](images/localized-screenshots.png)

本文全面介绍了如何使用资源，并涵盖了以下主题：

- **Android 资源基础知识**&ndash; 以编程方式使用默认资源，并以声明方式向应用程序添加资源类型（如图像和字体）。

- **设备特定配置**&ndash; 支持应用程序中的不同屏幕分辨率和密度。

- **本地化**&ndash; 使用资源来支持应用程序可使用的不同区域。

## <a name="related-links"></a>相关链接

- [使用 Android 资产](~/android/app-fundamentals/resources-in-android/android-assets.md)
- [应用程序基础知识](https://developer.android.com/guide/topics/fundamentals.html)
- [应用程序资源](https://developer.android.com/guide/topics/resources/index.html)
- [支持多个屏幕](https://developer.android.com/guide/practices/screens_support.html)
