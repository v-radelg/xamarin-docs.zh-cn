---
title: iOS 中的后台处理简介
description: 本文档介绍 iOS 中的后台处理：应用程序状态、应用程序生命周期方法和后台应用刷新。
ms.prod: xamarin
ms.assetid: E214F2C7-E74E-46C7-B5BA-080B30D61250
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 07/24/2018
ms.openlocfilehash: 9ae1860d127ea87e4db830d8a9d299a66fdd0f67
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70766593"
---
# <a name="introduction-to-backgrounding-in-ios"></a>iOS 中的后台处理简介

iOS 控制后台处理，并提供以下三种实现方法：

- **注册后台任务**-如果应用程序需要完成重要任务，则在应用程序移动到后台时，它可能会要求 iOS 不要中断该任务。 例如，应用程序可能需要在用户中完成日志记录，或完成下载大文件。
- **注册为后台必需的应用程序**-应用可注册为特定类型的应用程序，该应用程序具有已知的特定后台处理要求，如*音频*、 *VoIP* 、*外部附件*、 *Newsstand*和*位置*。 如果这些应用程序正在执行已注册应用程序类型的参数中的任务，则允许这些应用程序连续后台处理特权。
- **启用后台更新**-应用程序可以通过*区域监视*或侦听*重要位置更改*来触发后台更新。 在 iOS 7 中，应用程序还可以使用*后台提取*或*远程通知*注册来更新后台内容。

## <a name="application-states-and-application-delegate-methods"></a>应用程序状态和应用程序委托方法

在深入了解 iOS 中后台处理的代码之前，需要了解后台处理如何影响 iOS 应用程序的生命周期。

IOS 应用程序生命周期是指在它们之间移动的应用程序状态和方法的集合。 应用程序根据用户的行为和应用程序的后台处理要求在状态之间转换。 下图演示了移动：

 [![](introduction-to-backgrounding-in-ios-images/applicationlifecycle-.png "应用程序状态和应用程序委托方法关系图")](introduction-to-backgrounding-in-ios-images/applicationlifecycle-.png#lightbox)

- **未在运行**-该设备上尚未启动该应用程序。
- **运行/活动**-应用程序位于屏幕上，并在前台执行代码。
- **非活动**-应用程序被传入的电话呼叫、短信或其他干扰中断。
- **Backgrounded** -应用程序将移动到后台，并继续执行后台代码。
- "**挂起**"-如果应用程序不具有在后台运行的任何代码，或者所有代码都已完成，则操作系统将*暂停*应用程序。 挂起的应用程序的进程将保持活动状态，但应用程序无法执行处于此状态的任何代码。
- **返回到未运行/终止（罕见）** -偶尔情况下，应用程序的进程被销毁，应用程序返回到 "*未运行*" 状态。 在内存不足的情况下，或者如果用户手动终止了应用程序，则会发生这种情况。

由于引入了多任务支持，iOS 极少终止空闲的应用程序，而是将其进程保持在内存中*挂起*。 保持应用程序的进程处于活动状态确保应用程序在用户下一次打开时快速启动。 这也意味着，应用程序可以从*挂起*状态自由移动回*Backgrounded*状态，而无需在系统资源上进行绘制。 iOS 7 利用新的 Api 利用此功能，使应用程序可以在设备进入睡眠状态时暂停后台任务，直接从后台更新内容而无需用户交互等。 我们将介绍[IOS 后台处理技术](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/index.md)中的新 api。

## <a name="application-lifecycle-methods"></a>应用程序生命周期方法

当应用更改状态时，iOS 将通过类中的`AppDelegate`事件方法通知应用程序：

- `OnActivated`-第一次启动应用程序时，将调用此项，每次应用程序返回到前台。 这是放置需要在每次打开应用程序时运行的代码的位置。
- `OnResignActivation`-如果用户接收到诸如文本或电话呼叫等中断，则会调用此方法并暂时禁用应用。 如果用户接受电话呼叫，应用程序将发送到背景。
- `DidEnterBackground`-当应用进入 backgrounded 状态时调用，此方法为应用程序提供了大约5秒钟的时间来准备可能的终止。 使用此时间保存用户数据和任务，并从屏幕中删除敏感信息。
- `WillEnterForeground`-当用户返回到 backgrounded 或挂起的应用程序，并将其启动到前台时`WillEnterForeground` ，会调用。 这是通过解除冻结中`DidEnterBackground`保存的任何状态来准备应用程序以获取前景的时间。  `OnActivated`此方法完成后将立即调用。
- `WillTerminate`-应用程序已关闭，其进程已销毁。 此方法仅在以下情况下调用：当设备或操作系统版本上的多任务不可用时，如果内存不足，或者用户手动终止了 backgrounded 应用程序。 请注意，终止的挂起的应用程序将`WillTerminate`不会调用。

下图说明了应用程序状态和生命周期方法如何结合在一起：

 [![](introduction-to-backgrounding-in-ios-images/image2.png "此图说明了应用程序状态和生命周期方法是如何结合在一起的")](introduction-to-backgrounding-in-ios-images/image2.png#lightbox)

## <a name="user-controls-for-backgrounding-in-ios"></a>IOS 中后台处理的用户控件

iOS 7 引入了多项功能，使用户可以更好地控制应用程序的 backgrounded 状态。 应用切换器和后台应用刷新设置都会影响应用程序生命周期。

### <a name="app-switcher"></a>应用切换器

应用切换器是 iOS 7 中引入的一项重要控制功能。 它通过双击 "**主页**" 按钮来启动，并显示进程处于活动状态的应用程序：

 [![](introduction-to-backgrounding-in-ios-images/app-switcher-.png "使用应用切换器在应用之间移动")](introduction-to-backgrounding-in-ios-images/app-switcher-.png#lightbox)

使用应用切换器，用户可以滚动浏览所有 backgrounded 和挂起的应用程序的快照。 点击应用程序会将其启动到前台。 向上轻扫会从后台删除应用程序，终止进程。 在下一部分的[IOS 应用程序生命周期演示](~/ios/app-fundamentals/backgrounding/application-lifecycle-demo.md)中，我们将详细介绍应用切换器。

> [!IMPORTANT]
> 应用切换器不会显示 backgrounded 和挂起的应用程序之间的差异。

### <a name="background-app-refresh-settings"></a>后台应用刷新设置

iOS 7 允许用户选择不使用[已注册到后台处理](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/registering-applications-to-run-in-background.md)的应用程序的后台处理，从而提高了应用程序生命周期的用户控制。 *这不会阻止应用程序运行后台任务*。

用户可以通过以下方式更改此设置：导航到 "**设置" > 常规 > 后台应用刷新**和编辑所选应用程序的后台处理特权。 如果 "后台应用刷新" 设置为 "关闭"，则应用程序将在进入后台时立即挂起，并阻止进行任何后台处理：

 [![](introduction-to-backgrounding-in-ios-images/settings-.png "后台应用刷新设置")](introduction-to-backgrounding-in-ios-images/settings-.png#lightbox)

开发人员可以通过`BackgroundRefreshStatus` API 检查后台刷新应用程序状态。 有关示例，请参阅[检查后台刷新设置食谱](https://github.com/xamarin/recipes/tree/master/Recipes/ios/multitasking/check_background_refresh_setting)。

我们介绍了 iOS 应用程序生命周期的基础知识，以及用于控制应用程序生命周期的功能。 接下来，让我们看一下 iOS 应用程序生命周期。
