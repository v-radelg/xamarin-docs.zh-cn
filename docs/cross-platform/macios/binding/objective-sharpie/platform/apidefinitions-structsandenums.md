---
title: ApiDefinitions & StructsAndEnums 文件
description: 本文档介绍了客观 Sharpie 生成的 ApiDefinitions.cs 和 StructsAndEnums.cs 文件。 然后，这些文件将用于访问中C#的目标 C 代码。
ms.prod: xamarin
ms.assetid: AC2087C0-BA54-46D8-B70C-6972941C8F73
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: 950f9149744cb8aa2abaed60ccefb416405ab110
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290777"
---
# <a name="apidefinitions--structsandenums-files"></a>ApiDefinitions & StructsAndEnums 文件

如果客观 Sharpie 已成功运行，则会`Binding/ApiDefinitions.cs`生成`Binding/StructsAndEnums.cs`和文件。
这两个文件将添加到 Visual Studio for Mac 中的绑定项目，或者直接传递`btouch`到`bmac`或工具生成最终绑定。

在*某些*情况下，这些生成的文件可能都是您所需要的，但是，开发人员更频繁地需要手动修改这些生成的文件来修复任何无法由该工具自动处理的问题（例如，使用[ `Verify`特性](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md)）。

一些后续步骤包括：

- **调整名称**：有时，您需要调整方法和类的名称，以匹配 .NET Framework 设计准则。
- **方法或属性**：客观 Sharpie 使用的试探法有时会选取要转换为属性的方法。 此时，您可以确定这是否是预期的行为。
- **挂接事件**：你可以将类与委托类链接，并为这些类自动生成事件。
- **挂钩通知**：不能从纯头文件中提取通知的 API 协定，这将需要到 API 文档的行程。 如果需要强类型的通知，则需要更新结果。
- **API 特选**：此时，你可以选择提供额外的构造函数、添加方法（以允许用于C#初始化构造语法）、运算符重载并在额外定义文件上实现你自己的接口。

请参阅[绑定 API](~/cross-platform/macios/binding/objective-c-libraries.md)说明，了解如何将这些文件放入绑定进程中，如下图所示：

![](apidefinitions-structsandenums-images/binding-flowchart.png "此关系图中显示了绑定过程")

有关这些文件的内容的详细信息，请参阅[绑定类型参考](~/cross-platform/macios/binding/binding-types-reference.md)。

