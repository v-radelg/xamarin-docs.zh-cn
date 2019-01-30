---
title: Xamarin.iOS 中的标签
description: 本文档讨论了如何在 Xamarin.iOS 中使用标签。 它介绍如何以编程方式和使用 iOS 设计器创建标签。
ms.prod: xamarin
ms.assetid: 54DA1221-13E4-4D45-B263-5F22A0AC7B53
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/11/2017
ms.openlocfilehash: cca74ac74e5077822193f6dd97a69f8d9b823561
ms.sourcegitcommit: a1a58afea68912c79d16a3f64de9a0c1feb2aeb4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2019
ms.locfileid: "55233284"
---
# <a name="labels-in-xamarinios"></a>Xamarin.iOS 中的标签

`UILabel`控件用于显示单个和多行，读取纯文本。 

## <a name="implementing-a-label"></a>实现一个标签

通过实例化来创建新标签[ `UILabel` ](xref:UIKit.UILabel):

```csharp
UILabel label = new UILabel();
```

### <a name="labels-and-storyboards"></a>标签和情节提要

使用 iOS 设计器时，还可以将标签添加到你的 UI。 搜索**标签**中**工具箱**并将其拖到视图：

![中的工具箱标签](labels-images/image3.png)

在属性面板上，可以调整以下属性：

![标签属性面板](labels-images/image2.png)

- **文本上下文**– 纯文本或特性化。 纯文本，可设置[格式设置特性](#Formatting_Text_and_Label)针对整个字符串。 特性化的文本，可设置格式设置为不同的字符或单词在字符串中。
- **颜色、 字体、 对齐方式**– 可以应用于该标签的格式设置属性。
- **行**– 设置标签可以跨越的行数。 将此项设置为 0，以允许使用所需的所有行的标签。
- **行为**– 可设置为已启用或突出显示。 启用是默认设置，已禁用的文本将显示在较浅灰色。 突出显示默认处于禁用状态，并允许标签时用户选择重绘与突出显示状态。
- **Baselane 和分行符**– 
    - 基准内确定如何将文本定位如果字体大小不同于指定的一个。
    - 换行确定如何包装或如果它的长度大于单个行被截断字符串。
- **自动收缩**– 确定如何调整大小的字体将最小化内一个标签，如有必要。
- **突出显示，卷影，偏移量**– 允许您设置处于突出显示状态和阴影颜色和阴影偏移量。

## <a name="truncating-and-wrapping"></a>截断结果和包装

有关使用行的信息将在 iOS 中中断，请参阅[截断和文本换行](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/labels/uilabel-truncate-wrap-text)方案。

<a name="Formatting_Text_and_Label"/>

## <a name="formatting-text-and-label"></a>设置文本格式和标签

若要设置在标签中使用的字符串的格式可以设置整个字符串的属性的格式设置或可以使用特性化的字符串。 以下示例演示如何实现这些：

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

有关样式文本使用的详细信息`NSAttributedString`是指[文本的样式](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/text_field/style_text)方案。

默认情况下有标签`Enabled`设置为 true，但是它可以将其设置为禁用以授予用户某些控件处于禁用状态的提示：

```csharp
label.Enabled = false;
```

限制屏幕在 iOS 中的以下示例图中所示，此值设置为浅灰色标签：

![在 iOS 中的禁用的按钮](labels-images/image1.png)

此外可以向其他效果的标签文本设置突出显示和阴影的文本颜色：

```csharp
label.Highlighted = true;
label.HighlightedTextColor = UIColor.Cyan;

label.ShadowColor = UIColor.Black;
label.ShadowOffset = new CoreGraphics.CGSize(1.0f, 1.0f);
```

这将显示如下文本：

![突出显示和设置在文本上的卷影](labels-images/image4.png)

有关更改 UILabel 的字体的详细信息，请参阅[更改字体](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/labels/change_the_font)方案。





