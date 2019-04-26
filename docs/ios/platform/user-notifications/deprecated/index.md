---
title: 在 Xamarin.iOS 中弃用的通知技术
description: 本文档介绍 iOS 通知技术支持 iOS 10 中引入的用户通知框架已弃用。
ms.prod: xamarin
ms.assetid: 20C4F6E5-56DF-4A85-BBF0-E38C88586307
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 10/07/2016
ms.openlocfilehash: 7b0c2d99412f7a34c0e9c95a282d5d74dbe8dd78
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61085632"
---
# <a name="deprecated-notification-technologies-in-xamarinios"></a>在 Xamarin.iOS 中弃用的通知技术

本部分介绍如何实现本地和 Xamarin.iOS 中的推送通知。 它将介绍 iOS 通知的各种 UI 元素，并讨论了 API 的涉及创建并显示一条通知。

> [!IMPORTANT]
> 在本部分中的信息与 iOS 9 相关和之前，它已被保留此处以支持较旧 iOS 版本。 适用于 iOS 10 及更高版本，请参阅[用户通知框架指南](~/ios/platform/user-notifications/index.md)的 iOS 设备上支持本地和远程通知。

## <a name="sections"></a>部分

<a name="Local Notifications In iOS" />

##  <a name="local-notifications-in-ioslocal-notifications-in-iosmd"></a>[在 iOS 中的本地通知](local-notifications-in-ios.md)

本部分将讨论如何在 Xamarin.iOS 中实现本地通知。 它将介绍 iOS 通知的各种 UI 元素，并讨论了 API 的涉及创建并显示一条通知。

<a name="Local Notifications Walkthrough" />

##  <a name="walkthrough---using-local-notifications-in-xamarinioslocal-notifications-in-ios-walkthroughmd"></a>[演练 - 在 Xamarin.iOS 中使用本地通知](local-notifications-in-ios-walkthrough.md)

在本部分中我们将了解如何在 Xamarin.iOS 应用程序中使用本地通知。 它将演示创建和发布一条通知，将弹出警报时的应用接收到的基础知识。

<a name="Remote Notifications In iOS" />

##  <a name="remote-notifications-in-iosremote-notifications-in-iosmd"></a>[在 iOS 中的远程通知](remote-notifications-in-ios.md)

本部分介绍在 iOS 中的推送通知。 它引入了 Apple 推送通知网关服务 (APNS) 和它在发布通知到 iOS 应用程序中所起的作用。 它将说明如何创建启用推送通知和讨论所需的安全证书。 最后本部分将介绍一些应用程序服务器必须执行来跟踪客户端移动设备的内部管理任务。

## <a name="related-links"></a>相关链接

- [通知 （示例）](https://developer.xamarin.com/samples/monotouch/Notifications/)
