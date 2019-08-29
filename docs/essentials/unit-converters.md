---
title: Xamarin.Essentials 单位转换器
description: Xamarin.Essentials 中的 UnitConverters 类提供了几种单元转换器，以在开发人员使用 Xamarin.Essentials 时为其提供帮助。
ms.assetid: 35DE2704-E730-4337-9476-66CD53376943
author: jamesmontemagno
ms.author: jamont
ms.date: 03/13/2019
ms.openlocfilehash: be560a156647274932265597ae5b83f22255d061
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70120139"
---
# <a name="xamarinessentials-unit-converters"></a>Xamarin.Essentials:单位转换器

UnitConverters 类提供了几种单元转换器，以在开发人员使用 Xamarin.Essentials 时为其提供帮助  。

## <a name="get-started"></a>入门

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-unit-converters"></a>使用单位转换器

在你的类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

通过使用 Xamarin.Essentials 中的静态 `UnitConverters` 类，可以使用所有单位转换器。 例如，可以轻松地将华氏度转换为摄氏度。

```csharp
var celcius = UnitConverters.FahrenheitToCelsius(32.0);
```

以下是可用转换的列表：

- FahrenheitToCelsius
- CelsiusToFahrenheit
- CelsiusToKelvin
- KelvinToCelsius
- MilesToMeters
- MilesToKilometers
- KilometersToMiles
- DegreesToRadians
- RadiansToDegrees
- DegreesPerSecondToRadiansPerSecond
- RadiansPerSecondToDegreesPerSecond
- DegreesPerSecondToHertz
- RadiansPerSecondToHertz
- HertzToDegreesPerSecond
- HertzToRadiansPerSecond
- KilopascalsToHectopascals
- HectopascalsToKilopascals
- KilopascalsToPascals
- HectopascalsToPascals
- AtmospheresToPascals
- PascalsToAtmospheres
- CoordinatesToMiles
- CoordinatesToKilometers

## <a name="api"></a>API

- [单位转换器源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Types/UnitConverters.shared.cs)
- [单位转换器 API 文档](xref:Xamarin.Essentials.UnitConverters)
