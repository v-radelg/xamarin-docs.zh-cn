---
title: 突出显示地图上的圆形区域
description: 本文介绍如何向地图添加圆形叠加层，以突出显示地图上的某一圆形区域。 虽然 iOS 和 Android 提供用于向地图添加圆形叠加层的 API，但在 UWP 上，叠加层将呈现为多边形。
ms.prod: xamarin
ms.assetid: 6FF8BD15-074E-4E6A-9522-F9E2BE32EF12
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: f5a7dd9cca006157589f6fc43b41764129d90c86
ms.sourcegitcommit: b23a107b0fe3d2f814ae35b52a5855b6ce2a3513
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2019
ms.locfileid: "65926652"
---
# <a name="highlighting-a-circular-area-on-a-map"></a>突出显示地图上的圆形区域

[![下载示例](~/media/shared/download.png) 下载示例](https://developer.xamarin.com/samples/xamarin-forms/CustomRenderers/Map/Circle/)

本文介绍如何向地图添加圆形叠加层，以突出显示地图上的某一圆形区域。

## <a name="overview"></a>概述

叠加层是地图上的分层图形。 叠加层支持绘制在地图缩放时随之缩放的图形内容。 以下屏幕截图显示了向地图添加圆形叠加层的结果：

![](circle-map-overlay-images/screenshots.png)

当 Xamarin.Forms 应用程序呈现 [`Map`](xref:Xamarin.Forms.Maps.Map) 控件时，将在 iOS 中实例化 `MapRenderer` 类，而该操作又会实例化本机 `MKMapView` 控件。 在 Android 平台上，`MapRenderer` 类实例化本机 `MapView` 控件。 在通用 Windows 平台 (UWP) 上，`MapRenderer` 类实例化本机 `MapControl`。 通过在每个平台上为 `Map` 创建自定义呈现器，可以利用呈现过程来实现特定于平台的地图自定义。 执行此操作的过程如下：

1. [创建](#Creating_the_Custom_Map) Xamarin.Forms 自定义地图。
1. [使用](#Consuming_the_Custom_Map) Xamarin.Forms 中的自定义地图。
1. 通过在每个平台上为地图创建自定义呈现器来[自定义](#Customizing_the_Map)地图。

> [!NOTE]
> 必须在使用前对 [`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps) 进行初始化和配置。 有关详细信息，请参阅 [`Maps Control`](~/xamarin-forms/user-interface/map.md)

有关使用自定义呈现器自定义地图的信息，请参阅[自定义图钉](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md)。

<a name="Creating_the_Custom_Map" />

### <a name="creating-the-custom-map"></a>创建自定义地图

创建一个具有 `Position` 和 `Radius` 属性的 `CustomCircle` 类：

```csharp
public class CustomCircle
{
  public Position Position { get; set; }
  public double Radius { get; set; }
}
```

然后，创建一个 [`Map`](xref:Xamarin.Forms.Maps.Map) 类的子类，该子类添加类型为 `CustomCircle` 的属性：

```csharp
public class CustomMap : Map
{
  public CustomCircle Circle { get; set; }
}
```

<a name="Consuming_the_Custom_Map" />

### <a name="consuming-the-custom-map"></a>使用自定义地图

通过在 XAML 页面实例中声明 `CustomMap` 控件的实例以使用该控件：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:MapOverlay;assembly=MapOverlay"
             x:Class="MapOverlay.MapPage">
    <ContentPage.Content>
        <local:CustomMap x:Name="customMap" MapType="Street" WidthRequest="{x:Static local:App.ScreenWidth}" HeightRequest="{x:Static local:App.ScreenHeight}" />
    </ContentPage.Content>
</ContentPage>
```

或者，通过在 C# 页面实例中声明 `CustomMap` 控件的实例以使用该控件：

```csharp
public class MapPageCS : ContentPage
{
    public MapPageCS ()
    {
        var customMap = new CustomMap {
            MapType = MapType.Street,
            WidthRequest = App.ScreenWidth,
            HeightRequest = App.ScreenHeight
        };
        ...
        Content = customMap;
    }
}
```

根据需要初始化 `CustomMap` 控件：

```csharp
public partial class MapPage : ContentPage
{
  public MapPage ()
  {
    ...
    var pin = new Pin {
      Type = PinType.Place,
      Position = new Position (37.79752, -122.40183),
      Label = "Xamarin San Francisco Office",
      Address = "394 Pacific Ave, San Francisco CA"
    };

    var position = new Position (37.79752, -122.40183);
    customMap.Circle = new CustomCircle {
      Position = position,
      Radius = 1000
    };

    customMap.Pins.Add (pin);
    customMap.MoveToRegion (MapSpan.FromCenterAndRadius (position, Distance.FromMiles (1.0)));
  }
}
```

此初始化会将 [`Pin`](xref:Xamarin.Forms.Maps.Pin) 和 `CustomCircle` 实例添加到自定义地图中，并使用 [`MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion*) 方法（该方法通过根据 [`Position`](xref:Xamarin.Forms.Maps.Position) 和 [`Distance`](xref:Xamarin.Forms.Maps.Distance) 创建 [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) 来更改地图的位置和缩放级别）定位地图的视图。

<a name="Customizing_the_Map" />

### <a name="customizing-the-map"></a>自定义地图

现在必须将自定义呈现器添加到每个应用程序项目中，以将圆形叠加层添加到地图中。

#### <a name="creating-the-custom-renderer-on-ios"></a>在 iOS 上创建自定义呈现器

创建 `MapRenderer` 类的一个子类并替代其 `OnElementChanged` 方法以添加圆形叠加层：

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace MapOverlay.iOS
{
    public class CustomMapRenderer : MapRenderer
    {
        MKCircleRenderer circleRenderer;

        protected override void OnElementChanged(ElementChangedEventArgs<View> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null) {
                var nativeMap = Control as MKMapView;
                if (nativeMap != null) {
                    nativeMap.RemoveOverlays(nativeMap.Overlays);
                    nativeMap.OverlayRenderer = null;
                    circleRenderer = null;
                }
            }

            if (e.NewElement != null) {
                var formsMap = (CustomMap)e.NewElement;
                var nativeMap = Control as MKMapView;
                var circle = formsMap.Circle;

                nativeMap.OverlayRenderer = GetOverlayRenderer;

                var circleOverlay = MKCircle.Circle(new CoreLocation.CLLocationCoordinate2D(circle.Position.Latitude, circle.Position.Longitude), circle.Radius);
                nativeMap.AddOverlay(circleOverlay);
            }
        }
        ...
    }
}

```

如果自定义呈现器附加到新的 Xamarin.Forms 元素，则此方法执行以下配置：

- 将 `MKMapView.OverlayRenderer` 属性设置为相应的委托。
- 通过设置静态 `MKCircle` 对象（指定圆的中心和以米为单位的圆的半径）来创建圆。
- 通过调用 `MKMapView.AddOverlay` 方法将圆添加到地图中。

然后，实现 `GetOverlayRenderer` 方法以自定义叠加层的呈现：

```csharp
public class CustomMapRenderer : MapRenderer
{
  MKCircleRenderer circleRenderer;
  ...

  MKOverlayRenderer GetOverlayRenderer(MKMapView mapView, IMKOverlay overlayWrapper)
  {
      if (circleRenderer == null && !Equals(overlayWrapper, null)) {
          var overlay = Runtime.GetNSObject(overlayWrapper.Handle) as IMKOverlay;
          circleRenderer = new MKCircleRenderer(overlay as MKCircle) {
              FillColor = UIColor.Red,
              Alpha = 0.4f
          };
      }
      return circleRenderer;
  }
}
```

#### <a name="creating-the-custom-renderer-on-android"></a>在 Android 上创建自定义呈现器

创建 `MapRenderer` 类的一个子类并替代其 `OnElementChanged` 和 `OnMapReady` 方法以添加圆形叠加层：

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace MapOverlay.Droid
{
    public class CustomMapRenderer : MapRenderer
    {
        CustomCircle circle;

        public CustomMapRenderer(Context context) : base(context)
        {
        }

        protected override void OnElementChanged(Xamarin.Forms.Platform.Android.ElementChangedEventArgs<Xamarin.Forms.Maps.Map> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                // Unsubscribe
            }

            if (e.NewElement != null)
            {
                var formsMap = (CustomMap)e.NewElement;
                circle = formsMap.Circle;
                Control.GetMapAsync(this);
            }
        }

        protected override void OnMapReady(Android.Gms.Maps.GoogleMap map)
        {
            base.OnMapReady(map);

            var circleOptions = new CircleOptions();
            circleOptions.InvokeCenter(new LatLng(circle.Position.Latitude, circle.Position.Longitude));
            circleOptions.InvokeRadius(circle.Radius);
            circleOptions.InvokeFillColor(0X66FF0000);
            circleOptions.InvokeStrokeColor(0X66FF0000);
            circleOptions.InvokeStrokeWidth(0);

            NativeMap.AddCircle(circleOptions);
        }
    }
}
```

如果自定义呈现器附加到新的 Xamarin.Forms 元素，则`OnElementChanged` 方法调用 `MapView.GetMapAsync` 方法，该方法获取与视图关联的基础 `GoogleMap`。 一旦 `GoogleMap` 实例可用，便调用 `OnMapReady` 方法，在此情况下通过实例化静态 `CircleOptions` 对象（指定圆的中心和以米为单位的圆的半径）来创建圆。 然后，通过调用 `NativeMap.AddCircle` 方法将圆添加到地图中。

#### <a name="creating-the-custom-renderer-on-the-universal-windows-platform"></a>在通用 Windows 平台上创建自定义呈现器

创建 `MapRenderer` 类的一个子类并替代其 `OnElementChanged` 方法以添加圆形叠加层：

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace MapOverlay.UWP
{
    public class CustomMapRenderer : MapRenderer
    {
        const int EarthRadiusInMeteres = 6371000;

        protected override void OnElementChanged(ElementChangedEventArgs<Map> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                // Unsubscribe
            }
            if (e.NewElement != null)
            {
                var formsMap = (CustomMap)e.NewElement;
                var nativeMap = Control as MapControl;
                var circle = formsMap.Circle;

                var coordinates = new List<BasicGeoposition>();
                var positions = GenerateCircleCoordinates(circle.Position, circle.Radius);
                foreach (var position in positions)
                {
                    coordinates.Add(new BasicGeoposition { Latitude = position.Latitude, Longitude = position.Longitude });
                }

                var polygon = new MapPolygon();
                polygon.FillColor = Windows.UI.Color.FromArgb(128, 255, 0, 0);
                polygon.StrokeColor = Windows.UI.Color.FromArgb(128, 255, 0, 0);
                polygon.StrokeThickness = 5;
                polygon.Path = new Geopath(coordinates);
                nativeMap.MapElements.Add(polygon);
            }
        }
        // GenerateCircleCoordinates helper method (below)
    }
}
```

如果自定义呈现器附加到新的 Xamarin.Forms 元素，则此方法执行以下操作：

- 从 `CustomMap.Circle` 属性中检索圆的位置和半径并将其传递给 `GenerateCircleCoordinates` 方法，该方法生成圆周的纬度和经度坐标。 此帮助程序方法的代码如下所示。
- 圆周坐标转换为 `BasicGeoposition` 坐标 `List`。
- 通过实例化 `MapPolygon` 对象来创建圆。 将 `Path` 属性设置为包含形状坐标的 `Geopath` 对象，使 `MapPolygon` 类用于在地图上显示多点形状。
- 通过将多边形添加到 `MapControl.MapElements` 集合中，可在地图上呈现多边形。


```
List<Position> GenerateCircleCoordinates(Position position, double radius)
{
    double latitude = position.Latitude.ToRadians();
    double longitude = position.Longitude.ToRadians();
    double distance = radius / EarthRadiusInMeteres;
    var positions = new List<Position>();

    for (int angle = 0; angle <=360; angle++)
    {
        double angleInRadians = ((double)angle).ToRadians();
        double latitudeInRadians = Math.Asin(Math.Sin(latitude) * Math.Cos(distance) + Math.Cos(latitude) * Math.Sin(distance) * Math.Cos(angleInRadians));
        double longitudeInRadians = longitude + Math.Atan2(Math.Sin(angleInRadians) * Math.Sin(distance) * Math.Cos(latitude), Math.Cos(distance) - Math.Sin(latitude) * Math.Sin(latitudeInRadians));

        var pos = new Position(latitudeInRadians.ToDegrees(), longitudeInRadians.ToDegrees());
        positions.Add(pos);
    }

    return positions;
}
```

## <a name="summary"></a>总结

本文介绍了如何向地图添加圆形叠加层，以突出显示地图上的某一圆形区域。


## <a name="related-links"></a>相关链接

- [圆形地图叠加层（示例）](https://developer.xamarin.com/samples/xamarin-forms/CustomRenderers/Map/Circle/)
- [自定义图钉](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md)
- [Xamarin.Forms.Maps](xref:Xamarin.Forms.Maps)
