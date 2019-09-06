---
title: 体系结构概述
description: 本文档介绍 Xamarin Workbooks 的体系结构，并检查交互式代理和交互式客户端如何协同工作。
ms.prod: xamarin
ms.assetid: 6C0226BE-A0C4-4108-B482-0A903696AB04
author: conceptdev
ms.author: crdun
ms.date: 03/30/2017
ms.openlocfilehash: 7129d0bedddb272ef87e3d209cb05c2ca0c0acf4
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70285284"
---
# <a name="architecture-overview"></a>体系结构概述

Xamarin Workbooks 功能必须彼此结合使用的两个主要组件：_代理_和_客户端_。

## <a name="interactive-agent"></a>交互式代理

代理组件是小型平台特定的程序集，在 .NET 应用程序的上下文中运行。

Xamarin Workbooks 为多个平台（如 iOS、Android、Mac 和 WPF）提供预先生成的 "空" 应用程序。 这些应用程序显式托管代理。

在实时检查（Xamarin Inspector）期间，代理通过 IDE 调试器注入到现有应用程序中，作为常规开发 & 调试工作流的一部分。

## <a name="interactive-client"></a>交互式客户端

客户端是本机 shell （在 Mac 上为 Cocoa，在 Windows 上为 WPF），它承载了用于演示工作簿/复制接口的 web 浏览器图面。 从 SDK 的角度来看，所有客户端集成都是在 JavaScript 和 CSS 中实现的。

客户端负责将源代码编译为小程序集，并将其发送到连接的代理以执行。 执行结果被发送回客户端进行呈现。 工作簿中的每个单元都生成一个程序集，该程序集引用上一个单元格的程序集。

由于代理可以在任何类型的 .NET 平台上运行并且有权访问正在运行的应用程序中的任何内容，因此必须小心地以与平台无关的方式对结果进行序列化。
