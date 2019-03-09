---
title: 更智能的 Xamarin Android 支持 v4 / v13 NuGet 包
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: FE66A82A-6C05-4646-BC52-E806F5DC606C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: a990d933c258812b2b3d3374fb6435af06f729ea
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57671788"
---
# <a name="smarter-xamarin-android-support-v4--v13-nuget-packages"></a>更智能的 Xamarin Android 支持 v4 / v13 NuGet 包

## <a name="about-the-android-support-libraries"></a>有关 Android 支持库

Google 已创建了支持库以使新功能可用于较旧版本的 Android。 一般情况下，支持库中将给出的版本号是相互兼容的最低 Android API 级别其名称 (例如：支持 v4 只能在 API 级别 4 和更高版本。 有关详细信息这[Stack Overflow 讨论](https://stackoverflow.com/questions/9926403/android-support-package-compatibility-library-use-v4-or-v13))。 

两个支持库：`Support-v4`和`Support-v13`不能使用一起在相同的应用中，即，它们是互相排斥。 这是因为`Support-v13`实际包含的所有类型和实现`Support-v4`。 如果尝试在同一项目中同时引用将会遇到重复的类型错误。

## <a name="problems-with-referencing"></a>引用的问题

由于`Support-v4`变得如此受欢迎，很多第三方库现在依赖于它。 它们可能已选择依赖于支持 v13 相反，但更常见依赖_v4_由于这样使用这些第三方库的任何应用的支持一直到 4 的 API 级别的选项。

如果 Xamarin 第三方库引用`Xamarin.Android.Support.v4.dll`绑定到`Support-v4`，任何使用此库的应用还必须引用`Xamarin.Android.Support.v4.dll`。 这就存在问题时的相同应用程序还想要使用的功能的一些`Xamarin.Android.Support.v13.dll`绑定到`Support-v13`。 如果引用这两种绑定，将会遇到重复的类型错误。

## <a name="type-forwarded-v4-binding-assembly"></a>类型转发 v4 绑定程序集

若要解决此问题，我们创建了一个特殊`Xamarin.Android.Support.v4.dll`程序集不具有实现，但只需`[assembly: TypeForwardedTo (..)]`转发的所有特性`Support-v4`类型中实现`Xamarin.Android.Support.v13.dll`程序集。

这意味着开发人员可以引用此_的类型转发_在其应用中将满足对引用的程序集`Xamarin.Android.Support.v4.dll`任何第三方库，同时仍允许通过`Xamarin.Android.Support.v13.dll`要在应用中使用。

## <a name="nuget-assistance"></a>NuGet 协助

虽然开发人员可以手动添加正确的引用必要时，我们将能够使用 NuGet 来帮助选择正确的程序集 (任一普通_v4_绑定或类型转发_v4_程序集) 时安装 NuGet 包。

因此， `Xamarin.Android.Support.v4` NuGet 包现在包含以下逻辑：

如果应用面向 API 级别 13 (Gingerbread 3.2) 或更高版本：

*   `Xamarin.Android.Support.v13` NuGet 会自动添加为依赖项
*   _的类型转发_`Xamarin.Android.Support.v4.dll`将在项目中引用

如果您的应用程序面向任何低于 API 级别 13，则会正常`Xamarin.Android.Support.v4.dll`在项目中引用的绑定。

## <a name="do-i-have-to-use-support-v13"></a>我是否必须使用支持 v13？

如果您的应用程序面向 API 级别 13 或更高版本并且你选择使用`Xamarin Android Support-v4`NuGet 包，则`Xamarin Android Support v13`NuGet 包是必需的依赖关系。

我们认为非常小 （两个.jar 文件不同的 17 kb） 的应用程序大小增加很值得的兼容性和更少这会导致的问题。

如果您是使用偏激`Support-v4`在应用中的目标 API 级别 13 或更高版本，则可以始终手动下载`.nupkg`、 提取和引用程序集。
