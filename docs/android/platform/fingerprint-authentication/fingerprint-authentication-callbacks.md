---
title: 响应身份验证回调
ms.prod: xamarin
ms.assetid: 6533AFC9-1A1C-4897-A154-4D4ECFE27761
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/06/2017
ms.openlocfilehash: c720a30a59eea8f1ed74033da8d1c045a1fb9109
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61023478"
---
# <a name="responding-to-authentication-callbacks"></a>响应身份验证回调

指纹扫描程序在后台运行在它自己的线程上和完成后，它将通过调用的一种方法报告的扫描结果`FingerprintManager.AuthenticationCallback`UI 线程上。 Android 应用程序必须提供自己的处理程序扩展了此实现以下所有方法的抽象类：

* **`OnAuthenticationError(int errorCode, ICharSequence errString)`** &ndash; 不可恢复的错误时调用。 没有任何应用程序或用户可以如何更正这种情况只可能是重试。
* **`OnAuthenticationFailed()`** &ndash; 已检测到但无法识别该设备的指纹时，将调用此方法。
* **`OnAuthenticationHelp(int helpMsgId, ICharSequence helpString)`** &ndash; 可恢复的错误，如通过扫描程序快速实现到手指时调用。
* **`OnAuthenticationSucceeded(FingerprintManagerCompati.AuthenticationResult result)`** &ndash; 这称为时已识别指纹。

如果`CryptoObject`调用时使用`Authenticate`，建议调用`Cipher.DoFinal`中`OnAuthenticationSuccessful`。
`DoFinal` 如果密码已被篡改或未正确初始化，将引发异常，这表明，指纹扫描程序的结果可能已被篡改应用程序之外。


> [!NOTE]
> 建议保留回调类相对较轻量和免费的应用程序特定逻辑。 回调应充当"流量 cop"Android 应用程序和结果之间指纹扫描程序。

## <a name="a-sample-authentication-callback-handler"></a>示例身份验证回调处理程序

下面的类是举例说明最小`FingerprintManager.AuthenticationCallback`实现： 

```csharp
class MyAuthCallbackSample : FingerprintManagerCompat.AuthenticationCallback
{
    // Can be any byte array, keep unique to application.
    static readonly byte[] SECRET_BYTES = {1, 2, 3, 4, 5, 6, 7, 8, 9};
    // The TAG can be any string, this one is for demonstration.
    static readonly string TAG = "X:" + typeof (SimpleAuthCallbacks).Name;

    public MyAuthCallbackSample()
    {
    }

    public override void OnAuthenticationSucceeded(FingerprintManagerCompat.AuthenticationResult result)
    {
        if (result.CryptoObject.Cipher != null) 
        {
            try
            {
                // Calling DoFinal on the Cipher ensures that the encryption worked.
                byte[] doFinalResult = result.CryptoObject.Cipher.DoFinal(SECRET_BYTES);
    
                // No errors occurred, trust the results.              
            }
            catch (BadPaddingException bpe)
            {
                // Can't really trust the results.
                Log.Error(TAG, "Failed to encrypt the data with the generated key." + bpe);
            }
            catch (IllegalBlockSizeException ibse)
            {
                // Can't really trust the results.
                Log.Error(TAG, "Failed to encrypt the data with the generated key." + ibse);
            }
        }
        else
        {
            // No cipher used, assume that everything went well and trust the results.
        }
    }

    public override void OnAuthenticationError(int errMsgId, ICharSequence errString)
    {
        // Report the error to the user. Note that if the user canceled the scan,
        // this method will be called and the errMsgId will be FingerprintState.ErrorCanceled.
    }

    public override void OnAuthenticationFailed()
    {
        // Tell the user that the fingerprint was not recognized.
    }

    public override void OnAuthenticationHelp(int helpMsgId, ICharSequence helpString)
    {
        // Notify the user that the scan failed and display the provided hint.
    }
}
```

