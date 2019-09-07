---
title: Android 上的位置服务
description: 本指南介绍了如何在 Android 应用程序中了解位置，并说明了如何使用 Android 位置服务 API 获取用户的位置，并说明了 Google 位置服务 API 提供的随附位置提供商。
ms.prod: xamarin
ms.assetid: 0008682B-6CEF-0C1D-3200-56ECF58F5D3C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/22/2018
ms.openlocfilehash: 61532eb1e31db6a862275180394b2b5ba9b05f8e
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70761718"
---
# <a name="location-services-on-android"></a>Android 上的位置服务

_本指南介绍了如何在 Android 应用程序中了解位置，并说明了如何使用 Android 位置服务 API 获取用户的位置，并说明了 Google 位置服务 API 提供的随附位置提供商。_

Android 提供对各种位置技术（如单元塔位置、Wi-fi 和 GPS）的访问。 每个位置技术的详细信息是通过*位置提供程序*来抽象的，因此，无论使用哪种提供程序，应用程序都可以通过相同的方式获得位置。 本指南介绍了保险丝的位置提供程序，这是 Google Play Services 的一部分，它可以智能地根据提供的提供程序和设备的使用方式来确定设备位置的最佳方式。 Android 位置服务 API，并演示如何使用`LocationManager`与系统位置服务通信。 本指南的第二部分探讨使用的`LocationManager`Android 位置服务 API。

一般的经验法则是，应用程序应该更倾向于使用带保险丝的位置提供程序，仅在必要时才回退较旧的 Android 位置服务 API。

## <a name="location-fundamentals"></a>位置基础知识

在 Android 中，无论选择哪种 API 来处理位置数据，都有多个概念是相同的。 本部分介绍位置提供程序和与位置相关的权限。

### <a name="location-providers"></a>位置提供程序

在内部使用多种技术来确定用户的位置。 使用的硬件取决于为收集数据的作业选择的*位置提供程序*的类型。 Android 使用三个位置提供程序：

