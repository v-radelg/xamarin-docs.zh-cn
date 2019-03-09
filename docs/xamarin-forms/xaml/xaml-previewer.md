---
title: 适用于 Xamarin.Forms 的 XAML 预览程序
description: 本文介绍如何使用 XAML 预览程序若要查看的呈现将如你所键入的 Xamarin.Forms 布局。 XAML 预览程序现已推出 Visual Studio 2017、 Visual Studio for Mac 和 Visual Studio 2019 （预览版）。
zone_pivot_groups: platform-dev16
ms.prod: xamarin
ms.assetid: 84769ff1-72fd-4c44-8251-dd6d5bf8c7b2
ms.technology: xamarin-forms
author: maddyleger1
ms.author: maleger
ms.date: 02/04/2019
ms.openlocfilehash: 7834b87687db8fd1a3d51a40a4657b24e46bac17
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57670397"
---
# <a name="xaml-previewer-for-xamarinforms"></a>适用于 Xamarin.Forms 的 XAML 预览程序

_请参阅的呈现将如你所键入的 Xamarin.Forms 布局 ！_

## <a name="requirements"></a>要求

项目需要 XAML 预览程序若要运行的最新 Xamarin.Forms NuGet 包。 预览 Android 应用程序需要[JDK 1.8 x64](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)。

