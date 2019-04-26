---
title: 在 Xamarin.iOS 中的自定义文档图标
description: 此文章涉及包括并管理一个 Xamarin.iOS 应用程序中的图像资产，以用作自定义文档类型的图标。
ms.prod: xamarin
ms.assetid: 7A3F3C94-2578-4F53-9B8E-25714F48BDD6
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/23/2017
ms.openlocfilehash: 155847b4f5f6b3e6070f1afb6219db2d3789a075
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61258129"
---
# <a name="custom-document-icons-in-xamarinios"></a>在 Xamarin.iOS 中的自定义文档图标

_此文章涉及包括并管理一个 Xamarin.iOS 应用程序中的图像资产，以用作自定义文档类型的图标。_

如果 Xamarin.iOS 应用程序支持加载特定文档类型，开发人员可以提供遇到该文档类型，例如当用户按住中的附件时，系统将使用的图标*邮件应用程序*作为如下所示：

 [![](custom-document-types-images/17.png "举例说明了文档类型图标")](custom-document-types-images/17.png#lightbox)

开发人员可以添加文档类型信息的文件格式应用为能够打开，通过包含字典条目`CFBundleTypeName`字符串和`LSItemContentTypes`数组中应用的`Info.plist`。 文档类型的图标进入`CFBundleTypeIconFiles`数组。 如果未提供文档图标，iOS 将派生一个从应用程序图标。
图标可以提供有关多个大小，针对各种设备分辨率进行了优化。 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

若要将 Visual Studio 中的这些值分配为 Mac，请使用**文档类型**部分下**高级**选项卡上`Info.plist`编辑器来添加文档类型并为其分配图像的图标。 例如，下面是显示的 PDF 支持注册的屏幕截图：

 [![](custom-document-types-images/18.png "高级选项卡上的 Info.plist 编辑器下的文档类型部分")](custom-document-types-images/18.png#lightbox)
 
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

若要分配这些值在 Visual Studio 中的，使用**文档类型**部分下**高级**选项卡上`Info.plist`:

 ![](custom-document-types-images/doc01w.png "打开高级选项卡下的文档类型部分")

单击**添加文档类型**按钮，然后填写必填字段：

![](custom-document-types-images/doc02w.png "添加文档类型窗体")

-----


有关文档类型的详细信息，请参阅 Apple[统一的类型标识符引用](https://developer.apple.com/library/ios/#documentation/Miscellaneous/Reference/UTIRef/Articles/System-DeclaredUniformTypeIdentifiers.html)并[适用于 iOS 的文档交互编程主题](https://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Introduction/Introduction.html)。


## <a name="related-links"></a>相关链接

- [使用图像 （示例）](https://developer.xamarin.com/samples/WorkingWithImages/)
- [Hello，iPhone](~/ios/get-started/hello-ios/index.md)
- [自定义图标和图像创建指南](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html)
