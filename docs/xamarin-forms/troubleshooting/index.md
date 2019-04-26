---
title: 疑难解答
description: 常见错误情形和如何解决这些问题
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 63291951-7375-4CBF-BCC3-2E4AD157A2C8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: fbe4fb6fce52636b59a9637ee0150c4c19fcc9da
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60850430"
---
# <a name="troubleshooting"></a>疑难解答

_常见错误情形和如何解决这些问题_

## <a name="error-unable-to-find-a-version-of-xamarinforms-compatible-with"></a>错误："找不到的 Xamarin.Forms 版本与兼容..."

在出现以下错误，可以**包控制台**窗口更新所有 Nuget 包或 Xamarin.Forms Android 应用程序项目中的 Xamarin.Forms 解决方案时：

```csharp
Attempting to resolve dependency 'Xamarin.Android.Support.v7.AppCompat (= 23.3.0.0)'.
Attempting to resolve dependency 'Xamarin.Android.Support.v4 (= 23.3.0.0)'.
Looking for updates for 'Xamarin.Android.Support.v7.MediaRouter'...
Updating 'Xamarin.Android.Support.v7.MediaRouter' from version '23.3.0.0' to '23.3.1.0' in project 'Todo.Droid'.
Updating 'Xamarin.Android.Support.v7.MediaRouter 23.3.0.0' to 'Xamarin.Android.Support.v7.MediaRouter 23.3.1.0' failed.
Unable to find a version of 'Xamarin.Forms' that is compatible with 'Xamarin.Android.Support.v7.MediaRouter 23.3.0.0'.
```

### <a name="what-causes-this-error"></a>什么因素会导致此错误？

Visual Studio for Mac （或 Visual Studio） 可能指示有可用的 Xamarin.Forms Nuget 包更新*及其所有依赖项*。 在 Xamarin Studio，该解决方案中**包**节点可能如下所示 （版本号可能不同）：

![](images/updates-available.png "Android 项目的包文件夹")

如果你尝试更新可能会发生此错误_所有_包。

这是因为与 Android 项目设置为 Android 6.0 (API 23) 的目标/编译版本或 Xamarin.Forms 上具有硬依赖项的下面*特定*的 Android 版本支持包。 虽然可能会提供这些包的更新的版本，并不一定与之兼容 Xamarin.Forms。

在这种情况下应更新_仅_ **Xamarin.Forms**包因为这将确保依赖项保留在兼容版本上。 只要它们不会导致要更新的 Android 支持包，可能还会单独更新已添加到你的项目的其他包。


> [!NOTE]
> 如果您使用的 Xamarin.Forms 2.3.4 或更高版本**和**Android 项目的目标/编译版本设置为 Android 7.0 (API 24) 或更高版本，则不能再上面提到的硬依赖项适用，并且可能会更新支持独立于 Xamarin.Forms 包的包。


### <a name="fix-remove-all-packages-and-re-add-xamarinforms"></a>解决方法：删除所有包，并重新添加 Xamarin.Forms

如果**Xamarin.Android.Support**包已更新为不兼容版本，最简单的解决方法是：

1. 然后手动删除在 Android 项目中，所有 Nuget 包
2. 重新添加**Xamarin.Forms**包。

这会自动下载*正确*的其他包版本。

若要查看此过程的视频，请参阅此[论坛帖子](https://forums.xamarin.com/discussion/comment/170012/#Comment_170012)。
