---
title: 安装 .NET 嵌入
description: 本文档介绍如何安装 .NET 嵌入。 它讨论了如何手动运行工具, 如何自动生成绑定, 如何使用自定义 MSBuild 目标和必要的后期生成步骤。
ms.prod: xamarin
ms.assetid: 47106AF3-AC6E-4A0E-B30B-9F73C116DDB3
author: chamons
ms.author: chhamo
ms.date: 04/18/2018
ms.openlocfilehash: 47efbaa12475f627b5963cb6613c3441a1d96aac
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2019
ms.locfileid: "70227838"
---
# <a name="installing-net-embedding"></a>安装 .NET 嵌入

## <a name="installing-net-embedding-from-nuget"></a>从 NuGet 安装 .NET 嵌入

选择 "**添加" > "添加 Nuget 包 ...** ", 并从 NuGet 包管理器安装**Embeddinator-4000** :

![NuGet 程序包管理器](images/visualstudionuget.png)

这会将**Embeddinator-4000**和**objcgen**安装到**包/Embeddinator-4000/tools**目录中。

通常, 应选择最新版本的 Embeddinator-4000 以供下载。 目标-C 支持需要0.4 或更高版本。

## <a name="running-manually"></a>手动运行

安装 NuGet 后, 可以手动运行工具。

- 打开终端 (macOS) 或命令提示符 (Windows)
- 将目录更改为你的解决方案根目录
- 工具安装在中:
  - **。/packages/Embeddinator-4000。[VERSION]/tools/objcgen** (目标-C)
  - **。/packages/Embeddinator-4000。[VERSION]/tools/Embeddinator-4000.exe** (Java/C)
- 在 macOS 上, 可以直接运行**objcgen** 。
- 在 Windows 上, 可以直接运行**Embeddinator-4000** 。
- 在 macOS 上, **Embeddinator-4000**需要运行**mono**:
  - `mono ./packages/Embeddinator-4000.[VERSION]/tools/Embeddinator-4000.exe`

每个命令调用都需要特定于平台的文档中列出的多个参数。

## <a name="automatic-binding-generation"></a>自动绑定生成

可以通过两种方法自动运行生成过程的 .NET 嵌入部分。

- 自定义 MSBuild 目标
- 后期生成步骤

尽管本文档将介绍这两种情况, 但首选使用自定义 MSBuild 目标。 在从命令行生成时, 不一定要运行后期生成步骤。

### <a name="custom-msbuild-targets"></a>自定义 MSBuild 目标

若要使用 MSbuild 目标自定义生成, 请先在你的 .csproj 旁创建一个**Embeddinator**文件, 如下所示:

```xml
<Project DefaultTargets="Build" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
    <Target Name="RunEmbeddinator" AfterTargets="AfterBuild" Inputs="$(OutputPath)/$(AssemblyName).dll" Outputs="$(IntermediateOutputPath)/Embeddinator/$(AssemblyName).framework/$(AssemblyName)">
        <Exec Command="" />
    </Target>
</Project>
```

此处, 应在该命令的文本中填入平台特定文档中列出的某个 .NET 嵌入调用。

使用此目标:

- 在 Visual Studio 2017 或 Visual Studio for Mac 中关闭项目
- 在文本编辑器中打开库 .csproj
- 在最后`</Project>`一行上方的底部添加以下行:

```xml
 <Import Project="Embeddinator-4000.targets" />
```

- 重新打开项目

### <a name="post-build-steps"></a>后期生成步骤

添加生成后步骤以运行 .NET 嵌入的步骤因 IDE 而异:

#### <a name="visual-studio-for-mac"></a>Visual Studio for Mac

在 Visual Studio for Mac 中, **> "生成 > 自定义命令**并添加"**生成后**"步骤, 请参阅" 项目选项 "。

设置特定于平台的文档中列出的命令。

> [!NOTE]
> 请确保使用从 NuGet 安装的版本号

如果要对C#项目进行持续开发, 还可以添加自定义命令, 在运行 .net 嵌入之前清理**输出**目录:

```shell
rm -Rf '${SolutionDir}/output/'
```

![自定义生成操作](images/visualstudiocustombuild.png)

> [!NOTE]
> 生成的绑定将放置在`--outdir`或`--out`参数所指示的目录中。 生成的绑定名称可能会有所不同, 因为某些平台对包名称有限制。

#### <a name="visual-studio-2017"></a>Visual Studio 2017

我们实际上会设置相同的内容, 但 Visual Studio 2017 中的菜单略有不同。 Shell 命令也略有不同。

**> 生成事件**"中转到" 项目选项 ", 并将特定于平台的文档中列出的命令输入到"**生成后事件命令行**"框中。 例如：

![Windows 上的 .NET 嵌入](images/visualstudiowindows.png)
 