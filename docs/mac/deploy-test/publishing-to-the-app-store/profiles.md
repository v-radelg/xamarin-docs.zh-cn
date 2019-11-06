---
title: Xamarin.Mac 应用的预配配置文件
description: 本指南介绍如何创建发布 Xamarin.Mac 应用所需的设置配置文件。
ms.prod: xamarin
ms.assetid: bdff6c32-f7e3-4a97-a093-dbda48be8227
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 04/12/2017
ms.openlocfilehash: b361cd00121d16752d5eebc971653c8ea185c641
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73018107"
---
# <a name="provisioning-profiles-for-xamarinmac-apps"></a>Xamarin.Mac 应用的预配配置文件

配置文件允许开发人员将多个 macOS（以前称为 Mac OS X）特定功能（例如 iCloud 和推送通知）合并到 Xamarin.Mac 应用中。 开发人员必须为开发的每个应用程序创建、下载和安装 Mac 配置文件才能使用这些功能。

[![](profiles-images/certif13.png "The Apple Provisioning Portal")](profiles-images/certif13.png#lightbox)

<a name="Development_Provisioning_Profile" />

## <a name="development-provisioning-profile"></a>开发配置文件

通过开发配置文件，可在配置文件中设置的特定计算机上测试面向 Mac App Store 的应用。 这在使用 iCloud 和推送通知等 macOS 功能时尤其适用。

> [!NOTE]
> 开发人员必须先创建 Mac 开发证书，然后才能创建开发预配配置文件。 填写如屏幕截图中所示的详细信息，生成可用于创建版本的**开发配置文件**。 “证书”  框中必须存在可供选择的有效 Mac 开发证书，并且至少注册一个用于测试的系统。

请执行以下操作：

1. 选择要创建的配置文件的类型，然后单击“继续”  按钮： 

    [![](profiles-images/certif14.png "Selecting the profile type")](profiles-images/certif14.png#lightbox)
2. 选择要对其创建配置文件的应用程序的 ID，然后单击“继续”  按钮： 

    [![](profiles-images/certif15.png "Selecting the app ID")](profiles-images/certif15.png#lightbox)
3. 选择用于签名配置文件的开发者 ID，然后单击“继续”  ： 

    [![](profiles-images/certif16.png "Selecting the developer ID")](profiles-images/certif16.png#lightbox)
4. 选择可使用此配置文件的计算机，然后单击“继续”  ： 

    [![](profiles-images/certif17.png "Selecting the allowed computers")](profiles-images/certif17.png#lightbox)
5. 现在，输入“配置文件名称”  ，然后单击“生成”  按钮： 

    [![](profiles-images/certif18.png "Generating the profile")](profiles-images/certif18.png#lightbox)
6. 单击“下载”  按钮下载新的配置文件： 

    [![](profiles-images/certif19.png "Downloading the profile")](profiles-images/certif19.png#lightbox)
7. 将开发配置文件安装在 Mac“系统偏好设置”  应用程序的“配置文件首选项”窗格中： 

    [![](profiles-images/certif20.png "Installing the profile")](profiles-images/certif20.png#lightbox)
8. “配置文件首选项”窗格中会显示所有已安装的配置文件： 

    [![](profiles-images/image47.png "Showing all installed profiles")](profiles-images/image47.png#lightbox)
9. 还会在**开发者证书实用程序**中显示此配置文件，以便需要再次下载： 

    [![](profiles-images/image48.png "The Developer Certificate Utility")](profiles-images/image48.png#lightbox)

每个新应用都需创建新的开发配置文件，添加新的测试计算机时也需创建。

<a name="Production_Provisioning_Profile" />

## <a name="production-provisioning-profile"></a>生产配置文件

需要生产配置文件才能生成用于提交到 Mac App Store 的包。

请执行以下操作：

1. 选择要创建的配置文件类型，然后单击“继续”  按钮： 

    [![](profiles-images/certif21.png "Selecting the type of profile")](profiles-images/certif21.png#lightbox)
2. 选择要对其创建配置文件的应用的 ID，然后单击“继续”  按钮： 

    [![](profiles-images/certif15.png "Selecting the app ID")](profiles-images/certif15.png#lightbox)
3. 选择对配置文件签名的公司 ID，然后单击“继续”  按钮： 

    [![](profiles-images/certif23.png "Selecting the company ID")](profiles-images/certif23.png#lightbox)
4. 输入“配置文件名称”  ，然后单击“生成”  按钮： 

    [![](profiles-images/certif24.png "Generating the profile")](profiles-images/certif24.png#lightbox)
5. 单击“下载”  获取配置文件（扩展名 `.provisionprofile`）： 

    [![](profiles-images/certif25.png "Downloading the profile")](profiles-images/certif25.png#lightbox)
6. 将其拖入 **Xcode 管理器**或双击进行安装。 Xcode 管理器中随后会显示该配置文件： 

    [![](profiles-images/image51.png "Installing the profile")](profiles-images/image51.png#lightbox)
7. 列表中也会显示该配置文件： 

    [![](profiles-images/certif26.png "Showing the installed profiles")](profiles-images/certif26.png#lightbox)

如果开发人员更改了应用 ID 所用的功能（例如 启用 iCloud 或推送通知），则需为该应用 ID 重新创建配置文件。

## <a name="related-links"></a>相关链接

- [安装](~//mac/get-started/installation.md)
- [Hello，Mac 示例](~//mac/get-started/hello-mac.md)
- [在 Mac App Store 上分发应用](https://developer.apple.com/devcenter/mac/checklist/)
- [工具指南：对应用进行代码签名](https://developer.apple.com/library/mac/#documentation/ToolsLanguages/Conceptual/OSXWorkflowGuide/CodeSigning/CodeSigning.html)
- [开发人员 ID 和网关守卫](https://developer.apple.com/resources/developer-id/)
