---
title: Xamarin iOS 应用程序基础知识
description: 本文档链接到各种指南，这些指南描述了针对 Xamarin iOS 开发的基础概念，如应用传输安全、后台处理、事件和线程处理。
ms.prod: xamarin
ms.assetid: 608403AE-B09F-4D9C-8F59-F9DE9F0B1CF1
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/21/2017
ms.openlocfilehash: 0ccdde29183645b93831b7261909714f9baf3fa4
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73010027"
---
# <a name="xamarinios-application-fundamentals"></a>Xamarin iOS 应用程序基础知识

本部分介绍开发人员在开发 Xamarin iOS （以前称为 Monotouch.dialog）应用程序时需要注意的一些更常见的任务或概念。

## <a name="accessibilityiosapp-fundamentalsaccessibilitymd"></a>[辅助功能](~/ios/app-fundamentals/accessibility.md)

本文档介绍可用于帮助生成尽可能多的用户访问的应用程序的各种 Api 和工具。

## <a name="app-transport-securityiosapp-fundamentalsatsmd"></a>[应用传输安全性](~/ios/app-fundamentals/ats.md)

本文将介绍应用传输安全在 iOS 9 应用上强制实施的安全更改，以及这对你的 Xamarin iOS 项目的意义，它将介绍 ATS 配置选项，并将介绍如何选择退出 ATS （如果需要）。 因为默认情况下启用 ATS，所以任何不安全的 internet 连接将在 iOS 9 应用程序中引发异常（除非你显式允许）。

## <a name="backgroundingiosapp-fundamentalsbackgroundingindexmd"></a>[后台处理](~/ios/app-fundamentals/backgrounding/index.md)

后台处理或后台处理是让应用程序在后台中执行任务的过程。 本指南将介绍 iOS 中的后台处理。

## <a name="creating-ios-applications-in-codeiosapp-fundamentalsios-code-onlymd"></a>[在代码中创建 iOS 应用程序](~/ios/app-fundamentals/ios-code-only.md)

本文介绍如何使用 Visual Studio 和 Visual Studio for Mac 在代码中完全创建 iOS 应用程序。 本文介绍了如何从空项目模板开始，通过从 UIKit 创建视图层次结构，在控制器中生成应用程序屏幕。 然后介绍了如何创建可在控制器中加载的自定义视图。

## <a name="exception-marshalingiosplatformexception-marshalingmd"></a>[异常封送](~/ios/platform/exception-marshaling.md)

描述如何在本机和托管帧之间封送目标 C 和托管异常。

## <a name="events-protocols-and-delegatesiosapp-fundamentalsdelegates-protocols-and-eventsmd"></a>[事件、协议和委托](~/ios/app-fundamentals/delegates-protocols-and-events.md)

本文介绍用于接收回叫并使用数据填充用户界面控件的关键 iOS 技术。 这些技术包括事件、协议和委托;本文介绍了每种方法的定义，以及如何使用每种C#方法。 它演示了 Xamarin iOS 如何使用 iOS 控件公开熟悉的 .NET 事件，并说明了 Xamarin iOS 如何为目标 C 概念（如协议和委托）提供支持（不应将目标-C 委托与C#委托混淆）。 本文还提供了一些示例，说明如何将协议同时用作目标-C 委托和非委托方案的基础。

## <a name="working-with-the-file-systemiosapp-fundamentalsfile-systemmd"></a>[使用文件系统](~/ios/app-fundamentals/file-system.md)

Xamarin 可以使用相同的 System.IO 类来处理 iOS 中可在任何 .NET 应用程序中使用的文件和目录。 但尽管熟悉的类和方法，iOS 仍对可创建或访问的文件实施一些限制，并为某些目录提供特殊功能。 本文概述了这些限制和功能，并演示了如何在 Xamarin iOS 应用程序中进行文件访问。

## <a name="working-with-imagesiosapp-fundamentalsimages-iconsindexmd"></a>[使用图像](~/ios/app-fundamentals/images-icons/index.md)

本文介绍如何在 Xamarin 中使用图像，这两种应用程序支持图像（如图标、加载图像等）和应用程序（如应用于控件的图像）中的图像。 还介绍了如何使用 Visual Studio for Mac 来合并图像以及如何通过代码与图像交互。

## <a name="localizationiosapp-fundamentalslocalizationindexmd"></a>[本地化](~/ios/app-fundamentals/localization/index.md)

本指南介绍如何向 Xamarin iOS 应用程序添加编码以支持国际化。

## <a name="working-with-property-listsiosapp-fundamentalsindexmd"></a>[使用属性列表](~/ios/app-fundamentals/index.md)

本文档介绍 Visual Studio for Mac 的图形和高级属性列表（info.plist）编辑器来使用 info.plist 和 info.plist。 它说明了如何设置适用于 iOS 应用程序的图标和启动映像，并演示了如何在 Visual Studio for Mac 中指定应用功能（权利）。

## <a name="working-with-security-and-privacyiosapp-fundamentalssecurity-privacymd"></a>[使用安全和隐私](~/ios/app-fundamentals/security-privacy.md)

Apple 在 iOS 10 （及更高版本）中对安全和隐私进行了多项改进，有助于开发人员提高应用程序的安全性并确保最终用户的隐私。 本文介绍如何在 Xamarin iOS 应用程序中实现这些功能。

## <a name="threadingiosapp-fundamentalsthreadingmd"></a>[线程处理](~/ios/app-fundamentals/threading.md)

本文讨论了 Xamarin iOS 应用程序中的线程处理，并对 .NET 线程池、响应式应用程序和垃圾回收进行了讨论。

## <a name="touchiosapp-fundamentalstouchindexmd"></a>[触控](~/ios/app-fundamentals/touch/index.md)

如今的许多设备上的触摸屏允许用户以自然直观的方式快速、高效地与设备交互。 这种交互不只局限于简单的触摸检测，还可以使用手势。 例如，缩小到缩放手势是一种很常见的示例，即通过使用用户可以放大或缩小的两根手指收缩屏幕的一部分。本指南介绍 iOS 中的触摸和手势。

## <a name="working-with-user-defaultsiosapp-fundamentalsuser-defaultsmd"></a>[使用用户默认值](~/ios/app-fundamentals/user-defaults.md)

使用 `NSUserDefaults` 类，可以通过编程方式将 iOS 应用和扩展与系统范围的默认系统交互。 通过使用默认系统，用户可以配置应用的行为或样式以满足其首选项（基于应用的设计）。 例如，若要以指标与英制度量值显示数据，或选择给定的 UI 主题。
