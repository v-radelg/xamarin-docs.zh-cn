---
title: 第 1 章的摘要。 Xamarin.Forms 如何组合？
description: 使用 Xamarin.Forms 创建移动应用：第 1 章的摘要。 Xamarin.Forms 如何组合？
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F3F864FF-EE70-49D0-90D1-388889037625
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 58d3b3ae067913a85c3ada5f5b35e64511523ff8
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61334586"
---
# <a name="summary-of-chapter-1-how-does-xamarinforms-fit-in"></a>第 1 章的摘要。 Xamarin.Forms 如何组合？

[![下载示例](~/media/shared/download.png)下载示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter01)

> [!NOTE]
> 此页上的说明表明其中 Xamarin.Forms 已脱离一书中介绍的内容的区域。

在编程中最令人不快的作业中的一个移植的代码库从一个平台，尤其是如果该平台涉及不同的编程语言。 倾向于时移植代码对其进行重构，但如果这两个平台必须同时维护，然后两个代码库之间的差异会使将来维护更加困难。

## <a name="cross-platform-mobile-development"></a>跨平台移动开发

面向移动平台时，此问题很常见。 目前，存在两个主要的移动平台、 Iphone 和 Ipad 运行 iOS 操作系统，并在各种手机和平板电脑运行 Android 操作系统的 Apple 系列。 另一个重要的一个平台是 Microsoft 的通用 Windows 平台 (UWP)，它允许单个程序以面向这两个 Windows 10。

想要面向这些平台的软件供应商必须处理不同的用户界面模式、 三个不同的开发环境、 三个不同的编程接口，并&mdash;可能是最滑雪&mdash;三个不同的编程语言：适用于 iPhone 和 iPad，Java for Android、 objective C 和C#为 Windows。

## <a name="the-c-and-net-solution"></a>C# 和.NET 解决方案

尽管 Objective C、 Java 和 C# 所有派生自 C 编程语言，但它们已演变成将通过非常不同的路径。 C# 是一种语言最新和已成熟方式非常有用。 此外，C# 是密切相关与名为.NET、 数学、 调试、 反射、 集合、 全球化、 文件 I/O、 网络、 安全性、 线程、 web 服务、 数据处理和 XML 提供支持整个编程基础结构和读取和写入的 JSON。

当前，Xamarin 提供了工具来面向本机 Mac、 iOS 和 Android Api 使用 C# 和.NET。 这些工具称为 Xamarin.Mac 和 Xamarin.iOS、 Xamarin.Android，统称为 Xamarin 平台。 这些是库和 express 与.NET 惯例这些平台的本机 Api 的绑定。

开发人员可以使用 Xamarin 平台在 C# 中编写的应用程序，该目标 Mac、 iOS 或 Android。 但当目标多个平台，可以很有意义分享一些在目标平台的代码。 这涉及到将分成依赖于平台的代码 （通常涉及用户界面），并独立于平台的代码，通常需要只是基本的.NET framework 的程序。 此独立于平台的代码也可以驻留在可移植类库 (PCL) 或共享的项目，通常称为共享资产项目或 SAP。

> [!NOTE]
> .NET Standard 库已替换为可移植类库。 本书中的所有示例代码已都转换为使用.NET 标准库。

## <a name="introducing-xamarinforms"></a>Xamarin.Forms 简介

当目标多个移动平台，Xamarin.Forms 允许更多代码共享。 编写用于 Xamarin.Forms 的单个程序可以针对这些平台：

- 适用于 iPhone、 iPad 和 iPod touch 运行的程序的 iOS
- 在 Android 手机和平板电脑上运行的程序的 android
- 面向 Windows 10 通用 Windows 平台

