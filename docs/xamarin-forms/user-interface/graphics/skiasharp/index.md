---
title: Xamarin.Forms 中的 SkiaSharp Graphics
description: SkiaSharp 是.NET 和 C# 由 Google 产品中广泛使用的开放源代码 Skia 图形引擎提供支持的 2D 图形系统。 本指南介绍如何使用 SkiaSharp 的 Xamarin.Forms 应用程序中的 2D 图形。
ms.prod: xamarin
ms.assetid: 2C348BEA-81DF-4794-8857-EB1DFF5E11DB
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
ms.openlocfilehash: 9cd5a25c598a25500aee595439aeecd648d50526
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61291141"
---
# <a name="skiasharp-graphics-in-xamarinforms"></a>Xamarin.Forms 中的 SkiaSharp Graphics

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)

_用于二维图形 Xamarin.Forms 应用程序中使用 SkiaSharp_

SkiaSharp 是.NET 和 C# 由 Google 产品中广泛使用的开放源代码 Skia 图形引擎提供支持的 2D 图形系统。 可以在 Xamarin.Forms 应用程序中使用 SkiaSharp 绘制 2D 矢量图形、 位图和文本。 请参阅[2D 绘制](~/graphics-games/skiasharp/index.md)SkiaSharp 库有关的更多常规信息和一些其他教程的指南。

本指南假定你熟悉 Xamarin.Forms 编程。

> [!VIDEO https://channel9.msdn.com/Events/Xamarin/Xamarin-University-Presents-Webinar-Series/SkiaSharp-Graphics-for-XamarinForms/player]

**网络研讨会：适用于 Xamarin.Forms 的 SkiaSharp**

## <a name="skiasharp-preliminaries"></a>SkiaSharp 初步准备之后

SkiaSharp xamarin.forms 打包为 NuGet 包。 创建 Xamarin.Forms 解决方案在 Visual Studio 或 Visual Studio for Mac 后，可以使用 NuGet 包管理器来搜索**SkiaSharp.Views.Forms**包并将其添加到你的解决方案。 如果选中**引用**部分的每个项目添加 SkiaSharp 后，可以看到各种**SkiaSharp**库已添加到每个解决方案中的项目。

如果在 Xamarin.Forms 应用程序面向 iOS，使用项目属性页上以最小的部署目标更改为 iOS 8.0。

在使用 SkiaSharp 任何 C# 页面中，将想要包括`using`指令[ `SkiaSharp` ](xref:SkiaSharp)命名空间，其中包含所有 SkiaSharp 类、 结构和将在图形中使用的枚举编程。 您还应该`using`指令[ `SkiaSharp.Views.Forms` ](xref:SkiaSharp.Views.Forms)类特定于 Xamarin.Forms 的命名空间。 这是一个更小命名空间，其中最重要的类是[ `SKCanvasView` ](xref:SkiaSharp.Views.Forms.SKCanvasView)。 此类派生自 Xamarin.Forms`View`类，并承载 SkiaSharp 图形输出。

> [!IMPORTANT]
> `SkiaSharp.Views.Forms`命名空间还包含`SKGLView`派生的类`View`但使用 OpenGL 的呈现图形。 为了简单起见，本指南将限制到本身`SKCanvasView`，但使用`SKGLView`相反是非常相似。

## <a name="skiasharp-drawing-basicsbasicsindexmd"></a>[SkiaSharp 绘制基础知识](basics/index.md)

一些可以使用 SkiaSharp 绘制的最简单的图形图是圆、 椭圆和矩形。 在显示这些数字，你将了解 SkiaSharp 坐标、 大小和颜色。 文本和位图的显示是更复杂，但这些文章还介绍这些技术。

## <a name="skiasharp-lines-and-pathspathsindexmd"></a>[SkiaSharp 线和路径](paths/index.md)

图形路径是一系列相互连接的直线和曲线。 路径可以描边，填充，或两者。 本文包含线条图形，包括笔划结束和联接，并带短划线和点线，但缺乏曲线几何图形的停止点的多个方面。

## <a name="skiasharp-transformstransformsindexmd"></a>[SkiaSharp 转换](transforms/index.md)

转换允许图形对象统一转换、 缩放、 旋转或倾斜。 本文还介绍如何创建非仿射转换和将转换应用于路径使用标准的 3-3 转换矩阵。

## <a name="skiasharp-curves-and-pathscurvesindexmd"></a>[SkiaSharp 曲线和路径](curves/index.md)

路径的探索继续将曲线添加到路径对象，并利用功能强大的路径的其他功能。 您将看到如何简洁的文本字符串中指定完整路径、 如何使用路径的效果，以及如何深入探讨路径内部结构。

## <a name="skiasharp-bitmapsbitmapsindexmd"></a>[SkiaSharp 位图](bitmaps/index.md)

位图是位对应于显示设备的像素的矩形数组。 本系列文章演示如何加载、 保存、 显示、 创建、 上绘制、 进行动画处理，并访问 SkiaSharp 位图的位。

## <a name="skiasharp-effectseffectsindexmd"></a>[SkiaSharp 效果](effects/index.md)

效果是 alter 正常显示图形，包括线性和循环渐变、 位图平铺、 混合模式、 模糊和其他人的属性。

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （示例）](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
- [SkiaSharp 通过 Xamarin.Forms 网络研讨会 （视频）](https://channel9.msdn.com/Events/Xamarin/Xamarin-University-Presents-Webinar-Series/SkiaSharp-Graphics-for-XamarinForms)
