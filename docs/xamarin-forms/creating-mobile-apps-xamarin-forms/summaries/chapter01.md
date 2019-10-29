---
title: 第1章摘要： 如何调整 Xamarin 形式？
description: 用 Xamarin 创建移动应用：第1章的摘要。 如何调整 Xamarin 形式？
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F3F864FF-EE70-49D0-90D1-388889037625
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 6dfa473bdfb4c1dd88ca833dbf5011a0bbdec42a
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032884"
---
# <a name="summary-of-chapter-1-how-does-xamarinforms-fit-in"></a>第1章摘要： 如何调整 Xamarin 形式？

[![下载示例](~/media/shared/download.png) 下载示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter01)

> [!NOTE]
> 此页上的说明指出了 Xamarin 在书籍中提供的材料中分离的区域。

编程中最令人不愉快的作业之一是将基本代码从一个平台移植到另一个平台，尤其是在该平台涉及到不同的编程语言时。 同时移植代码以重构代码，但如果两个平台都必须并行维护，则这两个代码库之间的差异会使将来的维护变得更加困难。

## <a name="cross-platform-mobile-development"></a>跨平台移动开发

在面向移动平台时，此问题很常见。 目前，有两个主要的移动平台，即 Iphone 和 Ipad 的 Apple 系列运行 iOS 操作系统，以及在各种手机和平板电脑上运行的 Android 操作系统。 另一个重要的平台是 Microsoft 的通用 Windows 平台（UWP），它允许单个程序针对 Windows 10。

希望以这些平台为目标的软件供应商必须处理不同的用户界面模式、三个不同的开发环境和三个不同的编程接口，并&mdash;可能是大多数 awkwardly&mdash;三个不同编程语言：适用于 iPhone 和 iPad 的目标-C，适用于 Android 的C# Java，适用于 Windows。

## <a name="the-c-and-net-solution"></a>C#和 .net 解决方案

尽管目标为 C、Java 和C#都是从 C 编程语言派生的，但它们已由非常不同的路径发展。 C#是这些语言中的最新版本，并且已经以非常有用的方式成熟。 此外， C#与称为 .net 的整个编程基础结构密切相关，这为数学、调试、反射、集合、全球化、文件 i/o、网络、安全性、线程、web 服务、数据处理提供支持。和 XML 和 JSON 的读取和写入。

Xamarin 目前提供使用C#和 .net 面向本机 Mac、IOS 和 Android api 的工具。 这些工具称为 Xamarin、Xamarin 和 Xamarin （统称为 Xamarin 平台）。 这些是用 .NET 惯例表示这些平台的本机 Api 的库和绑定。

开发人员可以使用 Xamarin 平台写入目标 Mac、 C# IOS 或 Android 中的应用程序。 但在面向多个平台时，在目标平台之间共享某些代码非常有意义。 这涉及到将程序分离到平台相关代码（通常涉及用户界面）和独立于平台的代码中，这通常只需要基本的 .NET framework。 此独立于平台的代码既可以位于可移植类库（PCL）中，也可以驻留在共享项目中，通常称为共享资产项目或 SAP。

> [!NOTE]
> 可移植类库已替换为 .NET Standard 库。 书籍中的所有示例代码都已转换为使用 .NET standard 库。

## <a name="introducing-xamarinforms"></a>引入 Xamarin. Forms

面向多个移动平台时，Xamarin 允许进行更多代码共享。 为 Xamarin 编写的单个程序。表单可以面向以下平台：

- 在 iPhone、iPad 和 iPod touch 上运行的程序的 iOS
- Android （适用于 Android 手机和平板电脑上运行的程序）
- 面向 Windows 10 的通用 Windows 平台

