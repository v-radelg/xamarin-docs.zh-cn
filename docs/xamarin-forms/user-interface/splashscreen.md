---
title: Xamarin。窗体初始屏幕
description: 本文介绍如何为 Xamarin 应用程序创建初始屏幕。
ms.prod: xamarin
ms.assetId: 338C8F60-90F2-4B3D-BB47-7F846AE8DC6C
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 10/1/2019
ms.openlocfilehash: 2624c3f35a9cfac122a0b36ea8c1684d30f57824
ms.sourcegitcommit: 5110d1279809a2af58d3d66cd14c78113bb51436
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2019
ms.locfileid: "72035772"
---
# <a name="xamarinforms-splash-screen"></a>Xamarin。窗体初始屏幕

当应用程序完成其初始化过程时，应用程序通常会出现启动延迟。 在应用程序启动时，开发人员可能希望提供品牌体验（通常称为初始屏幕）。 本文介绍如何为 Xamarin 应用程序创建初始屏幕。

在本机启动序列完成后，会在每个平台上初始化 Xamarin。 Xamarin. 窗体已初始化：

- 在 Android 上 @no__t 类的 `OnCreate` 方法中。
- 在 iOS 上，@no__t 类的 `FinishedLaunching` 方法。
- 在 UWP 上的 @no__t 的 @no__t 0 方法中。

启动应用程序后，初始屏幕应该尽快显示，但在启动序列后期，就不会对其进行初始化，这意味着必须在每个平台上的 Xamarin 之外实现初始屏幕。 以下部分介绍如何在每个平台上创建初始屏幕。

## <a name="xamarinforms-android-splash-screen"></a>Xamarin. Forms Android 初始屏幕

若要在 Android 上创建初始屏幕，则需要创建一个初始 @no__t 为 `MainLauncher`，其中包含特殊主题。 初始 `Activity` 启动后，它将启动主 `Activity`，并提供正常的应用程序主题。

有关 Xamarin 中的初始屏幕的详细信息，请参阅[xamarin。](~/android/user-interface/splash-screen.md)

## <a name="xamarinforms-ios-splash-screen"></a>Xamarin Forms iOS 初始屏幕

IOS 上的初始屏幕称为启动屏幕。 在 iOS 上创建启动屏幕需要创建一个用于定义启动屏幕的 UI 的情节提要，然后将情节提要设置为**info.plist**中的启动屏幕。

有关在 Xamarin 上启动屏幕的详细信息，请参阅[Xamarin 启动屏幕](~/ios/app-fundamentals/images-icons/launch-screens.md)。

## <a name="xamarinforms-uwp-splash-screen"></a>Xamarin。窗体 UWP 初始屏幕

在 UWP 上， **appxmanifest.xml**包含带有**初始屏幕**子菜单的 "**视觉对象资产**" 选项卡。 初始屏幕图形可以在此菜单中指定：

[UWP 上的1Setting 初始屏幕 @no__t](splashscreen-images/uwp-splashscreen-cropped.png)](splashscreen-images/uwp-splashscreen.png#lightbox)

## <a name="related-links"></a>相关链接

- [Xamarin Android 初始屏幕](~/android/user-interface/splash-screen.md)
- [Xamarin iOS 启动屏幕](~/ios/app-fundamentals/images-icons/launch-screens.md)
