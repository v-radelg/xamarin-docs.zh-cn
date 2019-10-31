---
title: APK 扩展文件
ms.prod: xamarin
ms.assetid: DB7E38E8-3C4E-5191-27EA-22DE63044FE2
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 712322435614966348fc5c10cabf724870c307e4
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73021293"
---
# <a name="apk-expansion-files"></a>APK 扩展文件

某些应用程序（例如一些游戏）需要的资源和资产超出了 Google Play 规定的最大 Android 应用大小限制。 此限制取决于 APK 所适用的 Android 版本：

- 适用于 Android 4.0 或更高版本（API 级别 14 或更高）的 APK 的限制为 100MB。
- 适用于 Android 3.2 或更低版本（API 级别 13 或更高）的 APK 的限制为 50MB。

若要克服此限制，Google Play 将承载和分发 APK 随附的两个扩展文件  ，使应用程序可直接超过此限制。 

大多数设备上，下载应用程序后，扩展文件会随 APK 一并下载，并保存到设备上的共享存储位置（SD 卡或可安装 USB 的分区）。 在少数旧版设备上，扩展文件可能不会随 APK 一并自动安装。 这些情况下，应用程序有必要包含用户首次运行应用程序时要下载扩展文件的代码。

扩展文件会被视为不透明二进制 blob (obb)  ，其大小最大为 2GB。 这些文件下载后，Android 不会对其执行任何特殊处理 &ndash; 这些文件可以采用适合相应应用程序的任何格式。 从概念上讲，推荐的扩展文件方式应如下所示：

-  主扩展 &ndash; 此文件是不适合 APK 大小限制的资源和资产的主扩展文件。 主扩展文件应包含应用程序所需的主资产，并且应很少进行更新。
-  修补扩展 &ndash; 这适用于对主扩展文件进行少量更新。 此文件可更新。 应用程序负责从此文件执行任何必要的修补或更新。

上传 APK 的同时必须上传相应扩展文件。
Google Play 不允许向现有 APK 上传扩展文件或者不允许上传现有 APK。 如果有必要更新扩展文件，则必须上传新的 APK，同时更新 `versionCode`。

## <a name="expansion-file-storage"></a>扩展文件存储

文件下载到设备后，会存储在 shared-store/Android/obb/package-name 中   ：

-  shared-store &ndash; 这是 `Android.OS.Environment.ExternalStorageDirectory` 指定的目录。
-  package-name &ndash; 这是应用程序 Java 样式的包名称。

下载完成后，不应移动、更改、重命名扩展文件或从设备上的位置中删除扩展文件。 否则会导致再次下载扩展文件，这样会删除旧文件。 此外，扩展文件目录应仅包含扩展包文件。

扩展文件未对内容提供任何安全保护 &ndash; 其他应用程序或用户可访问存储在共享存储上的任何文件。

如果需要解压缩扩展文件，则解压缩文件应存储在单独的目录中，例如 `Android.OS.Environment.ExternalStorageDirectory` 中的一个目录。

