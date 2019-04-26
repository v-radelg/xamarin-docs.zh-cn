---
title: Microsoft Azure 和 Xamarin
description: 本文档所链接到有关在 Visual Studio 连接服务于 Mac、 Azure 移动应用、 Active Directory 身份验证和 WebAPI 的文档。
ms.prod: xamarin
ms.assetid: 7b9aa8d9-c181-4c33-8ab0-2f56e4dbfc04
author: asb3993
ms.author: amburns
ms.date: 10/09/2017
ms.openlocfilehash: 25eec247a0cb664897541a0e6e818a77018fda43
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61187766"
---
# <a name="microsoft-azure-and-xamarin"></a>Microsoft Azure 和 Xamarin

[ ![](images/evolve-mikej-azure-sml.png "Azure 应用服务功能可轻松地将添加到 Xamarin 应用，包括云数据存储和跨平台推送通知")](https://evolve.xamarin.com/session/56ec886fde91c6253c277bc6)

[Evolve 2016：开发使用 Azure 和 Xamarin 的已连接的应用](https://evolve.xamarin.com/session/56ec886fde91c6253c277bc6)

## <a name="connected-services-in-visual-studio-for-mac"></a>Visual Studio for Mac 中连接的服务

新[连接的服务](connected-services.md)Visual Studio for Mac 的功能可帮助开发人员能够快速轻松地将 Azure 功能添加到 IDE 从移动应用程序。 当前可用于测试的 Alpha 通道中。

## <a name="azure-app-services"></a>Azure App Services

有了一系列[Azure 移动应用文档](~/cross-platform/data-cloud/mobile-apps.md)的指导您完成的实现过程[Azure 移动客户端](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)。
Xamarin 还提供了有关 Azure 消息传送 NuGet 包[iOS](https://www.nuget.org/packages/Xamarin.Azure.NotificationHubs.iOS/)并[Android](https://www.nuget.org/packages/Xamarin.Azure.NotificationHubs.Android/)来帮助实现跨平台推送通知。

在配置您的应用程序[Azure 应用服务门户](https://portal.azure.com/)访问移动应用、 Web Api、 存储和更多。 了解如何[应用服务有何不同](http://azure.microsoft.com/updates/whats-new-with-azure-app-service/)并在观看[Microsoft 从这些视频](http://azure.microsoft.com/campaigns/azure-march-announcement/)。

## <a name="active-directory-authentication"></a>Active Directory 身份验证

[Azure Active Directory](~/cross-platform/data-cloud/active-directory/index.md)可用于对通过 Xamarin 应用中的登录名用户[Xamarin.Auth 组件](https://www.nuget.org/packages/Xamarin.Auth/)。
应用随后可以访问 Office 365 等其他服务。

## <a name="webapi"></a>WebAPI

Microsoft 的 Web API 公开 Xamarin 应用程序可以轻松地使用一个类似于 REST 的接口。
您可以轻松地启动[Azure 网站](https://trywebsites.azurewebsites.net/)并生成用于连接到 Xamarin 应用的基于 WebAPI 的应用。


###  <a name="introduction-to-web-servicescross-platformdata-cloudweb-servicesindexmd"></a>[介绍了 Web 服务](~/cross-platform/data-cloud/web-services/index.md)

本教程介绍了如何将集成的其余部分中，WCF 和 SOAP web 服务技术与 Xamarin 移动应用程序。 它检查各种服务实现，计算可用的工具和库集成，并提供示例模式使用服务的数据。 最后，它提供了创建供使用的 RESTful web 服务与 Xamarin 移动应用程序的基本概述。

## <a name="samples"></a>示例

除了[文档示例](https://github.com/xamarin/mobile-samples/tree/master/Azure)，以下完整应用程序演示了各种 Azure 功能整合到 Xamarin 应用：

- [运动](https://github.com/xamarin/Sport)– 友好的体育联盟跟踪应用使用数据存储和推送通知。
- [时间](https://github.com/pierceboggan/Moments)– 即时照片共享，使用 Azure 存储的映像。
- [Xamarin CRM](https://github.com/xamarin/app-crm) – 使用 Web API 后端进行。
- [MyShoppe](https://github.com/jamesmontemagno/MyShoppe) -Azure 移动应用。

- [eShop](https://github.com/dotnet-architecture/eShopOnContainers) – 示例[体系结构系列](https://www.microsoft.com/net/learn/architecture)电子图书。
- [MyDriving](https://azure.microsoft.com/campaigns/mydriving/) – Azure + IoT 示例的 Build 2016。


## <a name="related-links"></a>相关链接

- [Azure PCL 示例 (通过@paulbatum) （示例）](https://github.com/paulbatum/mobile-services-xamarin-pcl)
- [Azure 门户](http://azure.microsoft.com/)
- [Xamarin (NuGet) 的移动客户端](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
