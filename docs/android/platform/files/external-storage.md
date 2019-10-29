---
title: 使用 Xamarin 在外部存储上进行文件访问
description: 本指南将讨论 Xamarin Android 中外部存储上的文件访问
ms.prod: xamarin
ms.assetid: 40da10b2-a207-4f9c-a2dd-165d9b662f33
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 07/23/2018
ms.openlocfilehash: 96b0d6a00c7825939b1f89ed63e3e5559ca4ef59
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73020486"
---
# <a name="external-storage"></a>外部存储

外部存储指的是不在内部存储上的文件存储，并且不能以独占方式访问负责该文件的应用。 外部存储的主要目的是提供一个位置，用于放置要在应用之间共享的文件，或者要在内部存储上容纳太大的文件。

从历史上讲，外部存储指的是可移动介质（如 SD 卡）上的磁盘分区（也称为_便携式存储_）。 与 Android 设备相比，这种区别不再适用，许多 Android 设备不再支持可移动存储。 相反，某些设备会分配其中的某些内部非易失内存，Android 执行相同的功能可移动介质。 这称为_模拟_存储，仍被视为外部存储。 此外，某些 Android 设备可能有多个外部存储分区。 例如，Android 平板电脑（除了其内部存储）可能具有模拟的存储以及 SD 卡的一个或多个槽。 所有这些分区都由 Android 视为外部存储。

在具有多个用户的设备上，每个用户在其外部存储的主外部存储分区上都有一个专用目录。 作为一个用户运行的应用将无法访问设备上其他用户的文件。 所有用户的文件仍是世界上可读和可写的。但是，Android 会将每个用户配置文件与其他配置文件进行沙盒处理。

在 Xamarin 中，读取和写入文件几乎完全相同，因为它是任何其他 .NET 应用程序。 Xamarin Android 应用确定将操作的文件的路径，然后使用标准 .NET 惯例进行文件访问。 由于与设备到设备或从 Android 版本到 Android 版本相比，内部和外部存储的实际路径可能有所不同，因此不建议对文件路径进行硬编码。 取而代之的是，Xamarin 公开了本机 Android Api，可帮助确定内部和外部存储上的文件的路径。

本指南将讨论 Android 中特定于外部存储的概念和 Api。

## <a name="public-and-private-files-on-external-storage"></a>外部存储上的公用和专用文件

应用可以在外部存储上保留两种不同类型的文件：

* **专用**文件 &ndash; 专用文件是特定于您的应用程序的文件（但仍是世界上可读和可写的）。 Android 预期专用文件存储在外部存储中的特定目录中。 尽管这些文件称为 "专用"，但它们仍是可见的，并且可由设备上的其他应用程序访问，而不会对 Android 进行任何特殊保护。

* **公共**文件 &ndash; 这些是不被视为特定于应用程序的文件，并且应自由共享。

