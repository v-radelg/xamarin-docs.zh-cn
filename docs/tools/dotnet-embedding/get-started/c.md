---
title: C 入门
description: 本文档介绍如何在 C 应用程序中使用 .NET 嵌入嵌入 .NET 代码。 本文介绍了如何在 Visual Studio 2019 和 Visual Studio for Mac 中使用 .NET 嵌入。
ms.prod: xamarin
ms.assetid: 2A27BE0F-95FB-4C3A-8A43-72540179AA85
author: conceptdev
ms.author: crdun
ms.date: 04/19/2018
ms.openlocfilehash: 1dc68a709f8e1f864961bbe87af112b648b0dd2a
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70278738"
---
# <a name="getting-started-with-c"></a>C 入门

## <a name="requirements"></a>要求

若要将 .NET 嵌入到 C，你将需要运行的 Mac 或 Windows 计算机：

### <a name="macos"></a>macOS

* macOS 10.12 （塞拉利昂）或更高版本
* Xcode 8.3.2 或更高版本
* [Mono](https://www.mono-project.com/download/)

### <a name="windows"></a>Windows

* Windows 7、8、10或更高版本
* Visual Studio 2015 或更高版本

## <a name="installing-net-embedding-from-nuget"></a>从 NuGet 安装 .NET 嵌入

按照以下[说明](~/tools/dotnet-embedding/get-started/install/install.md)安装和配置项目的 .net 嵌入。

应配置的命令调用将类似于（可能采用不同的版本号和路径）：

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

如果一切顺利，您将看到以下输出：

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

由于已`--compile`将标志传递给工具，.net 嵌入操作还应将输出文件编译为共享库，可在生成的文件、macOS 上的**libmanaged 和 .dylib**文件（上的文件）和 Windows 上的**托管 .dll**中查找。

若要使用共享库，可以包含托管的 **.h** c 头文件，该文件提供与各自的托管库 api 对应的 C 声明，并与前面提到的编译共享库链接。

## <a name="further-reading"></a>其他阅读材料

* [.NET 嵌入限制](~/tools/dotnet-embedding/limitations.md)
* [参与开源项目](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)
* [错误代码和描述](~/tools/dotnet-embedding/errors.md)
