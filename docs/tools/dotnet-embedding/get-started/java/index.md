---
title: Java 入门
description: 本文档介绍如何开始在 Java 中使用 .NET 嵌入。 它讨论了系统要求、安装和受支持的平台。
ms.prod: xamarin
ms.assetid: B9A25E9B-3EC2-489A-8AD3-F78287609747
author: davidortinau
ms.author: daortin
ms.date: 03/28/2018
ms.openlocfilehash: 09ea33724c2b1184654ce7768ea1cb2525b62c28
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73007367"
---
# <a name="getting-started-with-java"></a>Java 入门

这是 Java 入门页面，涵盖所有支持的平台的基础知识。

## <a name="requirements"></a>要求

若要将 .NET 嵌入到 Java，你将需要：

* Java 1.8 或更高版本
* [Mono 5。0](https://www.mono-project.com/download/)

对于 Mac：

* Xcode 8.3.2 或更高版本

对于 Windows：

* Visual Studio 2017 C++支持
* Windows 10 SDK

对于 Android：

* [Xamarin 7.5](https://visualstudio.microsoft.com/xamarin/)或更高版本
* [Android Studio](https://developer.android.com/studio/index.html) 1.X 与 Java 1。8

您可以使用[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)来编辑和编译您C#的代码。

> [!NOTE]
> 早期版本的 Xcode、Visual Studio、Xamarin、Android Studio 和 Mono_可能_有效，但未经测试且不受支持。

## <a name="installation"></a>安装

.NET 嵌入目前在[NuGet](https://www.nuget.org/packages/Embeddinator-4000/)上提供：

```shell
nuget install Embeddinator-4000
```

这会将**Embeddinator-4000**放入**包/Embeddinator/工具**目录。

此外，还可以从源代码生成 .NET 嵌入，请参阅[git 存储库](https://github.com/mono/Embeddinator-4000/)和提供[的文档以](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)获取说明。

## <a name="platforms"></a>平台

Java 当前为 macOS、Windows 和 Android 的预览状态。

平台是通过将 `--platform=<platform>` 命令行参数传递给 .NET 嵌入工具来选择的。 目前支持 `macOS`、`Windows`和 `Android`。

### <a name="macos-and-windows"></a>macOS 和 Windows

对于开发，你应该能够使用支持 Java 1.8 的任何 Java IDE。 你甚至可以在需要时使用 Android Studio，[请参阅此处](https://stackoverflow.com/questions/16626810/can-android-studio-be-used-to-run-standard-java-projects)。 可以像使用任何标准 Java JAR 文件一样使用 JAR 文件输出。

### <a name="android"></a>Android

请确保已设置为开发 Android 应用程序，然后再尝试使用 .NET 嵌入进行创建。 [以下说明](~/tools/dotnet-embedding/get-started/java/android.md)假定你已成功从计算机生成并部署了 Android 应用程序。

建议 Android Studio 进行开发，但只要支持[AAR 文件格式](https://developer.android.com/studio/projects/android-library.html)，其他 ide 就会正常运行。

## <a name="further-reading"></a>其他阅读材料

* [Android 上的入门](~/tools/dotnet-embedding/get-started/java/android.md)
* [Android 上的回调](~/tools/dotnet-embedding/android/callbacks.md)
* [Android 初步研究](~/tools/dotnet-embedding/android/index.md)
* [.NET 嵌入限制](~/tools/dotnet-embedding/limitations.md)
* [参与开源项目](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)
* [错误代码和描述](~/tools/dotnet-embedding/errors.md)
