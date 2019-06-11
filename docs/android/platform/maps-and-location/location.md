---
title: 位置服务
description: 本指南介绍 Android 应用程序中的位置感知，并说明了如何获取 Google 位置服务 API 中使用 Android 的位置服务 API，以及可用的浮点混合的位置提供程序的用户的位置。
ms.prod: xamarin
ms.assetid: 0008682B-6CEF-0C1D-3200-56ECF58F5D3C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/22/2018
ms.openlocfilehash: 76f98f1e660f22ec25c48407f2e87cec60ff12ef
ms.sourcegitcommit: 2eb8961dd7e2a3e06183923adab6e73ecb38a17f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2019
ms.locfileid: "66827681"
---
# <a name="location-services"></a>位置服务

_本指南介绍 Android 应用程序中的位置感知，并说明了如何获取 Google 位置服务 API 中使用 Android 的位置服务 API，以及可用的浮点混合的位置提供程序的用户的位置。_

## <a name="location-services-overview"></a>位置服务概述

Android 提供对单元格 tower 位置、 Wi-fi 和 GPS 等各种位置技术的访问。 每个位置技术的详细信息抽象化通过*位置提供程序*，允许应用程序而不考虑使用的提供程序相同的方式获取位置。 本指南介绍浮点混合的位置提供程序，Google Play Services 后，它可以智能地确定获取基于哪些提供程序和设备的使用方式的设备的位置的最佳方法的一部分。 Android 位置服务 API，并演示如何与系统位置通信服务中使用`LocationManager`。 本指南的第二部分探讨了 Android 位置服务 API 使用`LocationManager`。
 
作为常规经验，应用程序应首选使用浮点混合的位置提供程序，回退较旧 Android 位置服务 API 仅在必要时。

## <a name="location-fundamentals"></a>位置基础知识

在 Android 中，无论您选择使用位置数据的哪些 API 的几个概念保持不变。 本部分介绍位置提供程序和与位置相关的权限。

### <a name="location-providers"></a>位置提供程序

在内部使用多种技术，以查明用户的位置。 使用的硬件上的类型取决于*位置提供程序*用于收集数据的作业。 Android 使用三个位置提供程序：

