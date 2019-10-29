---
title: 可以在哪里设置 Android SDK 位置？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 6A9DE6E9-3E27-4DD2-87D2-34E95E5D401C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 11/16/2017
ms.openlocfilehash: 8685be4bb1cc45ff04dc8d9f7d8e64e7b1483b60
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027028"
---
# <a name="where-can-i-set-my-android-sdk-locations"></a>可以在哪里设置 Android SDK 位置？

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

在 Visual Studio 中，导航到 "**工具" > "> Xamarin > Android 设置" 选项**以查看和设置 Android SDK 位置：

[首选项中的![示例位置选项卡](android-sdk-location-images/win/01-locations-sml.png)](android-sdk-location-images/win/01-locations.png#lightbox)

每个路径的默认位置如下所示：

- Java 开发工具包位置： 

    **C：\\程序文件\\Java\\jdk 1.8.0 _131**

- Android SDK 位置： 

    C:\\Program Files (x86)\\Android\\android-sdk

- Android NDK 位置： 

    **C：\\ProgramData\\Microsoft\\AndroidNDK64\\android-r13b**

请注意，NDK 的版本号可能会有所不同。 例如，它可以是较早的版本，如**android-r10e**，而不是**r13b**。

若要设置 Android SDK 位置，请在 " **Android SDK 位置**" 框中输入 Android SDK 目录的完整路径。 您可以在文件资源管理器中导航到 Android SDK 位置，从地址栏复制路径，然后将此路径粘贴到**Android SDK 位置**框中。
例如，如果你的 Android SDK 位置是**C：\\用户\\用户名\\AppData\\本地\\Android\\SDK**，请清除 " **Android SDK 位置**" 框中的旧路径，粘贴到此路径中，然后单击 **"确定"** 。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

在 Visual Studio for Mac 中，导航到 "**首选项" > 项目 > SDK 位置 > Android**"。 在**Android**页面中，单击 "**位置**" 选项卡以查看和设置 SDK 位置：

[首选项中的![示例位置选项卡](android-sdk-location-images/mac/01-locations-sml.png)](android-sdk-location-images/mac/01-locations.png#lightbox)

每个路径的默认位置如下所示：

- Android SDK 位置： 

    **~/Library/Developer/Xamarin/android-sdk-macosx**

- Android NDK 位置： 

    **~/Library/Developer/Xamarin/android-ndk/android-ndk-r14b**

- Java SDK （JDK）位置： 

    **/usr**

请注意，NDK 的版本号可能会有所不同。 例如，它可以是较早的版本，如**android-r10e**，而不是**r14b**。

若要设置 Android SDK 位置，请在 " **Android SDK 位置**" 框中输入 Android SDK 目录的完整路径。 您可以在查找器中选择 "Android SDK" 文件夹 **，按&#8984;CTRL + + I**以查看文件夹信息，单击并拖动**Where：** ，copy，然后将其粘贴到 "**位置**" 选项卡的 " **Android SDK 位置**" 框中。例如，如果 Android SDK 位置位于 **~/Library/Developer/Android/Sdk**，请在 " **Android SDK 位置**" 框中清除旧路径，粘贴到此路径中，然后单击 **"确定"** 。

-----
