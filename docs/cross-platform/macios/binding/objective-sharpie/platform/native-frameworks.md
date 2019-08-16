---
title: 绑定本机框架
description: 本文档介绍了如何使用客观 Sharpie 的框架选项创建到作为框架分发的库的绑定。
ms.prod: xamarin
ms.assetid: 91AE058A-3A1F-41A9-9DE4-4B96880A1869
author: asb3993
ms.author: amburns
ms.date: 01/15/2016
ms.openlocfilehash: cb6c39b2110161b3f839b8adc03701007f09cc4d
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69521881"
---
# <a name="binding-native-frameworks"></a>绑定本机框架

有时, 本机库以[框架](https://developer.apple.com/library/mac/documentation/MacOSX/Conceptual/BPFrameworks/Concepts/WhatAreFrameworks.html)的形式分发。 客观 Sharpie 为通过`-framework`选项绑定正确定义的框架提供了一项便利功能。

例如, 绑定适用于 iOS 的[Adobe 创造性 SDK 框架](https://creativesdk.adobe.com/downloads.html)非常简单:

```
$ sharpie bind \
    -framework AdobeCreativeSDKFoundation.framework \
    -sdk iphoneos8.1
```

在某些情况下, 框架将指定**info.plist** , 它指示框架应编译到的 SDK。 如果此信息存在并且未传递`-sdk`显式选项, 则目标 Sharpie 将从框架的信息中推断它 **。 info.plist** ( `DTSDKName`密钥或`DTPlatformName`和`DTPlatformVersion`键的组合)。

`-framework`选项不允许传递显式标头文件。 根据框架名称, 按约定选择伞标头文件。 如果找不到伞标头, 则目标 Sharpie 不会尝试绑定框架, 并且必须通过提供要分析的正确伞头文件以及 clang 的任何框架自变量 (例如`-F`框架搜索路径选项)。

在幕后, 指定`-framework`只是一个快捷方式。 以下绑定参数与上述`-framework`简写参数相同。
特别重要的是`-F .` , 提供给 clang 的框架搜索路径 (请注意, 这是命令的一部分所需的空间和句点)。

```
$ sharpie bind \
    -sdk iphoneos8.1 \
    AdobeCreativeSDKFoundation.framework/Headers/AdobeCreativeSDKFoundation.h \
    -scope AdobeCreativeSDKFoundation.framework/Headers \
    -c -F .
```
