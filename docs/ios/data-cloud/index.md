---
title: Xamarin iOS 应用中的数据和云服务
description: 本文档链接到介绍如何在 Xamarin iOS 应用程序中使用本地数据、iCloud 和 CloudKit 的指南。
ms.prod: xamarin
ms.assetid: 945719F7-7CE6-4207-BF0F-23195125FC84
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 06/13/2017
ms.openlocfilehash: 1c60c1631fd3ecdccf4a91840cfca9ee6116a367
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70289904"
---
# <a name="data-and-cloud-services-in-xamarinios-apps"></a>Xamarin iOS 应用中的数据和云服务

## <a name="data-accessiosdata-clouddataindexmd"></a>[数据访问](~/ios/data-cloud/data/index.md)

大多数应用程序都需要在本地将数据保存在设备上。 除非数据量完全小，否则这通常需要应用程序中的数据库和数据层来管理数据库访问。 iOS 具有 Sqlite 数据库引擎 "内置"，并通过包含 SQLite 数据提供程序的 Xamarin 平台简化对数据的访问。

## <a name="icloudiosdata-cloudintroduction-to-icloudmd"></a>[iCloud](~/ios/data-cloud/introduction-to-icloud.md)

IOS 5 中的 iCloud 存储 API 允许应用程序将用户文档和特定于应用程序的数据保存到一个中心位置，并从用户的所有设备访问这些项。

## <a name="cloudkitiosdata-cloudintro-to-cloudkitmd"></a>[CloudKit](~/ios/data-cloud/intro-to-cloudkit.md)

CloudKit 框架可简化访问 iCloud 的应用程序的开发。 这包括检索应用程序数据和资产权限以及能够安全地存储应用程序信息。 此工具包允许用户使用其 iCloud Id 访问应用程序，而无需共享个人信息。
