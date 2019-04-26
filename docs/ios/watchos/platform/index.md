---
title: watchOS 平台功能
description: 本文档所链接到各种指南描述 watchOS 平台功能，如 Apple Pay、 通知、 复杂性、 主动建议、 测验应用和的详细信息。
ms.prod: xamarin
ms.assetid: 13F23E01-BAED-43EB-A70E-3B30EF53D379
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 10/05/2018
ms.openlocfilehash: 09200ba5968838edf829b30a50a8ad0f4a3ab3aa
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61224555"
---
# <a name="watchos-platform-features"></a>watchOS 平台功能

## <a name="introduction-to-watchos-5introduction-to-watchos5indexmd"></a>[watchOS 5 简介](introduction-to-watchos5/index.md)

本文档提供了构建使用 Xamarin 的 watchOS 应用程序时是可供使用的 watchOS 5 中的新增和更新功能的高级概述。

## <a name="introduction-to-watchos-4introduction-to-watchos4md"></a>[watchOS 4 简介](introduction-to-watchos4.md)

本文档提供功能添加并更新 watchOS 4 中的高级的概述。

## <a name="introduction-to-watchos-3introduction-to-watchos3indexmd"></a>[watchOS 3 简介](introduction-to-watchos3/index.md)

本指南介绍了新的和已更新 watchOS 3 中的 Api。

## <a name="apple-pay-enhancementsioswatchosplatformapple-paymd"></a>[Apple Pay 增强功能](~/ios/watchos/platform/apple-pay.md)

在 watchOS 3，PassKit 框架已扩展为允许安全应用程序内付款 （的实体商品和服务） 在 Apple Watch 上运行的应用程序的支持。

## <a name="background-tasksioswatchosplatformbackground-tasksmd"></a>[后台任务](~/ios/watchos/platform/background-tasks.md)

watchOS 3 引入了多个应用程序可用于更新用户需要打开它之前确保它具有内容其信息的后台任务。

## <a name="notificationsnotificationsmd"></a>[通知](notifications.md)

了解如何提供自定义监视应用程序中处理的通知。

## <a name="complicationscomplicationsmd"></a>[复杂情况](complications.md)

添加复杂支持 watch 表盘上显示最新的数据。

## <a name="proactive-suggestionsioswatchosplatformproactive-suggestionsmd"></a>[主动建议](~/ios/watchos/platform/proactive-suggestions.md)

watchOS 3 允许此应用主动向内的用户显示信息提供上下文。 若要支持此功能， [NSUserActivity](https://developer.apple.com/reference/foundation/nsuseractivity)现在包括`MapItem`使应用可以通过其他应用提供位置信息以供将来使用的属性。

## <a name="quick-interaction-techniquesioswatchosplatformquick-interaction-techniquesmd"></a>[快速交互技术](~/ios/watchos/platform/quick-interaction-techniques.md)

提供快速的用户交互对创建引人注目的 Apple Watch 应用程序和复杂性问题至关重要。 新 watchOS 3，到 Apple 添加了对手势识别器，访问数字 Crown 和新的用户通知和导航技术支持。 此操作，同时还添加了支持 SceneKit 和 SpriteKit，允许开发人员能够轻松创建丰富、 glanceable 界面快速且响应迅速。

## <a name="workout-app-enhancementsioswatchosplatformworkout-appsmd"></a>[测验应用增强功能](~/ios/watchos/platform/workout-apps.md)

新 watchOS 3，与健身相关应用程序能够在 Apple Watch 上在后台运行。 若要启用此功能 （和获取对 HealthKit 数据访问），该应用程序必须包括`WKBackgroundModes`中的键`Info.plist`文件中的使用值`workout-processing`。
