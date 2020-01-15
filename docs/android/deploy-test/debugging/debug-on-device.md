---
title: 在设备上进行调试
description: 本文介绍如何在物理 Android 设备上调试 Xamarin.Android 应用程序。
ms.prod: xamarin
ms.assetid: 153D3746-A27F-198B-48FE-D219C0133A79
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 3ca524e451a7a4eb838805c839b33c4b9dd6bddd
ms.sourcegitcommit: 5821c9709bf5e06e6126233932f94f9cf3524577
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2019
ms.locfileid: "75556530"
---
# <a name="debug-on-an-android-device"></a>在 Android 设备上调试

本文介绍如何在物理 Android 设备上调试 Xamarin.Android 应用程序。 

可在 Android 设备上使用 Visual Studio for Mac 或 Visual Studio 调试 Xamarin.Android 应用。 在设备上执行调试之前，必须[设置设备进行开发](~/android/get-started/installation/set-up-device-for-development.md)并将其连接到电脑或 Mac。

## <a name="debug-application"></a>调试应用程序

设备连接到计算机后，即可采用与其他任何 Xamarin 产品或 .NET 应用程序相同的方式调试 Xamarin.Android 应用程序。 请务必在 IDE 中选择“调试”  配置和外部设备，确保必要的调试符号可用且 IDE 可连接到运行的应用程序： 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![已选中调试配置](debug-on-device-images/image1-vs.png)

随后，在代码中设置断点：

![代码行上设置的断点](debug-on-device-images/image2-vs.png)

选择设备后，Xamarin.Android 会连接到设备、部署应用程序，然后运行该程序。 达到断点时，调试程序会停止应用程序，允许以类似于任何其他 C# 应用程序的方式调试该程序： 

![已到达断点](debug-on-device-images/image3-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![已选中调试配置](debug-on-device-images/image1-xs.png)

随后，在代码中设置断点：

![代码行上设置的断点](debug-on-device-images/image2-xs.png)

选择设备后，Xamarin.Android 会连接到设备、部署应用程序，然后运行该程序。 达到断点时，调试程序会停止应用程序，允许以类似于任何其他 C# 应用程序的方式调试该程序： 

![已到达断点](debug-on-device-images/image3-xs.png)

-----

## <a name="summary"></a>总结

本文档介绍了如何通过设置断点和选择目标设备来调试 Xamarin.Android 应用程序。

## <a name="related-links"></a>相关链接

- [设置设备进行开发](~/android/get-started/installation/set-up-device-for-development.md)
- [设置可调式属性](~/android/deploy-test/debuggable-attribute.md)
