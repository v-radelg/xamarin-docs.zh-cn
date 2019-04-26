---
title: 创建 CryptoObject
ms.prod: xamarin
ms.assetid: 4D159B80-FFF4-4136-A7F0-F8A5C6B86838
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 1305a8a1f39d34b5e91e478a769750911afb2b3e
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60953183"
---
# <a name="creating-a-cryptoobject"></a>创建 CryptoObject

指纹身份验证结果的完整性非常重要的应用程序&ndash;它是在应用程序如何知道用户的标识。 它是理论上的第三方恶意软件以截获和篡改指纹扫描程序返回的结果。 本部分将讨论一种方法来保留的指纹结果的有效性。 

`FingerprintManager.CryptoObject`是 Java 加密 Api 的包装，并由`FingerprintManager`以保护身份验证请求的完整性。 通常情况下，`Javax.Crypto.Cipher`对象是用于加密指纹扫描程序的结果的机制。 `Cipher`对象本身会使用通过使用 Android 密钥存储 Api 的应用程序创建的密钥。

若要了解所有这些类是如何协同工作，让我们首先看一下下面的代码演示了如何创建`CryptoObject`，然后更详细地解释：

```csharp
public class CryptoObjectHelper
{
    // This can be key name you want. Should be unique for the app.
    static readonly string KEY_NAME = "com.xamarin.android.sample.fingerprint_authentication_key";

    // We always use this keystore on Android.
    static readonly string KEYSTORE_NAME = "AndroidKeyStore";

    // Should be no need to change these values.
    static readonly string KEY_ALGORITHM = KeyProperties.KeyAlgorithmAes;
    static readonly string BLOCK_MODE = KeyProperties.BlockModeCbc;
    static readonly string ENCRYPTION_PADDING = KeyProperties.EncryptionPaddingPkcs7;
    static readonly string TRANSFORMATION = KEY_ALGORITHM + "/" +
                                            BLOCK_MODE + "/" +
                                            ENCRYPTION_PADDING;
    readonly KeyStore _keystore;

    public CryptoObjectHelper()
    {
        _keystore = KeyStore.GetInstance(KEYSTORE_NAME);
        _keystore.Load(null);
    }

    public FingerprintManagerCompat.CryptoObject BuildCryptoObject()
    {
        Cipher cipher = CreateCipher();
        return new FingerprintManagerCompat.CryptoObject(cipher);
    }

    Cipher CreateCipher(bool retry = true)
    {
        IKey key = GetKey();
        Cipher cipher = Cipher.GetInstance(TRANSFORMATION);
        try
        {
            cipher.Init(CipherMode.EncryptMode | CipherMode.DecryptMode, key);
        } catch(KeyPermanentlyInvalidatedException e)
        {
            _keystore.DeleteEntry(KEY_NAME);
            if(retry)
            {
                CreateCipher(false);
            } else
            {
                throw new Exception("Could not create the cipher for fingerprint authentication.", e);
            }
        }
        return cipher;
    }

    IKey GetKey()
    {
        IKey secretKey;
        if(!_keystore.IsKeyEntry(KEY_NAME))
        {
            CreateKey();
        }

        secretKey = _keystore.GetKey(KEY_NAME, null);
        return secretKey;
    }

    void CreateKey()
    {
        KeyGenerator keyGen = KeyGenerator.GetInstance(KeyProperties.KeyAlgorithmAes, KEYSTORE_NAME);
        KeyGenParameterSpec keyGenSpec =
            new KeyGenParameterSpec.Builder(KEY_NAME, KeyStorePurpose.Encrypt | KeyStorePurpose.Decrypt)
                .SetBlockModes(BLOCK_MODE)
                .SetEncryptionPaddings(ENCRYPTION_PADDING)
                .SetUserAuthenticationRequired(true)
                .Build();
        keyGen.Init(keyGenSpec);
        keyGen.GenerateKey();
    }
}
```

示例代码将创建一个新`Cipher`为每个`CryptoObject`，使用的应用程序创建的密钥。 该密钥由`KEY_NAME`已设置在开头的变量`CryptoObjectHelper`类。 该方法`GetKey`将尝试并检索使用 Android 密钥存储 Api 的密钥。 如果键不存在，则该方法`CreateKey`将创建应用程序的新键。

密码通过调用实例化`Cipher.GetInstance`，采用_转换_（一个字符串值，该值指示如何进行加密和解密数据的密码）。 对调用`Cipher.Init`将完成的密码初始化通过提供从应用程序密钥。 

请务必认识到有某些情况下，Android 其中可能会使该密钥： 

