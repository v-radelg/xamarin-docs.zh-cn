---
title: Xamarin.Essentials:检测抖动
description: Xamarin.Essentials 中的 Accelerometer 类可以检测设备的抖动移动。
ms.assetid: 07513D32-120F-4F12-8757-A47802A8027B
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: 2a14d739806fef353472a5186a9dbedd8e218662
ms.sourcegitcommit: 3489c281c9eb5ada2cddf32d73370943342a1082
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "59019303"
---
# <a name="xamarinessentials-detect-shake"></a>Xamarin.Essentials:检测抖动

[Accelerometer](accelerometer.md) 类可用于监视设备的加速计传感器，指示设备在三维空间内的加速度。 此外，该类还能够在用户抖动设备时注册事件。

## <a name="get-started"></a>入门

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-detect-shake"></a>使用检测抖动

在你的类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

若要检测设备的抖动，必须通过调用 `Start` 和 `Stop` 方法来使用 Accelerometer 功能，以侦听加速度的变化并检测抖动。 每次检测到抖动时，都会触发 `ShakeDetected ` 事件。 建议对 `SensorSpeed` 使用 `Game` 或更快的速度。 示例用法如下：

```csharp

public class DetectShakeTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.Game;

    public DetectShakeTest()
    {
        // Register for reading changes, be sure to unsubscribe when finished
        Accelerometer.ShakeDetected  += Accelerometer_ShakeDetected ;
    }

    void Accelerometer_ShakeDetected (object sender, EventArgs e)
    {
        // Process shake event
    }

    public void ToggleAccelerometer()
    {
        try
        {
            if (Accelerometer.IsMonitoring)
              Accelerometer.Stop();
            else
              Accelerometer.Start(speed);
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

[!include[](~/essentials/includes/sensor-speed.md)]

## <a name="implementation-details"></a>实现详细信息

检测抖动 API 使用来自加速计的原始读数来计算加速度。 它使用一种简单的队列机制来检测最近加速计事件的 75% 是否发生在最后半秒。 通过将加速计的 X、Y 和 Z 读数的平方相加并将其与特定的阈值进行比较来计算加速度。

## <a name="api"></a>API

- [Accelerometer 源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Accelerometer)
- [Accelerometer API 文档](xref:Xamarin.Essentials.Accelerometer)
