---
title: 如何将 Google Maps 和 Location 与 Xamarin 配合使用
description: 本文介绍如何将 maps 和 location 与 Xamarin 一起使用。 其中包括利用内置地图应用程序直接使用 Google Maps Android API V2 的所有内容。
ms.prod: xamarin
ms.assetid: 425E0ED2-5380-6EBE-7059-256B6E9128B8
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/25/2018
ms.openlocfilehash: 194f82757b0b4cb5e148e06d4303dc0d22afb9b3
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70761711"
---
# <a name="how-to-use-google-maps-and-location-with-xamarinandroid"></a>如何将 Google Maps 和 Location 与 Xamarin 配合使用

_本文介绍如何将 maps 和 location 与 Xamarin 一起使用。其中包括利用内置地图应用程序直接使用 Google Maps Android API V2 的所有内容。_

## <a name="maps-overview"></a>映射概述

映射技术是移动设备的普遍补充。 台式计算机和便携式计算机不会在位置感知内置。 另一方面，移动设备使用此类应用程序来查找设备，并显示变化的位置信息。 Android 具有强大的内置技术，可在设备上使用可能提供的位置硬件显示地图上的位置数据。 本文介绍了在 Xamarin 中映射应用程序所需的各种功能，包括： 

- 使用内置 maps 应用程序快速添加映射功能。
- 使用 Maps API 控制地图的显示。
- 使用多种技术来添加图形覆盖。

本节中的主题涵盖各种映射功能。
首先，他们将介绍如何利用 Android 的内置地图应用程序以及如何显示位置的全景街道视图。 然后讨论如何使用 Maps API 直接在应用程序中合并映射功能，同时涵盖如何控制地图的位置和显示，以及如何添加图形覆盖。

## <a name="related-links"></a>相关链接

- [MapsAndLocationDemo_v3 (sample)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/mapsandlocationdemo-v3)
- [活动生命周期](~/android/app-fundamentals/activity-lifecycle/index.md)
- [获取 Google Maps API 密钥](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)
- [意向列表：在 Android 设备上调用 Google 应用程序](https://developer.android.com/guide/appendix/g-app-intents.html)
- [位置和地图](https://developer.android.com/guide/topics/location/index.html)
