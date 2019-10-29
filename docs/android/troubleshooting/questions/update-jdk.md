---
title: 如何更新 Java Development Kit (JDK) 版本？
description: 本文介绍如何在 Windows 和 Mac 上更新 Java 开发工具包（JDK）版本。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 4b3ac51d-18dd-4034-87b4-4365194e4ece
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 09/07/2018
ms.openlocfilehash: 0f7499551db7d86d7978b9c3e1f562a2f054c202
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73019519"
---
# <a name="how-do-i-update-the-java-development-kit-jdk-version"></a>如何更新 Java Development Kit (JDK) 版本？

_本文介绍如何在 Windows 和 Mac 上更新 Java 开发工具包（JDK）版本。_

## <a name="overview"></a>概述

Xamarin 使用 Java 开发工具包（JDK）与用于构建 Android 应用和运行 Android 设计器的 Android SDK 集成。 最新版本的 Android SDK （API 24 和更高版本）需要 JDK 8 （1.8）。 或者，你可以安装[Microsoft Mobile OpenJDK Preview](~/android/get-started/installation/openjdk.md)。 Microsoft Mobile OpenJDK 最终将为 Xamarin Android 开发替换 JDK 8。

若要更新 Microsoft Mobile OpenJDK，请参阅[Microsoft Mobile OpenJDK Preview](~/android/get-started/installation/openjdk.md)。 若要更新到 JDK 8，请按照以下步骤操作：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 从[Oracle 网站](https://www.oracle.com/technetwork/java/javase/downloads/index.html)下载 JDK 8 （1.8）：

    ![Oracle 网站上的 JDK 下载页的屏幕截图](update-jdk-images/image1.png)

2. 选择64位版本，允许在 Xamarin Android 设计器中呈现[自定义控件](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/vs/xamarin.vs_4/xamarin.vs_4.2/index.md#androiddesignercustomcontrols)：

    ![从 JDK 下载页选择要下载的 Windows x64 JDK 包](update-jdk-images/image2.png)

3. 运行 .exe 并安装**开发工具**：

    ![在 JDK 安装程序中安装开发工具](update-jdk-images/image3.png)

4. 打开 Visual Studio 并更新**Java 开发工具包位置**，使之指向 "工具" > "工具" 下的新 JDK **> Xamarin > Android 设置 > Java 开发工具包位置**：

    ["Android 设置" 页中的 JDK![路径设置](update-jdk-images/image4-sml.png)](update-jdk-images/image4.png#lightbox)

请确保在更新该位置后重启 Visual Studio。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 从[Oracle 网站](https://www.oracle.com/technetwork/java/javase/downloads/index.html)下载 JDK 8 （1.8）：

    ![Oracle 网站上的 JDK 下载页的屏幕截图](update-jdk-images/image1.png)

2. 打开 dmg 文件并运行. .pkg 安装程序：

    ![在 macOS 上运行 JDK 安装程序](update-jdk-images/image5.png)

Mac OS 将通过更新 **/System/Library/Frameworks/JavaVM.framework/Versions/Current**自动将新的 JDK 版本设置为默认版本。 然后，你可以仔细检查**JAVA sdk （jdk）** 位置是否设置为 "Visual Studio for Mac > 首选项" 下的 "默认值为 **/Usr** " **> 项目 > SDK 位置 > "Java sdk （JDK）位置 > Android > 位置**：

[在 "Android 位置" 选项卡中设置 JDK 位置![](update-jdk-images/image6-sml.png)](update-jdk-images/image6.png#lightbox)

-----
