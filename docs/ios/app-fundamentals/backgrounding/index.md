---
title: Xamarin.iOS 中的后台处理
description: 后台处理或后台处理是让应用程序在前台运行另一个应用程序时在后台执行任务的过程。 本指南可作为后台处理在 iOS 中的简介。
ms.prod: xamarin
ms.assetid: F377440C-C5D9-4267-85D8-2C816E3A0300
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/05/2018
ms.openlocfilehash: a4f5112b6e77ab6e00453c19c766d1e905df1144
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60946652"
---
# <a name="backgrounding-in-xamarinios"></a>Xamarin.iOS 中的后台处理

_后台处理或后台处理是让应用程序在前台运行另一个应用程序时在后台执行任务的过程。本指南可作为后台处理在 iOS 中的简介。_

移动应用程序中的后台处理是从根本上不同于传统的多任务在桌面上概念。 台式计算机有多种可用于应用程序，包括屏幕空间、 能力和内存资源。 应用程序都能够运行的同时保持高性能且可用。 在移动设备上，资源是很多的限制。 很难在小屏幕上显示多个应用程序和全速运行多个应用程序会耗尽电池。 后台处理是常量考虑为应用程序提供运行后台任务以及执行所需的资源并保持 foregrounded 应用程序和设备响应。 IOS 和 Android 可以后台处理，但它们非常不同的方式处理它。

在 iOS 中，后台处理被识别为应用程序状态，并应用将移入和移出具体行为取决于应用程序和用户的后台状态。 iOS 还提供了编写应用程序的多个选项在后台，包括请求完成一项重要任务，作为一种已知背景需要应用程序运行时间的 OS 中运行和刷新应用程序的内容指定时间间隔。

在本指南和随附的演练，我们将了解如何在后台执行应用程序任务。 我们将介绍主要概念和最佳实践，并且然后单步执行创建一个实际应用，在后台接收位置更新。

## <a name="contents"></a>内容

1.  [iOS 中的后台处理简介](~/ios/app-fundamentals/backgrounding/introduction-to-backgrounding-in-ios.md)
1.  [应用程序生命周期演示](~/ios/app-fundamentals/backgrounding/application-lifecycle-demo.md)
1.  [iOS 后台处理技术](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/index.md)
1.  [演练：iOS 中的后台处理](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/index.md)
1.  [iOS 后台处理指南](~/ios/app-fundamentals/backgrounding/ios-backgrounding-guidance.md)

## <a name="summary"></a>总结

在本指南中，我们引入了执行后台处理在 iOS 中的不同方法。 我们介绍 iOS 应用程序状态，并且检查后台处理 iOS 应用程序生命周期中的扮演的角色。 此外，我们了解到如何我们无法将注册的各个任务或整个应用程序在 iOS 中在后台中运行。 最后，我们进一步巩固了我们对后台处理在 iOS 上，通过构建在后台中执行更新的应用程序的了解。



## <a name="related-links"></a>相关链接

- [在 Android 上后台处理](~/android/app-fundamentals/services/index.md)
- [LifecycleDemo （示例）](https://developer.xamarin.com/samples/monotouch/LifecycleDemo/)
- [位置 （示例）](https://developer.xamarin.com/samples/monotouch/Location/)
- [简单后台传输 （示例）](https://developer.xamarin.com/samples/monotouch/SimpleBackgroundTransfer/)
- [iOS 后台执行](https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/BackgroundExecution/BackgroundExecution.html)