> [!NOTE]
> Xamarin.Forms 不再支持 Windows 8.1、 Windows Phone 8.1 或 Windows 10 移动版，但在 Windows 10 桌面版上运行的 Xamarin.Forms 应用程序。 此外，还有支持预览版[Mac](~/xamarin-forms/platform/other/mac.md)， [WPF](~/xamarin-forms/platform/other/wpf.md)， [GTK #](~/xamarin-forms/platform/other/gtk.md)，以及[Tizen](~/xamarin-forms/platform/other/tizen.md)平台。

一个库或 SAP 中存在的 Xamarin.Forms 程序大容量。 每个平台包括调入此共享代码的小型应用程序存根。

Xamarin.Forms Api 将映射到每个平台上的本机控件，以便每个平台维护其特征的外观和感觉：

[![平台共享的视觉对象的三个屏幕截图](images/ch01fg03-small.png "每个平台上的 Xamarin.Forms 控件")](images/ch01fg03-large.png#lightbox "每个平台上的 Xamarin.Forms 控件")

从左到右的屏幕截图显示在 iPhone 和 Android 手机：

每个屏幕上的页面包含 Xamarin.Forms [ `Label` ](xref:Xamarin.Forms.Label)用于显示文本， [ `Button` ](xref:Xamarin.Forms.Button)适用于启动操作， [ `Switch` ](xref:Xamarin.Forms.Switch)为选择开/关值和一个[ `Slider` ](xref:Xamarin.Forms.Slider)用于指定连续范围内的值。 所有这四个视图都是的子级[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)上[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)。

此外附加到该网页是多个包含一个 Xamarin.Forms 工具栏[ `ToolbarItem` ](xref:Xamarin.Forms.ToolbarItem)对象。 这些是显示为图标的 iOS 和 Android 屏幕顶部和底部的 Windows 10 移动版屏幕。

Xamarin.Forms 还支持 XAML、 Extensible Application Markup Language 由 Microsoft 开发的用于多个应用程序平台。 如上所示的程序的所有视觉对象中所示在 XAML 中定义[ **PlatformVisuals** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter01/PlatformVisuals)示例。

Xamarin.Forms 程序可以确定哪些平台运行，并相应地执行不同的代码。 更有效地，开发人员可以编写面向的各种平台的自定义代码，并从 Xamarin.Forms 程序独立于平台的方式运行该代码。 开发人员还可以通过编写为每个平台的呈现器中创建其他控件。

Xamarin.Forms 是一个不错的解决方案用于业务线应用程序，或用于原型制作或进行快速概念证明演示，而是不太适合用于需要矢量图形或复杂的触摸交互的应用程序。

## <a name="your-development-environment"></a>在开发环境

根据想要面向的平台和哪些计算机需要使用取决于你的开发环境。

如果想要面向 iOS，则需要 Xcode 和 Xamarin 平台安装的 Mac。 还支持 Android 需要安装 Java 和所需的 Sdk。 您可以指定 iOS 和 Android 使用 Visual Studio for mac。

安装 Visual Studio 可以在 PC 上面向 iOS、 Android 和 Windows 的所有平台。 但是，面向从 Visual Studio iOS 仍需要 Xcode 和 Xamarin 平台安装的 Mac。

你可以测试连接了 USB 到计算机，其中一个实际设备或模拟器上的程序。

## <a name="installation"></a>安装

之前创建和生成 Xamarin.Forms 应用程序，您应尝试创建单独生成 iOS 应用程序、 Android 应用程序和一个 UWP 应用程序，具体取决于你希望目标和开发环境的平台。

Xamarin 和 Microsoft web 站点包含有关如何执行此操作的信息：

- [IOS 入门](~/ios/get-started/index.md)
- [Android 入门](~/android/get-started/index.md)
- [Windows 开发人员中心](http://dev.windows.com)

一次可以创建和运行这些各个平台的项目，您应该没有什么问题，创建并运行 Xamarin.Forms 应用程序。

## <a name="related-links"></a>相关链接

- [第 1 章全文 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch01-Apr2016.pdf)
- [第 1 章示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter01)
