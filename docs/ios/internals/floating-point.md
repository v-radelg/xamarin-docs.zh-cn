---
title: Xamarin 中的浮点运算
description: 本文档介绍了 Xamarin iOS 如何处理32位和64位精度浮点运算，并讨论了性能的关联影响。
ms.prod: xamarin
ms.assetid: 003F25C1-B430-4339-9C95-7DF527EBC699
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 11/25/2015
ms.openlocfilehash: af0e37b41a7bbe831bc629b1fd916f62819b3711
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73022343"
---
# <a name="floating-point-operations-in-xamarinios"></a>Xamarin 中的浮点运算

在 ARM 上，Xamarin 默认使用64位精度执行32位和64位浮点运算。  

虽然这种更高的精度更接近于桌面C#上的浮点操作，而在移动上，对性能的影响可能会很大。

可以编译32位浮点代码以使用32位浮点运算。  为此，需要至少使用 Xamarin 8.10，并在 "mtouch 额外参数" 条目行的 "iOS 生成选项" 面板中设置以下值：

```
--aot-options=-O=float32
```

这将通知静态编译器（Mono 的内置静态编译器或 LLVM 驱动的编译器），以使用32位浮点执行浮点运算。
