---
title: 步骤 2。 为移动应用程序配置服务访问权限
description: 本文档介绍如何提供一个 Xamarin 应用程序具有访问权限由 Azure Active Directory 保护的 Azure 应用程序。
ms.prod: xamarin
ms.assetid: 8A14A457-F72E-4B08-B4B6-801F7619F893
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 6135740095395d5cd7bde9abc1cbbab1e1072161
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832242"
---
# <a name="step-2-configure-service-access-for-mobile-application"></a>步骤 2。 为移动应用程序配置服务访问权限

只要任何资源，例如 web 应用程序、 web 服务等需要保护的 Azure Active Directory，它需要注册。 所有安全的应用程序或服务可以看到下**应用程序**选项卡。您可以选择应用程序，需要从移动应用程序访问，并向其授予访问权限。

1. 上**配置**选项卡上，找到**其他应用程序的权限**部分：

  ![](configure-images/2.1-configure.png "在配置选项卡上找到其他应用程序的部分的权限")

2. 单击**添加应用程序**按钮。 在下一个屏幕弹出窗口会看到这样的使用由 Azure Active Directory 保护的所有应用程序的列表。 选择需要从移动应用程序访问的应用程序。

  ![](configure-images/2.2-add-application.png "选择需要从移动应用程序访问的应用程序")

3. 选择应用程序之后, 再一次选择新添加应用程序中的**其他应用程序的权限**部分，并提供适当的权限。

  ![](configure-images/2.3-permissions.png "选择后应用程序，再一次选择新添加的应用程序中对其他应用程序的部分的权限，授予适当的权限")

4. 最后，**保存**配置。 这些服务现在应可在移动应用程序中 ！



## <a name="related-links"></a>相关链接

- [Microsoft NativeClient 示例](https://github.com/AzureADSamples/NativeClient-MultiTarget-DotNet)
