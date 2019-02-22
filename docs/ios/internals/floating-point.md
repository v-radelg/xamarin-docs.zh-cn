---
title: Xamarin.iOS 中的浮点运算
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
# <a name="floating-point-operations-in-xamarinios"></a>Xamarin.iOS 中的浮点运算

默认情况下，Xamarin.iOS 将在 ARM 上执行使用 64 位精度的 32 位和 64 位浮点运算。  

虽然这种更高的精度更接近开发人员对桌面 C# 浮点运算的期望，但在移动设备上，对性能的影响可能会很大。

就可以编译 32-bit 浮动点代码以使用 32 位浮点运算。  若要执行此操作，您需要至少使用 Xamarin.iOS 8.10 和组在 iOS 上生成选项的面板"mtouch 多余的参数"条目行以下值：

     --aot-options=-O=float32

这将通知静态编译器（Mono 的内置静态编译器或 LLVM 驱动的静态编译器）使用 32 位浮点数执行浮点运算。
