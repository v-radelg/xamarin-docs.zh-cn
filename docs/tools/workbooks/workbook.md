---
title: 交互式工作簿
description: 本文档介绍如何使用 Xamarin 工作簿来创建实时文档包含C#的试验、 教学、 培训或浏览代码。
ms.prod: xamarin
ms.assetid: B79E5DE9-5389-4691-9AA3-FF4336CE294E
author: lobrien
ms.author: laobri
ms.date: 03/30/2017
ms.openlocfilehash: a900d427ad6ac2a0e211ef4f00d2f014b13e5d1c
ms.sourcegitcommit: 58d8bbc19ead3eb535fb8248710d93ba0892e05d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67674407"
---
# <a name="interactive-workbooks"></a>交互式工作簿

您可以作为独立应用程序，单独从 IDE 中使用工作簿。

若要开始创建新的工作簿，请运行该工作簿应用。 如果你尚未已安装此，请访问[安装](~/tools/workbooks/install.md#install)页。 系统将提示你选择，将自动连接到代理应用程序允许您以直观显示实时在文档的平台中创建的工作簿。

如果工作簿应用已在运行，可以通过浏览到创建一个新文档**文件 > 新建**。

可以保存并稍后在应用程序中打开工作簿。 你可以然后与他人共享演示创意，探索新 Api，或讲授新概念。

## <a name="code-editing"></a>代码编辑

代码编辑窗口中提供代码补全、 语法颜色设置、 内联实时诊断和多行语句的支持。

[![](workbook-images/inspector-0.6.0-repl-small.png "代码编辑窗口中提供代码补全、 语法颜色设置、 内联实时诊断和多行语句支持")](workbook-images/inspector-0.6.0-repl.png#lightbox)

Xamarin 工作簿都保存在`.workbook`文件，它是具有一些在顶部的元数据的 CommonMark 文件 (请参阅[工作簿文件类型](#workbooks-files-types)有关可以如何保存工作簿的详细信息)。

### <a name="nuget-package-support"></a>NuGet 包支持

直接在 Xamarin 工作簿中支持许多流行的 NuGet 包。 您可以通过浏览到搜索包**文件 > 添加包**。 添加包将自动显示`#r`引用包程序集，从而可以立即使用这些语句。

时使用的包引用保存工作簿，也保存这些引用。 如果与其他人共享该工作簿，它将自动下载引用的包。

有一些与工作簿中的 NuGet 包支持的已知的限制：

- 本机库都支持仅在 iOS 上，并且只通过托管库进行链接时。
- 包依赖于`.targets`文件或 PowerShell 脚本将可能无法按预期方式工作。
- 若要删除或修改的包依赖关系，请使用文本编辑器编辑工作簿的清单。 正确的包管理即将推出。

### <a name="xamarinforms-support"></a>Xamarin.Forms 支持

如果在您的工作簿中引用的 Xamarin.Forms NuGet 包，工作簿应用程序将更改其主视图，以便基于 Xamarin.Forms。 您可以访问它通过`Xamarin.Forms.Application.Current.MainPage`。

视图检查器选项卡还包含对显示的 Xamarin.Forms 视图层次结构，以帮助您了解布局的特殊支持。

## <a name="rich-text-editing"></a>丰富的文本编辑

使用富文本编辑器包含，如下图所示编写代码，可以编辑文本：

![](workbook-images/inspector-0.6.2-editing.gif "编辑使用内置的富文本编辑器的代码周围的文本")

### <a name="markdown-authoring"></a>Markdown 创作

工作簿作者可能有时更轻松地直接使用其喜爱的编辑器中编辑 CommonMark 工作簿的"源"。

请注意，如果您然后编辑并保存您的工作簿中工作簿客户端，CommonMark 文本可能会重新设置格式。

请注意，由于 CommonMark 扩展我们使用以启用 YAML 元数据工作簿文件中的`---`已保留用于此目的。 如果你想要创建[主题的分页符](http://spec.commonmark.org/0.27/#thematic-break)在文本中，您应使用`***`或`___`相反。 应避免使用此类中断，在工作簿 1.2 和前面由于期间保存的 bug。

### <a name="improvements-in-workbooks-13"></a>工作簿 1.3 中的改进

我们还扩展的 Markdown 块引号语法稍有提高演示文稿。 通过将表情符号添加为在块引用中的第一个字符，您可以影响报价的背景色：

- `> [!NOTE]`
    > 将呈现为具有蓝色背景的注释
- `> [!IMPORTANT]`
    > 将呈现为具有黄色背景的警告
- `> [!WARNING]`
    > 将呈现为红色背景的问题

此外可以链接到工作簿文档中的标头。 我们正在处理，如下所示的标头文本锚点 ID 与生成每个标头的定位的点：

1. 标头是小写。
1. 删除所有字符的字母数字和短划线除外。
1. 所有空格被都替换短划线。

这意味着，一个标头"重要标头"获取 id 为像`important-header`，并且可以通过插入到链接链接到`#important-header`工作簿中。

## <a name="document-structure"></a>文档结构

### <a name="cell"></a>单元格

表示可执行代码或 markdown 离散单元的内容。 代码单元格构成最多四个子组件：

- 编辑器
  - 缓冲区
- 编译器诊断
- 控制台输出
- 执行结果

### <a name="editor"></a>编辑器

单元格的交互式文本组件。 对于代码单元格，这是具有语法突出显示等的实际代码编辑器。对于 markdown 的单元格是一个富文本内容编辑器的上下文相关的格式设置以及编写工具栏。

### <a name="buffer"></a>缓冲区

编辑器的实际文本内容。

### <a name="compiler-diagnostics"></a>编译器诊断

编译代码时，呈现只在需要显式的执行时，任何诊断生成。 立即显示在单元格编辑器下方。

### <a name="console-output"></a>控制台输出

任何在单元格的执行期间写入到标准输出或标准错误输出。 将以黑色文本呈现标准输出和标准错误将以红色文本呈现。

### <a name="execution-results"></a>执行结果

提供由执行实际生成一个结果，将在成功编译完成后呈现的单元格的结果的丰富且可能交互式表示形式。 异常认为是在此上下文中的结果，因为它们生成实际执行编译结果。

## <a name="workbooks-files-types"></a>工作簿文件类型

### <a name="plain-files"></a>普通文件

默认情况下，工作簿将保存为纯文本`.workbook`文件包含 CommonMark 格式的文本。

### <a name="packages"></a>package

工作簿包是一个目录，其中具有名为`.workbook`扩展。
在 Mac 的查找器和 Xamarin 工作簿打开对话框和最新的文件菜单中，就像一个文件将识别此目录。

该目录必须包含`index.workbook`文件，它是实际的纯文本工作簿将在 Xamarin 工作簿中加载。 该目录还包含所需的资源`index.workbook`，包括图像或其他文件。

如果纯文本`.workbook`在工作簿 0.99.3 中打开其同一目录中引用资源的文件或更高版本，它保存时，它将转换为`.workbook`包。 这是在 Mac 和 Windows 上，则返回 true。

> [!NOTE]
> Windows 用户将会打开`package.workbook\index.workbook`文件直接，但包将行为的 mac 上相同

### <a name="archives"></a>存档

工作簿包，不目录，可能很难通过 internet 轻松地分发。 解决方法是工作簿存档。 工作簿存档是 zip 压缩工作簿包，创建名为`.workbook`扩展。

在工作簿 1.1 中，从开始，保存工作簿包时，保存对话框中提供改为将保存为存档的选项。 工作簿 1.0 必须创建或保存存档的内置的方法。

在工作簿 1.0 中，打开工作簿存档时，它以透明方式转换到工作簿包，和 zip 文件已丢失。 在工作簿 1.1 中，保持该 zip 文件。 当用户保存存档时，用新的 zip 文件代替它。

您可以通过右键单击工作簿包并选择手动创建一个工作簿的存档**压缩**在 Mac 上，或**发送到 > 压缩 (zipped) 文件夹**在 Windows 上。 然后重命名 zip 文件具有`.workbook`文件扩展名。 这仅适用于工作簿的包，不纯的工作簿文件。

## <a name="related-links"></a>相关链接

- [欢迎使用工作簿](https://developer.xamarin.com/workbooks/workbooks/getting-started/welcome.workbook)
