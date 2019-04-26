---
title: 示例集成
description: 本文档所链接到演示 Xamarin Workbooks 集成的示例。 链接的示例适用于表示形式呈现和 SkiaSharp。
ms.prod: xamarin
ms.assetid: 327DAD2E-1F76-4EB5-BCD0-9E7384D99E48
author: lobrien
ms.author: laobri
ms.date: 03/30/2017
ms.openlocfilehash: e35577b116180d2745e2f6afb792547f63873214
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61424592"
---
# <a name="sample-integrations"></a>示例集成

请参阅[厨房接收器][ KitchenSink]有关工作示例的集成示例。 只需构建`KitchenSink.sln`在 Visual Studio for Mac 或 Visual Studio，然后打开`KitchenSink.workbook`。

[![厨房接收器集成屏幕快照](samples-images/kitchensinkintegrationscreenshot.png)](samples-images/kitchensinkintegrationscreenshot.png#lightbox)

厨房接收器示例演示了这两个集的概念：

* 表示部分演示如何使用`RepresentationManager`来增强通过使用内置的表示形式呈现。
* `Person`对象和其关联的 JavaScript 呈现器演示如何使用`ISerializableObject`而无需通过表示形式提供程序。

另请参阅[SkiaSharp] [ skiasharp]的示例使用的现有集成[表示形式](~/tools/workbooks/sdk/representations.md)提供的 Xamarin Workbooks 来呈现它的类型。

[KitchenSink]: https://github.com/xamarin/Workbooks/tree/master/SDK/Samples/KitchenSink
[skiasharp]: https://github.com/mono/SkiaSharp/tree/master/source/SkiaSharp.Workbooks
