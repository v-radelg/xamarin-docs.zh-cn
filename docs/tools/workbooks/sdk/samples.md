---
title: 示例集成
description: 本文档链接到演示 Xamarin Workbooks 集成的示例。 链接示例使用表示形式呈现和 SkiaSharp。
ms.prod: xamarin
ms.assetid: 327DAD2E-1F76-4EB5-BCD0-9E7384D99E48
author: davidortinau
ms.author: daortin
ms.date: 03/30/2017
ms.openlocfilehash: fa66ee2b2b469900381e2d31dc5dec49eb42c4f6
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73018112"
---
# <a name="sample-integrations"></a>示例集成

有关集成的工作示例，请参阅[厨房 Sink][KitchenSink]示例。 只需在 Visual Studio for Mac 或 Visual Studio 中生成 `KitchenSink.sln`，然后打开 `KitchenSink.workbook`。

[![厨房 Sink 集成屏幕截图](samples-images/kitchensinkintegrationscreenshot.png)](samples-images/kitchensinkintegrationscreenshot.png#lightbox)

厨房 Sink 示例演示了这两种概念集：

* 表示段演示了如何使用 `RepresentationManager` 通过使用内置表示形式来增强呈现功能。
* `Person` 对象及其关联的 JavaScript 呈现器演示如何使用 `ISerializableObject`，而无需通过表示提供程序。

另请参阅[SkiaSharp][skiasharp] ，了解如何使用 Xamarin Workbooks 提供的现有[表示形式](~/tools/workbooks/sdk/representations.md)来呈现其类型。

[KitchenSink]: https://github.com/xamarin/Workbooks/tree/master/SDK/Samples/KitchenSink
[skiasharp]: https://github.com/mono/SkiaSharp/tree/master/source/SkiaSharp.Workbooks
