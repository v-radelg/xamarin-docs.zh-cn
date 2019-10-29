---
title: 如何将 IPA 输出文件复制到 TFS 放置文件夹？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: B0F1E09E-7315-45BA-B7FF-44D2063EE19C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 4493b1a0d06e2f44ee9a11a250395f058baa0548
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031025"
---
# <a name="how-can-i-copy-ipa-output-files-to-the-tfs-drop-folder"></a>如何将 IPA 输出文件复制到 TFS 放置文件夹？

在文本编辑器中打开 iOS 应用项目的 `.csproj` 文件，然后在结尾处添加以下行（紧靠在结束 `</Project>` 标记之前）：

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
        And '$(BuildIpa)' == 'true'
        And '$(TF_BUILD)' == 'true'">
    <Copy
        SourceFiles="$(OutputPath)$(IpaPackageName)"
        DestinationFolder="$(TF_BUILD_BINARIESDIRECTORY)"
    />
</Target>
```

## <a name="notes"></a>注意

- 这与[我在能否更改 IPA 文件的输出路径](~/ios/troubleshooting/questions/ipa-output-path.md)时所讨论的一般方法相同？。 将 `$(TF_BUILD_BINARIESDIRECTORY)` 设置为目标文件夹并添加一个额外条件，使 `CopyIpa` 只为 TFS 生成运行，这两个要点。

- 有关 `TF_BUILD_BINARIESDIRECTORY` 的说明，请参阅[预定义的生成变量](https://docs.microsoft.com/azure/devops/pipelines/build/variables)。

## <a name="additional-references"></a>其他参考

- [有关安装 TFS 以与 Xamarin 一起使用的文档](https://docs.microsoft.com/azure/devops/repos/tfvc/overview)
- [Azure DevOps 生成任务： Xamarin Android](https://docs.microsoft.com/azure/devops/pipelines/tasks/build/xamarin-android)
- [Azure DevOps 生成任务： Xamarin. iOS](https://docs.microsoft.com/azure/devops/pipelines/tasks/build/xamarin-ios)

### <a name="next-steps"></a>后续步骤

本文档讨论了从 Mac 生成主机上的 Xamarin 3.11.666 for Visual Studio 和 Xamarin 8.10.3 的当前行为。 若要获得更多帮助，请联系我们，或者，如果在使用上述信息后仍出现此问题，请参阅[哪些支持选项可用于 Xamarin？](~/cross-platform/troubleshooting/support-options.md)有关联系人选项、建议的信息，以及如何在需要时记录新 bug.
