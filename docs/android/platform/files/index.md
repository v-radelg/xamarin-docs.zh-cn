---
title: 使用 Xamarin.Android 的文件访问
description: 本指南将说明在 Xamarin.Android 中的文件访问
ms.prod: xamarin
ms.assetid: FC1CFC58-B799-4DD6-8ED1-DE36B0E56856
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/23/2018
ms.openlocfilehash: 2978f0b2bcbdd463876784a9addd7dec055b8af9
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60949411"
---
# <a name="file-storage-and-access-with-xamarinandroid"></a>文件存储和使用 Xamarin.Android 的访问

Android 应用的一个常见要求是操作文件&ndash;保存图片、 下载的文档，或导出数据与其他程序共享。 Android （这基于 Linux） 通过提供针对文件存储空间来支持此模式。 Android 到两个不同类型的存储组文件系统：

* **内部存储**&ndash;这是可以访问只能由应用程序或操作系统文件系统的一部分。
* **外部存储**&ndash;这是存储的文件访问的所有应用、 用户和其他设备可能是一个分区。 在某些设备上外部存储可能是可移动 （如 SD 卡）。

这些分组才概念，并不一定是引用单个分区或设备上的目录。 Android 设备将始终提供内部存储和外部存储的分区。 很可能某些设备可能包含被视为外部存储的多个分区。 而不考虑分区进行读取的 Api，编写，或创建的文件是相同的。 有两个 Xamarin.Android 应用程序可用于文件访问的 Api 集：

1. **.NET Api （提供的 Mono 和 xamarin.android 包装）** &ndash;这些包含[文件系统帮助程序](~/essentials/file-system-helpers.md?context=xamarin/android)提供[Xamarin.Essentials](~/essentials/index.md?context=xamarin/android)。 .NET Api 提供最佳的跨平台兼容性和本指南的重点将这种情况下是这些 Api。
1. **本机 Java 文件访问 Api （提供的 Java 和 xamarin.android 包装）** &ndash; Java 提供其自身 Api，可用于读取和写入文件。 这些是对.NET Api 中，完全可以接受的替代方案，但特定于 Android，并不适合用于跨平台的应用。

读取和写入文件，在 Xamarin.Android 中几乎完全相同，因为它是与任何其他.NET 应用程序。 Xamarin.Android 应用程序确定将进行操作，然后使用标准.NET 习惯用语文件访问的文件的路径。 由于内部和外部存储的实际路径可能不同设备的或从到 Android 版本的 Android 版本，建议不要进行硬编码文件的路径。 相反，使用 Xamarin.Android Api 来确定文件的路径。 这样一来，用于读取和写入文件的.NET Api 公开的本机 Android Api，可帮助确定文件存储在内部和外部存储的路径。

在讨论之前具有文件访问权限相关的 Api，务必了解一些有关内部和外部存储的详细信息。 这将在下一节中讨论。

## <a name="internal-vs-external-storage"></a>内部与外部存储

从概念上讲，内部存储和外部存储都是非常类似&ndash;它们是一个 Xamarin.Android 应用程序可能会将文件保存的这两个位置。 这种相似性可能令人困惑，因为它不是清除应用程序应使用内部存储 vs 外部存储时不熟悉 Android 开发人员。

