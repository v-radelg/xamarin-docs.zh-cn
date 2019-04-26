---
title: 在 Xamarin.iOS SiriKit
description: 本文介绍如何在 Xamarin.iOS 应用程序中使用 SiriKit 提供 iOS 设备上使用 Siri 用户可访问的服务。
ms.prod: xamarin
ms.assetid: 84E5681A-F557-4967-AA99-F831169157AA
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 9f7cbb3f7d9e448947ec8163a8660616910e750f
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61423457"
---
# <a name="sirikit-in-xamarinios"></a>在 Xamarin.iOS SiriKit

_本文介绍如何在 Xamarin.iOS 应用程序中使用 SiriKit 提供 iOS 设备上使用 Siri 用户可访问的服务。_

新 ios 10、 通过 SiriKit，iOS 应用程序提供服务可供用户使用应用扩展和新的 iOS 设备上使用 Siri 和 Maps 应用访问**意向**并**Intents UI**框架。

使用 Siri 适用于这一概念**域**，组知道相关的任务的操作。 应用具有 Siri 与每个交互必须按如下所示划分为其已知的服务域之一：

- 音频或视频呼叫。
- 预订滑水感觉的时间。
- 管理锻炼。
- 消息传递。
- 正在搜索照片。
- 发送或接收付款。

当用户发出的 Siri 请求涉及应用扩展的服务之一时，SiriKit 发送该扩展**意向**对象，描述用户的请求和任何支持的数据。 然后，应用扩展生成的相应**响应**对象的给定**意向**，详细介绍如何扩展可以处理该请求。

## <a name="understanding-sirikit-conceptsiosplatformsirikitunderstanding-sirikitmd"></a>[了解 SiriKit 概念](~/ios/platform/sirikit/understanding-sirikit.md)

本文介绍如何将需要在 Xamarin.iOS 应用程序中使用 SiriKit 的关键概念。 它涵盖了新 Intents 和 Intents UI 扩展点以及如何使用应用程序和用户词汇，若要打开应用程序使用 Siri。

## <a name="implementing-sirikitiosplatformsirikitimplementing-sirikitmd"></a>[实现 SiriKit](~/ios/platform/sirikit/implementing-sirikit.md)

本文介绍如何在 Xamarin.iOS 应用程序中实现 SiriKit 支持所需的步骤。 开发人员应尝试添加到应用，作为键概念介绍将需要以确保成功实现 SiriKit 支持之前阅读了解 SiriKit 概念指南上面。





## <a name="related-links"></a>相关链接

- [ElizaChat 示例](https://developer.xamarin.com/samples/monotouch/ios10/ElizaChat/)
- [SiriKit 编程指南](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
- [意向框架引用](https://developer.apple.com/reference/intents)
- [Intents UI 框架引用](https://developer.apple.com/reference/intentsui)
