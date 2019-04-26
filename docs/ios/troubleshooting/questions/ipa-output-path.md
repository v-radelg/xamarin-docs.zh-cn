---
title: 可以更改 IPA 文件的输出路径？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F5E5DCC6-F7CC-48E2-89E8-709E9C269502
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 1c3c3a63de40a63f040870505b086d67fe160773
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61421190"
---
# <a name="can-i-change-the-output-path-of-the-ipa-file"></a>可以更改 IPA 文件的输出路径？

## <a name="for-cycle-7-and-higher"></a>周期 7 及更高版本
是的可以使用自定义的 MSBuild 目标来实现此目的。 最简单的选项可能是复制`.ipa`文件后已生成。

这些步骤将适用于在 Mac 或 Windows 上使用 MSBuild 生成引擎的任何 iOS 项目。 (注意： 所有 Unified API 项目使用 MSBuild 生成引擎。)

1. 打开`.csproj`在文本编辑器中在 iOS 应用项目文件，然后在末尾添加以下行 (在关闭前立即`</Project>`标记):
    
    ```
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

2. 设置为所需的输出文件夹 DestinationFolder。 像往常一样，您可以使用 MSBuild 属性 （如 $(OutputPath)) 中根据需要此参数。

## <a name="notes"></a>说明
- `CreateIpaDependsOn`属性中定义`Xamarin.iOS.Common.targets`文件，它是 Xamarin.iOS 的一部分。 它的行为所述*重写 DependsOn 属性*上[ https://msdn.microsoft.com/library/ms366724.aspx ](https://msdn.microsoft.com/library/ms366724.aspx)。

- 可以使用**移动**任务而非**副本**如果首选的任务。 如果选择选项，并且构建在 Windows 上，您需要使用完全限定的任务名称`<Microsoft.Build.Tasks.Move>`以避免含糊歧义 XamarinVS 生成任务。

## <a name="for-versions-before-xamarin-studio-6005174--xamarin-for-visual-studio-410530"></a>Xamarin Studio 6.0.0.5174 之前的版本 |Xamarin for Visual Studio 4.1.0.530

是的可以使用自定义的 MSBuild 目标来实现此目的。 最简单的选项可能是复制`.ipa`文件后已生成。

这些步骤将适用于在 Mac 或 Windows 上使用 MSBuild 生成引擎的任何 iOS 项目。 (注意： 所有 Unified API 项目使用 MSBuild 生成引擎。)

1. 打开`.csproj`在文本编辑器中在 iOS 应用项目文件，然后在末尾添加以下行 (在关闭前立即`</Project>`标记)。

    ```csharp
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

2. 设置`DestinationFolder`到所需的输出文件夹。 像往常一样，您可以使用 MSBuild 属性 (如`$(OutputPath)`) 内根据需要此参数。

## <a name="notes"></a>说明
- `CreateIpaDependsOn`属性中定义`Xamarin.iOS.Common.targets`文件，它是 Xamarin.iOS 的一部分。 它的行为所述*重写"DependsOn"属性*上[ https://msdn.microsoft.com/library/ms366724.aspx ](https://msdn.microsoft.com/library/ms366724.aspx)。

- 可以使用**移动**任务而非**副本**如果首选的任务。 如果选择选项，并且构建在 Windows 上，您需要使用完全限定的任务名称`<Microsoft.Build.Tasks.Move>`以避免含糊歧义 XamarinVS 生成任务。
