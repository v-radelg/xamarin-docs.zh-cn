---
title: IBTool 错误：无法完成该操作。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: A804EBC4-2BBF-4A98-A4E8-A455DB2E8A17
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/03/2018
ms.openlocfilehash: b894c245a4391064746a08816dfa63a1148d436d
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67831256"
---
# <a name="ibtool-error-the-operation-couldnt-be-completed"></a>IBTool 错误：无法完成该操作。

## <a name="fixed-in-xcode-611"></a>在 Xcode 6.1.1 中修复

Apple[修复](https://developer.apple.com/library/content/documentation/Xcode/Conceptual/RN-Xcode-Archive/Chapters/xc6_release_notes.html#//apple_ref/doc/uid/TP40016994-CH4-SW1)这`ibtool`Xcode 6.1.1，因此升级到 Xcode 6.1.1 或更高版本中的 bug 是最简单的修复程序。

* * *

## <a name="description-of-the-problem"></a>问题的描述

`ibtool` Xcode 6.0 中的命令在 OS X 10.10 Yosemite 上存在一个 bug。 Xamarin.iOS 使用 Xcode 的`ibtool`进行编译的情节提要和`XIB`文件。

有关与 Xcode 相关 bug 的详细信息可以位于以下堆栈溢出文章： [https://stackoverflow.com/questions/25754763/cant-open-storyboard](https://stackoverflow.com/questions/25754763/cant-open-storyboard)

### <a name="error-message"></a>错误消息

> 无法打开文档"mainstoryboard.storyboard"。 无法完成该操作。 （com.apple.InterfaceBuilder 错误-1。）

## <a name="workarounds-for-xcode-60"></a>解决方法 （Xcode 6.0)

### <a name="option-1-manage-all-uiimageviewimage-properties-in-code"></a>选项 1：管理所有`UIImageView.Image`代码中的属性

而不是设置`Image`的属性`UIImageView`情节提要中或`.xib`文件中，您可以设置属性在一个视图的视图控制器中的生命周期重写方法 (例如，在`ViewDidLoad()`)。 另请参阅[处理图像](~/ios/app-fundamentals/images-icons/index.md)有关使用提示`UIImage.FromBundle()`与`UIImage.FromFile()`。

### <a name="option-2-move-all-of-the-image-resources-to-the-top-level-resources-folder"></a>选项 2：将所有图像资源移到顶级`Resources`文件夹

之后将映像移动到顶层`Resources`文件夹中，你将需要更新情节提要和`.xib`文件以使用新的映像路径。

### <a name="option-3-set-the-logicalname-for-any-problematic-image-assets-so-they-are-copied-to-the-top-level-of-theapp-bundle"></a>选项 3:设置`LogicalName`的任何有问题的图像资产，以便它们复制到的最高级别`.app`捆绑包

例如，假设原始`.csproj`文件包含以下条目：

`<BundleResource Include="Resources\Images\image.png" />`

可以更改此元素并添加`LogicalName`，以便该映像将改为复制到的最高级别`.app`捆绑包：

```xml
<BundleResource Include="Resources\Images\image.png">
    <LogicalName>image.png</LogicalName>
</BundleResource>
```

在 Visual Studio for Mac`LogicalName`也可以使用设置`Resource ID`字段下图像**视图 > 面板 > 属性**。 (另请参阅： [ https://stackoverflow.com/questions/16938250/xamarin-studio-folder-structure-issue-in-ios-project/16951545#16951545 ](https://stackoverflow.com/questions/16938250/xamarin-studio-folder-structure-issue-in-ios-project/16951545#16951545))

更改之后，你将需要更新情节提要和`.xib`文件以使用新的顶级图像路径。 Visual Studio for Mac 将自动更新为 autocompletions 列表`Image`iOS 设计器中的属性。 在 Visual Studio 中，你将需要手动编辑路径。 IOS 设计器将然后此应用显示为图像缺失，但该项目将生成并运行正确。

### <a name="next-steps"></a>后续步骤

获取进一步的帮助，请与我们联系，或如果此问题仍即使利用上述信息，请参阅[了可用于 Xamarin 的支持选项？](~/cross-platform/troubleshooting/support-options.md)有关联系人选项，建议的信息以及如何如果需要，提交新 bug。 

