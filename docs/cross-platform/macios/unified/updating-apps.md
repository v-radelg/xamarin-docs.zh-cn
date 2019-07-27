---
title: 将现有应用程序更新到 Unified API
description: 本文档链接到介绍如何将 Xamarin 应用程序更新到 Unified API 的各种指南。 它讨论了 Xamarin iOS 应用程序和 Xamarin 应用程序。 Xamarin、Forms 应用、跨平台应用中的本机类型和绑定项目。
ms.prod: xamarin
ms.assetid: 8A654C95-5DCA-4BB5-A582-F96C2BECC81C
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 61562eed8867b7a2e12b3d2e96feed28d957696b
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68511205"
---
# <a name="updating-existing-apps-to-the-unified-api"></a>将现有应用程序更新到 Unified API

> [!IMPORTANT]
> 已不推荐使用 Unified API 之前的 Xamarin Classic API。
> - 支持 Classic API (monotouch.dialog) 的最新版本的 Xamarin 是 Xamarin 9.10。
> - Xamarin 仍支持 Classic API, 但不再更新。 由于已弃用, 因此开发人员应将其应用程序移到 Unified API。

## <a name="how-to-update-your-apps"></a>如何更新应用

更新应用有三个步骤:

1. 修复现有代码中的任何编译器警告, 尤其是与弃用的 Api 相关的警告。

2. 使用中内置的迁移工具来 Visual Studio for Mac 更新项目文件和命名空间。

3. 修复与新的[64 类型](~/cross-platform/macios/nativetypes.md)和已更改的[其他 api](~/cross-platform/macios/unified/overview.md#deprecated-typos)相关的其余编译器错误。 有关可能需要的手动更新的其他信息, 请查看[以下提示](~/cross-platform/macios/unified/updating-tips.md)。

每种产品都有特定的指南可帮助你将应用程序更新为 Unified API 和64位支持:

### <a name="xamarinios-appscross-platformmaciosunifiedupdating-ios-appsmd"></a>[Xamarin iOS 应用](~/cross-platform/macios/unified/updating-ios-apps.md)

使用内置到 Visual Studio for Mac 的自动迁移工具可以将现有的 Xamarin iOS 应用更新到 Unified API。 然后, 可能需要一些其他修补程序, 如[这些说明](~/cross-platform/macios/unified/updating-ios-apps.md)和[提示](~/cross-platform/macios/unified/updating-tips.md)中所述。

### <a name="xamarinmac-appscross-platformmaciosunifiedupdating-mac-appsmd"></a>[Xamarin Mac 应用](~/cross-platform/macios/unified/updating-mac-apps.md)

现有的 Xamarin 应用可以使用内置到 Visual Studio for Mac 的自动迁移工具更新到 Unified API。 然后, 可能需要一些其他修补程序, 如[这些说明](~/cross-platform/macios/unified/updating-mac-apps.md)和[提示](~/cross-platform/macios/unified/updating-tips.md)中所述。

### <a name="xamarinforms-appscross-platformmaciosunifiedupdating-xamarin-forms-appsmd"></a>[Xamarin. Forms 应用](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md)

按照这些说明操作, 使用 iOS 项目更新现有的 Xamarin Forms 解决方案, 以便使用 Unified API。 Unified API 支持仅适用于 Xamarin. Forms 1.3 及更高版本, 因此[说明](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md)还介绍了如何将 xamarin 应用程序更新到版本1.3。 这些[提示](~/cross-platform/macios/unified/updating-tips.md)可帮助更新自定义呈现器或依赖项服务中的任何本机 iOS 代码。

## <a name="working-with-native-types-in-cross-platform-appscross-platformmaciosnativetypesmd"></a>[使用跨平台应用中的本机类型](~/cross-platform/macios/nativetypes.md)

本文介绍如何在跨平台应用程序中使用新的 iOS Unified API 本机类型 (nint、nuint、nfloat), 该应用程序中的代码与 Android 或 Windows Phone Os 等非 iOS 设备共享。 它可让你深入了解本机类型的使用情况, 并为新类型必须与跨平台代码一起使用的情况提供多种可能的解决方案。

## <a name="update-bindings-to-the-unified-api"></a>将绑定更新到 Unified API

已创建到目标 C 库的绑定的客户需要更新绑定项目, 以反映底层 API 中的更改 (其中, 一些类型现在将为64位)。
按照这些说明进行操作,[更新现有的绑定项目以支持 Unified API](~/cross-platform/macios/unified/update-binding.md)。

## <a name="related-links"></a>相关链接

- [更新 iOS 应用程序](~/cross-platform/macios/unified/updating-ios-apps.md)
- [更新 Mac 应用](~/cross-platform/macios/unified/updating-mac-apps.md)
- [更新 Xamarin 应用程序](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md)
- [更新绑定](~/cross-platform/macios/unified/update-binding.md)
- [更新提示](~/cross-platform/macios/unified/updating-tips.md)
- [经典与 Unified API 差异](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md)