中的详细信息[发行说明](https://developer.xamarin.com/releases/studio/xamarin.studio_6.2/xamarin.studio_6.2/#Xamarin_Forms_Previewer)。

## <a name="getting-started"></a>入门

Xamarin.Forms XAML 预览程序中实时显示在 XAML 文件的特定于平台的预览，而对其进行编辑。

::: zone pivot="windows"

### <a name="visual-studio"></a>Visual Studio

XAML 预览程序是可通过扩展拆分视图窗格。 默认的拆分视图行为可控制从**工具 > 选项 > Xamarin > 窗体预览程序**对话框。 在此对话框中，可以选择默认文档视图和拆分方向。

[![在 Visual Studio 中的 Xamarin.Forms 预览程序选项](xaml-previewer-images/xamlp-options-vs-sm.png "选项在 Visual Studio 中的 Xamarin.Forms 预览程序")](xaml-previewer-images/xamlp-options-vs-lg.png#lightbox)

在打开 XAML 页时，编辑器将拆分基于中所选的设置**工具 > 选项 > Xamarin > 窗体预览程序**对话框。 但是，可以为每个文件在编辑器窗口中更改拆分。

#### <a name="xaml-preview-controls"></a>XAML 预览控件

编辑器窗口的顶部具有要选择哪个窗格正在使用中，切换到设计窗格顶部的按钮和切换到源窗格的底部按钮的按钮。 中间的按钮交换窗格顺序。

[![Xamarin.Forms 预览程序控制来设计、 源和 Visual Studio 中的拆分视图之间切换](xaml-previewer-images/xamlp-controls-splitview-vs-sm.png "Xamarin.Forms 预览程序控制来设计、 源和 Visual Studio 中的拆分视图之间切换")](xaml-previewer-images/xamlp-controls-splitview-vs-lg.png#lightbox)

编辑器窗口的底部有水平和垂直方向拆分窗格，以展开或折叠当前子窗格的按钮。

[![Visual Studio 中的 Xamarin.Forms 预览程序窗格方向控件](xaml-previewer-images/xamlp-controls-orientation-vs-sm.png "Visual Studio 中的 Xamarin.Forms 预览程序窗格方向控件")](xaml-previewer-images/xamlp-controls-orientation-vs-lg.png#lightbox)

::: zone-end
::: zone pivot="macos"

### <a name="visual-studio-for-mac"></a>Visual Studio for Mac

**预览版**打开 XAML 页时，在编辑器上显示的按钮。 在预览窗格可显示或隐藏通过按**预览版**在任何 XAML 文档窗口的右上角的按钮：

[![Xamarin.Forms Previewer in Visual Studio for Mac](xaml-previewer-images/xamlp-list-sml.png "Xamarin.Forms Previewer in Visual Studio for Mac")](xaml-previewer-images/xamlp-list.png#lightbox)

::: zone-end
::: zone pivot="win-dev16"

### <a name="visual-studio-2019-preview"></a>Visual Studio 2019 （预览版）

XAML 预览程序是可通过扩展拆分视图窗格。 默认的拆分视图行为可控制从**工具 > 选项 > Xamarin > 窗体预览程序**对话框。 在此对话框中，可以选择默认文档视图和拆分方向。

[![在 Visual Studio 中的 Xamarin.Forms 预览程序选项](xaml-previewer-images/xamlp-options-vs-sm.png "选项在 Visual Studio 中的 Xamarin.Forms 预览程序")](xaml-previewer-images/xamlp-options-vs-lg.png#lightbox)

在打开 XAML 页时，编辑器将拆分基于中所选的设置**工具 > 选项 > Xamarin > 窗体预览程序**对话框。 但是，可以为每个文件在编辑器窗口中更改拆分。

#### <a name="xaml-preview-controls"></a>XAML 预览控件

编辑器窗口的顶部具有要选择哪个窗格正在使用中，切换到设计窗格顶部的按钮和切换到源窗格的底部按钮的按钮。 中间的按钮交换窗格顺序。

[![Xamarin.Forms 预览程序控制来设计、 源和 Visual Studio 中的拆分视图之间切换](xaml-previewer-images/xamlp-controls-splitview-vs-sm.png "Xamarin.Forms 预览程序控制来设计、 源和 Visual Studio 中的拆分视图之间切换")](xaml-previewer-images/xamlp-controls-splitview-vs-lg.png#lightbox)

编辑器窗口的底部有水平和垂直方向拆分窗格，以展开或折叠当前子窗格的按钮。

[![Visual Studio 中的 Xamarin.Forms 预览程序窗格方向控件](xaml-previewer-images/xamlp-controls-orientation-vs-sm.png "Visual Studio 中的 Xamarin.Forms 预览程序窗格方向控件")](xaml-previewer-images/xamlp-controls-orientation-vs-lg.png#lightbox)

::: zone-end

## <a name="xaml-previewer-options"></a>XAML 预览器选项

预览窗格顶部的选项包括：

* **Phone** – 大小的手机屏幕上的预览版
* **平板电脑**-调整大小的平板电脑屏幕上的预览版
* **Android** – 显示在屏幕的 Android 版本
* **iOS** – 显示在屏幕的 iOS 版本 (*注意：如果在 Windows 上使用 Visual Studio，你必须是[与 Mac 配对](~/ios/get-started/installation/windows/connecting-to-mac/index.md)若要使用此模式*)
* **纵向 （图标）** – 预览版使用纵向方向
* **横向 （图标）** – 使用横向方向预览版

::: zone pivot="win-dev16"

## <a name="device-drop-down-menu"></a>设备下拉列表菜单

在 Visual Studio 2019 （预览版），可以选择要预览上，从下拉列表中的 Android 或 iOS 设备的设备。 此下拉列表替换"电话"和"平板电脑"选项。 预览时 iOS，下拉列表将显示 iPhone 和 iPad 设备。 预览时 Android，下拉列表将显示各种 Android 手机和平板电脑。 这两个列表包括屏幕大小和屏幕分辨率。

::: zone-end

## <a name="detect-design-mode"></a>检测到设计模式

静态[ `DesignMode.IsDesignModeEnabled` ](xref:Xamarin.Forms.DesignMode.IsDesignModeEnabled)属性可以检查以确定是否在预览程序中运行应用程序。 这使您可以指定在预览程序中运行应用程序时，将仅执行的代码：

```csharp
if (DesignMode.IsDesignModeEnabled)
{
  // Previewer only code  
}
```

::: zone pivot="win-dev16"

## <a name="enable-design-time-rendering-for-custom-controls"></a>启用自定义控件的设计时呈现

自定义控件需要选择加入的设计时呈现才会出现在预览程序，而不考虑该控件是否驻留在你的项目，或从库导入。 这可以通过添加[ `[DesignTimeVisible(true)]` ](xref:System.ComponentModel.DesignTimeVisibleAttribute)到控件的类：

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

此示例所示[James Montemagno 的 ImageCirclePlugin 基类](https://github.com/jamesmontemagno/ImageCirclePlugin/blob/master/src/ImageCircle/CircleImage.shared.cs)。

::: zone-end

## <a name="add-design-time-data"></a>添加设计时数据

某些布局可能很难直观显示不包含任何数据绑定到用户界面控件。 若要使预览更有用，分配一些静态数据的控件进行硬编码的绑定上下文 （不管是在代码隐藏中或使用 XAML）。

请参阅 James Montemagno[博客文章上添加设计时数据](http://motzcod.es/post/143702671962/xamarinforms-xaml-previewer-design-time-data)若要了解如何将绑定到 XAML 中静态 ViewModel。

## <a name="troubleshooting"></a>疑难解答

检查以下问题并[Xamarin 论坛](https://forums.xamarin.com/categories/xamarin-forms)，如果遇到问题。

### <a name="xaml-previewer-isnt-showing-or-shows-an-error"></a>XAML 预览程序未显示或显示的错误消息

请检查是否属于以下情况：

* 设计器代理必须安装第一次预览 XAML 文件-进度指示器将显示在预览程序，以及进度消息，直到准备就绪。
* 请尝试关闭并重新打开 XAML 文件。
* 确保你`App`类具有无参数构造函数。

::: zone pivot="win-dev16"

### <a name="custom-controls-arent-rendering"></a>不会呈现自定义控件

请确保您的项目生成。 如果预览程序无法呈现且未发生错误的控件或控件的创建者未不选择-在设计时呈现，预览程序将显示控件的基类。

::: zone-end
