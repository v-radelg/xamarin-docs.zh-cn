---
title: 在 Xamarin.iOS 中浮点运算
description: 本文档介绍 Xamarin.iOS 如何处理 32 位和 64 位精度浮点操作，并讨论了对性能的关联的影响。
ms.prod: xamarin
ms.assetid: 003F25C1-B430-4339-9C95-7DF527EBC699
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 11/25/2015
ms.openlocfilehash: 28002ddbab80445757be907b5d9f1cc02fdefd9d
ms.sourcegitcommit: ee626f215de02707b7a94ba1d0fa1d75b22ab84f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54879350"
---
# <a name="floating-point-operations-in-xamarinios"></a>在 Xamarin.iOS 中浮点运算

Xamarin.iOS 默认情况下将执行 32 位和 64 位浮点运算在 ARM 上使用 64 位精度。  

虽然此较高的精度是更接近于开发人员从浮点运算中的预期C#可显著性能影响在桌面上，在移动设备上。

就可以编译 32-bit 浮动点代码以使用 32 位浮点运算。  若要执行此操作，您需要至少使用 Xamarin.iOS 8.10 和组在 iOS 上生成选项的面板"mtouch 多余的参数"条目行以下值：

     --aot-options=-O=float32

这会通知静态编译器 （Mono 的内置静态编译器或提供 LLVM 支持一个） 来执行浮点运算使用 32 位浮点数。
