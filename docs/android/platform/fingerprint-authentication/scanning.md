---
title: 扫描指纹
ms.prod: xamarin
ms.assetid: 1CDDC096-77E0-47B3-BE0B-8953E2DDACD3
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/23/2016
ms.openlocfilehash: 372fe4c7844448e7fb3cbc768f16feb3a5cc7791
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61023441"
---
# <a name="scanning-for-fingerprints"></a>扫描指纹

既然我们已了解如何准备 Xamarin.Android 应用程序使用指纹身份验证，让我们返回到`FingerprintManager.Authenticate`方法，并讨论其位置在 Android 6.0 指纹身份验证。 指纹身份验证的工作流的快速概述此列表所述：

1. 调用`FingerprintManager.Authenticate`，并传入`CryptoObject`和一个`FingerprintManager.AuthenticationCallback`实例。 `CryptoObject`用于确保指纹身份验证结果被未篡改。 
2. 子类[FingerprintManager.AuthenticationCallback](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html)类。 此类的实例将提供给`FingerprintManager`时指纹身份验证入门。 指纹扫描程序完成后，它将调用其中一个此类上的回调方法。
3. 编写代码以更新 UI，以让用户知道设备指纹扫描程序启动并正在等待用户交互。 
4. Android 指纹扫描程序完成操作后，将通过在调用的方法向应用程序返回结果`FingerprintManager.AuthenticationCallback`在上一步中提供的实例。
5. 应用程序将通知指纹身份验证结果的用户，并将结果作为相应做出反应。 

下面的代码段是将启动扫描指纹的活动中方法的示例：

```csharp
protected void FingerPrintAuthenticationExample()
{
    const int flags = 0; /* always zero (0) */

    // CryptoObjectHelper is described in the previous section.
    CryptoObjectHelper cryptoHelper = new CryptoObjectHelper();    
    
    // cancellationSignal can be used to manually stop the fingerprint scanner. 
    cancellationSignal = new Android.Support.V4.OS.CancellationSignal();
    
    FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(this);
    
    // AuthenticationCallback is a base class that will be covered later on in this guide.
    FingerprintManagerCompat.AuthenticationCallback authenticationCallback = new MyAuthCallbackSample(this);

    // Start the fingerprint scanner.
    fingerprintManager.Authenticate(cryptoHelper.BuildCryptoObject(), flags, cancellationSignal, authenticationCallback, null);
}
```

让我们来讨论这些参数中的每个`Authenticate`中更多详细信息的方法：

* 第一个参数是_crypto_对象指纹扫描程序将用来帮助进行身份验证的指纹扫描结果。 此对象可以为`null`，盲目地信任不出现任何应用程序必须在这种情况下已篡改指纹结果。 建议`CryptoObject`实例化并提供给`FingerprintManager`而不是 null。 [创建 CryptObject](~/android/platform/fingerprint-authentication/creating-a-cryptoobject.md)将详细说明如何实例化`CryptoObject`基于`Cipher`。
* 第二个参数始终为零。 Android 文档表明这是组标志，最有可能保留供将来使用。 
* 第三个参数，`cancellationSignal`是用于关闭指纹扫描程序，并取消当前请求的对象。 这是[Android CancellationSignal](https://developer.android.com/reference/android/os/CancellationSignal.html)，并不是从.NET framework 类型。
* 第四个参数是必需的是一个类，该子类`AuthenticationCallback`抽象类。 将调用此类的方法将信号发送到客户端时`FingerprintManager`已完成，结果将是什么。 如有很多关于实现需要了解`AuthenticationCallback`，将在介绍[它是自己的部分](~/android/platform/fingerprint-authentication/fingerprint-authentication-callbacks.md)。
* 第五个参数是可选的`Handler`实例。 如果`Handler`提供对象，则`FingerprintManager`将使用`Looper`从该对象处理从指纹硬件消息时。 通常情况下，一个不需要提供`Handler`，将使用 FingerprintManager`Looper`从应用程序。

## <a name="cancelling-a-fingerprint-scan"></a>正在取消指纹扫描

可能有必要的用户 （或应用程序） 来取消指纹扫描后已启动。 在这种情况下，将调用[ `IsCancelled` ](https://developer.android.com/reference/android/os/CancellationSignal.html#isCanceled())方法[ `CancellationSignal` ](https://developer.android.com/reference/android/os/CancellationSignal.html)提供给`FingerprintManager.Authenticate`时调用它以启动指纹扫描。

现在，我们看到`Authenticate`方法，让我们看一些更多详细信息中的更重要的参数。 首先，我们将介绍[响应身份验证回调](~/android/platform/fingerprint-authentication/fingerprint-authentication-callbacks.md)，其中将讨论如何创建子类[FingerprintManager.AuthenticationCallback](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html)，启用对做出反应的 Android 应用程序提供指纹扫描程序的结果。




## <a name="related-links"></a>相关链接

- [CancellationSignal](https://developer.android.com/reference/android/os/CancellationSignal.html)
- [FingerprintManager.AuthenticationCallback](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html)
- [FingerprintManager.CryptoObject](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.CryptoObject.html)
- [FingerprintManagerCompat.CryptoObject](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.CryptoObject.html)
- [FingerprintManager](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html)
- [FingerprintManagerCompat](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)
