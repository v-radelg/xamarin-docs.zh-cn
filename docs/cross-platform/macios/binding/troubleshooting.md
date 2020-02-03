---
title: 绑定疑难解答
description: 本指南介绍当你难以绑定目标-C 库时应执行的操作。 特别是，在将 null 传递到绑定和报告 bug 时，它将讨论缺少绑定、自变量异常。
ms.prod: xamarin
ms.assetid: 7C65A55C-71FA-46C5-A1B4-955B82559844
author: davidortinau
ms.author: daortin
ms.date: 10/19/2016
ms.openlocfilehash: 8194c369aa0e4f8bb17a1a162354b4f72c6aaa41
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76725330"
---
# <a name="binding-troubleshooting"></a>绑定疑难解答

用于排查 Xamarin 中的 macOS （以前称为 OS X） Api 的绑定的一些技巧。

## <a name="missing-bindings"></a>缺少绑定

虽然 Xamarin 覆盖了很多 Apple Api，但有时你可能需要调用一些尚未包含绑定的 Apple API。 在其他情况下，需要在 Xamarin 绑定范围之外调用第三方 C/目标-C。

如果你正在处理 Apple API，则第一步是让 Xamarin 知道，你已遇到了尚未覆盖的 API 的一部分。 [文件](#reporting-bugs)：记录缺少的 API。 我们使用客户的报告来确定我们接下来要处理的 Api 的优先级。 此外，如果你有企业版或企业版许可证，并且缺少绑定来阻止你的进度，还应按照[支持](https://visualstudio.microsoft.com/vs/support/)中的说明提交票证。 我们无法保证绑定，但在某些情况下，我们可以帮助你解决这一情况。

通知 Xamarin （如果适用）缺少的绑定后，下一步是考虑自己绑定。 我们在[此](~/cross-platform/macios/binding/overview.md)提供了完整的指南，并在[此处](https://brendanzagaeski.appspot.com/xamarin/0002.html)提供了一些非正式文档用于手动包装目标-C 绑定。 如果调用的是 C API，则可以使用C#的 P/Invoke 机制，[此处](https://www.mono-project.com/docs/advanced/pinvoke/)提供了文档。

如果决定自行处理绑定，请注意绑定中的错误可能会在本机运行时中产生各种有趣的故障。 特别要注意的是，中C#的签名与参数数量和每个参数的大小相匹配。 如果不这样做，可能会损坏内存和/或堆栈，并且你可能会立即崩溃，也可能是将来或损坏数据的某个任意点崩溃。

## <a name="argument-exceptions-when-passing-null-to-a-binding"></a>将 null 传递到绑定时的参数异常

虽然 Xamarin 可以为 Apple Api 提供高质量和良好测试的绑定，但有时也会出现错误和错误。 到目前为止，你可能遇到的最常见问题是 API 引发 `ArgumentNullException` 当基础 API 接受 `nil`时传入 null。 定义 API 的本机标头文件通常并不提供足够的信息来了解哪些 Api 接受 nil，哪些 Api 在你传递时将会崩溃。

如果你遇到传入 `null` 会引发 `ArgumentNullException` 但你认为它应正常工作，请执行以下步骤：

1. 请查看 Apple 文档和/或示例，以了解是否可以找到它接受 `nil`证明。 如果你熟悉客观-C，可以编写一个小型测试程序来验证它。
2. 提交[bug](#reporting-bugs)。
3. 能否解决该 bug？ 如果可以避免使用 `null`调用 API，则可以轻松地解决调用的简单 null 检查。
4. 但是，某些 Api 需要传入 null 才能关闭或禁用某些功能。 在这些情况下，您可以通过启动程序集浏览器（请参阅[查找给定选择器C#的成员](~/mac/app-fundamentals/mac-apis.md#finding_selector)）、复制绑定以及删除 null 检查来解决该问题。 如果执行此操作，请确保提交 bug （步骤2），因为复制的绑定不会收到在 Xamarin 中进行的更新和修补程序，因此应将这一点视为一项短期解决方法。

<a name="reporting-bugs"/>

## <a name="reporting-bugs"></a>报告 bug

您的反馈对于我们至关重要。 如果发现 Xamarin 的任何问题：

- 查看 [Xamarin.Mac 论坛](https://forums.xamarin.com/categories/xamarin-mac)
- 搜索[问题存储库](https://github.com/xamarin/xamarin-macios/issues)
- 切换到 GitHub 问题之前，会在 [Bugzilla](https://bugzilla.xamarin.com/describecomponents.cgi) 中跟踪 Xamarin 问题。 请在其中搜索匹配的问题。
- 如果找不到匹配的问题，请在 [GitHub 问题存储库](https://github.com/xamarin/xamarin-macios/issues/new)中提交一个新问题。

GitHub 的问题是完全公开的。 不能隐藏注释或附件。

请尽可能多地包含以下内容：

- 一个重现此问题的简单示例。 在可能的情况下，这非常有用。
- 故障的完整堆栈跟踪。
- 故障周围的 C# 代码。
