---
title: Xamarin 应用程序基础知识
description: 本文档链接到介绍在开发 Xamarin 应用程序时需要了解的各种概念的指南。
ms.prod: xamarin
ms.assetid: 5A36B3A7-F197-4AC3-A40D-B2C49362FF06
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 12/17/2015
ms.openlocfilehash: 2603360162ee9918e83b9f5c74b8086f71d02df8
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030101"
---
# <a name="xamarinmac-application-fundamentals"></a>Xamarin 应用程序基础知识

## <a name="common-patterns-and-idiomsmacapp-fundamentalspatternsmd"></a>[常见模式和惯例](~/mac/app-fundamentals/patterns.md)

在通过C#公开的所有 Apple api 中，某些惯例和模式会重新出现。 如果你有经验来使用 Xamarin 进行编程，这些都可能很熟悉。 文档通常会重复引用这些模式和惯例，因此，对其进行深刻了解将有助于你了解所找到的文档。

## <a name="understanding-mac-apismacapp-fundamentalsmac-apismd"></a>[了解 Mac Api](~/mac/app-fundamentals/mac-apis.md)

对于大部分用 Xamarin 开发的时间，您可以考虑、读取和写入， C#而不会对基础目标-C api 有很多关注。 但是，有时你需要从 Apple 中阅读 API 文档，将 Stack Overflow 的答案转换为你的问题的解决方案，或将其与现有示例进行比较。

## <a name="console-appsmacapp-fundamentalsconsolemd"></a>[控制台应用](~/mac/app-fundamentals/console.md)

你还可以使用 Xamarin 构建访问本机 macOS Api 的 "无外设" 控制台应用。

## <a name="working-with-xib-filesmacapp-fundamentalsxibmd"></a>[使用 xib 文件](~/mac/app-fundamentals/xib.md)

本文介绍如何使用在 Xcode 的 Interface Builder 中创建的 xib 文件来创建和维护 Xamarin 应用程序的用户界面。

## <a name="storyboardxib-less-user-interface-designmacapp-fundamentalsxibless-uimd"></a>[。 xib 更少的用户界面设计](~/mac/app-fundamentals/xibless-ui.md)

本文介绍如何直接从C#代码创建 Xamarin 应用程序的用户界面，而无需使用 Xcode 或 xib 文件 Interface Builder 的。

## <a name="working-with-imagesmacapp-fundamentalsimagemd"></a>[使用图像](~/mac/app-fundamentals/image.md)

本文介绍如何在 Xamarin. Mac 应用程序中使用图像和图标。 它介绍了如何创建和维护在C#代码和 Xcode 的 Interface Builder 中创建应用程序图标和使用图像所需的映像。

## <a name="data-binding-and-key-value-codingmacapp-fundamentalsdatabindingmd"></a>[数据绑定和键值编码](~/mac/app-fundamentals/databinding.md)

本文介绍如何使用键/值编码和键-值观察，以允许数据绑定到 Xcode 的 Interface Builder 中的 UI 元素。 使用此方法可以极大地减少需要为 Xamarin C#应用程序编写的代码量。 

## <a name="working-with-databasesmacapp-fundamentalsdatabasesmd"></a>[使用数据库](~/mac/app-fundamentals/databases.md)

本文介绍如何使用键/值编码和键-值观察，以允许数据绑定直接访问 SQLite 数据库到 Xcode 的 Interface Builder 中的 UI 元素。 还介绍了如何使用 SQLite.NET ORM 提供对 SQLite 数据的访问。

## <a name="working-with-copy-and-pastemacapp-fundamentalscopy-pastemd"></a>[使用复制和粘贴](~/mac/app-fundamentals/copy-paste.md)

本文介绍如何使用粘贴板在 Xamarin 应用程序中提供复制和粘贴。 它演示如何处理可在多个应用之间共享的标准数据类型，以及如何支持在提供应用中使用自定义数据。

## <a name="sandboxing-a-xamarinmac-appmacapp-fundamentalssandboxingmd"></a>[对 Xamarin 应用程序进行沙箱处理](~/mac/app-fundamentals/sandboxing.md)

本文介绍如何对应用商店上的发布进行沙箱处理。 其中涵盖了所有要进行沙盒处理的元素：容器目录、权利、用户确定的权限、特权分离和内核强制。

## <a name="playing-sound-with-avaudioplayermacapp-fundamentalssoundsmd"></a>[用 Avaudioplayer 播放声音播放声音](~/mac/app-fundamentals/sounds.md)

本文介绍如何使用 Avaudioplayer 播放声音的帮助器类控制声音播放。

## <a name="reporting-bugsmacapp-fundamentalstroubleshootingmd"></a>[报告 bug](~/mac/app-fundamentals/troubleshooting.md)

有时，在处理项目时，所有这些操作都会停滞，因为无法获取 API 来按我们所需的方式工作，也不能尝试解决错误。 Xamarin 的目标是让你能够成功编写移动和桌面应用程序，并提供了一些资源来提供帮助。
