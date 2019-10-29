---
title: 带指纹身份验证的入门
ms.prod: xamarin
ms.assetid: 7BACCB36-8E3E-4E5D-B8EF-56A639839FD2
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/17/2018
ms.openlocfilehash: 746a096f93036e63b29bc917826259f88426cead
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73020285"
---
# <a name="getting-started-with-fingerprint-authentication"></a>带指纹身份验证的入门

首先，让我们先介绍如何配置 Xamarin Android 项目，使应用程序能够使用指纹身份验证：

1. 更新**androidmanifest.xml**以声明指纹 api 所需的权限。
2. 获取对 `FingerprintManager`的引用。
3. 检查设备是否能够进行指纹扫描。

## <a name="requesting-permissions-in-the-application-manifest"></a>在应用程序清单中请求权限

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Android 应用程序必须请求清单中的 `USE_FINGERPRINT` 权限。 以下屏幕截图演示了如何在 Visual Studio 中将此权限添加到应用程序：

[![启用在 Android 清单屏幕中使用\_指纹](get-started-images/fingerprint-01-vs.png)](get-started-images/fingerprint-01-vs.png#lightbox) 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

Android 应用程序必须请求清单中的 `USE_FINGERPRINT` 权限。 以下屏幕截图显示了如何将此权限添加到 Visual Studio for Mac 中的应用程序：

[![在 Android 应用程序屏幕中启用 UseFingerprint](get-started-images/fingerprint-01-xs.png)](get-started-images/fingerprint-01-xs.png#lightbox) 

-----

## <a name="getting-an-instance-of-the-fingerprintmanager"></a>获取 FingerprintManager 的实例

接下来，应用程序必须获取 `FingerprintManager` 或 `FingerprintManagerCompat` 类的实例。 若要与较旧版本的 Android 兼容，Android 应用程序应使用 Android 支持 v4 NuGet 包中的兼容性 API。 以下代码片段演示如何从操作系统获取适当的对象： 

```csharp
// Using the Android Support Library v4
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);

// Using API level 23:
FingerprintManager fingerprintManager = context.GetSystemService(Context.FingerprintService) as FingerprintManager;
```  

在前面的代码片段中，`context` 是任何 Android `Android.Content.Context`。 通常，这是执行身份验证的活动。

## <a name="checking-for-eligibility"></a>检查资格

应用程序必须执行多次检查以确保可以使用指纹身份验证。 总共，应用程序使用五个条件来检查资格：  

**Api 级别 23** &ndash; 指纹 API 需要 API 级别23或更高版本。 `FingerprintManagerCompat` 类将为你包装 API 级别检查。 出于此原因，建议使用**Android 支持库 v4**和 `FingerprintManagerCompat`;这将考虑其中一项检查。

**硬件**&ndash; 第一次启动应用程序时，它应检查是否存在指纹扫描器：

```csharp
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);
if (!fingerprintManager.IsHardwareDetected)
{
    // Code omitted
}
```

**设备受到保护**&ndash; 用户必须使用屏幕锁定保护设备。 如果用户未使用屏幕锁定保护设备，并且安全对应用程序很重要，则应通知用户必须配置屏幕锁。 以下代码片段演示了如何检查此预 requiste：

```csharp
KeyguardManager keyguardManager = (KeyguardManager) GetSystemService(KeyguardService);
if (!keyguardManager.IsKeyguardSecure)
{
}
```

**注册的指纹**&ndash; 用户必须至少有一个向操作系统注册的指纹。 此权限检查应在每次身份验证尝试之前发生：

```csharp
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);
if (!fingerprintManager.HasEnrolledFingerprints)
{
    // Can't use fingerprint authentication - notify the user that they need to
    // enroll at least one fingerprint with the device.
}
```

应用程序 &ndash; 的**权限**必须先请求用户的权限，然后才能使用该应用程序。 对于 Android 5.0 和更低版本，用户会将权限授予安装应用的条件。 Android 6.0 引入了新的权限模型，可在运行时检查权限。 此代码片段举例说明了如何在 Android 6.0 上检查权限：

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

每次应用程序提供身份验证选项时，检查所有这些条件都将确保用户获得最佳用户体验。 更改或升级其设备或操作系统可能会影响指纹身份验证的可用性。 如果选择缓存任何这些检查的结果，请确保满足升级方案。

有关如何在 Android 6.0 中请求权限的详细信息，请参阅 Android 指南[在运行时请求权限](https://developer.android.com/training/permissions/requesting.html)。

## <a name="related-links"></a>相关链接

- [快捷](xref:Android.Content.Context)
- [KeyguardManager](xref:Android.App.KeyguardManager)
- [ContextCompat](https://developer.android.com/reference/android/support/v4/content/ContextCompat)
- [FingerprintManager](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html)
- [FingerprintManagerCompat](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)
- [在运行时请求权限](https://developer.android.com/training/permissions/requesting.html)
