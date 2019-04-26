---
title: 呈现 XAML 预览程序中的自定义控件
description: 本文介绍如何在 XAML 预览程序中显示自定义控件。
ms.prod: xamarin
ms.assetid: 4D795372-CB8F-48F4-B63D-845E44B261F7
ms.technology: xamarin-forms
author: maddyleger1
ms.author: maleger
ms.date: 03/27/2019
ms.openlocfilehash: 977c29312e0be8b92f216c224414f9bd03f8562d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60875972"
---
# <a name="render-custom-controls-in-the-xaml-previewer"></a>呈现 XAML 预览程序中的自定义控件

_自定义控件有时不起作用的 XAML 预览程序中的预期。使用本文中的指南以了解预览你的自定义控件的限制。_

## <a name="basic-preview-mode"></a>基本预览模式

即使尚未生成你的项目，XAML 预览程序将呈现您的页面。 只有在生成依赖于代码隐藏任何控件将显示其基的 Xamarin.Forms 类型。 你的项目生成时，XAML 预览程序将尝试显示自定义控件的设计时呈现启用。 如果呈现失败，它将显示基 Xamarin.Forms 类型。

## <a name="enable-design-time-rendering-for-custom-controls"></a>启用自定义控件的设计时呈现

如果使自己的自定义控件，或使用来自第三方库的控件，预览程序可能无法正确显示它们。 自定义控件必须选择在设计时呈现出现在预览程序，是否编写了该控件或从库将其导入。 与已创建的控件，添加[ `[DesignTimeVisible(true)]` ](xref:System.ComponentModel.DesignTimeVisibleAttribute)到控件的类，以显示在预览程序中：

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

使用[James Montemagno 的 ImageCirclePlugin 基类](https://github.com/jamesmontemagno/ImageCirclePlugin/blob/master/src/ImageCircle/CircleImage.shared.cs)作为示例。


## <a name="skiasharp-controls"></a>SkiaSharp 控件

目前，要在 iOS 上预览时，仅支持 SkiaSharp 控件。 它们不会呈现在 Android 预览。

## <a name="troubleshooting"></a>疑难解答

### <a name="check-your-xamarinforms-version"></a>检查 Xamarin.Forms 版本
请确保至少具有 Xamarin.Forms 3.6 安装。 可以更新 Xamarin.Forms 版本 NuGet 上。

### <a name="even-with-designtimevisibletrue-my-custom-control-isnt-rendering-properly"></a>即使使用`[DesignTimeVisible(true)]`，我的自定义控件未正确呈现。
自定义控件的代码隐藏或后端数据依赖于 XAML 预览程序中始终不起作用。 你可以尝试：
* 移动控件中，因此它不会初始化如果[启用设计模式](index.md#detect-design-mode)
* 设置[设计时数据](design-time-data.md)以显示从后端的模拟数据

### <a name="the-xaml-previewer-shows-the-error-custom-controls-arent-rendering-properly"></a>XAML 预览程序显示错误"自定义控件不会正确呈现"
请尝试清除和重新生成项目，或关闭并重新打开 XAML 文件。
