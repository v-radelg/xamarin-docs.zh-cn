---
title: 示例集成
description: 本文档链接到演示 Xamarin Workbooks 集成的示例。 链接示例使用表示形式呈现和 SkiaSharp。
ms.prod: xamarin
ms.assetid: 327DAD2E-1F76-4EB5-BCD0-9E7384D99E48
author: conceptdev
ms.author: crdun
ms.date: 03/30/2017
ms.openlocfilehash: fbe471aa7f08d85a870d68505cf2c983b7e442e9
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292811"
---
# <a name="sample-integrations"></a>示例集成

有关集成的工作示例，请参阅[厨房 Sink][KitchenSink]示例。 只需`KitchenSink.sln`在 Visual Studio for Mac 或 Visual Studio 中生成， `KitchenSink.workbook`然后打开。

[![厨房 Sink 集成屏幕快照](samples-images/kitchensinkintegrationscreenshot.png)](samples-images/kitchensinkintegrationscreenshot.png#lightbox)

厨房 Sink 示例演示了这两种概念集：

* 表示段演示了如何使用`RepresentationManager`内置表示形式来增强呈现。
* 对象及其关联的 JavaScript 呈现器演示如何`ISerializableObject`使用，而无需通过表示提供程序。 `Person`

另请参阅[SkiaSharp][skiasharp] ，了解如何使用 Xamarin Workbooks 提供的现有[表示形式](~/tools/workbooks/sdk/representations.md)来呈现其类型。

[KitchenSink]: https://github.com/xamarin/Workbooks/tree/master/SDK/Samples/KitchenSink
[skiasharp]: https://github.com/mono/SkiaSharp/tree/master/source/SkiaSharp.Workbooks
