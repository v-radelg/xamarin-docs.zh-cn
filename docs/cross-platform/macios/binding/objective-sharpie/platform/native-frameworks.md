---
title: 绑定本机框架
description: 本文档介绍如何使用目标 Sharpie 作为一个框架的-框架选项来创建绑定到一个库分发。
ms.prod: xamarin
ms.assetid: 91AE058A-3A1F-41A9-9DE4-4B96880A1869
author: asb3993
ms.author: amburns
ms.date: 01/15/2016
ms.openlocfilehash: ca103ee027597813be51e126aaa05f9aa969af35
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61199123"
---
# <a name="binding-native-frameworks"></a>绑定本机框架

本机库作为分发，有时[framework](https://developer.apple.com/library/mac/documentation/MacOSX/Conceptual/BPFrameworks/Concepts/WhatAreFrameworks.html)。 目标 Sharpie 提供便利功能正确绑定定义框架中的通过`-framework`选项。

例如，绑定[Adobe Creative SDK 框架](https://creativesdk.adobe.com/downloads.html)iOS 是非常简单：

<pre>$ <b>sharpie bind \
    -framework AdobeCreativeSDKFoundation.framework \
    -sdk iphoneos8.1</b></pre>

在某些情况下，将指定的一种框架**Info.plist**应编译指示针对哪些 SDK 框架。 如果该信息存在和未显式`-sdk`传递选项，则目标 Sharpie 将从 framework 的推断**Info.plist** (任一`DTSDKName`键或组合键的`DTPlatformName`和`DTPlatformVersion`密钥)。

`-framework`选项不允许显式标头文件传递。 总括性标头文件选择的约定，框架名称。 如果找不到一个涵盖性标头、 目标 Sharpie 不会尝试将绑定框架和你必须手动执行绑定，通过提供正确的涵盖性标头文件以分析以及 clang 任意框架自变量 (如`-F`框架搜索路径选项)。

实质上，指定`-framework`是一种方便。 以下绑定自变量相等`-framework`速记更高版本。
特别重要的是`-F .`提供 clang 框架搜索路径 （请注意空间和时间，所需的命令的一部分）。

<pre>$ <b>sharpie bind \
    -sdk iphoneos8.1 \
    AdobeCreativeSDKFoundation.framework/Headers/AdobeCreativeSDKFoundation.h \
    -scope AdobeCreativeSDKFoundation.framework/Headers \
    -c -F .</b></pre>

## <a name="related-links"></a>相关链接

- [Xamarin 大学课程：生成一个 Objective C 绑定库](https://university.xamarin.com/classes/track/all#building-an-objective-c-bindings-library)
- [Xamarin 大学课程：生成与目标 Sharpie Objective C 绑定库](https://university.xamarin.com/classes/track/all#build-an-objective-c-bindings-library-with-objective-sharpie)

