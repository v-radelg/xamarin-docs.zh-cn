---
title: Xamarin。窗体映射初始化和配置
description: 本文介绍如何使用 Xamarin Map 类在每个平台上使用本机地图 Api，为用户提供熟悉的地图体验。
ms.prod: xamarin
ms.assetid: 59CD1344-8248-406C-9144-0C8A67141E5B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/07/2019
ms.openlocfilehash: d9b1b93b0667415281bb04e2c4264f03be19bd83
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "72697709"
---
# <a name="xamarinforms-map-initialization-and-configuration"></a>Xamarin。窗体映射初始化和配置

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

_Xamarin 使用每个平台上的本机地图 Api。_

Xamarin 使用每个平台上的本机映射 Api。 这为用户提供了快速、熟悉的地图体验，但这意味着需要执行一些配置步骤来符合每个平台的 API 要求。
配置后，`Map` 控件的工作方式与通用代码中的任何其他 Xamarin 元素相同。

地图控件已在[MapsSample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)示例中使用，如下所示。

 [![MobileCRM 示例中的映射](map-images/maps-zoom-sml.png "Map Control 示例")](map-images/maps-zoom.png#lightbox "Map Control 示例")

可以通过创建[地图自定义呈现](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)器进一步增强地图功能。

<a name="Maps_Initialization" />

## <a name="map-initialization"></a>映射初始化

将映射添加到 Xamarin 应用程序时， **xamarin**是一个单独的 NuGet 包，你应该将其添加到解决方案中的每个项目。
在 Android 上，这也依赖于 GooglePlayServices （另一个 NuGet），在你添加 Xamarin 时，会自动下载。

安装 NuGet 包后，在每个应用程序项目中，`Xamarin.Forms.Forms.Init` 方法调用*之后*需要一些初始化代码。 对于 iOS，请使用以下代码：

```csharp
Xamarin.FormsMaps.Init();
```

在 Android 上，必须传递与 `Forms.Init` 相同的参数：

```csharp
Xamarin.FormsMaps.Init(this, bundle);
```

对于通用 Windows 平台（UWP），请使用以下代码：

```csharp
Xamarin.FormsMaps.Init("INSERT_AUTHENTICATION_TOKEN_HERE");
```

对于每个平台，请在以下文件中添加此调用：

- `FinishedLaunching` 方法**中的 AppDelegate.cs**文件。
- **Android** -MainActivity.cs 文件，在 `OnCreate` 方法。
- @No__t_1 构造函数中的**UWP** -MainPage.xaml.cs 文件。

添加 NuGet 包并在每个应用程序中调用初始化方法后，可以在常见的 .NET Standard 库项目或共享项目代码中使用 `Xamarin.Forms.Maps` Api。

<a name="Platform_Configuration" />

## <a name="platform-configuration"></a>平台配置

在显示地图之前，某些平台上还需要执行其他配置步骤。

### <a name="ios"></a>iOS

若要访问 iOS 上的位置服务，必须在**info.plist**中设置以下项：

- iOS 11
  - [`NSLocationWhenInUseUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26) –当应用程序正在使用时使用位置服务
  - [`NSLocationAlwaysAndWhenInUseUsageDescription`](https://developer.apple.com/documentation/corelocation/choosing_the_authorization_level_for_location_services/requesting_always_authorization?language=objc) -始终使用定位服务
- iOS 10 及更早版本
  - [`NSLocationWhenInUseUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26) –当应用程序正在使用时使用位置服务
  - [`NSLocationAlwaysUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW18) -始终使用定位服务    

若要支持 iOS 11 和更早版本，可以包括所有三个密钥： `NSLocationWhenInUseUsageDescription`、`NSLocationAlwaysAndWhenInUseUsageDescription` 和 `NSLocationAlwaysUsageDescription`。

**Info.plist**中的这些项的 XML 表示形式如下所示。 应更新 `string` 值，以反映应用程序使用位置信息的方式：

```xml
<key>NSLocationAlwaysUsageDescription</key>
<string>Can we use your location at all times?</string>
<key>NSLocationWhenInUseUsageDescription</key>
<string>Can we use your location when your app is being used?</string>
<key>NSLocationAlwaysAndWhenInUseUsageDescription</key>
<string>Can we use your location at all times?</string>
```

编辑**info.plist**文件时，也可以在**源**视图中添加**info.plist**项：

![适用于 iOS 8 的 info.plist](map-images/ios8-map-permissions.png "iOS 8 必需的信息。 info.plist 条目")

### <a name="android"></a>Android

若要在 Android 上使用[Google MAPS API v2](https://developers.google.com/maps/documentation/android/) ，必须生成 API 密钥并将其添加到 android 项目。
按照 Xamarin doc[获取 Google MAPS API v2 密钥](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)中的说明进行操作。
遵循这些说明后，请将 API 密钥粘贴到**Properties/androidmanifest.xml**文件中（查看源并查找/更新以下元素）：

```xml
<application ...>
    <meta-data android:name="com.google.android.maps.v2.API_KEY" android:value="YOUR_API_KEY" />
</application>
```

如果没有有效的 API 密钥，map 控件将在 Android 上显示为灰色框。

> [!NOTE]
> 请注意，为了让你的 APK 访问 Google Maps，你必须为每个用于签署 APK 的密钥存储（调试和发布）包含 SHA-1 指纹和包名称。 例如，如果你使用一台计算机进行调试，将另一台计算机用于生成发布 APK，则应在第一台计算机的调试密钥存储中包含 SHA-1 证书指纹，并在 release 密钥存储中包含 SHA-1 证书指纹第二台计算机。 另外，请记住，如果应用的**包名称**发生更改，则编辑密钥凭据。 请参阅[获取 Google MAPS API v2 密钥](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)。

还需要启用适当的权限，方法是右键单击 Android 项目，然后选择 "**选项" > 生成 > Android 应用程序**，然后执行以下操作：

- `AccessCoarseLocation`
- `AccessFineLocation`
- `AccessLocationExtraCommands`
- `AccessMockLocation`
- `AccessNetworkState`
- `AccessWifiState`
- `Internet`

其中一些部分显示在下面的屏幕截图中：

![适用于 Android 的必需权限](map-images/android-map-permissions.png "适用于 Android 的必需权限")

最后两个是必需的，因为应用程序需要网络连接才能下载地图数据。 阅读有关 Android[权限](https://developer.android.com/reference/android/Manifest.permission.html)以了解详细信息。

此外，Android 9 从 bootclasspath 中删除了 Apache HTTP 客户端库，因此它不适用于面向 API 28 或更高版本的应用程序。 必须将以下行添加到**androidmanifest.xml**文件的 "`application`" 节点中，才能继续在面向 API 28 或更高版本的应用程序中使用 Apache HTTP 客户端：

```xml
<application ...>
    ...
    <uses-library android:name="org.apache.http.legacy" android:required="false" />    
</application>
```

### <a name="universal-windows-platform"></a>通用 Windows 平台

若要在通用 Windows 平台上使用映射，必须生成授权令牌。 有关详细信息，请参阅 MSDN 上[的请求映射身份验证密钥](https://msdn.microsoft.com/library/windows/apps/mt219694.aspx)。

然后，应在 `FormsMaps.Init("AUTHORIZATION_TOKEN")` 方法调用中指定身份验证令牌，以通过 Bing 地图对应用进行身份验证。

<a name="Using_Maps" />

## <a name="map-configuration"></a>映射配置

有关如何在代码中使用地图控件的示例，请参阅 MobileCRM 示例中的[MapPage.cs](https://github.com/xamarin/xamarin-forms-samples/blob/master/MobileCRM/MobileCRM.Shared/Pages/MapPage.cs) 。 简单的 `MapPage` 类可能类似于下面的示例-请注意，创建了一个新的 `MapSpan` 来定位地图的视图：

```csharp
public class MapPage : ContentPage {
    public MapPage() {
        var map = new Map(
            MapSpan.FromCenterAndRadius(
                    new Position(37,-122), Distance.FromMiles(0.3))) {
                IsShowingUser = true,
                HeightRequest = 100,
                WidthRequest = 960,
                VerticalOptions = LayoutOptions.FillAndExpand
            };
        var stack = new StackLayout { Spacing = 0 };
        stack.Children.Add(map);
        Content = stack;
    }
}
```

### <a name="map-type"></a>映射类型

还可以通过设置 "`MapType`" 属性来更改地图内容，以显示常规街道地图（默认）、卫星图像或二者的组合。

```csharp
map.MapType = MapType.Street;
```

有效 `MapType` 值为：

- `Hybrid`
- `Satellite`
- `Street` （默认值）

### <a name="map-region-and-mapspan"></a>地图区域和 MapSpan

如上面的代码片段所示，将 `MapSpan` 实例提供给映射构造函数会在加载时设置地图的初始视图（中心点和缩放级别）。 可以通过两种方式创建新的 `MapSpan` 实例：

- **MapSpan. FromCenterAndRadius （）** -用于从 `Position` 创建跨度并指定 `Distance` 的静态方法。
- **New MapSpan （）** -使用 `Position` 的构造函数以及要显示的纬度和经度的度数。

然后，可以使用 map 类上的 `MoveToRegion` 方法更改地图的位置或缩放级别。 若要在不更改位置的情况下更改地图的缩放级别，请使用地图控件的 `VisibleRegion.Center` 属性中的当前位置创建新 `MapSpan`。 可以使用 `Slider` 来控制地图缩放，如图所示：在地图控件中直接缩放当前无法更新滑块的值）：

```csharp
Slider slider = new Slider (1, 18, 1);
slider.ValueChanged += (sender, e) =>
{
    var zoomLevel = e.NewValue; // between 1 and 18
    var latlongdegrees = 360 / (Math.Pow(2, zoomLevel));
    map.MoveToRegion(new MapSpan (map.VisibleRegion.Center, latlongdegrees, latlongdegrees));
};
```

[![带缩放的映射](map-images/maps-zoom-sml.png "Map Control 缩放")](map-images/maps-zoom.png#lightbox "Map Control 缩放")

此外， [`Map`](xref:Xamarin.Forms.Maps.Map)类具有类型 `bool` 的 `MoveToLastRegionOnLayoutChange` 属性，该属性由可绑定的属性支持。 默认情况下，此属性是 `true`，这表示当发生布局更改时，所显示的地图区域将从其当前区域移到先前设置的区域，如设备旋转。 如果将此属性设置为 "`false`"，则在发生布局更改时，所显示的地图区域将保持居中。 下面的示例演示如何设置此属性：

```csharp
map.MoveToLastRegionOnLayoutChange = false;
```

### <a name="map-clicks"></a>地图单击

`Map` 定义在点击地图时触发的 `MapClicked` 事件。 @No__t_1 事件附带的 `MapClickedEventArgs` 对象具有名为 `Position` 的单个属性，类型 `Position`。 触发事件时，`Position` 属性的值将设置为点击的地图位置。

下面的代码示例演示了 `MapClicked` 事件的事件处理程序：

```csharp
map.MapClicked += OnMapClicked;

void OnMapClicked(object sender, MapClickedEventArgs e)
{
    System.Diagnostics.Debug.WriteLine($"MapClick: {e.Position.Latitude}, {e.Position.Longitude}");
}
```

在此示例中，`OnMapClicked` 事件处理程序输出表示螺纹地图位置的纬度和经度。

<a name="Using_Xaml" />

### <a name="create-a-map-in-xaml"></a>在 XAML 中创建映射

还可以在 XAML 中创建映射，如以下示例中所示：

```xaml
<?xml version="1.0" encoding="UTF-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps"
             x:Class="MapDemo.MapPage">
    <StackLayout VerticalOptions="StartAndExpand" Padding="30">
        <maps:Map x:Name="MyMap"
                  Clicked="OnMapClicked"
                  WidthRequest="320"
                  HeightRequest="200"                  
                  IsShowingUser="true"
                  MapType="Hybrid" />
    </StackLayout>
</ContentPage>
```

> [!NOTE]
> 引用 Xamarin. Maps 控件需要额外的 `xmlns` 命名空间定义。 在前面的示例中，通过 `maps` 关键字引用 `Xamarin.Forms.Maps` 命名空间。

使用 `Map` 的命名引用可以在代码中设置 `MapRegion`：

```csharp
MyMap.MoveToRegion(
    MapSpan.FromCenterAndRadius(
        new Position(37,-122), Distance.FromMiles(1)));
```

## <a name="related-links"></a>相关链接

- [地图示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
- [Xamarin. Maps](~/xamarin-forms/user-interface/map/pins.md)。
- [地图 API](xref:Xamarin.Forms.Maps)
- [映射自定义呈现器](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)
