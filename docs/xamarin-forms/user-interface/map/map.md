---
title: Xamarin Map Control
description: 地图控件是一个跨平台视图，用于显示和批注地图。 它使用每个平台的本机地图控件，为用户提供快速且熟悉的地图体验。
ms.prod: xamarin
ms.assetid: 22C99029-0B16-43A6-BF58-26B48C4AED38
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/29/2019
ms.openlocfilehash: 1cfda90360557af1160d421f18807f8b534967a8
ms.sourcegitcommit: 3ea19e3a51515b30349d03c70a5b3acd7eca7fe7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/01/2019
ms.locfileid: "73426347"
---
# <a name="xamarinforms-map-control"></a>Xamarin Map Control

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

[`Map`](xref:Xamarin.Forms.Maps.Map)控件是一个跨平台视图，用于显示和批注地图。 它使用每个平台的本机地图控件，为用户提供快速且熟悉的地图体验：

[![在 iOS 和 Android 上的地图控件的屏幕截图](map-images/map-default.png "地图控件")](map-images/map-default-large.png#lightbox "地图控件")

[`Map`](xref:Xamarin.Forms.Maps.Map)类定义以下属性，这些属性控制地图的外观和行为：

- [`IsShowingUser`](xref:Xamarin.Forms.Maps.Map.IsShowingUser)，类型 `bool`，指示地图是否显示用户的当前位置。
- [`ItemsSource`](xref:Xamarin.Forms.Maps.Map.ItemsSource)类型 `IEnumerable`，它指定要显示的 `IEnumerable` 项的集合。
- [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)类型的[`ItemTemplate`](xref:Xamarin.Forms.Maps.Map.ItemTemplate)，它指定要应用于显示的项集合中的每个项的[`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 。
- `ItemTemplateSelector`类型[`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector)，它指定在运行时用于为项选择[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)的[`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) 。
- `bool`类型的[`HasScrollEnabled`](xref:Xamarin.Forms.Maps.Map.HasScrollEnabled)确定是否允许滚动映射。
- [`HasZoomEnabled`](xref:Xamarin.Forms.Maps.Map.HasZoomEnabled)，类型 `bool`，确定是否允许地图缩放。
- `IList<MapElement>`类型的 `MapElements`表示地图上的元素列表，如多边形和折线。
- [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType)类型[`MapType`](xref:Xamarin.Forms.Maps.Map.MapType)，指示地图的显示样式。
- `bool`类型的 `MoveToLastRegionOnLayoutChange`，控制在发生布局更改时，所显示的地图区域是否将从其当前区域移到先前设置的区域。
- `IList<Pin>`类型的[`Pins`](xref:Xamarin.Forms.Maps.Map.Pins)表示地图上的 pin 列表。
- [`VisibleRegion`](xref:Xamarin.Forms.Maps.Map.VisibleRegion)类型[`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan)，返回当前显示的地图区域。

这些属性（`MapElements`、`Pins`和 `VisibleRegion` 属性除外）都是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)对象支持的，这意味着它们可以是数据绑定的目标。

[`Map`](xref:Xamarin.Forms.Maps.Map)类还定义了在点击地图时触发的 `MapClicked` 事件。 事件附带的 `MapClickedEventArgs` 对象具有类型[`Position`](xref:Xamarin.Forms.Maps.Position)的名为 `Position`的单个属性。 触发事件时，`Position` 属性设置为点击的地图位置。 有关[`Position`](xref:Xamarin.Forms.Maps.Position)结构的信息，请参阅[地图位置和距离](position-distance.md)。

