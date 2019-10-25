---
title: Xamarin。窗体映射多边形和折线
description: 本文介绍如何在 Xamarin. Forms 地图实例上创建多边形和折线。
ms.prod: xamarin
ms.assetid: CDAF0B02-1AA8-4AD6-94A7-ABFC18006A2D
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 09/20/2019
ms.openlocfilehash: 42c00a060e0477aff4ea02f6213fa751b2adf4dd
ms.sourcegitcommit: 5c22097bed2a8d51ecaf6ca197bf4d449dfe1377
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72810494"
---
# <a name="xamarinforms-map-polygons-and-polylines"></a>Xamarin。窗体映射多边形和折线

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

[!["iOS 和 Android 上的多边形和折线演示"](polygons-images/polygon-app-cropped.png)](polygons-images/polygon-app.png#lightbox)

`Polygon` 和 `Polyline` 元素使您能够突出显示地图上的特定区域。 @No__t_0 是一种包含笔划和填充颜色的完全封闭的形状。 @No__t_0 是不完全包围某个区域的行。

> [!NOTE]
> @No__t_0 和 `Polyline` 的示例位于示例项目的**PolygonsPage**中。

@No__t_0 和 `Polyline` 类派生自 `MapElement`，这会公开以下[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)属性：

- `StrokeColor` 是确定线条颜色的 `Color` 属性。
- `StrokeWidth` 是确定线条宽度的 `float` 属性。
- `Geopath` 在 `Polygon` 和 `Polyline` 上定义，是指定形状点的[`Position`](xref:Xamarin.Forms.Maps.Position)对象的列表。

@No__t_0 类定义了其他属性：

- `FillColor` 是确定多边形背景色的 `Color` 属性。

> [!NOTE]
> 如果未指定 `StrokeColor` 属性，则笔划将默认为黑色。 如果未指定 `FillColor` 属性，则填充将默认为透明。 因此，如果两个属性均未指定，则该形状将有一个不带填充的黑色轮廓。

## <a name="create-a-polygon"></a>创建多边形

通过实例化对象并将其添加到地图的 `MapElements` 集合，可以将 `Polygon` 对象添加到地图中。 这可以通过以下操作在 XAML 中实现：

```xaml
<ContentPage ...
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps">
     <maps:Map>
         <maps:Map.MapElements>
             <maps:Polygon StrokeColor="#FF9900"
                           StrokeWidth="8"
                           FillColor="#88FF9900">
                 <maps:Polygon.Geopath>
                     <maps:Position>
                         <x:Arguments>
                             <x:Double>47.6368678</x:Double>
                             <x:Double>-122.137305</x:Double>
                         </x:Arguments>
                     </maps:Position>
                     ...
                 </maps:Polygon.Geopath>
             </maps:Polygon>
         </maps:Map.MapElements>
     </maps:Map>
</ContentPage>
```

等效 C# 代码如下：

```csharp
using Xamarin.Forms.Maps;
// ...
Map map = new Map
{
  // ...
};

// instantiate a polygon
Polygon polygon = new Polygon
{
    StrokeWidth = 8,
    StrokeColor = Color.FromHex("#1BA1E2"),
    FillColor = Color.FromHex("#881BA1E2"),
    Geopath =
    {
        new Position(47.6368678, -122.137305),
        new Position(47.6368894, -122.134655),
        new Position(47.6359424, -122.134655),
        new Position(47.6359496, -122.1325521),
        new Position(47.6424124, -122.1325199),
        new Position(47.642463,  -122.1338932),
        new Position(47.6406414, -122.1344833),
        new Position(47.6384943, -122.1361248),
        new Position(47.6372943, -122.1376912)
    }
};

// add the polygon to the map's MapElements collection
map.MapElements.Add(polygon);
```

指定 `StrokeColor` 和 `StrokeWidth` 属性以自定义多边形的轮廓。 @No__t_0 属性值与 `StrokeColor` 属性值相匹配，但指定了 alpha 值以使其透明，从而使基础地图可以通过形状可见。 @No__t_0 属性包含定义多边形点的地理坐标的 `Position` 对象的列表。 将 `Polygon` 对象添加到 `Map` 的 `MapElements` 集合后，该对象将呈现在该映射上。

> [!NOTE]
> @No__t_0 是完全包围的形状。 如果第一个点和最后一个点不匹配，则将自动进行连接。

## <a name="create-a-polyline"></a>创建折线

通过实例化对象并将其添加到地图的 `MapElements` 集合，可以将 `Polyline` 对象添加到地图中。 这可以通过以下操作在 XAML 中实现：

```xaml
<ContentPage ...
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps">
     <maps:Map>
         <maps:Map.MapElements>
             <maps:Polyline StrokeColor="Blue"
                            StrokeWidth="12">
                 <maps:Polyline.Geopath>
                     <maps:Position>
                         <x:Arguments>
                             <x:Double>47.6381401</x:Double>
                             <x:Double>-122.1317367</x:Double>
                         </x:Arguments>
                     </maps:Position>
                     ...
                 </maps:Polyline.Geopath>
             </maps:Polyline>
         </maps:Map.MapElements>
     </maps:Map>
</ContentPage>
```

```csharp
using Xamarin.Forms.Maps;
// ...
Map map = new Map
{
  // ...
};
// instantiate a polyline
Polyline polyline = new Polyline
{
    StrokeColor = Color.Blue,
    StrokeWidth = 12,
    Geopath =
    {
        new Position(47.6381401, -122.1317367),
        new Position(47.6381473, -122.1350841),
        new Position(47.6382847, -122.1353094),
        new Position(47.6384582, -122.1354703),
        new Position(47.6401136, -122.1360819),
        new Position(47.6403883, -122.1364681),
        new Position(47.6407426, -122.1377019),
        new Position(47.6412558, -122.1404056),
        new Position(47.6414148, -122.1418647),
        new Position(47.6414654, -122.1432702)
    }
};

// add the polyline to the map's MapElements collection
map.MapElements.Add(polyline);
```

指定 `StrokeColor` 和 `StrokeWidth` 属性以自定义该行。 @No__t_0 属性包含定义折线点的地理坐标的 `Position` 对象的列表。 将 `Polyline` 对象添加到 `Map` 的 `MapElements` 集合后，该对象将呈现在该映射上。

## <a name="related-links"></a>相关链接

- [地图示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
