---
title: 步骤 1。 注册应用以使用 Azure Active Directory
description: 本文档介绍如何使用 Azure Active Directory 注册 Azure 应用程序，以便移动客户端可以安全地访问。
ms.prod: xamarin
ms.assetid: 0B17991A-4573-4F6C-9E86-D4B9D1A47E4D
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: fd3cb0e8201a6e84c726b211852013bd5f35fba1
ms.sourcegitcommit: 58d8bbc19ead3eb535fb8248710d93ba0892e05d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67675114"
---
# <a name="step-1-register-an-app-to-use-azure-active-directory"></a>步骤 1。 注册应用以使用 Azure Active Directory

1. 导航到[windowsazure.com](https://manage.windowsazure.com)并使用你的 Microsoft 帐户或 Azure 门户中的组织帐户登录。 如果还没有 Azure 订阅，可以获取从试用版[azure.com](http://www.azure.com)

2. 登录后，请转到**Active Directory** （1） 部分，并选择你想要注册应用程序 (2) 的目录

  [![](register-images/01.-active-directory-in-azure-portal-sml.jpg "部分，并选择你想要注册应用程序的目录")](register-images/01.-active-directory-in-azure-portal.jpg#lightbox)

3. 单击**外**若要创建新的应用程序，然后选择**添加我的组织正在开发的应用程序**

  [![](register-images/02.-add-new-application-sml.jpg "添加我的组织正在开发的应用程序")](register-images/02.-add-new-application.jpg#lightbox)

4. 在下一个屏幕，提供您的应用程序的名称 （例如。 XAM-DEMO)。
  请确保选择**本机客户端应用程序**作为应用程序的类型。

  ![](register-images/03.-app-name.jpg "请确保选择本机客户端应用程序作为应用程序的类型")

5. 在最后一个屏幕上，提供 **重定向 URI*这是唯一的应用程序，因为身份验证完成时，将返回到此 URI。

  ![](register-images/04.-app-redirect.jpg "在最后一个屏幕上，提供一个重定向 URI，如身份验证完成时，将返回到此 URI 是唯一的应用程序")

6. 创建应用后，导航到**配置**选项卡。记下**客户端 ID**该选项将在应用程序中更高版本。 此外，可以在此屏幕上移动应用程序访问向 Active Directory 或添加另一个应用程序，如 Web API 或 office 365，身份验证完成后，移动应用程序可以使用。

    ![](register-images/05.-configure.jpg "此外，在此屏幕上可以授予对 Active Directory 在移动应用程序访问权限或添加 Web API 或 office 365 等其他应用程序")



## <a name="related-links"></a>相关链接

- [Microsoft NativeClient 示例](https://github.com/AzureADSamples/NativeClient-MultiTarget-DotNet)