内部存储是指 Android 分配到操作系统 Apk，并为单个应用的非易失性内存。 此空间不可访问除操作系统或应用程序。 Android 将为每个应用分配中内部存储分区的目录。 卸载应用，都将在内部存储在该目录中的所有文件也将被都删除。 内部存储最适合用于文件才可以访问该应用程序，并将不会与其他应用共享或卸载应用后，将具有很少的值。 Android 6.0 或更高版本，文件存储在内部存储可能会自动备份使用 Google [Android 6.0 中的自动备份功能](https://developer.android.com/guide/topics/data/autobackup)。 内部存储具有以下缺点：

* 不能共享文件。
* 卸载应用程序时，将删除文件。
* 可能是有限的内部存储上的可用空间。

外部存储是指不是内部存储的文件存储和应用程序无法以独占方式访问。 外部存储的主要用途是提供放置的文件，用于在应用之间共享或太大，无法在内部存储的地方。 外部存储的优点是它通常具有比内部存储的文件的更多空间。 但是，外部存储不能始终保证存在的设备上，可能需要从用户的特殊权限来访问它。

> [!NOTE]
> Android 将适用于支持多个用户的设备，提供每个用户自身的目录，对内部和外部存储。 此目录是在设备上的其他用户无法访问。 只要他们做不到文件存储在内部或外部存储进行硬编码路径，这种分离是对应用程序不可见。

根据经验，Xamarin.Android 应用应更喜欢将其文件存储在内部存储保存时是合理的并依赖于外部存储或文件时需要与其他应用程序共享，非常大，即使在卸载应用程序应保留。 例如，配置文件是最适合用于内部存储，因为它具有除到创建它的应用不重要。  与此相反，照片是外部存储的良好候选项。 它们可能会非常大，在许多情况下用户可能想要共享或访问它们，即使卸载应用。

本指南将重点介绍内部存储。 请参阅指南[外部存储](~/android/platform/files/external-storage.md)有关 Xamarin.Android 应用程序中使用外部存储的详细信息。

## <a name="working-with-internal-storage"></a>使用内部存储

应用程序的内部存储目录由操作系统，并且向 Android 应用程序的公开`Android.Content.Context.FilesDir`属性。 这将返回`Java.IO.File`表示 Android 有专门的专用于应用程序的目录对象。  例如，包名称的应用**com.companyname**可能是内部存储目录：

```bash
/data/user/0/com.companyname/files
```

本文档将到内部存储的目录，请参阅_内部\_存储_。

> [!IMPORTANT]
> 到设备和 Android 的不同版本之间的内部存储目录的确切路径可以因设备。 因此，应用必须未硬代码内部文件存储目录的路径并改为使用 Xamarin.Android Api，如`System.Environment.GetFolderPath()`。

若要最大化代码共享，Xamarin.Android 应用程序 （或面向 Xamarin.Android 的 Xamarin.Forms 应用程序） 应使用[ `System.Environment.GetFolderPath()` ](xref:System.Environment.GetFolderPath*)方法。 在 Xamarin.Android 中，此方法将返回与相同的位置的目录的字符串`Android.Content.Context.FilesDir`。 此方法采用枚举， `System.Environment.SpecialFolder`，用于标识一组表示由操作系统使用的特殊文件夹的路径的枚举常量。 不是所有`System.Environment.SpecialFolder`值将映射到 Xamarin.Android 上的有效目录。 下表描述了有关在给定的值的预期路径是什么`System.Environment.SpecialFolder`:

| System.Environment.SpecialFolder | 路径  |
|----------------------|---|
| `ApplicationData` | **_INTERNAL\_STORAGE_/.config** |
| `Desktop` | **_INTERNAL\_STORAGE_/Desktop** |
| `LocalApplicationData` | **_INTERNAL\_STORAGE_/.local/share** |
| `MyDocuments` | **_INTERNAL\_STORAGE_** |
| `MyMusic` | **_INTERNAL\_STORAGE_/Music** |
| `MyPictures` | **_INTERNAL\_STORAGE_/Pictures** |
| `MyVideos` | **_INTERNAL\_STORAGE_/Videos** |
| `Personal` | **_INTERNAL\_STORAGE_** |


### <a name="reading-or-writing-to-files-on-internal-storage"></a>读取或写入到文件存储在内部存储

任一[ C# Api，可用于编写](https://docs.microsoft.com/dotnet/csharp/programming-guide/file-system/how-to-write-to-a-text-file)到一个文件是不够的; 只需是获取分配给应用程序的目录中的文件的路径。 强烈建议具有文件访问权限阻止主线程将关联的异步版本的.NET Api 用于最小化可能是任何问题。

此代码片段是一个整数写入 utf-8 文本文件的内部存储目录的应用程序的一个示例：

```csharp
public async Task SaveCountAsync(int count)
{
    var backingFile = Path.Combine(System.Environment.GetFolderPath(System.Environment.SpecialFolder.Personal), "count.txt");
    using (var writer = File.CreateText(backingFile))
    {
        await writer.WriteLineAsync(count.ToString());
    }
}
```

下一步的代码片段提供了一种方法来读取存储在文本文件中的一个整数值：

```csharp
public async Task<int> ReadCountAsync()
{
    var backingFile = Path.Combine(System.Environment.GetFolderPath(System.Environment.SpecialFolder.Personal), "count.txt");

    if (backingFile == null || !File.Exists(backingFile))
    {
        return 0;
    }

    var count = 0;
    using (var reader = new StreamReader(backingFile, true))
    {
        string line;
        while ((line = await reader.ReadLineAsync()) != null)
        {
            if (int.TryParse(line, out var newcount))
            {
                count = newcount;
            }
        }
    }

    return count;
}
```

## <a name="using--xamarinessentials-ndash-file-system-helpers"></a>使用 Xamarin.Essentials&ndash;文件系统帮助程序

[Xamarin.Essentials](~/essentials/file-system-helpers.md?context=xamarin/android)是一套 Api 用于编写跨平台兼容的代码。 [文件系统帮助程序](~/essentials/file-system-helpers.md?context=xamarin/android)是一个类，包含一系列的帮助程序来简化查找应用程序的缓存和数据目录。 此代码片段提供了如何查找应用的内部存储目录和缓存目录的示例：

```csharp
// Get the path to a file on internal storage
var backingFile = Path.Combine(Xamarin.Essentials.FileSystem.AppDataDirectory, "count.txt");

// Get the path to a file in the cache directory
var cacheFile = Path.Combine(Xamarin.Essentials.FileSystem.CacheDirectory, "count.txt");
```

## <a name="hiding-files-from-the-mediastore"></a>隐藏文件 `MediaStore`

`MediaStore` Android 设备上是收集有关媒体文件 （视频、 音乐、 图像） 的元数据的 Android 组件。 其目的是简化设备上的所有 Android 应用之间共享这些文件。

专用文件都不会显示为可共享的媒体中。 例如，如果应用程序将图片保存到其专用的外部存储，然后该文件将不选取媒体扫描程序 (`MediaStore`)。

将提取公共文件通过`MediaStore`。 具有零字节文件名称的目录 **。NOMEDIA**不会通过扫描`MediaStore`。

## <a name="related-links"></a>相关链接

* [外部存储](~/android/platform/files/external-storage.md)
* [保存文件存储在设备存储](https://developer.android.com/training/data-storage/files)
* [Xamarin.Essentials 文件系统帮助程序](~/essentials/file-system-helpers.md?context=xamarin/android)
* [使用自动备份的备份用户数据](https://developer.android.com/guide/topics/data/autobackup)
* [采纳存储](https://source.android.com/devices/storage/adoptable)
