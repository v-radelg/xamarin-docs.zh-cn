---
title: iOS 后台处理技术
description: 本文档链接到介绍 iOS 中各种后台处理技术的指南：后台任务、后台传输服务、后台提取和远程通知。
ms.prod: xamarin
ms.assetid: 011A8D48-1CDC-486A-A2B0-C4946118E7A9
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: a81d6a651ee980b444da19341a89206afa8c4375
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73010816"
---
# <a name="ios-backgrounding-techniques"></a>iOS 后台处理技术

在以下部分中，我们将探讨现有后台处理选项的以下 iOS 功能：

- [机会后台任务](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/ios-backgrounding-with-tasks.md#background_tasks_in_iOS_7)-在设备处于唤醒状态以进行其他处理时，通过在机会块中运行后台任务来保留电池寿命。
- [后台传输服务](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/ios-backgrounding-with-tasks.md#background-transfers)-可靠地上传和下载文件，而不考虑网络状态或文件大小。
- [后台提取](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/updating-an-application-in-the-background.md#background_fetch)-在系统确定的时间间隔内从后台刷新应用程序。
- [远程通知](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/updating-an-application-in-the-background.md#remote_notifications)-在用户打开应用程序之前，使用推送通知在后台触发内容更新，并可选择通知用户或以无提示方式更新。
- **后台 UI 更新**-为用户准备应用程序 UI，并从后台更新应用程序的快照。
