---
title: Xamarin Android 应用程序基础知识
description: 核心应用程序概念
ms.prod: xamarin
ms.assetid: 935B8BFE-23B7-4239-5C87-F4A503B889CB
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: eb581d68f3b7e57975b6979fe1005b1fac411ec8
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73019220"
---
# <a name="xamarinandroid-application-fundamentals"></a>Xamarin Android 应用程序基础知识

本部分介绍开发人员在开发 Android 应用程序时需要注意的一些更常见的任务或概念。

## <a name="accessibilityandroidapp-fundamentalsaccessibilitymd"></a>[辅助功能](~/android/app-fundamentals/accessibility.md)

本页介绍如何使用 Android 辅助功能 Api 根据[辅助功能清单](~/cross-platform/app-fundamentals/accessibility.md)来构建应用。

## <a name="understanding-android-api-levelsandroidapp-fundamentalsandroid-api-levelsmd"></a>[了解 Android API 级别](~/android/app-fundamentals/android-api-levels.md)

本指南介绍 Android 如何使用 API 级别跨不同版本的 Android 管理应用兼容性，并说明如何配置 Xamarin Android 项目设置以在应用中部署这些 API 级别。 此外，本指南还介绍如何编写处理不同 API 级别的运行时代码，并提供所有 Android API 级别、版本号（如 Android 8.0）、Android 代码名称（如 Oreo）和生成版本代码的引用列表。

## <a name="resources-in-androidandroidapp-fundamentalsresources-in-androidindexmd"></a>[Android 中的资源](~/android/app-fundamentals/resources-in-android/index.md)

本文介绍了 Xamarin 中 Android 资源的概念，并介绍了如何使用这些资源。 其中介绍了如何使用 Android 应用程序中的资源来支持应用程序本地化和多个设备，包括不同的屏幕大小和密度。

## <a name="activity-lifecycleandroidapp-fundamentalsactivity-lifecycleindexmd"></a>[活动生命周期](~/android/app-fundamentals/activity-lifecycle/index.md)

活动是 Android 应用程序的基本构建基块，可在许多不同的状态中存在。 活动生命周期从实例化开始，以析构结束，并在之间包含许多状态。 活动更改状态时，将调用相应的生命周期事件方法，并通知活动即将发生的状态更改，并允许它执行代码以适应该更改。 本文介绍活动的生命周期，并说明在每种状态中，活动在每种状态中所发生的责任改变为操作良好且可靠的应用程序的一部分。

## <a name="localizationandroidapp-fundamentalslocalizationmd"></a>[本地化](~/android/app-fundamentals/localization.md)

本文介绍如何通过翻译字符串和提供备用图像将 Xamarin 本地化为其他语言。

## <a name="servicesandroidapp-fundamentalsservicesindexmd"></a>[服务](~/android/app-fundamentals/services/index.md)

本文介绍 Android 服务，后者允许在后台完成工作。 它介绍了服务适用的不同方案，并说明了如何实现它们，以执行长时间运行的后台任务以及提供远程过程调用的接口。

## <a name="broadcast-receiversandroidapp-fundamentalsbroadcast-receiversmd"></a>[广播接收器](~/android/app-fundamentals/broadcast-receivers.md)

本指南介绍了如何在 Xamarin 中创建和使用广播接收器，这是一个响应系统范围的广播的 Android 组件。

## <a name="permissionsandroidapp-fundamentalspermissionsmd"></a>[权限](~/android/app-fundamentals/permissions.md)

你可以使用 Visual Studio for Mac 或 Visual Studio 中内置的工具支持来创建和添加对 Android 清单的权限。 本文档介绍如何在 Visual Studio 和 Xamarin Studio 中添加权限。

## <a name="graphics-and-animationandroidapp-fundamentalsgraphics-and-animationmd"></a>[图形和动画](~/android/app-fundamentals/graphics-and-animation.md)

Android 提供了一个非常丰富的多样框架，用于支持2D 图形和动画。 本文档介绍了这些框架，并讨论了如何创建自定义图形和动画并在 Xamarin Android 应用程序中使用它们。

## <a name="cpu-architecturesandroidapp-fundamentalscpu-architecturesmd"></a>[CPU 体系结构](~/android/app-fundamentals/cpu-architectures.md)

Xamarin 支持多个 CPU 体系结构，包括32位和64位设备。 本文介绍如何将应用定位到一个或多个支持 Android 的 CPU 体系结构。

## <a name="handling-rotationandroidapp-fundamentalshandling-rotationmd"></a>[处理旋转](~/android/app-fundamentals/handling-rotation.md)

本文介绍如何处理 Xamarin 中的设备方向更改。 其中介绍了如何使用 Android 资源系统自动为特定设备方向加载资源，以及如何以编程方式处理方向更改。 然后介绍在设备旋转时用于维护状态的技术。

## <a name="android-audioandroidapp-fundamentalsandroid-audiomd"></a>[Android 音频](~/android/app-fundamentals/android-audio.md)

Android OS 为多媒体提供了广泛的支持，包括音频和视频。 本指南重点介绍 Android 中的音频，并介绍如何使用内置的音频播放器和录像机类以及低级音频 API 播放和录制音频。 还介绍了如何使用其他应用程序广播的音频事件，使开发人员能够构建表现良好的应用程序。

## <a name="notificationsandroidapp-fundamentalsnotificationsindexmd"></a>[通知](~/android/app-fundamentals/notifications/index.md)

本部分介绍如何在 Xamarin 中实现本地和远程通知。 它介绍了 Android 通知的各种 UI 元素，并讨论了创建和显示通知所涉及的 API。 对于远程通知，会解释 Google Cloud Messaging 和 Firebase 云消息传送。 包含分步演练和代码示例。

## <a name="touchandroidapp-fundamentalstouchindexmd"></a>[触控](~/android/app-fundamentals/touch/index.md)

本部分介绍在 Android 上实现触摸笔势的概念和详细信息。 接下来介绍触控 Api，并探讨手势识别器。

## <a name="httpclient-stack-and-ssltlsandroidapp-fundamentalshttp-stackmd"></a>[HttpClient 堆栈和 SSL/TLS](~/android/app-fundamentals/http-stack.md)

本部分介绍适用于 Android 的 HttpClient 堆栈和 SSL/TLS 实现选择器。 这些设置确定你的 Xamarin Android 应用将使用的 HttpClient 和 SSL/TLS 实现。

## <a name="writing-responsive-applicationswriting-responsive-appsmd"></a>[编写响应式应用程序](writing-responsive-apps.md)

本文介绍如何通过将长时间运行的任务移到后台线程来使用线程处理来保持 Xamarin Android 应用程序的响应能力。
