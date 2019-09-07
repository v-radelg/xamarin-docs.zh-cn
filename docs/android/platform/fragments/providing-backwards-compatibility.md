---
title: 为 Android 支持包提供向后兼容性
ms.prod: xamarin
ms.assetid: 7511D2F8-2B4F-4200-C74E-E967153B2E8D
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/12/2017
ms.openlocfilehash: 838f8bdcf3bd82a31bf0d033eee628bd19ad1c30
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70757553"
---
# <a name="providing-backwards-compatibility-with-the-android-support-package"></a>为 Android 支持包提供向后兼容性

片段的有用性会受到限制，而不会向 Android 之前的3.0 （API 级别11）设备提供向后兼容性。 为了提供此功能，Google 引入了[支持库](https://developer.android.com/sdk/compatibility-library.html)（最初称为*Android 兼容库*，发布后），它将一些 api 从较新版本的 android precise-backports 到较旧版本的 android。 它是支持运行 Android 1.6 （API 级别4）的设备到 Android 2.3.3 的 Android 支持包。 （API 级别10）。

> [!NOTE]
> `ListFragment` 只有`DialogFragment`和可通过 Android 支持包获得。 Android 支持包中`PreferenceFragment,`不支持任何其他片段子类（如）。 它们不会在 Android 之前的3.0 应用程序中运行。 

## <a name="adding-the-support-package"></a>添加支持包

Android 支持包不会自动添加到 Xamarin Android 应用程序中。 Xamarin 提供[Android 支持库 V4 NuGet 包](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)，简化了将支持库添加到 Xamarin Android 应用程序的工作。如以下屏幕截图中所示，将支持包包含在 Xamarin android 应用程序中包括你的 Xamarin Android 项目[中的支持](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)包： 

[![添加到项目中的 Android 支持库 v4 包的屏幕截图](providing-backwards-compatibility-images/02-sml.png)](providing-backwards-compatibility-images/02.png#lightbox)

执行这些步骤后，便可以在早期版本的 Android 中使用片段。 在这些早期版本中，片段 Api 现在的作用相同，但以下情况例外： 

- **更改最低 Android 版本**&ndash;应用程序不再需要面向 Android 3.0 或更高版本，如下所示： 

    [![在 Android 清单下设置的最小 Android 目标的屏幕截图](providing-backwards-compatibility-images/03-sml.png)](providing-backwards-compatibility-images/03.png#lightbox)

- **扩展 FragmentActivity**承载片段的活动现在必须从`Android.Support.V4.App.FragmentActivity`继承，而不是从`Android.App.Activity`继承。 &ndash; 

- **更新命名空间**继承自的`Android.App.Fragment`类现在必须继承自`Android.Support.V4.App.Fragment`。 &ndash; 删除源代码文件顶部的`using Android.App;` using 语句 ""，并将其替换为 " `using Android.Support.V4.App` "。 

- **使用 SupportFragmentManager**公开一个属性，`FragmentManager`该属性必须用于获取对的引用。 `SupportingFragmentManager` &ndash; `Android.Support.V4.App.FragmentActivity` 例如: 

```csharp
FragmentTransaction fragmentTx = this.SupportingFragmentManager.BeginTransaction();
DetailsFragment detailsFrag = new DetailsFragment();
fragmentTx.Add(Resource.Id.fragment_container, detailsFrag);
fragmentTx.Commit();
```

进行这些更改后，就可以在 Android 1.6 或2.x 上运行基于片段的应用程序，也可以在 Honeycomb 和冰淇淋三明治上运行。 

## <a name="related-links"></a>相关链接

- [Android 支持库 v4 NuGet](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)
