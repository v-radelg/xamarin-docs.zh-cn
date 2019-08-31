---
title: 步骤 1。 注册应用程序以使用 Azure Active Directory
description: 本文档介绍如何将 Azure 应用程序注册到 Azure Active Directory, 以便移动客户端可以安全地访问该应用程序。
ms.prod: xamarin
ms.assetid: 0B17991A-4573-4F6C-9E86-D4B9D1A47E4D
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 732c1ee241b39a4bb1422b8c27820631bf8c6b0c
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2019
ms.locfileid: "70199049"
---
# <a name="step-1-register-an-app-to-use-azure-active-directory"></a>步骤 1。 注册应用程序以使用 Azure Active Directory

1. 导航到[windowsazure.com](https://manage.windowsazure.com) , 然后在 Azure 门户中通过你的 Microsoft 帐户或组织帐户登录。 如果没有 Azure 订阅, 可以从[azure.com](https://www.azure.com)获取试用版

2. 登录后, 请跳到**Active Directory** (1) 部分, 并选择要在其中注册应用程序的目录 (2)

   [![](register-images/01.-active-directory-in-azure-portal-sml.jpg "部分并选择要在其中注册应用程序的目录")](register-images/01.-active-directory-in-azure-portal.jpg#lightbox)

3. 单击 "**添加**" 创建新应用程序, 然后选择 **"添加我的组织正在开发的应用程序**"

   [![](register-images/02.-add-new-application-sml.jpg "添加我的组织正在开发的应用程序")](register-images/02.-add-new-application.jpg#lightbox)

4. 在下一个屏幕上, 为应用命名 (例如 XAM-演示)。
   请确保选择 "**本机客户端应用程序**" 作为应用程序类型。

   ![](register-images/03.-app-name.jpg "请确保选择 \"本机客户端应用程序\" 作为应用程序类型")

5. 在最后一个屏幕上, 提供一个对应用程序唯一的 **重定向 URI* , 因为在身份验证完成时, 它将返回到此 uri。

   ![](register-images/04.-app-redirect.jpg "在最后一个屏幕上, 提供应用程序独有的重定向 URI, 因为在身份验证完成时, 它将返回到此 URI。")

6. 创建应用后, 导航到 "**配置**" 选项卡。记下稍后将在应用程序中使用的**客户端 ID** 。 此外, 在此屏幕上, 你可以让移动应用程序访问 Active Directory 或添加其他应用程序 (如 Web API 或 Office365), 一旦身份验证完成, 移动应用程序就可以使用这些应用程序。

   ![](register-images/05.-configure.jpg "此外, 在此屏幕上, 你可以让移动应用程序访问 Active Directory 或添加其他应用程序, 如 Web API 或 Office365")



## <a name="related-links"></a>相关链接

- [Microsoft Nativeclient-ios 示例](https://github.com/AzureADSamples/NativeClient-MultiTarget-DotNet)
