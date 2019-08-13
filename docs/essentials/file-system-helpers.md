---
title: Xamarin.Essentials:文件系统帮助程序
description: Xamarin.Essentials 中的 FileSystem 类包含一系列帮助程序，用于查找应用程序的缓存和数据目录以及打开应用包内的文件。
ms.assetid: B3EC2DE0-EFC0-410C-AF71-7410AE84CF84
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: 5b155e4976a67bda36e66d2ca3565c9237fde3c6
ms.sourcegitcommit: c6e56545eafd8ff9e540d56aba32aa6232c5315f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2019
ms.locfileid: "68738869"
---
# <a name="xamarinessentials-file-system-helpers"></a>Xamarin.Essentials:文件系统帮助程序

FileSystem 类包含一系列帮助程序，用于查找应用程序的缓存和数据目录以及打开应用包内的文件  。

## <a name="get-started"></a>入门

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-file-system-helpers"></a>使用文件系统帮助程序

在你的类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

获取应用程序的目录以存储缓存数据  。 缓存数据可用于满足以下要求的任何数据：需要比临时数据持续更长时间，但不应是正确执行操作所需的数据，因为操作系统将指示何时清除此存储。

```csharp
var cacheDir = FileSystem.CacheDirectory;
```

为任何非用户数据文件的文件获取应用程序的顶级目录。 这些文件是使用同步框架的操作系统进行备份的。 查看下面的平台实现细节。

```csharp
var mainDir = FileSystem.AppDataDirectory;
```

打开捆绑到应用程序包中的文件：

```csharp
 using (var stream = await FileSystem.OpenAppPackageFileAsync(templateFileName))
 {
    using (var reader = new StreamReader(stream))
    {
        var fileContents = await reader.ReadToEndAsync();
    }
 }
```

## <a name="platform-implementation-specifics"></a>平台实现细节

# <a name="androidtabandroid"></a>[Android](#tab/android)

- **CacheDirectory** - 返回当前上下文的 [CacheDir](https://developer.android.com/reference/android/content/Context.html#getCacheDir)。
- **AppDataDirectory** - 返回当前上下文的 [FilesDir](https://developer.android.com/reference/android/content/Context.html#getFilesDir)，并且是使用 API 23 及更高版本的[自动备份](https://developer.android.com/guide/topics/data/autobackup.html)进行备份的。

将任何文件添加到 Android 项目中的 Assets 文件夹中，并将生成操作标记为 AndroidAsset 以将其与 `OpenAppPackageFileAsync` 一起使用   。

# <a name="iostabios"></a>[iOS](#tab/ios)

- **CacheDirectory** - 返回 [Library/Caches](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html) 目录。
- **AppDataDirectory** - 返回由 iTunes 和 iCloud 备份的 [Library](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html) 目录。

将任何文件添加到 iOS 项目中的 Resources 文件夹中，并将生成操作标记为 BundledResource 以将其与 `OpenAppPackageFileAsync` 一起使用   。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

- **CacheDirectory** - 返回 [LocalCacheFolder](https://docs.microsoft.com/uwp/api/windows.storage.applicationdata.localcachefolder#Windows_Storage_ApplicationData_LocalCacheFolder) 目录。
- **AppDataDirectory** - 返回备份到云的 [LocalFolder](https://docs.microsoft.com/uwp/api/windows.storage.applicationdata.localfolder#Windows_Storage_ApplicationData_LocalFolder) 目录。

将任何文件添加到 UWP 项目中的根目录中，并将生成操作标记为 Content 以将其与 `OpenAppPackageFileAsync` 一起使用  。

--------------

## <a name="api"></a>API

- [文件系统帮助程序源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/FileSystem)
- [文件系统 API 文档](xref:Xamarin.Essentials.FileSystem)
