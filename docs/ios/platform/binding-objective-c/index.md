---
title: 绑定 iOS 库
description: 本文档介绍如何创建C#与目标-C 代码的绑定，从而能够在 Xamarin iOS 应用程序中使用本机库和 CocoaPods。
ms.prod: xamarin
ms.assetid: EBDC50DC-B44B-4003-AB2B-1EEB868A5E01
ms.technology: xamarin-ios
ms.custom: xamu-video
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 61d1adfc997b34302f1f89f56653af906ca90135
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73022226"
---
# <a name="binding-ios-libraries"></a>绑定 iOS 库

请单击以下链接了解有关 Xamarin 和 Xamarin 的绑定目标 C 库和 CocoaPods：

- [**概述**](~/cross-platform/macios/binding/overview.md)-
  说明绑定的工作原理。
- [**绑定目标-c 库**](~/cross-platform/macios/binding/objective-c-libraries.md)-
  有关如何绑定要在 Xamarin 项目中使用的目标-c 库的说明。
- [**类型定义参考指南**](~/cross-platform/macios/binding/binding-types-reference.md)-
  介绍了绑定作者用于驱动绑定生成过程的所有可用属性。

## <a name="objective-sharpiecross-platformmaciosbindingobjective-sharpieindexmd"></a>[目标 Sharpie](~/cross-platform/macios/binding/objective-sharpie/index.md)

客观 Sharpie 是一个命令行工具，可帮助启动绑定的第一步。
它的工作原理是分析本机库的标头文件，以将公共 API 映射到[绑定定义](~/cross-platform/macios/binding/objective-c-libraries.md)（否则手动执行的过程）。 客观 Sharpie 不会自行创建绑定，但可以帮助你入门！

客观 Sharpie 3.0 引入了直接绑定 Cocoapods 的功能！

## <a name="walkthrough---binding-an-ios-objective-c-librarywalkthroughmd"></a>[演练-绑定 iOS 目标-C 库](walkthrough.md)

本页提供使用开源[**InfColorPicker**](https://github.com/InfinitApps/InfColorPicker)目标-C 项目作为示例创建 iOS 绑定项目的分步演练。 **InfColorPicker**库提供了一个可重用的视图控制器，该控制器允许用户根据其 HSB 表示形式选择颜色，使颜色选择更易于用户使用。
目标 Sharpie 将用于帮助进行绑定。

## <a name="video"></a>视频

> [!VIDEO https://youtube.com/embed/ZUoPLcmnf1o]

**C/C++视频中的 iOS 绑定**

## <a name="related-links"></a>相关链接

- [绑定 Objective-C](~/cross-platform/macios/binding/index.md)
- [Mac 绑定](~/mac/platform/binding.md)
