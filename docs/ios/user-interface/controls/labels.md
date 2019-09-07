---
title: Xamarin 中的标签
description: 本文档介绍如何在 Xamarin 中使用标签。 本文介绍如何以编程方式和 iOS 设计器创建标签。
ms.prod: xamarin
ms.assetid: 54DA1221-13E4-4D45-B263-5F22A0AC7B53
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 07/11/2017
ms.openlocfilehash: 275acd02e6cece0842344d8e5833c23af1014a0f
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70768952"
---
# <a name="labels-in-xamarinios"></a>Xamarin 中的标签

`UILabel`控件用于显示单行和多行只读文本。

## <a name="implementing-a-label"></a>实现标签

通过实例化[`UILabel`](xref:UIKit.UILabel)创建新标签：

```csharp
UILabel label = new UILabel();
```

### <a name="labels-and-storyboards"></a>标签和情节提要

使用 iOS 设计器时，还可以向 UI 添加标签。 在 "**工具箱**" 中搜索 "**标签**" 并将其拖到您的视图：

![工具箱中的标签](labels-images/image3.png)

可以在 "属性" 板上调整以下属性：

![标签属性面板](labels-images/image2.png)

- **文本上下文**-无格式或特性化。 纯文本允许您设置整个字符串的[格式属性](#Formatting_Text_and_Label)。 特性化文本允许您将格式设置设置为字符串中的不同字符或单词。
- 可应用于标签的**颜色、字体、对齐方式**。
- **行**–设置标签可跨越的行数。 将此设置为0，以允许标签根据需要使用尽可能多的行。
- **行为**–可设置为 "已启用" 或 "突出显示"。 默认设置为 "已启用"，则禁用的文本将以较浅的灰色颜色显示。 默认情况下，突出显示处于禁用状态，并允许在用户选择标签时使用突出显示状态重新绘制标签。
- **Baselane 和分行符**–
  - 如果字号不同于指定的字号，基准内会确定文本的放置方式。
  - 如果字符串的长度超过单行，则分行符确定如何将其换行或截断。
- 自动**收缩**–根据需要确定如何在标签内最大程度地减小字体大小。
- **突出显示、阴影、偏移量**–允许您设置突出和阴影颜色以及阴影偏移量。

## <a name="truncating-and-wrapping"></a>截断和包装

有关在 iOS 中使用分行符的信息，请参阅 "[截断和折行文本](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/labels/uilabel-truncate-wrap-text)" 食谱。

<a name="Formatting_Text_and_Label"/>

## <a name="formatting-text-and-label"></a>设置文本和标签的格式

若要设置在标签中使用的字符串的格式，可以设置整个字符串的格式设置特性，也可以使用特性化字符串。 下面的示例演示如何实现这些：

```csharp
label = new UILabel(){
                Text = "Hello, this is a string",
                Font = UIFont.FromName("Papyrus", 20f),
                TextColor = UIColor.Magenta,
                TextAlignment = UITextAlignment.Center
            };
```

```csharp
label.AttributedText = new NSAttributedString(
                "This is some formatted text",
                font: UIFont.FromName("GillSans", 16.0f),
                foregroundColor: UIColor.Blue,
                backgroundColor: UIColor.White
            );
```

有关使用`NSAttributedString`设置文本样式的详细信息，请参阅[样式文本](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/text_field/style_text)食谱。

默认情况下，" `Enabled`标签" 设置为 "true"，但可以将其设置为 "已禁用" 以向用户提示某个控件被禁用：

```csharp
label.Enabled = false;
```

这会将标签设置为浅灰色，如 iOS 中限制屏幕的下面的示例图像中所示：

![IOS 中的禁用按钮](labels-images/image1.png)

你还可以将突出显示和阴影文本颜色设置为标签文本，以了解其他效果：

```csharp
label.Highlighted = true;
label.HighlightedTextColor = UIColor.Cyan;

label.ShadowColor = UIColor.Black;
label.ShadowOffset = new CoreGraphics.CGSize(1.0f, 1.0f);
```

这会显示如下所示的文本：

![文本上的突出显示和阴影设置](labels-images/image4.png)

有关更改 UILabel 字体的详细信息，请参阅[更改字体](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/labels/change_the_font)食谱。
