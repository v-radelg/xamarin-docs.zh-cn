---
title: watchOS 平台功能
description: 本文档链接到各种指南，这些指南介绍了 watchOS 平台功能，如 Apple Pay、通知、复杂性、前瞻性建议、健身应用等。
ms.prod: xamarin
ms.assetid: 13F23E01-BAED-43EB-A70E-3B30EF53D379
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 10/05/2018
ms.openlocfilehash: e571132b5f1e30bececb8302f2dacfcd908ad42e
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028297"
---
# <a name="watchos-platform-features"></a>watchOS 平台功能

## <a name="introduction-to-watchos-5introduction-to-watchos5indexmd"></a>[watchOS 5 简介](introduction-to-watchos5/index.md)

本文档简要概述了 watchOS 5 中的新增功能和更新功能，这些功能可在使用 Xamarin 生成 watchOS 应用时使用。

## <a name="introduction-to-watchos-4introduction-to-watchos4md"></a>[watchOS 4 简介](introduction-to-watchos4.md)

本文档提供了在 watchOS 4 中添加和更新的功能的高级概述。

## <a name="introduction-to-watchos-3introduction-to-watchos3indexmd"></a>[watchOS 3 简介](introduction-to-watchos3/index.md)

本文介绍了 watchOS 3 中的新 Api 和更新的 Api。

## <a name="apple-pay-enhancementsioswatchosplatformapple-paymd"></a>[Apple Pay 增强功能](~/ios/watchos/platform/apple-pay.md)

在 watchOS 3 中，已扩展 PassKit 框架，以允许在 Apple Watch 上运行的应用程序支持安全、应用内付款（同时包括物理商品和服务）。

## <a name="background-tasksioswatchosplatformbackground-tasksmd"></a>[后台任务](~/ios/watchos/platform/background-tasks.md)

watchOS 3 引入了多个后台任务，应用可以使用这些任务来更新其信息，确保在打开该应用程序之前，它具有用户需要的内容。

## <a name="notificationsnotificationsmd"></a>[通知](notifications.md)

了解如何在手表应用中提供自定义通知处理。

## <a name="complicationscomplicationsmd"></a>[复杂情况](complications.md)

添加复杂的支持以在手表面上显示最新数据。

## <a name="proactive-suggestionsioswatchosplatformproactive-suggestionsmd"></a>[主动建议](~/ios/watchos/platform/proactive-suggestions.md)

watchOS 3 允许应用在给定上下文中主动向用户显示信息。 为了支持此功能， [NSUserActivity](https://developer.apple.com/reference/foundation/nsuseractivity)包含 `MapItem` 属性，该属性使应用程序能够提供位置信息供其他应用稍后使用。

## <a name="quick-interaction-techniquesioswatchosplatformquick-interaction-techniquesmd"></a>[快速交互技术](~/ios/watchos/platform/quick-interaction-techniques.md)

提供快速用户交互对于创建引人注目的 Apple Watch 应用和复杂性至关重要。 WatchOS 3 中的新增技术，Apple 添加了对手势识别器的支持、对 Digital Crown 的访问权限以及新用户通知和导航技术的支持。 这同时增加了对 SceneKit 和 SpriteKit 的支持，使开发人员能够轻松地创建功能丰富的 glanceable 接口，这些接口既快速又有响应。

## <a name="workout-app-enhancementsioswatchosplatformworkout-appsmd"></a>[健身应用增强功能](~/ios/watchos/platform/workout-apps.md)

新到 watchOS 3，健身相关应用可以在 Apple Watch 的后台运行。 若要启用此功能（并获取对 HealthKit 数据的访问权限），应用必须在 `Info.plist` 文件中包含值为 `workout-processing`的 `WKBackgroundModes` 项。
