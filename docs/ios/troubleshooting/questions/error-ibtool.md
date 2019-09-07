---
title: IBTool 错误：无法完成该操作。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: A804EBC4-2BBF-4A98-A4E8-A455DB2E8A17
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 04/03/2018
ms.openlocfilehash: 39b522af5bdc3587e3d5aa1451ed4879c6af65f5
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769341"
---
# <a name="ibtool-error-the-operation-couldnt-be-completed"></a>IBTool 错误：无法完成该操作。

## <a name="fixed-in-xcode-611"></a>修复了 Xcode 6.1。1

Apple[修复](https://developer.apple.com/library/content/documentation/Xcode/Conceptual/RN-Xcode-Archive/Chapters/xc6_release_notes.html#//apple_ref/doc/uid/TP40016994-CH4-SW1)了`ibtool` Xcode 6.1.1 中的此 bug，因此升级到 Xcode 6.1.1 或更高版本是最简单的解决方法。

* * *

## <a name="description-of-the-problem"></a>问题说明

Xcode `ibtool` 6.0 中的命令在 OS X 10.10 Yosemite 上出现 bug。 Xamarin 使用 Xcode `ibtool`来编译情节提要和`XIB`文件。

有关与 Xcode 相关的 bug 的详细信息，请参阅以下 Stack Overflow post：[https://stackoverflow.com/questions/25754763/cant-open-storyboard](https://stackoverflow.com/questions/25754763/cant-open-storyboard)

### <a name="error-message"></a>错误消息

> 未能打开文档 "Mainstoryboard.storyboard"。 无法完成该操作。 （InterfaceBuilder 错误-1。）

## <a name="workarounds-for-xcode-60"></a>解决方法（适用于 Xcode 6.0）

### <a name="option-1-manage-all-uiimageviewimage-properties-in-code"></a>选项 1：在代码`UIImageView.Image`中管理所有属性

不是在情节`Image`提要或`.xib`文件`UIImageView`中设置的属性，而是可以在视图控制器中的一个视图生命周期重写方法（例如`ViewDidLoad()`）中设置属性。 另请参阅使用[图像](~/ios/app-fundamentals/images-icons/index.md)获取有关使用`UIImage.FromBundle()` vs. `UIImage.FromFile()`的提示。

### <a name="option-2-move-all-of-the-image-resources-to-the-top-level-resources-folder"></a>选项 2：将所有图像资源移至顶级`Resources`文件夹

将图像移到顶层`Resources`文件夹后，需要更新情节提要和`.xib`文件以使用新的图像路径。

### <a name="option-3-set-the-logicalname-for-any-problematic-image-assets-so-they-are-copied-to-the-top-level-of-theapp-bundle"></a>选项3：为任何有问题的图像资产设置，以便将其复制到`.app`捆绑包的顶层`LogicalName`

例如，假设原始`.csproj`文件包含以下条目：

`<BundleResource Include="Resources\Images\image.png" />`

你可以更改此元素并添加`LogicalName` ，以便将映像复制到`.app`包的顶层：

```xml
<BundleResource Include="Resources\Images\image.png">
    <LogicalName>image.png</LogicalName>
</BundleResource>
```

在 Visual Studio for Mac `LogicalName`中，还可以`Resource ID`使用 "**视图 >** " 面板 > "属性" 下的图像的字段设置。 （另请参阅[https://stackoverflow.com/questions/16938250/xamarin-studio-folder-structure-issue-in-ios-project/16951545#16951545](https://stackoverflow.com/questions/16938250/xamarin-studio-folder-structure-issue-in-ios-project/16951545#16951545)：）

完成此更改后，你将需要更新情节提要和`.xib`文件以使用新的顶级图像路径。 Visual Studio for Mac 将在 iOS 设计器中自动更新`Image`属性的 autocompletions 列表。 在 Visual Studio 中，需要手动编辑路径。 然后，iOS 设计器会将其显示为缺少的图像，但项目将会正确生成和运行。

### <a name="next-steps"></a>后续步骤

若要获得更多帮助，请联系我们，或者，如果在使用上述信息后仍出现此问题，请参阅[哪些支持选项可用于 Xamarin？](~/cross-platform/troubleshooting/support-options.md)有关联系人选项、建议的信息，以及如何在需要时记录新 bug. 
