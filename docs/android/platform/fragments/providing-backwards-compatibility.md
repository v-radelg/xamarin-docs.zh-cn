---
title: 提供向后兼容性与 Android 支持包
ms.prod: xamarin
ms.assetid: 7511D2F8-2B4F-4200-C74E-E967153B2E8D
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/12/2017
ms.openlocfilehash: 48ef40ce8560fd9fbb842dde70622d968591ab98
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57666900"
---
# <a name="providing-backwards-compatibility-with-the-android-support-package"></a>提供向后兼容性与 Android 支持包

片段的有效性是有限而无需向后兼容性与预 Android 3.0 (API 级别 11) 设备。 若要提供此功能，Google 引入了[支持库](https://developer.android.com/sdk/compatibility-library.html)(最初称为*Android Compatibility Library*发布时) 的 backports 的一些从较新版本的 Api与旧版本的 Android android。 它是 Android 支持包，它使运行 Android 2.3.3 Android 1.6 （API 级别 4） 的设备。 （API 级别 10）。

> [!NOTE]
> 仅`ListFragment`和`DialogFragment`通过 Android 支持包。 没有其他片段子类，如`PreferenceFragment,`所支持的 Android 支持包。 它们不会预先 Android 3.0 应用程序中。 


## <a name="adding-the-support-package"></a>添加支持包

Android 支持包不会自动添加到 Xamarin.Android 应用程序。 Xamarin 提供了[Android 支持库 v4 NuGet 包](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)来简化支持库添加到 Xamarin.Android 应用程序。将支持包包含到你的 Xamarin.Android 应用程序包含[Android 支持库 v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)组件到 Xamarin.Android 项目中，如以下屏幕截图中所示： 

[![屏幕截图 Android 支持库 v4 包添加到项目](providing-backwards-compatibility-images/02-sml.png)](providing-backwards-compatibility-images/02.png#lightbox)

在执行这些步骤后，就可以使用在早期版本的 Android 片段。 片段 Api 将工作相同现在在这些早期版本，但存在以下例外： 

-   **更改最低 Android 版本**&ndash;应用程序不再需要面向 Android 3.0 或更高版本，如下所示： 

    [![在 Android 清单下设置的最低 Android 屏幕截图目标](providing-backwards-compatibility-images/03-sml.png)](providing-backwards-compatibility-images/03.png#lightbox)

-   **扩展 FragmentActivity** &ndash;承载片段的活动必须现在继承自`Android.Support.V4.App.FragmentActivity`，而不是从`Android.App.Activity`。 

-   **更新命名空间**&ndash;类继承自`Android.App.Fragment`现在必须继承自`Android.Support.V4.App.Fragment`。 删除正在使用语句" `using Android.App;` "顶部的源代码文件，并将其替换为" `using Android.Support.V4.App` "。 

-   **使用 SupportFragmentManager** &ndash; `Android.Support.V4.App.FragmentActivity`公开`SupportingFragmentManager`属性，必须用来实现的引用`FragmentManager`。 例如： 

```csharp
FragmentTransaction fragmentTx = this.SupportingFragmentManager.BeginTransaction();
DetailsFragment detailsFrag = new DetailsFragment();
fragmentTx.Add(Resource.Id.fragment_container, detailsFrag);
fragmentTx.Commit();
```

这些更改后，它将可以在运行基于片段的应用程序，以及 Honeycomb 和 Ice Cream Sandwich 上 Android 1.6 或 2.x。 


## <a name="related-links"></a>相关链接

- [Android 支持库 v4 NuGet](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)
