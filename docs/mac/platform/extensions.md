---
title: Xamarin.Mac 扩展支持
description: 本文档介绍 Xamarin.Mac 的查找器、 共享和今天的扩展支持。 检查限制和已知的问题，链接到演练和示例应用，并提供用于处理扩展插件的提示。
ms.prod: xamarin
ms.assetid: 4148F1BE-DFA0-46B6-9FCD-425A6541F510
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 0f4d6bb042f8bc8d48b45d7148984a53e3ce3437
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61032509"
---
# <a name="xamarinmac-extension-support"></a>Xamarin.Mac 扩展支持

Xamarin.Mac 2.10 中添加了对支持多个 macOS 扩展点：

- 查找器
- 共享
- 今天

<a name="Limitations-and-Known-Issues" />

## <a name="limitations-and-known-issues"></a>限制和已知的问题

以下限制和了解开发在 Xamarin.Mac 中的扩展时可能出现的问题：

* 当前没有调试支持在 Visual Studio for mac。 所有调试将需要通过完成**NSLog**并**控制台**。 请参阅下面的提示部分以了解详细信息。
* 扩展必须包含在主机应用程序中，此操作时运行一次与向系统注册。 然后必须在启用它们**扩展**一部分**系统首选项**。 
* 某些扩展崩溃可能会破坏主机应用程序的稳定性和导致奇怪的行为。 具体而言， **Finder**并**今天**部分**通知中心**可能变成"卡纸"而变得无响应。 这已在扩展项目在 Xcode 中，方面经验丰富，似乎与 Xamarin.Mac 无关。 这通常可以看到系统日志中 (通过**控制台**，请参阅详细信息的提示) 打印重复的错误消息。 重新启动 macOS 将显示要解决此问题。

<a name="Tips" />

## <a name="tips"></a>提示

使用扩展在 Xamarin.Mac 中时，以下提示可以提供帮助：

- Xamarin.Mac 目前不支持调试扩展，如调试体验将主要取决于执行和`printf`等语句。 但是，扩展的进程中运行沙盒，因此`Console.WriteLine`其他 Xamarin.Mac 应用程序中一样将不执行操作。 调用[`NSLog`直接](https://gist.github.com/chamons/e2e409013a449cfbe1f2fbe5547f6554)将调试消息输出到系统日志。
- 任何未捕获的异常将崩溃扩展过程中，只有少量的有用信息中提供**系统日志**。 中的有问题代码包装`try/catch`(Exception) 块`NSLog`的之前重新引发可能会很有用。
- **系统日志**可以从访问**控制台**待应用程序**应用程序** > **实用程序**:

    [![](extensions-images/extension02.png "系统日志")](extensions-images/extension02.png#lightbox)
- 如上文所述，运行扩展主机应用程序将其注册系统。 删除与应用程序捆绑包将其注销。 
- 如果已注册"孤立"应用程序的扩展版本，使用以下命令 （因此也可删除） 找到它们： `plugin kit -mv`


<a name="Walkthrough-and-Sample-App" />

## <a name="walkthrough-and-sample-app"></a>演练和示例应用

由于开发人员将创建和处理 Xamarin.Mac 扩展作为 Xamarin.iOS 的扩展相同的方式，请参阅我们[扩展插件简介](~/ios/platform/extensions.md)文档了解详细信息。

包含小型示例 Xamarin.Mac 项目中，每个扩展类型的工作示例可以找到[此处](https://developer.xamarin.com/samples/mac/ExtensionSamples/)。

<a name="Summary" />

## <a name="summary"></a>总结

本文已采取快速了解如何使用 Xamarin.Mac 版本 2.10 （和更高版本） 应用程序中的扩展。

## <a name="related-links"></a>相关链接

- [了解 Mac](~/mac/get-started/hello-mac.md)
- [ExtensionSamples](https://developer.xamarin.com/samples/mac/ExtensionSamples/)
- [macOS 人机界面指南](https://developer.apple.com/design/human-interface-guidelines/macos/overview/themes/)
