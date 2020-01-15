---
title: 上传到 Mac App Store
description: 本文档介绍如何使用 iTunes Connect 将 Xamarin.Mac 应用上传到 Mac App Store。 其中讨论了 iTunes Connect 完成该过程所需的信息。
ms.prod: xamarin
ms.assetid: 30cd0e47-1b2e-47ef-93f6-4bed20b15c03
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: e2b25468255ff84a3fe79ed4fea913e04bf88687
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75489358"
---
# <a name="upload-to-mac-app-store"></a>上传到 Mac App Store

 本指南介绍如何上传 Xamarin.Mac 应用以发布到 Mac App Store。

通过 [iTunes Connect](https://itunesconnect.apple.com/) 提交应用程序供 Mac App Store 审核。 还需要 App Store 中的 [Transporter](https://apps.apple.com/us/app/transporter/id1450874784?mt=12) 工具  。

1. 选择要创建的 **macOS 应用**： 

    [![](uploading-images/image65.png "iTunes Connect")](uploading-images/image65.png#lightbox)

2. 输入应用程序的名称和其他详细信息。 开发人员只能从先前创建的现有**程序包 ID** 中进行选择： 

    [![](uploading-images/image66.png "Selecting the bundle ID")](uploading-images/image66.png#lightbox)

3. 选择发布日期和价格。 无论开发人员选择哪个生效日期，应用在获准后才可销售。 如果开发人员希望更好地控制实际生效日期，可将此值设置为很久之后的某个日期： 

    [![](uploading-images/image67.png "Setting the available date and price")](uploading-images/image67.png#lightbox)

4. 输入应用信息，包括其在 App Store 中所属的类别： 

    [![](uploading-images/image68.png "Entering the app information")](uploading-images/image68.png#lightbox) 

    选择适用的评级： 

    [![](uploading-images/image69.png "Setting the app ratings")](uploading-images/image69.png#lightbox) 

    说明、关键字和联系人 URL： 

    [![](uploading-images/image70.png "Editing the Description, keywords and contact URLs")](uploading-images/image70.png#lightbox) 

    联系人信息和对 App Store 审阅者的建议： 

    [![](uploading-images/image71.png "Editing the contact information and advice for the App Store reviewers")](uploading-images/image71.png#lightbox) 

    最后是屏幕截图： 

    [![](uploading-images/image72.png "Adding the required screenshots")](uploading-images/image72.png#lightbox) 

    屏幕截图应采用 JPG、TIF 或 PNG 格式，大小为 1280x800、1440x900、2880x1800 或 2560x1600 像素。 按“保存”  完成操作。

5. 应用信息会显示以供审核。 单击“查看详细信息”  更改状态： 

    [![](uploading-images/image73.png "Viewing the app details")](uploading-images/image73.png#lightbox)

6. 在详细信息视图中，单击“准备上传二进制文件”以提交该应用程序包文件： 

    [![](uploading-images/image74.png "Selecting Ready to Upload Binary")](uploading-images/image74.png#lightbox)

7. 解答加密问题： 

    [![](uploading-images/image75.png "Answering the cryptography question")](uploading-images/image75.png#lightbox)

8. 网站将在其准备接受应用程序包文件时进行通知： 

    [![](uploading-images/image76.png "The acceptance notification")](uploading-images/image76.png#lightbox)

9. 启动 Transporter 并使用你的 Apple ID 登录，然后选择“添加应用”   ：

    [![](uploading-images/transporter01-sml.png "The Application Loader interface")](uploading-images/transporter01.png#lightbox)

    按照说明将应用包上传到 iTunes Connect。

    > [!NOTE]
    > [Transporter](https://apps.apple.com/us/app/transporter/id1450874784?mt=12) 替换与 Xcode 10 及更低版本一起使用的“应用程序加载程序”工具   。
    > 应用程序加载程序在 Xcode 11 或更高版本中不再可用。

应用程序通过审核后，即可从 Mac App Store 下载或购买。

## <a name="related-links"></a>相关链接

- [安装](~//mac/get-started/installation.md)
- [Hello，Mac 示例](~/mac/get-started/hello-mac.md)
- [在 Mac App Store 上分发应用](https://developer.apple.com/devcenter/mac/checklist/)
- [工具指南：对应用进行代码签名](https://developer.apple.com/library/mac/#documentation/ToolsLanguages/Conceptual/OSXWorkflowGuide/CodeSigning/CodeSigning.html)
- [开发人员 ID 和网关守卫](https://developer.apple.com/resources/developer-id/)
