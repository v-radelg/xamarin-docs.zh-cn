---
title: Xamarin。窗体地图位置和距离
description: Xamarin 命名空间包含一个位置结构，该结构通常在定位地图及其引脚时使用，还包含定位地图时可选择使用的距离结构。
ms.prod: xamarin
ms.assetid: 2F4EA3D2-1351-40AD-A71D-CF7F1F18F1E8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/30/2019
ms.openlocfilehash: a68eab7bb3e6da764f5a35af4461d6af2be50ebe
ms.sourcegitcommit: 3ea19e3a51515b30349d03c70a5b3acd7eca7fe7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/01/2019
ms.locfileid: "73426281"
---
# <a name="xamarinforms-map-position-and-distance"></a>Xamarin。窗体地图位置和距离

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

[`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps)命名空间包含一个[`Position`](xref:Xamarin.Forms.Maps.Position)结构，该结构通常在定位地图及其引脚时使用，还包含在定位地图时可选择使用的[`Distance`](xref:Xamarin.Forms.Maps.Distance)结构。

## <a name="position"></a>位置

[`Position`](xref:Xamarin.Forms.Maps.Position)结构封装了存储为纬度值和经度值的位置。 此结构定义了两个只读属性：

- `double`类型的[`Latitude`](xref:Xamarin.Forms.Maps.Position.Latitude)，它表示以小数表示的位置的纬度。
- `double`类型的[`Longitude`](xref:Xamarin.Forms.Maps.Position.Longitude)，它表示以小数表示的位置的经度。

[`Position`](xref:Xamarin.Forms.Maps.Position)对象是用 `Position` 构造函数创建的，该构造函数要求将纬度和经度参数指定为 `double` 值：

```csharp
Position position = new Position(36.9628066, -122.0194722);
```

> [!NOTE]
> 创建 `Position` 对象时，纬度值将限制-90.0 和90.0 之间，并且经度值将为限制-180.0 和180.0 之间。

## <a name="distance"></a>长途

[`Distance`](xref:Xamarin.Forms.Maps.Distance)结构封装作为 `double` 值存储的距离，表示以米为单位的距离。 此结构定义了三个只读属性：

- `double`类型的[`Kilometers`](xref:Xamarin.Forms.Maps.Distance.Kilometers)，它表示 `Distance`跨的距离（公里）。
- `double`类型的[`Meters`](xref:Xamarin.Forms.Maps.Distance.Meters)，它表示 `Distance`跨的距离（以米为单位）。
- `double`类型的[`Miles`](xref:Xamarin.Forms.Maps.Distance.Miles)，它表示 `Distance`跨的距离（以英里为单位）。

可以使用 `Distance` 构造函数创建[`Distance`](xref:Xamarin.Forms.Maps.Distance)对象，该构造函数需要指定为 `double`的计量参数：

```csharp
Distance distance = new Distance(1450.5);
```

或者，可以通过[`FromKilometers`](xref:Xamarin.Forms.Maps.Distance.FromKilometers*)、 [`FromMeters`](xref:Xamarin.Forms.Maps.Distance.FromMeters*)和[`FromMiles`](xref:Xamarin.Forms.Maps.Distance.FromMiles*)工厂方法创建[`Distance`](xref:Xamarin.Forms.Maps.Distance)对象：

```csharp
Distance distance1 = Distance.FromKilometers(1.45); // argument represents the number of kilometers
Distance distance2 = Distance.FromMeters(1450.5);   // argument represents the number of meters
Distance distance3 = Distance.FromMiles(0.969);     // argument represents the number of miles
```

## <a name="related-links"></a>相关链接

- [地图示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
