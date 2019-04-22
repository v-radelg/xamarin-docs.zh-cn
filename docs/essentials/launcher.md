---
title: Xamarin.Essentials：Launcher
description: Xamarin.Essentials 中的 Launcher 类允许应用程序打开系统的 URI。
ms.assetid: BABF40CC-8BEE-43FD-BE12-6301DF27DD33
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: 26ab3687e9bcfecbd003ddd0a97f3e46a0cd92b2
ms.sourcegitcommit: 3489c281c9eb5ada2cddf32d73370943342a1082
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "58870048"
---
# <a name="xamarinessentials-launcher"></a>Xamarin.Essentials:启动器

Launcher 类允许应用程序打开系统的 URI。 通常在深入链接到另一个应用程序的自定义 URI 方案后使用此类。 如果想要将浏览器打开到某个网站，则应引用[浏览器](open-browser.md) API。

## <a name="get-started"></a>入门

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-launcher"></a>使用 Launcher

在你的类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

若要使用 Launcher 功能，请调用 `OpenAsync` 方法并传入要打开的 `string` 或 `Uri`。 （可选）`CanOpenAsync` 方法可用于检查是否可以由设备上的应用程序处理 URI 架构。

```csharp
public class LauncherTest
{
    public async Task OpenRideShareAsync()
    {
        var supportsUri = await Launcher.CanOpenAsync("lyft://");
        if (supportsUri)
            await Launcher.OpenAsync("lyft://ridetype?id=lyft_line");
    }
}
```

## <a name="platform-differences"></a>平台差异

# <a name="androidtabandroid"></a>[Android](#tab/android)

立即完成从 `CanOpenAsync` 返回的任务。

# <a name="iostabios"></a>[iOS](#tab/ios)

如果之前从未通过应用程序中的 `OpenAsync` 打开此设备上的目标应用程序，iOS 将提示一次用户允许你的应用将其打开。

立即完成从 `CanOpenAsync` 返回的任务。

[此处](xref:UIKit.UIApplication.CanOpenUrl*)提供了有关 iOS 实现的详细信息

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

无平台差异。

-----

## <a name="api"></a>API

- [Launcher 源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Launcher)
- [Launcher API 文档](xref:Xamarin.Essentials.Launcher)
