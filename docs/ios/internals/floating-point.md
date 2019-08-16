---
title: Xamarin.iOS 中的浮点运算
description: 本文档介绍了 Xamarin iOS 如何处理32位和64位精度浮点运算, 并讨论了性能的关联影响。
ms.prod: xamarin
ms.assetid: 003F25C1-B430-4339-9C95-7DF527EBC699
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 11/25/2015
ms.openlocfilehash: cd1bd0507f89f7b29bfcd3ef1ba0a3b1215632ce
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69527372"
---
# <a name="floating-point-operations-in-xamarinios"></a>Xamarin.iOS 中的浮点运算

默认情况下，Xamarin.iOS 将在 ARM 上执行使用 64 位精度的 32 位和 64 位浮点运算。  

虽然这种更高的精度更接近开发人员对桌面 C# 浮点运算的期望，但在移动设备上，对性能的影响可能会很大。

可以编译 32 位浮点代码以使用 32 位浮点运算。  为此，需要至少使用 Xamarin.iOS 版本 8.10 并在 iOS 生成选项面板中的“mtouch 额外参数”输入行中设置以下值：

```
--aot-options=-O=float32
```

这将通知静态编译器（Mono 的内置静态编译器或 LLVM 驱动的静态编译器）使用 32 位浮点数执行浮点运算。
