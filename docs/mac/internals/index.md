---
title: 实质上在 Xamarin.Mac 中
description: 本文档所链接到各种指南描述 Xamarin.Mac 的内部工作机制。 链接的文档讨论领先于实时编译、 Xamarin.Mac 体系结构和 Xamarin.Mac 注册机构。
ms.prod: xamarin
ms.assetid: 84974D75-0CCE-4455-AA38-00DE68AE33B6
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 11/10/2017
ms.openlocfilehash: 872f26febf3abbe4d659773d2bf2d27348c64513
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61033238"
---
# <a name="under-the-hood-in-xamarinmac"></a>实质上在 Xamarin.Mac 中

## <a name="ahead-of-time-compilation-aotaotmd"></a>[继续操作的实时编译 (AOT)](aot.md)

继续操作的预先 (aot) 编译为提高启动性能的功能强大的优化技术。 但是，它还会影响生成时，应用程序大小和程序执行中影响深远的方式，因此很值得了解其工作原理。

## <a name="mac-architecturearchitecturemd"></a>[Mac 体系结构](architecture.md)

Objective C，包括概念，如编译、 选择器、 注册机构、 应用程序启动和生成器到 Xamarin.Mac 的关系。

## <a name="xamarinmac-registrarregistrarmd"></a>[Xamarin.Mac registrar](registrar.md)

Xamarin.Mac 之间的差距托管的领域和 Cocoa 的运行时，允许托管的类来调用非托管的 Objective C 类，并返回发生事件时调用。 若要执行此"奇妙"所需的工作由注册机构，但有时可以帮助你了解这怎么回事"暗中"。
