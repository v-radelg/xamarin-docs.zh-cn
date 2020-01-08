---
title: Xamarin. Forms Map 地理编码
description: 本文介绍如何使用 Xamarin Geocoder 类地理编码和反向地理编码映射数据。
ms.prod: xamarin
ms.assetid: DE7DB31A-8921-4614-8B49-DAEF1E7B03B3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/22/2019
ms.openlocfilehash: 6df7a2e0056cd2d448206b56e1e9d2600b342b3b
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75490254"
---
# <a name="xamarinforms-map-geocoding"></a>Xamarin. Forms Map 地理编码

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

[`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps)命名空间提供了一个[`Geocoder`](xref:Xamarin.Forms.Maps.Geocoder)类，该类在[`Position`](xref:Xamarin.Forms.Maps.Position)对象中存储的字符串地址和纬度和经度坐标之间进行转换。 有关[`Position`](xref:Xamarin.Forms.Maps.Position)结构的详细信息，请参阅[地图位置和距离](position-distance.md)。

## <a name="geocode-an-address"></a>地理编码地址

街道地址可以通过创建[`Geocoder`](xref:Xamarin.Forms.Maps.Geocoder)实例并对 `Geocoder` 实例调用[`GetPositionsForAddressAsync`](xref:Xamarin.Forms.Maps.Geocoder.GetPositionsForAddressAsync*)方法来地理编码到纬度和经度坐标：

```csharp
using Xamarin.Forms.Maps;
// ...
Geocoder geoCoder = new Geocoder();

IEnumerable<Position> approximateLocations = await geoCoder.GetPositionsForAddressAsync("Pacific Ave, San Francisco, California");
Position position = approximateLocations.FirstOrDefault();
string coordinates = $"{position.Latitude}, {position.Longitude}";
```

[`GetPositionsForAddressAsync`](xref:Xamarin.Forms.Maps.Geocoder.GetPositionsForAddressAsync*)方法采用表示该地址的 `string` 参数，并以异步方式返回可能表示该地址的[`Position`](xref:Xamarin.Forms.Maps.Position)对象的集合。

## <a name="reverse-geocode-an-address"></a>反向地理编码地址

纬度和经度坐标可以通过创建[`Geocoder`](xref:Xamarin.Forms.Maps.Geocoder)实例并在 `Geocoder` 实例上调用[`GetAddressesForPositionAsync`](xref:Xamarin.Forms.Maps.Geocoder.GetAddressesForPositionAsync*)方法来反向地理编码街道地址：

```csharp
using Xamarin.Forms.Maps;
// ...
Geocoder geoCoder = new Geocoder();

Position position = new Position(37.8044866, -122.4324132);
IEnumerable<string> possibleAddresses = await geoCoder.GetAddressesForPositionAsync(position);
string address = possibleAddresses.FirstOrDefault();
```

[`GetAddressesForPositionAsync`](xref:Xamarin.Forms.Maps.Geocoder.GetAddressesForPositionAsync*)方法采用由纬度和经度坐标组成的[`Position`](xref:Xamarin.Forms.Maps.Position)参数，并以异步方式返回表示位置附近地址的字符串集合。

## <a name="related-links"></a>相关链接

- [地图示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
- [Xamarin。窗体地图位置和距离](position-distance.md)
- [Geocoder API](xref:Xamarin.Forms.Maps.Geocoder)
