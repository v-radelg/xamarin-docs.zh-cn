---
title: Xamarin.Forms Web 服务身份验证
description: 本指南介绍如何将身份验证服务集成到 Xamarin.Forms 应用程序以使用户能够在仅有权访问他们自己的数据的同时共享一个后端。
ms.prod: xamarin
ms.assetid: E6FCFAE1-4F83-4F93-9190-EC5290360C54
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/27/2019
ms.openlocfilehash: a36dfba7aa07de1633ca9620674ddb4887902ba9
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2019
ms.locfileid: "67650427"
---
# <a name="xamarinforms-web-service-authentication"></a>Xamarin.Forms Web 服务身份验证

## <a name="authenticate-a-restful-web-servicerestmd"></a>[RESTful Web 服务进行身份验证](rest.md)

HTTP 支持使用多个身份验证机制来控制对资源的访问。 基本身份验证提供给具有正确的凭据这些客户端对资源的访问。 本文介绍如何使用基本身份验证保护 RESTful web 服务资源的访问权限。

## <a name="authenticate-users-with-an-identity-provideroauthmd"></a>[与标识提供程序的用户进行身份验证](oauth.md)

Xamarin.Auth 是用于对用户进行身份验证和存储其帐户的跨平台 SDK。 它包括对使用 Google、 Microsoft、 Facebook 和 Twitter 等标识提供程序提供支持的 OAuth 身份验证器。 本文介绍如何使用 Xamarin.Auth 管理 Xamarin.Forms 应用程序中的身份验证过程。

## <a name="authenticate-users-with-azure-active-directory-b2cazure-ad-b2cmd"></a>[使用 Azure Active Directory B2C 的用户进行身份验证](azure-ad-b2c.md)

Azure Active Directory B2C 是面向消费者的 web 和移动应用程序的云标识管理解决方案。 本文介绍如何使用 Microsoft 身份验证库 (MSAL) 和 Azure Active Directory B2C 集成到 Xamarin.Forms 应用程序的使用者标识管理。

## <a name="authenticate-users-with-an-azure-cosmos-db-document-database-and-xamarinformsazure-cosmosdb-authmd"></a>[使用 Azure Cosmos DB 文档数据库和 Xamarin.Forms 的用户进行身份验证](azure-cosmosdb-auth.md)

Azure Cosmos DB 文档数据库支持已分区的集合，它可以跨多个服务器和分区，同时支持无限的存储和吞吐量。 本文介绍如何将访问控制与已分区集合相结合，以便用户只能访问自己的 Xamarin.Forms 应用程序中的文档。
