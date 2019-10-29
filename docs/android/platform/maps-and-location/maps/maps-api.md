---
title: 在应用程序中使用 Google Maps API
description: 如何在 Xamarin Android 应用程序中实现 Google Maps API v2 功能。
ms.prod: xamarin
ms.assetid: C0589878-2D04-180E-A5B9-BB41D5AF6E02
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 09/07/2018
ms.openlocfilehash: adcfb1457742d343f87a602885566107cf327e2d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027152"
---
# <a name="using-the-google-maps-api-in-your-application"></a>在应用程序中使用 Google Maps API

使用 Maps 应用程序非常有用，但有时您需要直接在应用程序中包含映射。 除了内置的 maps 应用程序，Google 还提供[适用于 Android 的本机映射 API](https://developers.google.com/maps/documentation/android-sdk/intro)。
Map API 适用于希望保持更好的映射体验控制的情况。 可以通过 Maps API 执行的操作包括：

- 以编程方式更改地图的视点。
- 添加和自定义标记。
- 使用叠加批注地图。

与现已弃用的 Google Maps Android API v1 不同，Google Maps Android API v2 是[Google Play Services](https://developers.google.com/android/guides/overview)的一部分。
Xamarin Android 应用必须满足一些必需的先决条件，然后才能使用 Google Maps Android API。

## <a name="google-maps-api-prerequisites"></a>Google Maps API 先决条件

需要执行几个步骤才能使用 Maps API，包括：

- [获取 Maps API 密钥](#obtain-maps-key)
- [安装 Google Play Services SDK](#install-gps-sdk)
- [从 NuGet 安装 GooglePlayServices 包](#install-gpsmaps-nuget)
- [指定所需权限](#declare-permissions)
- [（可选）使用 Google Api 创建模拟器](#create-emulator-with-google-api)

### <a name="a-nameobtain-maps-key-obtain-a-google-maps-api-key"></a><a name="obtain-maps-key" />获取 Google Maps API 密钥

第一步是获取 Google Maps API 密钥（请注意，不能重复使用旧版 Google Maps v1 API 中的 API 密钥）。 有关如何获取和使用适用于 Xamarin 的 API 密钥的信息，请参阅[获取 Google MAPS API 密钥](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)。

### <a name="a-nameinstall-gps-sdk--install-the-google-play-services-sdk"></a><a name="install-gps-sdk" /> 安装 Google Play Services SDK

Google Play Services 是 Google 中的一种技术，它允许 Android 应用程序利用各种 Google 功能，如 Google +、In-App Billing 和 Maps。 这些功能可在 Android 设备上作为后台服务访问，这些服务包含在[GOOGLE PLAY SERVICES APK](https://play.google.com/store/apps/details?id=com.google.android.gms&hl=en)中。

Android 应用程序通过 Google Play Services 客户端库与 Google Play Services 进行交互。 此库包含各个服务（如 Maps）的接口和类。 下图显示了 Android 应用程序和 Google Play Services 之间的关系：

![说明更新 Google Play Services APK Google Play 商店的示意图](maps-api-images/play-services-diagram.png)

Android Maps API 作为 Google Play Services 的一部分提供。
在 Xamarin Android 应用程序可以使用 Maps API 之前，必须使用[Android SDK 管理器](~/android/get-started/installation/android-sdk.md)安装 Google Play Services SDK。 以下屏幕截图显示了在 Android SDK Manager 中可找到 Google Play 服务客户端的位置：

![Google Play Services 显示在 "Android SDK 管理器" 中的 "其他" 下](maps-api-images/image01.png)

> [!NOTE]
> Google Play services APK 是可能不会出现在所有设备上的许可产品。 如果未安装，则 Google Maps 在设备上不起作用。

### <a name="a-nameinstall-gpsmaps-nuget--install-the-xamaringoogleplayservicesmaps-package-from-nuget"></a><a name="install-gpsmaps-nuget" /> 从 NuGet 安装 GooglePlayServices 包

[GooglePlayServices 包](https://www.nuget.org/packages/Xamarin.GooglePlayServices.Maps)包含 GOOGLE PLAY SERVICES 映射 API 的 Xamarin 的绑定。
若要添加 Google Play Services 映射包，请右键单击解决方案资源管理器中项目的 "**引用**" 文件夹，然后单击 "**管理 NuGet 包 ...** "：

![显示 "引用" 下的 "管理 NuGet 包" 上下文菜单项解决方案资源管理器](maps-api-images/image02.png)

这将打开**NuGet 包管理器**。 单击 "**浏览**"，并在搜索字段中输入**Xamarin Google Play Services 映射**。 选择 " **GooglePlayServices** "，然后单击 "**安装**"。 （如果之前已安装此程序包，请单击 "**更新**"。）：

[选择 GooglePlayServices 包的![NuGet 包管理器](maps-api-images/image03-sml.png)](maps-api-images/image03.png#lightbox)

请注意，还会安装以下依赖项包：

- **GooglePlayServices**
- **GooglePlayServices**
- **GooglePlayServices**

### <a name="a-namedeclare-permissions--specify-the-required-permissions"></a><a name="declare-permissions" /> 指定所需权限

应用必须确定硬件和权限要求才能使用 Google Maps API。  Google Play Services SDK 会自动授予某些权限，开发人员无需将其显式添加到**AndroidManfest**：

- **若要访问网络状态**&ndash; map API 必须能够检查是否可以下载地图图块。

- 若要下载地图磁贴并与 Google Play 服务器通信以获取 API 访问权限 **，internet 访问 &ndash; 需要**internet 访问。

对于 Google Maps Android API，必须在**androidmanifest.xml**中指定以下权限和功能：

- **OPENGL es v2** &ndash; 应用程序必须声明 OpenGL es v2 的要求。

- **Google MAPS Api 密钥**&ndash; api 密钥用于确认应用程序已注册并授权使用 Google Play Services。 有关此密钥的详细信息，请参阅[获取 Google MAPS API 密钥](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)。

- 请求面向 Android 9.0 （API 级别28）或更高版本的**旧版 APACHE http 客户端**&ndash; 应用必须指定旧 apache http 客户端是要使用的可选库。

- **若要访问基于 Google web 的服务**&ndash; 应用程序需要访问 google 的 Web 服务的权限才能访问 ANDROID Maps API。

- &ndash; 应用程序的**Google Play Services 通知权限**必须被授予从 Google Play Services 接收远程通知的权限。

- **访问位置提供程序**&ndash; 这些是可选权限。
   它们将允许 `GoogleMap` 类在地图上显示设备的位置。

此外，Android 9 从 bootclasspath 中删除了 Apache HTTP 客户端库，因此它不适用于面向 API 28 或更高版本的应用程序。 必须将以下行添加到**androidmanifest.xml**文件的 "`application`" 节点中，才能继续在面向 API 28 或更高版本的应用程序中使用 Apache HTTP 客户端：

```xml
<application ...>
   ...
   <uses-library android:name="org.apache.http.legacy" android:required="false" />    
</application>
```

> [!NOTE]
> 非常旧的 Google Play SDK 版本需要一个应用来请求 `WRITE_EXTERNAL_STORAGE` 权限。 Google Play Services 的最新 Xamarin 绑定不再需要此要求。

以下代码片段是必须添加到**androidmanifest.xml**的设置的示例：

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android" android:versionName="4.5" package="com.xamarin.docs.android.mapsandlocationdemo2" android:versionCode="6">
    <uses-sdk android:minSdkVersion="23" android:targetSdkVersion="28" />

    <!-- Google Maps for Android v2 requires OpenGL ES v2 -->
    <uses-feature android:glEsVersion="0x00020000" android:required="true" />

    <!-- Necessary for apps that target Android 9.0 or higher -->
    <uses-library android:name="org.apache.http.legacy" android:required="false" />

    <!-- Permission to receive remote notifications from Google Play Services -->
    <!-- Notice here that we have the package name of our application as a prefix on the permissions. -->
    <uses-permission android:name="<PACKAGE NAME>.permission.MAPS_RECEIVE" />
    <permission android:name="<PACKAGE NAME>.permission.MAPS_RECEIVE" android:protectionLevel="signature" />

    <!-- These are optional, but recommended. They will allow Maps to use the My Location provider. -->
    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />

    <application android:label="@string/app_name">
        <!-- Put your Google Maps V2 API Key here. -->
        <meta-data android:name="com.google.android.maps.v2.API_KEY" android:value="YOUR_API_KEY" />
        <meta-data android:name="com.google.android.gms.version" android:value="@integer/google_play_services_version" />
        <!-- Necessary for apps that target Android 9.0 or higher -->
        <uses-library android:name="org.apache.http.legacy" android:required="false" />
    </application>
</manifest>
```

除了请求权限**androidmanifest.xml**，应用还必须对 `ACCESS_COARSE_LOCATION` 和 `ACCESS_FINE_LOCATION` 权限执行运行时权限检查。 有关执行运行时权限检查的详细信息，请参阅[Xamarin Android 权限](~/android/app-fundamentals/permissions.md)指南。

### <a name="a-namecreate-emulator-with-google-api-create-an-emulator-with-google-apis"></a><a name="create-emulator-with-google-api" />使用 Google Api 创建模拟器

如果未安装带有 Google Play services 的物理 Android 设备，则可以创建用于开发的模拟器映像。 有关详细信息，请参阅[设备管理器](~/android/get-started/installation/android-emulator/device-manager.md)。

## <a name="the-googlemap-class"></a>GoogleMap 类

满足先决条件后，就可以开始开发应用程序并使用 Android Maps API 了。 [GoogleMap](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap)类是一个主要 API，Xamarin android 应用程序将使用它来显示适用于 Android 的 Google Maps 并与其交互。 此类具有以下职责：

- 与 Google Play 服务交互，以通过 Google web 服务向应用程序授权。

- 下载、缓存和显示地图图块。

- 显示用户的 UI 控件，如平移和缩放。

- 在地图上绘制标记和几何形状。

可以通过以下两种方式之一将 `GoogleMap` 添加到活动中：

- **MapFragment** - [MapFragment](https://developers.google.com/android/reference/com/google/android/gms/maps/MapFragment)是用作 `GoogleMap` 对象的主机的专用片段。 `MapFragment` 需要 Android API 级别12或更高版本。
   较早版本的 Android 可以使用[SupportMapFragment](https://developers.google.com/android/reference/com/google/android/gms/maps/SupportMapFragment)。  本指南将重点介绍如何使用 `MapFragment` 类。

- **MapView** - [MapView](https://developers.google.com/android/reference/com/google/android/gms/maps/MapView)是专用的视图子类，可充当 `GoogleMap` 对象的主机。 此类的用户必须将所有活动生命周期方法转发到 `MapView` 类。

其中每个容器都公开一个 `Map` 属性，该属性返回 `GoogleMap`的实例。 应为[MapFragment](https://developers.google.com/android/reference/com/google/android/gms/maps/MapFragment)类提供首选项，因为它是一个更简单的 API，可减少开发人员必须手动实现的样本代码量。

### <a name="adding-a-mapfragment-to-an-activity"></a>向活动添加 MapFragment

下面的屏幕截图是简单 `MapFragment`的示例：

[显示 Google 地图片段的设备![屏幕截图](maps-api-images/image05-sml.png)](maps-api-images/image05.png#lightbox)

与其他片段类类似，可以通过两种方法将 `MapFragment` 添加到活动中：

- **以声明方式**-可以通过活动的 XML 布局文件添加 `MapFragment`。 以下 XML 代码片段演示了如何使用 `fragment` 元素的示例：

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <fragment xmlns:android="http://schemas.android.com/apk/res/android"
              android:id="@+id/map"
              android:layout_width="match_parent"
              android:layout_height="match_parent"
              class="com.google.android.gms.maps.MapFragment" />
    ```

- **以编程方式**-可以使用[`MapFragment.NewInstance`](https://developers.google.com/android/reference/com/google/android/gms/maps/MapFragment.html#newInstance())方法以编程方式实例化 `MapFragment`，然后将其添加到活动。 此代码片段演示实例化 `MapFragment` 对象并添加到活动的最简单方法：

    ```csharp
        var mapFrag = MapFragment.NewInstance();
        activity.FragmentManager.BeginTransaction()
                                .Add(Resource.Id.map_container, mapFrag, "map_fragment")
                                .Commit();

    ```

    可以通过将[`GoogleMapOptions`](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMapOptions)对象传递到 `NewInstance`来配置 `MapFragment` 对象。 本指南后面的 " [GoogleMap 属性](#googlemap_object)" 部分对此进行了讨论。

`MapFragment.GetMapAsync` 方法用于初始化由片段承载的[`GoogleMap`](#googlemap_object) ，并获取对 `MapFragment`承载的地图对象的引用。 此方法采用实现 `IOnMapReadyCallback` 接口的对象。

此接口具有一个方法，`IMapReadyCallback.OnMapReady(MapFragment map)` 当应用可能与 `GoogleMap` 对象进行交互时，将调用此方法。 下面的代码片段演示了 Android 活动如何初始化 `MapFragment` 和实现 `IOnMapReadyCallback` 接口：

```csharp
public class MapWithMarkersActivity : AppCompatActivity, IOnMapReadyCallback
{
    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);
        SetContentView(Resource.Layout.MapLayout);

        var mapFragment = (MapFragment) FragmentManager.FindFragmentById(Resource.Id.map);
        mapFragment.GetMapAsync(this);

        // remainder of code omitted
    }

    public void OnMapReady(GoogleMap map)
    {
        // Do something with the map, i.e. add markers, move to a specific location, etc.
    }
}
```

### <a name="map-types"></a>映射类型

Google Maps API 提供五种不同类型的地图：

- **Normal** -这是默认的映射类型。 它显示了道路和重要的自然功能，以及一些相关的人工点（如建筑物和桥）。

- **附属**项-此地图显示卫星照相。

- **混合**-此地图显示卫星照片和道路地图。

- **地形**-这主要显示了 topographical 的功能和一些道路。

- **无**-此映射不会加载任何磁贴，而是呈现为空网格。

下图显示了三种不同类型的地图，从左到右（普通、混合、地形）：

[![三个地图示例屏幕截图： Normal、混合和地形](maps-api-images/map-types-sml.png)](maps-api-images/map-types.png#lightbox)

`GoogleMap.MapType` 属性用于设置或更改显示哪种类型的地图。 下面的代码段演示如何显示附属图。

```csharp
public void OnMapReady(GoogleMap map)
{
    map.MapType = GoogleMap.MapTypeHybrid;
}
```

### <a name="a-namegooglemap_object-googlemap-properties"></a><a name="googlemap_object" />GoogleMap 属性

`GoogleMap` 定义了多个属性，这些属性可控制地图的功能和外观。 配置 `GoogleMap` 初始状态的一种方法是在创建 `MapFragment`时传递[GoogleMapOptions](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMapOptions)对象。 以下代码片段是在创建 `MapFragment`时使用 `GoogleMapOptions` 对象的一个示例：

```csharp
GoogleMapOptions mapOptions = new GoogleMapOptions()
    .InvokeMapType(GoogleMap.MapTypeSatellite)
    .InvokeZoomControlsEnabled(false)
    .InvokeCompassEnabled(true);

FragmentTransaction fragTx = FragmentManager.BeginTransaction();
mapFragment = MapFragment.NewInstance(mapOptions);
fragTx.Add(Resource.Id.map, mapFragment, "map");
fragTx.Commit();
```

配置 `GoogleMap` 的另一种方法是操作地图对象的[UiSettings](https://developers.google.com/android/reference/com/google/android/gms/maps/UiSettings)的属性。 下面的代码示例演示如何配置 `GoogleMap` 以显示缩放控件和罗盘：

```csharp
public void OnMapReady(GoogleMap map)
{
    map.UiSettings.ZoomControlsEnabled = true;
    map.UiSettings.CompassEnabled = true;
}
```

## <a name="interacting-with-the-googlemap"></a>与 GoogleMap 交互

Android Maps API 提供 Api，使活动可以更改视点、添加标记、放置自定义叠加或绘制几何形状。 本部分将讨论如何在 Xamarin 中完成一些任务。

### <a name="changing-the-viewpoint"></a>更改视点

基于 Mercator 投影，地图在屏幕上建模为平面平面。 "地图" 视图是指在此平面上向下看的*相机*的图面。 可以通过更改位置、缩放、倾斜和轴承来控制照相机的位置。 [CameraUpdate](https://developers.google.com/android/reference/com/google/android/gms/maps/CameraUpdate)类用于移动照相机位置。 `CameraUpdate` 对象不是直接实例化的，而 Maps API 则提供[CameraUpdateFactory](https://developers.google.com/android/reference/com/google/android/gms/maps/CameraUpdateFactory)类。

创建 `CameraUpdate` 对象后，该对象将作为参数传递到[MoveCamera](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap#moveCamera(com.google.android.gms.maps.CameraUpdate))或[GoogleMap](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap#animateCamera(com.google.android.gms.maps.CameraUpdate))方法。 当 `AnimateCamera` 方法提供平滑的动画转换时，`MoveCamera` 方法会立即更新该映射。

此代码段是一个简单的示例，演示如何使用 `CameraUpdateFactory` 创建一个 `CameraUpdate`，该会将地图的缩放级别增加一个缩放级别：

```csharp
MapFragment mapFrag = (MapFragment) FragmentManager.FindFragmentById(Resource.Id.my_mapfragment_container);
mapFrag.GetMapAsync(this);
...

public void OnMapReady(GoogleMap map)
{   
    map.MoveCamera(CameraUpdateFactory.ZoomIn());
}
```

Maps API 提供了一个[CameraPosition](https://developer.android.com/reference/com/google/android/gms/maps/model/CameraPosition.html) ，它将为相机位置聚合所有可能的值。 此类的实例可以提供给[CameraUpdateFactory. NewCameraPosition](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/CameraUpdateFactory#newCameraPosition%28com.google.android.gms.maps.model.CameraPosition%29)方法，该方法将返回 `CameraUpdate` 的对象。 Maps API 还包括[CameraPosition](https://developer.android.com/reference/com/google/android/gms/maps/model/CameraPosition.Builder.html)类，该类提供用于创建 `CameraPosition` 对象的 Fluent API。
下面的代码片段演示了如何从 `CameraPosition` 创建 `CameraUpdate`，并使用该示例更改 `GoogleMap`上的照相机位置：

```csharp
public void OnMapReady(GoogleMap map)
{
    LatLng location = new LatLng(50.897778, 3.013333);

    CameraPosition.Builder builder = CameraPosition.InvokeBuilder();
    builder.Target(location);
    builder.Zoom(18);
    builder.Bearing(155);
    builder.Tilt(65);

    CameraPosition cameraPosition = builder.Build();

    CameraUpdate cameraUpdate = CameraUpdateFactory.NewCameraPosition(cameraPosition);

    map.MoveCamera(cameraUpdate);
}
```

在前面的代码片段中，地图上的特定位置由[LatLng](https://developers.google.com/android/reference/com/google/android/gms/maps/model/LatLng)类表示。 "缩放级别" 设置为18，这是 Google Maps 使用的缩放的任意度量。 轴承是从北顺时针测量的方向。 倾斜属性控制查看角度，并指定从垂直方向25度。 以下屏幕截图显示了执行上述代码后的 `GoogleMap`：

[![使用倾斜角度显示指定位置的 Google Map 示例](maps-api-images/image06-sml.png)](maps-api-images/image06.png#lightbox)

### <a name="drawing-on-the-map"></a>在地图上绘制

Android Maps API 提供 API，用于在地图上绘制以下各项：

- **标记**-这些是用于标识地图上单个位置的特殊图标。

- **覆盖**-这是一个图像，可用于标识地图上位置或区域的集合。

- **线条、多边形和圆圈**-这些是允许活动将形状添加到地图中的 api。

#### <a name="markers"></a>标记

Maps API 提供了一个[标记](https://developers.google.com/android/reference/com/google/android/gms/maps/model/Marker)类，用于封装有关映射上单个位置的所有数据。 默认情况下，标记类使用 Google Maps 提供的标准图标。 可以自定义标记的外观并响应用户单击。

##### <a name="adding-a-marker"></a>添加标记

若要将标记添加到地图，需要创建一个新的[MarkerOptions](https://developers.google.com/android/reference/com/google/android/gms/maps/model/MarkerOptions)对象，然后对 `GoogleMap` 实例调用[AddMarker](https://developer.android.com/reference/com/google/android/gms/maps/GoogleMap.html#addMarker%28com.google.android.gms.maps.model.MarkerOptions%29)方法。 此方法将返回[标记](https://developers.google.com/android/reference/com/google/android/gms/maps/model/Marker)对象。

```csharp
public void OnMapReady(GoogleMap map)
{
    MarkerOptions markerOpt1 = new MarkerOptions();
    markerOpt1.SetPosition(new LatLng(50.379444, 2.773611));
    markerOpt1.SetTitle("Vimy Ridge");

    map.AddMarker(markerOpt1);
}
```

用户点击标记时，标记的标题将显示在 "*信息" 窗口*中。 以下屏幕截图显示了此标记的外观：

[![带有标记的 Google Map 和 Vimy 凸的信息窗口](maps-api-images/image07-sml.png)](maps-api-images/image07.png#lightbox)

##### <a name="customizing-a-marker"></a>自定义标记

将标记添加到地图时，可以通过调用 `MarkerOptions.InvokeIcon` 方法来自定义标记使用的图标。
此方法采用包含呈现图标所需的数据的[BitmapDescriptor](https://developers.google.com/android/reference/com/google/android/gms/maps/model/BitmapDescriptor)对象。 [BitmapDescriptorFactory](https://developers.google.com/android/reference/com/google/android/gms/maps/model/BitmapDescriptorFactory)类提供了一些帮助器方法来简化 `BitmapDescriptor`的创建。 下面的列表介绍了其中一些方法：

- `DefaultMarker(float colour)` &ndash; 使用默认的 Google Maps 标记，但更改颜色。

- `FromAsset(string assetName)` &ndash; 使用 "资产" 文件夹中指定文件的自定义图标。

- `FromBitmap(Bitmap image)` &ndash; 使用指定的位图作为图标。

- `FromFile(string fileName)` &ndash; 从指定路径处的文件创建自定义图标。

- `FromResource(int resourceId)` &ndash; 从指定的资源创建自定义图标。

下面的代码片段演示了创建青色彩色默认标记的示例：

```csharp
public void OnMapReady(GoogleMap map)
{
    MarkerOptions markerOpt1 = new MarkerOptions();
    markerOpt1.SetPosition(new LatLng(50.379444, 2.773611));
    markerOpt1.SetTitle("Vimy Ridge");

    var bmDescriptor = BitmapDescriptorFactory.DefaultMarker (BitmapDescriptorFactory.HueCyan);
    markerOpt1.InvokeIcon(bmDescriptor);

    map.AddMarker(markerOpt1);
}
```

#### <a name="info-windows"></a>信息窗口

*信息窗口*是在用户点击特定标记时向用户显示信息的特殊窗口。 默认情况下，"信息" 窗口将显示标记的标题的内容。 如果尚未分配标题，则不会显示 "信息" 窗口。 一次只能显示一个 "信息" 窗口。

可以通过实现[GoogleMap. IInfoWindowAdapter](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.InfoWindowAdapter)接口来自定义 "信息" 窗口。 此接口上有两个重要的方法：

- `public View GetInfoWindow(Marker marker)` &ndash; 调用此方法以获取标记的自定义信息窗口。 如果它返回 `null`，则将使用默认的窗口呈现。 如果此方法返回一个视图，则该视图将放在信息窗口框架中。

- `public View GetInfoContents(Marker marker)` &ndash; 仅当 GetInfoWindow 返回 `null` 时才会调用此方法。 如果要使用信息窗口内容的默认呈现，则此方法可能会返回 `null` 值。 否则，此方法应返回具有信息窗口内容的视图。

信息窗口不是实时视图，而是 Android 将视图转换为静态位图并在图像上显示。 这意味着信息窗口无法响应任何触控事件或手势，也不会自动更新自身。 若要更新信息窗口，必须调用[GoogleMap. ShowInfoWindow](https://developers.google.com/android/reference/com/google/android/gms/maps/model/Marker.html#showInfoWindow())方法。

下图显示某些自定义信息窗口的一些示例。 左侧的图像自定义了其内容，而右侧的图像的窗口和内容具有圆角自定义：

![墨尔本的示例标记窗口，包括图标和填充。 右窗口具有圆角。](maps-api-images/marker-infowindows.png)

#### <a name="groundoverlays"></a>GroundOverlays

与标识地图上特定位置的标记不同， [GroundOverlay](https://developers.google.com/android/reference/com/google/android/gms/maps/model/GroundOverlay)是用于标识位置集合或地图上某个区域的图像。

##### <a name="adding-a-groundoverlay"></a>添加 GroundOverlay

将地面叠加添加到地图类似于向地图添加标记。 首先，创建一个[GroundOverlayOptions](https://developers.google.com/android/reference/com/google/android/gms/maps/model/GroundOverlayOptions)对象。 然后，将该对象作为参数传递给[`GoogleMap.AddGroundOverlay`](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.html#addGroundOverlay(com.google.android.gms.maps.model.GroundOverlayOptions))方法，该方法将返回一个 `GroundOverlay` 对象。 此代码片段是将地面叠加添加到地图的示例：

```csharp
BitmapDescriptor image = BitmapDescriptorFactory.FromResource(Resource.Drawable.polarbear);
GroundOverlayOptions groundOverlayOptions = new GroundOverlayOptions()
    .Position(position, 150, 200)
    .InvokeImage(image);
GroundOverlay myOverlay = googleMap.AddGroundOverlay(groundOverlayOptions);
```

以下屏幕截图显示了地图上的这一覆盖：

[使用极坐标的重叠图像![示例映射](maps-api-images/image09-sml.png)](maps-api-images/image09.png#lightbox)

#### <a name="lines-circles-and-polygons"></a>线条、圆圈和多边形

有三种简单的几何图类型可添加到地图中：

- **折线**-这是一系列连接的直线段。 它可以标记地图上的路径或创建几何形状。

- **圆圈**-这会在地图上绘制一个圆圈。

- **多边形**-这是用于标记地图上的区域的闭合形状。

##### <a name="polylines"></a>折线

[折线](https://developers.google.com/android/reference/com/google/android/gms/maps/model/Polyline)是连续 `LatLng` 对象的列表，这些对象指定每个线段的顶点。 通过首先创建一个 `PolylineOptions` 对象并向其中添加点，创建折线。 然后通过调用 `AddPolyline` 方法，将 `PolylineOption` 对象传递到 `GoogleMap` 对象。

```csharp
PolylineOption rectOptions = new PolylineOption();
rectOptions.Add(new LatLng(37.35, -122.0));
rectOptions.Add(new LatLng(37.45, -122.0));
rectOptions.Add(new LatLng(37.45, -122.2));
rectOptions.Add(new LatLng(37.35, -122.2));
rectOptions.Add(new LatLng(37.35, -122.0)); // close the polyline - this makes a rectangle.

googleMap.AddPolyline(rectOptions);
```

##### <a name="circles"></a>小号

通过首先实例化圆圈来创建一个[CircleOption](https://developers.google.com/android/reference/com/google/android/gms/maps/model/CircleOptions)对象，该对象将在 metres 中指定圆的中心和半径。 通过调用[GoogleMap](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.html#addCircle(com.google.android.gms.maps.model.CircleOptions))在地图上绘制圆圈。
下面的代码段演示如何绘制圆形：

```csharp
CircleOptions circleOptions = new CircleOptions ();
circleOptions.InvokeCenter (new LatLng(37.4, -122.1));
circleOptions.InvokeRadius (1000);

googleMap.AddCircle (circleOptions);
```

##### <a name="polygons"></a>多边形

`Polygon`s 类似于 `Polyline`，但未打开。 `Polygon`s 为关闭循环，并填充其内部。
`Polygon`的创建方式与 `Polyline`完全相同，只是调用了[GoogleMap. AddPolygon](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.html#addPolygon(com.google.android.gms.maps.model.PolygonOptions))方法。

与 `Polyline`不同，`Polygon` 自结束。 通过绘制一条线来连接第一个点和最后一个点，`AddPolygon` 方法会关闭多边形。 下面的代码段将在与 `Polyline` 示例中的上一个代码段相同的区域上创建一个实心矩形。

```csharp
PolygonOptions rectOptions = new PolygonOptions();
rectOptions.Add(new LatLng(37.35, -122.0));
rectOptions.Add(new LatLng(37.45, -122.0));
rectOptions.Add(new LatLng(37.45, -122.2));
rectOptions.Add(new LatLng(37.35, -122.2));
// notice we don't need to close off the polygon

googleMap.AddPolygon(rectOptions);
```

## <a name="responding-to-user-events"></a>响应用户事件

有三种类型的用户可以使用地图进行交互：

- **标记单击**-用户单击标记。

- **标记拖动**-用户在 mparger 上的单击时间较长

- **信息窗口单击**-用户已单击信息窗口。

下面将更详细地讨论每个事件。

### <a name="marker-click-events"></a>标记单击事件

当用户点击标记时，将引发 `MarkerClicked` 事件。 此事件接受 `GoogleMap.MarkerClickEventArgs` 的对象作为参数。 此类包含两个属性：

- `GoogleMap.MarkerClickEventArgs.Handled` &ndash; 应将此属性设置为 `true`，以指示事件处理程序已使用该事件。 如果将此设置为 `false`，则除了事件处理程序的自定义行为外，还会发生默认行为。

- `Marker` &ndash; 此属性是对引发 `MarkerClick` 事件的标记的引用。

此代码片段演示了一个 `MarkerClick` 的示例，该示例将相机位置更改为地图上的新位置：

```csharp
void MapOnMarkerClick(object sender, GoogleMap.MarkerClickEventArgs markerClickEventArgs)
{
    markerClickEventArgs.Handled = true;

    var marker = markerClickEventArgs.Marker;
    if (marker.Id.Equals(gotMauiMarkerId))
    {
        LatLng InMaui = new LatLng(20.72110, -156.44776);

        // Move the camera to look at Maui.
        PositionPolarBearGroundOverlay(InMaui);
        googleMap.AnimateCamera(CameraUpdateFactory.NewLatLngZoom(InMaui, 13));
        gotMauiMarkerId = null;
        polarBearMarker.Remove();
        polarBearMarker = null;
    }
    else
    {
        Toast.MakeText(this, $"You clicked on Marker ID {marker.Id}", ToastLength.Short).Show();
    }
}
```

### <a name="marker-drag-events"></a>标记拖动事件

当用户希望拖动标记时，将引发此事件。 默认情况下，不能拖动标记。 可以通过将 `Marker.Draggable` 属性设置为 `true` 或通过将 `true` 作为参数来调用 `MarkerOptions.Draggable` 方法，将标记设置为可拖动标记。

若要拖动标记，用户必须首先长时间单击该标记，然后其手指必须保留在地图上。 当用户的手指在屏幕上拖动时，标记将移动。 当用户的手指从屏幕上移开时，标记会保持不变。

以下列表描述了可拖动标记将引发的各种事件：

- `GoogleMap.MarkerDragStart(object sender, GoogleMap.MarkerDragStartEventArgs e)` &ndash; 用户第一次拖动标记时引发此事件。

- `GoogleMap.MarkerDrag(object sender, GoogleMap.MarkerDragEventArgs e)` &ndash; 在拖动标记时引发此事件。

- `GoogleMap.MarkerDragEnd(object sender, GoogleMap.MarkerDragEndEventArgs e)` &ndash; 在用户完成标记拖动后引发此事件。

每个 `EventArgs` 都包含一个名为 `P0` 的属性，该属性是对要拖动的 `Marker` 对象的引用。

### <a name="info-window-click-events"></a>信息窗口单击事件

一次只能显示一个 "信息" 窗口。 用户单击地图中的信息窗口时，地图对象将引发 `InfoWindowClick` 事件。 下面的代码段演示如何将处理程序绑定到事件：

```csharp
public void OnMapReady(GoogleMap map)
{
    map.InfoWindowClick += MapOnInfoWindowClick;
}

private void MapOnInfoWindowClick (object sender, GoogleMap.InfoWindowClickEventArgs e)
{
    Marker myMarker = e.Marker;
    // Do something with marker.
}
```

回忆一下，"信息" 窗口是一个静态 `View`，它呈现为地图上的图像。 放置在 "信息" 窗口中的任何小组件（例如按钮、复选框或文本视图）都将静态，无法响应其任何重要用户事件。

## <a name="related-links"></a>相关链接

- [SimpleMapDemo](https://github.com/xamarin/monodroid-samples/tree/master/MapsAndLocationDemo_v3/SimpleMapDemo)
- [Google Play Services](https://developers.google.com/android/guides/overview)
- [Google Maps Android API v2](https://developers.google.com/maps/documentation/android-sdk/intro)
- [Google Play Services APK](https://play.google.com/store/apps/details?id=com.google.android.gms&hl=en)
- [获取 Google Maps API 密钥](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)
- [使用库](https://developer.android.com/guide/topics/manifest/uses-library-element)
- [使用-功能](https://developer.android.com/guide/topics/manifest/uses-feature-element)
