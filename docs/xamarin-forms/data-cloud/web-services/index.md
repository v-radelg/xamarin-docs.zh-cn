---
title: Xamarin.Forms 和 Web 服务
description: 本指南介绍如何为与另一个 web 服务，以提供创建、 读取、 更新和删除 (CRUD) 功能到 Xamarin.Forms 应用程序。 涵盖的主题包括与 ASMX 服务、 WCF 服务、 REST 服务进行通信。
ms.prod: xamarin
ms.assetid: 8B360BDA-E4E3-4A3F-9004-0E35362F49F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/27/2019
ms.openlocfilehash: 1cf2714191528c5619b4f877bcb43e80464c44d1
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2019
ms.locfileid: "67659004"
---
# <a name="xamarinforms-and-web-services"></a>Xamarin.Forms 和 Web 服务

## <a name="introductionintroductionmd"></a>[介绍](introduction.md)

本文演示如何与另一个 web 服务进行通信的 Xamarin.Forms 示例应用程序的演练。 涵盖的主题包括页面、 数据模型中，应用程序剖析和调用 web 服务操作。

## <a name="consume-an-aspnet-web-service-asmxxamarin-formsdata-cloudweb-servicesasmxmd"></a>[使用 ASP.NET Web 服务 (ASMX)](~/xamarin-forms/data-cloud/web-services/asmx.md)

ASP.NET Web 服务 (ASMX) 提供的功能生成使用简单对象访问协议 (SOAP) 通过 HTTP 发送消息的 web 服务。 SOAP 是一种独立于平台的和独立于语言的构建和访问 web 服务协议。 一个 ASMX 服务的使用者不需要完全了解平台、 对象模型中或用于实现服务的编程语言。 它们只需了解如何发送和接收 SOAP 消息。 本文演示如何使用 Xamarin.Forms 应用程序中的 ASMX web 服务。

## <a name="consume-a-windows-communication-foundation-wcf-web-servicexamarin-formsdata-cloudweb-serviceswcfmd"></a>[使用 Windows Communication Foundation (WCF) Web 服务](~/xamarin-forms/data-cloud/web-services/wcf.md)

WCF 是 Microsoft 的统一的框架，用于构建面向服务的应用程序。 它允许开发人员构建安全、 可靠、 事务处理，且可互操作分布式应用程序。 ASP.NET Web 服务 (ASMX) 和 WCF 之间有差异，但务必要了解 WCF 支持 ASMX 提供的相同功能，通过 HTTP 的 SOAP 消息。 本文演示如何使用 Xamarin.Forms 应用程序从 WCF SOAP 服务。

## <a name="consume-a-restful-web-servicexamarin-formsdata-cloudweb-servicesrestmd"></a>[使用 RESTful Web 服务](~/xamarin-forms/data-cloud/web-services/rest.md)

具象状态传输 (REST) 是用于构建 web 服务的体系结构样式。 REST 请求是通过发出 HTTP 使用 web 浏览器使用检索 web 页面，并将数据发送到服务器的同一 HTTP 谓词。 本文演示如何使用 Xamarin.Forms 应用程序中的 RESTful web 服务。
