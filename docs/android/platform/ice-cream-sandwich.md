---
title: Ice Cream Sandwich 功能
description: 本指南介绍了几个与 Android 4 API-Ice Cream Sandwich 应用程序开发人员的新功能。 它介绍了几种新的用户界面技术，然后检查各种 Android 4 提供的数据之间的应用程序和设备之间共享的新功能。
ms.prod: xamarin
ms.assetid: 78E18A62-C12F-A699-37FA-44B9F6B44273
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: 00b553ae8de0dfcd86d57d1d5e3e2a892d6b5463
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61091159"
---
# <a name="ice-cream-sandwich-features"></a>Ice Cream Sandwich 功能

_本指南介绍了几个与 Android 4 API-Ice Cream Sandwich 应用程序开发人员的新功能。它介绍了几种新的用户界面技术，然后检查各种 Android 4 提供的数据之间的应用程序和设备之间共享的新功能。_

## <a name="overview"></a>概述

Android OS 版本 4.0 (API 级别 14) 表示主要的改编 Android 操作系统的并包括大量的重要更改和升级，包括：

-   **更新用户界面**– 几个新的 UI 功能使开发人员能够更多电源和接口时它们创建应用程序用户的灵活性。 这些新功能包括： `GridLayout` ， `PopupMenu` ，`Switch`小组件中，和`TextureView`。 
-   **更好的硬件加速**– 2D 呈现现在所有 Android 控件在 GPU 上的发生。 此外，硬件加速，默认情况下为针对 Android 4.0 开发的所有应用程序中。 
-   **新的数据 Api** – 新访问不是以前正式访问，如日历数据和设备所有者的用户配置文件的数据。 
-   **应用数据共享**– 应用程序和设备之间共享数据状态变得过通过技术如`ShareActionProvider`，这样就可以轻松从操作栏中，创建共享操作并*Android 无线发送*有关*近场通信 (NFC)* ，便于管理单元中相互邻近的设备之间共享数据。 


在本文中，我们将探索这些功能和其他 Android 4.0 API，对所做的更改，我们将介绍如何通过 Xamarin.Android 使用的每个功能。

## <a name="user-interface-features"></a>用户界面功能

各种新的用户界面技术是适用于 Android 4，其中包括：

-   **[GridLayout](~/android/user-interface/layouts/grid-layout.md)**  – 支持的控件的 2D 网格布局。 
-   **[切换小组件](~/android/user-interface/controls/switch.md)** – 允许之间切换为 ON 或 OFF。 
-   **[TextureView](~/android/user-interface/controls/texture-view.md)**  – 使视频和 OpenGL 内容视图中的。 
-   **[导航栏](~/android/user-interface/controls/navigation-bar.md)** – 包含虚拟按钮后，home 和多任务。 


此外，其他 UI 元素得到了增强，如`<a href"/guides/android/user_interface/popup_menus">PopupMenu</a>`，这是现在更轻松地使用，和选项卡，它具有更精美的外观。

## <a name="sharing-features"></a>共享功能

Android 4 包括多种新技术，让我们跨设备和应用程序之间共享数据。 它还提供对各种类型的数据未之前提供，如日历信息和设备所有者的用户配置文件的访问。 在本部分中我们将介绍各种功能 Android 4 提供该地址这些领域包括：

-  **[Android 无线发送](~/android/platform/android-beam.md)** – 允许数据通过 NFC 共享。
-   **[ShareActionProvider](~/android/user-interface/controls/action-bar.md)**  – 创建提供程序，允许开发人员指定操作栏中的共享操作。 
-   **[用户配置文件](~/android/user-interface/user-profile.md)** – 提供对设备所有者的配置文件数据的访问。 
-   **[日历 API](~/android/user-interface/controls/calendar.md)**  – 从日历提供程序提供对日历数据的访问。 

## <a name="x86-emulators"></a>x86 仿真程序

ICS 尚不支持 x86 开发仿真程序。 x86 仿真程序仅在使用 Android 2.3.3，API 级别 10 支持。 请参阅[配置 x86 仿真程序](~/android/get-started/installation/android-emulator/index.md)有关详细信息。

## <a name="summary"></a>总结

本文介绍了各种现适用于 Android 4 的新技术。 我们讨论了新的用户界面功能等*GridLayout*， *PopupMenu*，并*开关*小组件。 我们还了解了一些用于控制系统 UI，以及如何使用新的支持*TextureView*。 然后，我们讨论了各种新共享和技术。 我们介绍如何*Android 无线发送*让我们使用的设备之间共享信息*NFC*，讨论新*日历 API*，还介绍了如何使用内置的*ShareActionProvider*。
最后，我们探讨了如何使用*ContactsContract*访问用户配置文件数据提供程序。



## <a name="related-links"></a>相关链接

- [Ice Cream Sandwich 示例](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/ICS_Samples/)
- [TextureViewDemo （示例）](https://developer.xamarin.com/samples/monodroid/TextureViewDemo/)
- [CalendarDemo （示例）](https://developer.xamarin.com/samples/monodroid/CalendarDemo/)
- [选项卡布局教程](~/android/user-interface/layouts/tab-layout/index.md)
- [Ice Cream Sandwich](https://developer.android.com/about/versions/android-4.0-highlights.html)
- [Android 4.0 平台](https://developer.android.com/about/versions/android-4.0.html)
