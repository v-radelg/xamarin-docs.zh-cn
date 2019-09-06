---
title: 在 Xamarin 中的后台
description: 本文档链接到描述 Xamarin 的内部工作原理的各种指南。 链接文档提前讨论了时间编译、Xamarin 体系结构和 Xamarin 的注册器。
ms.prod: xamarin
ms.assetid: 84974D75-0CCE-4455-AA38-00DE68AE33B6
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 11/10/2017
ms.openlocfilehash: 51cf479ba07a769f5d7a875bb3f1203caef2ad0b
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290111"
---
# <a name="under-the-hood-in-xamarinmac"></a>在 Xamarin 中的后台

## <a name="ahead-of-time-compilation-aotaotmd"></a>[预先编译（AOT）](aot.md)

预先（AOT）编译是一种功能强大的优化技术，可提高启动性能。 但是，它还会以深远的方式影响生成时间、应用程序大小和程序执行情况，因此，有必要了解它的工作原理。

## <a name="mac-architecturearchitecturemd"></a>[Mac 体系结构](architecture.md)

Xamarin 与目标-C 的关系，包括编译、选择器、注册机构、应用程序启动和生成器等概念。

## <a name="xamarinmac-registrarregistrarmd"></a>[Xamarin Mac 注册机构](registrar.md)

Xamarin 将托管世界和 Cocoa 的运行时之间的差距桥梁，使托管类可以调用非托管的目标 C 类，并在发生事件时回调。 执行这种 "神奇" 所需的工作由注册机构处理，但了解 "后台" 的情况有时会很有帮助。
