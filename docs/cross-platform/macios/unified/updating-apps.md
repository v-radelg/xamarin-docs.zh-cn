---
title: 更新现有应用到统一的 API
description: 本文档所链接到各种指南描述如何更新到 Unified API 的 Xamarin 应用程序。 它讨论了 Xamarin.iOS 应用，Xamarin.Mac 应用。 Xamarin.Forms 应用，在跨平台应用和绑定项目中的本机类型。
ms.prod: xamarin
ms.assetid: 8A654C95-5DCA-4BB5-A582-F96C2BECC81C
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 7730d19e4f261a0e414e4946fd0add5312f5d030
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2019
ms.locfileid: "67864363"
---
# <a name="updating-existing-apps-to-the-unified-api"></a>更新现有应用到统一的 API

> [!IMPORTANT]
> Xamarin 经典 API 进行，前面有统一 API，已弃用。
> - Xamarin.iOS 支持经典 API (monotouch.dll) 的最后一个版本是 Xamarin.iOS 9.10。
> - Xamarin.Mac 仍支持传统的 API，但将不再更新。 因为它已被弃用，开发人员应移动到 Unified API 其应用程序。

## <a name="how-to-update-your-apps"></a>如何更新您的应用程序

有三个步骤来更新您的应用程序：

1. 修复任何编译器警告中的现有代码，特别是那些与已弃用的 Api。

2. 使用迁移工具内置于 Visual Studio for Mac 中更新你的项目文件和命名空间。

3. 剩余相关到新的编译器错误修复[64 类型](~/cross-platform/macios/nativetypes.md)并[其他 Api](~/cross-platform/macios/unified/overview.md#deprecated-typos)的已更改。 请查看[这些提示](~/cross-platform/macios/unified/updating-tips.md)有关手动更新可能需要的其他信息。

有特定的指南可帮助你更新应用到统一的 API 和 64 位支持每种产品：

### <a name="xamarinios-appscross-platformmaciosunifiedupdating-ios-appsmd"></a>[Xamarin ios 应用](~/cross-platform/macios/unified/updating-ios-apps.md)

现有的 Xamarin.iOS 应用程序可以更新为使用自动化的迁移工具内置到 Visual Studio for mac。 Unified API 一些额外的修补程序则可能是必需的如中所述[这些说明](~/cross-platform/macios/unified/updating-ios-apps.md)并[提示](~/cross-platform/macios/unified/updating-tips.md)。

### <a name="xamarinmac-appscross-platformmaciosunifiedupdating-mac-appsmd"></a>[Xamarin.Mac 应用](~/cross-platform/macios/unified/updating-mac-apps.md)

现有的 Xamarin.Mac 应用程序可以更新为使用自动化的迁移工具内置到 Visual Studio for mac。 Unified API 一些额外的修补程序则可能是必需的如中所述[这些说明](~/cross-platform/macios/unified/updating-mac-apps.md)并[提示](~/cross-platform/macios/unified/updating-tips.md)。

### <a name="xamarinforms-appscross-platformmaciosunifiedupdating-xamarin-forms-appsmd"></a>[Xamarin.Forms 应用](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md)

按照这些说明使用 iOS 项目以使用统一 API 更新现有的 Xamarin.Forms 解决方案。 统一的 API 支持，只是可用在 Xamarin.Forms 1.3 及更高版本，因此[说明](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md)还介绍了如何将 Xamarin.Forms 应用更新到版本 1.3。 这些[提示](~/cross-platform/macios/unified/updating-tips.md)可能会帮助正在更新自定义呈现器或依赖关系服务中的任何本机 iOS 代码。

## <a name="working-with-native-types-in-cross-platform-appscross-platformmaciosnativetypesmd"></a>[使用跨平台应用中的本机类型](~/cross-platform/macios/nativetypes.md)

本文介绍如何使用新的 iOS （nint，nuint，nfloat） 的统一 API 本机类型中的跨平台应用程序代码与非 iOS 设备，例如 Android 或 Windows Phone 操作系统的共享位置。 它提供了深入了解何时使用本机类型，并提供新的类型必须用于跨平台代码的情况下几个可能的解决方案。

## <a name="update-bindings-to-the-unified-api"></a>更新绑定到 Unified API

已创建对 OBJECTIVE-C 的库的绑定的客户将需要更新绑定项目以便反映中 （其中有些类型现在将 64 位） 的基础 API 的更改。
请按照这些说明[更新现有绑定项目以支持统一 API](~/cross-platform/macios/unified/update-binding.md)。

## <a name="related-links"></a>相关链接

- [更新 iOS 应用](~/cross-platform/macios/unified/updating-ios-apps.md)
- [更新 Mac 应用程序](~/cross-platform/macios/unified/updating-mac-apps.md)
- [更新 Xamarin.Forms 应用](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md)
- [更新绑定](~/cross-platform/macios/unified/update-binding.md)
- [正在更新提示](~/cross-platform/macios/unified/updating-tips.md)
- [经典 vs 统一的 API 差异](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
