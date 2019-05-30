---
title: Xamarin.Essentials:版本跟踪
description: Xamarin.Essentials 中的 VersionTracking 类使你能够检查应用程序版本和内部版本号以及查看其他信息，例如，此应用程序是第一次启动还是当前版本的第一次启动，以及获取之前的内部版本信息等。
ms.assetid: 670C7E8A-E882-4AC0-97D2-A53D90ADD6A3
author: jamesmontemagno
ms.author: jamont
ms.date: 05/28/2019
ms.custom: video
ms.openlocfilehash: 3728a209c99712fad6b3dbf9bc59a2c1a3c7bcd5
ms.sourcegitcommit: 4a1520dee7759f8355ea65c8bb3d1bac8ba58122
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/29/2019
ms.locfileid: "66354114"
---
# <a name="xamarinessentials-version-tracking"></a>Xamarin.Essentials:版本跟踪

VersionTracking 类使你能够检查应用程序版本和内部版本号以及查看其他信息，例如，此应用程序是第一次启动还是当前版本的第一次启动，以及获取之前的内部版本信息等  。

## <a name="get-started"></a>入门

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-version-tracking"></a>使用版本跟踪

在你的类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

首次使用 VersionTracking 类时，它将开始跟踪当前版本  。 每次加载时，必须仅在应用程序中提前调用 `Track` 以确保跟踪当前版本信息：

```csharp
VersionTracking.Track();
```

调用初始 `Track` 后，可以读取版本信息：

```csharp

// First time ever launched application
var firstLaunch = VersionTracking.IsFirstLaunchEver;

// First time launching current version
var firstLaunchCurrent = VersionTracking.IsFirstLaunchForCurrentVersion;

// First time launching current build
var firstLaunchBuild = VersionTracking.IsFirstLaunchForCurrentBuild;

// Current app version (2.0.0)
var currentVersion = VersionTracking.CurrentVersion;

// Current build (2)
var currentBuild = VersionTracking.CurrentBuild;

// Previous app version (1.0.0)
var previousVersion = VersionTracking.PreviousVersion;

// Previous app build (1)
var previousBuild = VersionTracking.PreviousBuild;

// First version of app installed (1.0.0)
var firstVersion = VersionTracking.FirstInstalledVersion;

// First build of app installed (1)
var firstBuild = VersionTracking.FirstInstalledBuild;

// List of versions installed (1.0.0, 2.0.0)
var versionHistory = VersionTracking.VersionHistory;

// List of builds installed (1, 2)
var buildHistory = VersionTracking.BuildHistory;
```

## <a name="platform-implementation-specifics"></a>平台实现细节

所有版本信息均是使用 Xamarin.Essentials 中的 [Preferences](preferences.md) API 存储的，是以 [你的-应用-包-ID].xamarinessentials.versiontracking 为文件名存储的，并且遵循 [Preferences](preferences.md#persistence) 文档中概述的同一数据持久性  。

## <a name="api"></a>API

- [版本跟踪源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/VersionTracking)
- [版本跟踪 API 文档](xref:Xamarin.Essentials.VersionTracking)

## <a name="related-video"></a>相关视频

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Version-Tracking-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
