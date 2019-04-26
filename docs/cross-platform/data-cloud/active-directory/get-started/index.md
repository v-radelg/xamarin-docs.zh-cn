---
title: Azure Active Directory
description: 本文档介绍了必须遵循为允许移动应用程序使用 Azure Active Directory 进行身份验证的步骤。
ms.prod: xamarin
ms.assetid: 70B3C2AB-CB4D-420C-9CFA-20CCFA0E3C78
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: ca33422817f19dbb0a04e8870800d3f5efa8af2a
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61318293"
---
# <a name="azure-active-directory"></a>Azure Active Directory

_注册应用以使用 Azure Active Directory_

Azure Active Directory 允许开发人员安全资源，例如文件、 链接和 Web Api 使用相同的组织帐户使员工可以登录到他们的系统或检查其电子邮件。

开发移动应用程序可以使用 Azure Active Directory 进行身份验证涉及三个步骤。
前两个步骤通常都是相同的而不考虑你打算使用哪些服务。 第三个步骤是不同的每个服务类型：

  1. [与 Azure Active Directory 注册](~/cross-platform/data-cloud/active-directory/get-started/register.md)上*windowsazure.com*门户，然后
  2. [配置服务](~/cross-platform/data-cloud/active-directory/get-started/configure.md)。
  3. 开发移动应用程序使用的服务。

您可以访问的不同服务的示例包括：

- [Graph API](~/cross-platform/data-cloud/active-directory/graph.md)
- Web API
- Office365


## <a name="conclusion"></a>结束语

使用上面的步骤可以进行移动应用针对 Azure Active Directory 身份验证。 Active Directory 身份验证库 (ADAL) 可以更轻松更少行代码，同时保持代码的大部分相同，从而使其可共享跨平台。



## <a name="related-links"></a>相关链接

- [Microsoft NativeClient 示例](https://github.com/AzureADSamples/NativeClient-MultiTarget-DotNet)
