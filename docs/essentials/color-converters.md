---
title: Xamarin.Essentials 颜色转换器
description: Xamarin.Essentials 中的 ColorConverters 类提供了几种帮助程序方法和扩展方法，以使用 System.Drawing.Color。
ms.assetid: B10428D6-89E2-4714-A39F-7E6E626391B2
author: jamesmontemagno
ms.author: jamont
ms.date: 03/13/2019
ms.openlocfilehash: 13b8c4c109855402928d3923a5910e23f933dc65
ms.sourcegitcommit: 64d6da88bb6ba222ab2decd2fdc8e95d377438a6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "58176016"
---
# <a name="xamarinessentials-color-converters"></a>Xamarin.Essentials:颜色转换器

Xamarin.Essentials 中的 ColorConverters 类为 System.Drawing.Color 提供了几种帮助程序方法。

## <a name="get-started"></a>入门

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-color-converters"></a>使用颜色转换器

在你的类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

使用 `System.Drawing.Color` 时，可以使用 Xamarin.Forms 的内置转换器从 Hsl、Hex 或 UInt 创建颜色。

```csharp
var blueHex = ColorConverters.FromHex("#3498db");
var blueHsl = ColorConverters.FromHsl(204, 70, 53);
var blueUInt = ColorConverers.FromUInt(3447003);
```

## <a name="using-color-extensions"></a>使用颜色扩展

通过 `System.Drawing.Color` 上的扩展方法可以应用不同的属性：

```csharp
var blue = ColorConverters.FromHex("#3498db");

// Multiplies the current alpha by 50%
var blueWithAlpha = blue.MultiplyAlpha(.5f);
```

还有其他几种扩展方法，包括：

* ToUInt
* MultiplyAlpha
* WithHue
* WithAlpha
* WithSaturation
* WithLuminosity


## <a name="using-platform-extensions"></a>使用平台扩展

此外，可以将 System.Drawing.Color 转换为平台特定的颜色结构。 这些方法只能从 iOS、Android 和 UWP 项目中调用。

```csharp
var system = System.Drawing.Color.FromArgb(255, 52, 152, 219);
 
// Extension to convert to Android.Graphics.Color, UIKit.UIColor, or Windows.UI.Color
var platform = system.ToPlatformColor();
```


```csharp
var platform = new Android.Graphics.Color(52, 152, 219, 255);
 
// Back to System.Drawing.Color
var system = platform.ToSystemColor();
```

`ToSystemColor` 方法适用于 Android.Graphics.Color、UIKit.UIColor 和 Windows.UI.Color。


## <a name="api"></a>API

- [颜色转换器源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Types/ColorConverters.shared.cs)
- [颜色转换器 API 文档](xref:Xamarin.Essentials.ColorConverters)
- [颜色扩展源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Types/ColorConverters.shared.cs)
- [颜色扩展 API 文档](xref:Xamarin.Essentials.ColorExtensions)
