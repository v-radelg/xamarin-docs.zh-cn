---
title: 绑定疑难解答
description: 本指南介绍了要执行的操作如果无法绑定 OBJECTIVE-C 库。 具体而言，它讨论了缺少绑定，将 null 传递给绑定和报告 bug 时的自变量异常。
ms.prod: xamarin
ms.assetid: 7C65A55C-71FA-46C5-A1B4-955B82559844
author: asb3993
ms.author: amburns
ms.date: 10/19/2016
ms.openlocfilehash: 686ce3b46f268b650c63cace2a29b9d501a4063c
ms.sourcegitcommit: bf18425f97b48661ab6b775195eac76b356eeba0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2019
ms.locfileid: "64977599"
---
# <a name="binding-troubleshooting"></a>绑定疑难解答

有关故障排除绑定向 macOS （以前称为 OS X） 的一些提示在 Xamarin.Mac 中的 Api。

## <a name="missing-bindings"></a>缺少绑定

Xamarin.Mac 涵盖了许多 Apple Api，而有时您可能需要调用一些不具有绑定的 Apple API 尚未。 在其他情况下，您需要调用第三方 C/Objective C 范围外的 Xamarin.Mac 绑定它。

如果处理的 Apple API，第一步是让 Xamarin 知道，你已达到 API 的部分，我们尚未针对没有覆盖率。 [提交 bug](#reporting-bugs)注意是缺少 API。 我们使用客户的报告来确定哪些 Api 下一步我们工作的优先级。 此外，如果您具有的业务或企业版许可证，并且缺乏一个绑定阻止您的进度，还请遵循的说明[支持](http://xamarin.com/support)来提交票证。 我们不能保证一个绑定，但在某些情况下我们可以避开您工作。

缺少绑定的 （如果适用） 通知 Xamarin 下, 一步是要考虑自己绑定。 我们有完整指南[这里](~/cross-platform/macios/binding/overview.md)和一些非官方文档[此处](http://brendanzagaeski.appspot.com/xamarin/0002.html)手动包装 OBJECTIVE-C 绑定。 如果正在调用 C API，则可以使用C#的文档是 P/Invoke 机制[此处](https://www.mono-project.com/docs/advanced/pinvoke/)。

如果你决定要在绑定上你自己，请注意在绑定中的错误，可以生成各种类型的本机运行时中的有趣崩溃。 具体而言，要非常小心，在签名中的C#中的参数数目和每个自变量的大小的本机签名匹配。 如果不这样做可能会损坏内存和/或堆栈并可能在将来崩溃立即或在某个任意时间点或损坏的数据。

## <a name="argument-exceptions-when-passing-null-to-a-binding"></a>将 null 传递到绑定时的自变量异常

尽管 Xamarin 工作以提供高质量和经过充分测试的绑定适用于 Apple 的 Api，有时错误和错误名单中。 到目前为止最常见的可能会遇到的问题是 API 引发`ArgumentNullException`传递空值时的基础 API 接受`nil`。 通常定义 API 的本机头文件不提供足够的 API 接受 nil 和其中将崩溃将其传递的信息。

如果遇到一种情况其中传入`null`引发`ArgumentNullException`但认为它应起作用，请执行以下步骤：

1. 检查 Apple 文档和/或示例，以看看是否可以找到它接受的证明`nil`。 如果您习惯使用 Objective C，可以编写一个小测试程序，以验证它。
2. [提交 bug](#reporting-bugs)。
3. 可以解决该 bug？ 如果可以避免调用的 API `null`，调用周围的简单 null 检查可以轻松解决办法。
4. 但是，有些 Api 需要传入 null 来关闭或禁用某些功能。 在这些情况下，您可以解决此问题通过将程序集浏览器 (请参阅[查找C#给定选择器成员](~/mac/app-fundamentals/mac-apis.md#finding_selector))，将绑定和 null 检查中删除复制。 请确保要提交 bug （步骤 2） 如果复制的绑定不会接收更新和修补程序，我们使在 Xamarin.Mac、 执行此操作，并且应将此视为短时间内的解决办法。

<a name="reporting-bugs"/>

## <a name="reporting-bugs"></a>报告 bug

你的反馈对我们很重要。 如果发现任何问题与 Xamarin.Mac:

- 查看 [Xamarin.Mac 论坛](https://forums.xamarin.com/categories/mac)
- 搜索[问题存储库](https://github.com/xamarin/xamarin-macios/issues) 
- 切换到 GitHub 问题之前，会在 [Bugzilla](https://bugzilla.xamarin.com/describecomponents.cgi) 中跟踪 Xamarin 问题。 请在其中搜索匹配的问题。
- 如果找不到匹配的问题，请在 [GitHub 问题存储库](https://github.com/xamarin/xamarin-macios/issues/new)中提交一个新问题。

GitHub 的问题是完全公开的。 不能隐藏注释或附件。 

请尽可能多地包含以下内容：

- 一个重现此问题的简单示例。 在可能的情况下，这非常有用。 
- 故障的完整堆栈跟踪。
- 故障周围的 C# 代码。
