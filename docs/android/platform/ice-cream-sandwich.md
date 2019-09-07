---
title: 冰淇淋三明治功能
description: 本文介绍了使用 Android 4 API 冰淇淋三明治的应用程序开发人员可以使用的一些新功能。 它介绍了几种新的用户界面技术，然后检查 Android 4 提供的各种新功能，用于在应用程序之间以及设备之间共享数据。
ms.prod: xamarin
ms.assetid: 78E18A62-C12F-A699-37FA-44B9F6B44273
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: 8cbb7c9c3da92b1ea5140e6f85accf53dab0bf80
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70761463"
---
# <a name="ice-cream-sandwich-features"></a>冰淇淋三明治功能

_本文介绍了使用 Android 4 API 冰淇淋三明治的应用程序开发人员可以使用的一些新功能。它介绍了几种新的用户界面技术，然后检查 Android 4 提供的各种新功能，用于在应用程序之间以及设备之间共享数据。_

## <a name="overview"></a>概述

Android OS 版本4.0 （API 级别14）表示 Android 操作系统的主要重新处理，并包括许多重要更改和升级，其中包括：

- **已更新的用户界面**–当开发人员创建应用程序用户界面时，多个新的 UI 功能使开发人员能够更强大的功能。 这些新功能包括： `GridLayout` 、 `PopupMenu` 、 `Switch`小组件和`TextureView` 。 
- **更好的硬件加速**–二维渲染现在适用于所有 Android 控件的 GPU。 此外，默认情况下，在为 Android 4.0 开发的所有应用程序中启用硬件加速。 
- **新的数据 api** -对以前未正式访问的数据有新的访问权限，如日历数据和设备所有者的用户配置文件。 
- **应用数据共享**–在`ShareActionProvider`应用程序和设备之间共享数据现在比以往更容易通过技术（例如），这使你可以轻松地从操作栏创建共享操作，将*Android 横梁*用于*近乎现场通信（NFC）* ，使其能够在彼此接近的设备之间共享数据。 

在本文中，我们将探讨对 Android 4.0 API 的这些功能和其他更改，我们将介绍如何将每项功能与 Xamarin 一起使用。

## <a name="user-interface-features"></a>用户界面功能

Android 4 提供多种新的用户界面技术，其中包括：

- **[GridLayout](~/android/user-interface/layouts/grid-layout.md)** –支持控件的2d 网格布局。 
- **[切换小组件](~/android/user-interface/controls/switch.md)** –允许在打开或关闭之间切换。 
- **[TextureView](~/android/user-interface/controls/texture-view.md)** –启用视图中的视频和 OpenGL 内容。 
- **[导航栏](~/android/user-interface/controls/navigation-bar.md)** -包含用于后退、home 和多任务的虚拟按钮。 

另外，还增强了其他 UI 元素，如`<a href"/guides/android/user_interface/popup_menus">PopupMenu</a>`现在更易于使用的和选项卡，它们具有更精美的外观。

## <a name="sharing-features"></a>共享功能

Android 4 包括几种新技术，使我们能够跨设备和应用程序共享数据。 它还提供对以前未提供的各种数据类型（例如日历信息和设备所有者的用户配置文件）的访问权限。 在本部分中，我们将检查 Android 4 提供的各种功能，这些功能包括：

- **[Android 无线](~/android/platform/android-beam.md)** 支持-允许通过 NFC 共享数据。
- **[ShareActionProvider](~/android/user-interface/controls/action-bar.md)** –创建允许开发人员指定操作栏共享操作的访问接口。 
- **[用户配置文件](~/android/user-interface/user-profile.md)** –提供对设备所有者的配置文件数据的访问。 
- **[日历 API](~/android/user-interface/controls/calendar.md)** –提供对日历提供商提供的日历数据的访问。 

## <a name="x86-emulators"></a>x86 模拟器

ICS 还不支持使用 x86 模拟器进行开发。 x86 模拟器仅支持 Android 2.3.3 （API 级别10）。 有关详细信息，请参阅[配置 X86 模拟器](~/android/get-started/installation/android-emulator/index.md)。

## <a name="summary"></a>总结

本文介绍了现在适用于 Android 4 的各种新技术。 我们回顾了新的用户界面功能，如*GridLayout*、 *PopupMenu*和*切换*小组件。 还介绍了一些用于控制系统 UI 的新支持，并介绍了如何使用*TextureView*。 然后我们讨论了各种新的共享技术。 我们介绍了*Android 横梁*如何让你在使用*NFC*的设备上共享信息，并讨论了新的*日历 API*，还演示了如何使用内置的*ShareActionProvider*。
最后，我们探讨了如何使用*ContactsContract*提供程序访问用户配置文件数据。

## <a name="related-links"></a>相关链接

- [冰淇淋三明治示例](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-ics-samples)
- [TextureViewDemo （示例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/textureviewdemo)
- [CalendarDemo （示例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/calendardemo)
- [选项卡布局教程](~/android/user-interface/layouts/tab-layout/index.md)
- [冰淇淋三明治](https://developer.android.com/about/versions/android-4.0-highlights.html)
- [Android 4.0 平台](https://developer.android.com/about/versions/android-4.0.html)
