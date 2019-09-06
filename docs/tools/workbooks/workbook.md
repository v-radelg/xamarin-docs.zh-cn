---
title: 交互式工作簿
description: 本文档介绍了如何使用 Xamarin Workbooks 创建包含C#试验、教学、培训或浏览代码的活动文档。
ms.prod: xamarin
ms.assetid: B79E5DE9-5389-4691-9AA3-FF4336CE294E
author: conceptdev
ms.author: crdun
ms.date: 03/30/2017
ms.openlocfilehash: 7b3c356460d9427821843dc084b3f306c026ffa0
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70293088"
---
# <a name="interactive-workbooks"></a>交互式工作簿

您可以使用工作簿作为独立于 IDE 的独立应用程序。

若要开始创建新的工作簿，请运行工作簿应用。 如果尚未安装，请访问[安装](~/tools/workbooks/install.md#install)页面。 系统将提示你在所选的平台中创建工作簿，该工作簿会自动连接到代理应用程序，使你可以实时直观显示你的文档。

如果工作簿应用程序已在运行，则可以通过浏览到 "文件" **> "新建**" 来创建新文档。

以后可以在应用程序中保存和打开工作簿。 然后，您可以与其他人共享这些观点、探索新的 Api 或讲授新的概念。

## <a name="code-editing"></a>代码编辑

代码编辑窗口提供代码完成、语法着色、内联实时诊断和多行语句支持。

[![](workbook-images/inspector-0.6.0-repl-small.png "代码编辑窗口提供代码完成、语法着色、内联实时诊断和多行语句支持")](workbook-images/inspector-0.6.0-repl.png#lightbox)

Xamarin Workbooks 保存在一个`.workbook`文件中，该文件是一个 CommonMark 文件，其中包含顶部的一些元数据（有关如何保存工作簿的详细信息，请参阅[工作簿文件类型](#workbooks-files-types)）。

### <a name="nuget-package-support"></a>NuGet 包支持

Xamarin Workbooks 中直接支持许多常用 NuGet 包。 可以通过浏览到**文件 > 添加包**来搜索包。 添加包将自动引入`#r`引用包程序集的语句，使你可以立即使用它们。

使用包引用保存工作簿时，还会保存这些引用。 如果与其他人共享工作簿，则会自动下载引用的包。

工作簿中的 NuGet 包支持有一些已知限制：

- 本机库仅在 iOS 上受支持，并且仅当与托管库链接时才受支持。
- 依赖于`.targets`文件或 PowerShell 脚本的包可能无法按预期方式工作。
- 若要删除或修改包依赖项，请使用文本编辑器编辑工作簿的清单。 正确的包管理正在进行。

### <a name="xamarinforms-support"></a>Xamarin。窗体支持

如果在工作簿中引用 Xamarin NuGet 包，则工作簿应用会将其主视图更改为 Xamarin。基于窗体。 可以通过`Xamarin.Forms.Application.Current.MainPage`访问。

"查看检查器" 选项卡还具有显示 Xamarin. Forms 视图层次结构的特殊支持，可帮助您了解您的布局。

## <a name="rich-text-editing"></a>富文本编辑

你可以使用包含的富文本编辑器来编辑代码周围的文本，如下所示：

![](workbook-images/inspector-0.6.2-editing.gif "使用内置的富文本编辑器编辑代码周围的文本")

### <a name="markdown-authoring"></a>Markdown 创作

工作簿作者有时可能会发现直接编辑工作簿的 CommonMark "源" 会更容易。

请注意，如果随后在工作簿客户端中编辑并保存工作簿，则可能会重新格式化 CommonMark 文本。

请注意，由于用于在工作簿文件中启用 YAML 元数据的 CommonMark 扩展， `---`该扩展是为此目的而保留的。 若要在文本中创建[主题中断](http://spec.commonmark.org/0.27/#thematic-break)，应使用`***`或。 `___` 在工作簿1.2 和更早版本中，应避免使用此类中断，因为保存过程中存在 bug。

### <a name="improvements-in-workbooks-13"></a>工作簿1.3 中的改进

此外，我们还扩展了 Markdown 块引号语法来改善演示。 通过将表情符号添加为块引号中的第一个字符，可以影响引号的背景色：

- `> [!NOTE]`
    > 将呈现为带有蓝色背景的注释
- `> [!IMPORTANT]`
    > 将呈现为带有黄色背景的警告
- `> [!WARNING]`
    > 将呈现为红色背景的问题

您还可以链接到工作簿文档中的标题。 我们会为每个标头生成定位点，并将定位点 ID 作为标题文本处理，如下所示：

1. 标头的大小写小写。
1. 删除字母数字和短划线以外的所有字符。
1. 所有空格均替换为短划线。

这意味着标头（如 "重要标头"）获取的`important-header` id，可以通过`#important-header`在工作簿中插入指向的链接来链接到。

## <a name="document-structure"></a>文档结构

### <a name="cell"></a>芯

表示可执行代码或 markdown 的离散内容单元。 代码单元包含最多四个子组件：

- 编辑器
  - 缓冲区
- 编译器诊断
- 控制台输出
- 执行结果

### <a name="editor"></a>编辑器

单元格的交互式文本组件。 对于代码单元，这是实际的代码编辑器，其中包含语法突出显示，等等。对于 markdown 单元格，这是一个具有上下文相关格式和创作工具栏的富文本内容编辑器。

### <a name="buffer"></a>缓冲区

编辑器的实际文本内容。

### <a name="compiler-diagnostics"></a>编译器诊断

编译代码时生成的任何诊断，仅在请求显式执行时呈现。 显示在单元格编辑器的紧下方。

### <a name="console-output"></a>控制台输出

在单元执行过程中写入到标准输出或标准错误的任何输出。 将以黑色文本呈现标准输出，并以红色文本显示标准错误。

### <a name="execution-results"></a>执行结果

如果结果实际上是通过执行生成的，则单元格的结果的丰富和可能的交互式表示形式将在成功编译后呈现。 在此上下文中，异常被视为结果，因为它们是在实际执行编译时生成的。

## <a name="workbooks-files-types"></a>工作簿文件类型

### <a name="plain-files"></a>普通文件

默认情况下，工作簿另存为包含`.workbook` CommonMark 格式文本的纯文本文件。

### <a name="packages"></a>package

工作簿包是一个名为的目录，其中`.workbook`包含扩展名。
在 Mac 的查找器和 "Xamarin Workbooks 打开" 对话框和 "最近使用的文件" 菜单中，会将此目录识别为文件。

目录必须包含`index.workbook`文件，该文件是将在 Xamarin Workbooks 中加载的实际纯文本工作簿。 该目录还可以包含所需的`index.workbook`资源，包括图像或其他文件。

如果在工作簿`.workbook` 0.99.3 或更高版本中打开从其相同目录引用资源的纯文本文件，则在保存该文件后，它将转换`.workbook`为包。 这一点适用于 Mac 和 Windows。

> [!NOTE]
> Windows 用户将直接打开`package.workbook\index.workbook`该文件，否则包的行为与 Mac 上的行为相同。

### <a name="archives"></a>截至

工作簿包是目录，很难通过 internet 轻松地分发。 解决方案是工作簿存档。 工作簿存档是 zip 压缩的工作簿包，并以`.workbook`扩展名命名。

从工作簿1.1 开始，保存工作簿包时，"保存" 对话框提供了将另存为存档的选项。 工作簿1.0 没有创建或保存存档的内置方法。

在工作簿1.0 中，打开工作簿存档后，它会以透明方式转换为工作簿包，并丢失 zip 文件。 在工作簿1.1 中，zip 文件仍然存在。 用户保存存档时，会将其替换为新的 zip 文件。

您可以通过右键单击工作簿包并选择 "在 Mac 上**压缩**" 或 "**发送到 Windows 上 > 压缩（zipped）文件夹**" 来手动创建工作簿存档。 然后，将 zip 文件重命名为`.workbook`具有文件扩展名。 这仅适用于工作簿包，而不能用于普通工作簿文件。

## <a name="related-links"></a>相关链接

- [欢迎使用工作簿](https://developer.xamarin.com/workbooks/workbooks/getting-started/welcome.workbook)
