---
title: Xamarin 工作簿编辑器键盘快捷方式
description: 本文档介绍适用于 Xamarin Workbooks 编辑器中的键盘快捷方式。 具体而言，它会返回密钥可用的各种方式查看。
ms.prod: xamarin
ms.assetid: 6375A371-3215-4A7C-B97B-A19E58BE96D6
author: lobrien
ms.author: laobri
ms.date: 03/30/2017
ms.openlocfilehash: 87af9f824117b20250c02a3e070652607626de44
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61341045"
---
# <a name="xamarin-workbooks-editor-keyboard-shortcuts"></a>Xamarin 工作簿编辑器键盘快捷方式

## <a name="the-return-key-and-its-nuances"></a>返回密钥和其细微差别，

下表介绍用于执行代码和创作 markdown 的各种键绑定。 我们已采取谨慎选择合理和一致的熟悉和流畅的键绑定。

|键绑定|代码单元格|Markdown 单元格|
|--- |--- |--- |
|<kbd>Return</kbd>|<p>如果脱字号位于单元格缓冲区的末尾，并且可以成功地分析该单元格，则将执行结果会显示在下方缓冲区，和新的代码单元格将插入和执行单元格之后专注于单元格。</p><p>如果分析操作不成功，将缓冲区中插入新行。 如果分析操作不成功，则不会生成编译器诊断。</p>|<p><kbd>返回</kbd>表现出不同的行为，具体取决于在插入点处的 Markdown 上下文。</p><ul><li>如果将插入符号是 Markdown 代码块中，插入文本的新行。</li><li>如果将插入符号的 Markdown 列表块中，创建一个新列表项或拆分当前列表项。</li><li>如果插入点在 Markdown 块的任何其他类型，创建一个新段落块或拆分当前块。</li></ul>|
|<dl><dt>Mac</dt><dd><kbd>Command‑Return</kbd></dd><dt>Win</dt><dd><kbd>Control‑Return</kbd></dd></dl>|<p>始终尝试解析和执行单元格内容。 如果编译成功，将缓冲区，下面显示结果 （包括执行异常），如果不有任何后续的单元格，将创建并专注于新建一个。</p><p>如果有任何编译错误，将显示诊断和使用插入符号位置保持不变，缓冲区将保留已设定焦点。</p>|将插入，并主要的新的代码单元格的当前 markdown 单元格之后。|
|<dl><dt>Mac</dt><dd><kbd>Command‑Shift‑Return</kbd><dd><dt>Win</dt><dd><kbd>Control‑Shift‑Return</kbd></dd></dl>|将插入，并主要的新的 markdown 单元格的当前单元格之后。|与相同的行为<kbd>返回</kbd>|
|<kbd>Shift‑Return</kbd>|始终将插入新行，而不考虑插入符号位置或内容。|插入在当前的 Markdown 块硬盘分行符。|
