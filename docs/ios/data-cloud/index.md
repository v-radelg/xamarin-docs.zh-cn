---
title: 数据和在 Xamarin.iOS 应用程序中的云服务
description: 此文档所链接到介绍如何使用本地数据、 iCloud、 和 CloudKit 在 Xamarin.iOS 应用程序中的指南。
ms.prod: xamarin
ms.assetid: 945719F7-7CE6-4207-BF0F-23195125FC84
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/13/2017
ms.openlocfilehash: 21a6c1c0c0ceb5596a056f0818dec39041808504
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61218240"
---
# <a name="data-and-cloud-services-in-xamarinios-apps"></a>数据和在 Xamarin.iOS 应用程序中的云服务

##  <a name="data-accessiosdata-clouddataindexmd"></a>[数据访问](~/ios/data-cloud/data/index.md)

大多数应用程序具有一些要求将数据保存在本地设备上。 除非数据量非常小，这通常需要一个数据库和管理数据库的访问权限的应用程序中的数据层。 iOS 具有"内置"Sqlite 数据库引擎和 Xamarin 的平台，其中随附了 SQLite 数据提供程序简化对数据的访问。

##  <a name="icloudiosdata-cloudintroduction-to-icloudmd"></a>[iCloud](~/ios/data-cloud/introduction-to-icloud.md)

在 iOS 5 中的 iCloud 存储 API 允许应用程序将用户文档和特定于应用程序数据保存到一个中心位置并从所有用户的设备访问这些项。

##  <a name="cloudkitiosdata-cloudintro-to-cloudkitmd"></a>[CloudKit](~/ios/data-cloud/intro-to-cloudkit.md)

CloudKit 框架简化了开发应用程序的访问 iCloud。 这包括应用程序数据和资产的权限，以及能够安全地存储应用程序信息的检索。 此工具包通过允许访问其 iCloud Id 与应用程序而无需共享个人信息，使用户匿名的层。