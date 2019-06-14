---
title: 检查电池状态
description: 本文介绍如何使用 Xamarin.Forms DependencyService 类访问每个平台的电池信息。
ms.prod: xamarin
ms.assetid: CF1C5A73-84ED-407D-BDC5-EB1D83D2D3DB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/09/2016
ms.openlocfilehash: 2c9fae211e6a88944c94ca265409865b3252a10a
ms.sourcegitcommit: d3f48bfe72bfe03aca247d47bc64bfbfad1d8071
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2019
ms.locfileid: "66740934"
---
# <a name="checking-battery-status"></a>检查电池状态

[![下载示例](~/media/shared/download.png) 下载示例](https://developer.xamarin.com/samples/xamarin-forms/DependencyService/)

本文介绍如何创建检查电池状态的应用程序。 本文基于 James Montemagno 所著的《电池插件》。 有关详细信息，请参阅 [GitHub 存储库](https://github.com/jamesmontemagno/Xamarin.Plugins/tree/master/Battery)。

由于 Xamarin.Forms 不包含检查当前电池状态的功能，因此该应用程序将需要使用 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 才能利用本机 API。  本文将介绍使用 `DependencyService` 的以下步骤：

- **[创建接口](#Creating_the_Interface)** &ndash; 了解如何在共享代码中创建接口。
- **[iOS 实现](#iOS_Implementation)** &ndash; 了解如何在 iOS 本机代码中实现接口。
- **[Android 实现](#Android_Implementation)** &ndash; 了解如何在 Android 本机代码中实现接口。
- **[通用 Windows 平台实现](#UWPImplementation)** &ndash; 了解如何在通用 Windows 平台 (UWP) 的本机代码中实现接口。
- **[在共享代码中实现](#Implementing_in_Shared_Code)** &ndash; 了解如何使用 `DependencyService` 从共享代码调用本机实现。

完成后，使用 `DependencyService` 的应用程序将具有以下结构：

![](battery-info-images/battery-diagram.png "DependencyService 应用程序结构")

<a name="Creating_the_Interface" />

## <a name="creating-the-interface"></a>创建界面

首先，在表示所需功能的共享代码中创建接口。 如果是电池检测应用程序，则相关信息为剩余电量的百分比、设备是否充电以及设备如何接收电源：

```csharp
namespace DependencyServiceSample
{
  public enum BatteryStatus
  {
    Charging,
    Discharging,
    Full,
    NotCharging,
    Unknown
  }

  public enum PowerSource
  {
    Battery,
    Ac,
    Usb,
    Wireless,
    Other
  }

  public interface IBattery
  {
    int RemainingChargePercent { get; }
    BatteryStatus Status { get; }
    PowerSource PowerSource { get; }
  }
}
```

在共享代码中对这个接口进行编码将允许 Xamarin.Forms 应用程序访问每个平台上的电源管理 API。

> [!NOTE]
> 实现接口的类必须具有无参数的构造函数才能使用 `DependencyService`。 构造函数不能由接口定义。

<a name="iOS_Implementation" />

## <a name="ios-implementation"></a>iOS 实现

`IBattery` 接口必须在每个特定于平台的应用程序项目中实现。 iOS 实现将使用本机 [`UIDevice`](xref:UIKit.UIDevice) API 访问电池信息。 请注意，以下类有一个无参数的构造函数，以便 `DependencyService` 可以创建新的实例：

```csharp
using UIKit;
using Foundation;
using DependencyServiceSample.iOS;

namespace DependencyServiceSample.iOS
{
  public class BatteryImplementation : IBattery
  {
    public BatteryImplementation()
    {
      UIDevice.CurrentDevice.BatteryMonitoringEnabled = true;
    }

    public int RemainingChargePercent
    {
      get
      {
        return (int)(UIDevice.CurrentDevice.BatteryLevel * 100F);
      }
    }

    public BatteryStatus Status
    {
      get
      {
        switch (UIDevice.CurrentDevice.BatteryState)
        {
          case UIDeviceBatteryState.Charging:
            return BatteryStatus.Charging;
          case UIDeviceBatteryState.Full:
            return BatteryStatus.Full;
          case UIDeviceBatteryState.Unplugged:
            return BatteryStatus.Discharging;
          default:
            return BatteryStatus.Unknown;
        }
      }
    }

    public PowerSource PowerSource
    {
      get
      {
        switch (UIDevice.CurrentDevice.BatteryState)
        {
          case UIDeviceBatteryState.Charging:
            return PowerSource.Ac;
          case UIDeviceBatteryState.Full:
            return PowerSource.Ac;
          case UIDeviceBatteryState.Unplugged:
            return PowerSource.Battery;
          default:
            return PowerSource.Other;
        }
      }
    }
  }
}
```

最后，将该 `[assembly]` 属性添加到类的上面（以及任何已经定义的名称空间的外面），包括任何必需的 `using` 语句：

```csharp
using UIKit;
using Foundation;
using DependencyServiceSample.iOS;//necessary for registration outside of namespace

[assembly: Xamarin.Forms.Dependency (typeof (BatteryImplementation))]
namespace DependencyServiceSample.iOS
{
  public class BatteryImplementation : IBattery {
    ...
```

此属性将该类注册为 `IBattery` 接口的实现，这意味着可以在共享代码中使用 `DependencyService.Get<IBattery>` 来创建它的实例：

<a name="Android_Implementation" />

## <a name="android-implementation"></a>Android 实现

Android 实现使用 [`Android.OS.BatteryManager`](https://developer.xamarin.com/api/type/Android.OS.BatteryManager/) API。 该实现比 iOS 版本的实现更复杂，要求检查以处理电池权限不足的问题：

```csharp
using System;
using Android;
using Android.Content;
using Android.App;
using Android.OS;
using BatteryStatus = Android.OS.BatteryStatus;
using DependencyServiceSample.Droid;

namespace DependencyServiceSample.Droid
{
  public class BatteryImplementation : IBattery
  {
    private BatteryBroadcastReceiver batteryReceiver;
    public BatteryImplementation() { }

    public int RemainingChargePercent
    {
      get
      {
        try
        {
          using (var filter = new IntentFilter(Intent.ActionBatteryChanged))
          {
            using (var battery = Application.Context.RegisterReceiver(null, filter))
            {
              var level = battery.GetIntExtra(BatteryManager.ExtraLevel, -1);
              var scale = battery.GetIntExtra(BatteryManager.ExtraScale, -1);

              return (int)Math.Floor(level * 100D / scale);
            }
          }
        }
        catch
        {
          System.Diagnostics.Debug.WriteLine ("Ensure you have android.permission.BATTERY_STATS");
          throw;
        }

      }
    }

    public DependencyServiceSample.BatteryStatus Status
    {
      get
      {
        try
        {
          using (var filter = new IntentFilter(Intent.ActionBatteryChanged))
          {
            using (var battery = Application.Context.RegisterReceiver(null, filter))
            {
              int status = battery.GetIntExtra(BatteryManager.ExtraStatus, -1);
              var isCharging = status == (int)BatteryStatus.Charging || status == (int)BatteryStatus.Full;

              var chargePlug = battery.GetIntExtra(BatteryManager.ExtraPlugged, -1);
              var usbCharge = chargePlug == (int)BatteryPlugged.Usb;
              var acCharge = chargePlug == (int)BatteryPlugged.Ac;
              bool wirelessCharge = false;
              wirelessCharge = chargePlug == (int)BatteryPlugged.Wireless;

              isCharging = (usbCharge || acCharge || wirelessCharge);
              if (isCharging)
                return DependencyServiceSample.BatteryStatus.Charging;

              switch(status)
              {
                case (int)BatteryStatus.Charging:
                  return DependencyServiceSample.BatteryStatus.Charging;
                case (int)BatteryStatus.Discharging:
                  return DependencyServiceSample.BatteryStatus.Discharging;
                case (int)BatteryStatus.Full:
                  return DependencyServiceSample.BatteryStatus.Full;
                case (int)BatteryStatus.NotCharging:
                  return DependencyServiceSample.BatteryStatus.NotCharging;
                default:
                  return DependencyServiceSample.BatteryStatus.Unknown;
              }
            }
          }
        }
        catch
        {
          System.Diagnostics.Debug.WriteLine ("Ensure you have android.permission.BATTERY_STATS");
          throw;
        }
      }
    }

    public PowerSource PowerSource
    {
      get
      {
        try
        {
          using (var filter = new IntentFilter(Intent.ActionBatteryChanged))
          {
            using (var battery = Application.Context.RegisterReceiver(null, filter))
            {
              int status = battery.GetIntExtra(BatteryManager.ExtraStatus, -1);
              var isCharging = status == (int)BatteryStatus.Charging || status == (int)BatteryStatus.Full;

              var chargePlug = battery.GetIntExtra(BatteryManager.ExtraPlugged, -1);
              var usbCharge = chargePlug == (int)BatteryPlugged.Usb;
              var acCharge = chargePlug == (int)BatteryPlugged.Ac;

              bool wirelessCharge = false;
              wirelessCharge = chargePlug == (int)BatteryPlugged.Wireless;

              isCharging = (usbCharge || acCharge || wirelessCharge);

              if (!isCharging)
                return DependencyServiceSample.PowerSource.Battery;
              else if (usbCharge)
                return DependencyServiceSample.PowerSource.Usb;
              else if (acCharge)
                return DependencyServiceSample.PowerSource.Ac;
              else if (wirelessCharge)
                return DependencyServiceSample.PowerSource.Wireless;
              else
                return DependencyServiceSample.PowerSource.Other;
            }
          }
        }
        catch
        {
          System.Diagnostics.Debug.WriteLine ("Ensure you have android.permission.BATTERY_STATS");
          throw;
        }
      }
    }
  }
}
```

将该 `[assembly]` 属性添加到类的上面（以及任何已经定义的名称空间的外面），包括任何必需的 `using` 语句：

```csharp
...
using BatteryStatus = Android.OS.BatteryStatus;
using DependencyServiceSample.Droid; //enables registration outside of namespace

[assembly: Xamarin.Forms.Dependency (typeof (BatteryImplementation))]
namespace DependencyServiceSample.Droid
{
  public class BatteryImplementation : IBattery {
    ...
```

此属性将该类注册为 `IBattery` 接口的实现，这意味着可以在共享代码中使用 `DependencyService.Get<IBattery>` 来创建它的实例。

<a name="UWPImplementation" />

## <a name="universal-windows-platform-implementation"></a>通用 Windows 平台实现

UWP 实现使用 `Windows.Devices.Power` API 获取电池状态信息：

```csharp
using DependencyServiceSample.UWP;
using Xamarin.Forms;

[assembly: Dependency(typeof(BatteryImplementation))]
namespace DependencyServiceSample.UWP
{
    public class BatteryImplementation : IBattery
    {
        private BatteryStatus status = BatteryStatus.Unknown;
        Windows.Devices.Power.Battery battery;

        public BatteryImplementation()
        {
        }

        private Windows.Devices.Power.Battery DefaultBattery
        {
            get
            {
                return battery ?? (battery = Windows.Devices.Power.Battery.AggregateBattery);
            }
        }

        public int RemainingChargePercent
        {
            get
            {
                var finalReport = DefaultBattery.GetReport();
                var finalPercent = -1;

                if (finalReport.RemainingCapacityInMilliwattHours.HasValue && finalReport.FullChargeCapacityInMilliwattHours.HasValue)
                {
                    finalPercent = (int)((finalReport.RemainingCapacityInMilliwattHours.Value /
                        (double)finalReport.FullChargeCapacityInMilliwattHours.Value) * 100);
                }
                return finalPercent;
            }
        }

        public BatteryStatus Status
        {
            get
            {
                var report = DefaultBattery.GetReport();
                var percentage = RemainingChargePercent;

                if (percentage >= 1.0)
                {
                    status = BatteryStatus.Full;
                }
                else if (percentage < 0)
                {
                    status = BatteryStatus.Unknown;
                }
                else
                {
                    switch (report.Status)
                    {
                        case Windows.System.Power.BatteryStatus.Charging:
                            status = BatteryStatus.Charging;
                            break;
                        case Windows.System.Power.BatteryStatus.Discharging:
                            status = BatteryStatus.Discharging;
                            break;
                        case Windows.System.Power.BatteryStatus.Idle:
                            status = BatteryStatus.NotCharging;
                            break;
                        case Windows.System.Power.BatteryStatus.NotPresent:
                            status = BatteryStatus.Unknown;
                            break;
                    }
                }
                return status;
            }
        }

        public PowerSource PowerSource
        {
            get
            {
                if (status == BatteryStatus.Full || status == BatteryStatus.Charging)
                {
                    return PowerSource.Ac;
                }
                return PowerSource.Battery;
            }
        }
    }
}
```

命名空间声明上面的 `[assembly]` 属性将该类注册为 `IBattery` 接口的实现，这意味着可以在共享代码中使用 `DependencyService.Get<IBattery>` 来创建它的实例。

<a name="Implementing_in_Shared_Code" />

## <a name="implementing-in-shared-code"></a>在共享代码中实现

为每个平台实现接口后，现在即可编写共享应用程序来利用该接口。 该应用程序将包含带有按钮的页面，点击该按钮时即可更新当前电池状态的文本。 它使用 `DependencyService` 来获取 `IBattery` 接口的实例。 在运行时，该实例将是特定于平台的实现，可以完全访问本机 SDK。

```csharp
public MainPage ()
{
    var button = new Button {
        Text = "Click for battery info",
        VerticalOptions = LayoutOptions.CenterAndExpand,
        HorizontalOptions = LayoutOptions.CenterAndExpand,
    };
    button.Clicked += (sender, e) => {
        var bat = DependencyService.Get<IBattery>();

        switch (bat.PowerSource){
          case PowerSource.Battery:
            button.Text = "Battery - ";
            break;
          case PowerSource.Ac:
            button.Text = "AC - ";
            break;
          case PowerSource.Usb:
            button.Text = "USB - ";
            break;
          case PowerSource.Wireless:
            button.Text = "Wireless - ";
            break;
          case PowerSource.Other:
          default:
            button.Text = "Other - ";
            break;
        }
        switch (bat.Status){
          case BatteryStatus.Charging:
            button.Text += "Charging";
            break;
          case BatteryStatus.Discharging:
            button.Text += "Discharging";
            break;
          case BatteryStatus.NotCharging:
            button.Text += "Not Charging";
            break;
          case BatteryStatus.Full:
            button.Text += "Full";
            break;
          case BatteryStatus.Unknown:
          default:
            button.Text += "Unknown";
            break;
        }
    };
    Content = button;
}
```

在 iOS、Android 或 UWP 上运行此应用程序并按下按钮将导致按钮文本更新，以反映设备当前的电源状态。

![](battery-info-images/battery.png "电池状态示例")


## <a name="related-links"></a>相关链接

- [DependencyService（示例）](https://developer.xamarin.com/samples/xamarin-forms/DependencyService/)
- [使用 DependencyService（示例）](https://developer.xamarin.com/samples/xamarin-forms/UsingDependencyService/)
- [Xamarin.Forms 示例](https://github.com/xamarin/xamarin-forms-samples)
