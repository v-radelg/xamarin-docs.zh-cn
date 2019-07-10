---
title: Microsoft Azure Mobile Apps
description: 本文档所链接到介绍如何构建连接到 Azure 的 Xamarin 应用的指南。 它讨论了如何使用 Xamarin Azure 组件、 用户和推送通知。
ms.prod: xamarin
ms.assetid: 7B9AA8D9-C181-4C33-8AB0-2F56E4DBFC03
author: conceptdev
ms.author: crdun
ms.date: 04/02/2017
ms.openlocfilehash: a1a0b078659441f0f45af66728a5f37d578d6274
ms.sourcegitcommit: 58d8bbc19ead3eb535fb8248710d93ba0892e05d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67675088"
---
# <a name="microsoft-azure-mobile-apps"></a>Microsoft Azure Mobile Apps

_示例和代码下载的 Azure 门户文档。_

<!--
NOTE TO AUTHORS: this page is referenced from
https://azure.microsoft.com/develop/mobile/xamarin/
as https://developer.xamarin.com/guides/cross-platform/data-cloud/mobile-services/
A redirect has been put in place to /mobile-apps/ HOWEVER the /Resources/ .ZIP files are still located in /mobile-services/ so that the following permalinks don't break

The ZIPs in /Resources/ are also referenced by inbound links
Getting Started  http://go.microsoft.com/fwlink/p/?LinkId=331359
Get started with data   http://go.microsoft.com/fwlink/p/?LinkId=331302
Get started with push   http://go.microsoft.com/fwlink/p/?LinkId=331303
Get started with authentication http://go.microsoft.com/fwlink/p/?LinkId=331328
Get started with Notification Hubs  http://go.microsoft.com/fwlink/p/?LinkId=331329
Validate and modify data    http://go.microsoft.com/fwlink/p/?LinkId=331330
-->


这些链接是有关上提供 Xamarin 文档[Azure 移动应用](https://docs.microsoft.com/azure/app-service-mobile/)网站。
将 Azure 功能添加到 Xamarin 应用程序，通过下载[Azure 移动客户端](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)。

## <a name="working-with-the-xamarin-azure-component"></a>使用 Xamarin Azure 组件

常规文档[使用 Xamarin 客户端库 （组件）](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library)来完成使用 Azure 移动应用的各种任务。 此页面包含大量示例代码片段，而无需详细的说明和示例在每个下面列出的演练文章中可用。

## <a name="getting-started"></a>入门

本文提供了让您的第一个 Xamarin Azure 应用程序启动并运行的分步说明。
它介绍了在门户中创建新的 Azure 移动应用，然后下载并运行预配置的应用。

-  [iOS](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-xamarin-ios-get-started/)
-  [Android](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-xamarin-android-get-started/)
-  [Xamarin.Forms](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started)

<!--
## Validate, Modify and Augment Data in Scripts

Demonstrates how to add server-side scripts to Azure Mobile Services data tables to implement server-side validation and other functionality.

-  [iOS](https://azure.microsoft.com/documentation/articles/mobile-services-dotnet-how-to-use-client-library/#errors)
-  [Android](https://azure.microsoft.com/documentation/articles/mobile-services-dotnet-how-to-use-client-library/#errors)
-->

<!--
## Add Paging to Data

A quick example of paging large sets of data using Skip() and Take().

-  [iOS](https://azure.microsoft.com/documentation/articles/mobile-services-dotnet-how-to-use-client-library/#paging)
-  [Android](https://azure.microsoft.com/documentation/articles/mobile-services-dotnet-how-to-use-client-library/#paging)
-->

## <a name="get-started-with-users"></a>用户入门

提供了完整的说明配置和编码使用 Azure 移动服务的登录屏幕。 支持的身份验证提供程序包括 Microsoft、 Google、 Facebook 和 Twitter。

-  [iOS](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-ios-get-started-users/)
-  [Android](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-android-get-started-users/)


## <a name="authorize-users-in-scripts"></a>脚本为用户授权

对于 Javascript 后端的一些示例代码

-  [Todo.js](https://github.com/Azure/azure-mobile-apps-node/blob/master/samples/personal-table/tables/TodoItem.js#L38)


## <a name="get-started-with-push"></a>推送通知入门

若要配置推送通知上的 Apple 和 Google 的网站，然后从 Azure 移动服务向设备发送推送通知的说明完成操作。

-  [iOS](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-xamarin-ios-get-started-push)
-  [Android](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-xamarin-android-get-started-push)


## <a name="get-started-with-notification-hubs"></a>使用通知中心入门

完整的说明来配置推送通知 Apple 和 Google 网站上，配置 Azure 通知中心，然后生成将通知推送到设备。

-  [iOS](https://docs.microsoft.com/azure/notification-hubs/xamarin-notification-hubs-ios-push-notification-apns-get-started)
-  [Android](https://docs.microsoft.com/azure/notification-hubs/xamarin-notification-hubs-push-notifications-android-gcm)



## <a name="related-links"></a>相关链接

- [GettingStarted （示例）](https://github.com/xamarin/mobile-samples/tree/master/Azure/GettingStarted)
- [GetStartedWithData （示例）](https://github.com/xamarin/mobile-samples/tree/master/Azure/GetStartedWithData)
- [GetStartedWithUsers （示例）](https://github.com/xamarin/mobile-samples/tree/master/Azure/GetStartedWithUsers)
- [GetStartedWithPush （示例）](https://github.com/xamarin/mobile-samples/tree/master/Azure/GetStartedWithPush)
- [NotificationHubs （示例）](https://github.com/xamarin/mobile-samples/tree/master/Azure/NotificationHubs)
- [Azure 移动客户端](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
- [Azure 移动应用学习路径](https://azure.microsoft.com/documentation/learning-paths/appservice-mobileapps/)

<!--
- [ValidateModifyData (sample)](https://github.com/xamarin/mobile-samples/tree/master/Azure/ValidateModifyData)
-->
