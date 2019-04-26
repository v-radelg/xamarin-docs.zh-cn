---
title: iOS 中的后台处理简介
description: 本文档介绍了 iOS 中的后台处理： 应用程序状态、 应用程序生命周期方法和后台应用程序刷新。
ms.prod: xamarin
ms.assetid: E214F2C7-E74E-46C7-B5BA-080B30D61250
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/24/2018
ms.openlocfilehash: c533dd54e3b6b11465cfd7daf5b9a93265dbe7b7
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61169657"
---
# <a name="introduction-to-backgrounding-in-ios"></a>iOS 中的后台处理简介

iOS 来控制后台处理非常紧密，它提供三种方法来实现它：

-  **注册后台任务**-如果应用程序需要完成一项重要任务，它可以请求 iOS 不中断时该应用程序进入后台任务。 例如，应用程序可能需要完成日志记录在用户或完成下载大型文件。
-  **作为背景需要应用程序注册**-应用程序可以注册为特定类型的应用程序具有已知的后台处理的特定要求，如*音频*， *VoIP* ， *外部附件*， *Newsstand* ，并*位置*。 这些应用程序允许连续的后台处理权限，只要它们执行所注册的应用程序类型的参数内的任务。
-  **启用后台更新**-应用程序可以触发具有后台更新*区域监视*或通过侦听*重大位置更改*。 截至 iOS 7、 应用程序还可以注册以更新在背景中使用的内容*后台提取*或*远程通知*。


## <a name="application-states-and-application-delegate-methods"></a>应用程序状态和应用程序的委托方法

我们深入到后台处理在 iOS 中的代码之前，我们需要了解如何后台处理影响的 iOS 应用程序的生命周期。

IOS 应用程序生命周期是一系列应用程序状态和方法将它们之间移动。 应用程序基于用户的行为和应用程序的 backgrounding 要求的状态之间转换。 移动由以下关系图所示：

 [![](introduction-to-backgrounding-in-ios-images/applicationlifecycle-.png "应用程序状态和应用程序的委托方法的关系图")](introduction-to-backgrounding-in-ios-images/applicationlifecycle-.png#lightbox)

-  **未在运行**-应用程序尚未尚未启动设备上。
-  **运行/主动**-应用程序在屏幕上，并且在前台执行代码。
-  **非活动**-传入的电话呼叫、 文本或其他中断中断应用程序。
-  **后台运行**-应用程序将移到后台，并继续执行后台代码。
-  **已挂起**-如果应用程序不具有任何代码即可在后台运行，或如果已完成的所有代码，应用将为*Suspended*由操作系统。 挂起应用程序的进程保持活动状态，但应用程序无法在此状态下执行任何代码。
-  **返回到未运行/终止 (Rare)** -有时，销毁应用程序的进程，并在应用程序返回*未运行*状态。 在内存不足的情况下，将发生这种情况或用户手动终止应用程序。


引入了多任务处理的支持，因为 iOS 很少会终止空闲的应用程序，并改为保留其流程*Suspended*在内存中。 保持应用程序的进程处于活动状态，以确保应用程序快速启动的下次用户打开它。 这还意味着应用程序可以从自由移动*Suspended*回状态*Backgrounded*状态不会绘制在系统资源。 iOS 7 利用此功能使用新的 Api，使应用程序时在设备进入睡眠状态，而无需用户交互和的详细信息在后台从直接的更新内容暂停的后台任务。 中的新 Api，我们将介绍[iOS 后台处理技术](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/index.md)。

## <a name="application-lifecycle-methods"></a>应用程序生命周期方法

IOS 应用更改状态时, 通知应用程序通过事件方法`AppDelegate`类：

-  `OnActivated` -这称为第一次启动应用程序，以及每次应用回来置于前台。 这是放置所需每次打开应用时运行代码的位置。
-  `OnResignActivation` -如果用户将收到短信或电话呼叫如中断，调用此方法，并应用暂时停用。 用户应接受电话呼叫，应用将发送到后台。
-  `DidEnterBackground` -当应用程序进入 backgrounded 的状态时调用，此方法提供的应用程序大约 5 秒钟内准备用于可能终止。 使用这一次保存用户数据和任务，并从屏幕中删除敏感信息。
-  `WillEnterForeground` -当用户返回到后台运行或挂起的应用程序，并将其置于前台，启动`WillEnterForeground`会被调用。 这是时间来准备应用通过解除冻结期间保存任何状态拍摄前台`DidEnterBackground`。  `OnActivated` 将此方法完成后立即调用。
-  `WillTerminate` -应用程序已关闭，并销毁其进程。 如果多任务处理不在设备或操作系统版本上可用，如果内存较少，或用户手动终止 backgrounded 应用程序只获取调用此方法。 请注意，将不会调用挂起的应用程序获取终止`WillTerminate`。


下图说明了如何在应用程序状态和生命周期方法组合在一起：

 [![](introduction-to-backgrounding-in-ios-images/image2.png "此图描述了如何在应用程序状态和生命周期方法组合在一起")](introduction-to-backgrounding-in-ios-images/image2.png#lightbox)

## <a name="user-controls-for-backgrounding-in-ios"></a>在 iOS 中的后台处理的用户控件

iOS 7 引入了多种功能，可为用户提供更好地控制应用程序的 backgrounded 状态。 应用程序切换器和后台应用程序刷新设置会影响应用程序生命周期。

### <a name="app-switcher"></a>应用程序切换器

应用程序切换器是在 iOS 7 中引入的重要的控件功能。 启动通过双点击**主页**按钮，并显示其进程处于活动状态的应用程序：

 [![](introduction-to-backgrounding-in-ios-images/app-switcher-.png "使用应用切换器的应用之间移动")](introduction-to-backgrounding-in-ios-images/app-switcher-.png#lightbox)

使用应用切换器，用户可以滚动浏览所有后台运行和挂起应用程序的快照。 点击应用程序将启动置于前台。 向上轻扫从在后台，终止其进程中删除应用程序。 我们将深入研究应用切换器中[iOS 应用程序生命周期演示](~/ios/app-fundamentals/backgrounding/application-lifecycle-demo.md)下一节中。

> [!IMPORTANT]
> 应用程序切换器不显示后台运行和挂起应用程序之间的差异。



### <a name="background-app-refresh-settings"></a>后台应用程序刷新设置

iOS 7 用户可以选择不使用后台处理应用程序，从而增加用户控制应用程序生命周期[注册的后台处理](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/registering-applications-to-run-in-background.md)。 *这不会阻止应用程序运行后台任务*。

用户可以更改此设置，通过导航到<span class="uiitem">设置 > 常规 > 背景应用刷新</span>和编辑所选应用程序的 backgrounding 特权。 如果后台应用程序刷新设置为 off，将立即挂起在输入背景，并阻止侦听任何后台处理应用程序：

 [![](introduction-to-backgrounding-in-ios-images/settings-.png "后台应用程序刷新设置")](introduction-to-backgrounding-in-ios-images/settings-.png#lightbox)

开发人员可以检查与后台刷新应用程序状态`BackgroundRefreshStatus`API。 有关示例，请参阅[检查后台刷新设置方案](https://github.com/xamarin/recipes/tree/master/Recipes/ios/multitasking/check_background_refresh_setting)。

我们已经介绍了 iOS 应用程序生命周期和用于控制应用程序生命周期的功能的基础知识。 接下来，让我们看 iOS 应用程序生命周期中操作。

