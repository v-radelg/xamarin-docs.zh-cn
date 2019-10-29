---
title: iOS 12 简介
description: 本文档提供了有关 Xamarin 预览版提供C#绑定的某些 IOS 12 api 的高级说明。
ms.prod: xamarin
ms.assetid: 99EA7090-315D-493C-87D3-26AB73D9E1A9
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 07/08/2018
ms.openlocfilehash: 39d954626bc9e789446e7f1deac67e2e0fca51c8
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032006"
---
# <a name="introduction-to-ios-12"></a>iOS 12 简介

本文档提供了有关 Xamarin 预览版提供C#绑定的某些 IOS 12 api 的高级说明。

若要开始通过 Xamarin 构建 iOS 12 应用，请参阅[入门指南](get-started.md)

## <a name="arkit-2arkit2md"></a>[ARKit 2](arkit2.md)

ARKit 是 iOS 随附的已扩充的现实框架。 在增加的现实场景中，ARKit 2 允许多个用户互相交互，使对象在空间中保持不变并稍后返回到它们，并提供2D 图像识别和跟踪以及3D 对象识别。 iOS 12 还提供了 AR 快速查找，这是在应用程序中呈现 usdz AR 模型的一种方法。

## <a name="siri-shortcutssiri-shortcutsmd"></a>[Siri 快捷方式](siri-shortcuts.md)

Siri 快捷方式允许开发人员更深入地将其应用程序与 Siri 集成。 使用 Siri 快捷方式，用户可以使用语音命令打开内容或启动后台任务，也可以通过 Siri 在锁屏上建议的快捷方式启动这些相同的任务。

## <a name="core-ml-2coremlmd"></a>[Core ML 2](coreml.md)

Core ML 2 通过模型量化和灵活的模型来减少应用程序大小，使用新的批预测 API 提高应用程序性能，并使用自定义模型来支持机器学习的进步。

## <a name="notification-improvementsnotificationsindexmd"></a>[通知改进](notifications/index.md)

在 iOS 12 中，分组通知使得可以在应用或与线程相关的分组中显示用户通知。 摘要文本提供有关通知组的详细信息。

IOS 12 中的通知内容扩展允许自定义用户界面和动态操作按钮。

## <a name="natural-language-frameworknatural-languagemd"></a>[自然语言框架](natural-language.md)

自然语言框架使应用程序可以执行各种类型的语言分析。 例如，它可以识别词性并确定文本块所代表的语言。

## <a name="vision-frameworkiosplatformintroduction-to-ios11visionmd"></a>[远景框架](~/ios/platform/introduction-to-ios11/vision.md)

远景框架包含可检测各种方向的人脸的改进面部检测器。 此外，请求修订可以选择特定的远景框架算法修订版本。

## <a name="photo-and-video-apis"></a>照片和视频 Api

在 iOS 12 中，纵向分段 API 返回纵向效果遮罩–一种线性蒙板，该蒙板从指定的背景开始，在创建各种图像效果时非常有用。 iOS 12 还可以使用 TrueDepth 照相机中的深度数据进行实时视频效果。

## <a name="passwords"></a>密码

iOS 12 使用户和开发人员可以更轻松地处理密码：

- 密码自动填充和自动强密码使你可以在注册并登录到应用程序时，在 iOS 应用程序中自动生成、存储和使用强密码。
- 安全代码自动填充使你可以使用基于短信的身份验证代码，而无需手动剪切和粘贴或记忆。
- `ASWebAuthenticationSession` 类简化了使用联合身份验证服务的过程。
- 自动填充凭据提供程序扩展使第三方密码应用程序可以提供用户名和密码以登录字段。

## <a name="healthkit-updates"></a>HealthKit 更新

iOS 11.3 引入了[运行状况记录](https://www.apple.com/healthcare/health-records/)，允许用户从各种医疗保健机构下载其健康记录信息并在其 iOS 设备上查看。 iOS 12 添加了允许第三方应用程序安全访问此数据的 Api。

## <a name="imessage-app-presentation-contexts"></a>iMessage 应用呈现上下文

在 iOS 12 中，iMessage apps 支持显示上下文，这允许应用作为普通的 iMessage 应用运行，或在照片或视频效果的上下文中运行。

## <a name="network-framework"></a>网络框架

网络框架是 iOS 应用程序中常用的 `URLSession` Api 基础的网络堆栈，现作为独立框架提供，使其更易于使用 TCP、UDP、TLS、IPv4/IPv6 等。

## <a name="carplay"></a>CarPlay

在 iOS 12 中，第三方应用可以通过使用新的 CarPlay 框架，在 CarPlay 中提供映射和打开的导航说明。

## <a name="deprecations"></a>弃用功能

对于 iOS 12，Apple 已弃用：

- OpenGL ES，[鼓励开发人员](https://developer.apple.com/ios/whats-new/)采用金。
- [`UIWebView`](xref:UIKit.UIWebView)，而不[是 `WKWebView`](https://developer.apple.com/documentation/webkit/wkwebview?language=objc)。
