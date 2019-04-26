---
title: .NET 嵌入限制
description: 本文档介绍.NET 嵌入内容，该工具，您可以使用其他编程语言中的.NET 代码的限制。
ms.prod: xamarin
ms.assetid: EBBBB886-1CEF-4DF4-AFDD-CA96049F878E
author: lobrien
ms.author: laobri
ms.date: 11/14/2017
ms.openlocfilehash: 7a162d632c98b4e412fa1b7b0c0c40ac945ff09f
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60945777"
---
# <a name="net-embedding-limitations"></a>.NET 嵌入限制

本文档介绍了.NET 嵌入的限制，只要有可能，可为他们提供解决方法。

## <a name="general"></a>常规

### <a name="use-more-than-one-embedded-library-in-a-project"></a>使用多个项目中的嵌入的库

不能有两个相同的应用程序中共存的 Mono 运行时。 这意味着不能使用两个不同.NET 嵌入生成的库在同一应用程序。

**临时解决方法：** 可以使用生成器创建的单个库中包含多个程序集 （从不同的项目）。

### <a name="subclassing"></a>子类化

.NET 嵌入，则可以通过提供随时可用 Api 的目标语言和平台的一组简化 Mono 运行时在应用程序的集成。

但这不是双向集成，例如您能子类的托管类型，并且希望托管的代码回调在本机代码内部，因为您的托管的代码不知道此共存。

具体取决于你的需求，有可能对解决方法部分的此限制例如

* 托管的代码可以在本机代码到 p/invoke。 这需要自定义托管的代码以允许从本机代码; 的自定义

* 使用 Xamarin.iOS 等产品，并公开的托管的库，将允许 Objective C （在这种情况下） 为子类某些托管 NSObject 子类。

## <a name="objective-c-generated-code"></a>Objective C 生成的代码

### <a name="nullability"></a>为 null 性

没有任何元数据，以告诉我们是否可以接受空引用的.NET 或为 API。 大多数 Api 将引发`ArgumentNullException`如果不能应对`null`参数。 这是有问题，因为 Objective C 处理的异常是更好地避免使用。

由于我们不能在标头文件中生成准确的为 null 性批注并想要最大程度减少托管的异常，因此我们默认为非空的参数 (`NS_ASSUME_NONNULL_BEGIN`)，并添加一些特定于，可能精度时，为 null 性批注。

### <a name="bitcode-ios"></a>Bitcode (iOS)

目前.NET 嵌入不支持 bitcode ios，可用于某些 Xcode 项目模板。 这会禁用已成功生成链接框架。

![Bitcode 选项](images/ios-bitcode-option.png)
