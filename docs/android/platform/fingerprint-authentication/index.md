---
title: 指纹身份验证
description: 本指南讨论如何向 Xamarin Android 应用程序添加在 Android 6.0 中引入的指纹身份验证。
ms.prod: xamarin
ms.assetid: 6742D874-4988-4516-A946-D5C714B20A10
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 7917f0cc03a4079d6b2adaba37fdc0332f8626c8
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68510610"
---
# <a name="fingerprint-authentication"></a>指纹身份验证

_本指南讨论如何向 Xamarin Android 应用程序添加在 Android 6.0 中引入的指纹身份验证。_


## <a name="fingerprint-authentication-overview"></a>指纹身份验证概述

Android 设备上的指纹扫描器到达为应用程序提供了用户身份验证的传统用户名/密码方法的替代方法。 使用指纹对用户进行身份验证, 使应用程序能够结合使用比用户名和密码更少的安全性。

FingerprintManager Api 使用指纹扫描器来定位设备, 并运行 API 级别 23 (Android 6.0) 或更高版本。 Api 位于`Android.Hardware.Fingerprints`命名空间中。 Android 支持库 v4 提供适用于较旧版本 Android 的指纹 Api 版本。 可在`Android.Support.v4.Hardware.Fingerprint`命名空间中找到兼容性 api, 这些 api 通过[Xamarin NuGet 包](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)分发。

[FingerprintManager](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html) (及其支持库对应的[FingerprintManagerCompat](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)) 是使用指纹扫描硬件的主要类。 此类是围绕系统级服务的 Android SDK 包装器, 用于管理与硬件本身的交互。 它负责启动指纹扫描器并响应扫描程序的反馈。 此类的接口非常简单, 只包含三个成员:

* **`Authenticate`** &ndash;此方法将初始化硬件扫描程序并在后台启动该服务, 并等待用户扫描其指纹。
* **`EnrolledFingerprints`** 如果用户已向设备注册一个或多个指纹, 此属性将返回`true`。 &ndash;
* **`HardwareDetected`** &ndash;此属性用于确定设备是否支持指纹扫描。

Android 应用程序使用方法来启动指纹扫描器。`FingerprintManager.Authenticate` 以下代码片段举例说明了如何使用支持库兼容性 Api 调用它:

```csharp
// context is any Android.Content.Context instance, typically the Activity 
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);
fingerprintManager.Authenticate(FingerprintManager.CryptoObject crypto,
                                int flags,
                                CancellationSignal cancel,
                                FingerprintManagerCompat.AuthenticationCallback callback,
                                Handler handler
                               );
```

本指南将介绍如何使用`FingerprintManager` api 来增强使用指纹身份验证的 Android 应用程序。 它将介绍如何实例化和创建`CryptoObject`来帮助保护指纹扫描器中的结果。 我们将检查应用程序应该`FingerprintManager.AuthenticationCallback`如何从指纹扫描器对反馈进行分类并做出响应。 最后, 我们将了解如何在 Android 设备或模拟器上注册指纹, 以及如何使用**adb**来模拟指纹扫描。

## <a name="requirements"></a>要求

指纹身份验证需要 Android 6.0 (API 级别 23) 或更高版本, 以及带有指纹扫描器的设备。 

对于要进行身份验证的每个用户, 指纹必须已注册到设备。 这涉及设置使用密码、PIN、刷模式或面部识别的屏幕锁。 可以在 Android Emulator 中模拟某些指纹身份验证功能。  有关这两个主题的详细信息, 请参阅[注册指纹](enrolling-fingerprint.md)部分。 






## <a name="related-links"></a>相关链接

- [指纹指南示例应用](https://developer.xamarin.com/samples/monodroid/FingerprintGuide/)
- [指纹对话示例](https://developer.xamarin.com/samples/monodroid/android-m/FingerprintDialog/)
- [在运行时请求权限](https://developer.android.com/training/permissions/requesting.html)
- [android.hardware.fingerprint](https://developer.android.com/reference/android/hardware/fingerprint/package-summary.html)
- [android.support.v4.hardware.fingerprint](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/package-summary.html)
- [Android.Content.Context](xref:Android.Content.Context)
- [指纹和付款 API (视频)](https://youtu.be/VOn7VrTRlA4)
