---
title: 对 Web 服务的访问进行身份验证
description: 本指南介绍如何将身份验证服务集成到 Xamarin.Forms 应用程序以使用户能够在仅有权访问他们自己的数据的同时共享一个后端。
ms.prod: xamarin
ms.assetid: E6FCFAE1-4F83-4F93-9190-EC5290360C54
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2016
ms.openlocfilehash: d598a9b3de31ea6823530f911c3544bf3cebb37f
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61331177"
---
# <a name="authenticating-access-to-web-services"></a>对 Web 服务的访问进行身份验证

_本指南介绍如何将身份验证服务集成到 Xamarin.Forms 应用程序以使用户能够在仅有权访问他们自己的数据的同时共享一个后端。使用内置的身份验证机制提供不同的提供程序和涵盖的主题包括与 REST 服务，使用 Xamarin.Auth 组件进行身份验证 OAuth 标识提供程序，使用基本身份验证。_

## <a name="authenticating-a-restful-web-servicerestmd"></a>[对 RESTful Web 服务进行身份验证](rest.md)

HTTP 支持使用多个身份验证机制来控制对资源的访问。 基本身份验证提供给具有正确的凭据这些客户端对资源的访问。 本文演示如何使用基本身份验证保护 RESTful web 服务资源的访问权限。

## <a name="authenticating-users-with-an-identity-provideroauthmd"></a>[与标识提供程序的用户进行身份验证](oauth.md)

Xamarin.Auth 是用于对用户进行身份验证和存储其帐户的跨平台 SDK。 它包括对使用 Google、 Microsoft、 Facebook 和 Twitter 等标识提供程序提供支持的 OAuth 身份验证器。 本文介绍如何使用 Xamarin.Auth 管理 Xamarin.Forms 应用程序中的身份验证过程。

## <a name="authenticating-users-with-azure-mobile-appsazuremd"></a>[使用 Azure 移动应用的用户进行身份验证](azure.md)

Azure 移动应用使用各种外部标识提供程序来支持身份验证和授权应用程序用户。 可以然后将权限设置表上为限制为仅限经过身份验证的用户的访问。 本文介绍如何使用 Azure 移动应用管理 Xamarin.Forms 应用程序中的身份验证过程。

## <a name="authenticating-users-with-azure-active-directory-b2cazure-ad-b2cmd"></a>[使用 Azure Active Directory B2C 的用户进行身份验证](azure-ad-b2c.md)

Azure Active Directory B2C 是面向消费者的 web 和移动应用程序的云标识管理解决方案。 本文演示如何使用 Microsoft 身份验证库 (MSAL) 和 Azure Active Directory B2C 集成到 Xamarin.Forms 应用程序的使用者标识管理。

## <a name="integrating-azure-active-directory-b2c-with-azure-mobile-appsazure-ad-b2c-mobile-appmd"></a>[将 Azure Active Directory B2C 与 Azure 移动应用集成](azure-ad-b2c-mobile-app.md)

可以使用 azure Active Directory B2C 来管理 Azure 移动应用身份验证工作流。 使用此方法时，标识管理体验已完全定义为在云中，并可以修改而无需更改你的移动应用程序代码。 本文演示如何使用 Azure Active Directory B2C 为 Xamarin.Forms 中提供身份验证和授权对 Azure 移动应用实例。

## <a name="related-links"></a>相关链接

- [Web 服务简介](~/cross-platform/data-cloud/web-services/index.md)
- [异步支持概述](~/cross-platform/platform/async.md)
