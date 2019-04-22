---
title: 使用 Xamarin.Android 的外部存储的文件访问
description: 本指南将讨论在 Xamarin.Android 中的外部存储的文件访问
ms.prod: xamarin
ms.assetid: 40da10b2-a207-4f9c-a2dd-165d9b662f33
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/23/2018
ms.openlocfilehash: 78051fce44239eea86948988a4d19ac37c5ea0d5
ms.sourcegitcommit: 3489c281c9eb5ada2cddf32d73370943342a1082
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "58854894"
---
# <a name="external-storage"></a>外部存储

外部存储是指不在内部存储且无法以独占方式访问到的应用程序负责将文件的文件存储。 外部存储的主要用途是提供放置的文件，用于在应用之间共享或太大，无法在内部存储的地方。

从历史上看来说，外部存储称为可移动媒体，如 SD 卡上的磁盘分区 (也称为_便携式存储_)。 这一区别不再的结果已演变成将 Android 设备和 Android 设备不再支持可移动存储。 而某些设备将分配一些其内部的非易失性内存执行相同的函数可移动媒体的 Android。 这称为_仿真_存储，并仍被视为是外部存储。 或者，某些 Android 设备可能有多个外部存储分区。 例如，在 Android 平板电脑 （除了其内部存储） 可能会模拟存储和一个或多个槽的 SD 卡。 所有这些分区被视为 android 外部存储。

具有多个用户的设备，每个用户将能够在其外部存储主外部存储分区上的专用的目录。 以某一用户运行的应用将无权访问文件从设备上的另一个用户。 为所有用户文件是仍世界上可读和世界可写;但是，Android 将沙盒与其他每个用户配置文件。

读取和写入文件，在 Xamarin.Android 中几乎完全相同，因为它是与任何其他.NET 应用程序。 Xamarin.Android 应用程序确定将进行操作，然后使用标准.NET 习惯用语文件访问的文件的路径。 由于内部和外部存储的实际路径可能不同设备的或从到 Android 版本的 Android 版本，建议不要进行硬编码文件的路径。 Xamarin.Android 公开的本机 Android Api，可帮助确定文件存储在内部和外部存储的路径。

本指南将讨论的概念和特定于外部存储在 Android 中的 Api。

## <a name="public-and-private-files-on-external-storage"></a>公钥和私钥文件存储在外部存储

有两种不同类型的应用程序可能将放在外部存储的文件：

* **私有**文件&ndash;专用文件是特定于应用程序 （但仍完全公开和全局可写） 的文件。 Android 需要以专用文件存储在外部存储上的特定目录。 即使文件被称为"专用"，它们仍可见且可由其他应用在设备上访问，它们不会提供任何特殊保护 Android。

* **公共**文件&ndash;这些是不被视为是特定于应用程序，旨在自由地共享的文件。

