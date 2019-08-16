---
title: 响应身份验证回调
ms.prod: xamarin
ms.assetid: 6533AFC9-1A1C-4897-A154-4D4ECFE27761
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/06/2017
ms.openlocfilehash: cb4933695d34a0805be4139c7b345f7a70f33613
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69524332"
---
# <a name="responding-to-authentication-callbacks"></a>响应身份验证回调

指纹扫描器在后台运行在其自己的线程上, 完成后, 它将通过调用 UI 线程上的的`FingerprintManager.AuthenticationCallback`一种方法来报告扫描结果。 Android 应用程序必须提供其自己的处理程序, 该处理程序可扩展此抽象类, 实现以下所有方法:

* **`OnAuthenticationError(int errorCode, ICharSequence errString)`** &ndash;当出现无法恢复的错误时调用。 除了可能再次尝试外, 应用程序或用户不能执行其他操作来纠正这种情况。
* **`OnAuthenticationFailed()`** &ndash;检测到指纹但设备无法识别指纹时, 会调用此方法。
* **`OnAuthenticationHelp(int helpMsgId, ICharSequence helpString)`** &ndash;当存在可恢复的错误时调用, 如手指通过扫描程序重击。
* **`OnAuthenticationSucceeded(FingerprintManagerCompati.AuthenticationResult result)`** &ndash;识别指纹后, 会调用此。

如果在`CryptoObject`调用`Authenticate`时使用了, 则建议在中`OnAuthenticationSuccessful`调用`Cipher.DoFinal` 。
`DoFinal`如果密码被篡改或初始化不正确, 则会引发异常, 指示指纹扫描器的结果可能在应用程序外被篡改。


> [!NOTE]
> 建议保持回调类相对较轻量, 并释放应用程序特定逻辑。 回调应充当 Android 应用程序和指纹扫描器的结果之间的 "流量 cop"。

## <a name="a-sample-authentication-callback-handler"></a>示例身份验证回调处理程序

下面的类是一个最小`FingerprintManager.AuthenticationCallback`实现的示例: 

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

`OnAuthenticationSucceeded`检查是否在调用`FingerprintManager`时`Authentication`向提供。 `Cipher` 如果是这样, `DoFinal`则对密码调用方法。 这会关闭`Cipher`, 并将其还原到其原始状态。 如果密码有问题, 则`DoFinal`会引发异常, 并且身份验证尝试应视为已失败。

`OnAuthenticationError` 和`OnAuthenticationHelp`回调分别接收指示问题的整数。 以下部分介绍每个可能的帮助或错误代码。 这两个回调的作用&ndash;类似, 通知应用程序指纹身份验证已失败。 它们之间的不同之处在于严重性。 `OnAuthenticationHelp`用户可恢复的错误, 例如轻扫指纹的速度太快;`OnAuthenticationError`出现严重错误, 如指纹扫描器损坏。

请注意`OnAuthenticationError` , 当`CancellationSignal.Cancel()`通过消息取消指纹扫描时, 将调用。 参数的值将为 5 (`FingerprintState.ErrorCanceled`)。 `errMsgId` 根据要求, 的`AuthenticationCallbacks`实现可能会不同于其他错误来处理这种情况。 

`OnAuthenticationFailed`如果指纹已成功扫描, 但与向设备注册的任何指纹不匹配, 则会调用。 

## <a name="help-codes-and-error-message-ids"></a>帮助代码和错误消息 Id 

有关错误代码和帮助代码的列表和说明, 请参阅 FingerprintManager 类的[Android SDK 文档](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html#FINGERPRINT_ACQUIRED_GOOD)。 Xamarin 通过`Android.Hardware.Fingerprints.FingerprintState`枚举表示这些值:


- **`AcquiredGood`** &ndash; (值为 0) 获取的图像良好。


- **`AcquiredImagerDirty`** &ndash; (值 3) 在传感器上怀疑或检测到灰尘时, 指纹映像太干扰。 例如, 在传感器上检测到多个`AcquiredInsufficient`或实际检测到灰尘 (粘滞像素、大量等) 后, 合理地将其返回。 返回此时, 用户应采取措施清理传感器。


- **`AcquiredInsufficient`** (值 2) 由于检测到的条件 (即晾干外观) 或可能的脏传感器 (请参阅`AcquiredImagerDirty`), 指纹映像太干扰。 &ndash;



- **`AcquiredPartial`** &ndash; (值 1) 仅检测到部分指纹映像。 在注册期间, 应通知用户要解决此问题所需执行的操作, 例如, &ldquo;用力按传感器。&rdquo;



- **`AcquiredTooFast`** &ndash; (值 5) 由于快速动作, 指纹映像不完整。 大多数情况下适用于线性阵列传感器, 如果在获取过程中移动了 finger, 也会发生这种情况。 应要求用户将手指向后移动 (线性), 或将手指置于传感器的更长时间。




- **`AcquiredToSlow`** &ndash; (值 4) 由于缺少动作, 无法读取指纹映像。 这对于需要轻扫运动的线性阵列传感器非常合适。



- **`ErrorCanceled`** &ndash; (值 5) 操作被取消, 因为指纹传感器不可用。 例如, 在切换用户、锁定设备或另一个挂起操作阻止或禁用此操作时, 可能会发生这种情况。



- **`ErrorHwUnavailable`** &ndash; (值 1) 硬件不可用。 请稍后再试。




- **`ErrorLockout`** &ndash; (值 7) 该操作已被取消, 因为由于尝试次数过多, API 被锁定。




- **`ErrorNoSpace`** &ndash; (值 4) 为注册操作返回的错误状态; 该操作无法完成, 因为没有足够的存储空间来完成该操作。



- **`ErrorTimeout`** &ndash; (值 3) 当前请求运行时间太长时返回的错误状态。 这是为了防止程序无限期地等待指纹传感器。 超时是平台和特定于传感器的, 但通常约为30秒。



- **`ErrorUnableToProcess`** &ndash; (值 2) 传感器无法处理当前图像时返回的错误状态。



## <a name="related-links"></a>相关链接

- [密码](https://docs.oracle.com/javase/7/docs/api/javax/crypto/Cipher.html)
- [AuthenticationCallback](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html)
- [AuthenticationCallback](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.AuthenticationCallback.html)
