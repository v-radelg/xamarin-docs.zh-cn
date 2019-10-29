---
title: 应何时以及如何提交 bug 报告？
description: 本文档介绍何时、在何处以及如何为 bug 报告归档。 它还提供了 bug 报告最佳实践，使工程师能够最好地诊断问题。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 8AD9CFBF-282A-4C1F-95E9-25F21141B052
author: davidortinau
ms.author: daortin
ms.date: 08/01/2018
ms.openlocfilehash: df00eebe682d2d06b99721a2d3c3b90d13454c75
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73014007"
---
# <a name="when-and-how-should-i-file-a-bug-report"></a>应何时以及如何提交 bug 报告？

> [!TIP]
> 使用 Visual Studio 中的 "**报告问题**" 菜单项 &ndash; 这将同时发送诊断信息和 bug 报告以帮助解决问题。
>
> 有关[Visual studio 2019 或 Visual studio 2017](https://docs.microsoft.com/visualstudio/ide/how-to-report-a-problem-with-visual-studio)和[Visual Studio for Mac](https://docs.microsoft.com/visualstudio/mac/report-a-problem)的详细说明。
>
> 你可以在[Visual Studio 开发人员社区](https://developercommunity.visualstudio.com/)网站上搜索现有报表。

## <a name="file-a-bug-if"></a>提交 bug if 。

你有一组步骤，你认为工程师将能够使用这些步骤再现问题。

要么

您可以仔细地描述问题的可见症状，尤其是在您还可以描述与问题相关的一些准确情况时。<sup> [[1]](#note-1)</sup>

## <a name="best-practices-to-help-address-bugs-quickly-and-efficiently"></a>有助于快速有效地解决 bug 的最佳实践

1. <a name="ref-1" />在[Visual Studio 开发人员社区](https://developercommunity.visualstudio.com/)和 Web 中搜索可能直接解决该问题的现有 bug 报告或使用建议。<sup>[[2]](#note-2)</sup><sup>[[3]](#note-3)</sup>

1. <a name="ref-2" />尽可能清晰、简洁地描述问题，其中包括对发生情况和预期发生情况的说明。

1. <a name="ref-3" />包括任何相关的堆栈跟踪、错误消息文本或崩溃日志（如果你使用 "**报告问题**" 功能，则可以自动包含这些内容）。 <sup>[4](#note-4)</sup>

1. <a name="ref-4" />记下作为纯文本出现在屏幕截图附件中的任何重要错误消息。

1. <a name="ref-5" />包含一个小型的独立测试用例，该用例会以尽可能少的代码重现 bug。  如果无法重现全新项目的问题（使用某个内置模板创建），请将演示问题的项目压缩，并将其附加到 bug 报告。  在附加示例项目之前，请尽可能简单。<sup>[[5]](#note-5)</sup><sup>[[6]](#note-6)</sup>

1. <a name="ref-6" />描述遇到 bug 的环境，包括操作系统和 Xamarin 以及所有依赖项的[版本](~/cross-platform/troubleshooting/questions/version-logs.md)。

## <a name="additional-details"></a>其他详细信息

1. <a name="note-1" />[ *^* ](#ref-1)理想情况下，"可见症状" 的描述应包括足够的详细信息，以便其他客户可以确认它们是否看到相同的问题（相同的错误消息、相同的性能下降、同一堆栈跟踪崩溃，_等等_）。 对于 "确切情况"，一个很好的例子就是： "我通常会遇到75% 的问题，但如果我更改这一项，就可以完全避免此问题。" 另一个类似于 "确切情况" 的示例是，如果降级到早期版本的 Xamarin，则会停止该问题。

1. <a name="note-2" />[ *^* ](#ref-2) ，则错误文本的代码段（或任何其他唯一的描述性文本）通常是最佳搜索词。 如果现有 bug 报告未完成，则欢迎您添加详细信息或将新的、更好的 bug 报告添加到其中。

1. <a name="note-3" />[ *^* ](#ref-3)另一个很好的问题是是否为任何 Java、目标 C 或 Swift 应用报告了相同的问题。 如果是这样，则问题很可能是 Android 或 iOS 本身的一部分，而不是 Xamarin 的组成部分。

1. <a name="note-4" />[ *^* ](#ref-4)要包含的信息的几个示例：

    1. 对于生成项目时出现的错误，请在 bug 报告中包含完整的[诊断生成输出](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output)。

    1. 对于在 Visual Studio 中生成或调试 iOS 项目时所发生的错误，请在遇到此错误后运行**帮助 > Xamarin > Zip 日志**，并在 bug 报告中包括生成的 .zip 文件。

    1. 对于 Android 或 iOS 应用中的异常或崩溃，请包含适用于[xamarin 和 Xamarin iOS 应用的相关调试日志](~/cross-platform/troubleshooting/questions/version-logs.md#debug-logs-for-xamarin-apps)。

1. <a name="note-5" />[ *^* ](#ref-5)如果可能遇到特定问题，一种方法是通过将少量文件从原始解决方案添加到全新的解决方案中来重新创建问题。 Xamarin 团队通常能够调查问题，即使在较大的测试用例中（假设有清楚地说明重现步骤），但更简单的测试用例也能使 bug 迅速得到解决。

1. <a name="note-6" />[ *^* ](#ref-6)如果_无法_通过向全新的解决方案添加少量文件来重现该问题，则可以为整个应用程序压缩并附加整个解决方案文件夹。 请删除 `bin`、`obj`、`Components`和 `packages` 文件夹，使 zip 文件更小。 （IDE 和生成过程通常会根据需要还原或重新创建这些文件夹的内容。）你还可以根据需要从项目中删除任意多的代码和资源文件，前提是结果解决方案仍演示了原始问题。
