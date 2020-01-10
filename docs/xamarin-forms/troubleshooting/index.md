---
title: 故障排除
description: 常见错误情况和解决方法
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 63291951-7375-4CBF-BCC3-2E4AD157A2C8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: b38e33e05b0bb9d40582611857671d6617023b35
ms.sourcegitcommit: 4691b48f14b166afcec69d1350b769ff5bf8c9f6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2020
ms.locfileid: "75728312"
---
# <a name="troubleshooting"></a>故障排除

_常见错误情况和解决方法_

## <a name="error-unable-to-find-a-version-of-xamarinforms-compatible-with"></a>错误： "找不到版本的 Xamarin。与之兼容的窗体 ..."

更新 Xamarin 解决方案或 Xamarin 中的所有 NuGet 包时，**包控制台**窗口中可能会出现以下错误：

```csharp
Attempting to resolve dependency 'Xamarin.Android.Support.v7.AppCompat (= 23.3.0.0)'.
Attempting to resolve dependency 'Xamarin.Android.Support.v4 (= 23.3.0.0)'.
Looking for updates for 'Xamarin.Android.Support.v7.MediaRouter'...
Updating 'Xamarin.Android.Support.v7.MediaRouter' from version '23.3.0.0' to '23.3.1.0' in project 'Todo.Droid'.
Updating 'Xamarin.Android.Support.v7.MediaRouter 23.3.0.0' to 'Xamarin.Android.Support.v7.MediaRouter 23.3.1.0' failed.
Unable to find a version of 'Xamarin.Forms' that is compatible with 'Xamarin.Android.Support.v7.MediaRouter 23.3.0.0'.
```

### <a name="what-causes-this-error"></a>导致此错误的原因是什么？

Visual Studio for Mac （或 Visual Studio）可能指示更新适用于 Xamarin。 Forms NuGet 程序包*及其所有依赖项*。 在 Xamarin Studio 中，解决方案的 "**包**" 节点可能如下所示（版本号可能不同）：

![](images/updates-available.png "Android Project Packages Folder")

如果尝试更新_所有_包，则可能出现此错误。

这是因为 Android 项目设置为 Android 6.0 （API 23）或更低版本的目标/编译版本，因此，在*特定*版本的 android 支持包上，Forms 会很难依赖。 尽管可以使用这些包的更新版本，但 Xamarin 不一定与它们兼容。

在这种情况下，应_仅_更新**Xamarin. Forms**包，因为这将确保依赖项保持在兼容的版本上。 你添加到项目中的其他包还可以单独更新，只要它们不会导致 Android 支持包更新。

> [!NOTE]
> 如果你使用的是 Xamarin. Forms 2.3.4 或更高版本，**并且**你的 android 项目的目标/编译版本设置为 android 7.0 （API 24）或更高版本，则上面提到的硬依赖关系将不再适用，你可以独立于 Xamarin 包更新支持包。

### <a name="fix-remove-all-packages-and-re-add-xamarinforms"></a>修复：删除所有包，然后重新添加 Xamarin。 Forms

如果**支持**包更新为不兼容的版本，最简单的解决方法是：

1. 手动删除 Android 项目中的所有 NuGet 包，然后
2. 重新添加**Xamarin. Forms**包。

这将自动下载其他包的*正确*版本。

若要查看此过程的视频，请参阅此[论坛帖子](https://forums.xamarin.com/discussion/comment/170012/#Comment_170012)。
