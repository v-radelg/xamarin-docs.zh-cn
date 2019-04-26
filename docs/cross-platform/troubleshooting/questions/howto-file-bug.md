---
title: 应何时以及如何提交 bug 报告？
description: 本文档介绍，其中，以及如何以提交 bug 报告。 它还提供了最佳实践，使工程师以便最好地诊断问题的 bug 报告。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 8AD9CFBF-282A-4C1F-95E9-25F21141B052
author: conceptdev
ms.author: crdun
ms.date: 08/01/2018
ms.openlocfilehash: 1224d38a2230fa2f5c7ca08f6e33c5468886c206
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61356982"
---
# <a name="when-and-how-should-i-file-a-bug-report"></a>应何时以及如何提交 bug 报告？

> [!TIP]
> 使用**报告的问题**Visual Studio 中的菜单项&ndash;这会将发送诊断信息，以及 bug 报告以帮助解决此问题。
>
> 有详细介绍了如何[Visual Studio 2019 或 Visual Studio 2017](https://docs.microsoft.com/visualstudio/ide/how-to-report-a-problem-with-visual-studio)并[Visual Studio for Mac](https://docs.microsoft.com/visualstudio/mac/report-a-problem)。
>
> 可以上搜索的现有报表[Visual Studio 开发者社区](https://developercommunity.visualstudio.com/)网站。

## <a name="file-a-bug-if"></a>如果，提交 bug...

你有一组步骤的您认为工程师将能够使用来重现问题。

或

仔细可以描述问题的根源，明显的表现，尤其是如果您还可以描述某些精确的情况下，与问题相关。<sup> [[1]](#note-1)</sup>

## <a name="best-practices-to-help-address-bugs-quickly-and-efficiently"></a>最佳实践，帮助快速有效地解决 bug

1. <a name="ref-1" />搜索[Visual Studio 开发人员社区](https://developercommunity.visualstudio.com/)和现有 web bug 报表或使用情况可能直接解决该问题的建议。<sup>[[2]](#note-2)</sup><sup>[[3]](#note-3)</sup>

1. <a name="ref-2" />描述作为清晰和简洁，包括什么发生了变化，但应发生这种情况的说明的问题。

1. <a name="ref-3" />包括任何相关的堆栈跟踪、 错误消息文本或崩溃日志 (如果您使用**报告问题**功能，这些可以自动包含)。 <sup>[[4]](#note-4)</sup>

1. <a name="ref-4" />记下过出现在屏幕截图附件以纯文本格式的任何重要的错误消息。

1. <a name="ref-5" />包括小型、 独立测试用例用于重现 bug 与为尽可能少的代码。  如果无法再现的问题 （使用内置模板之一创建） 的新项目，然后请压缩演示问题的项目，然后将其附加到 bug 报告。  将其附加之前进行的示例项目一样简单。<sup> [[5]](#note-5)</sup><sup>[[6]](#note-6)</sup>

1. <a name="ref-6" />描述其中遇到 bug，包括操作系统的环境并[版本的 Xamarin](~/cross-platform/troubleshooting/questions/version-logs.md)和任何依赖项。

## <a name="additional-details"></a>更多详细信息

1. <a name="note-1" />[*^*](#ref-1) 理想情况下的"可见症状"说明应包含足够的详细信息，以便其他客户，可以确定它们是否看到相同的问题 (相同的错误消息，相同的性能下降，同一个堆栈跟踪从崩溃，_等。_). 为"精确情况下"，一个很好的示例将是如果您可以指定类似于："我通常按问题 75%的时间，但如果更改此项然后我可以避免此问题完全"。 "精确情况"的另一个类似示例是如果降级到以前版本的 Xamarin 停止问题。

1. <a name="note-2" />[*^*](#ref-2) 如您所料，错误文本 （或任何其他唯一的描述性文本） 的代码段是通常是最佳的搜索词。 如果现有的 bug 报告为不完整，则你也可以添加详细信息或文件的新，更好地 bug 报表。

1. <a name="note-3" />[*^*](#ref-3) 另一个好问题就是相同的问题是否已报告的任何 Java、 OBJECTIVE-C 或 Swift 应用。 如果是这样，则问题是很有可能属于 Android 或 iOS 本身而不是 Xamarin 的一部分。

1. <a name="note-4" />[*^*](#ref-4) 要包括的信息的几个示例：

    1. 生成项目时出现的错误请包括完整[诊断生成输出](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output)bug 报表上。

    1. 生成或调试从 Visual Studio iOS 项目时出现的错误，请运行**帮助 > Xamarin > 压缩日志**后遇到错误，包括在 bug 报告生成的.zip 文件。

    1. 对于异常或崩溃 Android 或 iOS 应用中的，请包含相关[调试 Xamarin.Android 和 Xamarin.iOS 应用的日志](~/cross-platform/troubleshooting/questions/version-logs.md#debug-logs-for-xamarin-apps)。

1. <a name="note-5" />[*^*](#ref-5) 如有可能你遇到的特定问题，一种方法是通过将少量的文件从原始解决方案添加到全新的解决方案中再现问题。 Xamarin 团队通常能够调查甚至上更大的测试用例 （假设清楚地说明了要重现的步骤），但更简单的测试用例提供最佳机会将快速解决 bug 的问题。

1. <a name="note-6" />[*^*](#ref-6) 如果它是_不_可能通过将少量的文件添加到全新的解决方案中再现该问题，然后可以进行压缩并附加整个解决方案文件夹的完整应用程序。 请删除`bin`， `obj`， `Components`，和`packages`文件夹，以更 zip 文件更小。 （在 IDE 和生成过程将通常还原或根据需要重新创建这些文件夹的内容。）只要生成的解决方案仍演示了最初的问题，还可以删除任意数量的代码和资源文件从项目根据需要。
