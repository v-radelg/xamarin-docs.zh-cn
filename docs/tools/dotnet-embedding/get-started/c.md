---
title: C 入门
description: 本文档介绍如何使用.NET 嵌入 C 应用程序中嵌入.NET 代码。 介绍了如何使用.NET 嵌入在 Visual Studio 2017 和 Visual Studio for mac。
ms.prod: xamarin
ms.assetid: 2A27BE0F-95FB-4C3A-8A43-72540179AA85
author: lobrien
ms.author: laobri
ms.date: 04/19/2018
ms.openlocfilehash: 66475b7629f01b4229539e1edc323491a7861dfe
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57668395"
---
# <a name="getting-started-with-c"></a>C 入门

## <a name="requirements"></a>要求

若要使用.NET 嵌入 C，您将需要 Mac 或 Windows 计算机正在运行：

### <a name="macos"></a>macOS

* macOS 10.12 (Sierra) 或更高版本
* Xcode 8.3.2 或更高版本
* [Mono](https://www.mono-project.com/download/)

### <a name="windows"></a>Windows

* Windows 7、 8、 10 或更高版本
* Visual Studio 2015 或更高版本

## <a name="installing-net-embedding-from-nuget"></a>.NET 嵌入从 NuGet 安装

请按照这些[说明](~/tools/dotnet-embedding/get-started/install/install.md)来安装和配置.NET 嵌入为你的项目。

命令调用应配置将如下所示 （可能具有不同的版本号和路径）：

### <a name="visual-studio-for-mac"></a>Visual Studio for Mac

```shell
mono {SolutionDir}/packages/Embeddinator-4000.0.4.0.0/tools/Embeddinator-4000.exe --gen=c --outdir=managed_c --platform=macos --compile managed.dll
```

### <a name="visual-studio-2017"></a>Visual Studio 2017

```shell
$(SolutionDir)\packages\Embeddinator-4000.0.2.0.80\tools\Embeddinator-4000.exe --gen=c --outdir=managed_c --platform=windows --compile managed.dll
```

## <a name="generation"></a>代

### <a name="output-files"></a>输出文件

如果一切顺利，您将看到与以下输出：

```shell
Parsing assemblies...
    Parsed 'managed.dll'
Processing assemblies...
Generating binding code...
    Generated: managed.h
    Generated: managed.c
    Generated: mscorlib.h
    Generated: mscorlib.c
    Generated: embeddinator.h
    Generated: glib.c
    Generated: glib.h
    Generated: mono-support.h
    Generated: mono_embeddinator.c
    Generated: mono_embeddinator.h
```

由于`--compile`标志传递给该工具，.NET 嵌入应也已编译输出文件到共享库，可找到生成的文件旁边， **libmanaged.dylib**文件在 macOS 和**managed.dll**在 Windows 上。

若要使用共享的库，可以包括**managed.h** C 头文件，它提供对应于相应的 C 声明托管库 Api 并使用前面提到的链接编译共享的库。

## <a name="further-reading"></a>其他阅读材料

* [.NET 嵌入限制](~/tools/dotnet-embedding/limitations.md)
* [参与到开放源代码项目](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)
* [错误代码和说明](~/tools/dotnet-embedding/errors.md)
