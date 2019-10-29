---
title: Xamarin 中的权限
ms.prod: xamarin
ms.assetid: 3C440714-43E3-4D31-946F-CA59DAB303E8
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: 911f56026a1495099e81a542b30b280f26b6a9e1
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73025458"
---
# <a name="permissions-in-xamarinandroid"></a>Xamarin 中的权限

## <a name="overview"></a>概述

Android 应用程序在其自己的沙盒中运行，出于安全原因，无法访问设备上的某些系统资源或硬件。 用户必须显式授予对应用程序的权限，然后才能使用这些资源。 例如，应用程序无法访问设备上的 GPS，无需用户的显式权限。 如果应用尝试在没有权限的情况下访问受保护的资源，则 Android 会引发 `Java.Lang.SecurityException`。

开发应用程序时，应用程序开发人员在**androidmanifest.xml**中声明了权限。 Android 提供两个不同的工作流，用于获取用户对这些权限的同意：

- 对于面向 Android 5.1 （API 级别22）或更低版本的应用程序，在安装应用程序时发生权限请求。 如果用户未授予权限，则不会安装该应用。 安装应用后，除了卸载应用外，没有办法吊销权限。
- 从 Android 6.0 （API 级别23）开始，为用户提供了更多控制权限;只要在设备上安装了应用程序，他们就可以授予或撤消权限。 此屏幕截图显示 Google 联系人应用的权限设置。 它列出了各种权限，并允许用户启用或禁用权限：

!["权限示例" 屏幕](permissions-images/01-permissions-check.png) 

Android 应用必须在运行时进行检查，以查看它们是否有权访问受保护的资源。 如果应用没有权限，则必须使用由 Android SDK 提供的新 Api 发出请求，用户才可授予权限。 权限分为两类：

