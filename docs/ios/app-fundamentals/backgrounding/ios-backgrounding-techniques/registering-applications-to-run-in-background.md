---
title: 注册以在后台运行的 xamarin ios 应用
description: 本文档介绍如何注册一个 Xamarin.iOS 应用程序，以在后台运行。 它讨论音频应用、 VoIP 应用、 外部附件和蓝牙、 和的详细信息。
ms.prod: xamarin
ms.assetid: 8F89BE63-DDB5-4740-A69D-F60AEB21150D
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: a0a66571d0249ef6fd65ff382f14c38f48a8af37
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61393687"
---
# <a name="registering-xamarinios-apps-to-run-in-the-background"></a>注册以在后台运行的 xamarin ios 应用

注册单个任务的后台权限适用于某些应用程序，但如果应用程序不断地调用时，若要执行重要的是，长时间运行的任务，例如 GPS 通过为用户的方向会发生什么情况？ 这些应用程序应改为注册为已知的背景需要应用程序。

注册应用向发出信号，iOS 应用程序应提供在后台执行任务所需的特殊权限。

## <a name="application-registration-categories"></a>应用程序注册类别

已注册的应用程序可以划分为多个类别：

-  **音频**-音乐播放机和使用的音频内容的其他应用程序注册以继续播放音频，即使该应用程序时也不能再在前景中。 如果此类别中的应用尝试执行任何操作播放音频或在后台下载以外，iOS 将终止它。
-  **VoIP** -语音转移，Internet 协议 (VoIP) 应用程序获得的相同的权限授予到音频的应用程序以继续处理在后台中的音频。 它们还可以根据需要响应时，若要使其连接保持活动状态的 VoIP 服务。
-  **外部附件和蓝牙**-保留给需要与蓝牙设备和其他外部硬件附件进行通信的应用程序，注册这些类别下的允许此应用保持连接到的硬件。
-  **Newsstand** -Newsstand 应用程序可以继续在后台的内容同步。
-  **位置**-使的应用程序使用的 GPS 或网络位置数据可以发送和接收位置更新在后台。
-  **提取 (iOS 7 +)** -的背景提取特权可以按固定间隔，向用户显示更新的内容时它们返回到应用程序检查新内容的提供程序注册的应用程序。
-  **远程通知 (iOS 7 +)** -应用程序可以注册以接收通知，从提供程序，并使用通知以开始更新之前在用户打开应用程序。 通知可以采用以下形式的推送通知，或选择以无提示方式唤醒应用程序。


可以通过设置注册应用程序**需要后台模式**在应用程序的属性*Info.plist*。 应用程序可以在多个类别，因为它需要注册：

 [![](registering-applications-to-run-in-background-images/bgmodes.png "设置后台模式")](registering-applications-to-run-in-background-images/bgmodes.png#lightbox)

注册用于后台位置更新的应用程序的分步指南，请参阅[后台位置演练](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/location-walkthrough.md)。

## <a name="application-does-not-run-in-background-property"></a>应用程序不会在后台属性

可以设置中的另一个属性*Info.plist*是*程序不会在后台*，或`UIApplicationExitsOnSuspend`属性：

 [![](registering-applications-to-run-in-background-images/plist.png "禁用后台运行")](registering-applications-to-run-in-background-images/plist.png#lightbox)

这具有完全相同的效果与设置为 off 在 iOS 7 + 中，但只能在开发人员方面，更改的后台应用程序刷新设置，并适用于 iOS 4 及更高版本。 应用程序将进入后台后立即挂起，并且将不能执行任何处理。

如果你的应用程序未设计为处理后台处理，因为这有助于避免发生意外的行为，请使用此属性。

## <a name="background-fetch-and-remote-notifications"></a>背景提取和远程通知

后台获取和远程通知是在 iOS 7 中引入的特殊的注册类别。 这些类别允许应用程序从提供程序，接收新内容，并在后台更新。 下一节探讨提取和更详细地远程通知，并还引入了位置感知如意味着更新 iOS 6 上的背景中的应用程序。