- **GPS 提供程序**&ndash; GPS 提供最准确的位置，使用最强大的功能，并在户外工作。 此提供程序使用 GPS 和辅助 GPS （[aGPS](https://en.wikipedia.org/wiki/Assisted_GPS)）的组合，该组合可返回移动电话塔收集的 GPS 数据。

- **网络提供商**&ndash;提供 WiFi 和蜂窝数据的组合，包括单元塔收集的 aGPS 数据。 它比 GPS 提供程序使用的能耗更少，但返回的位置数据具有不同的准确性。

- **被动提供程序**&ndash;使用其他应用程序或服务请求的提供程序在应用程序中生成位置数据的 "被使用" 选项。 这是一种不太可靠，但节能选项非常适用于不需要就地更新工作的应用程序。

位置提供程序并非始终可用。 例如，我们可能希望将 GPS 用于我们的应用程序，但在 "设置" 中可以关闭 GPS，或者设备根本没有 GPS。 如果特定的提供程序不可用，则选择该提供程序`null`可能会返回。

### <a name="location-permissions"></a>位置权限

可识别位置的应用程序需要访问设备的硬件传感器来接收 GPS、Wi-fi 和蜂窝数据。 通过应用程序的 Android 清单中的适当权限控制访问权限。
根据应用程序的要求&ndash;以及所选的 API，有两种可用的权限：

- `ACCESS_FINE_LOCATION`&ndash;允许应用程序访问 GPS。
    对于*GPS 提供程序*和*被动提供*程序选项是必需的（*被动提供程序需要访问其他应用程序或服务收集的 GPS 数据的权限*）。 *网络提供程序*的可选权限。

- `ACCESS_COARSE_LOCATION`&ndash;允许应用程序访问手机网络和 wi-fi 位置。 如果`ACCESS_FINE_LOCATION`未设置，则需要用于*网络提供程序*。

对于面向 API 版本21（Android 5.0 棒糖）或更高版本的应用程序`ACCESS_FINE_LOCATION` ，你可以在没有 GPS 硬件的设备上启用并仍可运行。 如果你的应用需要 GPS 硬件，则应将`android.hardware.location.gps` `uses-feature`元素显式添加到 Android 清单中。 有关详细信息，请参阅 Android[使用功能](https://developer.android.com/guide/topics/manifest/uses-feature-element.html)元素参考。

若要设置权限，请展开**Solution Pad**中的 "**属性**" 文件夹，然后双击 " **androidmanifest.xml**"。 权限将在 "**所需权限**" 下列出：

[![Android 清单所需权限设置的屏幕截图](location-images/location-01-xs.png)](location-images/location-01-xs.png#lightbox)

设置其中任一权限会告诉 Android 应用程序需要用户的权限才能访问位置提供程序。 运行 API 级别22（Android 5.1）或更低版本的设备将要求用户在每次安装应用时授予这些权限。 在运行 API 级别23（Android 6.0）或更高版本的设备上，应用应在发出位置提供程序请求之前执行运行时权限检查。 

> [!NOTE]
>注意:设置`ACCESS_FINE_LOCATION`表示对粗和精细位置数据的访问。 决不要设置这两个权限，只需设置应用程序运行所需的*最小*权限。

此代码片段示例说明了如何检查应用是否具有`ACCESS_FINE_LOCATION`权限权限：

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

应用程序必须能够容忍用户不会授予权限（或废除了权限）的情况，并有适当的方式来处理这种情况。 请参阅[权限指南](~/android/app-fundamentals/permissions.md)，以了解有关在 Xamarin 中实施运行时权限检查的更多详细信息。

## <a name="using-the-fused-location-provider"></a>使用带保险丝的位置提供程序

带保险丝的位置提供程序是 Android 应用程序从设备接收位置更新的首选方式，因为它将在运行时有效地选择位置提供程序，以便以电池有效的方式提供最好的位置信息。 例如，在户外浏览的用户会获得最佳位置，并使用 GPS 阅读。 如果用户随后会走到室外，其中 GPS 的工作效果很差（如果根本如此），则已保险丝的位置提供商可能会自动切换到 WiFi，这会使室内工作更好。

带保险丝的位置提供程序 API 提供各种其他工具来提供可识别位置的应用程序，包括地理围栏和活动监视。 在本部分中，我们将重点介绍设置`LocationClient`、建立提供程序和获取用户位置的基本知识。

带保险丝的位置提供程序是[Google Play Services](https://developer.android.com/google/play-services/index.html)的一部分。
在应用程序中，必须正确安装和配置 Google Play Services 包，才能使用带保险丝位置提供程序的 API，并且设备必须安装了 Google Play Services APK。

在 Xamarin Android 应用程序可以使用带保险丝的位置提供程序之前，它必须将**GooglePlayServices**包添加到项目。 此外，应将以下`using`语句添加到引用下述类的任何源文件：

```csharp
using Android.Gms.Common;
using Android.Gms.Location;
```

### <a name="checking-if-google-play-services-is-installed"></a>检查是否已安装 Google Play Services

如果在未安装（或过期） Google Play Services 时，Xamarin 将崩溃，如果尝试使用带保险丝的位置提供程序，则会发生运行时异常。  如果未安装 Google Play Services，则应用程序应回退到前面讨论的 Android 位置服务。 如果 Google Play Services 过期，应用程序可能会向用户显示一条消息，要求他们更新 Google Play Services 的已安装版本。

此代码片段举例说明了 Android 活动如何以编程方式检查是否安装了 Google Play Services：

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

若要与带保险丝的位置提供程序交互，Xamarin Android 应用程序必须具有的`FusedLocationProviderClient`实例。 此类公开了订阅位置更新和检索设备的最后一个已知位置所必需的方法。

活动`OnCreate`的方法是获取对的`FusedLocationProviderClient`引用的合适位置，如以下代码片段所示：

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

### <a name="getting-the-last-known-location"></a>获取上一个已知位置

`FusedLocationProviderClient.GetLastLocationAsync()`方法为 Xamarin Android 应用程序提供了一种简单、非阻止的方式，用于快速获取设备的最后一个已知位置，并具有最小编码开销。

此代码片段演示如何使用`GetLastLocationAsync`方法检索设备的位置：

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

### <a name="subscribing-to-location-updates"></a>订阅位置更新

Xamarin Android 应用程序还可以使用`FusedLocationProviderClient.RequestLocationUpdatesAsync`方法从带保险丝的位置提供程序订阅位置更新，如以下代码片段所示：

```csharp
await fusedLocationProviderClient.RequestLocationUpdatesAsync(locationRequest, locationCallback);
```

此方法采用两个参数：

- **`Android.Gms.Location.LocationRequest`** &ndash; 对象是一种方法，即，Xamarin应用`LocationRequest`程序如何传递参数。 `LocationRequest`保存信息，例如应进行请求的频率，或精确位置更新的重要程度。 例如，重要的位置请求会导致设备在确定位置时使用 GPS，并因此更强大。 此代码段演示如何为具有高`LocationRequest`准确性的位置创建，每隔五分钟检查一次位置更新（但两次请求之间的间隔不能超过两分钟）。 在尝试确定设备位置时， `LocationRequest`带保险丝位置提供商将使用作为指导的位置提供程序：

    ```csharp
    LocationRequest locationRequest = new LocationRequest()
                                      .SetPriority(LocationRequest.PriorityHighAccuracy)
                                      .SetInterval(60 * 1000 * 5)
                                      .SetFastestInterval(60 * 1000 * 2);
    ```

- **`Android.Gms.Location.LocationCallback`** 为了接收位置更新，Xamarin Android 应用程序必须将抽象类作为`LocationProvider`子类。 &ndash; 此类公开了两个方法，这些方法可能由带保险丝的位置提供程序调用，用位置信息更新应用程序。 下面将更详细地讨论这一内容。

为了向 Xamarin 应用程序通知位置更新，带保险丝的位置提供程序将调用`LocationCallBack.OnLocationResult(LocationResult result)`。 `Android.Gms.Location.LocationResult`参数将包含更新位置信息。

当已保险丝的位置提供程序检测到位置数据的可用性变化时，它将调用`LocationProvider.OnLocationAvailability(LocationAvailability
locationAvailability)`方法。 如果该`LocationAvailability.IsLocationAvailable`属性返回`true`，则假定报告的`OnLocationResult`设备位置结果应为正确的，并且是所需`LocationRequest`的最新的。 如果`IsLocationAvailable`为 false，则不会`OnLocationResult`返回任何位置结果。

此代码片段是`LocationCallback`对象的一个示例实现：

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

## <a name="using-the-android-location-service-api"></a>使用 Android 定位服务 API

Android 定位服务是一种使用 Android 上位置信息的旧版 API。 位置数据由硬件传感器收集和收集，系统服务在应用程序中使用`LocationManager`类`ILocationListener`和进行访问。

位置服务最适用于必须在未安装 Google Play Services 的设备上运行的应用程序。

位置服务是由系统管理的一种特殊类型的[服务](https://developer.android.com/guide/components/services.html)。 系统服务与设备硬件进行交互，并且始终运行。 若要在应用程序中点击 "位置更新"，我们将使用`LocationManager` `RequestLocationUpdates`和调用订阅来自系统位置服务的位置更新。

使用 Android 定位服务获取用户的位置涉及几个步骤：

1. 获取对`LocationManager`服务的引用。
2. 当位置发生更改时，实现接口并处理事件。`ILocationListener`
3. `LocationManager`使用请求指定提供程序的位置更新。 上`ILocationListener`一步中的将用于接收来自的`LocationManager`回调。
4. 当应用程序不再适合接收更新时，停止位置更新。

### <a name="location-manager"></a>位置管理器

可以使用`LocationManager`类的实例访问系统位置服务。 `LocationManager`是一个特殊类，它允许我们与系统位置服务交互，并对其调用方法。 应用程序可以通过调用`LocationManager` `GetSystemService`并传入服务类型来获取对的引用，如下所示：

```csharp
LocationManager locationManager = (LocationManager) GetSystemService(Context.LocationService);
```

`OnCreate`是获取对的`LocationManager`引用的好地方。
最好将`LocationManager`作为类变量保留，以便我们可以在活动生命周期中的不同时间点调用它。

### <a name="request-location-updates-from-the-locationmanager"></a>从 LocationManager 请求位置更新

当应用程序具有对的`LocationManager`引用后，它需要`LocationManager`告知所需的位置信息类型以及该信息的更新频率。 为此，请`RequestLocationUpdates` `LocationManager`对对象调用，并传入一些更新条件和将接收位置更新的回调。 此回调是必须实现`ILocationListener`接口的类型（本指南稍后将对此进行详细介绍）。

`RequestLocationUpdates`方法告诉系统位置服务应用程序想要开始接收位置更新。 此方法允许您指定提供程序以及时间和距离阈值来控制更新频率。 例如，下面的方法每隔2000毫秒请求来自 GPS 位置提供程序的位置更新，并且仅当位置更改超过1个 metre 时：

```csharp
// For this example, this method is part of a class that implements ILocationListener, described below
locationManager.RequestLocationUpdates(LocationManager.GpsProvider, 2000, 1, this);
```

应用程序应该请求仅在应用程序正常运行所需的位置更新。 这会保留电池寿命，并为用户创建更好的体验。

### <a name="responding-to-updates-from-the-locationmanager"></a>响应 LocationManager 的更新

在`LocationManager`应用程序从请求更新后，它可以通过[`ILocationListener`](xref:Android.Locations.ILocationListener)实现接口来接收来自服务的信息。 此接口提供了四种侦听位置服务和位置提供程序`OnLocationChanged`的方法。 当用户的位置`OnLocationChanged`发生更改时，系统将调用，以根据请求位置更新时所设置的条件，将其限定为位置更改。 

下面的代码演示了`ILocationListener`接口中的方法：

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

### <a name="unsubscribing-to-locationmanager-updates"></a>取消订阅 LocationManager 更新

为了节省系统资源，应用程序应该尽快取消订阅位置更新。 方法指示停止将`LocationManager`更新发送到我们的应用程序。 `RemoveUpdates`  例如，活动可能`RemoveUpdates` `OnPause`在方法中调用，以便在应用程序的活动不在屏幕上时，如果应用程序不需要位置更新，则可以节省电能：

```csharp
protected override void OnPause ()
{
    base.OnPause ();
    locationManager.RemoveUpdates (this);
}
```

如果应用程序需要在后台获取位置更新，则需要创建订阅系统位置服务的自定义服务。 有关详细信息，请参阅[后台处理 With Android 服务](~/android/app-fundamentals/services/index.md)指南。

### <a name="determining-the-best-location-provider-for-the-locationmanager"></a>确定 LocationManager 的最佳位置提供程序

以上应用程序将 GPS 设置为位置提供程序。 但是，GPS 在所有情况下都可能不可用，例如，如果设备为室内或没有 GPS 接收方。 如果是这种情况，则结果是`null`提供程序的返回。

在应用程序启动时，若要使应用程序正常工作，请`GetBestProvider`使用方法在应用程序启动时要求提供最佳的（支持设备和用户的）位置提供程序。 可以不传入特定提供程序，而是告诉`GetBestProvider`提供程序的要求，例如[ `Criteria`对象](xref:Android.Locations.Criteria)的准确性和强大功能。 `GetBestProvider`返回给定条件的最佳提供程序。

下面的代码演示如何获取最佳的可用提供程序，并在请求位置更新时使用它：

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
> 如果用户禁用了所有位置提供程序， `GetBestProvider`则将`null`返回。 若要查看此代码如何在实际设备上运行，请确保在 Google Settings 下启用 GPS、Wi-fi 和移动网络， **> 位置 > 模式**，如以下屏幕截图所示：
>
> [![Android 手机上的设置位置模式屏幕](location-images/location-02.png)](location-images/location-02.png#lightbox)
>
> 下面的屏幕截图演示了使用`GetBestProvider`运行的应用程序的位置：
>
> [![显示纬度、经度和提供程序的 GetBestProvider 应用](location-images/location-03.png)](location-images/location-03.png#lightbox)
>
> 请记住， `GetBestProvider`不会动态更改提供程序。 相反，它会在活动生命周期内确定最佳的可用提供程序。 如果在设置提供程序状态后，提供程序状态发生更改，则该应用程序将`ILocationListener`要求&ndash;在`OnProviderDisabled`方法`OnProviderEnabled`、 `OnStatusChanged`和&ndash;中提供附加代码，以处理与提供程序开关。

## <a name="summary"></a>总结

本指南介绍了如何使用 Android 位置服务和来自 Google 位置服务 API 的带保险丝位置提供程序获取用户的位置。

## <a name="related-links"></a>相关链接

- [位置（示例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/location)
- [FusedLocationProvider （示例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/fusedlocationprovider)
- [Google Play Services](https://developer.android.com/google/play-services/index.html)
- [条件类](xref:Android.Locations.Criteria)
- [LocationManager 类](xref:Android.Locations.LocationManager)
- [LocationListener 类](xref:Android.Locations.ILocationListener)
- [LocationClient API](https://developer.android.com/reference/com/google/android/gms/location/LocationClient.html)
- [LocationListener API](https://developer.android.com/reference/com/google/android/gms/location/LocationListener.html)
- [LocationRequest API](https://developer.android.com/reference/com/google/android/gms/location/LocationRequest.html)