有关[`ItemsSource`](xref:Xamarin.Forms.Maps.Map.ItemsSource)、 [`ItemTemplate`](xref:Xamarin.Forms.Maps.Map.ItemTemplate)和 `ItemTemplateSelector` 属性的信息，请参阅[显示 pin 集合](pins.md#display-a-pin-collection)。

## <a name="display-a-map"></a>显示地图

可以通过将其添加到布局或页面来显示[`Map`](xref:Xamarin.Forms.Maps.Map) ：

```xaml
<ContentPage ...
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps">
    <maps:Map x:Name="map" />
</ContentPage>
```

> [!NOTE]
> 引用 Xamarin. Maps 控件需要额外的 `xmlns` 命名空间定义。 在前面的示例中，通过 `maps` 关键字引用 `Xamarin.Forms.Maps` 命名空间。

等效 C# 代码如下：

```csharp
using Xamarin.Forms;
using Xamarin.Forms.Maps;

namespace WorkingWithMaps
{
    public class MapTypesPageCode : ContentPage
    {
        public MapTypesPageCode()
        {
            Map map = new Map();
            Content = map;
        }
    }
}
```

此示例将调用默认的[`Map`](xref:Xamarin.Forms.Maps.Map)构造函数，该构造函数将地图居中：

[![在 iOS 和 Android 上具有默认位置的地图控件屏幕截图](map-images/map-default.png "具有默认位置的地图控件")](map-images/map-default-large.png#lightbox "具有默认位置的地图控件")

或者，可以将[`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan)参数传递到[`Map`](xref:Xamarin.Forms.Maps.Map)构造函数，以便在加载时设置地图的中心点和缩放级别。 有关详细信息，请参阅[在地图上显示特定位置](#display-a-specific-location-on-a-map)。

## <a name="map-types"></a>映射类型

[`Map.MapType`](xref:Xamarin.Forms.Maps.Map.MapType)属性可以设置为[`MapType`](xref:Xamarin.Forms.Maps.MapType)枚举成员，以定义地图的显示样式。 `MapType` 枚举定义下列成员：

- `Street` 指定将显示街道地图。
- `Satellite` 指定将显示包含附属图像的地图。
- `Hybrid` 指定将显示合并了街道和附属数据的映射。

默认情况下，如果未定义[`MapType`](xref:Xamarin.Forms.Maps.Map.MapType)属性， [`Map`](xref:Xamarin.Forms.Maps.Map)将显示街道地图。 或者，可以将 `MapType` 属性设置为[`MapType`](xref:Xamarin.Forms.Maps.MapType)枚举成员之一：

```xaml
<maps:Map MapType="Satellite" />
```

等效 C# 代码如下：

```csharp
Map map = new Map
{
    MapType = MapType.Satellite
};
```

以下屏幕截图显示了在将[`MapType`](xref:Xamarin.Forms.Maps.Map.MapType)属性设置为 `Street`时的[`Map`](xref:Xamarin.Forms.Maps.Map) ：

[![IOS 和 Android 上带有街道地图类型的地图控件屏幕截图](map-images/maptype-street.png "带有街道 maptype 的地图控件")](map-images/maptype-street-large.png#lightbox "Map control with the street map type")

以下屏幕截图显示了在将[`MapType`](xref:Xamarin.Forms.Maps.Map.MapType)属性设置为 `Satellite`时的[`Map`](xref:Xamarin.Forms.Maps.Map) ：

[![在 iOS 和 Android 上具有附属映射类型的地图控件屏幕截图](map-images/maptype-satellite.png "具有附属 maptype 的地图控件")](map-images/maptype-satellite-large.png#lightbox "Map control with the satellite map type")

以下屏幕截图显示了在将[`MapType`](xref:Xamarin.Forms.Maps.Map.MapType)属性设置为 `Hybrid`时的[`Map`](xref:Xamarin.Forms.Maps.Map) ：

[![在 iOS 和 Android 上具有混合地图类型的地图控件屏幕截图](map-images/maptype-hybrid.png "混合 maptype 的地图控件")](map-images/maptype-hybrid-large.png#lightbox "Map control with the hybrid map type")

## <a name="display-a-specific-location-on-a-map"></a>显示地图上的特定位置

可以通过将[`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan)参数传递到[`Map`](xref:Xamarin.Forms.Maps.Map)构造函数来设置映射时显示的地图区域：

```xaml
<maps:Map>
    <x:Arguments>
        <maps:MapSpan>
            <x:Arguments>
                <maps:Position>
                    <x:Arguments>
                        <x:Double>36.9628066</x:Double>
                        <x:Double>-122.0194722</x:Double>
                    </x:Arguments>
                </maps:Position>
                <x:Double>0.01</x:Double>
                <x:Double>0.01</x:Double>
            </x:Arguments>
        </maps:MapSpan>
    </x:Arguments>
</maps:Map>
```

等效 C# 代码如下：

```csharp
Position position = new Position(36.9628066, -122.0194722);
MapSpan mapSpan = new MapSpan(position, 0.01, 0.01);
Map map = new Map(mapSpan);
```

此示例创建一个[`Map`](xref:Xamarin.Forms.Maps.Map)对象，该对象显示[`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan)对象指定的区域。 `MapSpan` 对象在[`Position`](xref:Xamarin.Forms.Maps.Position)对象所代表的纬度和经度上居中，并跨0.01 纬度和0.01 经度度。 有关[`Position`](xref:Xamarin.Forms.Maps.Position)结构的信息，请参阅[地图位置和距离](position-distance.md)。 有关在 XAML 中传递自变量的信息，请参阅[在 xaml 中传递参数](~/xamarin-forms/xaml/passing-arguments.md)。

这样做的结果是：当显示地图时，它将位于特定位置，并跨越特定数量的纬度和经度度：

[![在 iOS 和 Android 上具有指定位置的地图控件的屏幕截图](map-images/map-region.png "具有指定位置的地图控件")](map-images/map-region-large.png#lightbox "具有指定位置的地图控件")

## <a name="create-a-mapspan-object"></a>创建 MapSpan 对象

创建[`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan)对象的方法有很多。 常见的方法是为 `MapSpan` 构造函数提供必需的参数。 这些是由[`Position`](xref:Xamarin.Forms.Maps.Position)对象表示的纬度和经度，`double` 表示 `MapSpan`跨的纬度和经度度的值。 有关[`Position`](xref:Xamarin.Forms.Maps.Position)结构的信息，请参阅[地图位置和距离](position-distance.md)。

另外， [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan)类中有三种方法可返回新的 `MapSpan` 对象：

1. [`ClampLatitude`](xref:Xamarin.Forms.Maps.MapSpan.ClampLatitude*)返回 `MapSpan`，其 `LongitudeDegrees` 与方法的类实例相同，以及由其 `north` 和 `south` 参数定义的半径。
1. [`FromCenterAndRadius`](xref:Xamarin.Forms.Maps.MapSpan.FromCenterAndRadius*)返回由其[`Position`](xref:Xamarin.Forms.Maps.Position)和[`Distance`](xref:Xamarin.Forms.Maps.Distance)参数定义的 `MapSpan`。
1. [`WithZoom`](xref:Xamarin.Forms.Maps.MapSpan.WithZoom*)返回与方法的类实例具有相同中心的 `MapSpan`，但其半径乘以其 `double` 参数。

有关[`Distance`](xref:Xamarin.Forms.Maps.Distance)结构的信息，请参阅[地图位置和距离](position-distance.md)。

创建[`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan)后，可以访问以下属性来检索有关它的数据：

- [`Center`](xref:Xamarin.Forms.Maps.MapSpan.Center)，表示 `MapSpan`地理位置的[`Position`](xref:Xamarin.Forms.Maps.Position) 。
- [`LatitudeDegrees`](xref:Xamarin.Forms.Maps.MapSpan.LatitudeDegrees)，表示 `MapSpan`跨越的纬度度。
- [`LongitudeDegrees`](xref:Xamarin.Forms.Maps.MapSpan.LongitudeDegrees)，表示 `MapSpan`跨的经度的度数。
- [`Radius`](xref:Xamarin.Forms.Maps.MapSpan.Radius)，表示 `MapSpan` 半径。

## <a name="move-the-map"></a>移动地图

可以调用[`Map.MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion*)方法来更改地图的位置和缩放级别。 此方法接受一个[`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan)参数，该参数定义要显示的地图区域及其缩放级别。

下面的代码演示了在地图上移动所显示区域的示例：

```csharp
MapSpan mapSpan = MapSpan.FromCenterAndRadius(position, Distance.FromKilometers(0.444));
map.MoveToRegion(mapSpan);
```

## <a name="zoom-the-map"></a>缩放地图

可以更改[`Map`](xref:Xamarin.Forms.Maps.Map)的缩放级别，而无需更改其位置。 这可以使用地图 UI 完成，也可以通过使用[`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan)自变量调用[`MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion*)方法来完成，此方法使用当前位置作为[`Position`](xref:Xamarin.Forms.Maps.Position)参数：

```csharp
double zoomLevel = 0.5;
double latlongDegrees = 360 / (Math.Pow(2, zoomLevel));
if (map.VisibleRegion != null)
{
    map.MoveToRegion(new MapSpan(map.VisibleRegion.Center, latlongDegrees, latlongDegrees));
}
```

在此示例中，使用[`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan)参数调用[`MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion*)方法，该参数通过[`Map.VisibleRegion`](xref:Xamarin.Forms.Maps.Map.VisibleRegion)属性指定地图的当前位置，并将缩放级别指定为纬度和经度的度数。 总体结果是更改了地图的缩放级别，但其位置却不是。 在地图上实现缩放的另一种方法是使用[`MapSpan.WithZoom`](xref:Xamarin.Forms.Maps.MapSpan.WithZoom*)方法来控制缩放系数。

> [!IMPORTANT]
> 缩放地图时，无论是通过地图 UI 还是按编程方式，都需要 `true`[`Map.HasZoomEnabled`](xref:Xamarin.Forms.Maps.Map.HasZoomEnabled)属性。 有关此属性的详细信息，请参阅[禁用缩放](#disable-zoom)。

## <a name="customize-map-behavior"></a>自定义映射行为

可以通过设置其一些属性并通过处理 `MapClicked` 事件来自定义[`Map`](xref:Xamarin.Forms.Maps.Map)的行为。

> [!NOTE]
> 可以通过创建地图自定义呈现器来实现其他地图行为 customizatin。 有关详细信息，请参阅[自定义 Xamarin。 Forms 地图](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)。

### <a name="disable-scroll"></a>禁用滚动

[`Map`](xref:Xamarin.Forms.Maps.Map)类定义 `bool`类型的[`HasScrollEnabled`](xref:Xamarin.Forms.Maps.Map.HasScrollEnabled)属性。 默认情况下，此属性为 "`true`"，表示允许滚动映射。 如果将此属性设置为 `false`，则不会滚动映射。 下面的示例演示如何设置此属性：

```xaml
<maps:Map HasScrollEnabled="false" />
```

等效 C# 代码如下：

```csharp
Map map = new Map
{
    HasScrollEnabled = false
};
```

### <a name="disable-zoom"></a>禁用缩放

[`Map`](xref:Xamarin.Forms.Maps.Map)类定义 `bool`类型的[`HasZoomEnabled`](xref:Xamarin.Forms.Maps.Map.HasZoomEnabled)属性。 默认情况下，此属性是 `true`，这表示可以在地图上执行缩放。 如果将此属性设置为 `false`，则无法缩放地图。 下面的示例演示如何设置此属性：

```xaml
<maps:Map HasZoomEnabled="false" />
```

等效 C# 代码如下：

```csharp
Map map = new Map
{
    HasZoomEnabled = false
};
```

### <a name="show-the-users-location"></a>显示用户的位置

[`Map`](xref:Xamarin.Forms.Maps.Map)类定义 `bool`类型的[`IsShowingUser`](xref:Xamarin.Forms.Maps.Map.IsShowingUser)属性。 默认情况下，此属性为 "`false`"，表示该地图未显示用户的当前位置。 当此属性设置为 `true`时，地图将显示用户的当前位置。 下面的示例演示如何设置此属性：

```xaml
<maps:Map IsShowingUser="true" />
```

等效 C# 代码如下：

```csharp
Map map = new Map
{
    IsShowingUser = true
};
```

> [!IMPORTANT]
> 在 iOS、Android 和通用 Windows 平台上，访问用户的位置时，需要授予应用程序的位置权限。 有关详细信息，请参阅[平台配置](setup.md#platform-configuration)。

### <a name="maintain-map-region-on-layout-change"></a>维护布局更改时的地图区域

[`Map`](xref:Xamarin.Forms.Maps.Map)类定义 `bool`类型的 `MoveToLastRegionOnLayoutChange` 属性。 默认情况下，此属性是 `true`，这表示当发生布局更改时，所显示的地图区域将从其当前区域移到先前设置的区域，如设备旋转。 如果将此属性设置为 "`false`"，则在发生布局更改时，所显示的地图区域将保持居中。 下面的示例演示如何设置此属性：

```xaml
<maps:Map MoveToLastRegionOnLayoutChange="false" />
```

等效 C# 代码如下：

```csharp
Map map = new Map
{
    MoveToLastRegionOnLayoutChange = false
};
```

### <a name="map-clicks"></a>地图单击

[`Map`](xref:Xamarin.Forms.Maps.Map)类定义一个 `MapClicked` 事件，该事件在点击地图时激发。 事件附带的 `MapClickedEventArgs` 对象具有类型[`Position`](xref:Xamarin.Forms.Maps.Position)的名为 `Position`的单个属性。 触发事件时，`Position` 属性设置为点击的地图位置。 有关[`Position`](xref:Xamarin.Forms.Maps.Position)结构的信息，请参阅[地图位置和距离](position-distance.md)。

下面的代码示例演示了 `MapClicked` 事件的事件处理程序：

```csharp
void OnMapClicked(object sender, MapClickedEventArgs e)
{
    System.Diagnostics.Debug.WriteLine($"MapClick: {e.Position.Latitude}, {e.Position.Longitude}");
}
```

在此示例中，`OnMapClicked` 事件处理程序输出表示螺纹地图位置的纬度和经度。 事件处理程序可以在 `MapClicked` 事件中进行注册，如下所示：

```xaml
<maps:Map MapClicked="OnMapClicked" />
```

等效 C# 代码如下：

```csharp
Map map = new Map();
map.MapClicked += OnMapClicked;
```

## <a name="related-links"></a>相关链接

- [地图示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
- [地图位置和距离](position-distance.md)
- [自定义 Xamarin 图](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)
- [在 XAML 中传递参数](~/xamarin-forms/xaml/passing-arguments.md)
