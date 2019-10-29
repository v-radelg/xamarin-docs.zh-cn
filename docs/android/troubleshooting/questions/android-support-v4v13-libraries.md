---
title: 更智能的 Xamarin Android 支持 v4 / v13 NuGet 包
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: FE66A82A-6C05-4646-BC52-E806F5DC606C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: c74cac6a6d669385855999a565711a3fdc85f3b7
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73019540"
---
# <a name="smarter-xamarin-android-support-v4--v13-nuget-packages"></a>更智能的 Xamarin Android 支持 v4 / v13 NuGet 包

## <a name="about-the-android-support-libraries"></a>关于 Android 支持库

Google 已创建支持库，使新功能可用于较早版本的 Android。 一般情况下，支持库在其名称中提供版本号，这是与兼容的最低 Android API 级别（例如：支持-v4 只能在 API 级别4和更高版本上使用。 有关详细信息，请[Stack Overflow 讨论](https://stackoverflow.com/questions/9926403/android-support-package-compatibility-library-use-v4-or-v13)）。 

两个支持库：不能在同一应用中一起使用 `Support-v4` 和 `Support-v13`，也就是说，它们是互斥的。 这是因为 `Support-v13` 实际上包含 `Support-v4`的所有类型和实现。 如果尝试在同一项目中同时引用这两个项目，则会遇到重复的类型错误。

## <a name="problems-with-referencing"></a>引用问题

由于 `Support-v4` 现已变得非常普遍，因此许多第三方库现在依赖于它。 他们可以选择依赖于 v13，但更常见的做法是依赖于_v4_ ，因为使用这些第三方库的任何应用都可以选择支持 API 级别。

如果 Xamarin 第三方库引用 `Xamarin.Android.Support.v4.dll` 绑定到 `Support-v4`，则使用此库的任何应用程序还必须引用 `Xamarin.Android.Support.v4.dll`。 当同一个应用程序还希望使用 `Xamarin.Android.Support.v13.dll` 绑定中的某些功能来 `Support-v13`时，这会成为一个问题。 如果同时引用这两个绑定，则会遇到重复的类型错误。

## <a name="type-forwarded-v4-binding-assembly"></a>类型转发 v4 绑定程序集

若要解决此问题，我们创建了一个没有实现的特殊 `Xamarin.Android.Support.v4.dll` 程序集，但只是 `[assembly: TypeForwardedTo (..)]` 属性，这些属性将所有 `Support-v4` 类型转发到 `Xamarin.Android.Support.v13.dll` 程序集内的实现。

这意味着开发人员可以在其应用程序中引用此_类型转发的_程序集，这将满足任何第三方库对 `Xamarin.Android.Support.v4.dll` 的引用，同时仍允许在应用中使用 `Xamarin.Android.Support.v13.dll`。

## <a name="nuget-assistance"></a>NuGet 帮助

尽管开发人员可以手动添加所需的正确引用，但在安装 NuGet 包时，我们可以使用 NuGet 来帮助选择正确的程序集（常规_v4_绑定或类型转发_v4_程序集）。

因此，`Xamarin.Android.Support.v4` NuGet 包现在包含以下逻辑：

如果你的应用面向 API 级别13（Gingerbread 3.2）或更高版本：

* `Xamarin.Android.Support.v13` NuGet 将自动添加为依赖项
* 将在项目中引用_类型转发_的 `Xamarin.Android.Support.v4.dll`

如果你的应用面向的是低于 API 级别13的任何内容，你将获得你的项目中引用的普通 `Xamarin.Android.Support.v4.dll` 绑定。

## <a name="do-i-have-to-use-support-v13"></a>我是否必须使用 v13？

如果你的应用面向 API 级别13或更高版本，并且你选择使用 `Xamarin Android Support-v4` NuGet 包，则 `Xamarin Android Support v13` NuGet 包是必需的依赖项。

我们认为应用程序大小的变化非常小（两个 .jar 文件在17kb 上存在差异）非常值得，这就是兼容性，并且它带来的麻烦更少。

如果你 adamant 在面向 API 级别13或更高版本的应用程序中使用 `Support-v4`，你始终可以手动下载 `.nupkg`、提取和引用程序集。
