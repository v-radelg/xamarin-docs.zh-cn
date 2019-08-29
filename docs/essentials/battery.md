---
title: Xamarin.Essentials:电池
description: 本文档介绍 Xamarin.Essentials 中的 Battery 类，此类使你能够查看设备的电池信息并监视更改。
ms.assetid: 47EB26D8-8C62-477B-A13C-6977F74E6E43
author: jamesmontemagno
ms.author: jamont
ms.date: 01/22/2019
ms.custom: video
ms.openlocfilehash: d5408894a9eda6b782f1f790ed8f1d0bb138a2f3
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70120157"
---
# <a name="xamarinessentials-battery"></a>Xamarin.Essentials:电池

Battery  类允许检查设备的电池信息、监视更改，并提供有关设备的节能模式状态的信息，该状态指示设备是否正在低功耗模式下运行。 如果设备的节能模式状态已打开，则应用程序应避免后台处理。

## <a name="get-started"></a>入门

[!include[](~/essentials/includes/get-started.md)]

若要访问 Battery 功能，需要以下特定于平台的设置  。

# <a name="androidtabandroid"></a>[Android](#tab/android)

需要具有 `Battery` 权限，并且必须在 Android 项目中进行配置。 可以通过以下方法添加此权限：

打开 Properties 文件夹下的 AssemblyInfo.cs 文件并添加   ：

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.BatteryStats)]
```

或更新 Android 清单：

打开 Properties 文件夹下的 AndroidManifest.xml 文件，并在“manifest”节点内添加以下代码    。

```xml
<uses-permission android:name="android.permission.BATTERY_STATS" />
```

或右键单击 Android 项目并打开项目的属性。 在“Android 清单”下找到“所需权限:”区域，然后选中“Battery”权限    。 这样会自动更新 AndroidManifest.xml 文件  。

# <a name="iostabios"></a>[iOS](#tab/ios)

无需其他设置。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

无需其他设置。

-----

## <a name="using-battery"></a>使用 Battery

在你的类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

查看当前的电池信息：

```csharp
var level = Battery.ChargeLevel; // returns 0.0 to 1.0 or 1.0 when on AC or no battery.

var state = Battery.State;

switch (state)
{
    case BatteryState.Charging:
        // Currently charging
        break;
    case BatteryState.Full:
        // Battery is full
        break;
    case BatteryState.Discharging:
    case BatteryState.NotCharging:
        // Currently discharging battery or not being charged
        break;
    case BatteryState.NotPresent:
        // Battery doesn't exist in device (desktop computer)
    case BatteryState.Unknown:
        // Unable to detect battery state
        break;
}

var source = Battery.PowerSource;

switch (source)
{
    case BatteryPowerSource.Battery:
        // Being powered by the battery
        break;
    case BatteryPowerSource.AC:
        // Being powered by A/C unit
        break;
    case BatteryPowerSource.Usb:
        // Being powered by USB cable
        break;
    case BatteryPowerSource.Wireless:
        // Powered via wireless charging
        break;
    case BatteryPowerSource.Unknown:
        // Unable to detect power source
        break;
}
```

每当电池的任一属性发生更改时，将触发一个事件：

```csharp
public class BatteryTest
{
    public BatteryTest()
    {
        // Register for battery changes, be sure to unsubscribe when needed
        Battery.BatteryInfoChanged += Battery_BatteryInfoChanged;
    }

    void Battery_BatteryInfoChanged(object sender, BatteryInfoChangedEventArgs   e)
    {
        var level = e.ChargeLevel;
        var state = e.State;
        var source = e.PowerSource;
        Console.WriteLine($"Reading: Level: {level}, State: {state}, Source: {source}");
    }
}
```

使用电池运行的设备可以置于低功耗节能模式。 有时，设备会自动切换到此模式，例如，当电池电量降到 20% 以下时。 操作系统通过减少往往会消耗电池的活动来响应节能模式。 打开节能模式时，应用程序有助于避免后台处理或其他高功率活动。

还可以使用静态 `Battery.EnergySaverStatus` 属性获取设备的当前节能状态：

```csharp
// Get energy saver status
var status = Battery.EnergySaverStatus;
```

此属性会返回 `EnergySaverStatus` 枚举的成员，可以是 `On`、`Off` 或 `Unknown`。 如果该属性返回 `On`，则应用程序应避免后台处理或可能会消耗大量电力的其他活动。

应用程序还应安装事件处理程序。  Battery 类会公开节能模式状态发生更改时触发的事件：

```csharp
public class EnergySaverTest
{
    public EnergySaverTest()
    {
        // Subscribe to changes of energy-saver status
        Battery.EnergySaverStatusChanged += OnEnergySaverStatusChanged;
    }

    private void OnEnergySaverStatusChanged(EnergySaverStatusChangedEventArgs e)
    {
        // Process change
        var status = e.EnergySaverStatus;
    }
}
```

如果节能模式状态更改为 `On`，则应用程序应停止执行后台处理。 如果状态更改为 `Unknown` 或 `Off`，则应用程序可以继续执行后台处理。


## <a name="platform-differences"></a>平台差异

# <a name="androidtabandroid"></a>[Android](#tab/android)

无平台差异。

# <a name="iostabios"></a>[iOS](#tab/ios)

- 必须使用设备来测试 API。 
- 将仅为 `PowerSource` 返回 `AC` 或 `Battery`。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

- 将仅为 `PowerSource` 返回 `AC` 或 `Battery`。

-----

## <a name="api"></a>API

- [Battery 源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Battery)
- [Battery API 文档](xref:Xamarin.Essentials.Battery)

## <a name="related-video"></a>相关视频

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Battery-Essential-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
