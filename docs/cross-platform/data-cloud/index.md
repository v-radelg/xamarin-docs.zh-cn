---
title: Microsoft Azure 和 Xamarin
description: 本文档链接到有关 Visual Studio for Mac、Azure 移动应用、Active Directory 身份验证和 WebAPI 中的连接的服务的文档。
ms.prod: xamarin
ms.assetid: 7b9aa8d9-c181-4c33-8ab0-2f56e4dbfc04
author: conceptdev
ms.author: crdun
ms.date: 10/09/2017
ms.openlocfilehash: 0979a0b65cc3d5b4944dadaf67aaa14cf1b3cf73
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70287602"
---
# <a name="microsoft-azure-and-xamarin"></a>Microsoft Azure 和 Xamarin

[![](images/evolve-mikej-azure-sml.png "Azure 应用 Services 功能易于添加到 Xamarin 应用，包括云数据存储和跨平台推送通知")](https://evolve.xamarin.com/session/56ec886fde91c6253c277bc6)

[Evolve 2016：使用 Azure 和 Xamarin 开发已连接的应用](https://evolve.xamarin.com/session/56ec886fde91c6253c277bc6)

## <a name="connected-services-in-visual-studio-for-mac"></a>Visual Studio for Mac 中连接的服务

Visual Studio for Mac 的新[连接的服务](connected-services.md)功能可帮助开发人员快速轻松地将 Azure 功能添加到 IDE 中的移动应用程序。 当前可用于 Alpha 通道中的测试。

## <a name="azure-app-services"></a>Azure 应用服务

其中提供了[Azure 移动应用文档](~/cross-platform/data-cloud/mobile-apps.md)的集合，该文档将指导你完成实现[Azure 移动客户端](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)的过程。
Xamarin 还提供适用于[iOS](https://www.nuget.org/packages/Xamarin.Azure.NotificationHubs.iOS/)和[Android](https://www.nuget.org/packages/Xamarin.Azure.NotificationHubs.Android/)的 Azure 消息传递 NuGet 包，帮助实现跨平台的推送通知。

在[Azure 应用服务门户](https://portal.azure.com/)上配置你的应用，以便访问移动应用、Web Api、存储等。 了解[应用服务的不同](https://azure.microsoft.com/updates/whats-new-with-azure-app-service/)之处，以及[从 Microsoft 观看这些视频](https://azure.microsoft.com/campaigns/azure-march-announcement/)。

## <a name="active-directory-authentication"></a>Active Directory 身份验证

[Azure Active Directory](~/cross-platform/data-cloud/active-directory/index.md)可用于通过[Xamarin. Auth 组件](https://www.nuget.org/packages/Xamarin.Auth/)登录 xamarin 应用中的用户。
然后，应用程序可以访问其他服务，如 Office 365。

## <a name="webapi"></a>WebAPI

Microsoft 的 Web API 公开了类似于 REST 的接口，适用于 Xamarin 应用程序。
可以轻松地启动[Azure 网站](https://trywebsites.azurewebsites.net/)并构建基于 WebAPI 的应用程序，以连接到 Xamarin 应用程序。


### <a name="introduction-to-web-servicescross-platformdata-cloudweb-servicesindexmd"></a>[Web 服务简介](~/cross-platform/data-cloud/web-services/index.md)

本教程介绍如何将 REST、WCF 和 SOAP web 服务技术与 Xamarin 移动应用程序集成。 它将检查各种服务实现、评估可用的工具和库，并提供用于使用服务数据的示例模式。 最后，它提供了有关使用 Xamarin 移动应用程序创建 RESTful web 服务以供使用的基本概述。

## <a name="samples"></a>示例

除了[文档示例](https://github.com/xamarin/mobile-samples/tree/master/Azure)之外，以下完整的应用程序还演示了集成到 Xamarin 应用中的各种 Azure 功能：

- [体育](https://github.com/xamarin/Sport)–友好的体育跟踪应用，使用数据存储 & 推送通知。
- [分钟](https://github.com/pierceboggan/Moments)–为映像使用 Azure 存储空间的即时照片共享。
- [XAMARIN CRM](https://github.com/xamarin/app-crm) –使用用于后端的 Web API。
- [MyShoppe](https://github.com/jamesmontemagno/MyShoppe) -Azure 移动应用。

- [eShop](https://github.com/dotnet-architecture/eShopOnContainers) –电子书[体系结构系列](https://www.microsoft.com/net/learn/architecture)的示例。
- [MyDriving](https://azure.microsoft.com/campaigns/mydriving/) –版本2016中的 Azure + IoT 示例。


## <a name="related-links"></a>相关链接

- [Azure PCL 示例（按@paulbatum）（示例）](https://github.com/paulbatum/mobile-services-xamarin-pcl)
- [Azure 门户](https://azure.microsoft.com/)
- [适用于 Xamarin 的移动客户端（NuGet）](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
