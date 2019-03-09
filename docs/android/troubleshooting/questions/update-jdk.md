---
title: 如何更新 Java Development Kit (JDK) 版本？
description: 本文演示了如何更新 Windows 和 mac 上的 Java 开发工具包 (JDK) 版本
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 4b3ac51d-18dd-4034-87b4-4365194e4ece
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 09/07/2018
ms.openlocfilehash: 290a7deef4fdc10163bdb09881382f011b0dcd32
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57670838"
---
# <a name="how-do-i-update-the-java-development-kit-jdk-version"></a>如何更新 Java Development Kit (JDK) 版本？

_本文演示了如何更新 Windows 和 mac 上的 Java 开发工具包 (JDK) 版本_

## <a name="overview"></a>概述

Xamarin.Android 使用 Java 开发工具包 (JDK) 将与用于构建 Android 应用程序和运行 Android 设计器的 Android SDK 进行集成。 Android SDK (API 24 及更高版本) 的最新版本需要 JDK 8 (1.8)。 或者，也可以安装[Microsoft Mobile OpenJDK 预览版](~/android/get-started/installation/openjdk.md)。 Microsoft Mobile OpenJDK 将最终替换 Xamarin.Android 开发的 JDK 8。

若要更新到 Microsoft Mobile OpenJDK，请参阅[Microsoft Mobile OpenJDK 预览版](~/android/get-started/installation/openjdk.md)。 若要更新到 JDK 8，请按照下列步骤：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1.  从下载 JDK 8 (1.8) [Oracle 网站](https://www.oracle.com/technetwork/java/javase/downloads/index.html):

    ![屏幕截图的 jdk 下载 Oracle 网站上的页](update-jdk-images/image1.png)

2.  选择要允许的呈现的 64 位版本[自定义控件](https://developer.xamarin.com/releases/vs/xamarin.vs_4/xamarin.vs_4.2/#androiddesignercustomcontrols)Xamarin Android 设计器中：

    ![选择要从 JDK 下载页下载的 Windows x64 JDK 包](update-jdk-images/image2.png)

3.  运行.exe 和安装**开发工具**:

    ![中的 JDK 安装程序安装开发工具](update-jdk-images/image3.png)

4.  打开 Visual Studio 并更新**Java Development Kit 位置**以指向下新的 JDK**工具 > 选项 > Xamarin > Android 设置 > Java Development Kit 位置**:

    [![在 Android 设置页中的 JDK 路径设置](update-jdk-images/image4-sml.png)](update-jdk-images/image4.png#lightbox)

请确保更新的位置后，重启 Visual Studio。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1.  从下载 JDK 8 (1.8) [Oracle 网站](https://www.oracle.com/technetwork/java/javase/downloads/index.html):

    ![屏幕截图的 jdk 下载 Oracle 网站上的页](update-jdk-images/image1.png)

2.  打开.dmg 文件并运行.pkg 安装程序：

    ![在 macOS 上运行的 JDK 安装程序](update-jdk-images/image5.png)

Mac OS 将自动将新的 JDK 版本设置为默认值通过更新 **/System/Library/Frameworks/JavaVM.framework/Versions/Current**。 然后可以仔细检查该**Java SDK (JDK)** 位置设置为预期的默认值为 **/usr**下**Visual Studio for Mac > 首选项 > 项目 > SDK 位置 >Android > 位置 > Java SDK (JDK) 位置**:

[![在 Android 位置选项卡中设置 JDK 位置](update-jdk-images/image6-sml.png)](update-jdk-images/image6.png#lightbox)

-----

