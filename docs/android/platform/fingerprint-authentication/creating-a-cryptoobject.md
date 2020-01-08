---
title: 创建 CryptoObject
ms.prod: xamarin
ms.assetid: 4D159B80-FFF4-4136-A7F0-F8A5C6B86838
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 871058d1c128b37a0f2e77b43587139efb433de1
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75487771"
---
# <a name="creating-a-cryptoobject"></a>创建 CryptoObject

指纹身份验证结果的完整性对应用程序而言很重要 &ndash; 它是应用程序了解用户身份的方式。 理论上，第三方恶意软件可能会截获并篡改指纹扫描器返回的结果。 本部分将讨论一种保护指纹结果有效性的方法。 

`FingerprintManager.CryptoObject` 是围绕 Java 加密 Api 的包装器，由 `FingerprintManager` 用于保护身份验证请求的完整性。 通常，`Javax.Crypto.Cipher` 对象是用于对指纹扫描器的结果进行加密的机制。 `Cipher` 对象本身将使用应用程序使用 Android 密钥库 Api 创建的密钥。

若要了解这些类的整体工作方式，让我们首先看看下面的代码，其中演示了如何创建 `CryptoObject`，并详细说明了：

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
            cipher.Init(CipherMode.EncryptMode, key);
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

示例代码将使用应用程序创建的键为每个 `CryptoObject`创建一个新的 `Cipher`。 该键由在 `CryptoObjectHelper` 类的开头设置的 `KEY_NAME` 变量标识。 方法 `GetKey` 将使用 Android 密钥库 Api 尝试并检索密钥。 如果该键不存在，则 `CreateKey` 方法将为应用程序创建新的密钥。

使用对 `Cipher.GetInstance`的调用来实例化密码，并采用_转换_（告知密码如何加密和解密数据的字符串值）。 对 `Cipher.Init` 的调用将通过从应用程序提供密钥来完成密码初始化。 

需要注意的是，在某些情况下，Android 可能会使密钥失效： 

- 新指纹已注册到设备。
- 未向设备注册任何指纹。
- 用户已禁用屏幕锁。
- 用户更改了屏幕锁（screenlock 的类型或使用的 PIN/模式）。

发生这种情况时，`Cipher.Init` 将引发[`KeyPermanentlyInvalidatedException`](https://developer.android.com/reference/android/security/keystore/KeyPermanentlyInvalidatedException.html)。 上面的示例代码将捕获该异常，删除该密钥，然后创建一个新密钥。

下一部分将讨论如何创建密钥并将其存储在设备上。

## <a name="creating-a-secret-key"></a>创建密钥

`CryptoObjectHelper` 类使用 Android [`KeyGenerator`](xref:Javax.Crypto.KeyGenerator)创建密钥，并将其存储在设备上。 `KeyGenerator` 类可以创建密钥，但需要一些与要创建的密钥类型有关的元数据。 此信息由[`KeyGenParameterSpec`](https://developer.android.com/reference/android/security/keystore/KeyGenParameterSpec.html)类的实例提供。 

使用 `GetInstance` 工厂方法实例化 `KeyGenerator`。 示例代码使用[_高级加密标准_](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)（_AES_）作为加密算法。 AES 会将数据分解为固定大小的块，并加密每个块。

接下来，使用 `KeyGenParameterSpec.Builder`创建 `KeyGenParameterSpec`。 `KeyGenParameterSpec.Builder` 包装有关要创建的密钥的下列信息：

- 密钥名称。
- 密钥必须对加密和解密都有效。
- 在示例代码中，`BLOCK_MODE` 设置为_密码块链_（`KeyProperties.BlockModeCbc`），这意味着每个块都与前一个块 XORed （创建每个块之间的依赖关系）。 
- `CryptoObjectHelper` 使用[_公钥加密标准 #7_](https://tools.ietf.org/html/rfc2315) （_PKCS7_）来生成将填充块的字节，以确保它们都具有相同的大小。
- `SetUserAuthenticationRequired(true)` 表示在使用密钥之前需要用户身份验证。

创建 `KeyGenParameterSpec` 后，它将用于初始化 `KeyGenerator`，它将生成一个密钥并将其安全地存储在设备上。 

## <a name="using-the-cryptoobjecthelper"></a>使用 CryptoObjectHelper

现在，示例代码已封装了许多用于创建 `CryptoWrapper` 到 `CryptoObjectHelper` 类的逻辑，接下来让我们重新访问本指南开头的代码，并使用 `CryptoObjectHelper` 创建密码并启动指纹扫描器： 

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

现在，我们已经了解了如何创建 `CryptoObject`，接下来了解如何使用 `FingerprintManager.AuthenticationCallbacks` 将指纹扫描器服务的结果传输到 Android 应用程序。

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
