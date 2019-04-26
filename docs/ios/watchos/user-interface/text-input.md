---
title: 使用 watchOS 在 Xamarin 中的文本输入
description: 本文档介绍在 Xamarin 中的 watchOS 文本输入。 它讨论 PresentTextInputController 方法、 随意绘制的线条、 纯文本、 表情符号，以及听写。
ms.prod: xamarin
ms.assetid: E9CDF1DE-4233-4C39-99A9-C0AA643D314D
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 2092b12254008936f2c5b6a7d9dd610ff751e802
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61207390"
---
# <a name="working-with-watchos-text-input-in-xamarin"></a>使用 watchOS 在 Xamarin 中的文本输入

Apple Watch 不提供供用户输入文本，键盘，但它支持一些监视友好的替代方案：

- 从文本选项的预定义列表中选择
- 使用 Siri 听写
- 选择表情符号
- Scribble （watchOS 3 中引入） 的按字母手写识别。

模拟器当前不支持听写，但您仍可测试的文本输入控制器的其他选项，如 Scribble，如下所示：

![](text-input-images/textinput-sml.png "测试 scribble 选项")

若要接受的监视应用中的文本输入：

1. 创建预定义的选项的字符串的数组。
2. 调用`PresentTextInputController`借助阵列，是否允许表情符号或不是，和一个`Action`用户已完成时调用。
3. 在完成操作，用于输入结果的测试，并采取相应的措施 （可能设置标签的文本值） 的应用中。

下面的代码段向用户显示三个预定义的选项：

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

如果设置的纯模式，用户可以选择：

- 听写
- Scribble，或
- 从应用程序提供的预定义列表。

[![](text-input-images/plain-scribble-sml.png "听写，Scribble，或从应用程序提供的预定义列表")](text-input-images/plain-scribble.png#lightbox)

始终返回的结果为`NSObject`可强制转换为`string`。

## <a name="emoji"></a>表情符号

有两种类型的表情符号：

- 正则 Unicode 表情符号
- 带动画的图像

当用户选择 Unicode 表情符号时，它是作为字符串返回。

如果选择了动画的图像表情符号`result`在完成处理程序将包含`NSData`对象，其中包含表情符号`UIImage`。

## <a name="accepting-dictation-only"></a>仅接受听写

若要将用户直接向听写屏幕转而不显示任何建议 （或 Scribble 选项）：

- 传递的建议列表中，一个空数组和
- 设置`WatchKit.WKTextInputMode.Plain`。

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

当用户正在说话时，监视屏幕将显示以下屏幕，其中包括文本，它识别 （例如，"is a test"）：

![](text-input-images/dictation.png "当用户正在说话时，监视屏幕显示的文本是识别")

他们一旦按下**完成**按钮将返回的文本。



## <a name="related-links"></a>相关链接

- [Apple 的文本和标签的文档](https://developer.apple.com/library/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/TextandLabels.html)
- [watchOS 3 简介](~/ios/watchos/platform/introduction-to-watchos3/index.md)
