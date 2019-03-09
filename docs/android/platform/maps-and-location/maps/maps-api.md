---
title: 使用 Google 地图 API 在应用程序中
description: 如何在 Xamarin.Android 应用程序中实现 Google 地图 API v2 功能。
ms.prod: xamarin
ms.assetid: C0589878-2D04-180E-A5B9-BB41D5AF6E02
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 09/07/2018
ms.openlocfilehash: 12ff6f615b30e53704fee6368c9d7f171f881df0
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57671060"
---
# <a name="using-the-google-maps-api-in-your-application"></a>应用程序中使用 Google 地图 API

使用地图应用程序是很好，但有时您想要直接在您的应用程序中包括地图。 除了内置映射应用程序，还提供了 Google[适用于 Android 的本机映射 API](https://developers.google.com/maps/documentation/android-sdk/intro)。
地图 API 是适用于想要维护的映射体验的更好地控制的情况。 可以使用地图 API 的操作包括：

-  以编程方式更改映射的角度来看。
-  添加和自定义标记。
-  批注具有叠加的映射。

现已弃用的 Google Maps Android API v1 与 Google Maps Android API v2 是一部分[Google Play Services](https://developers.google.com/android/guides/overview)。
可以使用 Google Maps Android API 之前，Xamarin.Android 应用程序必须满足一些强制性先决条件。


## <a name="google-maps-api-prerequisites"></a>Google 地图 API 的先决条件

需要之前可以使用地图 API 中，执行几个步骤包括：

-  [获取地图 API 密钥](#obtain-maps-key)
-  [安装 Google Play Services SDK](#install-gps-sdk)
-  [从 NuGet 安装 Xamarin.GooglePlayServices.Maps 包](#install-gpsmaps-nuget)
-  [指定所需的权限](#declare-permissions)
-  [（可选） 使用 Google Api 创建一个仿真程序](#create-emulator-with-google-api)


### <a name="a-nameobtain-maps-key-obtain-a-google-maps-api-key"></a><a name="obtain-maps-key" />获取 Google 地图 API 密钥

第一步是获取 Google Maps API 密钥 （请注意，不能重复使用传统的 Google Maps v1 API 的 API 密钥）。 有关如何获取和使用 Xamarin.Android 使用的 API 密钥的信息，请参阅[获取 Google 地图 API 密钥](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)。
 

### <a name="a-nameinstall-gps-sdk--install-the-google-play-services-sdk"></a><a name="install-gps-sdk" /> 安装 Google Play Services SDK

Google Play Services 是一种技术将来自 Google，Android 应用程序可以充分利用各种 Google 功能，例如 Google +、 应用内计费和映射。 这些功能都在 Android 设备上可以访问作为后台服务，它包含在[Google Play Services APK](https://play.google.com/store/apps/details?id=com.google.android.gms&hl=en)。

与 Google Play Services 进行交互的 android 应用程序通过 Google Play 服务客户端库。 此库包含的接口和类，如映射单个服务。 下图显示了 Android 应用程序和 Google Play Services 之间的关系：

![说明 Google Play 商店更新 Google Play Services APK 的关系图](maps-api-images/play-services-diagram.png)

Android 地图 API 提供 Google Play 服务的一部分。
Xamarin.Android 应用程序可以使用地图 API 之前，必须使用安装 Google Play Services SDK [Android SDK 管理器](~/android/get-started/installation/android-sdk.md)。 以下屏幕截图显示的位置在 Android SDK 管理器中找不到 Google Play 服务客户端：

![Google Play Services 下 Extras Android SDK 管理器中显示](maps-api-images/image01.png)

> [!NOTE]
> Google Play services APK 是可能不会显示在所有设备上的许可的产品。 如果未安装，然后 Google Maps 不会在该设备。

### <a name="a-nameinstall-gpsmaps-nuget--install-the-xamaringoogleplayservicesmaps-package-from-nuget"></a><a name="install-gpsmaps-nuget" /> 从 NuGet 安装 Xamarin.GooglePlayServices.Maps 包

[Xamarin.GooglePlayServices.Maps 包](https://www.nuget.org/packages/Xamarin.GooglePlayServices.Maps)包含 Google Play Services 地图 API 的 Xamarin.Android 绑定。
若要添加 Google Play 服务映射包，请右键单击**引用**在解决方案资源管理器中单击项目文件夹**管理 NuGet 包...**:

![在引用下的解决方案资源管理器中显示管理 NuGet 包上下文菜单项](maps-api-images/image02.png)

这将打开**NuGet 包管理器**。 单击**浏览**并输入**Xamarin Google Play Services 地图**搜索字段中。 选择**Xamarin.GooglePlayServices.Maps**然后单击**安装**。 (如果以前已安装此包，请单击**更新**。):

[![使用选择的 Xamarin.GooglePlayServices.Maps 包的 NuGet 包管理器](maps-api-images/image03-sml.png)](maps-api-images/image03.png#lightbox)

请注意，还会安装以下依赖项包：

-   **Xamarin.GooglePlayServices.Base**
-   **Xamarin.GooglePlayServices.Basement**
-   **Xamarin.GooglePlayServices.Tasks**

### <a name="a-namedeclare-permissions--specify-the-required-permissions"></a><a name="declare-permissions" /> 指定所需的权限

应用程序必须确定要使用 Google Maps API 的硬件和权限要求。  Google Play Services SDK 将自动授予一些权限并不需要开发人员显式将其添加到**AndroidManfest.XML**:

-  **对网络状态的访问**&ndash;地图 API 必须能够检查是否它可以下载地图图块。

-  **Internet 访问**&ndash;都下载地图图块和与 API 访问 Google Play 服务器通信所需访问 Internet。

必须在指定的以下权限和功能**AndroidManifest.XML** Google Maps Android api:

-  **OpenGL ES v2** &ndash;应用程序必须声明 OpenGL ES v2 的要求。

-  **Google 地图 API 密钥** &ndash; API 密钥用于确认注册并有权使用 Google Play Services 应用程序。 请参阅[获取 Google 地图 API 密钥](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)有关此密钥的详细信息。
   
- **请求的旧的 Apache HTTP 客户端**&ndash;面向 Android 9.0 （API 级别 28） 的应用，或更高版本必须指定旧 Apache HTTP 客户端是一个可选的库使用。 

-  **对基于 Google Web 服务的访问**&ndash;应用程序需要有权访问 Google 的返回 Android 地图 API 的 web 服务。

-  **Google Play 服务通知的权限**&ndash;必须授予应用程序从 Google Play 服务接收远程通知的权限。

-  **访问位置提供程序**&ndash;这些是可选的权限。
   将允许`GoogleMap`类，以在地图上显示设备的位置。


> [!NOTE]
> 非常旧版本的 Google Play SDK 所需的应用以请求`WRITE_EXTERNAL_STORAGE`权限。 此要求是不再需要使用 Google Play 服务的最新的 Xamarin 绑定。

以下代码片段示范了必须添加到的设置**AndroidManifest.XML**:

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
    </application>
</manifest>
```

除了请求的权限**AndroidManifest.XML**，应用还必须执行的运行时权限检查`ACCESS_COARSE_LOCATION`和`ACCESS_FINE_LOCATION`权限。 请参阅[Xamarin.Android 权限](~/android/app-fundamentals/permissions.md)指南以获取有关执行运行时权限检查的详细信息。


### <a name="a-namecreate-emulator-with-google-api-create-an-emulator-with-google-apis"></a><a name="create-emulator-with-google-api" />使用 Google Api 创建一个仿真程序

在的物理 Android 设备与 Google Play 服务未安装，就可以创建用于开发的仿真程序映像。 有关详细信息请参阅[设备管理器](~/android/get-started/installation/android-emulator/device-manager.md)。


## <a name="the-googlemap-class"></a>GoogleMap 类

一旦满足先决条件，就可以开始开发应用程序并使用 Android 地图 API。 [GoogleMap](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap)类是主要的 Xamarin.Android 应用程序将用于显示和适用于 Android 与 Google 地图进行交互的 API。 此类具有下列职责：

-  与 Google Play 服务授权与 Google web 服务应用程序进行交互。

-  下载、 缓存和显示地图图块。

-  显示 UI 控件，如平移和缩放到用户。

-  在地图上绘制标记和几何形状。

`GoogleMap`添加到两种方式之一中的活动：

-  **MapFragment** - [MapFragment](https://developers.google.com/android/reference/com/google/android/gms/maps/MapFragment)充当主机是专用片段`GoogleMap`对象。 `MapFragment`需要 Android API 级别 12 或更高版本。
   可以使用较旧版本的 Android [SupportMapFragment](https://developers.google.com/android/reference/com/google/android/gms/maps/SupportMapFragment)。  本指南将重点介绍使用`MapFragment`类。

-  **MapView** - [MapView](https://developers.google.com/android/reference/com/google/android/gms/maps/MapView)是专用的视图子类，可充当主机`GoogleMap`对象。 此类的用户必须转发到的活动生命周期方法的所有`MapView`类。

每个容器公开`Map`返回的实例的属性`GoogleMap`。 首选项应授予给[MapFragment](https://developers.google.com/android/reference/com/google/android/gms/maps/MapFragment)类，如它是一种更简单的 API，减少了开发人员必须手动实现的量样板代码。

### <a name="adding-a-mapfragment-to-an-activity"></a>向活动添加 MapFragment

下面的屏幕截图是一个简单的示例`MapFragment`:

[![显示 Google 地图片段设备的屏幕截图](maps-api-images/image05-sml.png)](maps-api-images/image05.png#lightbox)

与其他片段类相似，有两种方法来添加`MapFragment`到活动：

-   **以声明方式**-`MapFragment`活动可以通过 XML 布局文件中添加。 以下 XML 代码片段示范如何使用`fragment`元素：

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <fragment xmlns:android="http://schemas.android.com/apk/res/android"
              android:id="@+id/map"
              android:layout_width="match_parent"
              android:layout_height="match_parent"
              class="com.google.android.gms.maps.MapFragment" />
    ```

-   **以编程方式**-`MapFragment`可以以编程方式使用实例化[ `MapFragment.NewInstance` ](https://developers.google.com/android/reference/com/google/android/gms/maps/MapFragment.html#newInstance())方法，然后添加到活动。 此代码段显示了实例化的最简单方法`MapFragment`对象，并向活动中添加：
    
    ```csharp
        var mapFrag = MapFragment.NewInstance();
        activity.FragmentManager.BeginTransaction()
                                .Add(Resource.Id.map_container, mapFrag, "map_fragment")
                                .Commit();

    ```

    可以配置`MapFragment`对象通过传递[ `GoogleMapOptions` ](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMapOptions)对象传递给`NewInstance`。 此部分中讨论[GoogleMap 属性](#googlemap_object)本指南中稍后显示。

`MapFragment.GetMapAsync`方法用来初始化[ `GoogleMap` ](#googlemap_object)的片段托管并获取对由托管的映射对象的引用`MapFragment`。 此方法采用一个对象，实现`IOnMapReadyCallback`接口。 

此接口具有单个方法`IMapReadyCallback.OnMapReady(MapFragment map)`很可能要与之交互的应用时，将调用的`GoogleMap`对象。 以下代码片段演示如何对 Android 活动可以将其初始化`MapFragment`并实现`IOnMapReadyCallback`接口：
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

### <a name="map-types"></a>将类型映射

有五种不同类型的映射从 Google Maps API 可用：

-  **正常**-这是默认映射类型。 它显示道路和重要自然功能，此外还 （如建筑物和桥） 感兴趣的一些人工分数。

-  **附属**-此图显示了附属摄影。

-  **混合**-此映射显示附属摄影和道路映射。

-  **地形**-这主要显示了与部分道路的地形特征。

-  **无**-此映射将不会加载任何磁贴，呈现为一个空网格。


下图显示了三个不同类型的地图，从左到右 （正常、 混合，地形）：

[![三个映射示例屏幕快照：Normal、 混合和地形](maps-api-images/map-types-sml.png)](maps-api-images/map-types.png#lightbox)

`GoogleMap.MapType`属性用于设置或更改显示哪种类型的映射。 下面的代码段演示如何显示附属映射。

```csharp
public void OnMapReady(GoogleMap map)
{
    map.MapType = GoogleMap.MapTypeHybrid;
}
```


### <a name="a-namegooglemapobject-googlemap-properties"></a><a name="googlemap_object" />GoogleMap 属性

`GoogleMap` 定义多个可以控制功能和地图的外观的属性。 配置的初始状态的一种方法`GoogleMap`是将传递[GoogleMapOptions](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMapOptions)对象创建时`MapFragment`。 下面的代码段是使用的一个示例`GoogleMapOptions`对象创建时`MapFragment`:

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

配置的其他方式`GoogleMap`通过在操作属性是[UiSettings](https://developers.google.com/android/reference/com/google/android/gms/maps/UiSettings)的 map 对象。 下一步的代码示例演示如何配置`GoogleMap`显示缩放控件和指南针：

```csharp
public void OnMapReady(GoogleMap map)
{
    map.UiSettings.ZoomControlsEnabled = true;
    map.UiSettings.CompassEnabled = true;
}
```

## <a name="interacting-with-the-googlemap"></a>与 GoogleMap 交互

Android 地图 API 提供的 Api，使活动更改角度来看，添加标记、 放置自定义覆盖或绘制几何形状。 本部分将讨论如何完成这些任务在 Xamarin.Android 中的一些。

### <a name="changing-the-viewpoint"></a>更改视区

映射是在屏幕上，根据 Mercator 投影为平面平面来建模。 地图视图是*照相机*直接向下查找此平面上。 可以通过更改位置、 缩放、 倾斜，并影响控制照相机的位置。 [CameraUpdate](https://developers.google.com/android/reference/com/google/android/gms/maps/CameraUpdate)类用于移动照相机位置。 `CameraUpdate` 不直接实例化对象，而是映射 API 提供[CameraUpdateFactory](https://developers.google.com/android/reference/com/google/android/gms/maps/CameraUpdateFactory)类。

一次`CameraUpdate`创建对象，它作为参数传递到[GoogleMap.MoveCamera](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap#moveCamera(com.google.android.gms.maps.CameraUpdate))或[GoogleMap.AnimateCamera](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap#animateCamera(com.google.android.gms.maps.CameraUpdate))方法。 `MoveCamera`方法更新时立即映射`AnimateCamera`方法提供平滑的动画转换。

此代码段是如何使用一个简单示例`CameraUpdateFactory`若要创建`CameraUpdate`的递增一个缩放级别的地图的缩放级别：

```csharp
MapFragment mapFrag = (MapFragment) FragmentManager.FindFragmentById(Resource.Id.my_mapfragment_container);
mapFrag.GetMapAsync(this);
...

public void OnMapReady(GoogleMap map)
{   
    map.MoveCamera(CameraUpdateFactory.ZoomIn());
}
```

地图 API 提供了[CameraPosition](https://developer.android.com/reference/com/google/android/gms/maps/model/CameraPosition.html)其中将聚合所有照相机的位置的可能值。 此类的实例可以提供给[CameraUpdateFactory.NewCameraPosition](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/CameraUpdateFactory#newCameraPosition%28com.google.android.gms.maps.model.CameraPosition%29)方法将返回`CameraUpdate`对象。 地图 API 还包括[CameraPosition.Builder](https://developer.android.com/reference/com/google/android/gms/maps/model/CameraPosition.Builder.html) fluent API 提供用于创建类`CameraPosition`对象。
下面的代码段显示了创建的示例`CameraUpdate`从`CameraPosition`并使用该更改照相机的位置上`GoogleMap`:

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

在上一代码段中，由表示在地图上的特定位置[LatLng](https://developers.google.com/android/reference/com/google/android/gms/maps/model/LatLng)类。 缩放级别设置为 18 这是由 Google 地图缩放任意度量值。 影响是指南针的度量值从北部顺时针旋转。 倾斜属性控制的查看角度，它指定从垂直 25 度角。 以下屏幕截图显示`GoogleMap`后执行前面的代码：

[![显示具有倾斜的指定的位置的示例 Google 地图查看角度](maps-api-images/image06-sml.png)](maps-api-images/image06.png#lightbox)


### <a name="drawing-on-the-map"></a>在地图上绘制

Android 地图 API 提供了 API 的用于在地图上绘制的以下项：

-  **标记**-这些是用于标识在地图上的单个位置的特殊图标。

-  **覆盖**-这是可以使用用于标识某个集合的位置或在地图上的区域的图像。

-  **线条、 多边形和圆形**-这些是允许活动向映射添加形状的 Api。


#### <a name="markers"></a>标记

地图 API 提供了[标记](https://developers.google.com/android/reference/com/google/android/gms/maps/model/Marker)类封装所有有关在地图上的单个位置的数据。 默认情况下，标记类使用 Google 地图提供的一个标准图标。 可以自定义标记的外观并响应用户单击它。


##### <a name="adding-a-marker"></a>添加一个标记

若要向映射添加一个标记，有必要创建一个新[MarkerOptions](https://developers.google.com/android/reference/com/google/android/gms/maps/model/MarkerOptions)对象，然后调用[AddMarker](https://developer.android.com/reference/com/google/android/gms/maps/GoogleMap.html#addMarker%28com.google.android.gms.maps.model.MarkerOptions%29)方法`GoogleMap`实例。 此方法将返回[标记](https://developers.google.com/android/reference/com/google/android/gms/maps/model/Marker)对象。

```csharp
public void OnMapReady(GoogleMap map)
{
    MarkerOptions markerOpt1 = new MarkerOptions();
    markerOpt1.SetPosition(new LatLng(50.379444, 2.773611));
    markerOpt1.SetTitle("Vimy Ridge");
    
    map.AddMarker(markerOpt1);
}
```

标记的标题将显示在*信息窗口*时在用户点击该标记。 下面的屏幕截图显示了此标记如下所示：

[![使用标记和 Vimy 凸缘的信息窗口示例 Google 地图](maps-api-images/image07-sml.png)](maps-api-images/image07.png#lightbox)


##### <a name="customizing-a-marker"></a>自定义标记

可以自定义图标标记由调用`MarkerOptions.InvokeIcon`方法将标记添加到代码图时。
此方法采用[BitmapDescriptor](https://developers.google.com/android/reference/com/google/android/gms/maps/model/BitmapDescriptor)对象，其中包含要呈现图标所需的数据。 [BitmapDescriptorFactory](https://developers.google.com/android/reference/com/google/android/gms/maps/model/BitmapDescriptorFactory)类提供了一些帮助器方法来简化创建`BitmapDescriptor`。 以下列表介绍了其中的某些方法：

-   `DefaultMarker(float colour)` &ndash; 使用默认 Google 地图标记，但更改颜色。

-   `FromAsset(string assetName)` &ndash; 从资产文件夹中的指定文件中使用自定义图标。

-   `FromBitmap(Bitmap image)` &ndash; 指定的位图用作图标。

-   `FromFile(string fileName)` &ndash; 从指定路径处的文件创建自定义图标。

-   `FromResource(int resourceId)` &ndash; 从指定的资源创建自定义图标。

下面的代码段显示了创建青色彩色的默认标记的示例：

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

#### <a name="info-windows"></a>信息 windows

*信息 windows*是该弹出窗口，用于向用户显示的信息，当用户点击特定标记的特殊窗口。 默认情况下信息窗口会显示标记的标题的内容。 如果未分配有标题，将不显示任何信息窗口。 只能有一个信息窗口中显示的一次。

可以通过实现自定义信息窗口[GoogleMap.IInfoWindowAdapter](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.InfoWindowAdapter)接口。 此接口上有两个重要方法：

-  `public View GetInfoWindow(Marker marker)` &ndash; 若要自定义的信息窗口获得一个标记，调用此方法。 如果它返回`null`，则将使用默认的窗口呈现。 如果此方法返回的视图，该视图将被放置在信息窗口框架。

-  `public View GetInfoContents(Marker marker)` &ndash; 将仅调用此方法，如果 GetInfoWindow 返回`null`。 此方法可返回`null`时的信息窗口内容的默认呈现时要使用的值。 否则，此方法应返回具有信息窗口的内容的视图。

信息窗口不是实时视图-Android 改为将将视图转换为静态位图和显示的图像。 这意味着，信息窗口无法响应的任何触摸事件或手势，也不将其自动进行自我更新。 若要更新的信息窗口，为调用所需[GoogleMap.ShowInfoWindow](https://developers.google.com/android/reference/com/google/android/gms/maps/model/Marker.html#showInfoWindow())方法。

下图显示了某些自定义的信息窗口的一些示例。 在左侧图像都有其自定义，而右侧图像具有其窗口的内容和使用圆角自定义的内容：

![墨尔本，包括图标和填充的示例中标记 windows。 在右侧窗口具有圆角。](maps-api-images/marker-infowindows.png)

#### <a name="groundoverlays"></a>GroundOverlays

与不同的标记，标识在地图上的特定位置，请[GroundOverlay](https://developers.google.com/android/reference/com/google/android/gms/maps/model/GroundOverlay)是用于标识的位置或在地图上的某个区域集合的映像。

##### <a name="adding-a-groundoverlay"></a>添加 GroundOverlay

向地图添加地面覆盖层是类似于向地图添加一个标记。 首先， [GroundOverlayOptions](https://developers.google.com/android/reference/com/google/android/gms/maps/model/GroundOverlayOptions)创建对象。 此对象然后作为参数传递[ `GoogleMap.AddGroundOverlay` ](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.html#addGroundOverlay(com.google.android.gms.maps.model.GroundOverlayOptions))方法，它将返回`GroundOverlay`对象。 此代码片段是向映射添加地面覆盖层的示例：

```csharp
BitmapDescriptor image = BitmapDescriptorFactory.FromResource(Resource.Drawable.polarbear);
GroundOverlayOptions groundOverlayOptions = new GroundOverlayOptions()
    .Position(position, 150, 200)
    .InvokeImage(image);
GroundOverlay myOverlay = googleMap.AddGroundOverlay(groundOverlayOptions);
```

下面的屏幕截图显示在地图上覆盖此层：

[![使用极坐标图都具有其映像的示例映射](maps-api-images/image09-sml.png)](maps-api-images/image09.png#lightbox)


#### <a name="lines-circles-and-polygons"></a>线条、 圆形和多边形

有三个简单类型的可以添加到地图的几何图：

-  **折线**-这是一系列相互连接的线段。 它可以将标记在地图上的路径，或创建几何形状。

-  **圆形**-这将在地图上绘制一个圆圈。

-  **多边形**-这是一个闭合的形状用于标记在地图上的区域。


##### <a name="polylines"></a>折线

一个[折线](https://developers.google.com/android/reference/com/google/android/gms/maps/model/Polyline)是一系列连续`LatLng`对象指定每个线段的顶点。 通过首先创建创建一条折线`PolylineOptions`对象并向其中添加点。 `PolylineOption`对象然后传递给`GoogleMap`对象通过调用`AddPolyline`方法。

```csharp
PolylineOption rectOptions = new PolylineOption();
rectOptions.Add(new LatLng(37.35, -122.0));
rectOptions.Add(new LatLng(37.45, -122.0));
rectOptions.Add(new LatLng(37.45, -122.2));
rectOptions.Add(new LatLng(37.35, -122.2));
rectOptions.Add(new LatLng(37.35, -122.0)); // close the polyline - this makes a rectangle.

googleMap.AddPolyline(rectOptions);
```

##### <a name="circles"></a>圆形

由第一个实例化创建圆形[CircleOption](https://developers.google.com/android/reference/com/google/android/gms/maps/model/CircleOptions)对象将指定米在中心和圆的半径。 调用在地图上绘制圆形[GoogleMap.AddCircle](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.html#addCircle(com.google.android.gms.maps.model.CircleOptions))。
下面的代码段显示了如何绘制一个圆：

```csharp
CircleOptions circleOptions = new CircleOptions ();
circleOptions.InvokeCenter (new LatLng(37.4, -122.1));
circleOptions.InvokeRadius (1000);

googleMap.AddCircle (circleOptions);
```


##### <a name="polygons"></a>多边形

`Polygon`类似于`Polyline`s，但未处于打开状态结束。 `Polygon`s 是封闭式的循环，并且具有填充其内部。
`Polygon`在完全相同的方式创建`Polyline`，除非[GoogleMap.AddPolygon](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.html#addPolygon(com.google.android.gms.maps.model.PolygonOptions))调用方法。

与不同`Polyline`、`Polygon`自结束。 将通过关闭多边形`AddPolygon`通过绘制一条连接的第一个和最后一个点的直线的方法。 下面的代码段将通过相同区域中的上一代码段创建一个实心矩形`Polyline`示例。

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

有三种类型的用户可能有一个带有地图的交互：

-  **标记单击**-用户单击某个标记。

-  **标记拖动**-用户已长时间-单击 mparger 上

-  **信息窗口中单击**-用户单击信息窗口上。

将下面更详细地讨论每个这些事件。


### <a name="marker-click-events"></a>单击事件标记

`MarkerClicked`用户点击一个标记时引发事件。 此事件接受`GoogleMap.MarkerClickEventArgs`对象作为参数。 此类包含两个属性：

-  `GoogleMap.MarkerClickEventArgs.Handled` &ndash; 此属性应设置为`true`以指示事件处理程序已使用该事件。 如果此值设置为`false`则默认行为将发生除了事件处理程序的自定义行为。

-  `Marker` &ndash; 此属性是对引发的标记的引用`MarkerClick`事件。


此代码片段演示的示例`MarkerClick`，会将照相机的位置更改为地图上的新位置：

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


### <a name="marker-drag-events"></a>将事件标记

当用户想要将标记拖动时，引发此事件。 默认情况下，标记不是可拖动。 一个标记，可以设置为可拖动通过设置`Marker.Draggable`属性设置为`true`或通过调用`MarkerOptions.Draggable`方法替换`true`作为参数。

若要将标记拖动，用户必须首先长时间单击标记，那么他们的手指必须保持在代码图上。 当用户的手指在屏幕上拖动围绕时，将移动标记。 当用户的手指抬起出屏幕时，该标记将保持不变。

以下列表描述可拖动标记，将生成的各种事件：

-   `GoogleMap.MarkerDragStart(object sender, GoogleMap.MarkerDragStartEventArgs e)` &ndash; 在用户第一次拖动标记时，引发此事件。

-   `GoogleMap.MarkerDrag(object sender, GoogleMap.MarkerDragEventArgs e)` &ndash; 按标记进行拖动，将引发此事件。

-   `GoogleMap.MarkerDragEnd(object sender, GoogleMap.MarkerDragEndEventArgs e)` &ndash; 当用户已完成时，将引发此事件将标记。

每个`EventArgs`包含一个名为的单个属性`P0`，它是引用`Marker`对象正被拖动。


### <a name="info-window-click-events"></a>信息窗口中单击事件

可以一次显示一个信息窗口。 当用户单击图中的信息窗口中时，将引发 map 对象`InfoWindowClick`事件。 下面的代码段显示了如何绑定到事件处理程序：

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

请注意，信息窗口是一个静态`View`其呈现为地图上的图像。 任何小组件，如按钮、 复选框或放置在信息窗口内的文本视图将静态，并且不能响应任何其整型用户事件。


## <a name="related-links"></a>相关链接

- [SimpleMapDemo](https://github.com/xamarin/monodroid-samples/tree/master/MapsAndLocationDemo_v3/SimpleMapDemo)
- [Google Play 服务](https://developers.google.com/android/guides/overview)
- [Google 映射 Android API v2](https://developers.google.com/maps/documentation/android-sdk/intro)
- [Google Play 服务的 APK](https://play.google.com/store/apps/details?id=com.google.android.gms&hl=en)
- [获取 Google Maps API 密钥](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)
- [uses-library](https://developer.android.com/guide/topics/manifest/uses-library-element)
- [uses-feature](https://developer.android.com/guide/topics/manifest/uses-feature-element)
