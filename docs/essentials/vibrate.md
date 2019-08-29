---
title: Xamarin.Essentials:振动
description: 本文档介绍 Xamarin.Essentials 中的 Vibration 类，此类使你能够在所需的时间内启动和停止振动功能。
ms.assetid: 7E8B24C4-2625-4DAE-A129-383542D34F1E
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: 2e4cf713f9ad7478c0d8e288fd3beff4b5015ef5
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70120119"
---
# <a name="xamarinessentials-vibration"></a>Xamarin.Essentials:振动

Vibration 类使你能够在所需的时间内启动和停止振动功能  。

## <a name="get-started"></a>入门

[!include[](~/essentials/includes/get-started.md)]

若要访问 Vibration 功能，需要以下特定于平台的设置  。

# <a name="androidtabandroid"></a>[Android](#tab/android)

需要具有 Vibrate 权限，并且必须在 Android 项目中进行配置。 可以通过以下方法添加此权限：

打开 Properties 文件夹下的 AssemblyInfo.cs 文件并添加   ：

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.Vibrate)]
```

或更新 Android 清单：

打开 Properties 文件夹下的 AndroidManifest.xml 文件，并在“manifest”节点内添加以下代码    。

```xml
<uses-permission android:name="android.permission.VIBRATE" />
```

或右键单击 Android 项目并打开项目的属性。 在“Android 清单”下找到“所需权限:”区域，然后选中“VIBRATE”权限    。 这样会自动更新 AndroidManifest.xml 文件  。

# <a name="iostabios"></a>[iOS](#tab/ios)

无需其他设置。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

无平台差异。

-----

## <a name="using-vibration"></a>使用 Vibration

在你的类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

可以通过设置所需的时间量或使用默认 500 毫秒来使用 Vibration 功能。

```csharp
try
{
    // Use default vibration length
    Vibration.Vibrate();

    // Or use specified time
    var duration = TimeSpan.FromSeconds(1);
    Vibration.Vibrate(duration);
}
catch (FeatureNotSupportedException ex)
{
    // Feature not supported on device
}
catch (Exception ex)
{
    // Other error has occurred.
}
```

可以使用 `Cancel` 方法来请求取消使用设备振动：

```csharp
try
{
    Vibration.Cancel();
}
catch (FeatureNotSupportedException ex)
{
    // Feature not supported on device
}
catch (Exception ex)
{
    // Other error has occurred.
}
```

## <a name="platform-differences"></a>平台差异

# <a name="androidtabandroid"></a>[Android](#tab/android)

无平台差异。

# <a name="iostabios"></a>[iOS](#tab/ios)

- 仅在设备设置为“响铃时振动”时振动。
- 振动时长始终为 500 毫秒。
- 无法取消振动。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

无平台差异。

-----

## <a name="api"></a>API

- [Vibration 源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Vibration)
- [Vibration API 文档](xref:Xamarin.Essentials.Vibration)
