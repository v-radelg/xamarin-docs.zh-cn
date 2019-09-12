---
title: 使用 Instruments 分析 Xamarin.iOS 应用程序
description: 本文档介绍了如何使用 Apple 的 Instruments 应用分析设备或模拟器上安装的 Xamarin.iOS 应用程序。
ms.prod: xamarin
ms.assetid: 70A8CAC8-20C2-655B-37C3-ACF9EA7874D8
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/19/2017
ms.openlocfilehash: 866ebd2a66557cda5372c5bc3d7a909e18c762d7
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70756092"
---
# <a name="profiling-xamarinios-applications-with-instruments"></a>使用 Instruments 分析 Xamarin.iOS 应用程序

Xcode Instruments 是一种工具，可用于分析设备或模拟器中的 Xamarin.iOS 应用  。 Mono 使用其实时模型编译代码，并且 Instruments 也不会解读此类数据，因此难以处理对基于模拟器的应用程序使用 Instruments生成的输出。
鉴于此，本指南将集中讨论如何使用开发人员应用解读此文档中的 Instruments 输出。

## <a name="requirements"></a>要求

Xcode Instruments 只能在 Mac 上运行。

## <a name="opening-the-instruments-app"></a>打开 Instruments 应用

选择设备，然后运行 Instruments 应用：

1. 在 Visual Studio for Mac 中打开 Xamarin.iOS 项目。
2. 选择“调试|iPhone”  配置。
3. 将 iOS 设备连接到计算机。
4. 在“运行”  菜单中，选择“上传到设备”  。 现在应用程序将生成并上传到该设备。
5. 在“工具”  菜单中，选择“启动 Instruments”  。

此时将打开 Instruments 并显示以下对话：

 [![](using-instruments-to-detect-native-leaks-using-markheap-images/instruments1.png "选择分析模板")](using-instruments-to-detect-native-leaks-using-markheap-images/instruments1.png#lightbox)

单击以选择“分配”  模板。 其他模板也有效，但本文仅讨论**分配**分析模板。

接下来，使用窗口顶部的菜单选择设备和应用程序：

[![](using-instruments-to-detect-native-leaks-using-markheap-images/instruments2.png "选择设备和应用程序")](using-instruments-to-detect-native-leaks-using-markheap-images/instruments2.png#lightbox)

在窗口顶部的菜单中选择 iOS 设备，并且应该在其旁边（上面屏幕截图中的 **MemoryDemo**）选择要分析的应用程序。

如果菜单中未列出该设备，请在 Visual Studio for Mac 中查看“控制台”  是否有错误消息，当向设备部署该应用时，可能会显示这些错误消息。 另外，请确保已针对部署通过 Xcode 管理器设置设备。

单击“选择”  按钮，随即将显示下一个屏幕：

[![](using-instruments-to-detect-native-leaks-using-markheap-images/instruments3.png "分析接口")](using-instruments-to-detect-native-leaks-using-markheap-images/instruments3.png#lightbox)

单击记录按钮（左上角的红色圆圈）以开始分析。

以下屏幕截图是使用 **Instruments** 进行分析的示例：

[![](using-instruments-to-detect-native-leaks-using-markheap-images/instruments4.png "使用 Instruments 进行分析的示例")](using-instruments-to-detect-native-leaks-using-markheap-images/instruments4.png#lightbox)

## <a name="summary"></a>总结

本指南介绍了如何启动 Xcode Instruments，以在 Visual Studio for Mac 中监视 iOS 应用。 请转到 [Instruments 演练](~/ios/deploy-test/walkthrough-apples-instrument.md)了解如何使用 Instruments 诊断内存问题的示例。

## <a name="related-links"></a>相关链接

- [Instruments 演练](~/ios/deploy-test/walkthrough-apples-instrument.md)
- [Xamarin.iOS 垃圾回收（博客文章）](http://c-sharx.net/2015-04-27-xamarin-ios-the-garbage-collector-and-me/)
