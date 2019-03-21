---
title: Xamarin.Essentials 平台扩展
description: 必须使用矩形、大小和点等平台类型时，Xamarin.Essentials 提供了几种平台扩展方法。
ms.assetid: AB4D198A-4FD7-479E-8627-01F887A6D056
author: jamesmontemagno
ms.author: jamont
ms.date: 03/13/2019
ms.openlocfilehash: 806fcb3fa90a5ec9d39cecb743b72116b8388a03
ms.sourcegitcommit: 64d6da88bb6ba222ab2decd2fdc8e95d377438a6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "58176014"
---
# <a name="xamarinessentials-platform-extensions"></a>Xamarin.Essentials:平台扩展

必须使用矩形、大小和点等平台类型时，Xamarin.Essentials 提供了几种平台扩展方法。 这意味着可以针对其 iOS、Android 和 UWP 特定类型在这些类型的 `System` 版本之间进行转换。 

## <a name="get-started"></a>入门

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-platform-extensions"></a>使用平台扩展

在你的类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

所有平台扩展只能从 iOS、Android 或 UWP 项目中调用。

### <a name="point"></a>点

```csharp
var system = new System.Drawing.Point(x, y);

// Convert to CoreGraphics.CGPoint, Android.Graphics.Point, and Windows.Foundation.Point
var platform = system.ToPlatformSize();

// Back to System.Drawing.Size
var system2 = platform.ToSystemSize();
```

### <a name="size"></a>大小

```csharp
var system = new System.Drawing.Size(width, height);

// Convert to CoreGraphics.CGSize, Android.Util.Size, and Windows.Foundation.Size
var platform = system.ToPlatformSize();

// Back to System.Drawing.Size
var system2 = platform.ToSystemSize();
```

### <a name="rectangle"></a>矩形

```csharp
var system = new System.Drawing.Rectangle(x, y, width, height);

// Convert to CoreGraphics.CGRect, Android.Graphics.Rect, and Windows.Foundation.Rect
var platform = system.ToPlatformSize();

// Back to System.Drawing.Size
var system2 = platform.ToSystemSize();
```

## <a name="api"></a>API

- [转换器源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Types/PlatformExtensions)
- [点转换器 API 文档](xref:Xamarin.Essentials.PointConverters)
- [矩形转换器 API 文档](xref:Xamarin.Essentials.RectangleConverters)
- [大小转换器 API 文档](xref:Xamarin.Essentials.SizeConverters)
