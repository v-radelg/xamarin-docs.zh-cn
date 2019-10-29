---
title: Xamarin Mac 注册机构
description: 本文档描述了 Xamarin 的注册器及其动态、静态和部分静态（混合）使用配置的用途。
ms.prod: xamarin
ms.assetid: 7CAAA6B7-D654-4AD3-BAEC-9DD01210978A
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 11/10/2017
ms.openlocfilehash: 991d9b2d911b5aa4ac07225fd1df34877451df49
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73017309"
---
# <a name="xamarinmac-registrar"></a>Xamarin Mac 注册机构

_本文档介绍了 Xamarin 和其他使用配置的用途。_

## <a name="overview"></a>概述

Xamarin 可以在托管（.NET）和 Cocoa 的运行时之间实现桥梁，使托管类可以调用非托管的目标 C 类，并在发生事件时回调。 执行此 "神奇" 所需的工作由注册机构处理，一般情况下，在视图中隐藏。

此注册有性能影响，特别是在应用程序启动时，以及了解 "在幕后" 这一情况下所发生的事情有时会很有帮助。

## <a name="configurations"></a>配置

从根本上讲，在启动时注册机构的作业可以分为两个类别：

- 扫描从 NSObject 派生的每个托管类，并收集要公开给目标 C 运行时的项的列表。
- 将此信息注册到目标 C 运行时。

随着时间的推移，将创建三个不同的注册器配置来涵盖不同用例。 每个都有不同的生成和运行时后果：

- **动态注册**器–在启动过程中，使用 .net 反射扫描每个加载的类型，确定相关项的列表，并通知本机运行时。 此选项向生成添加了零时间，但在启动期间计算成本非常高（最多秒）。
- **静态注册**器–在生成过程中，计算要注册的项集，并生成目标-C 代码来处理注册。 此代码在启动过程中调用以快速注册所有项。 添加了一个很长的暂停时间，但可以在应用程序启动时缩短很长时间。
- **"部分" 静态**-一种较新的 "混合" 方法，这两种方法的优点非常高。 由于**Xamarin**中的导出是常量，因此请保存预计算库来处理其注册，并链接到中的链接。 使用反射来处理用户库，但由于用户库导出的类型的类型更少，平台绑定的类型通常相当简单。 Neglectable 的生成时间会受到影响，并减少众多动态的 "成本"。

"今天的部分静态" 是 "调试" 配置的默认值，"静态" 是发布配置的默认设置。

存在以下情况：

- 启动后加载的、从 NSObject 派生的类的插件
- 动态创建的类实例，派生自 NSObject

注册器无法知道在开始时需要注册某种类型。 提供 `ObjCRuntime.Runtime.RegisterAssembly` 方法，以通知注册机构有其他要考虑的类型。
