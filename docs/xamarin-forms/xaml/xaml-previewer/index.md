---
title: 适用于 Xamarin.Forms 的 XAML 预览程序
description: 本文介绍如何使用 XAML 预览程序若要查看的呈现将如你所键入的 Xamarin.Forms 布局。 XAML 预览程序现已推出 Visual Studio 2019 和 Visual Studio 2019 for mac。
zone_pivot_groups: platform
ms.prod: xamarin
ms.assetid: 84769ff1-72fd-4c44-8251-dd6d5bf8c7b2
ms.technology: xamarin-forms
author: maddyleger1
ms.author: maleger
ms.date: 02/04/2019
ms.openlocfilehash: db243a9c8dcb25f51bc7926a7aa239531e9c24f6
ms.sourcegitcommit: c4be32ef914465e808d89767c4d5ee72afe93cc6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2019
ms.locfileid: "58859007"
---
# <a name="xaml-previewer-for-xamarinforms"></a>适用于 Xamarin.Forms 的 XAML 预览程序

_请参阅的呈现将如你所键入的 Xamarin.Forms 布局_

## <a name="overview"></a>概述

XAML 预览程序显示在 iOS 和 Android 上 Xamarin.Forms XAML 页面外观。 当在 XAML 中进行了更改时，将看到它们与代码一起立即预览。 XAML 预览程序现已推出 Visual Studio 和 Visual Studio for mac。

## <a name="getting-started"></a>入门

::: zone pivot="windows"

### <a name="visual-studio-2019"></a>Visual Studio 2019

可以通过单击拆分视图窗格上的箭头打开 XAML 预览程序。 如果你想要更改默认的拆分视图行为，请使用**工具 > 选项 > Xamarin > 窗体预览程序**对话框。 在此对话框中，可以选择默认文档视图和拆分方向。

[![Xamarin。在 Visual Studio 中，窗体预览器选项](xaml-previewer-images/xamlp-options-vs-sm.png "选项在 Visual Studio 中的 Xamarin.Forms 预览程序")](xaml-previewer-images/xamlp-options-vs-lg.png#lightbox)

当打开 XAML 文件时，编辑器随即打开大或为预览程序中所选的设置下一步**工具 > 选项 > Xamarin > 窗体预览程序**对话框。 但是，可以为每个文件在编辑器窗口中更改拆分。

#### <a name="xaml-preview-controls"></a>XAML 预览控件

选择是否想要查看代码中，XAML 预览程序，或者通过选择这些按钮在拆分视图窗格。 中间的按钮交换什么端预览程序和你的代码位于：

[![Xamarin。窗体预览器控件来设计、 源和 Visual Studio 中的拆分视图之间切换](xaml-previewer-images/xamlp-controls-splitview-vs-sm.png "Xamarin.Forms 预览程序控制来设计、 源和 Visual Studio 中的拆分视图之间切换")](xaml-previewer-images/xamlp-controls-splitview-vs-lg.png#lightbox)

您可以更改是否垂直或水平拆分屏幕或完全折叠窗格：

[![Xamarin。在 Visual Studio 中，窗体预览器窗格方向控件](xaml-previewer-images/xamlp-controls-orientation-vs-sm.png "Visual Studio 中的 Xamarin.Forms 预览程序窗格方向控件")](xaml-previewer-images/xamlp-controls-orientation-vs-lg.png#lightbox)

::: zone-end
::: zone pivot="macos"

### <a name="visual-studio-for-mac"></a>Visual Studio for Mac

**预览版**打开 XAML 页时，在编辑器上显示的按钮。 显示或隐藏预览程序通过按**预览版**在任何 XAML 文档窗口的右上角的按钮：

[![Xamarin。构成 Visual Studio 中用于 Mac 预览程序](xaml-previewer-images/xamlp-list-sml.png "Visual Studio for Mac 中的 Xamarin.Forms 预览程序")](xaml-previewer-images/xamlp-list.png#lightbox)

::: zone-end

## <a name="xaml-previewer-options"></a>XAML 预览器选项

预览窗格顶部的选项包括：

* **Android** – 显示在屏幕的 Android 版本
* **iOS** – 显示在屏幕的 iOS 版本 (*注意：如果在 Windows 上使用 Visual Studio，你必须是[与 Mac 配对](~/ios/get-started/installation/windows/connecting-to-mac/index.md)若要使用此模式*)
* **设备**-Android 或 iOS 设备，包括分辨率和屏幕大小的下拉列表
* **纵向 （图标）** – 预览版使用纵向方向
* **横向 （图标）** – 使用横向方向预览版

## <a name="detect-design-mode"></a>检测到设计模式

静态[ `DesignMode.IsDesignModeEnabled` ](xref:Xamarin.Forms.DesignMode.IsDesignModeEnabled)属性指明是否在预览程序中运行该应用程序。 使用它，可以指定将仅执行时应用程序或不在预览程序中运行的代码：

```csharp
if (DesignMode.IsDesignModeEnabled)
{
  // Previewer only code  
}

if (!DesignMode.IsDesignModeEnabled)
{
  // Don't run in the Previewer  
}
```

此属性很有用，如果您无法在设计时运行的页构造函数中初始化一个库。

## <a name="troubleshooting"></a>疑难解答

检查以下问题并[Xamarin 论坛](https://forums.xamarin.com/categories/xamarin-forms)，如果预览程序不能正常工作。

### <a name="xaml-previewer-isnt-showing-or-shows-an-error"></a>XAML 预览程序未显示或显示的错误消息

* 它可能需要一些时间为预览程序启动-您将看到"正在初始化呈现"直至其准备。
* 请尝试关闭并重新打开 XAML 文件。
* 确保你`App`类具有无参数构造函数。
* 检查 Xamarin.Forms 版本-它必须至少是 Xamarin.Forms 3.6。 可以通过 NuGet 的最新的 Xamarin.Forms 版本进行更新。
* 检查您的 JDK 安装-预览 Android 要求至少[JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)。
* 请尝试包装任何初始化中页面的类C#中的代码的后面`if (!DesignMode.IsDesignModeEnabled)`。

### <a name="custom-controls-arent-rendering"></a>不会呈现自定义控件

请尝试生成项目。 预览程序显示控件的基类，如果它将无法呈现该控件，或如果控件的创建者选择的扩展设计时呈现。 有关详细信息，请参阅[呈现 XAML 预览程序中的自定义控件](render-custom-controls.md)。
