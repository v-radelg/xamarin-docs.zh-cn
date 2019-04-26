---
title: UrhoSharp Mac 的支持
description: 本文档讨论对 UrhoSharp macOS 支持。 它介绍如何创建一个项目，并提供一些示例代码的链接。
ms.prod: xamarin
ms.assetid: 95FFBD36-14E9-4C17-B1E8-9A04E81E824D
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: 6d0a048020284319682c1bee0f9a1d7f9af00977
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61386359"
---
# <a name="urhosharp-mac-support"></a>UrhoSharp Mac 的支持

_Mac 特定设置和功能_

尽管 Urho 是可移植类库，并且允许相同的 API，用于跨各种平台的游戏逻辑，仍需要初始化 Urho 在平台特定驱动程序，并在某些情况下，将想要充分利用平台特定功能.

在以下页中，假定`MyGame`是一个的子类`Application`类。

## <a name="macos"></a>macOS

**支持的体系结构：** x86/x86-64 为 32 位和 64 位。

## <a name="creating-a-project"></a>创建项目

创建控制台项目、 引用 Urho NuGet 和确保找到资产 （包含的数据目录的目录）。

```csharp
DesktopUrhoInitializer.AssetsDirectory = "../Assets";
new MyGame().Run();
```

## <a name="example"></a>示例

[完整示例](https://github.com/xamarin/urho-samples/tree/master/FeatureSamples/Cocoa)


