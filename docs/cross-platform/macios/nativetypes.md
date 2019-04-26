---
title: 适用于 iOS 和 macOS 的本机类型
description: 本文档介绍了 Xamarin 的统一 API 如何将.NET 类型映射到 32 位和 64 位本机类型，根据需要基于编译目标体系结构。
ms.prod: xamarin
ms.assetid: B5237770-0FC3-4B01-9E22-766B35C9A952
author: asb3993
ms.author: amburns
ms.date: 01/25/2016
ms.openlocfilehash: fc2b91a9265fcf09e4f58d5de27a1fdef9350b2d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61199620"
---
# <a name="native-types-for-ios-and-macos"></a>适用于 iOS 和 macOS 的本机类型

Mac 和 iOS Api 使用特定于体系结构的数据类型是始终在 32 位平台上和 64 位平台上的 64 位的 32 位的。

例如，映射 Objective C`NSInteger`数据类型设置为`int32_t`32 位系统上和`int64_t`64 位系统上。

若要匹配此行为，请在我们统一的 API，我们要替换的上一次使用`int`(在.NET 中被定义为始终`System.Int32`) 到新的数据类型： `System.nint`。 您可以将"n"是有意义"本机"，因此平台的本机整数类型。

使用这些新的数据类型相同的源代码编译为 32 位和 64 位体系结构，具体取决于编译标记。

## <a name="new-data-types"></a>新数据类型

下表显示我们的数据类型，以匹配这个新的 32/64 位世界中所做的更改：

|本机类型|32 位后备类型|64 位后备类型|
|--- |--- |--- |
|`System.nint`|`System.Int32` (`int`)|`System.Int64` (`long`)|
|`System.nuint`|`System.UInt32` (`uint`)|`System.UInt64` (`ulong`)|
|`System.nfloat`|`System.Single` (`float`)|`System.Double` (`double`)|

我们选择允许这些名称在C#代码以查找更多或更少看起来是今天的相同方法。

### <a name="implicit-and-explicit-conversions"></a>隐式转换和显式转换

新的数据类型的设计为了让单个C#源文件自然地使用 32 或 64 位存储，具体取决于主机平台和编译设置。

这需要我们可以设计一组的隐式和显式转换与特定于平台的数据类型为.NET 整型和浮点数据类型。

不可能有数据丢失 （存储在 64 位空间上的 32 位值） 时，提供隐式转换运算符。

可能发生了数据丢失时提供显式转换运算符 （64 位值存储在 32 或 32 个可能的存储位置上）。

 `int``uint`并`float`都隐式转换为`nint`，`nuint`和`nfloat`32 位将始终能放入 32 或 64 位。

 `nint``nuint`并`nfloat`都隐式转换为`long`，`ulong`和`double`32 或 64 位值将始终能放在 64 位存储中。

必须使用从的显式转换`nint`，`nuint`并`nfloat`到`int`，`uint`和`float`由于本机类型可能占用的存储空间的 64 位。

必须使用从的显式转换`long`，`ulong`并`double`到`nint`，`nuint`和`nfloat`由于本机类型可能仅能容纳 32 位的存储。

## <a name="coregraphics-types"></a>CoreGraphics 类型

与 CoreGraphics 一起使用的点、 大小和矩形数据类型使用 32 或 64 位，具体取决于设备运行。  当我们最初绑定 iOS 和 Mac Api 时我们使用现有相匹配的主机平台大小的数据结构 (中的数据类型`System.Drawing`)。

移到时**统一**，将需要的实例替换为`System.Drawing`使用其`CoreGraphics`对应的以下表中所示：

|System.Drawing 中的旧类型|新的数据类型 CoreGraphics|描述|
|--- |--- |--- |
|`RectangleF`|`CGRect`|浮动的保存点的矩形的信息。|
|`SizeF`|`CGSize`|浮动的保存点 （宽度、 高度） 的大小信息|
|`PointF`|`CGPoint`|保存浮点型、 点信息 (X，Y)|

旧数据类型使用浮点数来存储的元素的数据结构，而新的其中一个使用`System.nfloat`。

## <a name="related-links"></a>相关链接

- [使用跨平台应用中的本机类型](~/cross-platform/macios/native-types-cross-platform.md)
- [经典 vs 统一的 API 差异](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