这些文件之间的差异主要体现在概念上。 专用文件是专用的，因为它们被视为应用程序的一部分，而公用文件是外部存储中存在的任何其他文件。 Android 提供两个不同的 Api 来解析专用文件和公共文件的路径，但也使用相同的 .NET Api 来读取和写入这些文件。 这些是在[读取和写入](~/android/platform/files/index.md#reading-or-writing-to-files-on-internal-storage)部分中讨论的相同的 api。

### <a name="private-external-files"></a>专用外部文件

专用外部文件被视为是特定于应用程序的（类似于内部文件），但出于任何原因（例如，对于内部存储而言太大），都将其保存在外部存储中。 与内部文件类似，用户卸载应用程序时，将删除这些文件。

通过调用 `Android.Content.Context.GetExternalFilesDir(string type)`方法，可找到专用外部文件的主要位置。 此方法将返回一个 `Java.IO.File` 对象，该对象表示应用程序的专用外部存储目录。 将 `null` 传递到此方法将返回应用程序的用户存储目录的路径。 例如，对于包名称为 `com.companyname.app`的应用程序，专用外部文件的 "root" 目录将为：

```bash
/storage/emulated/0/Android/data/com.companyname.app/files/
```

本文档将外部存储上的专用文件的存储目录称为_专用\_外部\_存储_。

`GetExternalFilesDir()` 的参数是指定_应用程序目录_的字符串。 这是一个目录，旨在提供文件逻辑组织的标准位置。 可以通过 `Android.OS.Environment` 类上的常量获取字符串值：

| `Android.OS.Environment` | 目录 |
|-|-|
| DirectoryAlarms | **_私有\_外部\_存储_/Alarms** |
| DirectoryDcim | **_私有\_外部\_存储_/DCIM** |
| DirectoryDownloads | **_私有\_外部\_存储_/Download** |
| DirectoryDocuments | **_私有\_外部\_存储_/Documents** |
| DirectoryMovies | **_私有\_外部\_存储_/Movies** |
| DirectoryMusic | **_私有\_外部\_存储_/Music** |
| DirectoryNotifications | **_私有\_外部\_存储_/Notifications** |
| DirectoryPodcasts | **_私有\_外部\_存储_/Podcasts** |
| DirectoryRingtones | **_私有\_外部\_存储_/Ringtones** |
| DirectoryPictures | **_私有\_外部\_存储_/Pictures** |

对于具有多个外部存储分区的设备，每个分区都有一个用于专用文件的目录。 方法 `Android.Content.Context.GetExternalFilesDirs(string type)` 将返回 `Java.IO.Files`的数组。 每个对象都将代表所有共享/外部存储设备上的特定于应用程序的特定目录，应用程序可以在其中放置其拥有的文件。

> [!IMPORTANT]
> 专用外部存储目录的准确路径可能因设备而异，并且不同于 Android 的版本。 因此，应用程序不能对此目录的路径进行硬编码，而是使用 Xamarin Api，如 `Android.Content.Context.GetExternalFilesDir()`。

### <a name="public-external-files"></a>公共外部文件

公共文件是位于外部存储上的文件，这些文件未存储在 Android 为专用文件分配的目录中。 卸载应用时，不会删除公共文件。 Android 应用必须获得权限才能读取或写入任何公共文件。 公共文件可能存在于外部存储中的任何位置，但通过约定，Android 要求公共文件存在于属性 `Android.OS.Environment.ExternalStorageDirectory`标识的目录中。 此属性将返回一个表示主外部存储目录的 `Java.IO.File` 对象。 例如，`Android.OS.Environment.ExternalStorageDirectory` 可以引用以下目录：

```bash
/storage/emulated/0/
```

本文档将外部存储上公共文件的存储目录作为_公用\_外部\_存储_。

Android 还支持_公用\_外部\_存储_上的应用程序目录的概念。 这些目录与 `PRIVATE_EXTERNAL_STORAGE` 的应用程序目录完全相同，在上一部分的表中进行了介绍。 方法 `Android.OS.Environment.GetExternalStoragePublicDirectory(string directoryType)` 将返回对应于公共应用程序目录的 `Java.IO.File` 对象。 `directoryType` 参数是必需参数，不能 `null`。

例如，调用 `Environment.GetExternalStoragePublicDirectory(Environment.DirectoryDocuments).AbsolutePath` 将返回如下所示的字符串：

```bash
/storage/emulated/0/Documents
```

> [!IMPORTANT]
> 从设备到设备以及 Android 版本之间，公共外部存储目录的准确路径可能有所不同。 因此，应用程序不能对此目录的路径进行硬编码，而是使用 Xamarin Api，如 `Android.OS.Environment.ExternalStorageDirectory`。

## <a name="working-with-external-storage"></a>使用外部存储

Xamarin Android 应用获取文件的完整路径后，它应利用任何标准 .NET Api 来创建、读取、写入或删除文件。 这可最大程度地提高应用程序的跨平台兼容代码数量。 但是，在尝试访问文件之前，Xamarin Android 应用必须确保可以访问该文件。

1. **验证外部存储**&ndash; （具体取决于外部存储的性质），应用可能未装载和使用该存储。 所有应用程序都应在尝试使用外部存储之前检查其状态。
2. **执行运行时权限检查**&ndash; Android 应用程序必须请求用户的权限才能访问外部存储。 这意味着在任何文件访问之前都应执行运行时权限请求。 [Xamarin 中](~/android/app-fundamentals/permissions.md)的指南权限包含有关 Android 权限的更多详细信息。

下面将讨论这两个任务中的每一个。

### <a name="verifying-that-external-storage-is-available"></a>验证外部存储是否可用

写入外部存储之前的第一个步骤是检查它是否可读或可写。 `Android.OS.Environment.ExternalStorageState` 属性保留一个字符串，该字符串标识外部存储的状态。 此属性将返回表示状态的字符串。 此表列出了 `Environment.ExternalStorageState`可能返回 `ExternalStorageState` 值：

| ExternalStorageState | 描述  |
|----------------------|---|
| MediaBadRemoval      | 已突然删除媒体，但未正确卸载。 |
| MediaChecking        | 媒体存在，但要进行磁盘检查。  |
| MediaEjecting        | 媒体正在卸载并弹出。  |
| MediaMounted         | 介质已装载，可以读取或写入。  |
| MediaMountedReadOnly | 介质已装载，但只能从读取。 |
| MediaNofs            | 媒体存在，但不包含适用于 Android 的文件系统。 |
| MediaRemoved         | 不存在介质。 |
| MediaShared          | 媒体存在，但未装入。 它将通过 USB 与其他设备共享。|
| MediaUnknown         | 此媒体的状态无法通过 Android 识别。 |
| MediaUnmountable     | 媒体存在，但不能由 Android 装入。 |
| MediaUnmounted       | 媒体存在，但未装入。 |

大多数 Android 应用只需检查是否已装载外部存储。 下面的代码段演示如何验证是否已为只读访问或读写访问权限装载外部存储：

```csharp
bool isReadonly = Environment.MediaMountedReadOnly.Equals(Environment.ExternalStorageState);
bool isWriteable = Environment.MediaMounted.Equals(Environment.ExternalStorageState);
```

## <a name="external-storage-permissions"></a>外部存储权限

Android 将外部存储视为_危险的权限_，这通常要求用户授予其访问资源的权限。 用户可以随时撤消此权限。  这意味着在任何文件访问之前都应执行运行时权限请求。 应用会被自动授予读取和写入其自己的专用文件的权限。 在[授予用户权限](~/android/app-fundamentals/permissions.md)后，应用程序可以读取和写入属于其他应用程序的专用文件。

所有 Android 应用必须为**androidmanifest.xml**中的外部存储声明两个权限中的一个。 若要标识权限，必须将以下两个 `uses-permission` 元素之一添加到**androidmanifest.xml**：

```xml
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

> [!NOTE]
> 如果用户授予 `WRITE_EXTERNAL_STORAGE`，则还会隐式授予 `READ_EXTERNAL_STORAGE`。 不需要在**androidmanifest.xml**中请求这两个权限。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

还可以使用 "**解决方案属性**" 的 " **Android 清单**" 选项卡添加权限：

![解决方案资源管理器-Visual Studio 所需的权限](./images/required-permissions.w157.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

还可以使用 "**解决方案属性板**" 的 " **Android 清单**" 选项卡添加权限：

[![Solution Pad-Visual Studio for Mac 所需的权限](./images/required-permissions.m752-sml.png)](./images/required-permissions.m752.png#lightbox)

-----

一般而言，所有危险权限都必须由用户批准。 外部存储的权限是异常的，因为此规则有例外，具体取决于应用运行的 Android 版本：

![外部存储权限检查流程图](./images/external-permission-check-flowchart.png)

有关执行运行时权限请求的详细信息，请参阅[Xamarin 中](~/android/app-fundamentals/permissions.md)的指南权限。 **Monodroid-示例** [LocalFiles](https://github.com/xamarin/monodroid-samples/tree/master/LocalFiles)还演示了执行运行时权限检查的一种方法。

#### <a name="granting-and-revoking-permissions-with-adb"></a>通过 ADB 授予和撤消权限

在开发 Android 应用程序的过程中，可能需要授予和撤消权限以测试与运行时权限检查相关的各种工作流。 可以在命令提示符下使用 ADB 来执行此操作。 以下命令行代码段演示了如何使用 ADB 为其包名称为 **.com**的 Android 应用授予或撤消权限：

```bash
$ adb shell pm grant com.companyname.app android.permission.WRITE_EXTERNAL_STORAGE

$ adb shell pm revoke com.companyname.app android.permission.WRITE_EXTERNAL_STORAGE
```

## <a name="deleting-files"></a>删除文件

任何标准C# api 都可用于从外部存储中删除文件，例如[`System.IO.File.Delete`](xref:System.IO.File.Delete*)。 还可以使用 Java Api，代价是代码可移植性。 例如:

```csharp
System.IO.File.Delete("/storage/emulated/0/Android/data/com.companyname.app/files/count.txt");
```

## <a name="related-links"></a>相关链接

* [Monodroid 上的 Xamarin 本地文件示例 **-示例**](https://github.com/xamarin/monodroid-samples/tree/master/LocalFiles)
* [Xamarin 中的权限](~/android/app-fundamentals/permissions.md)
