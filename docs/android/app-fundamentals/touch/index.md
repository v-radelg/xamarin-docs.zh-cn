---
title: 触控和手势在 Xamarin.Android 中
description: 在许多现有的设备上的触摸屏允许用户快速高效地与设备交互自然和直观的方式。 这种交互不只限于简单触摸检测-可以使用手势以及。 例如，捏合缩放手势是一个非常常见的示例通过收缩手势用户可以放大或缩小的两根手指与屏幕的一部分。本指南检查触摸屏输入，并在 Android 中的笔势。
ms.prod: xamarin
ms.assetid: 61874769-978A-4562-9B2A-7FFD45F58B38
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 7f957c9ff5a0e7c3a0821978703860ed2f723a92
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61013110"
---
# <a name="touch-and-gestures-in-xamarinandroid"></a>触控和手势在 Xamarin.Android 中

_在许多现有的设备上的触摸屏允许用户快速高效地与设备交互自然和直观的方式。这种交互不只限于简单触摸检测-可以使用手势以及。例如，捏合缩放手势是一个非常常见的示例通过收缩手势用户可以放大或缩小的两根手指与屏幕的一部分。本指南检查触摸屏输入，并在 Android 中的笔势。_

## <a name="touch-overview"></a>触控概述

iOS 和 Android 是在它们处理触控的方式类似。 都可以支持多点触控的很多的联系点在屏幕上的和复杂的笔势。 本指南介绍一些概念中的相似之处以及实现触摸的 particularities，并在这两个平台上笔势。

Android 使用`MotionEvent`对象封装触控数据和要侦听的收尾工作了的视图对象上的方法。

除了捕获触摸屏输入数据，iOS 和 Android 提供种解释模式的到手势收尾工作了。 这些笔势识别器又用于解释特定于应用程序的命令，例如图像的旋转或页面的一个轮次。 Android 提供了少量的受支持的手势，以及资源，以使添加简单的复杂自定义笔势。

无论您在 Android 或 iOS 上使用，触摸和手势识别器之间的选择，可以是一个令人困惑。 本指南建议，一般情况下，首选项应授予给手势识别器。 笔势识别器都实现为离散类，它们提供更大关注点分离和更好的封装。 这样可以方便地共享尽量减少编写的代码量的不同视图之间的逻辑。

本指南遵循类似的格式为每个操作系统： 首先，该平台的触控 Api 会引入并解释了，因为它们是哪些触摸交互的基础。 然后，我们深入探讨的手势识别器 – 世界上首次通过浏览一些常见的手势，并创建自定义笔势中的进行应用程序正在完成。 最后，您将了解如何跟踪各手指使用低级别触控跟踪创建 finger-paint 程序。

## <a name="sections"></a>部分

-  [Android 中的触控](~/android/app-fundamentals/touch/android-touch-walkthrough.md)
-  [演练：在 Android 中使用触控](~/android/app-fundamentals/touch/android-touch-walkthrough.md)
-  [多点触控跟踪](touch-tracking.md)

## <a name="summary"></a>总结

在本指南中，我们将探讨在 Android 中的触控。 对于这两个操作系统，我们了解了如何启用触摸以及如何响应触摸事件。 接下来，我们了解到有关手势和一些手势识别器，Android 和 iOS 提供能够处理一些较为常见的情景。 介绍了如何创建自定义笔势和应用程序中实现它们。 演练演示的概念和 Api，用于在操作中，每个操作系统，您还了解到如何跟踪各手指。



## <a name="related-links"></a>相关链接

- [Android 接触启动 （示例）](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/Touch_start)
- [Android 接触最终 （示例）](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/Touch_final)
- [FingerPaint （示例）](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/FingerPaint)
