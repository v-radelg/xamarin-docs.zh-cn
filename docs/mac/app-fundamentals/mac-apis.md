---
title: macOS 对于 Xamarin.Mac 开发人员的 Api
description: 本文档介绍如何读取 OBJECTIVE-C 选择器以及如何在 Xamarin.Mac 应用中查找其相应的 C# 方法。
ms.prod: xamarin
ms.assetid: 9F7451FA-E07E-4C7B-B5CF-27AFC157ECDA
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/02/2017
ms.openlocfilehash: c387bbead1ac56d7f4c4c05a79c430302e50aec1
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61085274"
---
# <a name="macos-apis-for-xamarinmac-developers"></a>macOS 对于 Xamarin.Mac 开发人员的 Api

## <a name="overview"></a>概述

对于很多时间使用 Xamarin.Mac 开发，可以认为、 读取和写入在 C# 中，而无需过多地考虑使用基础的 Objective C Api。 但是，有时您需要从 Apple，读取 API 文档转换为您的问题的解决方案从 Stack Overflow 上的答案或向现有示例进行比较。

## <a name="reading-enough-objective-c-to-be-dangerous"></a>读取足够 Objective C 有危险

它有时需要读取 Objective C 定义或方法调用，并将该转换为等效的 C# 方法。 让我们看一看 Objective C 函数定义和分解各个部分。 此方法 (*选择器*Objective C 中) 上可以找到`NSTableView`:

```objc
- (BOOL)canDragRowsWithIndexes:(NSIndexSet *)rowIndexes atPoint:(NSPoint)mouseDownPoint
```

声明可以左到右读取：

- `-`前缀意味着它是一个实例 （非静态） 方法。 + 表示它是一个类 （静态） 方法
- `(BOOL)` 是返回类型 (bool 在 C#)
- `canDragRowsWithIndexes` 是名称的第一部分。
- `(NSIndexSet *)rowIndexes` 是第一个参数并使用它的类型。 第一个参数的格式： `(Type) pararmName`
- `atPoint:(NSPoint)mouseDownPoint` 是第二个参数和它的类型。 第一个之后的每个参数是格式： `selectorPart:(Type) pararmName`
- 此消息选择器的完整名称是： `canDragRowsWithIndexes:atPoint:`。 请注意`:`末尾的非常重要。
- 实际的 Xamarin.Mac C# 绑定是： `bool CanDragRows (NSIndexSet rowIndexes, PointF mouseDownPoint)`

此选择器调用可以读取相同的方式：

```objc
[v canDragRowsWithIndexes:set atPoint:point];
```

- 实例`v`让其`canDragRowsWithIndexes:atPoint`选择器使用两个参数，调用`set`和`point`，传递。
- 在 C# 中，如下所示的方法调用： `x.CanDragRows (set, point);`

<a name="finding_selector" />

## <a name="finding-the-c-member-for-a-given-selector"></a>C# 成员查找给定选择器

现在，你发现您需要调用的 OBJECTIVE-C 的选择器，则下一步映射的到等效的 C# 成员。 有四种方法可以尝试 (继续`NSTableView CanDragRows`示例):

1. 使用自动完成列表快速扫描的内容相同的名称。 由于我们知道它是的一个实例`NSTableView`可以键入：

    - `NSTableView x;`
    - `x.` [ctrl + 空间如果未显示列表）。
    - `CanDrag` [输入]
    - 右键单击方法，请转到声明，以打开程序集浏览器可以比较`Export`属性相关的选择器

2. 搜索整个类绑定。 由于我们知道它是的一个实例`NSTableView`可以键入：

    - `NSTableView x;`
    - 右键单击`NSTableView`，请转到声明到程序集浏览器
    - 搜索相关的选择器

3. 可以使用[Xamarin.Mac API 联机文档](https://docs.microsoft.com/dotnet/api/?view=xamarinmac-3.0)。

4. Miguel 提供了 Xamarin.Mac Api 的"Rosetta Stone"视图[此处](https://tirania.org/tmp/rosetta.html)您可以通过搜索的给定 API。 如果你的 API 不是 AppKit 或特定的 macOS，您可能会那里发现。

<!--
Note: In some cases, the assembly browser can hit a bug where it will open but not jump to the right definition. Keep that tab open, switch back to your source code and try again.
Note: The assembly browser tricks currently only works with Xamarin.Mac Classic. This will be fixed in a future version.
-->
