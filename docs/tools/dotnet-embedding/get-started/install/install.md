---
title: 安装.NET 嵌入
description: 本文档介绍如何安装.NET 嵌入。 它讨论了如何手动运行此工具如何生成绑定自动，如何使用自定义 MSBuild 目标和必要的生成后步骤。
ms.prod: xamarin
ms.assetid: 47106AF3-AC6E-4A0E-B30B-9F73C116DDB3
author: chamons
ms.author: chhamo
ms.date: 04/18/2018
ms.openlocfilehash: 2a572748c21d2a640add3346d1162f4b6bdc8e99
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61076584"
---
# <a name="installing-net-embedding"></a>安装.NET 嵌入

## <a name="installing-net-embedding-from-nuget"></a>.NET 嵌入从 NuGet 安装

选择**添加 > 添加 NuGet 包...** 并安装**Embeddinator 4000**从 NuGet 包管理器：

![NuGet 程序包管理器](images/visualstudionuget.png)

这将安装**Embeddinator 4000.exe**并**objcgen**到**包/Embeddinator 4000/工具**目录。

一般情况下，应下载选择 Embeddinator 4000 的最新版本。 Objective C 的支持，需要 0.4 或更高版本。

## <a name="running-manually"></a>手动运行

安装 NuGet 后，可以手动运行此工具。

- 打开终端 (macOS) 或命令提示符 (Windows)
- 将目录更改为解决方案根目录
- 在安装工具：
    - **。 / 包/Embeddinator 4000。[VERSION] / tools/objcgen** (Objective C)
    - **./packages/Embeddinator-4000.[VERSION]/tools/Embeddinator-4000.exe** (Java/C)
- 在 macOS 上， **objcgen**可以直接运行。
- 在 Windows 中， **Embeddinator 4000.exe**可以直接运行。
- 在 macOS 上， **Embeddinator 4000.exe**需要运行与**mono**:
    - `mono ./packages/Embeddinator-4000.[VERSION]/tools/Embeddinator-4000.exe`

每个命令调用将需要大量特定于平台的文档中列出的参数。

## <a name="automatic-binding-generation"></a>自动绑定生成

有两种方法来自动运行.NET 嵌入的生成过程的一部分。

- 自定义 MSBuild 目标
- 生成后步骤

虽然本文档将介绍两者，使用自定义 MSBuild 目标是首选。 从命令行生成时，将不一定是运行后生成步骤。

### <a name="custom-msbuild-targets"></a>自定义 MSBuild 目标

若要自定义 MSbuild 目标与你的生成，请先创建**Embeddinator 4000.targets**旁边如下所示在 csproj 文件：

```xml
<Project DefaultTargets="Build" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
    <Target Name="RunEmbeddinator" AfterTargets="AfterBuild" Inputs="$(OutputPath)/$(AssemblyName).dll" Outputs="$(IntermediateOutputPath)/Embeddinator/$(AssemblyName).framework/$(AssemblyName)">
        <Exec Command="" />
    </Target>
</Project>
```

在这里，应使用一个特定于平台的文档中列出的.NET 嵌入调用填充命令的文本。

若要使用此目标：

- 关闭你的项目在 Visual Studio 2017 或 Visual Studio for Mac
- 在文本编辑器中打开库 csproj
- 在底部，最后一个上面添加以下行`</Project>`行：

```xml
 <Import Project="Embeddinator-4000.targets" />
```

- 重新打开你的项目

### <a name="post-build-steps"></a>生成后步骤

若要添加后期生成步骤来运行.NET 嵌入的步骤根据 IDE 而有所不同：

#### <a name="visual-studio-for-mac"></a>Visual Studio for Mac

在 Visual Studio for Mac 中，转到**项目选项 > 生成 > 自定义命令**并添加**后生成**步骤。

设置特定于平台的文档中列出的命令。

> [!NOTE]
> 请务必使用从 NuGet 安装的版本号

如果要在执行正在进行开发C#项目中，还可以添加自定义的命令以清理**输出**目录之前运行.NET 嵌入：

```shell
rm -Rf '${SolutionDir}/output/'
```

![自定义生成操作](images/visualstudiocustombuild.png)

> [!NOTE]
> 生成的绑定将在指示的目录中放置`--outdir`或`--out`参数。 生成的绑定名称可能会有所不同，因为某些平台上的包名称中有限制。

#### <a name="visual-studio-2017"></a>Visual Studio 2017

我们将实质上是设置相同的操作，但 Visual Studio 2017 中的菜单会稍有不同。 Shell 命令也略有不同。

转到**项目选项 > 生成事件**并输入到特定于平台的文档中列出的命令**生成后事件命令行**框。 例如：

![嵌入在 Windows 上的.NET](images/visualstudiowindows.png)