`OnAuthenticationSucceeded` 检查以查看是否`Cipher`提供给`FingerprintManager`时`Authentication`调用时。 如果是这样，`DoFinal`密码上调用方法。 这会关闭`Cipher`，将其还原到其原始状态。 如果出现问题具有密码，然后`DoFinal`将引发异常，并且应考虑身份验证尝试已失败。

`OnAuthenticationError`和`OnAuthenticationHelp`回调每个接收一个整数，指示出现了什么问题。 以下部分介绍每个可能的帮助或错误代码。 两个回调具有相似的用途&ndash;以通知应用程序的指纹身份验证失败。 它们之间的区别是在严重级别。 `OnAuthenticationHelp` 用户可恢复错误，如轻扫太快; 指纹`OnAuthenticationError`是更严重错误，例如损坏的指纹扫描程序。

请注意，`OnAuthenticationError`通过取消指纹扫描时，将调用`CancellationSignal.Cancel()`消息。 `errMsgId`参数将具有值为 5 (`FingerprintState.ErrorCanceled`)。 根据要求的实现`AuthenticationCallbacks`可能不同于其他错误处理这种情况。 

`OnAuthenticationFailed` 已成功扫描指纹，但与任何已注册设备的指纹不匹配时调用。 

## <a name="help-codes-and-error-message-ids"></a>帮助代码和错误消息 Id 

中可能找到的列表和说明的错误代码和帮助代码[Android SDK 文档](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html#FINGERPRINT_ACQUIRED_GOOD)FingerprintManager 类。 Xamarin.Android 表示这些值用于`Android.Hardware.Fingerprints.FingerprintState`枚举：


-   **`AcquiredGood`** &ndash; （值 0）获取图像的很好。


-   **`AcquiredImagerDirty`** &ndash; （值 3）指纹图像的传感器上的可疑或检测到灰尘由于过多的干扰。 例如，它是合理地在多个后返回此`AcquiredInsufficient`或实际检测的传感器 （像素滞留、 负责等） 上的灰尘。 用户需要采取措施来清理传感器时，返回此项。


-   **`AcquiredInsufficient`** &ndash; （值 2）指纹图像的过多的干扰处理由于检测到的条件 （即 dry 外观） 或可能是脏的传感器 (请参阅`AcquiredImagerDirty`。



-   **`AcquiredPartial`** &ndash; （值 1）检测到仅部分指纹映像。 注册期间，用户应通知上需要发生若要解决此问题，例如，&ldquo;用力按传感器。&rdquo;



-   **`AcquiredTooFast`** &ndash; （值为 5）指纹映像是由于快速动作不完整。 尽管主要适用于线性数组传感器，还时会发生此情况期间获取移动手指。 应会要求用户移动手指速度较慢 （线性） 或将手指保留更长的传感器上。




-   **`AcquiredToSlow`** &ndash; （值 4）指纹图像会由于缺乏动作不可读。 这是最适合于需要进行轻扫移动的线性数组传感器。



-   **`ErrorCanceled`** &ndash; （值为 5）该操作已取消，因为指纹传感器是不可用。 例如，这可能会发生时切换用户、 设备锁定状态，或另一个挂起操作会阻止或禁用它。



-   **`ErrorHwUnavailable`** &ndash; （值 1）硬件不可用。 请稍后再试。




-   **`ErrorLockout`** &ndash; （值 7）该操作已取消，因为 API 因尝试次数过多而被锁定。




-   **`ErrorNoSpace`** &ndash; （值 4）为操作，例如注册; 返回的错误状态无法完成该操作，因为没有要完成该操作的剩余足够的存储。



-   **`ErrorTimeout`** &ndash; （值 3）当前请求已运行太长时返回的错误状态。 这是为了防止程序无限期地等待指纹传感器。 超时是平台和特定于传感器的但通常大约 30 秒。



-   **`ErrorUnableToProcess`** &ndash; （值 2）传感器无法处理当前映像时，返回的错误状态。



## <a name="related-links"></a>相关链接

- [Cipher](https://docs.oracle.com/javase/7/docs/api/javax/crypto/Cipher.html)
- [AuthenticationCallback](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html)
- [AuthenticationCallback](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.AuthenticationCallback.html)
