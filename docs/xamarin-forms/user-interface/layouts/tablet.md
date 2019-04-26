---
title: 对于平板电脑和桌面应用程序的布局
description: 本文介绍如何优化适用于平板电脑，而不是手机的 Xamarin.Forms 应用程序布局。
ms.prod: xamarin
ms.assetid: D62F472B-4345-4983-8403-659A538B591F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/01/2016
ms.openlocfilehash: 9d1f54fa4753ba2ef44ba9b8b48a84a3ca932c4b
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61369887"
---
# <a name="layout-for-tablet-and-desktop-apps"></a>对于平板电脑和桌面应用程序的布局

Xamarin.Forms 支持所有设备类型支持的平台上可用，因此除了手机，应用还可以运行：

* Ipad，
* Android 平板电脑
* Windows 平板电脑和台式计算机 （运行 Windows 10）。

此页简要讨论了。

* 受支持[设备类型](#Device_Types)，和
* 如何[优化](#optimize)适用于平板电脑与手机布局。

<a name="Device_Types" />

## <a name="device-types"></a>设备类型

较大屏幕设备是可用于所有 Xamarin.Forms 支持的平台。

### <a name="ipads-ios"></a>Ipad (iOS)

使用 Xamarin.Forms 模板自动包括 iPad 支持通过配置**Info.plist > 设备**将设置为**通用**（这意味着支持 iPhone 和 iPad）。

若要提供愉快的启动体验，并确保所有设备上使用完整屏幕分辨率，应确保[特定于 iPad 的启动屏幕](~/ios/app-fundamentals/images-icons/launch-screens.md)（使用情节提要） 提供。 这可确保应用程序在 iPad mini、 iPad 和 iPad Pro 设备上正确呈现。

在 iOS 9 之前的所有应用都占据整个屏幕在设备上，但现在可以执行一些 Ipad[拆分屏幕多任务处理](~/ios/platform/multitasking.md)。
这意味着您的应用程序可能需要多达只是精简的用户列在屏幕的屏幕或整个屏幕宽度的 50%的一侧。

[![](tablet-images/ipad-sml.png "iPad 屏幕示例拆分")](tablet-images/ipad.png#lightbox "iPad 拆分屏幕示例")

分割屏幕功能意味着您应设计您的应用程序很好地配合只需少量的 320 像素宽，或高达 1366年像素宽。

### <a name="android-tablets"></a>Android 平板电脑

Android 生态系统具有大量的受支持的屏幕尺寸、 从最大的平板电脑的小电话。 Xamarin.Forms 可以支持所有屏幕大小，但作为与其他平台可能会想要调整用户界面的较大的设备。

支持许多不同的屏幕分辨率，您可以在不同的大小以优化用户体验中的本机映像资源。
审阅[Android 资源](~/android/app-fundamentals/resources-in-android/index.md)文档 (特别[为不同的屏幕尺寸创建资源](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md)) 详细了解如何组织文件夹和 Android 应用程序中的文件名若要优化的映像资源包含在您的应用程序中的项目。

### <a name="windows-tablets-and-desktops"></a>Windows 平板电脑和台式电脑

若要支持平板电脑和运行 Windows 的台式计算机，你将需要使用[Windows UWP 支持](~/xamarin-forms/platform/windows/installation/index.md)，哪些生成通用 Windows 10 运行的应用程序。

Windows 平板电脑和台式计算机上运行的应用可调整到任意维度此外到正在运行全屏幕。

[![](tablet-images/splitscreen-sml.png "Windows 拆分屏幕示例")](tablet-images/splitscreen.png#lightbox "Windows 拆分屏幕示例")


<a name="optimize" />

## <a name="optimizing-for-tablet-and-desktop"></a>平板电脑和桌面优化

您可以调整 Xamarin.Forms 用户界面，具体取决于是否在手机或平板电脑/桌面设备正在使用。 这意味着您可以优化用于大屏幕设备，例如平板电脑和台式计算机的用户体验。


### <a name="deviceidiom"></a>Device.Idiom

可以使用[ `Device` ](~/xamarin-forms/platform/device.md)类，以更改您的应用或用户界面的行为。 使用`Device.Idiom`可以的枚举

```csharp
if (Device.Idiom == TargetIdiom.Phone)
{
    HeroImage.Source = ImageSource.FromFile("hero.jpg");
} else {
    HeroImage.Source = ImageSource.FromFile("herotablet.jpg");
}
```

这种方法可以通过展开，以对各个页面布局进行重大更改，或者甚至还可以呈现在较大的屏幕完全不同页面。

### <a name="leveraging-masterdetailpage"></a>利用 MasterDetailPage

[ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage)非常适合于较大的屏幕，尤其是在其中使用的 iPad 上[ `UISplitViewController` ](xref:UIKit.UISplitViewController)提供本机 iOS 体验。

审阅[此 Xamarin 博客文章](https://blog.xamarin.com/bringing-xamarin-forms-apps-to-tablets/)若要查看如何适应您的用户界面，以便电话使用一种布局和较大的屏幕可以使用另一个 (使用`MasterDetailPage`)。



## <a name="related-links"></a>相关链接

- [Xamarin 博客](https://blog.xamarin.com/bringing-xamarin-forms-apps-to-tablets/)
- [MyShoppe 示例](https://github.com/jamesmontemagno/myshoppe)