- **一般权限**&ndash; 这些权限对用户的安全或隐私带来了极大的安全风险。 Android 6.0 在安装时将自动授予正常权限。 有关[正常权限的完整列表](https://developer.android.com/guide/topics/permissions/normal-permissions.html)，请参阅 Android 文档。
- 与常规权限相比，**危险的权限**&ndash;，但危险的权限是保护用户安全或隐私的权限。 这些必须是用户显式授予的权限。 发送或接收 SMS 消息就是需要危险性权限的操作的一个示例。

> [!IMPORTANT]
> 权限所属的类别可能会随时间而改变。  可能会在将来的 API 级别中将已分类为 "常规" 权限的权限提升为危险权限。

危险权限进一步划分为[_权限组_](https://developer.android.com/guide/topics/permissions/requesting.html#perm-groups)。 权限组将包含逻辑上相关的权限。 当用户向权限组中的一个成员授予权限时，Android 会自动向该组的所有成员授予权限。 例如， [`STORAGE`](https://developer.android.com/reference/android/Manifest.permission_group.html#STORAGE)权限组包含 `WRITE_EXTERNAL_STORAGE` 和 `READ_EXTERNAL_STORAGE` 权限。 如果用户授予 `READ_EXTERNAL_STORAGE`的权限，则将自动授予 `WRITE_EXTERNAL_STORAGE` 权限。

在请求一个或多个权限之前，最佳做法是在请求权限之前，提供有关应用程序需要权限的原因的基本原理。 用户了解其原理后，应用程序可以请求用户的权限。 了解基础知识后，用户可以做出明智的决定，告诉他们是否希望授予权限并了解其后果。 

检查和请求权限的整个工作流称为 "_运行时权限_检查"，并可以在下图中汇总： 

[![运行时权限检查流程图](permissions-images/02-permissions-workflow-sml.png)](permissions-images/02-permissions-workflow.png#lightbox)

Android 支持库 precise-backports 了一些新的 Api，以获得更早版本的 Android 的权限。 这些向后移植 Api 将自动检查设备上的 Android 版本，因此无需每次都执行一次 API 级别检查。  

本文档介绍如何向 Xamarin Android 应用程序添加权限，以及面向 Android 6.0 （API 级别23）或更高版本的应用如何执行运行时权限检查。

> [!NOTE]
> 硬件的权限可能会影响应用通过 Google Play 筛选的方式。 例如，如果应用需要相机的权限，则 Google Play 不会在未安装照相机的设备上的 Google Play 商店中显示应用。

<a name="requirements" />

## <a name="requirements"></a>要求

强烈建议使用 xamarin Android 项目，其中包括 Xamarin。[支持](https://www.nuget.org/packages/Xamarin.Android.Support.Compat/)的 NuGet 包。 此程序包会将权限特定的 Api 向后移植到较旧版本的 Android，提供一个公共接口，而无需不断检查应用运行的 Android 版本。

## <a name="requesting-system-permissions"></a>请求系统权限

使用 Android 权限的第一步是在 Android 清单文件中声明权限。 无论应用是针对的 API 级别，都必须执行此操作。

面向 Android 6.0 或更高版本的应用程序无法假定，因为用户在过去某个时间点授予了权限，所以该权限将在下一次生效。 面向 Android 6.0 的应用必须始终执行运行时权限检查。 面向 Android 5.1 或更低版本的应用程序不需要执行运行时权限检查。

> [!NOTE]
> 应用程序应该只请求所需的权限。

### <a name="declaring-permissions-in-the-manifest"></a>在清单中声明权限

将权限添加到具有 `uses-permission` 元素的**androidmanifest.xml** 。 例如，如果应用程序要查找设备的位置，则需要良好的位置权限。 以下两个元素将添加到清单中： 

```xml
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
```

<!-- markdownlint-disable MD001 -->

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

可以使用 Visual Studio 中内置的工具支持来声明权限：

1. 双击 "**解决方案资源管理器**中的"**属性**"，然后选择" 属性窗口中的 " **Android 清单**" 选项卡：

    ["Android 清单" 选项卡中![必需的权限](permissions-images/04-required-permissions-vs-sml.png)](permissions-images/04-required-permissions-vs.png#lightbox)

2. 如果应用程序还没有 Androidmanifest.xml，请单击 "**未找到 androidmanifest.xml。单击以添加一个**，如下所示：

    [不![Androidmanifest.xml 消息](permissions-images/05-no-manifest-vs-sml.png)](permissions-images/05-no-manifest-vs.png#lightbox)

3. 从 "**所需权限**" 列表中选择应用程序所需的任何权限并保存：

    [选择![照相机权限](permissions-images/06-selected-permission-vs-sml.png)](permissions-images/06-selected-permission-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

可以使用 Visual Studio for Mac 中内置的工具支持来声明权限：

1. 双击 " **Solution Pad** " 中的项目，然后选择 " **> 生成 > Android 应用程序" 选项**：

    [显示![必需权限部分](permissions-images/04-required-permissions-xs-sml.png)](permissions-images/04-required-permissions-xs.png#lightbox)

2. 如果项目还没有**androidmanifest.xml**，请单击 "**添加 Android 清单**" 按钮：

    [缺少项目的 Android 清单![](permissions-images/05-no-manifest-xs-sml.png)](permissions-images/05-no-manifest-xs.png#lightbox)

3. 从 "**所需权限**" 列表中选择应用程序所需的任何权限，然后单击 **"确定"** ：

    [选择![照相机权限](permissions-images/03-select-permission-xs-sml.png)](permissions-images/03-select-permission-xs.png#lightbox)
    
-----

在生成时，Xamarin 会自动将某些权限添加到调试版本。 这样可以更轻松地调试应用程序。 具体而言，`INTERNET` 和 `READ_EXTERNAL_STORAGE`有两个值得注意的权限。 这些自动设置的权限将不会在 "**所需权限**" 列表中启用。 不过，发布版本只使用在 "**所需权限**" 列表中显式设置的权限。 

对于面向 Android 5.1 （API 级别22）或更低版本的应用程序，无需执行任何其他操作。 将在 Android 6.0 （API 23 级别23）或更高版本上运行的应用程序应继续到下一节，了解如何执行运行时权限检查。 

### <a name="runtime-permission-checks-in-android-60"></a>Android 6.0 中的运行时权限检查

`ContextCompat.CheckSelfPermission` 方法（适用于 Android 支持库）用于检查是否已授予特定权限。 此方法将返回[`Android.Content.PM.Permission`](xref:Android.Content.PM.Permission)枚举，该枚举具有以下两个值之一：

- 已授予 **`Permission.Granted`** &ndash; 指定的权限。
- 尚未授予 &ndash; 指定权限 **`Permission.Denied`** 。

此代码片段举例说明了如何在活动中检查相机权限： 

```csharp
if (ContextCompat.CheckSelfPermission(this, Manifest.Permission.Camera) == (int)Permission.Granted) 
{
    // We have permission, go ahead and use the camera.
} 
else 
{
    // Camera permission is not granted. If necessary display rationale & request.
}
```

最佳做法是，通知用户有关应用程序需要权限的原因，以便可以做出明智的决策来授予权限。 这种情况的一个示例是使用照片和地理标记的应用。 用户需要具有照相机权限，但可能并不清楚为什么应用程序也需要设备的位置。 基本原理应显示一条消息，以帮助用户了解 location 权限为何需要，以及需要相机权限。

`ActivityCompat.ShouldShowRequestPermissionRationale` 方法用于确定是否应向用户显示理论。 如果应显示给定权限的基本原理，此方法将返回 `true`。 此屏幕截图显示了一个应用程序显示的 Snackbar 的示例，该应用程序说明了应用程序需要知道设备位置的原因：

![位置的基本原理](permissions-images/07-rationale-snackbar.png) 

如果用户授予权限，则应调用 `ActivityCompat.RequestPermissions(Activity activity, string[] permissions, int requestCode)` 方法。 此方法需要以下参数：

- **活动**&ndash; 这是请求权限的活动，并将由 Android 的结果通知。
- **权限**&ndash; 请求的权限列表。
- **requestCode** &ndash; 一个整数值，该值用于将权限请求的结果与 `RequestPermissions` 调用进行匹配。 此值应大于零。

此代码片段是讨论的两种方法的一个示例。 首先，进行检查以确定是否应显示权限的理由。 如果显示了基本原理，则会显示一个 Snackbar，其中包含基本原理。 如果用户在 Snackbar 中单击 **"确定"** ，则该应用将请求权限。 如果用户不接受此基本原理，则应用不应继续请求权限。 如果未显示说明，则活动将请求权限：

```csharp
if (ActivityCompat.ShouldShowRequestPermissionRationale(this, Manifest.Permission.AccessFineLocation)) 
{
    // Provide an additional rationale to the user if the permission was not granted
    // and the user would benefit from additional context for the use of the permission.
    // For example if the user has previously denied the permission.
    Log.Info(TAG, "Displaying camera permission rationale to provide additional context.");

    var requiredPermissions = new String[] { Manifest.Permission.AccessFineLocation };
    Snackbar.Make(layout, 
                   Resource.String.permission_location_rationale,
                   Snackbar.LengthIndefinite)
            .SetAction(Resource.String.ok, 
                       new Action<View>(delegate(View obj) {
                           ActivityCompat.RequestPermissions(this, requiredPermissions, REQUEST_LOCATION);
                       }    
            )
    ).Show();
}
else 
{
    ActivityCompat.RequestPermissions(this, new String[] { Manifest.Permission.Camera }, REQUEST_LOCATION);
}
```

即使用户已授予权限，也可以调用 `RequestPermission`。 后续调用不是必需的，但它们向用户提供了确认（或撤消）权限的机会。 调用 `RequestPermission` 时，控制将移交给操作系统，该操作系统将显示用于接受权限的 UI：  

![权对话框](permissions-images/08-location-permission-dialog.png)

用户完成后，Android 会通过回调方法将结果返回到活动，`OnRequestPermissionResult`。 此方法是接口 `ActivityCompat.IOnRequestPermissionsResultCallback` 的一部分，必须由活动实现。 此接口具有一个 `OnRequestPermissionsResult`的方法，该方法将由 Android 调用以通知用户选择的活动。 如果用户已授予权限，则应用可以继续并使用受保护的资源。 下面显示了如何实现 `OnRequestPermissionResult` 的示例： 

```csharp
public override void OnRequestPermissionsResult(int requestCode, string[] permissions, Permission[] grantResults)
{
    if (requestCode == REQUEST_LOCATION) 
    {
        // Received permission result for camera permission.
        Log.Info(TAG, "Received response for Location permission request.");

        // Check if the only required permission has been granted
        if ((grantResults.Length == 1) && (grantResults[0] == Permission.Granted)) {
            // Location permission has been granted, okay to retrieve the location of the device.
            Log.Info(TAG, "Location permission has now been granted.");
            Snackbar.Make(layout, Resource.String.permission_available_camera, Snackbar.LengthShort).Show();            
        } 
        else 
        {
            Log.Info(TAG, "Location permission was NOT granted.");
            Snackbar.Make(layout, Resource.String.permissions_not_granted, Snackbar.LengthShort).Show();
        }
    } 
    else 
    {
        base.OnRequestPermissionsResult(requestCode, permissions, grantResults);
    }
}
```  

## <a name="summary"></a>总结

本指南讨论了如何在 Android 设备中添加和检查权限。 旧 Android 应用（API 级别 < 23）与新 Android 应用（API 级别 > 22）之间的权限工作方式的不同。 本文讨论了如何在 Android 6.0 中执行运行时权限检查。

## <a name="related-links"></a>相关链接

- [普通权限列表](https://developer.android.com/guide/topics/permissions/normal-permissions.html)
- [运行时权限示例应用](https://github.com/xamarin/monodroid-samples/tree/master/android-m/RuntimePermissions)
- [处理 Xamarin 中的权限](https://github.com/xamarin/recipes/tree/master/Recipes/android/general/projects/add_permissions_to_android_manifest)
