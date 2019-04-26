---
title: 适用于 Xamarin.Mac 的绑定 Mac 库
description: 此文档所链接到介绍如何使用 Xamarin.Mac 应用程序，包括目标 Sharpie 和示例代码中的 Objective C 绑定的指南。
ms.prod: xamarin
ms.assetid: 521707CD-79D3-488A-84CB-A37EBF93AC94
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 01/13/2017
ms.openlocfilehash: fde21b2056d56cbf1c4768b287e29f559390f500
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61032468"
---
# <a name="binding-mac-libraries-for-xamarinmac"></a>适用于 Xamarin.Mac 的绑定 Mac 库

请遵循以下链接了解有关绑定 OBJECTIVE-C 库上 Xamarin.Mac:

- [**概述**](~/cross-platform/macios/binding/overview.md) -
  描述绑定的工作原理。
- [**绑定 OBJECTIVE-C 库**](~/cross-platform/macios/binding/objective-c-libraries.md) -
  如何将绑定 OBJECTIVE-C 库的 Xamarin 项目中使用的说明。
- [**键入定义参考指南**](~/cross-platform/macios/binding/binding-types-reference.md) -
  描述所有可用来驱动绑定生成进程的绑定作者的属性。

## <a name="objective-sharpiecross-platformmaciosbindingobjective-sharpieindexmd"></a>[目标 Sharpie](~/cross-platform/macios/binding/objective-sharpie/index.md)

目标 Sharpie 是一个命令行工具来帮助启动绑定的第一次传递。
其工作原理是分析要映射到的公共 API 的本机库的标头文件[绑定定义](~/cross-platform/macios/binding/binding-types-reference.md)（否则手动完成该过程）。 目标 Sharpie 不会创建一个绑定本身，但它可以帮助您入门 ！

## <a name="examples"></a>示例

请参阅[XMBindingExample Mac 示例](https://github.com/xamarin/mac-samples/tree/master/XMBindingExample)若要了解如何创建 Mac 绑定使用绑定项目。

## <a name="related-links"></a>相关链接

- [绑定 Objective-C](~/cross-platform/macios/binding/index.md)
- [绑定 iOS 库](~/ios/platform/binding-objective-c/index.md)
