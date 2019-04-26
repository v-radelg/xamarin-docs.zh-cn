---
title: Xamarin.Android 应用程序基础知识
description: 应用程序的核心概念
ms.prod: xamarin
ms.assetid: 935B8BFE-23B7-4239-5C87-F4A503B889CB
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 1d3bd071eeffb77f94a1b5f35f1df59f2d8c7a8a
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61022188"
---
# <a name="xamarinandroid-application-fundamentals"></a>Xamarin.Android 应用程序基础知识

本部分提供有关的更常见的操作任务或概念的开发人员需要开发 Android 应用程序时应注意的一些指南。

## <a name="accessibilityandroidapp-fundamentalsaccessibilitymd"></a>[辅助功能](~/android/app-fundamentals/accessibility.md)

此页介绍了如何使用 Android 的辅助功能 Api 来构建应用程序根据[可访问性清单](~/cross-platform/app-fundamentals/accessibility.md)。

##  <a name="understanding-android-api-levelsandroidapp-fundamentalsandroid-api-levelsmd"></a>[了解 Android API 级别](~/android/app-fundamentals/android-api-levels.md)

本指南介绍了 Android 如何使用 API 级别来管理跨不同版本的 Android 应用程序兼容性，并介绍如何配置 Xamarin.Android 项目设置以便部署这些应用程序中的 API 级别。 此外，本指南介绍如何编写运行时用于处理使用不同的 API 级别，并提供所有的 Android API 级别、 版本数字 （如 Android 8.0)、 Android 代码名称 （例如 Oreo) 和生成版本代码的引用列表。



##  <a name="resources-in-androidandroidapp-fundamentalsresources-in-androidindexmd"></a>[在 Android 中的资源](~/android/app-fundamentals/resources-in-android/index.md)

本文介绍了 Android 资源 Xamarin.Android 和文档中的概念及其用法。 它介绍了如何在 Android 应用程序中使用的资源以支持应用程序本地化和多个设备，包括不同的屏幕大小和密度。




##  <a name="activity-lifecycleandroidapp-fundamentalsactivity-lifecycleindexmd"></a>[活动生命周期](~/android/app-fundamentals/activity-lifecycle/index.md)

活动是 Android 应用程序的基本构造块，它们可以存在于多个不同的状态。 活动生命周期以实例化开始和结尾析构，，并且包括很多状态之间。 当活动更改状态时，会调用相应的生命周期事件方法，通知即将发生的状态更改的活动并使其能够执行的代码以适应所做的更改。 本文分析活动的生命周期，并解释了负责该活动具有这些状态更改为良好、 可靠的应用程序的一部分的每一阶段。

##  <a name="localizationandroidapp-fundamentalslocalizationmd"></a>[本地化](~/android/app-fundamentals/localization.md)

此文章介绍了如何将 Xamarin.Android 本地化为其他语言中，通过转换字符串，并提供替代图像。

## <a name="servicesandroidapp-fundamentalsservicesindexmd"></a>[服务](~/android/app-fundamentals/services/index.md)

本文介绍 Android 服务，是 Android 组件，允许在后台完成工作。 它说明服务适合于不同方案，并演示如何实现它们同时执行长时间运行后台任务，以及有关为远程过程调用提供一个接口。

## <a name="broadcast-receiversandroidapp-fundamentalsbroadcast-receiversmd"></a>[广播接收器](~/android/app-fundamentals/broadcast-receivers.md)

本指南介绍了如何创建和使用广播的接收器，响应系统级广播，在 Xamarin.Android 中的 Android 组件。



##  <a name="permissionsandroidapp-fundamentalspermissionsmd"></a>[权限](~/android/app-fundamentals/permissions.md)

可以使用内置于 Visual Studio for Mac 或 Visual Studio 的工具支持创建并将权限添加到 Android 清单。 本文档介绍如何在 Visual Studio 和 Xamarin Studio 中添加的权限。



##  <a name="graphics-and-animationandroidapp-fundamentalsgraphics-and-animationmd"></a>[图形和动画](~/android/app-fundamentals/graphics-and-animation.md)

Android 提供了非常丰富、 不同的框架支持二维图形和动画。 本文档介绍这些框架，并讨论如何创建自定义图形和动画和 Xamarin.Android 应用程序中使用它们。


##  <a name="cpu-architecturesandroidapp-fundamentalscpu-architecturesmd"></a>[CPU 体系结构](~/android/app-fundamentals/cpu-architectures.md)

Xamarin.Android 支持多个 CPU 体系结构，包括 32 位和 64 位设备。 本文介绍如何以应用到一个或多个支持 Android 的 CPU 体系结构为目标。




##  <a name="handling-rotationandroidapp-fundamentalshandling-rotationmd"></a>[处理旋转](~/android/app-fundamentals/handling-rotation.md)

本文介绍如何处理在 Xamarin.Android 中的设备方向更改。 它介绍了如何使用 Android 资源系统以自动加载资源的特定设备方向以及如何以编程方式处理方向更改。 然后，介绍在旋转设备时维护状态的方法。



##  <a name="android-audioandroidapp-fundamentalsandroid-audiomd"></a>[Android Audio](~/android/app-fundamentals/android-audio.md)

Android OS 提供了广泛支持为多媒体，其中包含音频和视频。 本指南重点介绍在 Android 中的音频，涵盖播放和录制音频，使用内置的音频播放器和记录器类，以及低级别的音频 API。 它还介绍如何使用由其他应用程序，广播音频事件，以便开发人员可以构建良好的应用程序。




##  <a name="notificationsandroidapp-fundamentalsnotificationsindexmd"></a>[通知](~/android/app-fundamentals/notifications/index.md)

本部分介绍如何在 Xamarin.Android 中实现本地和远程通知。 其中介绍了 Android 通知的各种 UI 元素，并讨论了 API 的涉及创建并显示一条通知。 有关远程通知，说明了同时 Google Cloud Messaging 和 Firebase Cloud Messaging。 中包含分步演练和代码示例。



##  <a name="touchandroidapp-fundamentalstouchindexmd"></a>[触控](~/android/app-fundamentals/touch/index.md)

本部分介绍的概念和实现的详细信息上的触控手势 Android。 引入和解释跟探索手势识别器的触控 Api。



##  <a name="httpclient-stack-and-ssltlsandroidapp-fundamentalshttp-stackmd"></a>[HttpClient 堆栈和 SSL/TLS](~/android/app-fundamentals/http-stack.md)

本部分介绍适用于 Android 的 HttpClient 堆栈和 SSL/TLS 实现选择器。 这些设置确定将由您的 Xamarin.Android 应用程序的 HttpClient 和 SSL/TLS 实现。


##  <a name="writing-responsive-applicationswriting-responsive-appsmd"></a>[编写响应式应用程序](writing-responsive-apps.md)

本文介绍如何使用线程处理来使 Xamarin.Android 应用程序通过将长时间运行任务到后台线程保持响应状态。