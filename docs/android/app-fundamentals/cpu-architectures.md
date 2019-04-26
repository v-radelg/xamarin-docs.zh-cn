---
title: CPU 体系结构
description: Xamarin.Android 支持多个 CPU 体系结构，包括 32 位和 64 位设备。 本文介绍如何以应用到一个或多个支持 Android 的 CPU 体系结构为目标。
ms.prod: xamarin
ms.assetid: D4BC889D-9164-49BB-9B7B-F6C4E4E109F1
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: f2865858552d4445dff95c85767c41849c19cc29
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61018245"
---
# <a name="cpu-architectures"></a>CPU 体系结构

_Xamarin.Android 支持多个 CPU 体系结构，包括 32 位和 64 位设备。本文介绍如何以应用到一个或多个支持 Android 的 CPU 体系结构为目标。_

## <a name="cpu-architectures-overview"></a>CPU 体系结构概述

当你准备你的应用版本时，必须指定哪些平台 CPU 体系结构应用支持。 单个 APK 可包含计算机代码，以支持多个不同的体系结构。 特定于体系结构的代码的每个集合相关联*应用程序二进制接口*(ABI)。 每个 ABI 定义应如何与 Android 进行交互，在运行时此机器代码。
有关此方面的详细信息，请参阅[多核设备&amp;Xamarin.Android](~/android/deploy-test/multicore-devices.md)。


## <a name="how-to-specify-supported-architectures"></a>如何指定支持的体系结构

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

通常情况下，显式选择一种体系结构 （或体系结构） 如果您的应用程序配置为**版本**。 如果您的应用程序配置为**调试**，则**使用共享运行时**并**使用快速部署**选项处于启用状态，其中禁用选择显式体系结构。

在 Visual Studio 中，右键单击您的项目**解决方案资源管理器**，然后选择**属性**。 下**Android 选项**页上，确保**打包属性**部分并验证**使用共享运行时**已禁用 （关闭此选项，您可以显式选择哪些 Abi 支持）。 单击**高级**按钮并在**支持的体系结构**，检查你想要支持的体系结构：

[![选择 armeabi 和 armeabi-v7a 同时](cpu-architectures-images/vs/01-abi-selections-sml.png)](cpu-architectures-images/vs/01-abi-selections.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

通常情况下，显式选择一种体系结构 （或体系结构） 如果您的应用程序配置为**版本**。 如果您的应用程序配置为**调试**，则**使用共享 Mono 运行时**并**快速程序集部署**选项处于启用状态，这阻止显式体系结构所选内容。

在 Visual Studio for Mac 中，找到您的项目**解决方案**填充，单击你的项目旁的齿轮图标并选择**选项**。 在中**项目选项**对话框中，单击**Android 生成**。 单击**常规**选项卡，确认**使用共享 Mono 运行时**已禁用 （关闭此选项允许您明确选择哪些 Abi 支持）。 单击**高级**选项卡并在**支持的 Abi**，检查你想要支持的体系结构的 Abi:

[![选择 armeabi 和 armeabi-v7a 同时](cpu-architectures-images/xs/01-abi-selections-sml.png)](cpu-architectures-images/xs/01-abi-selections.png#lightbox)

-----


Xamarin.Android 支持以下体系结构：

-   **armeabi** &ndash;至少支持 ARMv5TE 指令集的基于 ARM 的 Cpu。 请注意，`armeabi`不是线程安全的不应在多 CPU 的设备上使用。

-   **armeabi-v7a** &ndash;基于 ARM 的 Cpu 使用硬件浮点操作和多个 CPU (SMP) 设备。 请注意，`armeabi-v7a`计算机代码不会在 ARMv5 设备上运行。

-   **arm64-v8a** &ndash;基于 64 位 ARMv8 体系结构的 Cpu。

-   **x86** &ndash;支持 x86 （或 IA-32） 的 Cpu 指令集。 此指令集是等效的 Pentium Pro，包括 MMX、 SSE、 SSE2 和 SSE3 指令。

-   **x86_64**支持的 64 位 x86 的 Cpu (也称为*x64*并*AMD64*) 指令集。

Xamarin.Android 将默认为`armeabi-v7a`有关**发行**生成。 此设置可以提供性能明显优于`armeabi`。 如果你面向的 64 位 ARM 平台 （如 Nexus 9 中)，选择`arm64-v8a`。 如果你将应用部署到 x86 设备，选择`x86`。 如果面向 x86 设备使用 64 位 CPU 体系结构，选择`x86_64`。

## <a name="targeting-multiple-platforms"></a>面向多个平台

若要针对多个 CPU 体系结构，可以选择多个 ABI （但代价是 APK 文件较大）。 可以使用**生成一个包 (.apk) 每个选定 ABI**选项 (中所述[设置打包属性](~/android/deploy-test/release-prep/index.md#Set_Packaging_Properties)) 以创建每个单独的 APK 支持的体系结构。

无需选择**arm64-v8a**或**x86_64**面向 64 位设备; 64 位支持不需要 64 位硬件上运行你的应用。 例如，64 位 ARM 设备 (如[Nexus 9](http://www.google.com/nexus/9/)) 可以运行应用程序配置为`armeabi-v7a`。 启用 64 位支持的主要优点是使你的应用以解决更多的内存。

> [!NOTE]
> 64 位运行时支持目前试验性功能。 请记住，64 位运行时*不*64 位设备上运行您的应用程序所必需。 

## <a name="additional-information"></a>其他信息

在某些情况下，您可能需要创建单独的 APK 的每个体系结构 (可减小 APK，或者是因为你的应用已共享库的特定于特定的 CPU 体系结构)。
有关此方法的详细信息，请参阅[生成特定于 ABI 的 Apk](~/android/deploy-test/building-apps/abi-specific-apks.md)。
