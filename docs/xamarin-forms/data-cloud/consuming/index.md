---
title: 使用 Web 服务
description: 本指南演示如何与另一个 web 服务，以提供通信创建、 读取、 更新和删除 (CRUD) 功能到 Xamarin.Forms 应用程序。 涵盖的主题包括与 ASMX 服务、 WCF 服务、 REST 服务和 Azure 移动应用进行通信。
ms.prod: xamarin
ms.assetid: 8B360BDA-E4E3-4A3F-9004-0E35362F49F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2016
ms.openlocfilehash: a4c842ea7fd37ade9be0a9cb3e3ff7e50a6d1491
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61328185"
---
# <a name="consuming-web-services"></a>使用 Web 服务

（_t） 指南演示如何与另一个 web 服务，以提供通信创建、 读取、 更新和删除 (CRUD) 功能到 Xamarin.Forms 应用程序。 涵盖的主题包括与 ASMX 服务、 WCF 服务、 REST 服务和 Azure 移动应用进行通信。

## <a name="consuming-an-aspnet-web-service-asmxxamarin-formsdata-cloudconsumingasmxmd"></a>[使用 ASP.NET Web 服务 (ASMX)](~/xamarin-forms/data-cloud/consuming/asmx.md)

ASP.NET Web 服务 (ASMX) 提供的功能生成使用简单对象访问协议 (SOAP) 通过 HTTP 发送消息的 web 服务。 SOAP 是一种独立于平台的和独立于语言的构建和访问 web 服务协议。 一个 ASMX 服务的使用者不需要完全了解平台、 对象模型中或用于实现服务的编程语言。 它们只需了解如何发送和接收 SOAP 消息。 本文演示如何使用 Xamarin.Forms 应用程序中的 ASMX web 服务。

## <a name="consuming-a-windows-communication-foundation-wcf-web-servicexamarin-formsdata-cloudconsumingwcfmd"></a>[使用 Windows Communication Foundation (WCF) Web 服务](~/xamarin-forms/data-cloud/consuming/wcf.md)

WCF 是 Microsoft 的统一的框架，用于构建面向服务的应用程序。 它允许开发人员构建安全、 可靠、 事务处理，且可互操作分布式应用程序。 ASP.NET Web 服务 (ASMX) 和 WCF 之间有差异，但务必要了解 WCF 支持 ASMX 提供的相同功能，通过 HTTP 的 SOAP 消息。 本文演示如何使用 Xamarin.Forms 应用程序从 WCF SOAP 服务。

## <a name="consuming-a-restful-web-servicexamarin-formsdata-cloudconsumingrestmd"></a>[使用 RESTful Web 服务](~/xamarin-forms/data-cloud/consuming/rest.md)

具象状态传输 (REST) 是用于构建 web 服务的体系结构样式。 REST 请求是通过发出 HTTP 使用 web 浏览器使用检索 web 页面，并将数据发送到服务器的同一 HTTP 谓词。 本文演示如何使用 Xamarin.Forms 应用程序中的 RESTful web 服务。

## <a name="consuming-an-azure-mobile-appxamarin-formsdata-cloudconsumingazuremd"></a>[使用 Azure 移动应用](~/xamarin-forms/data-cloud/consuming/azure.md)

Azure 移动应用让你能够使用移动身份验证、 脱机同步和推送通知的支持在 Azure 应用服务中托管的可缩放后端进行开发的应用。 此文章，这仅适用于使用 Node.js 后端的 Azure 移动应用，介绍了如何查询、 插入、 更新和删除数据存储在 Azure 移动应用实例中的表。

## <a name="related-links"></a>相关链接

- [Web 服务简介](~/cross-platform/data-cloud/web-services/index.md)
- [异步支持概述](~/cross-platform/platform/async.md)
