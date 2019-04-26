---
title: 使用 MonoGame 的游戏开发简介
description: 此多部分演练演示如何创建使用 MonoGame 的简单 2D 应用程序。  它涵盖了常见的游戏编程概念，例如，图形中，输入，游戏实体和物理特性。
ms.prod: xamarin
ms.assetid: D781401F-7A96-4098-9645-5F98AEAF7F71
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 4ab98d59bc74672f9531f4dbd3c33a6270582612
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61386256"
---
# <a name="introduction-to-game-development-with-monogame"></a>使用 MonoGame 的游戏开发简介

_此多部分演练演示如何创建使用 MonoGame 的简单 2D 应用程序。它涵盖了常见的游戏编程概念，例如，图形中，输入，游戏实体和物理特性。_

本文介绍了用于制作跨平台游戏的 MonoGame API 技术。 有关平台的完整列表，请参阅 [MonoGame 网站](http://www.monogame.net/)。 本教程将使用 C＃ 代码示例，虽然 MonoGame 也完全可以使用F＃。

MonoGame 是一个跨平台的硬件加速API、提供图形、音频、游戏状态管理、输入以及用于导入资产的内容流水线。 与大多数游戏引擎不同，MonoGame 不提供或强加任何模式或项目结构。  虽然这意味着开发人员可以随意组织他们的代码，但这也意味着首次启动新项目时需要一些设置代码。

本演练的第一部分重点介绍如何设置一个空项目。 最后一部分介绍了如何编写我们所有的游戏逻辑和内容 — 大多数都是跨平台的。

本演练结束时，我们将创建一个简单的游戏，玩家可以通过触摸输入控制动画角色。  虽然这在技术上并不是一个完整的游戏（因为它没设置赢或输的条件），但它展示了许多游戏开发概念，可以作为许多游戏类型的基础。 

下面显示了此演练的结果：

![示例游戏字符跟随鼠标的动画](images/image1.gif)

## <a name="monogame-and-xna"></a>Monogame 和 XNA

MonoGame 库旨在模仿 Microsoft XNA 库的功能和语法。  所有 MonoGame 对象都存在于 Microsoft.Xna 命名空间下 - 允许大多数 XNA 代码在 MonoGame 中使用而无需修改。 

熟悉 XNA 的开发人员已熟悉 MonoGame 的语法，想寻找更多有关使用 MonoGame 的信息的开发人员可参考现有的在线XNA演练、API 文档和讨论。


## <a name="walkthrough-parts"></a>演练部分

- [第 1 部分 – 创建跨平台 MonoGame 项目](~/graphics-games/monogame/introduction/part1.md)
- [第 2 部分 – 实现 WalkingGame ](~/graphics-games/monogame/introduction/part2.md)

## <a name="related-links"></a>相关链接

- [WalkingGame MonoGame 项目 （示例）](https://developer.xamarin.com/samples/mobile/WalkingGameMG/)
- [XNB 字体 iOS](https://github.com/mono/CocosSharp/tree/master/Samples/GameStarterKit/GameStarterKit/Content/fonts)
- [XNB 字体 Android](https://github.com/mono/CocosSharp/tree/master/Samples/GameStarterKit/GameStarterKit/Assets/Content/fonts)
- [NuGet 上的 MonoGame Android](https://www.nuget.org/packages/MonoGame.Framework.Android/)
- [NuGet 上的 MonoGame iOS](https://www.nuget.org/packages/MonoGame.Framework.iOS/)
- [MonoGame API 文档](http://www.monogame.net/documentation/?page=main)
