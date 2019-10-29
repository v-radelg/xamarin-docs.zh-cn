---
title: 响应身份验证回调
ms.prod: xamarin
ms.assetid: 6533AFC9-1A1C-4897-A154-4D4ECFE27761
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/06/2017
ms.openlocfilehash: 8dc06740355bd95828e1a1bd8d9d15a2ef37e6b2
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027533"
---
# <a name="responding-to-authentication-callbacks"></a>响应身份验证回调

指纹扫描器在后台运行在其自己的线程上，完成后，它将通过调用 UI 线程上 `FingerprintManager.AuthenticationCallback` 的一种方法来报告扫描结果。 Android 应用程序必须提供其自己的处理程序，该处理程序可扩展此抽象类，实现以下所有方法：

- 出现无法恢复的错误时调用 **`OnAuthenticationError(int errorCode, ICharSequence errString)`** &ndash;。 除了可能再次尝试外，应用程序或用户不能执行其他操作来纠正这种情况。
- **`OnAuthenticationFailed()`** &ndash; 在检测到指纹但设备无法识别指纹时，将调用此方法。
- 当存在可恢复的错误时， **`OnAuthenticationHelp(int helpMsgId, ICharSequence helpString)`** &ndash;，例如手指通过扫描程序重击快。
- **`OnAuthenticationSucceeded(FingerprintManagerCompati.AuthenticationResult result)`** &ndash; 识别到指纹后会调用此。

如果调用 `Authenticate`时使用了 `CryptoObject`，则建议在 `OnAuthenticationSuccessful`中调用 `Cipher.DoFinal`。
如果密码被篡改或初始化不正确，则 `DoFinal` 会引发异常，这表明指纹扫描器的结果可能在应用程序外被篡改。

> [!NOTE]
> 建议保持回调类相对较轻量，并释放应用程序特定逻辑。 回调应充当 Android 应用程序和指纹扫描器的结果之间的 "流量 cop"。

## <a name="a-sample-authentication-callback-handler"></a>示例身份验证回调处理程序

下面的类是最小 `FingerprintManager.AuthenticationCallback` 实现的示例： 

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

`OnAuthenticationSucceeded` 检查是否在调用 `Authentication` 时 `FingerprintManager` 提供了 `Cipher`。 如果是这样，则对密码调用 `DoFinal` 方法。 这会关闭 `Cipher`，并将其还原到其原始状态。 如果密码有问题，`DoFinal` 将引发异常，并且身份验证尝试应视为已失败。

每个 `OnAuthenticationError` 和 `OnAuthenticationHelp` 回调都接收一个整数，指示问题的原因。 以下部分介绍每个可能的帮助或错误代码。 这两个回调服务于类似的目的 &ndash; 来通知应用程序指纹身份验证已失败。 它们之间的不同之处在于严重性。 `OnAuthenticationHelp` 是用户可恢复的错误，例如轻扫指纹的速度太快;`OnAuthenticationError` 出现严重错误，如指纹扫描器损坏。

请注意，当通过 `CancellationSignal.Cancel()` 消息取消指纹扫描时，将调用 `OnAuthenticationError`。 `errMsgId` 参数的值将为5（`FingerprintState.ErrorCanceled`）。 根据要求，`AuthenticationCallbacks` 的实现可能会以不同于其他错误的方式处理这种情况。 

如果指纹已成功扫描，但与向设备注册的任何指纹不匹配，则会调用 `OnAuthenticationFailed`。 

## <a name="help-codes-and-error-message-ids"></a>帮助代码和错误消息 Id 

有关错误代码和帮助代码的列表和说明，请参阅 FingerprintManager 类的[Android SDK 文档](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html#FINGERPRINT_ACQUIRED_GOOD)。 Xamarin 通过 `Android.Hardware.Fingerprints.FingerprintState` 枚举表示这些值：

- **`AcquiredGood`** &ndash; （值为0）获取的图像良好。

- **`AcquiredImagerDirty`** &ndash; （值3），因为在传感器上怀疑或检测到灰尘，所以指纹映像太干扰。 例如，在传感器上出现多个 `AcquiredInsufficient` 或检测到灰尘（粘滞像素、大量等）后，合理地返回此值是合理的。 返回此时，用户应采取措施清理传感器。

- **`AcquiredInsufficient`** &ndash; （值2）指纹映像由于检测到的条件（即晾干外观）或可能脏的传感器而太干扰，请参阅 `AcquiredImagerDirty`。

- 仅检测到部分指纹映像 **`AcquiredPartial`** &ndash; （值1）。 在注册期间，应通知用户需要发生哪些情况才能解决此问题，例如，&ldquo;在传感器上用力按压。&rdquo;

- **`AcquiredTooFast`** &ndash; （值5）指纹映像因快速动作而不完整。 大多数情况下适用于线性阵列传感器，如果在获取过程中移动了 finger，也会发生这种情况。 应要求用户将手指向后移动（线性），或将手指置于传感器的更长时间。

- **`AcquiredToSlow`** &ndash; （值4）无法读取指纹映像，因为缺少动作。 这对于需要轻扫运动的线性阵列传感器非常合适。

- **`ErrorCanceled`** &ndash; （值5）操作被取消，因为指纹传感器不可用。 例如，在切换用户、锁定设备或另一个挂起操作阻止或禁用此操作时，可能会发生这种情况。

- **`ErrorHwUnavailable`** &ndash; （值1）硬件不可用。 请稍后再试。

- **`ErrorLockout`** &ndash; （值7），操作被取消，因为由于尝试次数过多，API 被锁定。

- 为注册等操作返回的 **`ErrorNoSpace`** &ndash; （值4）错误状态;操作无法完成，因为没有足够的存储空间来完成该操作。

- **`ErrorTimeout`** &ndash; （值3）当当前请求运行时间太长时返回的错误状态。 这是为了防止程序无限期地等待指纹传感器。 超时是平台和特定于传感器的，但通常约为30秒。

- **`ErrorUnableToProcess`** &ndash; （值2）当传感器无法处理当前图像时返回错误状态。

## <a name="related-links"></a>相关链接

- [密码](https://docs.oracle.com/javase/7/docs/api/javax/crypto/Cipher.html)
- [Authenticationcallback 传递给](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html)
- [Authenticationcallback 传递给](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.AuthenticationCallback.html)
