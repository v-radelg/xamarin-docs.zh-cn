---
title: 使用 Xcode 项目的实际示例
description: 本文档介绍了如何使用 Xcode 项目作为客观 Sharpie 的直接输入，简化了将绑定创建C#到目标 C 代码的过程。
ms.prod: xamarin
ms.assetid: 168AA64C-E181-4937-A1F2-AD095B9A36F2
author: conceptdev
ms.author: crdun
ms.date: 01/15/2016
ms.openlocfilehash: e460994994c1383f29028be7b13cec216f2d12f7
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290651"
---
# <a name="real-world-example-using-an-xcode-project"></a>使用 Xcode 项目的实际示例

**此示例使用[Facebook 中的 POP 库](https://github.com/facebook/pop)。**

版本3.0 中的新增项，目标 Sharpie 支持 Xcode 项目作为输入。 这些项目指定编译本机库所需的正确的标头文件和编译器标志，因此也需要对其进行绑定。 否则，目标 Sharpie 将选择项目的第一个_目标_及其默认配置（如果不指示这样做）。

在客观 Sharpie 尝试分析项目文件和头文件之前，它必须生成它。 项目通常具有生成阶段，这些阶段会正确地构造用于外部使用和集成的标头文件，因此最好在尝试绑定之前始终生成完整的项目。

```
$ git clone https://github.com/facebook/pop.git
Cloning into 'pop'...
   (more git clone output)

$ cd pop
$ sharpie bind pop.xcodeproj -sdk iphoneos9.0
```
