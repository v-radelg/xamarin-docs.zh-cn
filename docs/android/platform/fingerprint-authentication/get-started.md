---
title: 开始使用指纹身份验证
ms.prod: xamarin
ms.assetid: 7BACCB36-8E3E-4E5D-B8EF-56A639839FD2
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/17/2018
ms.openlocfilehash: 3082dfcd6d0ffbc6404a89a10819e60b57b9c61c
ms.sourcegitcommit: 650458de1d362cd7de174cacef7838f0e74426f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/15/2019
ms.locfileid: "58070704"
---
# <a name="getting-started-with-fingerprint-authentication"></a>开始使用指纹身份验证

若要开始，让我们先了解如何配置 Xamarin.Android 项目，以便应用程序都可以使用指纹身份验证：

1. 更新**AndroidManifest.xml**声明指纹 Api 需要的权限。
2. 获取对引用`FingerprintManager`。
3. 检查设备能够指纹扫描。

## <a name="requesting-permissions-in-the-application-manifest"></a>在应用程序中的请求权限清单

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Android 应用程序必须请求`USE_FINGERPRINT`清单中的权限。 下面的屏幕截图显示了如何将此权限添加到 Visual Studio 中的应用程序：

[![启用使用\_指纹在 Android 清单屏幕](get-started-images/fingerprint-01-vs.png)](get-started-images/fingerprint-01-vs.png#lightbox) 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

Android 应用程序必须请求`USE_FINGERPRINT`清单中的权限。 下面的屏幕截图显示了如何将此权限添加到 Mac 在 Visual Studio 中的应用程序：

[![在 Android 应用程序屏幕中启用 UseFingerprint](get-started-images/fingerprint-01-xs.png)](get-started-images/fingerprint-01-xs.png#lightbox) 

-----

## <a name="getting-an-instance-of-the-fingerprintmanager"></a>获取 FingerprintManager 的实例

接下来，应用程序必须获取的实例`FingerprintManager`或`FingerprintManagerCompat`类。 若要使用较旧版本的 Android 兼容，Android 应用程序应使用的兼容性 API 的 Android 支持 v4 NuGet 包中找到。 以下代码片段演示了如何从操作系统获取相应的对象： 

```csharp
// Using the Android Support Library v4
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);

// Using API level 23:
FingerprintManager fingerprintManager = context.GetSystemService(Context.FingerprintService) as FingerprintManager;
```  

在上一片段中，`context`是任何 Android `Android.Content.Context`。 这通常是执行身份验证的活动。

## <a name="checking-for-eligibility"></a>检查合格性

应用程序必须执行多个检查以确保其可以使用指纹身份验证。 总共有五个应用程序使用检查有资格的条件：  

**API 级别 23** &ndash;指纹 Api 需要 API 级别 23 或更高版本。 `FingerprintManagerCompat`类将包装对你的 API 级别检查。 出于此原因，它是建议用于**Android 支持库 v4**和`FingerprintManagerCompat`; 这将为这些检查的一个帐户。

**硬件**&ndash;时首次启动应用程序，应检查是否存在指纹扫描程序：

```csharp
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);
if (!fingerprintManager.IsHardwareDetected)
{
    // Code omitted
}
```

**设备保护**&ndash;的用户必须具有与屏幕锁保护的设备。 如果用户具有不安全的屏幕锁定的设备和安全性是对应用程序，然后用户应收到通知，必须配置屏幕锁定。 下面的代码段演示了如何检查此先决条件：

```csharp
KeyguardManager keyguardManager = (KeyguardManager) GetSystemService(KeyguardService);
if (!keyguardManager.IsKeyguardSecure)
{
}
```

**注册指纹**&ndash;的用户必须具有至少一个操作系统系统中注册的指纹。 在每次身份验证尝试之前应发生这种权限检查：

```csharp
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);
if (!fingerprintManager.HasEnrolledFingerprints)
{
    // Can't use fingerprint authentication - notify the user that they need to
    // enroll at least one fingerprint with the device.
}
```

**权限**&ndash;应用程序必须使用应用程序之前从用户中请求权限。 Android 5.0 及更低，用户授予的权限，以安装该应用程序的状态。 Android 6.0 引入了新的权限模型，用于检查在运行时权限。 此代码片段示范了如何在 Android 6.0 上的权限检查：

```csharp
// The context is typically a reference to the current activity.
Android.Content.PM.Permission permissionResult = ContextCompat.CheckSelfPermission(context, Manifest.Permission.UseFingerprint);
if (permissionResult == Android.Content.PM.Permission.Granted)
{
    // Permission granted - go ahead and start the fingerprint scanner.
}
else
{
    // No permission. Go and ask for permissions and don't start the scanner. See
    // https://developer.android.com/training/permissions/requesting.html
}
```

检查所有这些条件，每次应用程序提供了身份验证选项可确保用户获取最佳用户体验。 更改或对其设备或操作系统升级可能会影响指纹身份验证的可用性。 如果您选择要缓存的任何这些检查的结果，请确保满足客户的升级方案。

有关如何在 Android 6.0 中的请求权限的详细信息，请参阅 Android 指南[在运行时请求权限](https://developer.android.com/training/permissions/requesting.html)。

## <a name="related-links"></a>相关链接

- [上下文](https://developer.xamarin.com/api/type/Android.Content.Context/)
- [ContextCompat](https://developer.xamarin.com/api/type/Android.Support.V4.Content.ContextCompat/)
- [KeyguardManager](https://developer.xamarin.com/api/type/Android.App.KeyguardManager/)
- [FingerprintManager](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html)
- [FingerprintManagerCompat](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)
- [在运行时的请求权限](https://developer.android.com/training/permissions/requesting.html)
