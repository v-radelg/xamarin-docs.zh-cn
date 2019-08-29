---
title: 创建 CryptoObject
ms.prod: xamarin
ms.assetid: 4D159B80-FFF4-4136-A7F0-F8A5C6B86838
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 03796af880aaef74c2d4b54007ac34ef1c5dc180
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70119841"
---
# <a name="creating-a-cryptoobject"></a>创建 CryptoObject

指纹身份验证结果的完整性对应用程序&ndash;而言很重要, 即应用程序了解用户身份的方式。 理论上, 第三方恶意软件可能会截获并篡改指纹扫描器返回的结果。 本部分将讨论一种保护指纹结果有效性的方法。 

是围绕 Java 加密 api 的包装器, `FingerprintManager`由用于保护身份验证请求的完整性。 `FingerprintManager.CryptoObject` 通常, `Javax.Crypto.Cipher`对象是用于对指纹扫描器的结果进行加密的机制。 `Cipher`对象本身将使用应用程序使用 Android 密钥库 api 创建的密钥。

若要了解这些类的整体工作方式, 让我们首先看看下面的代码, 其中演示了如何`CryptoObject`创建, 然后更详细地说明了:

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

示例代码将使用应用程序创建`Cipher`的键`CryptoObject`为每个代码创建一个新的。 键由`KEY_NAME`在`CryptoObjectHelper`类的开头设置的变量标识。 方法`GetKey`将尝试使用 Android 密钥库 api 来检索密钥。 如果该键不存在, 则该方法`CreateKey`将为应用程序创建新的密钥。

使用转换 (告知密码如何加密和`Cipher.GetInstance`解密数据的字符串值), 通过对的调用来实例化密码。 对的调用`Cipher.Init`将通过从应用程序提供密钥来完成密码初始化。 

需要注意的是, 在某些情况下, Android 可能会使密钥失效: 

- 新指纹已注册到设备。
- 未向设备注册任何指纹。
- 用户已禁用屏幕锁。
- 用户更改了屏幕锁 (screenlock 的类型或使用的 PIN/模式)。

发生这种情况`Cipher.Init`时, 将[`KeyPermanentlyInvalidatedException`](https://developer.android.com/reference/android/security/keystore/KeyPermanentlyInvalidatedException.html)引发。 上面的示例代码将捕获该异常, 删除该密钥, 然后创建一个新密钥。

下一部分将讨论如何创建密钥并将其存储在设备上。

## <a name="creating-a-secret-key"></a>创建密钥

类使用 Android [`KeyGenerator`](xref:Javax.Crypto.KeyGenerator)创建密钥, 并将其存储在设备上。 `CryptoObjectHelper` `KeyGenerator`类可以创建密钥, 但需要一些与要创建的密钥类型有关的元数据。 此信息由[`KeyGenParameterSpec`](https://developer.android.com/reference/android/security/keystore/KeyGenParameterSpec.html)类的实例提供。 

`KeyGenerator` 使用工厂`GetInstance`方法来实例化。 示例代码使用[_高级加密标准_](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)(_AES_) 作为加密算法。 AES 会将数据分解为固定大小的块, 并加密每个块。

接下来, `KeyGenParameterSpec` `KeyGenParameterSpec.Builder`使用创建。 `KeyGenParameterSpec.Builder`包装有关要创建的密钥的下列信息:

- 密钥名称。
- 密钥必须对加密和解密都有效。
- 在示例代码`BLOCK_MODE`中, 将设置为_密码块链_(`KeyProperties.BlockModeCbc`), 这意味着每个块都与前一个块 XORed (创建每个块之间的依赖关系)。 
- 使用公钥[_加密标准 #7_](https://tools.ietf.org/html/rfc2315) (PKCS7) 生成将填充块的字节, 以确保它们都具有相同的大小。 `CryptoObjectHelper`
- `SetUserAuthenticationRequired(true)`表示在使用密钥之前需要用户身份验证。

创建后, 它将用于`KeyGenerator`初始化, 它将生成一个密钥并将其安全地存储在设备上。 `KeyGenParameterSpec` 

## <a name="using-the-cryptoobjecthelper"></a>使用 CryptoObjectHelper

现在, 示例代码已封装了许多用于在`CryptoWrapper` `CryptoObjectHelper`类中创建的逻辑, 接下来, 让我们重新访问`CryptoObjectHelper`本指南开头的代码, 并使用创建密码并启动指纹扫描器: 

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

现在`CryptoObject`, 我们已了解如何创建, 接下来了解`FingerprintManager.AuthenticationCallbacks`如何使用将指纹扫描器服务的结果传输到 Android 应用程序。



## <a name="related-links"></a>相关链接

- [密码](xref:Javax.Crypto.Cipher)
- [FingerprintManager.CryptoObject](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.CryptoObject.html)
- [FingerprintManagerCompat.CryptoObject](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.CryptoObject.html)
- [KeyGenerator](xref:Javax.Crypto.KeyGenerator)
- [KeyGenParameterSpec](https://developer.android.com/reference/android/security/keystore/KeyGenParameterSpec.html)
- [KeyGenParameterSpec.Builder](https://developer.android.com/reference/android/security/keystore/KeyGenParameterSpec.Builder.html)
- [KeyPermanentlyInvalidatedException](https://developer.android.com/reference/android/security/keystore/KeyPermanentlyInvalidatedException.html)
- [KeyProperties](https://developer.android.com/reference/android/security/keystore/KeyProperties.html)
- [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)
- [RFC 2315-PCKS #7](https://tools.ietf.org/html/rfc2315)
