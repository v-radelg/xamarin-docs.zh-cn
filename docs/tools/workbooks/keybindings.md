---
title: Xamarin Workbooks 编辑器键盘快捷键
description: 本文档介绍可在 Xamarin Workbooks 编辑器中使用的键盘快捷方式。 特别是，它会查看使用 Return 键的各种方式。
ms.prod: xamarin
ms.assetid: 6375A371-3215-4A7C-B97B-A19E58BE96D6
author: davidortinau
ms.author: daortin
ms.date: 03/30/2017
ms.openlocfilehash: f8c76f96ea16b9341d349ca110a3eb39207a4d30
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73019106"
---
# <a name="xamarin-workbooks-editor-keyboard-shortcuts"></a>Xamarin Workbooks 编辑器键盘快捷键

## <a name="the-return-key-and-its-nuances"></a>返回键及其细微差别

下表描述了用于执行代码和创作 markdown 的各种键绑定。 我们非常关心如何选择非常熟悉且一致的键绑定。

|键绑定|代码单元|Markdown 单元|
|--- |--- |--- |
|<kbd>Return</kbd>|<p>如果插入符号位于单元缓冲区的末尾，并且可以成功分析该单元格，则将执行该单元格，结果将显示在缓冲区的下方，并将在执行的单元格之后插入并突出显示新的代码单元。</p><p>如果分析失败，则会在缓冲区中插入新行。 如果分析失败，则不会生成编译器诊断。</p>|<p><kbd>返回</kbd>表现不同的行为，具体取决于插入点处的 Markdown 上下文。</p><ul><li>如果插入符号位于 Markdown 代码块中，则插入文本新行。</li><li>如果插入点位于 Markdown 列表块中，请创建新的列表项或拆分当前列表项。</li><li>如果插入符号位于任何其他类型的 Markdown 块中，请创建新的段落块或拆分当前块。</li></ul>|
|<dl><dt>Mac</dt><dd><kbd>命令-返回</kbd></dd><dt>入选</dt><dd><kbd>控制-返回</kbd></dd></dl>|<p>始终尝试分析和执行单元格内容。 如果编译成功，则结果（包括执行异常）将显示在缓冲区的下方，如果没有后续单元，则将创建一个新的单元格，并使其成为焦点。</p><p>如果存在任何编译错误，将显示诊断，并且缓冲区将保持不变的插入符号位置。</p>|在当前 markdown 单元格之后插入并聚焦新的代码单元。|
|<dl><dt>Mac</dt><dd><kbd>Command-Shift 返回</kbd><dd><dt>入选</dt><dd><kbd>按住 ctrl 键并返回</kbd></dd></dl>|将新的 markdown 单元格插入并聚焦于当前单元格。|与<kbd>Return</kbd>相同的行为|
|<kbd>移位-返回</kbd>|始终插入新行，而不考虑插入符号位置或内容。|在当前 Markdown 块中插入硬换行符。|
