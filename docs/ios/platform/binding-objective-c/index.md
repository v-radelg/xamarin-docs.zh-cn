---
title: 绑定 iOS 库
description: 本文档介绍如何创建C#绑定到 OBJECTIVE-C 代码，使其能够使用本机库和 CocoaPods Xamarin.iOS 应用程序中。
ms.prod: xamarin
ms.assetid: EBDC50DC-B44B-4003-AB2B-1EEB868A5E01
ms.technology: xamarin-ios
ms.custom: xamu-video
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: e599d4f99877e24e06de2c26ed2cafe48526f6f5
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61346899"
---
# <a name="binding-ios-libraries"></a>绑定 iOS 库

请按照下面的链接来了解对于 Xamarin.iOS 和 Xamarin.Mac 绑定 OBJECTIVE-C 库和 CocoaPods:

- [**概述**](~/cross-platform/macios/binding/overview.md) -
  描述绑定的工作原理。
- [**绑定 OBJECTIVE-C 库**](~/cross-platform/macios/binding/objective-c-libraries.md) -
  如何将绑定 OBJECTIVE-C 库的 Xamarin 项目中使用的说明。
- [**键入定义参考指南**](~/cross-platform/macios/binding/binding-types-reference.md) -
  描述所有可用来驱动绑定生成进程的绑定作者的属性。

## <a name="objective-sharpiecross-platformmaciosbindingobjective-sharpieindexmd"></a>[目标 Sharpie](~/cross-platform/macios/binding/objective-sharpie/index.md)

目标 Sharpie 是一个命令行工具来帮助启动绑定的第一次传递。
其工作原理是分析要映射到的公共 API 的本机库的标头文件[绑定定义](~/cross-platform/macios/binding/objective-c-libraries.md)（否则手动完成该过程）。 目标 Sharpie 不会创建一个绑定本身，但它可以帮助您入门 ！

目标 Sharpie 3.0 引入了直接绑定 Cocoapods 的功能 ！

## <a name="walkthrough---binding-an-ios-objective-c-librarywalkthroughmd"></a>[演练-: 绑定 iOS OBJECTIVE-C 库](walkthrough.md)

此页提供了创建使用开放源代码 iOS 绑定项目的分步演练[ **InfColorPicker** ](https://github.com/InfinitApps/InfColorPicker) Objective C 项目作为示例。 **InfColorPicker**库提供了允许用户选择一种颜色根据其 HSB 表示形式，使用户更加友好的颜色选择的可重用视图控制器。
目标 Sharpie 将用于绑定过程中提供帮助。

## <a name="xamarin-university-lightning-lecture"></a>Xamarin University 闪电讲座

> [!VIDEO https://youtube.com/embed/ZUoPLcmnf1o]

**iOS 在 C 中的绑定 /C++，也可由[Xamarin University](https://university.xamarin.com/)**

## <a name="related-links"></a>相关链接

- [绑定 Objective-C](~/cross-platform/macios/binding/index.md)
- [Mac 绑定](~/mac/platform/binding.md)
- [Xamarin 大学课程：生成一个 Objective C 绑定库](https://university.xamarin.com/classes/track/all#building-an-objective-c-bindings-library)
- [Xamarin 大学课程：生成与目标 Sharpie Objective C 绑定库](https://university.xamarin.com/classes/track/all#build-an-objective-c-bindings-library-with-objective-sharpie)