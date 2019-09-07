---
title: 正在扫描指纹
ms.prod: xamarin
ms.assetid: 1CDDC096-77E0-47B3-BE0B-8953E2DDACD3
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/23/2016
ms.openlocfilehash: 4ead912b55790caf3e2e1f22e149f5682e6bb697
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70761217"
---
# <a name="scanning-for-fingerprints"></a>正在扫描指纹

现在，我们已了解如何准备 Xamarin Android 应用程序以使用指纹身份验证，接下来让我们返回到`FingerprintManager.Authenticate`方法，并讨论其在 Android 6.0 指纹身份验证中的位置。 此列表介绍了指纹身份验证工作流的快速概述：

1. 调用`FingerprintManager.Authenticate`， `CryptoObject`同时传递和`FingerprintManager.AuthenticationCallback`实例。 `CryptoObject`用于确保指纹身份验证结果未被篡改。 
2. 将[FingerprintManager. authenticationcallback 传递给](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html)类的子类。 指纹身份验证开始时， `FingerprintManager`将提供此类的实例。 指纹扫描器完成后，它将调用此类的一个回调方法。
3. 编写代码以更新 UI，以让用户知道设备已启动指纹扫描器并等待用户交互。 
4. 指纹扫描器完成后，Android 会通过对上一步中提供的`FingerprintManager.AuthenticationCallback`实例调用方法，将结果返回到应用程序。
5. 应用程序将向用户通知指纹身份验证结果，并根据需要对结果做出反应。 

以下代码片段是活动中要开始扫描指纹的方法示例：

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

让我们更详细地讨论`Authenticate`方法中的每个参数：

- 第一个参数是一个_加密_对象，指纹扫描器将使用该对象来帮助对指纹扫描的结果进行身份验证。 此对象可能为`null`，在这种情况下，应用程序必须盲目地信任不会篡改指纹结果的内容。 建议`CryptoObject`将实例化并提供`FingerprintManager`给而不是 null。 [创建 CryptObject](~/android/platform/fingerprint-authentication/creating-a-cryptoobject.md)将详细说明如何`CryptoObject`基于`Cipher`创建实例化。
- 第二个参数始终为零。 Android 文档将此标识为一组标志，最有可能保留供将来使用。 
- 第三个参数`cancellationSignal`是用于关闭指纹扫描器并取消当前请求的对象。 这是[Android CancellationSignal](https://developer.android.com/reference/android/os/CancellationSignal.html)，而不是 .net framework 中的类型。
- 第四个参数是必需的，是子类`AuthenticationCallback`抽象类的类。 在完成后，将调用此类上的`FingerprintManager`方法向客户端发出信号，并返回结果。 由于实现`AuthenticationCallback`需要了解很多知识，它将在[其自己的部分](~/android/platform/fingerprint-authentication/fingerprint-authentication-callbacks.md)中介绍。
- 第五个参数是一个`Handler`可选实例。 如果提供`Handler`了一个对象`FingerprintManager` ，则在处理来自`Looper`指纹硬件的消息时，将使用该对象的。 通常情况下，不需要提供`Handler`，FingerprintManager 将`Looper`从应用程序使用。

## <a name="cancelling-a-fingerprint-scan"></a>取消指纹扫描

用户（或应用程序）可能需要在启动指纹扫描后将其取消。 在这种情况下， [`IsCancelled`](https://developer.android.com/reference/android/os/CancellationSignal.html#isCanceled())在调用以[`CancellationSignal`](https://developer.android.com/reference/android/os/CancellationSignal.html)启动指纹扫描时`FingerprintManager.Authenticate`提供给的上调用方法。

现在，我们已经了解了`Authenticate`方法，让我们更详细地研究一些更重要的参数。 首先，我们将介绍如何[响应身份验证回调](~/android/platform/fingerprint-authentication/fingerprint-authentication-callbacks.md)，该回调将讨论如何为[FingerprintManager. authenticationcallback 传递给](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html)划分子类，使 Android 应用程序能够对指纹扫描器提供的结果做出反应。

## <a name="related-links"></a>相关链接

- [CancellationSignal](https://developer.android.com/reference/android/os/CancellationSignal.html)
- [FingerprintManager.AuthenticationCallback](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html)
- [FingerprintManager.CryptoObject](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.CryptoObject.html)
- [FingerprintManagerCompat.CryptoObject](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.CryptoObject.html)
- [FingerprintManager](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html)
- [FingerprintManagerCompat](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)
