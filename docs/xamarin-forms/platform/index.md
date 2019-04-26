---
title: Xamarin.Forms 平台功能
description: 本指南介绍如何通过使用各种技术在 Xamarin.Forms 应用程序中充分利用特定于平台的功能。
ms.prod: xamarin
ms.assetid: 2C6CE42C-E380-4BB9-90CC-D0F4E60C4C03
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/08/2018
ms.openlocfilehash: 3f0156926f8d7a31e2e80318d7b05a909f158653
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61359155"
---
# <a name="xamarinforms-platform-features"></a>Xamarin.Forms 平台功能

Xamarin.Forms 是可扩展的并允许您使用的合并特定于平台的功能[效果](~/xamarin-forms/app-fundamentals/effects/index.md)，[自定义呈现器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)，则[DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md)， [MessagingCenter](~/xamarin-forms/app-fundamentals/messaging-center.md)，和的详细信息。

## <a name="androidandroidindexmd"></a>[Android](android/index.md)

本指南介绍了 Android 平台特定信息提供的 Xamarin.Forms，以及如何更新现有 Xamarin.Forms Android 应用程序以实现材料设计。

## <a name="device-classdevicemd"></a>[设备类](device.md)

如何使用`Device`类，以在共享的代码和用户界面 （包括使用 XAML） 创建特定于平台的行为。 此外介绍了`BeginInvokeOnMainThread`时修改 UI 控件从后台线程，这非常重要。

## <a name="iosiosindexmd"></a>[iOS](ios/index.md)

本指南介绍 iOS 平台特定信息提供的 Xamarin.Forms，以及如何执行其他 iOS 通过设置样式**Info.plist**和`UIAppearance`API。

## <a name="native-formsnative-formsmd"></a>[本机窗体](native-forms.md)

本机窗体允许 Xamarin.Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-派生页可供本机 Xamarin.iOS、 Xamarin.Android 和通用 Windows 平台 (UWP) 项目。

## <a name="native-viewsnative-viewsindexmd"></a>[本机视图](native-views/index.md)

从 iOS、 Android 和通用 Windows 平台的本机视图，可以直接引用 Xamarin.Forms 从。 上的本机视图，可以设置属性和事件处理程序，它们可以与 Xamarin.Forms 视图进行交互。

## <a name="platform-specificsplatform-specificsindexmd"></a>[平台特定信息](platform-specifics/index.md)

平台特定信息，可使用的功能仅适用于特定的平台，而无需自定义呈现器或效果。 此外，供应商可以使用效果创建其自己的平台特定信息。

## <a name="windowswindowsindexmd"></a>[Windows](windows/index.md)

本指南介绍了 Windows 平台特定信息提供的 Xamarin.Forms，以及如何将 UWP 项目添加到现有的 Xamarin.Forms 解决方案。
