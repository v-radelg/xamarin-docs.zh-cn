---
title: 在 Xamarin iOS 应用中处理触控
description: 本文档链接到介绍如何在 Xamarin iOS 应用程序中使用触控、多点触控、笔势和3D 触摸的指南。
ms.prod: xamarin
ms.assetid: E3904713-6018-4755-A315-EB045DFB3500
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 01/23/2017
ms.openlocfilehash: edf5f1c82857930a2647f306195a4c82f4338db6
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76724627"
---
# <a name="handling-touch-in-xamarinios-apps"></a>在 Xamarin iOS 应用中处理触控

与其他移动平台一样，iOS 提供多种方法来处理触摸。 它可以支持多点触控（屏幕上的多点联系）和复杂的手势。 本指南介绍了一些概念，以及在 iOS 上实现触摸和手势的 particularities。

iOS 在 `UITouch` 类中封装触控数据，可通过一系列 `UIResponder` 方法将其提供给应用程序。 应用程序可以在 `UIView` 和 `UIViewController`的子类中重写这些方法，这两种方法都继承自 `UIResponder`。

除了捕获触控数据以外，iOS 还提供了一种方法来解释对手势的接触模式。 这些手势识别器可用于解释特定于应用程序的命令，例如，旋转图像或打开页面。 iOS 提供了一系列丰富的类，可使用最少添加的代码处理常见的手势。

触摸和手势识别器之间的选择可能会令人费解。 本指南建议在一般情况下，应将首选项指定给手势识别器。 手势识别器作为离散类实现，这可提供更大的问题分离和更好的封装。 这样就可以轻松地在不同视图之间共享逻辑，从而最大程度地减少编写的代码量。

但是，有时你需要使用低级别的触摸处理，甚至跟踪多个手指，例如创建 finger 式程序。

## <a name="sections"></a>섹션

- [iOS의 터치](touch-in-ios.md)
- [연습: iOS에서 터치 사용](ios-touch-walkthrough.md)
- [멀티 터치 추적](touch-tracking.md)

本指南介绍了 iOS 中的触控。 若要深入了解如何在 ios 中使用 3D Touch 和 Haptic 反馈（在 iOS 9 和10中分别引入），请参阅下面的特定指南：

- [3D Touch](~/ios/platform/3d-touch.md)
- [햅틱 피드백 제공](~/ios/user-interface/ios-ui/haptic-feedback.md)

## <a name="related-links"></a>관련 링크

- [iOS 触摸最终（示例）](https://docs.microsoft.com/samples/xamarin/ios-samples/applicationfundamentals-touch-final)
- [FingerPaint （示例）](https://docs.microsoft.com/samples/xamarin/ios-samples/applicationfundamentals-fingerpaint)
