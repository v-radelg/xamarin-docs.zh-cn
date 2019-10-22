---
title: 与 Xamarin 的源链接
description: 本文介绍如何使用源链接来调试 Xamarin. Forms。
zone_pivot_groups: platform
ms.prod: xamarin
ms.assetId: 1E13FCD9-5607-46E8-80E4-87A58B389BEB
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 09/26/2019
ms.openlocfilehash: 7a3fe70c8ac29f9e84b5d071a0ba1ef73afbbe11
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "72697805"
---
# <a name="source-link-with-xamarinforms"></a>与 Xamarin 的源链接

Xamarin。 Forms NuGet 包包含源链接映射。 源链接将包含在 NuGet 包中的已编译库映射到源代码存储库。 Visual Studio 将在调试期间下载源代码文件，使开发人员可以单步执行代码，从而启用包调试，而无需从源生成。

有关使用源链接的详细信息，请参阅[源链接文档](/dotnet/standard/library-guidance/sourcelink)。

::: zone pivot="windows"

> [!WARNING]
> Visual Studio 2019 支持 **.net 调试器**的源链接，但目前不支持**Mono 调试器**的源链接。 因此，你可以使用源链接来调试 UWP 应用，但不能使用 Android 或 iOS 应用。 调试 UWP 应用时，必须确保要调试的库的 PDB 文件已复制到用于编译应用程序的**bin**目录中的**AppX**文件夹中。

## <a name="enable-source-link"></a>启用源链接

使用源链接需要为外部代码启用调试，否则调试器将单步执行对当前解决方案中未包含的代码的调用。 在 Visual Studio 2019 中，可以在 "**调试**" 部分的 "**选项**" 菜单下找到此项：

[Visual Studio 2019 中的 ![Enable 源链接](sourcelink-images/sourcelink-enable-pc-cropped.png)](sourcelink-images/sourcelink-enable-pc.png#lightbox)

确保 **"启用仅我的代码"** 已禁用，启用**源链接支持**。

::: zone-end
::: zone pivot="macos"

## <a name="enable-source-link"></a>启用源链接

使用源链接需要为外部代码启用调试，否则调试器将单步执行对当前解决方案中未包含的代码的调用。 此选项可在 "**调试器**" 部分的 "**首选项**" 窗口中找到：

[Visual Studio for Mac 中的 ![Enable 源链接](sourcelink-images/sourcelink-enable-mac-cropped.png)](sourcelink-images/sourcelink-enable-mac.png#lightbox)

请确保已启用 "**单步执行外部代码**"。

::: zone-end

## <a name="debug-xamarinforms-using-source-link"></a>使用源链接调试 Xamarin

如果启用了 "调试外部包"，则 Visual Studio 将使用 NuGet 包中包含的源链接映射下载并单步执行外部源代码。 这可以通过在对 Xamarin 提供的方法的调用上设置断点进行测试。 Forms：

[在 Xamarin. Forms 方法上设置 ![Breakpoint](sourcelink-images/breakpoint-cropped.png)](sourcelink-images/external-code-available.png#lightbox)

根据你在**调试器**选项中指定的设置，Visual Studio 会警告你正在下载源文件：

[![Visual Studio 外部代码警告](sourcelink-images/external-code-cropped.png)](sourcelink-images/external-code-available.png#lightbox)

允许 Visual Studio 下载文件后，调试器将单步执行外部代码。

::: zone pivot="windows"

## <a name="source-link-caching"></a>源链接缓存

源链接使用缓存来提高性能。 在 "**符号**" 部分的 "**调试**" 下的 "**选项**" 菜单中定义源链接的缓存目录：

[![Visual Studio 源链接缓存](sourcelink-images/sourcelink-caching-pc-cropped.png)](sourcelink-images/sourcelink-caching-pc.png#lightbox)

此菜单允许您为所有调试符号指定缓存目录，并在遇到缓存符号问题时清除缓存。

::: zone-end
::: zone pivot="macos"

## <a name="source-link-caching"></a>源链接缓存

源链接使用缓存来提高性能。 MacOS 上源链接的缓存目录已 `/Users/<username>/Library/Caches/VisualStudio/8.0/Symbols`。 此文件夹包含存储用于下载源文件的存储库的子文件夹。 如果 NuGet 包的后备存储库已更改，可能需要手动删除这些文件夹以刷新缓存。

::: zone-end

## <a name="related-links"></a>相关链接

- [源链接文档](/dotnet/standard/library-guidance/sourcelink)
- [GitHub 上的源链接](https://github.com/dotnet/sourcelink)
