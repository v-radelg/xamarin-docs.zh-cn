---
title: 处理在 Xamarin.iOS 应用程序中的触控
description: 此文档所链接到介绍如何使用触摸、 多点触控、 手势和一个 Xamarin.iOS 应用程序中的 3D Touch 的指南。
ms.prod: xamarin
ms.assetid: E3904713-6018-4755-A315-EB045DFB3500
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 01/23/2017
ms.openlocfilehash: 5aabc3a3c2ffbcffc0e12379989f7eb43b03a902
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61399286"
---
# <a name="handling-touch-in-xamarinios-apps"></a>处理在 Xamarin.iOS 应用程序中的触控

与其他移动平台，iOS 有多种方法来处理触控。 它可以支持多点触控 — 在屏幕上的联系人的多个点 — 和复杂的笔势。 本指南介绍的一些概念，以及在 ios 设备上实现触控和手势的 particularities。

iOS 中的触控数据封装`UITouch`类，该类将提供给应用程序通过一系列`UIResponder`方法。 应用程序可以重写这些方法中的子类`UIView`并`UIViewController`，这两个继承`UIResponder`。

捕获触摸屏输入数据，除了 iOS 提供种解释模式的到手势收尾工作了。 这些笔势识别器又用于解释特定于应用程序的命令，例如图像的旋转或页面的一个轮次。 iOS 提供了一系列丰富的类来处理常见手势与最小添加的代码。

触摸和手势识别器之间的选择，可以是一个令人困惑。 本指南建议，一般情况下，首选项应授予给手势识别器。 笔势识别器都实现为离散类，它们提供更大关注点分离和更好的封装。 这可以直观地共享尽量减少编写的代码量的不同视图之间的逻辑。

但是，有些的时候需要使用低级的触摸处理和甚至跟踪多个手指，例如，若要创建 finger-paint 程序。

## <a name="sections"></a>部分

-  [iOS 中的触控](touch-in-ios.md)
-  [演练：在 iOS 中使用触控](ios-touch-walkthrough.md)
-  [多点触控跟踪](touch-tracking.md)

本指南可作为在 iOS 中的触摸简介。 有关在 iOS 中使用 3D Touch 和 Haptic 反馈的详细信息，其中引入了在 iOS 9 和 10 分别，请参阅下面的特定指导原则：

* [3D Touch](~/ios/platform/3d-touch.md)
* [提供 Haptic 反馈](~/ios/user-interface/ios-ui/haptic-feedback.md)

## <a name="related-links"></a>相关链接

- [iOS 接触启动 （示例）](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/Touch_start)
- [iOS 最终接触 （示例）](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/Touch_final)
- [FingerPaint （示例）](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/FingerPaint)
