---
title: .NET 嵌入限制
description: 本文档介绍 .NET 嵌入的限制，它是一种工具，可用于在其他编程语言中使用 .NET 代码。
ms.prod: xamarin
ms.assetid: EBBBB886-1CEF-4DF4-AFDD-CA96049F878E
author: davidortinau
ms.author: daortin
ms.date: 11/14/2017
ms.openlocfilehash: a8b63638861e8d44deb4ea72959d7461190f7713
ms.sourcegitcommit: 6266ef043ae0289f174e901f204f2a280a53c071
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/30/2019
ms.locfileid: "75545801"
---
# <a name="net-embedding-limitations"></a>.NET 嵌入限制

本文档介绍 .NET 嵌入的限制，并尽可能为其提供解决方法。

## <a name="general"></a>“常规”

### <a name="use-more-than-one-embedded-library-in-a-project"></a>在一个项目中使用多个嵌入库

同一应用程序中不能有两个共同存在的 Mono 运行时。 这意味着不能在同一个应用程序中使用两个不同的 .NET 嵌入生成的库。

**解决方法：** 您可以使用生成器创建包含多个程序集（来自不同项目）的单个库。

### <a name="subclassing"></a>分类

通过为目标语言和平台公开一组现成的 Api，.NET 嵌入可简化应用程序内的 Mono 运行时集成。

但这并不是双向集成，例如，你不能对托管类型进行子类化并期望托管代码在本机代码中回调，因为托管代码不知道此共存。

根据你的需要，可以解决此限制的某些部分，例如

* 托管代码可以通过 p/invoke 进入本机代码。 这需要自定义托管代码，以允许来自本机代码的自定义;

* 使用类似于 Xamarin 的产品并公开托管库，以允许目标 C （在本例中为）对某些托管的 NSObject 子类进行子类处理。

## <a name="objective-c-generated-code"></a>目标-C 生成的代码

### <a name="nullability"></a>可空性

.NET 中没有元数据，告诉我们对于 API 是否可以接受空引用。 如果大多数 Api 无法处理 `null` 参数，则会引发 `ArgumentNullException`。 这会造成问题，因为异常的目标 C 处理会更好地避免。

由于我们不能在标头文件中生成准确的空性注释并希望最大程度地减少托管异常，因此，在可能的情况下，可以为`NS_ASSUME_NONNULL_BEGIN`可为 null 的注释添加一些特定的。

### <a name="bitcode-ios"></a>Bitcode （iOS）

目前，.NET 嵌入不支持 bitcode on iOS，这是针对某些 Xcode 项目模板启用的。 这将必须禁用才能成功链接生成的框架。

* 对于 iOS，bitcode 是将应用提交到 Apple 的 AppStore 时可选的。 Xamarin 不支持 iOS，因为生成的 bitcode 是 "内联程序集"。 这不会对 iOS 平台提供任何好处，因为它不能优化服务器端，但会使二进制文件更大，生成时间更长。

* 对于 tvOS 和 watchOS，需要 bitcode 将应用提交到 Apple 的 AppStore。 Xamarin 支持 tvOS 上的 bitcode （作为 "内联程序集"）和 watchOS （作为 "LLVM/IR"）来满足此要求。

* 对于 macOS，当前不需要 bitcode 支持，也不支持 Xamarin。

![Bitcode 选项](images/ios-bitcode-option.png)
