---
title: 编程在 Xamarin 中的语言支持
description: 本文档介绍了 Xamarin 支持各种编程语言。 此外，介绍了C#， F#，可移植 Visual Basic.NET 和 Razor 模板。
ms.prod: xamarin
ms.assetid: CEE8C464-67D7-45F4-9614-EAEF5217CACC
author: asb3993
ms.author: amburns
ms.date: 02/18/2018
ms.openlocfilehash: 715f63a0be54ba3342bd63c1c76d89656313359a
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61035891"
---
# <a name="programming-language-support-in-xamarin"></a>编程在 Xamarin 中的语言支持

## <a name="c"></a>C# 

###  <a name="async-support-overviewcross-platformplatformasyncmd"></a>[异步支持概述](~/cross-platform/platform/async.md)

第 5 版C#引入了两个新关键字来表示异步操作： async 和 await。 这些关键字可以编写另一个线程中使用任务并行库执行长时间运行操作 （如网络访问权限） 的简单代码，并轻松地访问上完成的结果。 最新版本的 Xamarin.iOS 和 Xamarin.Android 支持 async 和 await-本文档提供了说明和通过 Xamarin 使用新语法的示例。

### <a name="c-6-language-featurescross-platformplatformcsharp-sixmd"></a>[C# 6 语言功能](~/cross-platform/platform/csharp-six.md)

最新版本的C#语言 – 版本 6 – 不断发展的语言具有更少的样本，提高的清晰度和更多的一致性。 更简洁的初始化语法，以及要使用的功能`await`中`catch/finally`块和 null 条件`?`运算符时特别有用。

## <a name="ffsharpindexmd"></a>[F#](fsharp/index.md)

使用构建移动应用F#和 Xamarin。

##  <a name="portable-visual-basicnetcross-platformplatformvisual-basicindexmd"></a>[可移植 Visual Basic.NET](~/cross-platform/platform/visual-basic/index.md)

Visual Studio 支持使用 Visual Basic.NET 然后可以合并到 Xamarin 应用程序的可移植类库的创建。 本文介绍如何创建新的 Visual Basic PCL，然后在示例 Xamarin.iOS、 Xamarin.Android 和 Windows Phone 应用程序使用它。

##  <a name="building-html-views-using-razor-templatescross-platformplatformrazor-html-templatesindexmd"></a>[使用 Razor 模板构建 HTML 视图](~/cross-platform/platform/razor-html-templates/index.md)

借助 Xamarin，开发人员可以使用 Razor 模板化引擎，以及使用 ASP.NET MVC 中，最初引入了C#若要使用 HTML、 Javascript 和 CSS 轻松合并数据，而不必费力手动构建代码中的 HTML 字符串。
本文演示如何通过 Xamarin 使用 Razor 模板适用于 Android 和 iOS。
