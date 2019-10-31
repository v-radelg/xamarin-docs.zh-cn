---
title: 查找密钥存储的签名
ms.prod: xamarin
ms.assetid: 1b511fec-e6f6-453e-89c8-810aafb02b77
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 49d67db46416584248a9b15db606538f5ebfba79
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73021007"
---
# <a name="finding-your-keystores-signature"></a>查找密钥存储的签名

Xamarin.Android 应用的 MD5 或 SHA1 签名取决于用于对 APK 进行签名的 **.keystore** 文件。 通常，调试版本会使用不同于发布版本的 **.keystore** 文件。

## <a name="for-debug--non-custom-signed-builds"></a>对于调试/非自定义签名版本

Xamarin.Android 使用相同的 **debug.keystore** 文件对所有调试版本进行签名。 初次安装 Xamarin.Android 时会生成此文件。以下步骤详细介绍了默认 Xamarin.Android **debug.keystore** 文件的 MD5 或 SHA1 签名查找过程。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

找到用于对应用进行签名的 Xamarin **debug.keystore** 文件。 默认情况下，用于对 Xamarin.Android 应用程序调试版本进行签名的密钥存储位于以下位置：

**C:\\Users\\*USERNAME*\\AppData\\Local\\Xamarin\\Mono for Android\\debug.keystore**

可通过从 JDK 运行 `keytool.exe` 命令来获取有关密钥存储的信息。 此工具通常位于以下位置：

**C:\\Program Files (x86)\\Java\\jdk*VERSION*\\bin\\keytool.exe**

将包含 **keytool.exe** 的目录添加到 `PATH` 环境变量。
打开**命令提示符**，使用下面的命令运行 `keytool.exe`：

```cmd
keytool.exe -list -v -keystore "%LocalAppData%\Xamarin\Mono for Android\debug.keystore" -alias androiddebugkey -storepass android -keypass android
```

运行时，**keytool.exe** 应输出以下文本。 **MD5:** 和 **SHA1:** 标签标识相应的签名：

```cmd
Alias name: androiddebugkey
Creation date: Aug 19, 2014
Entry type: PrivateKeyEntry
Certificate chain length: 1
Certificate[1]:
Owner: CN=Android Debug, O=Android, C=US
Issuer: CN=Android Debug, O=Android, C=US
Serial number: 53f3b126
Valid from: Tue Aug 19 13:18:46 PDT 2014 until: Sun Nov 15 12:18:46 PST 2043
Certificate fingerprints:
         MD5:  27:78:7C:31:64:C2:79:C6:ED:E5:80:51:33:9C:03:57
         SHA1: 00:E5:8B:DA:29:49:9D:FC:1D:DA:E7:EE:EE:1A:8A:C7:85:E7:31:23
         SHA256: 21:0D:73:90:1D:D6:3D:AB:4C:80:4E:C4:A9:CB:97:FF:34:DD:B4:42:FC:
08:13:E0:49:51:65:A6:7C:7C:90:45
         Signature algorithm name: SHA1withRSA
         Version: 3
```

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

找到用于对应用进行签名的 Xamarin **debug.keystore** 文件。 默认情况下，用于对 Xamarin.Android 应用程序调试版本进行签名的密钥存储位于以下位置：

**~/.local/share/Xamarin/Mono for Android/debug.keystore**

可通过从 JDK 运行 **keytool** 命令来获取有关密钥存储的信息。 此工具通常位于以下位置：

**/System/Library/Java/JavaVirtualMachines/*VERSION*.jdk/Contents/Home/bin/keytool**

将包含 **keytool** 的目录添加到 **PATH** 环境变量。
打开终端，使用下面的命令运行 keytool   ：

```bash
$ keytool -list -v -keystore ~/.local/share/Xamarin/Mono\ for\ Android/debug.keystore -alias androiddebugkey -storepass android -keypass android
```

运行时，**keytool** 应输出以下文本。 **MD5:** 和 **SHA1:** 标签标识相应的签名：

```bash
Alias name: androiddebugkey
Creation date: Apr 16, 2015
Entry type: PrivateKeyEntry
Certificate chain length: 1
Certificate[1]:
Owner: CN=Android Debug, O=Android, C=US
Issuer: CN=Android Debug, O=Android, C=US
Serial number: 76afa120
Valid from: Thu Apr 16 10:52:27 PDT 2015 until: Sat Apr 08 10:52:27 PDT 2045
Certificate fingerprints:
     MD5:  0A:D3:7E:80:3D:40:2A:23:89:B9:AB:9C:4B:B6:63:36
     SHA1: 89:33:8F:F2:C5:0C:91:08:4A:CF:04:A5:EC:4A:31:80:84:18:0D:D4
     SHA256: 91:AC:3E:2F:CB:EF:50:07:2B:E0:D9:8D:8B:C2:42:87:6A:85:02:86:EB:44:84:10:34:02:ED:35:CE:C6:38:47
     Signature algorithm name: SHA256withRSA
     Version: 3

Extensions:

#1: ObjectId: 2.5.29.14 Criticality=false
SubjectKeyIdentifier [
KeyIdentifier [
0000: 65 6C FE 67 8E CD CB 9A   01 CD 9F E8 25 9C A4 A3  el.g........%...
0010: 4F 4E CF 17                                        ON..
]
]
```

-----

## <a name="for-release--custom-signed-builds"></a>对于发布/自定义签名版本

对于使用自定义 **.keystore** 文件签名的发布版本，过程与上述相同，将 Xamarin.Android 使用的 **debug.keystore** 文件替换为发布 **.keystore** 文件。 发布密钥存储文件创建后，替换为自己的密钥存储密码值和别名。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

使用 Visual Studio“分发”  向导对 Xamarin.Android 应用进行签名时，产生的密钥存储位于以下位置：

C:\\Users\\USERNAME\\AppData\\Local\\Xamarin\\Mono for Android\\Keystore\\alias\\alias.keystore   

例如，如果按照[创建新证书](~/android/deploy-test/signing/index.md#newcertvs)中的步骤创建新的签名密钥，则产生的示例密钥存储位于以下位置：

C:\\Users\\USERNAME\\AppData\\Local\\Xamarin\\Mono for Android\\Keystore\\chimp\\chimp.keystore  

有关对 Xamarin.Android 应用进行签名的详细信息，请参阅[对 Android 应用程序包进行签名](~/android/deploy-test/signing/index.md)。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

通过 Visual Studio for Mac 的“签名并分发...”  向导对应用签名时，产生的密钥存储位于以下位置：

**~/Library/Developer/Xamarin/Keystore/*alias*/*alias*.keystore**

例如，如果按照[创建新证书](~/android/deploy-test/signing/index.md#newcertxs)中的步骤创建新的签名密钥，则产生的示例密钥存储位于以下位置：

**~/Library/Developer/Xamarin/Keystore/chimp/chimp.keystore**

有关对 Xamarin.Android 应用进行签名的详细信息，请参阅[对 Android 应用程序包进行签名](~/android/deploy-test/signing/index.md)。

-----
