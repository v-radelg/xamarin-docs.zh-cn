---
title: 平板电脑和桌面应用的布局
description: 本文介绍如何优化 Xamarin 的 Xamarin 应用程序布局，而不是手机。
ms.prod: xamarin
ms.assetid: D62F472B-4345-4983-8403-659A538B591F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/01/2016
ms.openlocfilehash: f91d0127d0f2ffe37e3e0ff016dee551a679ad84
ms.sourcegitcommit: e354aabfb39598e0ce11115db3e6bcebb9f68338
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2019
ms.locfileid: "72273109"
---
# <a name="layout-for-tablet-and-desktop-apps"></a>平板电脑和桌面应用的布局

Xamarin 支持在支持的平台上提供的所有设备类型，因此除了手机外，应用程序还可以在以下设备上运行：

- Ipad
- Android 平板电脑、
- Windows 平板电脑和台式计算机（运行 Windows 10）。

本页简要讨论：

- 支持的[设备类型](#Device_Types)和
- 如何[优化](#optimize)平板电脑与手机的布局。

<a name="Device_Types" />

## <a name="device-types"></a>设备类型

大屏幕设备适用于 Xamarin 支持的所有平台。

### <a name="ipads-ios"></a>Ipad （iOS）

Xamarin 模板通过将**info.plist > 设备**设置配置为**通用**（这意味着支持 iPhone 和 iPad）来自动包含 iPad 支持。

若要提供令人愉快的启动体验，并确保在所有设备上都使用全屏分辨率，应确保提供[iPad 特定的启动屏幕](~/ios/app-fundamentals/images-icons/launch-screens.md)（使用情节提要）。 这可确保在 iPad 迷你、iPad 和 iPad Pro 设备上正确呈现应用。

在 iOS 9 之前，所有应用程序都在设备上占用整个屏幕，但有些 Ipad 现在可以执行[拆分屏幕多任务](~/ios/platform/multitasking.md)。
这意味着，你的应用程序可能会在屏幕的一侧（50% 的宽度）或整个屏幕上只占用一个超薄柱形。

[![](tablet-images/ipad-sml.png "iPad 拆分屏幕示例")](tablet-images/ipad.png#lightbox "iPad 拆分屏幕示例")

拆分屏幕功能意味着您应该将您的应用程序设计为在最大宽度为320像素的情况下正常工作，或者尽可能多1366像素。

### <a name="android-tablets"></a>Android 平板电脑

Android 生态系统提供多种受支持的屏幕大小（从小手机到大平板电脑）。 Xamarin 可以支持所有屏幕大小，但与其他平台一样，你可能需要调整更大设备的用户界面。

支持许多不同的屏幕分辨率时，可以提供不同大小的本机映像资源来优化用户体验。
查看[android 资源](~/android/app-fundamentals/resources-in-android/index.md)文档（特别是[创建不同屏幕大小的资源](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md)），详细了解如何在 android 应用程序项目中构造文件夹和文件名，以便在中包含优化的映像资源您的应用程序。

### <a name="windows-tablets-and-desktops"></a>Windows 平板电脑和桌面

若要支持运行 Windows 的平板电脑和台式计算机，你将需要使用[WINDOWS UWP 支持](~/xamarin-forms/platform/windows/installation/index.md)，该支持生成在 windows 10 上运行的通用应用。

除了运行全屏外，还可以将 Windows 平板电脑和桌面上运行的应用调整为任意尺寸。

[![](tablet-images/splitscreen-sml.png "Windows 拆分屏幕示例")](tablet-images/splitscreen.png#lightbox "Windows 拆分屏幕示例")

<a name="optimize" />

## <a name="optimizing-for-tablet-and-desktop"></a>为平板电脑和桌面优化

你可以根据手机或平板电脑/桌面设备是否正在使用来调整 Xamarin. Forms 用户界面。 这意味着你可以优化大屏幕设备（如平板电脑和台式计算机）的用户体验。

### <a name="deviceidiom"></a>Device.Idiom

可以使用[`Device`](~/xamarin-forms/platform/device.md)类更改应用或用户界面的行为。 使用 `Device.Idiom` 枚举可以

```csharp
if (Device.Idiom == TargetIdiom.Phone)
{
    HeroImage.Source = ImageSource.FromFile("hero.jpg");
} else {
    HeroImage.Source = ImageSource.FromFile("herotablet.jpg");
}
```

此方法可以进行扩展，以对单个页面布局进行重大更改，甚至在较大屏幕上呈现完全不同的页面。

### <a name="leveraging-masterdetailpage"></a>利用 MasterDetailPage

[@No__t-1](xref:Xamarin.Forms.MasterDetailPage)非常适合用于更大的屏幕，特别是在 iPad 上，它使用[`UISplitViewController`](xref:UIKit.UISplitViewController)提供本机 iOS 体验。

查看[此 Xamarin 博客文章](https://devblogs.microsoft.com/xamarin/bringing-xamarin-forms-apps-to-tablets/)，了解如何改编你的用户界面，以便手机使用一个布局，而较大的屏幕可以使用另一个布局（@no__t 为-1）。

## <a name="related-links"></a>相关链接

- [Xamarin 博客](https://devblogs.microsoft.com/xamarin/bringing-xamarin-forms-apps-to-tablets/)
- [MyShoppe 示例](https://github.com/jamesmontemagno/myshoppe)
