---
title: 使用 Xcode 项目的实际示例
description: 本文档介绍如何使用 Xcode 项目作为目标 Sharpie，从而简化了创建的过程直接输入C#绑定到 OBJECTIVE-C 代码。
ms.prod: xamarin
ms.assetid: 168AA64C-E181-4937-A1F2-AD095B9A36F2
author: asb3993
ms.author: amburns
ms.date: 01/15/2016
ms.openlocfilehash: ccfc2f1760d8971e2d824cf65344fa2a5e158c12
ms.sourcegitcommit: bf18425f97b48661ab6b775195eac76b356eeba0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2019
ms.locfileid: "64978365"
---
# <a name="real-world-example-using-an-xcode-project"></a>使用 Xcode 项目的实际示例

**此示例使用[POP 库从 Facebook](https://github.com/facebook/pop)。**

新版本 3.0，目标 Sharpie 支持 Xcode 项目作为输入。 这些项目指定正确的标头文件和编译器标志需要编译的本机库，并因此需要将其绑定过。 目标 Sharpie 将选择第一个_目标_项目中，如果不指示你以其他及其默认配置。

目标 Sharpie 试图分析的项目和标头文件之前，它必须生成它。 项目通常具有将正确结构外部使用和集成，标头文件，因此，最好始终在尝试将其绑定前生成完整的项目的生成阶段。

<pre>$ <b>git clone https://github.com/facebook/pop.git</b>
Cloning into 'pop'...
   <em>(more git clone output)</em>

$ <b>cd pop</b>
$ <b>sharpie bind pop.xcodeproj -sdk iphoneos9.0</b></pre>

