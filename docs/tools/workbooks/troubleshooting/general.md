---
title: 已知的问题和解决方法
description: 本文档介绍了 Xamarin 工作簿的已知的问题和解决方法。 它讨论 CultureInfo 问题、 JSON 问题和详细信息。
ms.prod: xamarin
ms.assetid: 495958BA-C9C2-4910-9BAD-F48A425208CF
author: lobrien
ms.author: laobri
ms.date: 03/30/2017
ms.openlocfilehash: 221ed97db17da62f513448b6c85d4df205a7cbaf
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61268867"
---
# <a name="known-issues--workarounds"></a>已知的问题和解决方法

## <a name="persistence-of-cultureinfo-across-cells"></a>跨单元格的 CultureInfo 持久性

设置`System.Threading.CurrentThread.CurrentCulture`或`System.Globalization.CultureInfo.CurrentCulture`不会不会保留在工作簿在基于 Mono 的工作簿目标 （Mac、 iOS 和 Android） 上的单元格由于[Mono 的中的 bug `AppContext.SetSwitch` ] [ appcontext-bug]实现.

### <a name="workarounds"></a>问题解决

* 设置应用程序域本地`DefaultThreadCurrentCulture`:
```csharp
using System.Globalization;
CultureInfo.DefaultThreadCurrentCulture = new CultureInfo("de-DE")
```

* 或更新工作簿 1.2.1 或更高版本，这将重写的赋值`System.Threading.CurrentThread.CurrentCulture`和`System.Globalization.CultureInfo.CurrentCulture`提供所需的行为 （解决 Mono bug）。

## <a name="unable-to-use-newtonsoftjson"></a>无法使用 Newtonsoft.Json

### <a name="workaround"></a>解决方法

* 更新到的工作簿 1.2.1，它将安装 Newtonsoft.Json 9.0.1。
  工作簿 1.3，目前处于 alpha 通道，支持版本 10 和更高版本。

### <a name="details"></a>详细信息

Newtonsoft.Json 10 月发布的已升级其依赖于版本的工作簿的冲突提供支持的 Microsoft.CSharp `dynamic`。 在工作簿 1.3 预览版本中，解决此问题，但现在我们一直在解决此问题的特定于版本 9.0.1 钉住 Newtonsoft.Json。

NuGet 包显式根据 Newtonsoft.Json 10 或更高版本仅支持在工作簿 1.3 中目前处于 alpha 通道。

## <a name="code-tooltips-are-blank"></a>代码的工具提示是空白

没有[Monaco 编辑器中的 bug] [ monaco-bug]在 Safari/WebKit，Mac 工作簿应用中使用的会导致代码而无需文本的工具提示呈现。

![](general-images/monaco-signature-help-bug.png)

### <a name="workaround"></a>解决方法

* 单击后显示工具提示将强制要呈现的文本。

* 或将更新为工作簿 1.2.1 或更高版本

[appcontext-bug]: https://bugzilla.xamarin.com/show_bug.cgi?id=54448
[monaco-bug]: https://github.com/Microsoft/monaco-editor/issues/408

## <a name="skiasharp-renderers-are-missing-in-workbooks-13"></a>SkiaSharp 呈现器是在工作簿 1.3 中缺失

从工作簿 1.3 开始，我们删除了我们交付 SkiaSharp 呈现器中以 SkiaSharp 本身提供呈现器，请使用支持的工作簿 0.99.0，我们[SDK](~/tools/workbooks/sdk/index.md)。

### <a name="workaround"></a>解决方法

* 在 NuGet 中的最新版本更新 SkiaSharp。 在撰写本文时，这是 1.57.1。

## <a name="related-links"></a>相关链接

- [报告 Bug](~/tools/workbooks/install.md#reporting-bugs)
