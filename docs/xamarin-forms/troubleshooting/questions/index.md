---
title: Xamarin.Forms 方面的常见问题
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 89364175-53BA-4A09-B3E2-44AC67DD971C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: 24e2ae456e478585f30aa704917f66bb0bf11da9
ms.sourcegitcommit: 650458de1d362cd7de174cacef7838f0e74426f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/15/2019
ms.locfileid: "57981635"
---
# <a name="xamarinforms-frequently-asked-questions"></a>Xamarin.Forms 方面的常见问题

## <a name="can-i-update-the-xamarinforms-default-template-to-a-newer-nuget-packageupdate-forms-templatemd"></a>[可否将 Xamarin.Forms 默认模板更新到较新的 NuGet 包？](update-forms-template.md)
本指南使用 Xamarin.Forms.NET 标准库模板作为示例，但相同的常规方法也适用于 Xamarin.Forms 共享项目模板。

## <a name="why-doesnt-the-visual-studio-xaml-designer-work-for-xamarinforms-xaml-filesforms-xaml-designermd"></a>[为何 Visual Studio XAML 设计器对 Xamarin.Forms XAML 文件不起作用？](forms-xaml-designer.md)
Xamarin.Forms XAML 文件当前不支持的可视化设计器。

## <a name="android-build-error-the-linkassemblies-task-failed-unexpectedlyandroid-linkassemblies-errormd"></a>[Android 生成错误：“LinkAssemblies”任务意外失败](android-linkassemblies-error.md)
可能会看到一条错误消息`The "LinkAssemblies" task failed unexpectedly`时生成 Xamarin.Android 项目使用的窗体。 链接器处于活动状态时将发生这种情况 (通常在*版本*生成以减少应用包的大小); 以及执行因为 Android 目标不会更新到最新的 framework。 

## <a name="why-does-my-xamarinformsmaps-android-project-fail-with-compiletodalvik--unexpected-top-level-errormaps-compiletodalvik-errormd"></a>["为何 Xamarin.Forms.Maps Android 项目失败并 COMPILETODALVIK:意外顶级错误？"](maps-compiletodalvik-error.md)
For Mac 或 Visual Studio; 在生成输出窗口中，可能会在 Visual Studio 的错误面板中出现此错误在使用 Xamarin.Forms.Maps Android 项目中。 它通常通过增加你的 Xamarin.Android 项目的 Java 堆大小来解决。
