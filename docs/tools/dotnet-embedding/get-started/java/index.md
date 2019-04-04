---
title: 开始使用 Java
description: 本文档介绍如何开始使用.NET 嵌入与 Java 配合使用。 它讨论的系统要求、 安装和支持的平台。
ms.prod: xamarin
ms.assetid: B9A25E9B-3EC2-489A-8AD3-F78287609747
author: lobrien
ms.author: laobri
ms.date: 03/28/2018
ms.openlocfilehash: 79a483743946c4f7509833867f2afe4b1e055183
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57667173"
---
# <a name="getting-started-with-java"></a>开始使用 Java

这是适用于 Java，介绍了所有支持平台的基础知识的入门页。

## <a name="requirements"></a>要求

若要使用 Java 将需要使用.NET 嵌入：

* Java 1.8 或更高版本
* [Mono 5.0](https://www.mono-project.com/download/)

为 Mac:

* Xcode 8.3.2 或更高版本

对于 Windows:

* 使用 c + + 支持 visual Studio 2017
* Windows 10 SDK

对于 Android:

* [Xamarin.Android 7.5](https://visualstudio.microsoft.com/xamarin/)或更高版本
* [Android Studio 3.x](https://developer.android.com/studio/index.html)使用 Java 1.8

可以使用[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)来编辑和编译在C#代码。

> [!NOTE]
> 早期版本的 Xcode、 Visual Studio、 Xamarin.Android、 Android Studio 中和 Mono_可能_起作用，而是经过测试，不受支持。

## <a name="installation"></a>安装

.NET 嵌入是上当前可用[NuGet](https://www.nuget.org/packages/Embeddinator-4000/):

```shell
nuget install Embeddinator-4000
```

这会将**Embeddinator 4000.exe**成**包/Embeddinator 4000/工具**目录。

此外，您可以构建.NET 嵌入来自源，请参阅我们[git 存储库](https://github.com/mono/Embeddinator-4000/)并[参与](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)文档有关的说明。

## <a name="platforms"></a>平台

Java 目前处于预览状态时适用于 macOS、 Windows 和 Android。

通过选择平台`--platform=<platform>`嵌入.NET 工具的命令行参数。 目前`macOS`， `Windows`，和`Android`支持。

### <a name="macos-and-windows"></a>macOS 和 Windows

对于开发，您应能够使用任何支持的 Java 1.8 的 Java IDE。 您甚至可以使用 Android Studio 中为此必要时，[在这里看到](https://stackoverflow.com/questions/16626810/can-android-studio-be-used-to-run-standard-java-projects)。 也可以是任何标准的 Java jar 文件，可以使用 JAR 文件输出。

### <a name="android"></a>Android

请确保你已设置为之前尝试创建一个开发 Android 应用程序使用.NET 嵌入。 [按照](~/tools/dotnet-embedding/get-started/java/android.md)假定您已成功地构建和部署 Android 应用程序从您的计算机。

Android Studio 建议进行开发，但其他 Ide，只要没有针对支持应适用[AAR 文件格式](https://developer.android.com/studio/projects/android-library.html)。

## <a name="further-reading"></a>其他阅读材料

* [Android 上开始使用](~/tools/dotnet-embedding/get-started/java/android.md)
* [在 Android 上的回调](~/tools/dotnet-embedding/android/callbacks.md)
* [Android 的初步研究](~/tools/dotnet-embedding/android/index.md)
* [.NET 嵌入限制](~/tools/dotnet-embedding/limitations.md)
* [参与到开放源代码项目](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)
* [错误代码和说明](~/tools/dotnet-embedding/errors.md)
