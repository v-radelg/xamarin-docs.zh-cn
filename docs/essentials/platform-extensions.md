---
title: Xamarin.Essentials 平台扩展
description: 必须使用矩形、大小和点等平台类型时，Xamarin.Essentials 提供了几种平台扩展方法。
ms.assetid: AB4D198A-4FD7-479E-8627-01F887A6D056
author: jamesmontemagno
ms.author: jamont
ms.date: 03/13/2019
ms.openlocfilehash: 740f6f4658c3e840f73261f554eb330ee02edb5d
ms.sourcegitcommit: 5d4e6677224971e2bc0268f405d192d0358c74b8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/21/2019
ms.locfileid: "58329060"
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
- [点转换器 API 文档](xref:Xamarin.Essentials.PointExtensions)
- [矩形转换器 API 文档](xref:Xamarin.Essentials.RectangleExtensions)
- [大小转换器 API 文档](xref:Xamarin.Essentials.SizeExtensions)
