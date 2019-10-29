---
title: 步骤 2。 配置移动应用程序的服务访问
description: 本文档介绍如何为 Xamarin 应用程序提供对受 Azure Active Directory 保护的 Azure 应用程序的访问权限。
ms.prod: xamarin
ms.assetid: 8A14A457-F72E-4B08-B4B6-801F7619F893
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: eeac7b0b70b2f11304a374de7522f28d4bcad6c6
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016667"
---
# <a name="step-2-configure-service-access-for-mobile-application"></a>步骤 2。 配置移动应用程序的服务访问

只要任何资源（例如 web 应用程序、web 服务等）需要 Azure Active Directory 的保护，就需要注册。 所有安全应用程序或服务都可以在 "**应用程序**" 选项卡下查看。可在此处选择需要从移动应用程序访问的应用程序，并提供对该应用程序的访问权限。

1. 在 "**配置**" 选项卡上，找到 "**其他应用程序的权限**" 部分：

   ![](configure-images/2.1-configure.png "On the Configure tab, locate permissions to other applications section")

2. 单击 "**添加应用程序**" 按钮。 在下一个屏幕弹出窗口中，应该会看到 Azure Active Directory 保护的所有应用程序的列表。 选择需要从移动应用程序访问的应用程序。

   ![](configure-images/2.2-add-application.png "Select the applications that needs to be accessed from the mobile application")

3. 选择应用程序后，再次选择 "**其他应用**程序的权限" 部分中新添加的应用程序，并授予适当的权限。

   ![](configure-images/2.3-permissions.png "After selecting the application, once again select the newly-added application in permissions to other   applications section and give appropriate rights")

4. 最后，**保存**配置。 这些服务现在应在移动应用程序中可用！

## <a name="related-links"></a>相关链接

- [Microsoft Nativeclient-ios 示例](https://github.com/AzureADSamples/NativeClient-MultiTarget-DotNet)
