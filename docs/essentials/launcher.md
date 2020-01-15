---
title: Xamarin.Essentials：Launcher
description: Xamarin.Essentials 中的 Launcher 类允许应用程序打开系统的 URI。
ms.assetid: BABF40CC-8BEE-43FD-BE12-6301DF27DD33
author: jamesmontemagno
ms.author: jamont
ms.date: 08/20/2019
ms.openlocfilehash: 88c1450d28b4c94fe8079b8915503cf5de118644
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75488512"
---
# <a name="xamarinessentials-launcher"></a>Xamarin.Essentials:启动器

 Launcher 类允许应用程序打开系统的 URI。 通常在深入链接到另一个应用程序的自定义 URI 方案后使用此类。 如果想要将浏览器打开到某个网站，则应引用[浏览器](open-browser.md)  API。

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

可以用 `TryOpenAsync` 将此合并为单个调用，此语法将检查是否可以打开该参数，如果可以打开则打开它。

```csharp
public class LauncherTest
{
    public async Task<bool> OpenRideShareAsync()
    {
        return await Launcher.TryOpenAsync("lyft://ridetype?id=lyft_line");
    }
}
```

### <a name="additional-platform-setup"></a>其他平台设置

# <a name="androidtabandroid"></a>[Android](#tab/android)

无其他设置。

# <a name="iostabios"></a>[iOS](#tab/ios)

在 iOS 9 及更高版本中，Apple 强制实施应用程序可以对其进行查询的方案。 若要指定想要使用的方案，必须在 `Info.plist` 文件中指定 `LSApplicationQueriesSchemes`。

```
<key>LSApplicationQueriesSchemes</key>
<array>
    <string>lyft</string>  
    <string>fb</string>
</array>
```

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

无其他设置。

-----

## <a name="files"></a>文件

此功能使应用能够请求其他应用打开和查看文件。 Xamarin.Essentials 将自动检测文件类型 (MIME) 并请求打开文件。

以下是将文本写入磁盘并请求将其打开的示例：

```csharp
var fn = "File.txt";
var file = Path.Combine(FileSystem.CacheDirectory, fn);
File.WriteAllText(file, "Hello World");

await Launcher.OpenAsync(new OpenFileRequest
{
    File = new ReadOnlyFile(file)
});
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
