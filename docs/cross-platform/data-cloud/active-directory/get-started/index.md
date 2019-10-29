---
title: Azure Active Directory
description: 本文档介绍了允许移动应用使用 Azure Active Directory 进行身份验证时必须遵循的步骤。
ms.prod: xamarin
ms.assetid: 70B3C2AB-CB4D-420C-9CFA-20CCFA0E3C78
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: 18604ffa4980d47149d8feed257460e782f30bee
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016637"
---
# <a name="azure-active-directory"></a>Azure Active Directory

_注册应用程序以使用 Azure Active Directory_

Azure Active Directory 允许开发人员使用员工用于登录系统或检查电子邮件的相同组织帐户来保护资源（如文件、链接和 Web Api）。

开发可通过 Azure Active Directory 进行身份验证的移动应用程序涉及三个步骤。
不管计划使用哪些服务，前两个步骤通常都是相同的。 每种服务类型的第三步是不同的：

  1. 在*windowsazure.com*门户中[注册 Azure Active Directory](~/cross-platform/data-cloud/active-directory/get-started/register.md) ，然后
  2. [配置服务](~/cross-platform/data-cloud/active-directory/get-started/configure.md)。
  3. 使用服务开发移动应用。

可以访问的不同服务的示例包括：

- [图形 API](~/cross-platform/data-cloud/active-directory/graph.md)
- Web API
- Office365

## <a name="conclusion"></a>结论

使用上述步骤，你可以对移动应用进行 Azure Active Directory 身份验证。 利用 Active Directory 身份验证库（ADAL），可以更轻松地处理代码行，同时将大部分代码保持相同，从而使其可在平台之间共享。

## <a name="related-links"></a>相关链接

- [Microsoft Nativeclient-ios 示例](https://github.com/AzureADSamples/NativeClient-MultiTarget-DotNet)
