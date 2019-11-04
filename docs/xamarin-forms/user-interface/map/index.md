---
title: Xamarin. Forms 地图
description: 地图控件显示一个地图，并需要 Xamarin. Maps NuGet 包。
ms.prod: xamarin
ms.assetid: B669B5EE-D24C-4C69-93E1-2CA5CC9108B5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/29/2019
ms.openlocfilehash: 013e126b76de08442327707cd0502f207826dad8
ms.sourcegitcommit: 3ea19e3a51515b30349d03c70a5b3acd7eca7fe7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/01/2019
ms.locfileid: "73425601"
---
# <a name="xamarinforms-map"></a>Xamarin. Forms 地图

## <a name="initialization-and-configurationsetupmd"></a>[初始化和配置](setup.md)

若要在应用程序中使用地图功能，需要使用[Xamarin](https://www.nuget.org/packages/Xamarin.Forms.Maps/) NuGet NuGet 包。 此外，访问用户的位置时，需要向应用程序授予 location 权限。

## <a name="map-controlmapmd"></a>[Map Control](map.md)

[`Map`](xref:Xamarin.Forms.Maps.Map)控件是一个跨平台视图，用于显示和批注地图。 它使用每个平台的本机地图控件，为用户提供快速且熟悉的地图体验。

## <a name="position-and-distanceposition-distancemd"></a>[位置和距离](position-distance.md)

[`Position`](xref:Xamarin.Forms.Maps.Position)结构通常在定位地图及其引脚时使用，而在定位地图时可以选择使用[`Distance`](xref:Xamarin.Forms.Maps.Distance)结构。

## <a name="pinspinsmd"></a>[锁定](pins.md)

[`Map`](xref:Xamarin.Forms.Maps.Map)控件允许将位置标记[`Pin`](xref:Xamarin.Forms.Maps.Pin)对象。 `Pin` 是在点击时打开信息窗口的地图标记。

## <a name="polygons-and-polylinespolygonsmd"></a>[多边形和折线](polygons.md)

`Polygon` 和 `Polyline` 元素使您能够突出显示地图上的特定区域。 `Polygon` 是一种包含笔划和填充颜色的完全封闭的形状。 `Polyline` 是不完全包围某个区域的行。

## <a name="geocodinggeocodermd"></a>[地理编码](geocoder.md)

[`Geocoder`](xref:Xamarin.Forms.Maps.Geocoder)类在[`Position`](xref:Xamarin.Forms.Maps.Position)对象中存储的字符串地址和纬度和经度坐标之间进行转换。

## <a name="launch-the-native-map-appnative-map-appmd"></a>[启动本机映射应用](native-map-app.md)

可以通过 Xamarin `Launcher` 类从 Xamarin 应用程序中启动每个平台上的本机映射应用。
