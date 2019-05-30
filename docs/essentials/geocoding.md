---
title: Xamarin.Essentials:地理编码
description: Xamarin.Essentials 中的 Geocoding 类提供了 API，既可以将地标地理编码为位置坐标，又可以将坐标反向地理编码为地标。
ms.assetid: 3ADC440C-B000-4708-A2CC-296F5160AF90
author: jamesmontemagno
ms.author: jamont
ms.date: 05/28/2019
ms.custom: video
ms.openlocfilehash: 157eb3116f09268790036f8983543114e7a58276
ms.sourcegitcommit: 4a1520dee7759f8355ea65c8bb3d1bac8ba58122
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/29/2019
ms.locfileid: "66354111"
---
# <a name="xamarinessentials-geocoding"></a>Xamarin.Essentials:地理编码

Geocoding 类提供了 API，既可以将地标地理编码为位置坐标，又可以将坐标反向地理编码为地标  。

## <a name="get-started"></a>入门

[!include[](~/essentials/includes/get-started.md)]

若要访问 Geocoding 功能，需要以下特定于平台的设置  。

# <a name="androidtabandroid"></a>[Android](#tab/android)

无需其他设置。

# <a name="iostabios"></a>[iOS](#tab/ios)

无需其他设置。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

需要必应地图 API 密钥才能使用地理编码功能。 注册免费的[必应地图](https://www.bingmapsportal.com/)帐户。 在“我的帐户”>“我的密钥”下创建一个新密钥，并根据你的应用程序类型（对于 UWP 应用，应该是公共 Windows 应用（UWP、8.x 及更早版本））填写信息   。

在应用程序生命周期的早期调用任何 Geocoding 方法之前，应设置 API 密钥（仅在 UWP 上可用）  ：

```csharp
Platform.MapServiceToken = "YOUR-KEY-HERE";
```

-----

## <a name="using-geocoding"></a>使用 Geocoding

在你的类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

获取一个地址的[位置](xref:Xamarin.Essentials.Location)坐标：

```csharp
try
{
    var address =  "Microsoft Building 25 Redmond WA USA";
    var locations = await Geocoding.GetLocationsAsync(address);

    var location = locations?.FirstOrDefault();
    if (location != null)
    {
        Console.WriteLine($"Latitude: {location.Latitude}, Longitude: {location.Longitude}, Altitude: {location.Altitude}");
    }
}
catch (FeatureNotSupportedException fnsEx)
{
    // Feature not supported on device
}
catch (Exception ex)
{
    // Handle exception that may have occurred in geocoding
}
```

高度并非总是可用的。 如果不可用，`Altitude` 属性可能为 `null` 或值可能为零。 如果高度可用，此值为海拔高度（以米为单位）。

## <a name="using-reverse-geocoding"></a>使用反向地理编码

反向地理编码是获取现有一组坐标对应的[地标](xref:Xamarin.Essentials.Placemark)的过程：

```csharp
try
{
    var lat = 47.673988;
    var lon = -122.121513;

    var placemarks = await Geocoding.GetPlacemarksAsync(lat, lon);

    var placemark = placemarks?.FirstOrDefault();
    if (placemark != null)
    {
        var geocodeAddress =
            $"AdminArea:       {placemark.AdminArea}\n" +
            $"CountryCode:     {placemark.CountryCode}\n" +
            $"CountryName:     {placemark.CountryName}\n" +
            $"FeatureName:     {placemark.FeatureName}\n" +
            $"Locality:        {placemark.Locality}\n" +
            $"PostalCode:      {placemark.PostalCode}\n" +
            $"SubAdminArea:    {placemark.SubAdminArea}\n" +
            $"SubLocality:     {placemark.SubLocality}\n" +
            $"SubThoroughfare: {placemark.SubThoroughfare}\n" +
            $"Thoroughfare:    {placemark.Thoroughfare}\n";

        Console.WriteLine(geocodeAddress);
    }
}
catch (FeatureNotSupportedException fnsEx)
{
    // Feature not supported on device
}
catch (Exception ex)
{
    // Handle exception that may have occurred in geocoding
}
```

## <a name="distance-between-two-locations"></a>两个位置之间的距离

[`Location`](xref:Xamarin.Essentials.Location) 和 [`LocationExtensions`](xref:Xamarin.Essentials.LocationExtensions) 类定义了可用于计算两个位置之间的距离的方法。 有关示例，请参阅文章 [**Xamarin.Essentials：地理位置**](geolocation.md#calculate-distance)。

## <a name="api"></a>API

- [Geocoding 源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Geocoding)
- [Geocoding API 文档](xref:Xamarin.Essentials.Geocoding)

## <a name="related-video"></a>相关视频

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Geocoding-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
