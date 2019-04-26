---
title: 演练-在 Xamarin.iOS 中的后台位置
description: 本文档提供了如何在 backgrounded Xamarin.iOS 应用程序中使用位置信息的演练。 它介绍了必要的安装程序、 用户界面和应用程序状态。
ms.prod: xamarin
ms.assetid: F8EEA0FD-5614-47FE-ADAC-80A5BCA6EB5F
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: fa8a48e165764a449af4bc5414d2e66aecea8269
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61392246"
---
# <a name="walkthrough---background-location-in-xamarinios"></a>演练-在 Xamarin.iOS 中的后台位置

在此示例中，我们将构建 iOS 打印有关我们当前的位置信息的位置应用程序： 纬度、 经度和屏幕的其他参数。 此应用程序将演示如何正确执行位置更新该应用程序时处于活动状态或 Backgrounded。

本演练说明了一些后台处理概念，包括作为背景需要应用程序注册的应用程序、 挂起更新 UI 时应用程序在后台运行，以及使用密钥`WillEnterBackground`并`WillEnterForeground``AppDelegate`方法.

## <a name="application-set-up"></a>设置应用程序


1. 首先，创建一个新**iOS > 应用程序 > 单视图应用程序 (C#)**。 调用它_位置_，并确保 iPad 和 iPhone 已选中。

1. 作为背景需要应用程序在 iOS 中限定位置应用程序。 注册应用程序作为位置的应用程序通过编辑**Info.plist**项目文件。

    在解决方案资源管理器，双击**Info.plist**文件来打开它，并滚动到列表的底部。 由已勾选**启用后台模式**并**位置更新**复选框。

    在 Visual Studio for Mac 中，它将类似如下：

    [![](location-walkthrough-images/image7.png "通过启用后台模式和位置更新复选框选中")](location-walkthrough-images/image7.png#lightbox)

    在 Visual Studio 中， **Info.plist**需要手动更新通过添加以下键/值对：

    ```xml
    <key>UIBackgroundModes</key>
    <array>
      <string>location</string>
    </array>
    ```

1. 现在，注册应用程序，它可以从设备获取位置数据。 在 iOS 中，`CLLocationManager`类用于访问位置信息，并可以引发提供位置更新的事件。

1. 在代码中，创建一个名为的新类`LocationManager`，提供各种屏幕和代码以订阅位置更新了一个位置。 在中`LocationManager`类中，使实例`CLLocationManager`调用`LocMgr`:

    ```csharp
    public class LocationManager
    {
        protected CLLocationManager locMgr;

        public LocationManager () {
            this.locMgr = new CLLocationManager();
            this.locMgr.PausesLocationUpdatesAutomatically = false;

            // iOS 8 has additional permissions requirements
            if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
                locMgr.RequestAlwaysAuthorization (); // works in background
                //locMgr.RequestWhenInUseAuthorization (); // only in foreground
            }

            if (UIDevice.CurrentDevice.CheckSystemVersion (9, 0)) {
                locMgr.AllowsBackgroundLocationUpdates = true;
            }
        }

        public CLLocationManager LocMgr {
            get { return this.locMgr; }
        }
    }
    ```

    上面的代码上设置的属性和权限数[CLLocationManager](xref:CoreLocation.CLLocationManager)类：

    - `PausesLocationUpdatesAutomatically` – 这是一个布尔值，可以根据是否允许系统暂停位置更新设置。 在某些设备上则默认为`true`，这可能导致设备停止获取位置的更新，大约 15 分钟后的背景。
    - `RequestAlwaysAuthorization` -你应传入此方法为应用程序用户提供的选项以允许要在背景中访问的位置。 `RequestWhenInUseAuthorization` 此外可以传递如果想要为用户提供选项以便仅当该应用位于前台时，才可访问的位置。
    - `AllowsBackgroundLocationUpdates` – 这是一个布尔值属性，在 iOS 9，可设置为允许接收位置更新挂起时的应用程序中引入。

    > [!IMPORTANT]
    > iOS 8 （和更高版本） 还要求中的条目**Info.plist**文件向用户显示授权请求的一部分。

1. 添加的键`NSLocationAlwaysUsageDescription`或`NSLocationWhenInUseUsageDescription`将向用户请求位置数据的访问权限的警报中显示的字符串。

1. iOS 9 需要使用时`AllowsBackgroundLocationUpdates` **Info.plist**包括密钥`UIBackgroundModes`值`location`。 如果您已完成本演练的步骤 2 中，这已应已在 Info.plist 文件中。


1. 内部`LocationManager`类中，创建一个名为方法`StartLocationUpdates`用下面的代码。 此代码显示了如何开始接收来自的位置更新`CLLocationManager`:

    ```csharp
    if (CLLocationManager.LocationServicesEnabled) {
        //set the desired accuracy, in meters
        LocMgr.DesiredAccuracy = 1;
        LocMgr.LocationsUpdated += (object sender, CLLocationsUpdatedEventArgs e) =>
        {
            // fire our custom Location Updated event
            LocationUpdated (this, new LocationUpdatedEventArgs (e.Locations [e.Locations.Length - 1]));
        };
        LocMgr.StartUpdatingLocation();
    }
    ```

    有几种重要方法，此方法中发生的情况。 首先，我们将执行检查以查看应用程序是否在设备上有权访问位置数据。 我们验证这是否通过调用`LocationServicesEnabled`上`CLLocationManager`。 此方法将返回**false**如果用户已拒绝应用程序访问位置信息。

1. 接下来，告知何种频率的位置管理器来更新。 `CLLocationManager` 提供了许多选项用于筛选和配置位置数据，包括更新的频率。 在此示例中，设置`DesiredAccuracy`更新位置发生更改的一个表，用时。 有关配置位置更新频率和其他首选项的详细信息，请参阅[CLLocationManager 类引用](https://developer.apple.com/library/ios/#documentation/CoreLocation/Reference/CLLocationManager_Class/CLLocationManager/CLLocationManager.html)Apple 文档中。

1. 最后，调用`StartUpdatingLocation`上`CLLocationManager`实例。 这将告知位置管理器来获取初始的修补程序上当前的位置，并开始发送更新

到目前为止，位置管理器创建后，配置了我们想要接收的数据类型，并已确定的初始位置。 现在该代码需要呈现用户界面的位置数据。 我们可以执行此操作与自定义事件采用`CLLocation`作为自变量：

```csharp
// event for the location changing
public event EventHandler<LocationUpdatedEventArgs>LocationUpdated = delegate { };
```

下一步将订阅从位置更新`CLLocationManager`，并引发自定义`LocationUpdated`新位置数据可用，传入作为自变量位置时的事件。 若要执行此操作，创建一个新类**LocationUpdateEventArgs.cs**。 此代码可以在主应用程序中访问，并引发事件时返回设备位置：

```csharp
public class LocationUpdatedEventArgs : EventArgs
{
    CLLocation location;

    public LocationUpdatedEventArgs(CLLocation location)
    {
       this.location = location;
    }

    public CLLocation Location
    {
       get { return location; }
    }
}
```

## <a name="user-interface"></a>用户界面

1. 使用 iOS 设计器生成的屏幕将显示位置的信息。 双击**Main.storyboard**文件以开始。

    在情节提要，将多个标签拖屏幕充当占位符的位置信息。 在此示例中，有纬度、 经度、 海拔高度、 课程和速度的标签。

    布局应如下所示：

    ![](location-walkthrough-images/image8.png "在 iOS 设计器布局示例 UI")

1. 在解决方案面板中，双击`ViewController.cs`文件，并编辑它以创建 LocationManager 和调用的新实例`StartLocationUpdates`上它。
  更改代码以如下所示：

    ```csharp
    #region Computed Properties
    public static bool UserInterfaceIdiomIsPhone {
                get { return UIDevice.CurrentDevice.UserInterfaceIdiom == UIUserInterfaceIdiom.Phone; }
            }

    public static LocationManager Manager { get; set;}
    #endregion

    #region Constructors
    public ViewController (IntPtr handle) : base (handle)
    {
    // As soon as the app is done launching, begin generating location updates in the location manager
        Manager = new LocationManager();
        Manager.StartLocationUpdates();
    }

    #endregion
    ```

    虽然会不显示任何数据，这会在应用程序启动时启动位置更新。

1. 现在，接收位置更新，则使用位置信息更新屏幕。 以下方法获取从位置我们`LocationUpdated`事件并将其显示在 UI 中：

    ```csharp
    #region Public Methods
    public void HandleLocationChanged (object sender, LocationUpdatedEventArgs e)
    {
        // Handle foreground updates
        CLLocation location = e.Location;

        LblAltitude.Text = location.Altitude + " meters";
        LblLongitude.Text = location.Coordinate.Longitude.ToString ();
        LblLatitude.Text = location.Coordinate.Latitude.ToString ();
        LblCourse.Text = location.Course.ToString ();
        LblSpeed.Text = location.Speed.ToString ();

        Console.WriteLine ("foreground updated");
    }
    #endregion
    ```

我们仍需订阅`LocationUpdated`事件中，我们 AppDelegate 中，并调用新方法来更新 UI。 将以下代码中的添加`ViewDidLoad,`之后`StartLocationUpdates`调用：

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // It is better to handle this with notifications, so that the UI updates
    // resume when the application re-enters the foreground!
    Manager.LocationUpdated += HandleLocationChanged;

}
```


现在，当运行该应用程序时，它应如下所示：

[![](location-walkthrough-images/image5.png "运行示例应用")](location-walkthrough-images/image5.png#lightbox)

## <a name="handling-active-and-background-states"></a>处理活动和背景的状态

1. 在前景中并处于活动状态时，应用程序将输出位置更新。 若要演示时应用程序进入后台，会发生什么情况，重写`AppDelegate`跟踪应用程序的方法状态更改，以便应用程序在前台和后台之间转换时写入控制台：

    ```csharp
    public override void DidEnterBackground (UIApplication application)
    {
        Console.WriteLine ("App entering background state.");
    }

    public override void WillEnterForeground (UIApplication application)
    {
        Console.WriteLine ("App will enter foreground");
    }
    ```

    中的以下代码添加`LocationManager`连续打印更新的位置到应用程序输出中，若要验证的位置信息的数据是在后台中仍然可用：

    ```csharp
    public class LocationManager
    {
        public LocationManager ()
        {
        ...
        LocationUpdated += PrintLocation;
        }
        ...

        //This will keep going in the background and the foreground
        public void PrintLocation (object sender, LocationUpdatedEventArgs e) {
        CLLocation location = e.Location;
        Console.WriteLine ("Altitude: " + location.Altitude + " meters");
        Console.WriteLine ("Longitude: " + location.Coordinate.Longitude);
        Console.WriteLine ("Latitude: " + location.Coordinate.Latitude);
        Console.WriteLine ("Course: " + location.Course);
        Console.WriteLine ("Speed: " + location.Speed);
        }
    }
    ```

1. 使用代码的一个剩余问题： 尝试更新用户界面，当应用在后台运行时将原因 iOS 将终止它。 当应用程序进入后台时，代码需要取消订阅位置更新并停止更新 UI。

    iOS 为我们提供了通知时在应用程序即将转换到另一个应用程序状态。 在这种情况下，我们可以订阅`ObserveDidEnterBackground`通知。

    以下代码段演示如何使用通知来让知道何时停止 UI 更新视图。 这将进入`ViewDidLoad`:

    ```csharp
    UIApplication.Notifications.ObserveDidEnterBackground ((sender, args) => {
        Manager.LocationUpdated -= HandleLocationChanged;
    });
    ```

    当运行该应用程序时，输出将如下所示：

    ![](location-walkthrough-images/image6.png "在控制台中的位置输出示例")

1. 应用程序在前台，操作时将打印到屏幕的位置更新，并继续在后台操作时打印到应用程序输出窗口的数据。

仍然只有一个未完成的问题： 当首次加载应用程序，但是它没有办法知道当应用重新进入前台时，屏幕启动 UI 更新。 如果 backgrounded 应用程序恢复到前台，不会恢复 UI 更新。

若要解决此问题，嵌套调用以启动在另一条通知，当应用程序进入活动状态时将触发 UI 更新：

```csharp
UIApplication.Notifications.ObserveDidBecomeActive ((sender, args) => {
  Manager.LocationUpdated += HandleLocationChanged;
});
```

现在 UI 将开始更新时首次启动应用程序，并且恢复更新应用程序的任何时间回来置于前台。

在本演练中，我们生成打印到屏幕和应用程序输出窗口的位置数据的良好、 识别背景的 iOS 应用程序。


## <a name="related-links"></a>相关链接

- [位置 (第 4) （示例）](https://developer.xamarin.com/samples/monotouch/Location/)
- [核心位置框架引用](https://developer.apple.com/library/ios/documentation/CoreLocation/Reference/CoreLocation_Framework/_index.html)
