---
title: 在 XAML 预览器中呈现自定义控件
description: 本文介绍如何在 XAML 预览程序中显示自定义控件。
ms.prod: xamarin
ms.assetid: 4D795372-CB8F-48F4-B63D-845E44B261F7
ms.technology: xamarin-forms
author: maddyleger1
ms.author: maleger
ms.date: 03/27/2019
ms.openlocfilehash: 5b87c631574f159230e1dc23285b9087bcc94255
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70767322"
---
# <a name="render-custom-controls-in-the-xaml-previewer"></a>在 XAML 预览器中呈现自定义控件

_自定义控件有时在 XAML 预览器中不按预期方式工作。使用本文中的指南来了解预览自定义控件的限制。_

## <a name="basic-preview-mode"></a>基本预览模式

即使您未生成项目，XAML 预览器也会呈现您的页面。 在生成之前，依赖于代码隐藏的任何控件都将显示其基本 Xamarin。窗体类型。 生成项目时，XAML 预览器会尝试在启用设计时呈现的情况下显示自定义控件。 如果呈现失败，它将显示基本 Xamarin. 窗体类型。

## <a name="enable-design-time-rendering-for-custom-controls"></a>启用自定义控件的设计时呈现

如果创建自己的自定义控件或使用第三方库中的控件，则预览程序可能会错误地显示这些控件。 无论是从库中写入控件还是从库中导入，自定义控件都必须选择将时间呈现设计为显示在预览器中。 如果已创建控件，请将添加[`[DesignTimeVisible(true)]`](xref:System.ComponentModel.DesignTimeVisibleAttribute)到控件的类，以便在预览器中显示它：

```csharp
namespace MyProject
{
  [DesignTimeVisible(true)]
  public class MyControl : BaseControl
  {
    // Your control's code here
  }

}
```

使用[James Montemagno 的 ImageCirclePlugin's 基类](https://github.com/jamesmontemagno/ImageCirclePlugin/blob/master/src/ImageCircle/CircleImage.shared.cs)作为示例。

## <a name="skiasharp-controls"></a>SkiaSharp 控件

目前，仅当你在 iOS 上预览时，才支持 SkiaSharp 控件。 它们不会在 Android 预览版中呈现。

## <a name="troubleshooting"></a>疑难解答

### <a name="check-your-xamarinforms-version"></a>检查 Xamarin. Forms 版本
请确保至少安装了 Xamarin. Forms 3.6。 可以在 NuGet 上更新 Xamarin. Forms 版本。

### <a name="even-with-designtimevisibletrue-my-custom-control-isnt-rendering-properly"></a>即使有`[DesignTimeVisible(true)]`，自定义控件也不会正确呈现。
很大程度上依赖于代码隐藏或后端数据的自定义控件并不总是在 XAML 预览器中工作。 你可以尝试：
* 如果[启用设计模式](index.md#detect-design-mode)，则移动控件以使其不初始化
* 设置[设计时数据](design-time-data.md)以显示后端的虚假数据

### <a name="the-xaml-previewer-shows-the-error-custom-controls-arent-rendering-properly"></a>XAML 预览器显示错误 "自定义控件不能正确呈现"
尝试清除并重新生成项目，或关闭并重新打开 XAML 文件。
