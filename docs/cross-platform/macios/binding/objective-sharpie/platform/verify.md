---
title: 目标 Sharpie 验证属性
description: 本文档介绍了生成的目标 Sharpie [验证] 属性。 开发人员，他们应手动验证目标 Sharpie 输出到突出显示 [验证] 属性。
ms.prod: xamarin
ms.assetid: 107FBCEA-266B-4295-B7AA-40A881B82B7B
author: asb3993
ms.author: amburns
ms.date: 01/15/2016
ms.openlocfilehash: 4bca896afb4dfc96fd6c1d7cdf489feb6a879e31
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61261216"
---
# <a name="objective-sharpie-verify-attributes"></a>目标 Sharpie 验证属性

通常会发现，将使用批注绑定生成的目标 Sharpie`[Verify]`属性。 这些属性指示你应_验证_目标 Sharpie 通过比较与原始 C/Objective C 声明 （它将在绑定声明上方的注释中提供） 的绑定未正确的做法。

验证建议用于_所有_绑定声明，但最有可能_需要_为声明使用批注`[Verify]`属性。 这是因为在许多情况下，没有足够的元数据中原始的本机源代码来推断如何最好地生成一个绑定。 您可能需要引用文档或要做出最佳的绑定决策的标头文件中的代码注释。

确认后，绑定是更正或修复它正确，_删除_`[Verify]`从绑定的属性。

> [!IMPORTANT]
> `[Verify]` 属性导致出现C#编译错误，以便您会被迫验证绑定。 应删除`[Verify]`属性时在您阅读 （并可能更正） 代码。

## <a name="verify-hints-reference"></a>验证提示引用

提供给特性的提示参数可以是交叉引用与以下文档。 对于任何生成的文档`[Verify]`完成绑定后，将在控制台上也提供属性。

|`[Verify]` 提示|描述|
|---|---|
|InferredFromPreceedingTypedef|此声明的名称通过从常见约定推导立即前面`typedef`原始本机源代码中。 验证推断的名称正确，因为此约定不明确。|
|ConstantsInterfaceAssociation|没有可靠方法来确定使用哪个 Objective C 接口外部变量声明可能是相关联。 这些实例绑定为`[Field]`到附近的由具体接口以生成更直观的 API，可能消除常量部分接口中的属性完全接口。|
|MethodToProperty|Objective C 方法被绑定为C#由于约定不采用任何参数和返回值 （非 void 返回） 等属性。 通常这些方法应绑定为属性，以呈现一个更好的 API，但有时出现误报和绑定实际上应该就是一种方法。|
|StronglyTypedNSArray|一个本机`NSArray*`被绑定为`NSObject[]`。 有可能根据预期通过 API 文档 （例如标头文件中的注释） 设置的绑定中的更具强类型数组或通过检查通过测试的数组内容。 例如，NSArray * 包含仅 NSNumber * 作为绑定 instancescan`NSNumber[]`而不是`NSObject[]`。|

你可以快速收到有关提示使用文档`sharpie verify-docs`工具，例如：

```csharp
sharpie verify-docs InferredFromPreceedingTypedef
```

## <a name="related-links"></a>相关链接

- [Xamarin 大学课程：生成一个 Objective C 绑定库](https://university.xamarin.com/classes/track/all#building-an-objective-c-bindings-library)
- [Xamarin 大学课程：生成与目标 Sharpie Objective C 绑定库](https://university.xamarin.com/classes/track/all#build-an-objective-c-bindings-library-with-objective-sharpie)
