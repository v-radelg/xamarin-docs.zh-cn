---
title: 上传到 Mac App Store
description: 本文档介绍如何使用 iTunes Connect 将 Xamarin.Mac 应用上传到 Mac App Store。 其中讨论了 iTunes Connect 完成该过程所需的信息。
ms.prod: xamarin
ms.assetid: 30cd0e47-1b2e-47ef-93f6-4bed20b15c03
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 03/14/2017
ms.openlocfilehash: b0bb58fee1c9eaeee391b3a838b3fa2921e97a57
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70284256"
---
# <a name="upload-to-mac-app-store"></a>上传到 Mac App Store

 本指南介绍如何上传 Xamarin.Mac 应用以发布到 Mac App Store。

通过 [iTunes Connect](http://itunesconnect.apple.com/) 提交应用程序供 Mac App Store 审核。

1. 选择要创建的 **macOS 应用**： 

    [![](uploading-images/image65.png "iTunes Connect")](uploading-images/image65.png#lightbox)

2. 输入应用程序的名称和其他详细信息。 开发人员只能从先前创建的现有**程序包 ID** 中进行选择： 

    [![](uploading-images/image66.png "选择捆绑 ID")](uploading-images/image66.png#lightbox)

3. 选择发布日期和价格。 无论开发人员选择哪个生效日期，应用在获准后才可销售。 如果开发人员希望更好地控制实际生效日期，可将此值设置为很久之后的某个日期： 

    [![](uploading-images/image67.png "设置发售日期和价格")](uploading-images/image67.png#lightbox)

4. 输入应用信息，包括其在 App Store 中所属的类别： 

    [![](uploading-images/image68.png "输入应用信息")](uploading-images/image68.png#lightbox) 

    选择适用的评级： 

    [![](uploading-images/image69.png "设置应用评级")](uploading-images/image69.png#lightbox) 

    说明、关键字和联系人 URL： 

    [![](uploading-images/image70.png "编辑说明、关键字和联系人 URL")](uploading-images/image70.png#lightbox) 

    联系人信息和对 App Store 审阅者的建议： 

    [![](uploading-images/image71.png "编辑联系人信息和对 App Store 审阅者的建议")](uploading-images/image71.png#lightbox) 

    最后是屏幕截图： 

    [![](uploading-images/image72.png "添加所需的屏幕截图")](uploading-images/image72.png#lightbox) 

    屏幕截图应采用 JPG、TIF 或 PNG 格式，大小为 1280x800、1440x900、2880x1800 或 2560x1600 像素。 按“保存”  完成操作。

5. 应用信息会显示以供审核。 单击“查看详细信息”  更改状态： 

    [![](uploading-images/image73.png "查看应用详细信息")](uploading-images/image73.png#lightbox)

6. 在详细信息视图中，单击“准备上传二进制文件”以提交该应用程序包文件： 

    [![](uploading-images/image74.png "选择“准备上传二进制文件”")](uploading-images/image74.png#lightbox)

7. 解答加密问题： 

    [![](uploading-images/image75.png "解答加密问题")](uploading-images/image75.png#lightbox)

8. 网站将在其准备接受应用程序包文件时进行通知： 

    [![](uploading-images/image76.png "接受通知")](uploading-images/image76.png#lightbox)

9. 启动应用程序加载程序，确保使用 Apple ID 进行登录。
选择“提交应用”  继续操作： 

    [![](uploading-images/image77.png "应用程序加载程序接口")](uploading-images/image77.png#lightbox)

10. 从处于“准备上传二进制文件”  状态的应用程序列表中进行选择，然后单击“下一步”  ： 

    [![](uploading-images/image78.png "选择要上传的应用")](uploading-images/image78.png#lightbox)

11. 查看应用程序元数据，然后单击“选择...”  查找包文件： 

    [![](uploading-images/image79.png "审阅应用元数据")](uploading-images/image79.png#lightbox)

12. 使用 App Store 生成配置查找在 Visual Studio for Mac 中内置的包文件： 

    [![](uploading-images/image80.png "选择要上传的文件")](uploading-images/image80.png#lightbox)

13. 按“发送”  ： 

    [![](uploading-images/image81.png "发送应用")](uploading-images/image81.png#lightbox)

14. 将对包进行验证，并报告任何错误。 修复这些错误并重新上传。 成功上传后，将自动提交应用供 App Store 团队审核： 

    [![](uploading-images/image82.png "上传错误示例")](uploading-images/image82.png#lightbox)

应用程序通过审核后，即可从 Mac App Store 下载或购买。

## <a name="related-links"></a>相关链接

- [安装](~//mac/get-started/installation.md)
- [Hello，Mac 示例](~//mac/get-started/hello-mac.md)
- [在 Mac App Store 上分发应用](https://developer.apple.com/devcenter/mac/checklist/)
- [工具指南：对应用进行代码签名](https://developer.apple.com/library/mac/#documentation/ToolsLanguages/Conceptual/OSXWorkflowGuide/CodeSigning/CodeSigning.html)
- [开发人员 ID 和网关守卫](https://developer.apple.com/resources/developer-id/)
