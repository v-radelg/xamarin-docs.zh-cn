---
title: iOS 后台处理技术
description: 本文档链接到指南描述在 iOS 中的各种 backgrounding 技术： 后台任务、 后台传输服务、 后台获取和远程通知。
ms.prod: xamarin
ms.assetid: 011A8D48-1CDC-486A-A2B0-C4946118E7A9
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 00cca0c75cc79858f6edda5d6fb954611d81161b
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61169601"
---
# <a name="ios-backgrounding-techniques"></a>iOS 后台处理技术

在以下部分中，我们将探讨以下 iOS 功能以及现有 backgrounding 选项：

-  [伺机后台任务](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/ios-backgrounding-with-tasks.md#background_tasks_in_iOS_7)-保留的电池使用寿命的机会区块中运行后台任务，当设备处于唤醒状态以进行其他处理。
-  [后台传输服务](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/ios-backgrounding-with-tasks.md#background-transfers)-可靠上载和下载文件而不考虑网络状态或文件大小。
-  [后台提取](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/updating-an-application-in-the-background.md#background_fetch)-刷新从后台系统确定的时间间隔内的应用程序。
-  [远程通知](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/updating-an-application-in-the-background.md#remote_notifications)-使用推送通知，以触发在后台中的内容更新之前在用户打开应用程序，用于通知用户或无提示更新的选项。
-  **后台 UI 更新**-准备应用程序 UI 的用户，并更新应用程序的快照，在后台中的所有内容。
