---
title: IOS 12 简介
description: 本文档提供的 Xamarin 预览版版本提供了 C# 绑定某些 iOS 12 Api 的高级说明。
ms.prod: xamarin
ms.assetid: 99EA7090-315D-493C-87D3-26AB73D9E1A9
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/08/2018
ms.openlocfilehash: 99f2b98614c2b8d558dd8744b31a62b787fc955c
ms.sourcegitcommit: a1a58afea68912c79d16a3f64de9a0c1feb2aeb4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2019
ms.locfileid: "55233830"
---
# <a name="introduction-to-ios-12"></a>IOS 12 简介

本文档提供的 Xamarin 预览版版本提供了 C# 绑定某些 iOS 12 Api 的高级说明。

若要开始构建使用 Xamarin iOS 12 应用程序，请参阅[入门指南](get-started.md)

## <a name="arkit-2arkit2md"></a>[ARKit 2](arkit2.md)

ARKit 是 iOS 中包含的增强的现实框架。 ARKit 2 允许多个用户进行相互交互在增强的现实场景中使用可以保留空间中的对象并返回到其在更高版本时，并提供 2D 图像识别和跟踪和 3D 物体识别。 iOS 12 还提供了 AR 快速查找，一种方法来呈现 usdz AR 应用程序中的模型。

## <a name="siri-shortcutssiri-shortcutsmd"></a>[使用 Siri 快捷方式](siri-shortcuts.md)

使用 Siri 快捷方式允许开发人员将其应用程序与使用 Siri 更深入地集成。 使用 Siri 快捷方式，用户可以使用语音命令来打开内容或启动后台任务，或它们可以启动这些任务通过使用 Siri 建议在锁屏界面的快捷方式。

## <a name="core-ml-2coremlmd"></a>[Core ML 2](coreml.md)

Core ML 2 可以减少应用程序通过模型量化和灵活的模型的大小、 提高了与新的批次预测 API，应用程序性能和使用自定义模型以在机器学习中支持的改进。

## <a name="notification-improvementsnotificationsindexmd"></a>[通知的改进](notifications/index.md)

在 iOS 12 中，分组的通知使其可在应用程序或线程相关分组中存在用户通知。 摘要文本提供进一步的通知组有关的信息。

在 iOS 中 12 通知内容扩展允许自定义用户界面和动态操作按钮。

## <a name="natural-language-frameworknatural-languagemd"></a>[自然语言框架](natural-language.md)

自然语言框架使应用程序可以执行各种类型的语言分析。 例如，它可以识别词类并确定所表示的文本块的语言。

## <a name="vision-framework"></a>影像框架

影像框架包括改进的面部检测器可以检测人脸的不同的方向。 此外，请求修订可以选择特定的视觉 framework 算法修订版本。

## <a name="photo-and-video-apis"></a>照片和视频 Api

在 iOS 12、 纵向分段 API 返回纵向效果遮罩 – 的线性掩码描述从纵向图在后台的前景和在创建各种图像效果非常有用。 iOS 12 还使它可以使用从 TrueDepth 照相机的深度数据进行实时视频效果。

## <a name="passwords"></a>密码

iOS 12 简化用户和开发人员可以使用的密码：

- 密码自动填充并自动强密码使其可以自动生成、 存储和 iOS 应用程序时注册并登录到应用程序中使用强密码。
- 安全代码自动填充使可能使用基于短信的身份验证代码，而无需手动剪切和粘贴或记忆。
- `ASWebAuthenticationSession`类简化了使用联合身份验证服务的过程。
- 自动填充凭据提供程序扩展使第三方密码的应用程序提供用户名和密码的登录字段。

## <a name="healthkit-updates"></a>HealthKit 更新

引入了 iOS 11.3[健康记录](https://www.apple.com/healthcare/health-records/)，它允许用户下载它们的运行状况记录来自各种医疗保健机构的信息并在其 iOS 设备上查看它。 iOS 12 添加的 Api，使第三方应用程序安全地访问此数据。

## <a name="imessage-app-presentation-contexts"></a>iMessage 应用演示文稿上下文

在 iOS 12、 iMessage 应用支持允许作为普通 iMessage 应用或照片或视频效果的上下文中运行的应用程序的演示文稿上下文。

## <a name="network-framework"></a>网络框架

基础网络框架网络堆栈`URLSession`iOS 应用程序中常用的 Api 现可作为独立框架，使其更轻松地使用 TCP、 UDP、 TLS、 IPv4/IPv6，和的详细信息。

## <a name="carplay"></a>CarPlay

在 iOS 12 中，第三方应用程序可以提供映射和启用通过打开导航说明进行操作 CarPlay 通过使用新的 CarPlay 框架。

## <a name="deprecations"></a>弃用功能

与 iOS 12、 Apple 便已弃用：

- OpenGL ES[鼓励开发人员](https://developer.apple.com/ios/whats-new/)采用裸机。
- [`UIWebView`](xref:UIKit.UIWebView)[以便获取`WKWebView` ](https://developer.apple.com/documentation/webkit/wkwebview?language=objc)。

## <a name="related-links"></a>相关链接

- [适用于 iOS 12 (Apple) 进行准备](https://developer.apple.com/ios/)