* 新的具有指纹具有已注册设备。
* 没有已注册设备的指纹。
* 用户已禁用屏幕锁定。
* 屏幕锁定 （screenlock 或使用的 PIN/模式的类型），用户已更改。

在此情况下，`Cipher.Init`将引发[ `KeyPermanentlyInvalidatedException` ](https://developer.android.com/reference/android/security/keystore/KeyPermanentlyInvalidatedException.html)。 上面的示例代码将捕获该异常、 删除密钥，，然后创建一个新。

下一节将讨论如何创建密钥，并将其存储在设备上。

## <a name="creating-a-secret-key"></a>创建机密密钥

`CryptoObjectHelper`类使用 Android [ `KeyGenerator` ](https://developer.xamarin.com/api/type/Javax.Crypto.KeyGenerator/)创建密钥并将其存储在设备上。 `KeyGenerator`类可以创建密钥，但需要一些有关要创建的密钥类型的元数据。 此信息由的实例提供[ `KeyGenParameterSpec` ](https://developer.android.com/reference/android/security/keystore/KeyGenParameterSpec.html)类。 

一个`KeyGenerator`实例化时使用`GetInstance`工厂方法。 示例代码使用[_高级加密标准_](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) (_AES_) 作为加密算法。 AES 会将数据分解为固定大小的块并对每个这些块加密。

下一步，`KeyGenParameterSpec`使用创建`KeyGenParameterSpec.Builder`。 `KeyGenParameterSpec.Builder`包装有关是要创建的密钥的以下信息：

* 密钥名称。
* 密钥必须同时用于加密和解密。
* 在示例代码`BLOCK_MODE`设置为_密码块链_(`KeyProperties.BlockModeCbc`)，这意味着每个块与上一个块 （创建每个块之间的依赖关系） 位移。 
* `CryptoObjectHelper`使用[_公共密钥加密标准 #7_ ](https://tools.ietf.org/html/rfc2315) (_PKCS7_) 来生成出的块，以确保它们是所有相同的大小将填充的字节数.
* `SetUserAuthenticationRequired(true)` 意味着该用户身份验证是必需的然后才能使用该密钥。

一次`KeyGenParameterSpec`是创建的它用于初始化`KeyGenerator`，以生成密钥并安全地将其存储在设备上。 

## <a name="using-the-cryptoobjecthelper"></a>使用 CryptoObjectHelper

现在，示例代码已封装创建的逻辑的大部分`CryptoWrapper`成`CryptoObjectHelper`类，让我们重新审视该代码从一开始本指南并使用`CryptoObjectHelper`创建密码并启动指纹扫描程序： 

```csharp
protected void FingerPrintAuthenticationExample()
{
    const int flags = 0; /* always zero (0) */
    
    CryptoObjectHelper cryptoHelper = new CryptoObjectHelper();
    cancellationSignal = new Android.Support.V4.OS.CancellationSignal();
    
    // Using the Support Library classes for maximum reach
    FingerprintManagerCompat fingerPrintManager = FingerprintManagerCompat.From(this);
    
    // AuthCallbacks is a C# class defined elsewhere in code.
    FingerprintManagerCompat.AuthenticationCallback authenticationCallback = new MyAuthCallbackSample(this);

    // Here is where the CryptoObjectHelper builds the CryptoObject. 
    fingerprintManager.Authenticate(cryptohelper.BuildCryptoObject(), flags, cancellationSignal, authenticationCallback, null);
}
```

既然我们已了解如何创建`CryptoObject`，可让看看如何`FingerprintManager.AuthenticationCallbacks`用于将指纹扫描程序服务的结果传输到 Android 应用程序。



## <a name="related-links"></a>相关链接

- [Cipher](https://developer.xamarin.com/api/type/Javax.Crypto.Cipher/)
- [FingerprintManager.CryptoObject](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.CryptoObject.html)
- [FingerprintManagerCompat.CryptoObject](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.CryptoObject.html)
- [KeyGenerator](https://developer.xamarin.com/api/type/Javax.Crypto.KeyGenerator/)
- [KeyGenParameterSpec](https://developer.android.com/reference/android/security/keystore/KeyGenParameterSpec.html)
- [KeyGenParameterSpec.Builder](https://developer.android.com/reference/android/security/keystore/KeyGenParameterSpec.Builder.html)
- [KeyPermanentlyInvalidatedException](https://developer.android.com/reference/android/security/keystore/KeyPermanentlyInvalidatedException.html)
- [KeyProperties](https://developer.android.com/reference/android/security/keystore/KeyProperties.html)
- [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)
- [RFC 2315 - PCKS #7](https://tools.ietf.org/html/rfc2315)
