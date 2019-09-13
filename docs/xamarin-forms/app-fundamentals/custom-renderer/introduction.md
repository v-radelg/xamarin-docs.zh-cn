---
title: 自定义呈现器简介
description: 本文介绍了自定义呈现器，并概述了创建自定义呈现器的过程。
ms.prod: xamarin
ms.assetid: 264314BE-1C5C-4727-A14E-F6F98151CDBD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/19/2016
ms.openlocfilehash: ad2868a82f662f45066a6111a1dd3bd2aacad671
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70771883"
---
# <a name="introduction-to-custom-renderers"></a>自定义呈现器简介

“自定义呈现器为自定义 Xamarin.Forms 控件的外观和行为提供了一种功能强大的方法。可使用它们进行细微的样式更改，也可进行复杂的特定于平台的布局和行为自定义。本文介绍了自定义呈现器，并概述了创建自定义呈现器的过程。”_

Xamarin.Forms [页、布局和控件](~/xamarin-forms/user-interface/controls/index.md)提供常见的 API，用于描述跨平台的移动用户界面。 通过 `Renderer` 类可以在每个平台上以不同方式呈现每个页面、布局和控件，反过来又可以创建本机控件（对应于 Xamarin.Forms 表示），在屏幕上排列该控件，并添加共享代码中指定的行为。

开发人员可以实现自定义 `Renderer` 类，以自定义控件的外观和/或行为。 可以将给定类型的自定义呈现器添加到一个应用程序项目中，以便在同一个位置自定义控件，同时允许其他平台上的默认行为，或者将不同的自定义呈现器添加到每个应用程序项目中，以便在 iOS、Android 和通用 Windows 平台 (UWP) 上创建不同的外观和感觉。 但是，实现自定义呈现器类以执行简单的控件自定义通常是大型响应。 Effects 可简化此过程，并通常用于较小的样式更改。 有关详细信息，请参阅[效果](~/xamarin-forms/app-fundamentals/effects/index.md)。

## <a name="examining-why-custom-renderers-are-necessary"></a>检查需要自定义呈现器的原因

要在不使用自定义呈现器的情况下更改 Xamarin.Forms 控件的外观，这一过程包含两个步骤：通过子类化创建自定义控件，然后使用自定义控件代替原始控件。 下面的代码示例演示将 `Entry` 控件子类化的示例:

```csharp
public class MyEntry : Entry
{
  public MyEntry ()
  {
    BackgroundColor = Color.Gray;
  }
}
```

`MyEntry` 控件是 `BackgroundColor` 设置为灰色的 `Entry` 控件，可以通过声明其位置的名称空间并在控件元素上使用该名称空间的前缀在 Xaml 中引用。 下面的代码示例演示 `ContentPage` 可以如何使用 `MyEntry` 自定义控件：

```xaml
<ContentPage
    ...
    xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer"
    ...>
    ...
    <local:MyEntry Text="In Shared Code" />
    ...
</ContentPage>
```

`local` 命名空间前缀可以是任何内容。 但是，`namespace` 和 `assembly` 值必须与自定义控件的详细信息相匹配。 声明命名空间后，前缀用于引用自定义控件。

> [!NOTE]
> 在 .NET Standard 库项目中定义 `xmlns` 比在共享项目中定义简单得多。 由于 .NET Standard 库已编译到程序集中，因此可以轻松确定 `assembly=CustomRenderer` 值。 使用共享项目时，所有共享资产（包括 XAML）都编译到每个引用项目中，这意味着如果 iOS、Android 和 UWP 项目有自己的程序集名称，则不能写入 `xmlns` 声明，因为每个应用程序需要不同的值  。 XAML 中的共享项目的自定义控件将需要每个应用程序项目用相同的程序集名称来配置。

然后在每个平台上呈现背景为灰色的 `MyEntry` 自定义控件，如以下屏幕截图中所示：

![](introduction-images/screenshots.png "每个平台上的 MyEntry 自定义控件")

仅通过将控件子类化即可完成对每个平台上的控件背景色的更改。 但是，由于无法利用特定于平台的增强和自定义，这种技术的用途有限。 必要时，必须实现自定义呈现器。

## <a name="creating-a-custom-renderer-class"></a>创建自定义呈现器类

创建自定义呈现器类的过程如下所示：

1. 创建呈现本机控件的呈现器类的子类。
1. 替代呈现本机控件的方法并写入逻辑以自定义控件。 通常，`OnElementChanged` 方法用于呈现本机控件，当创建相应的 Xamarin.Forms 控件时，调用该本机控件。
1. 向自定义呈现器类添加 `ExportRenderer` 属性，以指定其将用于呈现 Xamarin.Forms 控件。 此属性用于向 Xamarin.Forms 注册自定义呈现器。

> [!NOTE]
> 对大多数 Xamarin.Forms 元素来说，可以选择在每个平台项目中提供自定义呈现器。 如果未注册自定义呈现器，将使用控件基类的默认呈现器。 但是，呈现 [View](xref:Xamarin.Forms.View) 或 [ViewCell](xref:Xamarin.Forms.ViewCell) 元素时，每个平台项目中都需要自定义呈现器。

本系列的主题将提供针对不同 Xamarin.Forms 元素的此过程的演示和解释。

## <a name="troubleshooting"></a>疑难解答

如果自定义控件包含在已添加到解决方案的 .NET Standard 库项目中（即不是由 Visual Studio for Mac/Visual Studio Xamarin.Forms 应用项目模板创建的 .NET Standard 库），尝试访问自定义控件时，iOS 中可能会发生异常。 如果发生此问题，可以通过从 `AppDelegate` 类创建对自定义控件的引用来解决此问题：

```csharp
var temp = new ClassInPCL(); // in AppDelegate, but temp not used anywhere
```

这迫使编译器通过解析来识别 `ClassInPCL` 类型。 或者，可以将 `Preserve` 属性添加到 `AppDelegate` 类，以实现相同结果：

```csharp
[assembly: Preserve (typeof (ClassInPCL))]
```

这会创建对 `ClassInPCL` 类型的引用，指明其在运行时是必需的。 有关详细信息，请参阅[保留代码](~/ios/deploy-test/linker.md)。

## <a name="summary"></a>总结

本文介绍了自定义呈现器，并概述了创建自定义呈现器的过程。 自定义呈现器为自定义 Xamarin.Forms 控件的外观和行为提供了一种功能强大的方法。 可使用它们进行细微的样式更改，也可进行复杂的特定于平台的布局和行为自定义。

## <a name="related-links"></a>相关链接

- [效果](~/xamarin-forms/app-fundamentals/effects/index.md)
