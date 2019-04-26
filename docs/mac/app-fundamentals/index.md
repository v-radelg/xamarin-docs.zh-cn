---
title: Xamarin.Mac 应用程序基础知识
description: 此文档所链接到介绍了各种概念了解开发 Xamarin.Mac 应用程序时所需的指南。
ms.prod: xamarin
ms.assetid: 5A36B3A7-F197-4AC3-A40D-B2C49362FF06
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 12/17/2015
ms.openlocfilehash: 376286b73c92cba40de183043b86cb4ffb5e699d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61085300"
---
# <a name="xamarinmac-application-fundamentals"></a>Xamarin.Mac 应用程序基础知识

## <a name="common-patterns-and-idiomsmacapp-fundamentalspatternsmd"></a>[常用模式和惯例](~/mac/app-fundamentals/patterns.md)

通过 C# 公开 Apple Api，在特定的惯用语言和模式出现反复地。 如果您没有使用与 Xamarin.iOS 编程经验，这些可能很熟悉。 文档将通常引用这些模式和惯例重复，因此具有清楚地了解它们将帮助你了解你找到的文档。

## <a name="understanding-mac-apismacapp-fundamentalsmac-apismd"></a>[了解 Mac Api](~/mac/app-fundamentals/mac-apis.md)

对于很多时间使用 Xamarin.Mac 开发，可以认为、 读取和写入在 C# 中，而无需过多地考虑使用基础的 Objective C Api。 但是，有时您需要从 Apple，读取 API 文档转换为您的问题的解决方案从 Stack Overflow 上的答案或向现有示例进行比较。

## <a name="console-appsmacapp-fundamentalsconsolemd"></a>[控制台应用](~/mac/app-fundamentals/console.md)

此外可以构建访问本机 macOS Api 的"无外设"控制台应用程序使用 Xamarin.Mac。

## <a name="working-with-xib-filesmacapp-fundamentalsxibmd"></a>[使用.xib 文件](~/mac/app-fundamentals/xib.md)

本文介绍如何在 Xcode 的 Interface Builder 来创建和维护一个 Xamarin.Mac 应用程序的用户界面中创建的.xib 文件。

## <a name="storyboardxib-less-user-interface-designmacapp-fundamentalsxibless-uimd"></a>[.storyboard/.xib 小于用户界面设计](~/mac/app-fundamentals/xibless-ui.md)

本文介绍如何创建 Xamarin.Mac 应用程序的用户界面直接通过 C# 代码而无需使用 Xcode 的 Interface Builder.storyboard 或.xib 文件。

## <a name="working-with-imagesmacapp-fundamentalsimagemd"></a>[使用图像](~/mac/app-fundamentals/image.md)

本文介绍如何使用图像和 Xamarin.Mac 应用程序中的图标。 介绍如何创建和维护映像所需创建应用程序的图标和使用 C# 代码和 Xcode 的 Interface Builder 中的映像。

## <a name="data-binding-and-key-value-codingmacapp-fundamentalsdatabindingmd"></a>[数据绑定和键值编码](~/mac/app-fundamentals/databinding.md)

本文介绍如何使用键-值的编码和观察以便数据绑定到 Xcode 的 Interface Builder 中的 UI 元素的键-值。 使用此方法，大大减少需要编写的 Xamarin.Mac 应用程序的 C# 代码量。 

## <a name="working-with-databasesmacapp-fundamentalsdatabasesmd"></a>[使用数据库](~/mac/app-fundamentals/databases.md)

本文介绍如何使用键-值的编码和键-值观察以允许进行数据绑定的 SQLite 数据库到 Xcode 的 Interface Builder 中的 UI 元素的直接访问权限。 它还介绍了使用 SQLite.NET ORM 以提供对 SQLite 数据的访问。

## <a name="working-with-copy-and-pastemacapp-fundamentalscopy-pastemd"></a>[使用复制和粘贴](~/mac/app-fundamentals/copy-paste.md)

本文介绍如何使用粘贴板提供复制并粘贴在 Xamarin.Mac 应用程序中。 它演示如何在多个应用程序和如何支持自定义数据中为应用程序之间共享的标准数据类型。

## <a name="sandboxing-a-xamarinmac-appmacapp-fundamentalssandboxingmd"></a>[沙盒处理 Xamarin.Mac 应用](~/mac/app-fundamentals/sandboxing.md)

本文介绍如何在 App Store 上发布的 Xamarin.Mac 应用程序沙盒处理。 它介绍了所有进入沙箱处理的元素： 容器目录、 授权、 确定用户的权限、 权限分离和内核强制。

## <a name="playing-sound-with-avaudioplayermacapp-fundamentalssoundsmd"></a>[使用 AVAudioPlayer 声音播放](~/mac/app-fundamentals/sounds.md)

本文介绍如何使用一个帮助器类控制的声音使用 AVAudioPlayer 播放。

## <a name="reporting-bugsmacapp-fundamentalstroubleshootingmd"></a>[报告 bug](~/mac/app-fundamentals/troubleshooting.md)

有时，我们都就会处理一个项目，可以在无法得到我们所希望的方式协同工作的 API 或在尝试解决 bug 时停留。 若要成功地编写你的移动和桌面应用程序，是 Xamarin 的目标，我们提供了一些资源，以帮助。
