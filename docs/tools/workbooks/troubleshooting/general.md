---
title: 已知问题 & 解决方法
description: 本文档介绍 Xamarin Workbooks 的已知问题和解决方法。 它讨论了 CultureInfo 问题、JSON 问题等。
ms.prod: xamarin
ms.assetid: 495958BA-C9C2-4910-9BAD-F48A425208CF
author: lobrien
ms.author: laobri
ms.date: 03/30/2017
ms.openlocfilehash: 21f61b4504367dafc2907fd6471af333f636b521
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2019
ms.locfileid: "69889405"
---
# <a name="known-issues--workarounds"></a>已知问题 & 解决方法

## <a name="persistence-of-cultureinfo-across-cells"></a>单元格之间的 CultureInfo 持久性

由于`System.Threading.CurrentThread.CurrentCulture` [mono 的实现中的 bug `AppContext.SetSwitch` ][appcontext-bug] , 在基于 mono 的工作簿目标 (Mac、iOS 和 Android) 上, 对工作簿单元格的设置或`System.Globalization.CultureInfo.CurrentCulture`不会保持不变。

### <a name="workarounds"></a>问题解决

* 设置应用程序-域本地`DefaultThreadCurrentCulture`:

```csharp
using System.Globalization;
CultureInfo.DefaultThreadCurrentCulture = new CultureInfo("de-DE")
```

* 或者, 更新为1.2.1 或更高版本的工作簿, 这`System.Threading.CurrentThread.CurrentCulture`将`System.Globalization.CultureInfo.CurrentCulture`重写对和的分配, 以提供所需的行为 (围绕 Mono 错误)。

## <a name="unable-to-use-newtonsoftjson"></a>无法使用 Newtonsoft.json

### <a name="workaround"></a>解决方法

* 更新到将安装 Newtonsoft.json 9.0.1 的工作簿1.2.1。
  当前在 alpha 通道中的工作簿1.3 支持版本10和更高版本。

### <a name="details"></a>详细信息

Newtonsoft.json 发布了已升级依赖于支持`dynamic`的版本工作簿的依赖项。 工作簿1.3 预览版本中对此进行了说明, 但现在, 我们通过将 Newtonsoft.json 专门固定到9.0.1 版来解决此问题。

根据 Newtonsoft.json 10 或更高版本, 仅支持在当前 alpha 通道中的工作簿1.3 中显式启用 NuGet 包。

## <a name="code-tooltips-are-blank"></a>代码工具提示为空

在用于 Mac 工作簿应用程序的 "Safari/WebKit" 中, 在["摩纳哥编辑器"][monaco-bug]中有一个 bug, 该 bug 会导致不带文本的代码工具提示呈现。

![](general-images/monaco-signature-help-bug.png)

### <a name="workaround"></a>解决方法

* 在工具提示显示后单击它将强制呈现文本。

* 或更新到1.2.1 或更高版本的工作簿

[appcontext-bug]: https://bugzilla.xamarin.com/show_bug.cgi?id=54448
[monaco-bug]: https://github.com/Microsoft/monaco-editor/issues/408

## <a name="skiasharp-renderers-are-missing-in-workbooks-13"></a>工作簿1.3 中缺少 SkiaSharp 呈现器

从工作簿1.3 开始, 我们移除了在工作簿0.99.0 中提供的 SkiaSharp 呈现器, 并使用我们的[SDK](~/tools/workbooks/sdk/index.md)来支持提供呈现器的 SkiaSharp。

### <a name="workaround"></a>解决方法

* 将 SkiaSharp 更新为 NuGet 中的最新版本。 撰写本文时, 这是1.57.1 的。

## <a name="related-links"></a>相关链接

- [报告 Bug](~/tools/workbooks/install.md#reporting-bugs)
