---
title: 绑定 Xamarin mac 库
description: 本文档链接到介绍如何在 Xamarin 应用程序（包括客观 Sharpie 和示例代码）中使用目标 C 绑定的指南。
ms.prod: xamarin
ms.assetid: 521707CD-79D3-488A-84CB-A37EBF93AC94
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 01/13/2017
ms.openlocfilehash: 59ac5a4f9949f1e65e67b9629c43ddb4b822bf43
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290052"
---
# <a name="binding-mac-libraries-for-xamarinmac"></a>绑定 Xamarin mac 库

单击下面的链接可了解如何在 Xamarin 上绑定目标-C 库：

- [**概述**](~/cross-platform/macios/binding/overview.md)-
  描述绑定的工作方式。
- [**绑定目标-C 库**](~/cross-platform/macios/binding/objective-c-libraries.md)-
  有关如何绑定要在 Xamarin 项目中使用的目标 C 库的说明。
- [**类型定义参考指南**](~/cross-platform/macios/binding/binding-types-reference.md)-
  介绍可用于绑定的作者用于驱动绑定生成过程的所有属性。

## <a name="objective-sharpiecross-platformmaciosbindingobjective-sharpieindexmd"></a>[目标 Sharpie](~/cross-platform/macios/binding/objective-sharpie/index.md)

客观 Sharpie 是一个命令行工具，可帮助启动绑定的第一步。
它的工作原理是分析本机库的标头文件，以将公共 API 映射到[绑定定义](~/cross-platform/macios/binding/binding-types-reference.md)（否则手动执行的过程）。 客观 Sharpie 不会自行创建绑定，但可以帮助你入门！

## <a name="examples"></a>示例

请参阅[XMBindingExample Mac 示例](https://github.com/xamarin/mac-samples/tree/master/XMBindingExample)，了解如何使用绑定项目创建 Mac 绑定。

## <a name="related-links"></a>相关链接

- [绑定 Objective-C](~/cross-platform/macios/binding/index.md)
- [绑定 iOS 库](~/ios/platform/binding-objective-c/index.md)