> [!NOTE]
> Xamarin 不再支持 Windows 8.1、Windows Phone 8.1 或 Windows 10 移动版，但 Xamarin. 窗体应用程序在 Windows 10 桌面上运行。 此外，还提供对[Mac](~/xamarin-forms/platform/other/mac.md)、 [WPF](~/xamarin-forms/platform/other/wpf.md)、 [GTK #](~/xamarin-forms/platform/other/gtk.md)和[Tizen](~/xamarin-forms/platform/other/tizen.md)平台的预览支持。

库或 SAP 中存在大量 Xamarin 程序。 每个平台都包含调用此共享代码的小型应用程序存根。

Xamarin Api 映射到每个平台上的本机控件，使每个平台保持其特征外观：

[![平台视觉对象共享的三向屏幕截图](images/ch01fg03-small.png "每个平台上的 Xamarin 控件")](images/ch01fg03-large.png#lightbox "每个平台上的 Xamarin 控件")

从左到右的屏幕截图显示 iPhone 和 Android 手机：

在每个屏幕上，页面都包含一个 Xamarin。窗体[`Label`](xref:Xamarin.Forms.Label)用于显示文本、用于启动操作的[`Button`](xref:Xamarin.Forms.Button) 、用于选择打开/关闭值的[`Switch`](xref:Xamarin.Forms.Switch) ，以及用于指定连续范围内的值的[`Slider`](xref:Xamarin.Forms.Slider). 这四个视图都是[`ContentPage`](xref:Xamarin.Forms.ContentPage)上的[`StackLayout`](xref:Xamarin.Forms.StackLayout)的子视图。

还附加到页面是一个 Xamarin. Forms 工具栏，其中包含多个[`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem)对象。 这些图标在 iOS 和 Android 屏幕顶部以及 Windows 10 移动版屏幕的底部显示为图标。

Xamarin 还支持 XAML，这是在 Microsoft 为多个应用程序平台开发的 Extensible Application Markup Language。 上述程序的所有视觉对象都在 XAML 中定义，如[**PlatformVisuals**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter01/PlatformVisuals)示例中所示。

Xamarin 程序可以确定运行它的平台，并相应地执行不同的代码。 更有效的是，开发人员可以编写各种平台的自定义代码，并以独立于平台的方式从 Xamarin 程序运行该代码。 开发人员还可以通过为每个平台编写呈现器来创建其他控件。

虽然 Xamarin 对于业务线应用程序而言是一个不错的解决方案，或用于创建原型或进行概念证明演示，但对于需要矢量图形或复杂触摸交互的应用程序而言，这种方法不太理想。

## <a name="your-development-environment"></a>你的开发环境

你的开发环境取决于你想要使用的平台以及要使用的计算机。

如果要面向 iOS，则需要一个 Mac with Xcode 和 Xamarin 平台。 同时支持 Android 需要安装 Java 和所需的 Sdk。 然后，可以使用 Visual Studio for Mac 以 iOS 和 Android 为目标。

安装 Visual Studio 后，你将能够在你的 PC 上面向 iOS、Android 和所有 Windows 平台。 但是，面向 iOS 的 Visual Studio 仍需要具有 Xcode 的 Mac 和安装的 Xamarin 平台。

可以在通过 USB 连接到计算机的实际设备上或在模拟器上测试程序。

## <a name="installation"></a>安装

创建和生成 Xamarin 应用程序应用程序之前，应尝试单独创建和生成 iOS 应用程序、Android 应用程序和 UWP 应用程序，具体取决于要面向的平台和开发环境。

Xamarin 和 Microsoft 网站包含有关如何执行此操作的信息：

- [IOS 入门](~/ios/get-started/index.md)
- [Android 入门](~/android/get-started/index.md)
- [Windows 开发人员中心](https://dev.windows.com)

为这些单独的平台创建和运行项目后，在创建和运行 Xamarin. Forms 应用程序时，应该没有问题。

## <a name="related-links"></a>相关链接

- [第1章完整文本（PDF）](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch01-Apr2016.pdf)
- [第1章示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter01)
