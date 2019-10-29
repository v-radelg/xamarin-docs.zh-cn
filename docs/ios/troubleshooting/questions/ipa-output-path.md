---
title: 能否更改 IPA 文件的输出路径？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F5E5DCC6-F7CC-48E2-89E8-709E9C269502
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 6df481688bfaa1e23cc56e6e34586f23d8ab9da6
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031029"
---
# <a name="can-i-change-the-output-path-of-the-ipa-file"></a>能否更改 IPA 文件的输出路径？

## <a name="for-cycle-7-and-higher"></a>对于周期7和更高版本
是的，可以使用自定义的 MSBuild 目标来实现此目的。 最简单的选项可能是在生成 `.ipa` 文件后将其复制。

这些步骤适用于在 Mac 或 Windows 上使用 MSBuild 生成引擎的任何 iOS 项目。 （注意：所有 Unified API 项目都使用 MSBuild 生成引擎。）

1. 在文本编辑器中打开 iOS 应用项目的 `.csproj` 文件，然后在结尾处添加以下行（紧靠在结束 `</Project>` 标记之前）：

    ```xml
    <PropertyGroup>
        <CreateIpaDependsOn>
        $(CreateIpaDependsOn);
        CopyIpa
        </CreateIpaDependsOn>
    </PropertyGroup>
    
    <Target Name="CopyIpa"
            Condition="'$(OutputType)' == 'Exe'
            And '$(ComputedPlatform)' == 'iPhone'
            And '$(BuildIpa)' == 'true'">
        <Copy
            SourceFiles="$(IpaPackagePath)"
            DestinationFolder="$(OutputPath)"
        />
    </Target>
    ```

2. 将 DestinationFolder 设置为所需的输出文件夹。 通常，如果需要，可以在此参数中使用 MSBuild 属性（如 $ （OutputPath））。

## <a name="notes"></a>注意

- `CreateIpaDependsOn` 属性在属于 Xamarin 的 `Xamarin.iOS.Common.targets` 文件中定义。 它的行为如 "[如何：扩展 Visual Studio 生成过程](https://docs.microsoft.com/visualstudio/msbuild/how-to-extend-the-visual-studio-build-process)" 一文中的 "[重写预定义目标](https://docs.microsoft.com/visualstudio/msbuild/how-to-extend-the-visual-studio-build-process#overriding-predefined-targets)" 一节中所述。

- 如果愿意，可以使用**移动**任务而不是**复制**任务。 如果选择该选项，并且是在 Windows 上生成的，则需要使用完全限定的任务名称 `<Microsoft.Build.Tasks.Move>` 来避免 XamarinVS 生成任务的歧义。

## <a name="for-versions-before-xamarin-studio-6005174--xamarin-for-visual-studio-410530"></a>Xamarin Studio 6.0.0.5174 之前的版本 |适用于 Visual Studio 的 Xamarin 4.1.0.530

是的，可以使用自定义的 MSBuild 目标来实现此目的。 最简单的选项可能是在生成 `.ipa` 文件后将其复制。

这些步骤适用于在 Mac 或 Windows 上使用 MSBuild 生成引擎的任何 iOS 项目。 （注意：所有 Unified API 项目都使用 MSBuild 生成引擎。）

1. 在文本编辑器中打开 iOS 应用项目的 `.csproj` 文件，然后在结尾处添加以下行（紧靠在结束 `</Project>` 标记之前）。

    ```xml
    <PropertyGroup>
        <CreateIpaDependsOn>
            $(CreateIpaDependsOn);
            CopyIpa
        </CreateIpaDependsOn>
    </PropertyGroup>

    <Target Name="CopyIpa"
            Condition="'$(OutputType)' == 'Exe'
            And '$(ComputedPlatform)' == 'iPhone'
            And '$(BuildIpa)' == 'true'">
        <Copy
            SourceFiles="$(OutputPath)$(IpaPackageName)"
            DestinationFolder="/Users/macuser/Desktop/"
        />
    </Target>
    ```

2. 将 `DestinationFolder` 设置为所需的输出文件夹。 通常，如果需要，可以在此参数中使用 MSBuild 属性（如 `$(OutputPath)`）。

## <a name="notes"></a>注意

- `CreateIpaDependsOn` 属性在属于 Xamarin 的 `Xamarin.iOS.Common.targets` 文件中定义。 t 的行为如 "[如何：扩展 Visual Studio 生成过程](https://docs.microsoft.com/visualstudio/msbuild/how-to-extend-the-visual-studio-build-process)" 一文中的 "[重写预定义目标](https://docs.microsoft.com/visualstudio/msbuild/how-to-extend-the-visual-studio-build-process#overriding-predefined-targets)" 一节中所述。

- 如果愿意，可以使用**移动**任务而不是**复制**任务。 如果选择该选项，并且是在 Windows 上生成的，则需要使用完全限定的任务名称 `<Microsoft.Build.Tasks.Move>` 来避免 XamarinVS 生成任务的歧义。
