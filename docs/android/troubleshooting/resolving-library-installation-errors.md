---
title: 解决库安装错误
description: 在某些情况下，可能会在安装 Android 支持库时出现错误。 本指南为一些常见错误提供了解决方法。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 2AE68ACE-8496-445D-BF17-5E4097D4AE35
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/14/2018
ms.openlocfilehash: 41f207f5a7f7b861a3406572372ead31c957c849
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73026720"
---
# <a name="resolving-library-installation-errors"></a>解决库安装错误

_在某些情况下，可能会在安装 Android 支持库时出现错误。本指南为一些常见错误提供了解决方法。_

## <a name="overview"></a>概述

生成 Xamarin Android 应用项目时，可能会在 Visual Studio 或 Visual Studio for Mac 尝试下载和安装依赖项库时出现生成错误。 其中许多错误都是由于网络连接问题、文件损坏或版本控制问题导致的。 本指南介绍最常见的支持库安装错误，并提供解决这些问题的步骤，并重新生成应用程序项目。 

## <a name="errors-while-downloading-m2repository"></a>下载 m2Repository 时出错

引用 Android 支持库的 NuGet 包或 Google Play 服务时，可能会出现**m2repository**错误。 该错误消息类似于以下内容：

```shell
Download failed. Please download https://dl-ssl.google.com/android/repository/android_m2repository_r16.zip and extract it to the C:\Users\mgm\AppData\Local\Xamarin\Android.Support.v4\22.2.1\content directory.
```

此示例适用于**android\_m2repository\_r16**，但对于不同版本（例如**android\_m2repository\_r18**或**android\_m2repository\_r25**），可能会看到此相同的错误消息。 

### <a name="automatic-recovery-from-m2repository-errors"></a>M2repository 错误的自动恢复 

通常，可以通过删除有问题的库并根据以下步骤重新生成来解决此问题： 

1. 导航到计算机上的支持库目录：

    - 在 Windows 上，支持库位于**C：\\用户\\_用户名_\\AppData\\本地\\Xamarin**。 

    - Mac OS X 上，支持库位于 **/Users/_username_/.local/share/Xamarin**。 

