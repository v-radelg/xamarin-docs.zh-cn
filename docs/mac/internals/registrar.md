---
title: Xamarin.Mac registrar
description: 本文档介绍了 Xamarin.Mac 注册机构和它动态、 静态的且部分的静态 （混合） 的目的使用情况的配置。
ms.prod: xamarin
ms.assetid: 7CAAA6B7-D654-4AD3-BAEC-9DD01210978A
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 11/10/2017
ms.openlocfilehash: 21e1a2c6ae5a9ad8b6acf520851ea92a340da887
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61032492"
---
# <a name="xamarinmac-registrar"></a>Xamarin.Mac registrar

_本文档介绍 Xamarin.Mac 注册机构和其不同的使用配置的用途。_

## <a name="overview"></a>概述

Xamarin.Mac 之间的差距托管 (.NET) 领域和 Cocoa 的运行时，允许托管的类来调用非托管的 Objective C 类，并返回发生事件时调用。 若要执行此"奇妙"所需的工作由注册机构进行处理，并且，一般情况下，从视图中隐藏。

没有性能影响的特定于应用程序启动时间，此注册，并且有时可以帮助理解的"暗中"位。

## <a name="configurations"></a>配置

从根本上说在启动时注册机构的作业可以分为两个类别：

- 对于那些从 NSObject 派生扫描每个托管的类，并收集要向 Objective C 运行时公开的项列表。
- Objective C 运行时注册此信息。

随着时间推移，三个其他注册机构配置已创建了以涵盖不同用例。 每个具有不同的生成和运行时间的后果：

- **动态注册机构**– 在启动期间，使用.NET 反射来扫描每个已加载的类型，确定相关项的列表，并通知本机运行时。 此选项添加到生成时间为零，但是 （最多在数秒内） 的启动过程中的计算费用非常地昂贵。
- **静态注册机构**– 生成，期间计算要注册，并生成 Objective C 代码来处理注册项的组。 若要快速注册的所有项的启动期间调用此代码。 将添加到生成，但可以一长段停顿剪切从应用程序启动很长时间。
- **使用 partial 静态**– 较新的"混合"方法，它提供了大部分这两者的优势。 由于从导出**Xamarin.Mac.dll**是不变，保存一个预计算库来处理其注册中的链接。 使用反射来处理用户库，但为用户库导出要少很多平台绑定这通常是相当快速的类型。 Neglectable 生成时间的影响，并减少，绝大多数动态的"成本"。

部分静态今天是调试配置的默认值和静态是发布配置的默认值。

有一些方案：

- 从 NSObject 派生的类启动后加载插件
- 动态创建从 NSObject 派生的类实例

其中，注册机构是无法知道它需要注册在启动某些类型。 `ObjCRuntime.Runtime.RegisterAssembly`提供方法以通知它具有要考虑的其他类型的注册机构。
