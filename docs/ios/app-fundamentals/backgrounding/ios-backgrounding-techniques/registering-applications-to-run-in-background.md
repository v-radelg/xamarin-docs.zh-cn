---
title: 注册 Xamarin iOS 应用程序以在后台运行
description: 本文档介绍如何注册 Xamarin iOS 应用程序以在后台运行。 它讨论了音频应用、VoIP 应用、外部附件和蓝牙等。
ms.prod: xamarin
ms.assetid: 8F89BE63-DDB5-4740-A69D-F60AEB21150D
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 67cc2028276b6e1415c88cb8957e2cd567fa5bae
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69521277"
---
# <a name="registering-xamarinios-apps-to-run-in-the-background"></a>注册 Xamarin iOS 应用程序以在后台运行

为后台特权注册单独的任务适用于某些应用程序, 但如果经常调用应用程序执行重要的长时间运行的任务 (例如, 通过 GPS 获取用户指导), 会发生什么情况呢？ 此类应用程序应改为注册为已知的后台必需的应用程序。

向 iOS 注册应用程序信号, 应为应用程序提供在后台执行任务所需的特殊权限。

## <a name="application-registration-categories"></a>应用程序注册类别

已注册的应用可以分为多个类别:

- **音频**-音乐播放器和其他使用音频内容的应用程序可能会被注册为继续播放音频, 即使应用程序不再处于前台。 如果此类别中的应用在后台执行除播放音频或下载操作以外的任何操作, 则 iOS 将终止。
- **VoIP** -通过 Internet 协议 (VoIP) 应用程序获得的权限与向音频应用程序授予的权限相同, 以便在后台处理音频。 还可以根据需要对其进行响应, 以使其连接保持活动状态。
- **外部附件和蓝牙**-保留, 适用于需要与蓝牙设备和其他外部硬件附件通信的应用程序, 在这些类别下注册可使应用程序保持连接到硬件。
- **Newsstand** -Newsstand 应用程序可以继续在后台同步内容。
- **位置**-使用 GPS 或网络位置数据的应用程序可以在后台发送和接收位置更新。
- **提取 (iOS 7 +)** -注册为后台提取权限的应用程序可以定期检查提供程序的新内容, 并在用户返回到应用程序时向其提供更新的内容。
- **远程通知 (iOS 7 +)** -应用程序可以注册以从提供程序接收通知, 并在用户打开应用程序之前使用通知启动更新。 通知可以是推送通知的形式, 也可以选择以无提示方式唤醒应用程序。


可以通过在应用程序的*info.plist*中设置**所需的后台模式**属性来注册应用程序。 应用程序可以根据需要注册任意多个类别:

 [![](registering-applications-to-run-in-background-images/bgmodes.png "设置背景模式")](registering-applications-to-run-in-background-images/bgmodes.png#lightbox)

有关为后台位置更新注册应用程序的分步指南, 请参阅[后台位置演练](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/location-walkthrough.md)。

## <a name="application-does-not-run-in-background-property"></a>应用程序未在后台属性中运行

可以在*info.plist*中设置的另一个属性是*应用程序不会在后台运行*, 也`UIApplicationExitsOnSuspend`不能在属性中运行:

 [![](registering-applications-to-run-in-background-images/plist.png "禁用后台运行")](registering-applications-to-run-in-background-images/plist.png#lightbox)

这与在 iOS 7 + 中将后台应用刷新设置设置为 off 完全相同, 只是它只能在开发人员端进行更改, 适用于 iOS 4 及更高版本。 在进入后台后, 应用程序将立即挂起, 并且将无法进行任何处理。

如果你的应用程序设计为不处理后台处理, 则使用此属性, 因为它有助于避免意外的行为。

## <a name="background-fetch-and-remote-notifications"></a>后台提取和远程通知

后台提取和远程通知是 iOS 7 中引入的特殊注册类别。 这些类别允许应用程序从提供程序接收新内容, 并在后台更新。 下一节更详细地探讨了提取和远程通知, 并介绍了在 iOS 6 上的后台更新应用程序的位置识别。
