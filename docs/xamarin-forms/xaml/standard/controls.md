---
title: XAML 标准 （预览） 控件
description: 本文探讨了可用于 Xamarin.Forms 的 XAML 标准控件。
ms.prod: xamarin
ms.assetid: 287E6631-D1C5-46C5-8905-AB53D34E365D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/15/2017
ms.openlocfilehash: b9bf0e1ba14f4e8584bfd8492776ac7c8668df87
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61175173"
---
# <a name="xaml-standard-preview-controls"></a>XAML 标准 （预览） 控件

![预览](~/media/shared/preview.png)

此页列出了在预览版中，其等效的 Xamarin.Forms 控件一起提供的 XAML 标准控件。

此外，还有在 XAML 标准了新的属性和枚举名称的控件的列表。

## <a name="controls"></a>Controls

|Xamarin.Forms|XAML 标准|
|--- |--- |
|Frame|Border|
|选取器|组合框|
|ActivityIndicator|ProgressRing|
|StackLayout|StackPanel|
|Label|TextBlock|
|条目|文本框|
|开关|ToggleSwitch|
|ContentView|用户控件|


## <a name="properties-and-enumerations"></a>属性和枚举

|具有更新的属性的 Xamarin.Forms 控件|Xamarin.Forms 属性或枚举|XAML 标准等效项|
|--- |--- |--- |
|按钮、 入口、 标签、 DatePicker、 编辑器、 SearchBar、 TimePicker|TextColor|Foreground|
|VisualElement|BackgroundColor|背景 *|
|选取器中按钮|边框颜色 OutlineColor|BorderBrush|
|Button|BorderWidth|BorderThickness|
|ProgressBar|进度|“值”|
|按钮、 入口、 标签、 编辑器、 SearchBar、 范围、 字体|FontAttributesBold、 斜体、 无|FontStyleItalic 正常|
|按钮、 入口、 标签、 编辑器、 SearchBar、 范围、 字体|FontAttributes|FontWeights * 加粗、 正常|
|InputView|KeyboardDefault、 Url、 数字、 电话、 文本、 聊天、 电子邮件|InputScopeNameValue * 默认值、 Url、 数字、 TelephoneNumber、 文本、 聊天、 EmailNameOrAddress|
|StackPanel|StackOrientation|方向 *|

> [!IMPORTANT]
> 项标有 * 是在当前的预览版不完整

## <a name="related-links"></a>相关链接

- [预览 NuGet](https://aka.ms/xf-xamlstandard-nuget)