这些文件之间的区别是主要概念。 专用文件是专用的意义上说，它们被视为公共文件时存在于外部存储的任何其他文件是应用程序的一部分。 Android 提供两个不同的 Api 来解析到私有和公共文件的路径，但否则使用相同的.NET Api 来读取和写入这些文件。 这些是相同的 Api，上节中讨论[读取和写入](~/android/platform/files/index.md#reading-or-writing-to-files-on-internal-storage)。

### <a name="private-external-files"></a>专用的外部文件

专用的外部文件被视为特定于应用程序 （类似于内部文件），但保持在外部存储中以任意数量的原因 （例如正在用于内部存储太大）。 类似于内部文件，这些文件将被删除时由用户卸载应用。

通过调用该方法找到的专用外部文件的主要位置`Android.Content.Context.GetExternalFilesDir(string type)`。 此方法将返回`Java.IO.File`对象，表示应用程序的专用外部存储目录。 传递`null`到此方法将返回路径到应用程序的用户的存储目录。 例如，应用程序的包名称`com.companyname.app`，是专用的外部文件的"root"目录：

```bash
/storage/emulated/0/Android/data/com.companyname.app/files/
```

本文档将专用文件存储在为外部存储的存储目录引用_私有\_外部\_存储_。


为参数`GetExternalFilesDir()`是一个字符串，指定_应用程序目录_。 这是用于提供文件的逻辑组织的标准位置的目录。 字符串值是可通过在常量`Android.OS.Environment`类：

| `Android.OS.Environment` | 目录 |
|-|-|
| DirectoryAlarms | **_PRIVATE\_EXTERNAL\_STORAGE_/Alarms** |
| DirectoryDcim | **_PRIVATE\_EXTERNAL\_STORAGE_/DCIM** |
| DirectoryDownloads | **_私有\_外部\_存储_  /下载** |
| DirectoryDocuments | **_PRIVATE\_EXTERNAL\_STORAGE_/Documents** |
| DirectoryMovies | **_PRIVATE\_EXTERNAL\_STORAGE_/Movies** |
| DirectoryMusic | **_PRIVATE\_EXTERNAL\_STORAGE_/Music** |
| DirectoryNotifications | **_私有\_外部\_存储_/Notifications** |
| DirectoryPodcasts | **_PRIVATE\_EXTERNAL\_STORAGE_/Podcasts** |
| DirectoryRingtones | **_私有\_外部\_存储_/Ringtones** |
| DirectoryPictures | **_私有\_外部\_存储_  /图片** |

对于具有多个外部存储分区的设备，每个分区都适用于专用文件的目录。 该方法`Android.Content.Context.GetExternalFilesDirs(string type)`将返回一个数组`Java.IO.Files`。 每个对象将表示专用的特定于应用程序目录在所有应用程序可以在其中放置文件的共享/外部存储设备上它所拥有。

> [!IMPORTANT]
> 到设备和 Android 的不同版本之间的专用 exteral 存储目录的确切路径可以因设备。 正因为如此，应用必须未硬编码到该目录，路径并改为使用 Xamarin.Android Api，如`Android.Content.Context.GetExternalFilesDir()`。

### <a name="public-external-files"></a>公共外部文件

公共文件是存在于外部存储不在 Android 分配的专用文件的目录中存储的文件。 卸载应用程序时，将不会删除公共文件。 Android 应用程序之前，必须授予权限他们可以读取或写入任何公共文件。 很可能存在于外部存储的任何位置的公共文件，但按照惯例 Android 要求要由属性标识在目录中存在的公共文件`Android.OS.Environment.ExternalStorageDirectory`。 此属性将返回`Java.IO.File`表示主外部存储目录的对象。 例如，`Android.OS.Environment.ExternalStorageDirectory`可能指以下目录：

```bash
/storage/emulated/0/
```

本文档将引用公共文件存储在为外部存储的存储目录_公共\_外部\_存储_。


Android 上还支持的应用程序目录_公共\_外部\_存储_。 这些目录是完全相同的应用程序目录`_PRIVATE\_EXTERNAL\_STORAGE_`和上一节中表所述。 该方法`Android.OS.Environment.GetExternalStoragePublicDirectory(string directoryType)`将返回`Java.IO.File`公共应用程序目录所对应的对象。 `directoryType`参数是必需的参数，且不能为`null`。

例如，调用`Environment.GetExternalStoragePublicDirectory(Environment.DirectoryDocuments).AbsolutePath`将返回一个字符串，它将类似于：

```bash
/storage/emulated/0/Documents
```

> [!IMPORTANT]
> 到设备和 Android 的不同版本之间的公共外部存储目录的确切路径可以因设备。 正因为如此，应用必须未硬编码到该目录，路径并改为使用 Xamarin.Android Api，如`Android.OS.Environment.ExternalStorageDirectory`。

## <a name="working-with-external-storage"></a>使用外部存储

后一个 Xamarin.Android 应用程序已获取到文件的完整路径，它应使用任何标准的.NET Api，用于创建、 读取、 写入或删除文件。 这可最大化跨平台兼容应用程序代码的量。 但是，在尝试访问的文件之前 Xamarin.Android 应用程序必须确保这是它可以访问该文件。

1. **验证外部存储**&ndash;具体取决于外部存储的性质，很可能不可能会装入和可用的应用程序。 所有应用程序应尝试使用它之前检查外部存储的状态。
2. **执行运行时权限检查** &ndash; Android 应用程序必须以访问外部存储从用户请求权限。 这意味着，如果运行的时间应在任何文件访问权限之前执行权限请求。 本指南[权限在 Xamarin.Android](~/android/app-fundamentals/permissions.md)包含 Android 权限的详细信息。

这两项任务的每个将如下所述。

### <a name="verifying-that-external-storage-is-available"></a>验证外部存储可用

写入到外部存储之前，第一步是检查它可读或可写。 `Android.OS.Environment.ExternalStorageState`属性包含一个字符串，标识外部存储的状态。 此属性将返回一个字符串，表示状态。 此表是一系列`ExternalStorageState`可能返回的值`Environment.ExternalStorageState`:

| ExternalStorageState | 描述  |
|----------------------|---|
| MediaBadRemoval      | 媒体已突然删除不正确所造成。 |
| MediaChecking        | 媒体存在，但正在执行磁盘检查。  |
| MediaEjecting        | 媒体正在被卸载，弹出。  |
| MediaMounted         | 媒体已装载，可读取或写入。  |
| MediaMountedReadOnly | 媒体已装载，但只能从读取。 |
| MediaNofs            | 媒体存在，但不包含适用于 Android 的文件系统。 |
| MediaRemoved         | 没有存在的媒体。 |
| MediaShared          | 媒体存在，但未装入。 它是正在通过 USB 与共享另一台设备。|
| MediaUnknown         | 媒体的状态不受 Android。 |
| MediaUnmountable     | 媒体存在，但不能由 Android 装载。 |
| MediaUnmounted       | 媒体存在但未装入。 |


大多数 Android 应用只需检查是否已装入外部存储。 以下代码片段演示如何验证外部存储已装入的只读访问权限或读写访问权限：

```csharp
bool isReadonly = Environment.MediaMountedReadOnly.Equals(Environment.ExternalStorageState);
bool isWriteable = Environment.MediaMounted.Equals(Environment.ExternalStorageState);
```

## <a name="external-storage-permissions"></a>外部存储权限

Android 会考虑访问外部存储要_非常危险的权限_，这通常要求用户授予其访问资源的权限。 用户可以撤销此权限在任何时间。  这意味着，如果运行的时间应在任何文件访问权限之前执行权限请求。 应用会自动授予权限以读取和写入其自身的专用文件。 应用读取和写入后属于其他应用程序的专用文件可能[授予的权限](~/android/app-fundamentals/permissions.md)用户。

所有 Android 应用必须声明外部存储中的两个权限之一**AndroidManifest.xml** 。 若要标识的权限，以下两种状态之一`uses-permission`必须将元素添加到**AndroidManifest.xml**:

```xml
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

> [!NOTE]
> 如果用户授予`WRITE_EXTERNAL_STORAGE`，然后`READ_EXTERNAL_STORAGE`也是隐式授予。 不需要请求中的这两个权限**AndroidManifest.xml**。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

此外可以使用添加的权限**Android 清单**选项卡**解决方案属性**:

![解决方案资源管理器-Visual Studio 所需的权限](./images/required-permissions.w157.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

此外可以使用添加的权限**Android 清单**选项卡**解决方案属性板**:

[![Solution Pad 适用于 Mac 的 Visual Studio 所需权限](./images/required-permissions.m752-sml.png)](./images/required-permissions.m752.png#lightbox)

-----

通常情况下，用户必须批准所有危险的权限。 外部存储的权限，因为此规则，具体取决于应用程序运行版本的例外情况是 android 的异常：

![外部存储权限检查的流程图](./images/external-permission-check-flowchart.png)

有关执行运行时权限请求的详细信息，请参阅指南[权限在 Xamarin.Android](~/android/app-fundamentals/permissions.md)。 **Monodroid 示例** [LocalFiles](https://github.com/xamarin/monodroid-samples/tree/master/LocalFiles)还演示了一种方法的执行运行时权限检查。

#### <a name="granting-and-revoking-permissions-with-adb"></a>授予和撤消权限与 ADB

在过程中开发 Android 应用程序，它可能需要授权或撤销权限来测试各种工作流所涉及的运行时权限检查。 就可以在使用 ADB 命令提示符下执行此操作。 以下命令行代码片段演示了如何授予或撤消权限的 Android 应用包名称是使用 ADB **com.companyname.app**:

```bash
$ adb shell pm grant com.companyname.app android.permission.WRITE_EXTERNAL_STORAGE

$ adb shell pm revoke com.companyname.app android.permission.WRITE_EXTERNAL_STORAGE
```

## <a name="deleting-files"></a>正在删除文件

任何可以使用 C# Api 从外部存储，如删除文件的标准[ `System.IO.File.Delete` ](xref:System.IO.File.Delete*)。 还有可能要使用 Java Api，但要牺牲代码可移植性。 例如：

```csharp
System.IO.File.Delete("/storage/emulated/0/Android/data/com.companyname.app/files/count.txt");
```

## <a name="related-links"></a>相关链接

* [Xamarin.Android 本地文件示例上**monodroid 示例**](https://github.com/xamarin/monodroid-samples/tree/master/LocalFiles)
* [在 Xamarin.Android 中的权限](~/android/app-fundamentals/permissions.md)
