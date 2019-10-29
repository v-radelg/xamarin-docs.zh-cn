---
title: Xamarin 中的编程语言支持
description: 本文档介绍 Xamarin 支持的各种编程语言。 它讨论C# F#了、可移植的视觉 Basic.NET 和 Razor 模板。
ms.prod: xamarin
ms.assetid: CEE8C464-67D7-45F4-9614-EAEF5217CACC
author: davidortinau
ms.author: daortin
ms.date: 02/18/2018
ms.openlocfilehash: db963a38322e809d1aa82c02fbb9ae5cc4a650fc
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73014622"
---
# <a name="programming-language-support-in-xamarin"></a>Xamarin 中的编程语言支持

## <a name="c"></a>C\#

### <a name="async-support-overviewcross-platformplatformasyncmd"></a>[异步支持概述](~/cross-platform/platform/async.md)

版本5中C#引入了两个新关键字来表示异步操作： async 和 await。 通过这些关键字，可以编写简单的代码，该代码利用任务并行库来执行另一个线程中长时间运行的操作（例如网络访问），并在完成后轻松访问结果。 最新版本的 Xamarin 和 Xamarin 支持 async 和 await-本文档提供了说明，并举例说明了如何结合使用新语法和 Xamarin。

### <a name="c-6-language-featurescross-platformplatformcsharp-sixmd"></a>[C# 6 语言功能](~/cross-platform/platform/csharp-six.md)

最新版本的C#语言–版本 6-继续演化语言以降低样本，提高清晰度和一致性。 更清晰的初始化语法，使用 `catch/finally` 块中 `await` 的功能和 null 条件 `?` 运算符特别有用。

## <a name="ffsharpindexmd"></a>[F#](fsharp/index.md)

用F#和 Xamarin 构建移动应用。

## <a name="portable-visual-basicnetcross-platformplatformvisual-basicindexmd"></a>[可移植视觉对象 Basic.NET](~/cross-platform/platform/visual-basic/index.md)

Visual Studio 支持使用 Visual Basic.NET 创建可移植类库，然后可以将该类库并入 Xamarin 应用程序。 本文介绍如何创建新的 Visual Basic PCL，然后在示例 Xamarin. iOS、Xamarin 和 Windows Phone 应用程序中使用它。

## <a name="building-html-views-using-razor-templatescross-platformplatformrazor-html-templatesindexmd"></a>[使用 Razor 模板构建 HTML 视图](~/cross-platform/platform/razor-html-templates/index.md)

Xamarin 使开发人员能够利用 Razor 模板化引擎，该引擎最初随 ASP.NET MVC 一起C#引入，并可轻松地将数据与 HTML、JAVASCRIPT 和 CSS 组合在一起，无需在代码中手动构建 HTML 字符串。
本文演示如何将 Razor 模板用于适用于 Android 和 iOS 的 Xamarin。
