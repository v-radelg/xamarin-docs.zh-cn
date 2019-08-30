---
title: Xamarin.Essentials:OrientationSensor
description: OrientationSensor 类可让你监视设备在三维空间中的方向。
ms.assetid: F3091D93-E779-41BA-8696-23D296F2F6F5
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: 5423872da7966bc7e4bb88e278d76b709f114158
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70120717"
---
# <a name="xamarinessentials-orientationsensor"></a>Xamarin.Essentials:OrientationSensor

 OrientationSensor 类可让你监视设备在三维空间中的方向。

> [!NOTE]
> 此类用于确定设备在三维空间中的方向。 如果需要确定设备的视频显示器是处于纵向模式还是横向模式，请使用可从 [`DeviceDisplay`](device-display.md) 类获得的 `ScreenMetrics` 对象的 `Orientation` 属性。

## <a name="get-started"></a>入门

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-orientationsensor"></a>使用 OrientationSensor

在你的类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

通过调用 `Start` 方法启用 `OrientationSensor` 以便监视对设备方向所做的更改，并通过调用 `Stop` 方法进行禁用。 然后通过 `ReadingChanged` 事件反馈任何变化。 示例用法如下：

```csharp

public class OrientationSensorTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.UI;

    public OrientationSensorTest()
    {
        // Register for reading changes, be sure to unsubscribe when finished
        OrientationSensor.ReadingChanged += OrientationSensor_ReadingChanged;
    }

    void OrientationSensor_ReadingChanged(object sender, OrientationSensorChangedEventArgs e)
    {
        var data = e.Reading;
        Console.WriteLine($"Reading: X: {data.Orientation.X}, Y: {data.Orientation.Y}, Z: {data.Orientation.Z}, W: {data.Orientation.W}");
        // Process Orientation quaternion (X, Y, Z, and W)
    }

    public void ToggleOrientationSensor()
    {
        try
        {
            if (OrientationSensor.IsMonitoring)
                OrientationSensor.Stop();
            else
                OrientationSensor.Start(speed);
        }
        catch (FeatureNotSupportedException fnsEx)
        {
            // Feature not supported on device
        }
        catch (Exception ex)
        {
            // Other error has occurred.
        }
    }
}
```

`OrientationSensor` 读数以 [`Quaternion`](xref:System.Numerics.Quaternion) 的形式返回报告，描述了设备基于两个三维坐标系的方向：

设备（通常为手机或平板电脑）的三维坐标系具有以下轴：

- X 轴正方向指向显示器右侧（在纵向模式下）。
- Y 轴正方向指向显示器顶部（在纵向模式下）。
- Z 轴正方向指向屏幕外侧。

地球的三维坐标系具有以下轴：

- X 轴正方向与地球表面相切并指向东边。
- Y 轴正方向也与地球表面相切并指向北边。
- Z 轴正方向与地球表面垂直并指向上边。

`Quaternion` 描述了设备坐标系相对于地球坐标系的旋转。

`Quaternion` 值与绕轴旋转密切相关。 如果旋转的轴是规范化矢量 (a<sub>x</sub>, a<sub>y</sub>, a<sub>z</sub>)，并且旋转角度为 Θ，则四元数的 (X, Y, Z, W) 分量是：

(a<sub>x</sub>·sin(Θ/2), a<sub>y</sub>·sin(Θ/2), a<sub>z</sub>·sin(Θ/2), cos(Θ/2))

这些是右手坐标系，因此右手的拇指指向旋转轴的正方向，手指弯曲表示正角的旋转方向。

示例：

- 如果设备平躺在桌子上，使其屏幕面朝上，设备顶部（在纵向模式下）指向北边，则会对齐这两个坐标系。 `Quaternion` 值表示标识四元数 (0, 0, 0, 1)。 可以相对于此位置分析所有旋转。

- 如果设备平躺在桌子上，使其屏幕面朝上，设备顶部（在纵向模式下）指向西边，则 `Quaternion` 值为 (0, 0, 0.707, 0.707)。 设备已绕地球的 Z 轴旋转 90 度。

- 如果设备直立，设备顶部（在纵向模式下）指向天空，设备背面朝向北边，则设备已绕 X 轴旋转 90 度。 `Quaternion` 值为 (0.707, 0, 0, 0.707)。

- 如果设备的左边缘在桌子上，顶部指向北边，则设备已绕 Y 轴旋转 &ndash;90 度（或绕 Y 轴负方向旋转 90 度）。 `Quaternion` 值为 (0, -0.707, 0, 0.707)。

[!include[](~/essentials/includes/sensor-speed.md)]

## <a name="api"></a>API

- [OrientationSensor 源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/OrientationSensor)
- [OrientationSensor API 文档](xref:Xamarin.Essentials.OrientationSensor)
