---
title: 绑定 Xamarin mac 库
description: 本文档链接到介绍如何在 Xamarin 应用程序（包括客观 Sharpie 和示例代码）中使用目标 C 绑定的指南。
ms.prod: xamarin
ms.assetid: 521707CD-79D3-488A-84CB-A37EBF93AC94
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 01/13/2017
ms.openlocfilehash: c478437a9c84475e8c31484523db16336f8808e6
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029930"
---
# <a name="binding-mac-libraries-for-xamarinmac"></a>绑定 Xamarin mac 库

单击下面的链接可了解如何在 Xamarin 上绑定目标-C 库：

- [**概述**](~/cross-platform/macios/binding/overview.md)-
  说明绑定的工作原理。
- [**绑定目标-c 库**](~/cross-platform/macios/binding/objective-c-libraries.md)-
  有关如何绑定要在 Xamarin 项目中使用的目标-c 库的说明。
- [**类型定义参考指南**](~/cross-platform/macios/binding/binding-types-reference.md)-
  介绍了绑定作者用于驱动绑定生成过程的所有可用属性。

## <a name="objective-sharpiecross-platformmaciosbindingobjective-sharpieindexmd"></a>[目标 Sharpie](~/cross-platform/macios/binding/objective-sharpie/index.md)

客观 Sharpie 是一个命令行工具，可帮助启动绑定的第一步。
它的工作原理是分析本机库的标头文件，以将公共 API 映射到[绑定定义](~/cross-platform/macios/binding/binding-types-reference.md)（否则手动执行的过程）。 客观 Sharpie 不会自行创建绑定，但可以帮助你入门！

## <a name="examples"></a>示例

请参阅[XMBindingExample Mac 示例](https://github.com/xamarin/mac-samples/tree/master/XMBindingExample)，了解如何使用绑定项目创建 Mac 绑定。

## <a name="related-links"></a>相关链接

- [绑定 Objective-C](~/cross-platform/macios/binding/index.md)
- [绑定 iOS 库](~/ios/platform/binding-objective-c/index.md)
