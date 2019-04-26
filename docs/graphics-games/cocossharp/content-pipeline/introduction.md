---
title: 内容管道简介
description: 内容管道是应用程序或应用程序的组成部分，用于将文件转换成可由游戏项目加载的格式。 MonoGame 内容管道是转换为 CocosSharp 和 MonoGame 项目文件的特定内容管道实现。
ms.prod: xamarin
ms.assetid: 40628B5F-FAF7-4FA7-A929-6C3FEA83F8EC
author: conceptdev
ms.author: crdun
ms.date: 03/27/2017
ms.openlocfilehash: 712c430fb6309ba0f5c3e573267c59e422de8ad2
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61029515"
---
# <a name="introduction-to-content-pipelines"></a>内容管道简介

_内容管道是应用程序或应用程序的组成部分，用于将文件转换成可由游戏项目加载的格式。MonoGame 内容管道是转换为 CocosSharp 和 MonoGame 项目文件的特定内容管道实现。_

本文提供了对内容的管道，主要专注于概念的理解*MonoGame Content Pipeline*，这是内容管道实现与 CocosSharp 和 MonoGame 一起使用。


## <a name="what-is-a-content-pipeline"></a>什么是内容管道？

术语*内容管道*是将文件从一种格式转换为另一个进程的常规术语。 *输入*的内容管道通常是输出的创作工具，如 Photoshop 的图像文件的文件。 创建的 content pipeline*输出*游戏项目可以直接加载的格式的文件。 通常的输出文件进行了优化的快速加载和减少磁盘大小。

内容管道可能是命令行可执行文件、 专用的基于 GUI 的应用程序或插件中嵌入另一个应用程序，如 Visual Studio。 内容管道通常运行游戏在执行之前。 如果内容管道与 Visual Studio 等一些应用程序相关联，它可能会执行编译时期间。 如果内容的管道，独立应用程序，它可能运行时明确告知应用程序来执行此操作由用户。 应用程序或逻辑负责将特定的输入的文件转换 (如 **.png**) 到关联的输出文件被称为*生成器*。 

我们可以可视化从到要在运行时加载，如下所示编写所需的文件的路径：

![](introduction-images/image1.png "从创作到要在运行时加载所需的文件的路径是在此关系图中进行可视化")

## <a name="why-use-a-content-pipeline"></a>为什么要使用内容管道？

内容管道会引入额外的步骤创建应用程序和游戏时，这可以增加编译时间，并与开发过程中添加复杂性之间。 尽管上述考虑，内容管道到游戏开发引入了许多优势：


### <a name="converting-to-a-format-understood-by-the-game"></a>转换为格式理解的游戏

CocosSharp 和 MonoGame 提供用于加载不同类型的内容; 方法但是，必须在加载之前正确地格式化内容。 大多数类型的内容需要某种类型的转换之前加载。 例如中的声音效果 **.wav**格式必须转换为 **.xnb**由于 CocosSharp 和 MonoGame 不支持加载要在运行时加载文件 **.wav**文件格式。


### <a name="converting-to-a-format-native-to-the-hardware"></a>转换为硬件的本机格式

不同的硬件可能会在运行时以不同方式处理内容。 例如，CocosSharp 游戏可以加载图像文件创建时`CCSprite`实例。 尽管可以使用相同的代码加载在 iOS 和 Android 上的文件，但每个平台以不同的方式存储的已加载的文件。 因此，MonoGame 内容管道格式纹理 **.xnb**以不同的方式取决于目标平台的文件。


### <a name="reducing-size-on-disk"></a>减少磁盘上大小 

内容管道可用于删除信息，这是在创作时很有用，但不是需要在运行时。 原始 （输入） 文件可以存储所有信息可帮助维护现有内容的内容创建者，但输出文件可以是精简以保持较小的整体游戏文件。 这种考虑因素是下载而不是在安装介质上分发的移动游戏尤其有用。


### <a name="reducing-load-time"></a>减少加载时间

游戏可能需要修改的内容以提高运行时性能，以提高视觉对象，或添加新功能。 例如许多 3D 游戏计算照明一次，然后呈现复杂的场景时使用此计算的结果。 正在加载内容可以是成本过高时，执行这些计算自计算时，可以改为执行构建游戏。 可以在内容中，启用内容要比其他可能更快地加载包含生成的计算。 


## <a name="xnb-file-extension"></a>xnb 文件扩展名

**.Xnb**文件扩展名是输出的 Monogame 内容管道的所有文件的扩展名。 此匹配输出的 Microsoft XNA Content Pipeline 的文件的扩展名。

**.Xnb**而不考虑原始的文件类型使用扩展。 换而言之，图像文件 (**.png**)，音频文件 (**.wav**)，和任何自定义文件类型将所有输出为 **.xnb**文件时通过内容管道进行传递。 因为扩展不能用于区分不同的文件格式然后 CocosSharp 和 MonoGame 方法加载 **.xnb**文件加载文件时不希望扩展。

可以使用 Monogame 管道工具对它进行讨论创建 CocosSharp 和 MonoGame.xnb 文件[在本演练中](~/graphics-games/cocossharp/content-pipeline/walkthrough.md)。


## <a name="summary"></a>总结

本文的概述和内容的管道的好处一般情况下，提供以及 MonoGame 内容管道简介。

## <a name="related-links"></a>相关链接

- [MonoGame 管道文档](http://www.monogame.net/documentation/?page=Pipeline)
