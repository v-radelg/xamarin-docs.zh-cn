---
title: 在 Xamarin 中使用 watchOS 文本输入
description: 本文档介绍 Xamarin 中的 watchOS 文本输入。 它讨论了 PresentTextInputController 方法、scribbling、纯文本、表情符号和听写。
ms.prod: xamarin
ms.assetid: E9CDF1DE-4233-4C39-99A9-C0AA643D314D
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/17/2017
ms.openlocfilehash: a0e45c51ba5460da87b80f21d4e9e54c13deabde
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70766780"
---
# <a name="working-with-watchos-text-input-in-xamarin"></a>在 Xamarin 中使用 watchOS 文本输入

Apple Watch 不会为用户提供用于输入文本的键盘，但它确实支持一些便于查看的替代方案：

- 从预定义的文本选项列表中进行选择，
- Siri 听写，
- 选择表情符号，
- 自由曲线字母手写识别（在 watchOS 3 中引入）。

模拟器目前不支持听写，但你仍可以测试文本输入控制器的其他选项，如 "自由曲线"，如下所示：

![](text-input-images/textinput-sml.png "测试自由曲线选项")

接受 watch 应用中的文本输入：

1. 创建预定义选项的字符串数组。
2. 使用`PresentTextInputController`数组调用，无论是否允许使用表情符号， `Action`以及用户完成时调用的。
3. 在完成操作中，测试输入结果并在应用中采取适当的操作（可能会设置标签的文本值）。

以下代码片段向用户提供三个预定义选项：

```csharp
var suggest = new string[] {"Get groceries", "Buy gas", "Post letter"};

PresentTextInputController (suggest, WatchKit.WKTextInputMode.AllowEmoji, (result) => {
    // action when the "text input" is complete
    if (result != null && result.Count > 0) {
    // this only works if result is a text response (Plain or AllowEmoji)
        enteredText = result.GetItem<NSObject>(0).ToString();
        Console.WriteLine (enteredText);
        // do something, such as myLabel.SetText(enteredText);
    }
});
```

`WKTextInputMode`枚举具有三个值：

- 纯
- AllowEmoji
- AllowAnimatedEmoji

## <a name="plain"></a>纯

设置纯模式后，用户可以选择：

- 听写
- 自由曲线或
- 从应用程序提供的预定义列表中。

[![](text-input-images/plain-scribble-sml.png "听写、自由曲线或应用提供的预定义列表")](text-input-images/plain-scribble.png#lightbox)

结果始终作为`NSObject`可强制转换`string`为的返回。

## <a name="emoji"></a>表情符号

有两种类型的表情符号：

- 常规 Unicode 表情符号
- 动画图像

当用户选择 Unicode 表情符号时，它将作为字符串返回。

如果选择了动画图像表情符号， `result`则在完成处理程序中将`NSData`包含包含表情符号`UIImage`的对象。

## <a name="accepting-dictation-only"></a>仅接受听写

在不显示任何建议（或自由曲线选项）的情况下，直接将用户带到听写屏幕：

- 为建议列表传递一个空数组，并
- 集`WatchKit.WKTextInputMode.Plain`。

```csharp
PresentTextInputController (new string[0], WatchKit.WKTextInputMode.Plain, (result) => {
    // action when the "text input" is complete
    if (result != null && result.Count > 0) {
        dictatedText = result.GetItem<NSObject>(0).ToString();
        Console.WriteLine (dictatedText);
        // do something, such as myLabel.SetText(dictatedText);
    }
});
```

用户讲话时，"监视" 屏幕会显示以下屏幕，其中包括所理解的文本（例如 "这是一项测试"）：

![](text-input-images/dictation.png "用户讲话时，\"监视\" 屏幕会显示其内容，")

用户按 "**完成**" 按钮后，将返回文本。

## <a name="related-links"></a>相关链接

- [Apple 的文本和标签文档](https://developer.apple.com/library/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/TextandLabels.html)
- [watchOS 3 简介](~/ios/watchos/platform/introduction-to-watchos3/index.md)
