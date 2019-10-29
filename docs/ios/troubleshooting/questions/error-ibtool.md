---
title: IBTool 错误：无法完成操作。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: A804EBC4-2BBF-4A98-A4E8-A455DB2E8A17
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/03/2018
ms.openlocfilehash: 4ff7b88ad63870246acbf2b29d01775f6711a8ce
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031190"
---
# <a name="ibtool-error-the-operation-couldnt-be-completed"></a>IBTool 错误：无法完成操作。

## <a name="fixed-in-xcode-611"></a>修复了 Xcode 6.1。1

Apple[修复](https://developer.apple.com/library/content/documentation/Xcode/Conceptual/RN-Xcode-Archive/Chapters/xc6_release_notes.html#//apple_ref/doc/uid/TP40016994-CH4-SW1)了 Xcode 6.1.1 中的这 `ibtool` bug，因此升级到 Xcode 6.1.1 或更高版本是最简单的解决方法。

* * *

## <a name="description-of-the-problem"></a>问题说明

Xcode 6.0 中的 `ibtool` 命令在 OS X 10.10 Yosemite 上存在 bug。 Xamarin 使用 Xcode 的 `ibtool` 编译情节提要和 `XIB` 文件。

有关与 Xcode 相关的 bug 的详细信息，请参阅以下 Stack Overflow post： [https://stackoverflow.com/questions/25754763/cant-open-storyboard](https://stackoverflow.com/questions/25754763/cant-open-storyboard)

### <a name="error-message"></a>错误消息

> 未能打开文档 "Mainstoryboard.storyboard"。 无法完成该操作。 （InterfaceBuilder 错误-1。）

## <a name="workarounds-for-xcode-60"></a>解决方法（适用于 Xcode 6.0）

### <a name="option-1-manage-all-uiimageviewimage-properties-in-code"></a>选项1：在代码中管理所有 `UIImageView.Image` 属性

无需在情节提要或 `.xib` 文件中设置 `UIImageView` 的 `Image` 属性，只需在视图控制器中的一个视图生命周期替代方法中设置属性（例如，在 `ViewDidLoad()`中）。 另请参阅使用[图像](~/ios/app-fundamentals/images-icons/index.md)以获取有关使用 `UIImage.FromBundle()` 与 `UIImage.FromFile()`的提示。

### <a name="option-2-move-all-of-the-image-resources-to-the-top-level-resources-folder"></a>选项2：将所有图像资源移到顶层 `Resources` 文件夹

将图像移到顶层 `Resources` 文件夹后，你将需要更新情节提要和 `.xib` 文件以使用新的映像路径。

### <a name="option-3-set-the-logicalname-for-any-problematic-image-assets-so-they-are-copied-to-the-top-level-of-theapp-bundle"></a>选项3：为任何有问题的图像资产设置 `LogicalName`，以便将其复制到`.app` 捆绑包的顶层

例如，假设原始 `.csproj` 文件包含以下条目：

`<BundleResource Include="Resources\Images\image.png" />`

您可以更改此元素并添加 `LogicalName`，以便将图像复制到 `.app` 束的顶层：

```xml
<BundleResource Include="Resources\Images\image.png">
    <LogicalName>image.png</LogicalName>
</BundleResource>
```

在 Visual Studio for Mac 还可以使用 "**查看 >" > 属性**下的图像的 "`Resource ID`" 字段来设置 `LogicalName`。 （另请参阅： [https://stackoverflow.com/questions/16938250/xamarin-studio-folder-structure-issue-in-ios-project/16951545#16951545](https://stackoverflow.com/questions/16938250/xamarin-studio-folder-structure-issue-in-ios-project/16951545#16951545)）

完成此更改后，你将需要更新情节提要，并 `.xib` 文件以使用新的顶级图像路径。 Visual Studio for Mac 将在 iOS 设计器中自动更新 `Image` 属性的 autocompletions 列表。 在 Visual Studio 中，需要手动编辑路径。 然后，iOS 设计器会将其显示为缺少的图像，但项目将会正确生成和运行。

### <a name="next-steps"></a>后续步骤

若要获得更多帮助，请联系我们，或者，如果在使用上述信息后仍出现此问题，请参阅[哪些支持选项可用于 Xamarin？](~/cross-platform/troubleshooting/support-options.md)有关联系人选项、建议的信息，以及如何在需要时记录新 bug. 