-   **GPS 提供程序** &ndash; GPS 提供最准确的位置，使用最高的能力，并室外可达效果最佳。 此提供程序使用 GPS 和辅助的 GPS 的组合 ([aGPS](https://en.wikipedia.org/wiki/Assisted_GPS))，这会返回收集的移动电话 towers GPS 数据。

-   **网络提供商**&ndash;提供的 WiFi 和手机网络数据，包括收集的单元格 towers aGPS 数据组合。 它使用较少的电量比 GPS 提供程序，但返回不同的准确性的位置数据。

-   **被动的提供程序**&ndash;使用请求的其他应用程序或服务提供程序以生成位置数据的应用程序中的"非法携带"选项。 这是一个不太可靠但的节能选项非常适合于不需要常量的位置更新，若要运行应用程序。

位置提供程序并不总是可用。 例如，我们可能想要为应用程序，使用 GPS 但 GPS 可能在设置中，已关闭或设备可能不会在所有具有 GPS。 如果特定的提供程序不可用，则选择该提供程序可能会返回`null`。

### <a name="location-permissions"></a>位置权限

位置感知应用程序需要访问设备的硬件传感器接收 GPS、 Wi-fi 和移动电话网络数据。 通过应用程序的 Android 清单中的适当权限控制的访问。
有两个权限&ndash;具体取决于应用程序的要求和所选的 API，你将想要允许一：

-   `ACCESS_FINE_LOCATION` &ndash; 允许对 GPS 的应用程序访问权限。
    所需*GPS 提供程序*并*被动的提供程序*选项 (*被动的提供程序必须有权访问 GPS 数据收集的另一个应用程序或服务*)。 可选的权限*网络提供商*。

-   `ACCESS_COARSE_LOCATION` &ndash; 允许对手机网络和 Wi-fi 位置的应用程序访问权限。 所需*网络提供商*如果`ACCESS_FINE_LOCATION`未设置。

对于面向 API 21 (Android 5.0 Lollipop) 版本的应用或更高版本，可以启用`ACCESS_FINE_LOCATION`并仍在没有 GPS 硬件的设备上运行。 如果你的应用需要 GPS 硬件，您应显式添加`android.hardware.location.gps` `uses-feature` Android 清单元素。 有关详细信息，请参阅 Android[使用功能](https://developer.android.com/guide/topics/manifest/uses-feature-element.html)元素引用。

若要设置权限，请展开**属性**中的文件夹**Solution Pad** ，然后双击**AndroidManifest.xml**。 将下面列出的权限**所需的权限**:

[![Android 清单所需的权限设置的屏幕截图](location-images/location-01-xs.png)](location-images/location-01-xs.png#lightbox)

设置这些权限之一告知 Android 应用程序需要从用户的权限才能访问的位置提供程序。 设备的运行 API 级别 22 (Android 5.1) 或更低时将要求用户每次安装应用程序授予这些权限。 在设备运行 API 级别 23 (Android 6.0) 或更高版本，应用应运行时权限之前执行检查发出请求的位置提供程序。 

> [!NOTE]
>注意:设置`ACCESS_FINE_LOCATION`意味着对这两个粗糙和正常位置数据的访问。 应无需设置这两个权限，仅*最小*您的应用程序工作所需的权限。

此代码片段示范了如何检查应用具有的权限`ACCESS_FINE_LOCATION`权限：

```csharp
 if (ContextCompat.CheckSelfPermission(this, Manifest.Permission.AccessFineLocation) == Permission.Granted)
{
    StartRequestingLocationUpdates();
    isRequestingLocationUpdates = true;
}
else
{
    // The app does not have permission ACCESS_FINE_LOCATION 
}
```

应用程序必须为容错的用户权限将授予 （或已吊销权限） 的方案，有一种方法来适当地处理这种情况。 请参阅[权限指南](~/android/app-fundamentals/permissions.md)上实现运行时权限的更多详细信息会检查在 Xamarin.Android 中。


## <a name="using-the-fused-location-provider"></a>使用浮点混合的位置提供程序

浮点混合的位置提供程序是为 Android 应用程序以从设备接收位置更新，因为它会有效地在运行时提供最佳的位置信息，以电池高效的方式选择的位置提供程序的首选的方法。 例如，移动办公了室外用户获取使用 GPS 读取的最佳位置。 如果用户然后走室内，其中 GPS 的工作效果不佳 （如果有），浮点混合的位置提供程序可能会自动切换到 WiFi，这非常适合更好的室内。
 
浮点混合的位置提供程序 API 提供了多种其他工具来使位置感知应用程序，包括地理围栏和活动监视。 在本部分中，我们将重点介绍有关设置的基础知识`LocationClient`，建立提供程序，以及获取用户的位置。

浮点混合的位置提供程序的一部分[Google Play Services](https://developer.android.com/google/play-services/index.html)。
Google Play Services 包必须安装并正确配置中的浮点混合的位置提供程序 API 应用程序工作，并且设备必须具有对 Google Play Services APK 安装。

在之前 Xamarin.Android 应用程序可以使用浮点混合的位置提供程序，它必须添加**Xamarin.GooglePlayServices.Maps**到项目的包。 此外，以下`using`语句应添加到引用如下所述的类的任何源文件：

```csharp
using Android.Gms.Common;
using Android.Gms.Location;
```

### <a name="checking-if-google-play-services-is-installed"></a>检查是否已安装 Google Play Services

然后运行时异常，Xamarin.Android 会崩溃，如果它尝试在未安装 Google Play 服务时使用浮点混合的位置提供程序 （或过期），会发生。  如果未安装 Google Play 服务，然后在应用程序应回退到 Android 上文所述的位置服务。 Google Play 服务已过时，如果应用程序可能向用户询问他们更新已安装的版本的 Google Play 服务显示一条消息。

此代码段是如何对 Android 活动可以通过编程方式检查是否已安装 Google Play 服务的示例：

```csharp
bool IsGooglePlayServicesInstalled()
{
    var queryResult = GoogleApiAvailability.Instance.IsGooglePlayServicesAvailable(this);
    if (queryResult == ConnectionResult.Success)
    {
        Log.Info("MainActivity", "Google Play Services is installed on this device.");
        return true;
    }

    if (GoogleApiAvailability.Instance.IsUserResolvableError(queryResult))
    {
        // Check if there is a way the user can resolve the issue
        var errorString = GoogleApiAvailability.Instance.GetErrorString(queryResult);
        Log.Error("MainActivity", "There is a problem with Google Play Services on this device: {0} - {1}",
                  queryResult, errorString);
                  
        // Alternately, display the error to the user.
    }

    return false;
}
```

### <a name="fusedlocationproviderclient"></a>FusedLocationProviderClient

若要与浮点混合的位置提供程序进行交互，Xamarin.Android 应用程序必须具有的实例`FusedLocationProviderClient`。 此类公开必需的方法来订阅位置更新和检索设备的最后一个已知的位置。

`OnCreate`活动的方法是一个合适的地方获取对引用`FusedLocationProviderClient`，如下面的代码段中所示：

```csharp
public class MainActivity: AppCompatActivity
{
    FusedLocationProviderClient fusedLocationProviderClient;

    protected override void OnCreate(Bundle bundle) 
    {
        fusedLocationProviderClient = LocationServices.GetFusedLocationProviderClient(this);
    }
}
```

### <a name="getting-the-last-known-location"></a>获取最后一个已知的位置

`FusedLocationProviderClient.GetLastLocationAsync()`方法提供了有关 Xamarin.Android 应用程序快速获取具有最少的编码开销的设备的最后一个已知的位置简单、 非阻止方式。

此代码片段演示如何使用`GetLastLocationAsync`方法来检索设备的位置：

```csharp
async Task GetLastLocationFromDevice()
{
    // This method assumes that the necessary run-time permission checks have succeeded.
    getLastLocationButton.SetText(Resource.String.getting_last_location);
    Android.Locations.Location location = await fusedLocationProviderClient.GetLastLocationAsync();

    if (location == null)
    {
        // Seldom happens, but should code that handles this scenario
    }
    else
    {
        // Do something with the location 
        Log.Debug("Sample", "The latitude is " + location.Latitude);
    }
}
```

### <a name="subscribing-to-location-updates"></a>订阅到位置更新

Xamarin.Android 应用程序还可以从使用浮点混合的位置提供程序订阅到位置更新`FusedLocationProviderClient.RequestLocationUpdatesAsync`方法，此代码片段中所示：

```csharp
await fusedLocationProviderClient.RequestLocationUpdatesAsync(locationRequest, locationCallback);
```
此方法采用两个参数：

-   **`Android.Gms.Location.LocationRequest`** &ndash; 一个`LocationRequest`对象是 Xamarin.Android 应用程序如何将参数传递的浮点混合的位置提供程序的工作原理。 `LocationRequest`保存信息此类应进行频繁的请求或准确位置更新应为重要程度如何。 例如，一个重要位置请求将导致设备时要使用 GPS，并因此更多能耗，确定位置。 此代码片段演示如何创建`LocationRequest`对于高精度的位置，检查大约每 5 分钟一次位置更新 （但不是早于请求之间的两分钟）。 浮点混合的位置提供程序将使用`LocationRequest`作为要使用时尝试确定设备位置的位置提供程序的指导原则：

    ```csharp
    LocationRequest locationRequest = new LocationRequest()
                                      .SetPriority(LocationRequest.PriorityHighAccuracy)
                                      .SetInterval(60 * 1000 * 5)
                                      .SetFastestInterval(60 * 1000 * 2);
    ```
                                          

-   **`Android.Gms.Location.LocationCallback`** &ndash; 为了接收位置更新，Xamarin.Android 应用程序必须子类`LocationProvider`抽象类。 此类公开由浮点混合的位置提供程序来更新应用程序的位置信息可能是调用该两种方法。 这将下面更详细地讨论。

若要通知的位置更新的 Xamarin.Android 应用程序，浮点混合的位置提供程序将调用`LocationCallBack.OnLocationResult(LocationResult result)`。 `Android.Gms.Location.LocationResult`参数将包含更新位置信息。

当浮点混合的位置提供程序位置数据的可用性中检测到更改时，它将调用`LocationProvider.OnLocationAvailability(LocationAvailability
locationAvailability)`方法。 如果`LocationAvailability.IsLocationAvailable`属性返回`true`，则可以假定，将设备位置结果报告`OnLocationResult`一样准确且为最新的所需的`LocationRequest`。 如果`IsLocationAvailable`为 false，则不将返回的任何位置结果`OnLocationResult`。

此代码片段是一个示例实现`LocationCallback`对象：

```csharp
public class FusedLocationProviderCallback : LocationCallback
{
    readonly MainActivity activity;

    public FusedLocationProviderCallback(MainActivity activity)
    {
        this.activity = activity;
    }

    public override void OnLocationAvailability(LocationAvailability locationAvailability)
    {
        Log.Debug("FusedLocationProviderSample", "IsLocationAvailable: {0}",locationAvailability.IsLocationAvailable);
    }

    public override void OnLocationResult(LocationResult result)
    {
        if (result.Locations.Any())
        {
            var location = result.Locations.First();
            Log.Debug("Sample", "The latitude is :" + location.Latitude);
        }
        else
        {
            // No locations to work with.
        }
    }
}
```

## <a name="using-the-android-location-service-api"></a>使用 Android 位置服务 API

Android 的位置服务是用于在 Android 上使用位置信息的较旧的 API。 位置数据收集的硬件传感器和系统服务，可在访问该应用程序中收集`LocationManager`类和一个`ILocationListener`。

位置服务最适合用于必须在没有 Google Play Services 安装的设备运行的应用程序。

位置服务是一种特殊[服务](https://developer.android.com/guide/components/services.html)由系统管理。 系统服务与设备硬件进行交互，并始终在运行。 若要利用我们的应用程序中的位置更新，我们将订阅位置更新系统的位置服务使用从`LocationManager`和一个`RequestLocationUpdates`调用。

若要获取用户的位置使用 Android 位置服务涉及到几个步骤：

1.  获取对引用`LocationManager`服务。
2.  实现`ILocationListener`位置更改时，接口和句柄事件。
3.  使用`LocationManager`为指定的提供程序的请求位置更新。 `ILocationListener`前一步骤中将用来接收从回调`LocationManager`。
4.  应用程序它不再适合时接收更新，请停止位置更新。

### <a name="location-manager"></a>位置管理器

我们就可以访问的实例的系统位置服务`LocationManager`类。 `LocationManager` 是一个特殊类，可以让我们使用的系统位置服务进行交互，对其调用方法。 应用程序可以获取对的引用`LocationManager`通过调用`GetSystemService`并传入服务类型，如下所示：

```csharp
LocationManager locationManager = (LocationManager) GetSystemService(Context.LocationService);
```

`OnCreate` 为获取对引用的好时机`LocationManager`。
它是最好随时`LocationManager`作为类变量，以便我们可以在活动生命周期的各个点调用它。

### <a name="request-location-updates-from-the-locationmanager"></a>从 LocationManager 请求位置更新

一旦应用程序后，对引用`LocationManager`，它需要告知`LocationManager`哪种类型的位置信息所必需的并更新该信息的频率。 执行此操作通过调用`RequestLocationUpdates`上`LocationManager`对象，并传入更新和将接收位置更新的回调的一些标准。 此回叫是一种类型，必须实现`ILocationListener`接口 （在本指南后面的更多详细信息中所述）。

`RequestLocationUpdates`方法告诉系统位置服务，你的应用程序想要开始接收位置更新。 此方法允许您指定的提供程序，以及时间和距离阈值来控制更新频率。 例如，下面的方法请求从 GPS 位置提供程序每隔 2000年毫秒，更新的位置，并仅当位置更改多个 1 metre:

```csharp
// For this example, this method is part of a class that implements ILocationListener, described below
locationManager.RequestLocationUpdates(LocationManager.GpsProvider, 2000, 1, this);
```

应用程序应仅根据需要为很好地运行应用程序请求位置更新。 这会保留的电池使用寿命，并创建用户更好的体验。

### <a name="responding-to-updates-from-the-locationmanager"></a>响应来自 LocationManager 的更新

应用程序已请求从更新后`LocationManager`，它可以从服务接收的信息，通过实现[ `ILocationListener` ](https://developer.xamarin.com/api/type/Android.Locations.ILocationListener/)接口。 此接口提供了四个方法以侦听服务的位置和位置提供程序， `OnLocationChanged`。 系统将调用`OnLocationChanged`用户的位置发生更改时够格作为根据请求位置更新时所设置的条件的位置更改。 

下面的代码演示中的方法`ILocationListener`接口：

```csharp
public class MainActivity : AppCompatActivity, ILocationListener
{
    TextView latitude;
    TextView longitude;
    
    public void OnLocationChanged (Location location)
    {
        // called when the location has been updated.
    }
    
    public OnProviderDisabled(string locationProvider)
    {
        // called when the user disables the provider
    }
    
    public OnProviderEnabled(string locationProvider)
    {
        // called when the user enables the provider
    }
    
    public OnStatusChanged(string locationProvider, Availability status, Bundle extras)
    {
        // called when the status of the provider changes (there are a variety of reasons for this)
    }
}
```

### <a name="unsubscribing-to-locationmanager-updates"></a>取消订阅到 LocationManager 更新

为了节省系统资源，应用程序应取消订阅到位置更新越早越好。 `RemoveUpdates`方法会示意`LocationManager`停止将更新发送到我们的应用程序。  例如，活动可以调用`RemoveUpdates`在`OnPause`方法，以便我们能够节省电量，如果应用程序不需要位置更新其活动时不在屏幕上：

```csharp
protected override void OnPause ()
{
    base.OnPause ();
    locationManager.RemoveUpdates (this);
}
```

如果你的应用程序需要获取在后台位置更新，你将想要创建订阅的系统位置服务的自定义服务。 请参阅[后台处理及 Android 服务](~/android/app-fundamentals/services/index.md)指南以获取详细信息。

### <a name="determining-the-best-location-provider-for-the-locationmanager"></a>确定 LocationManager 的最佳位置提供程序

更高版本的应用程序将 GPS 设置为位置提供程序。 但是，GPS 可能不能在所有情况下，例如设备是否室内或不具有 GPS 接收器。 如果是这样，则结果是`null`返回提供程序。

若要获取你的应用程序能够 GPS 不可用时，您使用`GetBestProvider`方法来请求应用程序启动时的最佳可用 （支持设备和用户启用） 的位置提供程序。 而不是特定提供程序中传递，可以判断`GetBestProvider`-如准确性和电源的使用的提供程序的要求[`Criteria`对象](https://developer.xamarin.com/api/type/Android.Locations.Criteria/)。 `GetBestProvider` 返回给定条件的最佳提供程序。

下面的代码演示如何获取最佳可用的提供程序，并请求位置更新时使用它：

```csharp
Criteria locationCriteria = new Criteria();   
locationCriteria.Accuracy = Accuracy.Coarse;
locationCriteria.PowerRequirement = Power.Medium;

locationProvider = locationManager.GetBestProvider(locationCriteria, true);

if(locationProvider != null)
{
    locationManager.RequestLocationUpdates (locationProvider, 2000, 1, this);
}
else
{
    Log.Info(tag, "No location providers available");
}
```

> [!NOTE]
>  如果用户已禁用所有位置提供程序`GetBestProvider`将返回`null`。 若要查看此代码在真实设备上的工作方式，请务必启用 GPS、 Wi-fi 和移动电话网络下的**Google 设置 > 位置 > 模式**此屏幕截图中所示：

[![在 Android 手机上的设置位置模式屏幕](location-images/location-02.png)](location-images/location-02.png#lightbox)

下面的屏幕截图演示了如何位置应用程序正在运行使用`GetBestProvider`:

[![GetBestProvider 应用显示纬度、 经度和提供程序](location-images/location-03.png)](location-images/location-03.png#lightbox)

请记住，`GetBestProvider`不会动态更改提供程序。 然而，它将一次在活动生命周期期间确定最佳可用的提供程序。 如果提供程序状态将更改已设置后，应用程序将需要额外的代码中`ILocationListener`方法&ndash; `OnProviderEnabled`， `OnProviderDisabled`，并`OnStatusChanged`&ndash;来处理所有与相关的可能性提供程序开关。

## <a name="summary"></a>总结

本指南介绍如何获取使用 Android 的位置服务和 Google 位置服务 API 中的浮点混合的位置提供程序的用户的位置。


## <a name="related-links"></a>相关链接

- [位置 （示例）](https://developer.xamarin.com/samples/monodroid/Location/)
- [FusedLocationProvider （示例）](https://developer.xamarin.com/samples/monodroid/FusedLocationProvider/)
- [Google Play 服务](https://developer.android.com/google/play-services/index.html)
- [条件类](https://developer.xamarin.com/api/type/Android.Locations.Criteria/)
- [LocationManager 类](https://developer.xamarin.com/api/type/Android.Locations.LocationManager/)
- [LocationListener 类](https://developer.xamarin.com/api/type/Android.Locations.ILocationListener/)
- [LocationClient API](https://developer.android.com/reference/com/google/android/gms/location/LocationClient.html)
- [LocationListener API](https://developer.android.com/reference/com/google/android/gms/location/LocationListener.html)
- [LocationRequest API](https://developer.android.com/reference/com/google/android/gms/location/LocationRequest.html)
