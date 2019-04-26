---
title: 体系结构概述
description: 本文档介绍 Xamarin 工作簿，检查交互式代理和交互式客户端如何协同工作的体系结构。
ms.prod: xamarin
ms.assetid: 6C0226BE-A0C4-4108-B482-0A903696AB04
author: lobrien
ms.author: laobri
ms.date: 03/30/2017
ms.openlocfilehash: 352e8d0191512184ffd7d82fa0dfa0bc79fa24ca
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61257420"
---
# <a name="architecture-overview"></a>体系结构概述

Xamarin Workbooks 功能必须在个相互配合工作的两个主要组件：_代理_并_客户端_。

## <a name="interactive-agent"></a>交互式代理

代理组件是在.NET 应用程序的上下文中运行的小型特定于平台的程序集中。

Xamarin 工作簿提供了多个平台，例如 iOS、 Android、 Mac 和 WPF 的预建"空"应用程序。 这些应用程序显式托管代理。

实时检查 (Xamarin Inspector)，过程中的代理注入通过 IDE 调试器到现有应用程序作为常规开发和调试工作流的一部分。

## <a name="interactive-client"></a>交互式客户端

客户端是承载 web 浏览器图面来显示工作簿/REPL 界面的本机 shell （Cocoa Mac 上的，Windows 上的 WPF）。 从 SDK 的角度看，所有客户端集成 JavaScript 和 CSS 中实现。

客户端负责 （通过 Roslyn) 到小的程序集编译源代码和通过发送到已连接代理的执行。 执行结果发送回客户端以进行呈现。 每个单元格的工作簿中生成一个程序集引用的程序集的上一个单元格。

因为代理可以运行任何类型的.NET 平台上和运行的应用程序中具有访问权限的任何内容，必须格外小心以平台无关的方式序列化结果。
