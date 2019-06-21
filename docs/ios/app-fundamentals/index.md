---
title: Xamarin.iOS 应用程序基础知识
description: 本文档所链接到各种指南描述概念基础到 Xamarin.iOS 开发，例如应用传输安全时，后台处理，事件，与线程处理。
ms.prod: xamarin
ms.assetid: 608403AE-B09F-4D9C-8F59-F9DE9F0B1CF1
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/21/2017
ms.openlocfilehash: 120bbfe0d5fa91e632fc56ee05431f5555653360
ms.sourcegitcommit: a153623a69b5cb125f672df8007838afa32e9edf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2019
ms.locfileid: "67268593"
---
# <a name="xamarinios-application-fundamentals"></a>Xamarin.iOS 应用程序基础知识

本部分提供有关的更常见的操作任务或概念开发人员需要开发 Xamarin.iOS (以前称为 MonoTouch) 应用程序时应注意的一些指南。

## <a name="accessibilityiosapp-fundamentalsaccessibilitymd"></a>[辅助功能](~/ios/app-fundamentals/accessibility.md)

本文档介绍了各种 Api 和工具，可用来帮助生成尽可能多的用户可以访问的应用程序。

## <a name="app-transport-securityiosapp-fundamentalsatsmd"></a>[应用传输安全性](~/ios/app-fundamentals/ats.md)

本文将介绍对 iOS 9 应用程序，这意味着你的 Xamarin.iOS 项目的应用传输安全强制实施的安全更改，它将覆盖 ATS 配置选项，以及如果需要，它将介绍如何选择退出的 ATS。 由于默认情况下启用了 ATS，任何不安全的 internet 连接将引发在 iOS 9 应用程序中的异常 （除非已显式允许它）。

## <a name="backgroundingiosapp-fundamentalsbackgroundingindexmd"></a>[后台处理](~/ios/app-fundamentals/backgrounding/index.md)

后台处理或后台处理是让应用程序在前台运行另一个应用程序时在后台执行任务的过程。 本指南可作为后台处理在 iOS 中的简介。

## <a name="creating-ios-applications-in-codeiosapp-fundamentalsios-code-onlymd"></a>[在代码中创建 iOS 应用程序](~/ios/app-fundamentals/ios-code-only.md)

本文介绍如何完全在代码使用 Visual Studio 和 Visual Studio for mac 中创建 iOS 应用程序 本文介绍了如何从空项目模板开始，通过从 UIKit 创建视图层次结构，在控制器中生成应用程序屏幕。 然后介绍了如何创建可在控制器中加载的自定义视图。

## <a name="exception-marshalingiosplatformexception-marshalingmd"></a>[异常封送处理](~/ios/platform/exception-marshaling.md)

介绍如何通过 Objective C 和托管的异常封送处理本机和托管帧之间。

## <a name="events-protocols-and-delegatesiosapp-fundamentalsdelegates-protocols-and-eventsmd"></a>[事件、 协议和委托](~/ios/app-fundamentals/delegates-protocols-and-events.md)

本文提供了用于接收回调并填充数据的用户界面控件的关键 iOS 技术。 这些技术是事件、 协议和委托;本文介绍了什么其中每项功能是和每个从 C# 的使用方式。 它演示了 Xamarin.iOS 如何使用 iOS 控件来公开熟悉.NET 事件，以及如何 Xamarin.iOS OBJECTIVE-C 的概念，例如协议和委托提供支持 （Objective C 委托不应与 C# 委托相混淆）。 本文还提供了显示协议的使用同时作为基础的 Objective C 委托和非委托方案中的示例。

## <a name="working-with-the-file-systemiosapp-fundamentalsfile-systemmd"></a>[使用文件系统](~/ios/app-fundamentals/file-system.md)

Xamarin.iOS 可以使用相同的 System.IO 类来处理文件和目录在 iOS 中，你将在任何.NET 应用程序。 但是，尽管的熟悉的类和方法，iOS 上的文件可以创建或访问实现一些限制，还提供特殊功能对于某些目录。 本文概述了这些限制和功能，并演示如何在 Xamarin.iOS 应用程序中的文件访问工作原理。

## <a name="working-with-imagesiosapp-fundamentalsimages-iconsindexmd"></a>[使用图像](~/ios/app-fundamentals/images-icons/index.md)

本文介绍如何在 Xamarin.iOS 应用程序支持图像 （如图标，加载图像，等等） 和 （如应用于控件的图像） 的应用程序中的映像中使用的映像。 它还介绍了如何使用 Visual Studio for Mac 将映像，以及如何与代码中的映像进行交互。

## <a name="localizationiosapp-fundamentalslocalizationindexmd"></a>[本地化](~/ios/app-fundamentals/localization/index.md)

本指南介绍了添加到 Xamarin.iOS 应用程序以支持国际化的编码。

## <a name="working-with-property-listsiosapp-fundamentalsindexmd"></a>[使用属性列表](~/ios/app-fundamentals/index.md)

本文档介绍 Visual Studio for Mac 的图形和高级属性列表 (.plist) 编辑器使用 Info.plist 和 Entitlements.plist。 它阐释了如何设置图标和启动映像的 iOS 应用程序，并演示从指定的应用程序功能 （授权） 在 Visual Studio for mac。

## <a name="working-with-security-and-privacyiosapp-fundamentalssecurity-privacymd"></a>[使用安全和隐私](~/ios/app-fundamentals/security-privacy.md)

Apple 已与安全和隐私 iOS 10 （及更高版本） 中存在多项增强的功能，可帮助开发人员提高他们的应用的安全性，并确保最终用户的隐私。 本文将介绍 Xamarin.iOS 应用程序中实现这些功能。

## <a name="threadingiosapp-fundamentalsthreadingmd"></a>[线程处理](~/ios/app-fundamentals/threading.md)

本文讨论了在 Xamarin.iOS 应用程序中，线程处理，并稍微讨论了.NET 线程池、 响应式应用程序和垃圾回收。

## <a name="touchiosapp-fundamentalstouchindexmd"></a>[触控](~/ios/app-fundamentals/touch/index.md)

在许多现有的设备上的触摸屏允许用户快速高效地与设备交互自然和直观的方式。 这种交互不只限于简单触摸检测 – 可以使用手势以及。 例如，捏合缩放手势是一个非常常见的示例 – 例如收缩用户可以放大或缩小的两根手指与屏幕的一部分。本指南探讨了触控和手势在 iOS 中。

## <a name="working-with-user-defaultsiosapp-fundamentalsuser-defaultsmd"></a>[使用用户默认值](~/ios/app-fundamentals/user-defaults.md)

`NSUserDefaults`类提供一种方法适用于 iOS 的应用和扩展来以编程方式与整个系统的默认系统进行交互。 通过使用默认系统，用户可以配置应用程序的行为或样式设计来满足其首选项 （基于应用的设计）。 例如，若要提供英制的指标的 vs 中的数据或选择一个给定的用户界面主题。