从扩展文件提取文件的另一方法是直接从扩展文件读取资产或资源。 扩展文件只是可通过合适的 `ContentProvider` 进行使用的 zip 文件。 [Android.Play.ExpansionLibrary](https://github.com/mattleibow/Android.Play.ExpansionLibrary) 包含的程序集 [System.IO.Compression.Zip](https://github.com/mattleibow/Android.Play.ExpansionLibrary/tree/master/System.IO.Compression.Zip) 中包括了一个允许对某些媒体文件直接进行文件访问的 `ContentProvider`。 如果将媒体文件打包为 zip 文件，媒体播放调用可能直接使用 zip 中的文件而无需解压缩 zip 文件。 添加到 zip 文件时不应压缩媒体文件。 

### <a name="filename-format"></a>文件名格式

下载扩展文件后，Google Play 会使用下面的方案来对扩展进行命名：

```
[main|patch].<expansion-version>.<package-name>.obb
```

此方案的三个组件包括：

- `main` 或 `patch` &ndash; 这会指定是主扩展文件还是修补扩展文件。 二者只能选其一。
- `<expansion-version>` &ndash; 这是一个整数，该整数与文件首次关联的 APK 的 `versionCode` 匹配。
- `<package-name>` &ndash; 这是应用程序的 Java 样式包名称。

例如，如果 APK 版本是 21，包名称是 `mono.samples.helloworld`，则主扩展文件应命名为 main.21.mono.samples.helloworld  。

## <a name="download-process"></a>下载过程

从 Google Play 安装应用程序时，扩展文件会随 APK 一并下载和保存。 在某些情况下可能有所例外，或者扩展文件可能会被删除。 为处理这种情况，应用需检查扩展文件是否存在，然后根据需要进行下载。 下方流程图显示了此过程的推荐流程：

[![APK 扩展流程图](apk-expansion-files-images/apkexpansion.png)](apk-expansion-files-images/apkexpansion.png#lightbox)

应用程序启动时，应检查当前设备上是否存在合适的扩展文件。 如果不存在，则应用程序必须在 Google Play 的[应用程序授权](https://developer.android.com/google/play/licensing/index.html)中作出请求。 使用许可验证库 (LVL)  执行该检查，并且免费或许可应用程序都必须执行该检查。 LVL 主要由付费应用程序用于实施许可证限制。 但是，Google 已扩展 LVL，使其也可用于扩展库。 免费应用程序必须执行 LVL 检查，但可以忽略许可证限制。 LVL 请求负责提供以下有关应用程序所需扩展文件的信息： 

-  文件大小 &ndash; 扩展文件的文件大小作为检查的一部分，可用于确定是否已经下载正确的扩展文件。
-  文件名 &ndash; 这是扩展包必须保存到其下的文件名（当前设备上）。
-  下载 URL &ndash; 用于下载扩展包的 URL。 每次下载所用的 URL 都是唯一的，提供之后很快会过期。

执行 LVL 检查后，应用程序应会下载扩展文件，对于下载过程，请考虑到以下几点：

- 设备可能没有足够的空间来存储扩展文件。
- 如果 Wi-Fi 不可用，应允许用户暂停或取消下载，以避免产生不需要的数据费用。
- 在后台下载扩展文件以避免阻止用户交互。
- 在后台进行下载时，应显示进度指示。
- 下载期间出现的错误可轻松进行处理和恢复。

## <a name="architectural-overview"></a>体系结构概述

主活动启动时，会检查是否已下载了扩展文件。 如果已下载文件，则必须检查其有效性。

如果未下载扩展文件，或者当前文件无效，则必须下载新的扩展文件。 创建绑定服务作为应用程序的一部分。 应用程序主活动启动时，会使用绑定服务根据 Google 授权服务执行检查，以找出要下载文件的文件名和 URL。 绑定服务然后会在后台线程下载文件。

为减轻将扩展文件集成到应用程序所需的工作量，Google 在 Java 中创建了几个库。 这些库包括：

-  下载程序库 &ndash; 该库可减少将扩展文件集成到应用程序所需的工作量。 该库会在后台服务下载扩展文件、显示用户通知、处理网络连接问题、恢复下载以及执行其他任务。
-  许可验证库 (LVL) &ndash; 该库用于执行和处理对应用程序授权服务的调用。 也可用于执行授权检查，以检查应用程序是否经授权在设备上使用。
-  APK 扩展 Zip 库（可选）&ndash; 如果扩展文件在 zip 文件中，则该库会作为内容提供程序，允许应用程序直接从 zip 文件读取资源和资产，而无需展开 zip 文件。

这些库已经移植到 C#，可用于 Apache 2.0 许可证。 若要将扩展文件快速集成到现有应用程序，可将这些库添加到现有 Xamarin.Android 应用程序。 GitHub 上的 [Android.Play.ExpansionLibrary](https://github.com/mattleibow/Android.Play.ExpansionLibrary) 中提供有相应代码。
