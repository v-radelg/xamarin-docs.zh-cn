---
title: Permissions In Xamarin.Android
ms.prod: xamarin
ms.assetid: 3C440714-43E3-4D31-946F-CA59DAB303E8
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: 204dd903586164691d068a956e741c406df10b36
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61026910"
---
# <a name="permissions-in-xamarinandroid"></a>Permissions In Xamarin.Android


## <a name="overview"></a>概述

在其自己的沙盒中运行的 android 应用程序和安全原因不能访问某些系统资源或硬件设备上。 它可能会使用这些资源之前，用户必须显式授予应用权限。 例如，应用程序不能从用户访问而无需显式权限的设备上 GPS。 Android 将引发`Java.Lang.SecurityException`如果应用程序尝试访问受保护的资源而无需权限。

权限声明中**AndroidManifest.xml**由应用开发的应用程序开发。 Android 有两个不同的工作流以获取这些权限的用户的同意：
 
* 对于针对 Android 5.1 （API 级别 22） 或更低版本的应用程序，安装了应用时，将出现的权限请求。 如果用户未授予的权限，将不安装该应用程序。 安装应用后，没有办法通过卸载应用程序撤销权限除外。
* 从 Android 6.0 （API 级别 23） 开始，用户提供更好地控制权限;在可授予或撤消的权限，只要在设备上安装应用。 此屏幕截图显示的权限设置的 Google 联系人应用。 它列出了不同的权限，并允许用户启用或禁用的权限：

![示例权限屏幕](permissions-images/01-permissions-check.png) 

Android 应用程序必须在运行时以查看它们是否具有访问受保护的资源的权限检查。 如果应用不具有权限，则它必须进行请求使用新的 Api 提供的 Android SDK 的用户授予的权限。 权限划分为两个类别：

