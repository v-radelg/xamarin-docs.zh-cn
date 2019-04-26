---
title: 如何将 IPA 输出文件复制到 TFS 放置文件夹？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: B0F1E09E-7315-45BA-B7FF-44D2063EE19C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 74e2f2219dcb0908edce7f109844932639038b25
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61421118"
---
# <a name="how-can-i-copy-ipa-output-files-to-the-tfs-drop-folder"></a>如何将 IPA 输出文件复制到 TFS 放置文件夹？

打开`.csproj`在文本编辑器中在 iOS 应用项目文件，然后在末尾添加以下行 (在关闭前立即`</Project>`标记):

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

## <a name="notes"></a>说明

- 这是相同的常规方法上详细讨论[可以更改 IPA 文件的输出路径？](~/ios/troubleshooting/questions/ipa-output-path.md)。 两个重要点是设置`$(TF_BUILD_BINARIESDIRECTORY)`为目标文件夹，并因此添加的其他条件`CopyIpa`才会运行为 TFS 版本。

- 有关的说明`TF_BUILD_BINARIESDIRECTORY`请参阅[预定义生成变量](https://docs.microsoft.com/azure/devops/pipelines/build/variables)。

## <a name="additional-references"></a>其他参考

- [用于 Xamarin 安装 TFS 的文档](https://docs.microsoft.com/azure/devops/repos/tfvc/overview)
- [Azure DevOps 生成任务：Xamarin.Android](https://docs.microsoft.com/azure/devops/pipelines/tasks/build/xamarin-android)
- [Azure DevOps 生成任务：Xamarin.iOS](https://docs.microsoft.com/azure/devops/pipelines/tasks/build/xamarin-ios)

### <a name="next-steps"></a>后续步骤

本文档讨论为 Visual Studio 的 Xamarin 3.11.666 当前行为和 Mac 上的 8.10.3 Xamarin.iOS 生成主机。 获取进一步的帮助，请与我们联系，或如果此问题仍即使利用上述信息，请参阅[了可用于 Xamarin 的支持选项？](~/cross-platform/troubleshooting/support-options.md)有关联系人选项，建议的信息以及如何如果需要，提交新 bug。