2. 找到对应于错误消息的库和版本文件夹。 例如，上述错误消息的库和版本文件夹位于**Android. 支持\\22.2.1**：

    [22.2.1 支持库的![示例文件夹位置](resolving-library-installation-errors-images/01-example-location.png)](resolving-library-installation-errors-images/01-example-location.png#lightbox)

3. 删除版本文件夹的内容。 请确保删除该 **.zip**文件以及此文件夹中的**内容**和**嵌入**的子目录。 对于上面所示的示例错误消息，将删除此屏幕截图中显示的文件和子目录（**content**、 **embedded**和**android_m2repository_r16**）：

    [22.2.1 支持库文件夹的![示例内容](resolving-library-installation-errors-images/02-example-folder-vs.png)](resolving-library-installation-errors-images/02-example-folder-vs.png#lightbox)

   请注意，删除此文件夹的*全部*内容非常重要。 尽管此文件夹最初可能包含缺少**android\_m2repository\_r16**文件，但此文件可能已部分下载或已损坏。

4. 重新生成项目 &ndash; 执行此操作将导致生成过程重新下载缺少的库。

在大多数情况下，这些步骤将解决生成错误并允许继续。 如果删除此库不能解决生成错误，则必须按照下一部分中所述，手动下载并安装**android\_m2repository\_r_nn_** 文件。 

### <a name="manually-downloading-m2repository"></a>手动下载 m2repository

如果尝试使用上述自动恢复步骤但仍有生成错误，则可以手动下载**android\_m2repository\_r_nn_** 文件（使用 web 浏览器）并根据以下步骤安装它。 如果你的开发计算机上没有 internet 访问权限，但你可以使用其他计算机下载存档，则此过程也很有用。 

1. 下载与错误消息相对应的**android\_m2repository\_r_nn_** 文件 &ndash; 以下列表中提供了链接（连同每个链接的 URL 对应的 MD5 哈希）：

    - [android\_m2repository\_r33](https://dl-ssl.google.com/android/repository/android_m2repository_r33.zip) &ndash; 5FB756A25962361D17BBE99C3B3FCC44

    - [android\_m2repository\_r32](https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip) &ndash; F16A3455987DBAE5783F058F19F7FCDF

    - [android\_m2repository\_r31](https://dl-ssl.google.com/android/repository/android_m2repository_r31.zip) &ndash; 99A8907CE2324316E754A95E4C2D786E

    - [android\_m2repository\_r30](https://dl-ssl.google.com/android/repository/android_m2repository_r30.zip) &ndash; 05AD180B8BDC7C21D6BCB94DDE7F2C8F

    - [android\_m2repository\_r29](https://dl-ssl.google.com/android/repository/android_m2repository_r29.zip) &ndash; 2A3A8A6D6826EF6CC653030E7D695C41

    - [android\_m2repository\_r28](https://dl-ssl.google.com/android/repository/android_m2repository_r28.zip) &ndash; 17BE247580748F1EDB72E9F374AA0223

    - [android\_m2repository\_r27](https://dl-ssl.google.com/android/repository/android_m2repository_r27.zip) &ndash; C9FD4FCD69D7D12B1D9DF076B7BE4E1C

    - [android\_m2repository\_r26](https://dl-ssl.google.com/android/repository/android_m2repository_r26.zip) &ndash; 8157FC1C311BB36420C1D8992AF54A4D

    - [android\_m2repository\_r25](https://dl-ssl.google.com/android/repository/android_m2repository_r25.zip) &ndash; 0B3F1796C97C707339FB13AE8507AF50

    - [android\_m2repository\_r24](https://dl-ssl.google.com/android/repository/android_m2repository_r24.zip) &ndash; 8E3C9EC713781EDFE1EFBC5974136BEA

    - [android\_m2repository\_r23](https://dl-ssl.google.com/android/repository/android_m2repository_r23.zip) &ndash; D5BB66B3640FD9B9C6362C9DB5AB0FE7

    - [android\_m2repository\_r22](https://dl-ssl.google.com/android/repository/android_m2repository_r22.zip) &ndash; 96659D653BDE0FAEDB818170891F2BB0

    - [android\_m2repository\_r21](https://dl-ssl.google.com/android/repository/android_m2repository_r21.zip) &ndash; CD3223F2EFE068A26682B9E9C4B6FBB5

    - [android\_m2repository\_r20](https://dl-ssl.google.com/android/repository/android_m2repository_r20.zip) &ndash; 650E58DF02DB1A832386FA4A2DE46B1A

    - [android\_m2repository\_r19](https://dl-ssl.google.com/android/repository/android_m2repository_r19.zip) &ndash; 263B062D6EFAA8AEE39E9460B8A5851A

    - [android\_m2repository\_r18](https://dl-ssl.google.com/android/repository/android_m2repository_r18.zip) &ndash; 25947AD38DCB4865ABEB61522FAFDA0E

    - [android\_m2repository\_r17](https://dl-ssl.google.com/android/repository/android_m2repository_r17.zip) &ndash; 49054774F44AE5F35A6BA9D3C117EFD8

    - [android\_m2repository\_r16](https://dl-ssl.google.com/android/repository/android_m2repository_r16.zip) &ndash; 0595E577D19D31708195A83087881EE6

    如果未在此表中显示**m2repository**存档，则可以通过在要下载的**m2repository**的名称前面加 **https://dl-ssl.google.com/android/repository/** 来创建下载 URL。 例如，使用 **https://dl-ssl.google.com/android/repository/android\_m2repository\_r10.zip** 下载**android\_ m2repository\_ r10**。

2. 将该文件重命名为下载 URL 对应的 MD5 哈希，如上表所示。 例如，如果下载了**android\_m2repository\_r25**，请将其重命名为**0B3F1796C97C707339FB13AE8507AF50**。 如果在表中未显示已下载文件的下载 URL 的 MD5 哈希，则可以使用[联机 md5 生成器](http://www.webconfs.com/online-md5-generator.php)将 URL 转换为 MD5 哈希字符串。 

3. 将该文件复制到 Xamarin **zips**文件夹： 

    - 在 Windows 上，此文件夹位于**C：\\用户\\***用户名***\\AppData\\本地\\Xamarin\\zips**。 

    - 在 Mac OS X 上，此文件夹位于 **/Users/***username***/.local/share/Xamarin/zips**。 

    例如，以下屏幕截图说明了在将**android\_m2repository\_r16**下载并重命名为它在 Windows 上下载 URL 的 MD5 哈希时的结果：

    [要重命名为0595E577D19D31708195A83087881EE6 的 r16 存储库![示例](resolving-library-installation-errors-images/03-md5-rename-vs.png)](resolving-library-installation-errors-images/03-md5-rename-vs.png#lightbox)

如果此过程无法解决生成错误，则必须手动下载**android\_m2repository\_r_nn_** 文件，将其解压缩，然后安装其内容，如以下部分所述。 

### <a name="manually-downloading-and-installing-m2repository-files"></a>手动下载和安装 m2repository 文件

从**m2repository**错误中恢复的完全手动过程需要下载**android\_m2repository\_r_nn_** 文件（使用 web 浏览器），将其解压缩，然后将其内容复制到支持库目录在计算机上。 在下面的示例中，我们将从此错误消息中恢复： 

```shell
Unzipping failed. Please download https://dl-ssl.google.com/android/repository/android_m2repository_r25.zip and extract it to the C:\Users\mgm\AppData\Local\Xamarin\Android.Support.v4\23.1.1\content directory.
```

使用以下步骤下载**m2repository**并安装其内容：

1. 删除对应于错误消息的库文件夹的内容。 例如，在上面的错误消息中，你将删除**C：\\用户\\***用户名***\\AppData\\本地\\Xamarin\\\\23.1.1.0**的内容。 
    如前文所述，必须删除此目录的全部内容：

    [![从23.1.1.0 文件夹删除 content、embedded 和 android_m2repository 文件夹](resolving-library-installation-errors-images/04-delete-contents-vs.png)](resolving-library-installation-errors-images/04-delete-contents-vs.png#lightbox)

2. 从 Google 下载与错误消息对应的**android\_m2repository\_r_nn_** 文件（请参阅上一节中的链接）。

3. 将此 **.zip**存档提取到任何位置（如桌面）。 这应创建与 **.zip**存档的名称对应的目录。 在此目录中，应找到名为**m2repository**的子目录： 

    [在提取的 zip 存档中找到![m2repository 文件夹](resolving-library-installation-errors-images/05-m2repository-vs.png)](resolving-library-installation-errors-images/05-m2repository-vs.png#lightbox)

4. 在步骤1中清除的版本控制库目录中，重新创建**内容**和**嵌入**的子目录。 例如，以下屏幕截图说明了在**android\_m2repository\_r25**的**23.1.1.0**文件夹中创建的**内容**和**嵌入**子目录： 

    [![在23.1.1.0 文件夹中创建内容和嵌入文件夹](resolving-library-installation-errors-images/06-recreate-folders-vs.png)](resolving-library-installation-errors-images/06-recreate-folders-vs.png#lightbox)

5. 将**m2repository**从提取的 **.zip**复制到你在上一步中创建的**内容**目录中： 

    [复制到 23.1.1.0/content 文件夹的 m2repository 的![屏幕快照](resolving-library-installation-errors-images/07-copied-m2repository-vs.png)](resolving-library-installation-errors-images/07-copied-m2repository-vs.png#lightbox)

6. 在提取的 **.zip**目录中，浏览到**m2repository\\com\\android\\支持\\支持-v4** ，并打开与上面创建的版本号相对应的文件夹（在本例中为**23.1.1**）：

    [支持-v4/23.1.1 文件夹中包含的文件的![示例列表](resolving-library-installation-errors-images/08-zip-contents-vs.png)](resolving-library-installation-errors-images/08-zip-contents-vs.png#lightbox)

7. 将此文件夹中的所有文件复制**到在步骤**4：

    [复制到 23.1.1.0/embedded 文件夹的文件![示例](resolving-library-installation-errors-images/09-copied-vs.png)](resolving-library-installation-errors-images/09-copied-vs.png#lightbox)

8. 验证是否复制了所有文件。 **嵌入**目录现在应包含文件，如 **.jar**、 **. aar**和**pom**。

9. 将**aar**文件的内容解压缩到**嵌入**目录。 在 Windows 上，将 **.zip**扩展名附加到**aar**文件，将其打开，然后将内容复制到**嵌入**的目录。
    在 macOS 上，使用终端中的 "**解压缩**" 命令解压缩**aar**文件（例如，**解压缩 aar**）。

此时，你已经手动安装了缺少的组件，并且你的项目应在生成时不会出现任何错误。 否则，请验证是否已下载与错误消息中的版本完全对应的**m2repository** **存档版本**，并验证是否已将其内容安装到正确位置（如上述步骤所述）。 

## <a name="summary"></a>总结 

本文介绍了如何从可在依赖项库的自动下载和安装过程中发生的常见错误中进行恢复。 本文介绍了如何删除有问题的库，以及如何重新生成项目，以便重新下载并重新安装库。 本文介绍了如何下载库并将其安装在**zips**文件夹中。 此外，还介绍了如何手动下载和安装所需文件的更多相关过程，以解决无法通过自动方式解决的问题。 