* **正常的权限**&ndash;这些是这会带来很少的安全风险，用户的安全或隐私的权限。 在安装时，android 6.0 将自动授予正常的权限。 请参阅 Android 文档[普通权限的完整列表](https://developer.android.com/guide/topics/permissions/normal-permissions.html)。
* **危险权限**&ndash;与普通权限相比危险的权限是指那些保护用户的安全或隐私。 这些必须显式授予用户。 发送或接收短信是操作的需要非常危险的权限的示例。

> [!IMPORTANT]
> 随着时间的推移可能会更改权限所属的类别。  有可能，此分类为"正常"权限就是将权限提升的将来 API 级别非常危险的权限。

危险权限进一步细分为多个[_权限组_](https://developer.android.com/guide/topics/permissions/requesting.html#perm-groups)。 权限组将保存在逻辑上关联的权限。 时用户授予权限的权限组的一个成员，Android 会自动为该组的所有成员授予权限。 例如， [ `STORAGE` ](https://developer.android.com/reference/android/Manifest.permission_group.html#STORAGE)权限组保留着两`WRITE_EXTERNAL_STORAGE`和`READ_EXTERNAL_STORAGE`权限。 如果用户授予权限`READ_EXTERNAL_STORAGE`，则`WRITE_EXTERNAL_STORAGE`同时自动授予权限。

请求一个或多个权限之前, 它是最佳做法提供关于为何应用需要请求权限前的权限了合理依据。 一旦用户理解基本原理，该应用程序可从用户请求权限。 通过了解基本原理，用户可以做出明智的决策，如果他们想要授予权限，并了解负面影响，如果它们不匹配。 

检查和请求权限的整个工作流被称为_运行时权限_检查，并可以汇总成以下关系图： 

[![运行时权限检查流图表](permissions-images/02-permissions-workflow-sml.png)](permissions-images/02-permissions-workflow.png#lightbox)

Android 支持库 backports 某些较旧版本的 Android 权限的新 Api。 这些向后移植 Api 将自动检查设备上的 Android 版本，因此不需要执行 API 级别检查每个时间。  

本文档将讨论如何将权限添加到 Xamarin.Android 应用程序以及如何面向 Android 6.0 （API 级别 23） 的应用，或更高版本应该执行运行时权限检查。


> [!NOTE]
> 就可以对硬件的权限可能会影响通过 Google Play 筛选应用的方式。 例如，如果应用需要用于相机的权限，然后 Google Play 不会显示应用不具有安装摄像头的设备上 Google Play 商店中。


<a name="requirements" />

## <a name="requirements"></a>要求

强烈建议包括的 Xamarin.Android 项目[Xamarin.Android.Support.Compat](https://www.nuget.org/packages/Xamarin.Android.Support.Compat/) NuGet 包。 而且无需不断地与旧版本的 Android，提供一种常见的特定 Api 接口。 此包将向后移植权限检查应用程序运行的 Android 的版本。


## <a name="requesting-system-permissions"></a>请求系统权限

使用 Android 权限的第一步是声明的权限在 Android 清单文件。 这必须完成而不考虑 API 级别应用所面向。

面向 Android 6.0 或更高版本不能假定，因为用户授予在过去，在某个时间点的权限，权限将为有效的下一步的时间的应用程序。 面向 Android 6.0 的应用必须始终执行运行时权限检查。 面向 Android 5.1 或更低的应用，不需要执行运行时权限检查。

> [!NOTE]
> 应用程序应只请求所需的权限。


### <a name="declaring-permissions-in-the-manifest"></a>声明在清单中的权限

权限添加到**AndroidManifest.xml**与`uses-permission`元素。 例如，如果应用程序是查找该设备的位置，它需要正常运行，并且课程位置权限。 以下两个元素添加到清单中： 

```xml
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
```

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

它是可以声明使用 Visual Studio 中内置的工具支持的权限：

1. 双击**属性**中**解决方案资源管理器**，然后选择**Android 清单**属性窗口中的选项卡：

    [![Android 清单选项卡中需要的权限](permissions-images/04-required-permissions-vs-sml.png)](permissions-images/04-required-permissions-vs.png#lightbox)

2. 如果应用程序尚不包含 AndroidManifest.xml，单击**找到任何 AndroidManifest.xml。单击此项可添加一个**，如下所示：

    [![任何 AndroidManifest.xml 消息](permissions-images/05-no-manifest-vs-sml.png)](permissions-images/05-no-manifest-vs.png#lightbox)

3. 选择你的应用程序需要从任何权限**所需的权限**列出并保存：

    [![所选权限示例照相机](permissions-images/06-selected-permission-vs-sml.png)](permissions-images/06-selected-permission-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

它是可以声明使用内置到 Visual Studio for Mac 的工具支持的权限：

1. 双击项目中的**Solution Pad** ，然后选择**选项 > 生成 > Android 应用程序**:

    [![所需的权限部分所示](permissions-images/04-required-permissions-xs-sml.png)](permissions-images/04-required-permissions-xs.png#lightbox)

2. 单击**添加 Android 清单**按钮，如果项目不具有**AndroidManifest.xml**:

    [![缺少项目的 Android 清单](permissions-images/05-no-manifest-xs-sml.png)](permissions-images/05-no-manifest-xs.png#lightbox)

3. 选择你的应用程序需要从任何权限**所需的权限**列表中，单击**确定**:

    [![所选权限示例照相机](permissions-images/03-select-permission-xs-sml.png)](permissions-images/03-select-permission-xs.png#lightbox)
    
-----

Xamarin.Android 将自动添加某些权限在生成时的调试版本。 这将使调试更轻松的应用程序。 具体而言，两个值得注意权限是`INTERNET`和`READ_EXTERNAL_STORAGE`。 这些自动设置的权限不会在中启用**所需的权限**列表。 发行版本，但是，请使用仅中显式设置的权限**所需的权限**列表。 

对于面向 Android 5.1 （API 级别 22） 或更低版本的应用程序，没有任何更多，需要完成。 将运行 Android 6.0 （API 23 级别 23） 或更高版本的应用程序应继续进行到下一节介绍了如何执行权限检查的运行的时。 


### <a name="runtime-permission-checks-in-android-60"></a>在 Android 6.0 运行时权限检查

`ContextCompat.CheckSelfPermission` （适用于 Android 支持库） 的方法用于检查是否已授予特定权限。 此方法将返回[ `Android.Content.PM.Permission` ](https://developer.xamarin.com/api/type/Android.Content.PM.Permission/)枚举具有两个值之一：

* **`Permission.Granted`** &ndash; 已授予指定的权限。
* **`Permission.Denied`** &ndash; 尚未授予指定的权限。

此代码片段示范了如何在活动中的照相机权限检查： 

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

它是最佳做法以告知用户为何权限是应用程序，以便可以进行明智的决策要授予的权限。 此示例将应用采用的照片和地域标记它们。 它是明确告知用户照相机权限是必需的但可能不会清除应用也需要该设备的位置的原因。 要弄清楚为何应显示一条消息，以帮助用户了解为什么说的位置权限是比较理想和照相机权限是必需。

`ActivityCompat.ShouldShowRequestPermissionRationale`方法用于确定是否应为用户显示基本原理。 此方法将返回`true`如果应显示给定的权限的基本原理。 此屏幕截图显示解释为什么应用程序需要知道设备的位置的应用程序会显示 Snackbar 的示例：

![位置的基本原理](permissions-images/07-rationale-snackbar.png) 

如果用户授予权限，`ActivityCompat.RequestPermissions(Activity activity, string[] permissions, int requestCode)`应调用方法。 此方法需要以下参数：

* **活动**&ndash;这是请求的权限，并且是通过 Android 结果的通知的活动。
* **权限**&ndash;正在请求权限的列表。
* **requestCode** &ndash;一个整数值，用于匹配对的权限请求的结果`RequestPermissions`调用。 此值应大于零。

此代码片段是讨论了两种方法的示例。 首先，进行检查以确定是否应显示权限基本原理。 如果要弄清楚为何将显示，则 Snackbar 显示与基本原理。 如果用户单击**确定**中 Snackbar，然后应用将请求的权限。 如果用户不接受基本原理，然后该应用程序应无法继续以请求权限。 如果未显示基本原理，该活动将请求的权限：

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

`RequestPermission` 可以调用，即使用户已授予的权限。 后续调用不是必需的但用户提供机会来确认 （或吊销） 权限。 当`RequestPermission`是名为，控件将转移到操作系统，将显示一个用户界面的接受权限：  

![Permssion 对话框](permissions-images/08-location-permission-dialog.png)

用户已完成后，Android 将返回结果的活动的回调方法，通过`OnRequestPermissionResult`。 此方法是接口的一部分`ActivityCompat.IOnRequestPermissionsResultCallback`活动必须实现的。 此接口具有单个方法`OnRequestPermissionsResult`，这将调用由 Android 通知的活动的用户的选择。 如果用户已授予的权限，该应用程序可以继续操作并使用受保护的资源。 举例说明如何实现`OnRequestPermissionResult`如下所示： 

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

本指南介绍如何添加和查看有在 Android 设备的权限。 权限旧的 Android 应用 （API 级别 < 23） 和新的 Android 应用程序之间的工作方式的差异 （API 级别 > 22）。 它介绍了如何在 Android 6.0 中执行运行时权限检查。


## <a name="related-links"></a>相关链接

- [正常的权限的列表](https://developer.android.com/guide/topics/permissions/normal-permissions.html)
- [运行时权限示例应用](https://github.com/xamarin/monodroid-samples/tree/master/android-m/RuntimePermissions)
- [在 Xamarin.Android 中的处理权限](https://github.com/xamarin/recipes/tree/master/Recipes/android/general/projects/add_permissions_to_android_manifest)
