---
title: 对 APK 进行手动签名
ms.prod: xamarin
ms.assetid: 08549E1C-7F04-4D20-9E7A-794B9D09FD12
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: d20ec990253ff86e7b426baad8da5a919a91ef6c
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69525021"
---
# <a name="manually-signing-the-apk"></a>对 APK 进行手动签名


生成用于发布的应用程序后，应先对 APK 进行签名，然后再进行分发，以便它能够在 Android 设备上运行。 此过程通常在 IDE 中处理，但某些情况下，需要在命令行中手动对 APK 进行签名。 对 APK 进行签名的步骤如下：

1. **创建私钥** &ndash; 此步骤仅需执行一次。 对 APK 进行数字签名需要私钥。
    准备好私钥后，可对将来的发布版本跳过此步骤。

2. **使用 Zipalign 优化 APK** &ndash; *Zipalign* 是对应用程序执行的优化过程。 它可使 Android 和 APK 在运行时的交互更高效。 Xamarin.Android 会在运行时执行检查，如果 APK 尚未使用 zipalign 进行优化，则不允许应用程序运行。

3. 对 APK 进行签名&ndash;此步骤涉及使用来自 Android SDK 的 apksigner 实用工具，并通过上一步创建的私钥对 APK 进行签名。   对于使用 v24.0.3 之前的 Android SDK 生成工具版本进行开发的应用程序，它们将使用来自 JDK 的 jarsigner 应用。  下面更详细地讨论这两种工具。 

步骤的顺序至关重要，取决于使用何种工具对 APK 进行签名。 当使用 apksigner 时，务必先使用 zipalign 优化应用程序，然后再通过 apksigner 对其签名。     如需使用 jarsigner 对 APK 进行签名，则需先对 APK 进行签名，然后再运行 zipalign。   



## <a name="prerequisites"></a>系统必备

本指南主要介绍使用来自 Android SDK 生成工具 v24.0.3 或更高版本的 apksigner。  假设已生成 APK。

通过早期 Android SDK 生成工具版本生成的应用程序必须使用 jarsigner  ，如下面的[使用 jarsigner 对 APK 进行签名](#Sign_the_APK_with_jarsigner)中所述。



## <a name="create-a-private-keystore"></a>创建专用密钥存储

密钥存储  是使用 Java SDK 中的 [keytool](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html) 程序创建的安全证书数据库。 密钥存储对发布 Xamarin.Android 应用程序至关重要，因为 Android 仅运行经数字签名的应用程序。

在开发期间，Xamarin.Android 使用调试密钥存储对应用程序进行签名，从而使应用程序可直接部署到仿真程序或配置为使用可调试应用程序的设备中。
但若要分发应用程序，此密钥存储不会被视为有效的密钥存储。

因此，必须创建专用密钥存储，并用其对应用程序签名。 该步骤应该仅执行一次，因为同一密钥可用于发布更新，然后用于对其他应用程序进行签名。

请务必保护此密钥存储。 如果丢失，则无法通过 Google Play 发布应用程序更新。
若要解决密钥存储丢失造成的问题，只能创建新的密钥存储、使用新密钥对 APK 重新签名，然后提交新版应用程序。 然后，必须从 Google Play 中删除旧版应用程序。 同样，如果新的密钥存储被泄露或公开分发，则可能会分发非官方版本或恶意版本的应用程序。



### <a name="create-a-new-keystore"></a>创建新的密钥存储

需要 Java SDK 中的命令行工具 [keytool](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html) 才可新建密钥存储。 下面的示例代码段演示了如何使用 **keytool**（将 `<my-filename>` 替换为密钥存储的文件名，将 `<key-name>` 替换为密钥存储内密钥的名称）：

```shell
$ keytool -genkeypair -v -keystore <filename>.keystore -alias <key-name> -keyalg RSA \
          -keysize 2048 -validity 10000
```

**keytool** 首先要求提供密钥存储的密码。 然后要求提供一些有助于创建密钥的信息。 下面的示例代码段演示了如何创建将存储在文件 `xample.keystore` 中的名为 `publishingdoc` 的新密钥：

```shell
$ keytool -genkeypair -v -keystore xample.keystore -alias publishingdoc -keyalg RSA -keysize 2048 -validity 10000
Enter keystore password:
Re-enter new password:
What is your first and last name?
  [Unknown]:  Ham Chimpanze
What is the name of your organizational unit?
  [Unknown]:  NASA
What is the name of your organization?
  [Unknown]:  NASA
What is the name of your City or Locality?
  [Unknown]:  Cape Canaveral
What is the name of your State or Province?
  [Unknown]:  Florida
What is the two-letter country code for this unit?
  [Unknown]:  US
Is CN=Ham Chimpanze, OU=NASA, O=NASA, L=Cape Canaveral, ST=Florida, C=US correct?
  [no]:  yes

Generating 2,048 bit RSA key pair and self-signed certificate (SHA1withRSA) with a validity of 10,000 days
        for: CN=Ham Chimpanze, OU=NASA, O=NASA, L=Cape Canaveral, ST=Florida, C=US
Enter key password for <publishingdoc>
        (RETURN if same as keystore password):
Re-enter new password:
[Storing xample.keystore]
```

若要列出密钥存储中存储的密钥，请通过 **keytool** 中使用 &ndash; `list` 选项：

```shell
$ keytool -list -keystore xample.keystore
```


## <a name="zipalign-the-apk"></a>使用 Zipalign 优化 APK

在使用 apksigner 对 APK 进行签名之前，首先要使用来自 Android SDK 的 zipalign 工具对文件进行优化。   zipalign 将沿着 4 字节边界重组 APK 中的资源。  这种调整使 Android 可快速加载 APK 中的资源，提高应用程序的性能，并有可能降低内存使用。 Xamarin.Android 将执行运行时检查，确定是否已使用 zipalign 优化 APK。 对 APK 进行 zipalign 优化后，应用程序才会运行。

以下命令将使用已签名的 APK 并生成一个名为 **helloworld.apk** 的 APK，后者已经过签名和 zipalign 优化且可分发。

```shell
$ zipalign -f -v 4 mono.samples.helloworld-unsigned.apk helloworld.apk
```


## <a name="sign-the-apk"></a>对 APK 进行签名

对 APK 进行 zipalign 优化后，务必使用密钥存储对其进行签名。 此操作需使用 apksigner 工具来完成，该工具可在 SDK 生成工具版本的 build-tools 目录中找到。    例如，如果安装了 Android SDK 生成工具 v25.0.3，则可在目录中找到 apksigner： 

```bash
$ ls $ANDROID_HOME/build-tools/25.0.3/apksigner
/Users/tom/android-sdk-macosx/build-tools/25.0.3/apksigner*
```

以下代码片段假设 apksigner 可由 `PATH` 环境变量访问。  它使用包含在文件 xample.keystore 中的密钥别名 `publishingdoc` 来签名 APK： 

```shell
$ apksigner sign --ks xample.keystore --ks-key-alias publishingdoc mono.samples.helloworld.apk
```

运行此命令时，apksigner 将要求提供密钥存储的密码（如有必要）。 

有关如何使用 apksigner 的详细信息，请参阅 [Google 文档](https://developer.android.com/studio/command-line/apksigner.html)。 

> [!NOTE]
> 根据 [Google 问题 62696222](https://issuetracker.google.com/issues/62696222)，Android SDK 中“缺少”apksigner  。 此问题的解决方案是安装 Android SDK 生成工具 v25.0.3，并使用该版本的 apksigner。   


<a name="Sign_the_APK_with_jarsigner" />

### <a name="sign-the-apk-with-jarsigner"></a>使用 jarsigner 对 APK 进行签名

> [!WARNING]
> 本节仅适用于需要使用 jarsigner  实用工具对 APK 进行签名的情况。 推荐开发者使用 apksigner 对 APK 进行签名。 

此技术包括使用来自 Java SDK 的 [jarsigner](https://docs.oracle.com/javase/8/docs/technotes/tools/windows/jarsigner.html) 命令对 APK 文件进行签名。   jarsigner 工具由 Java SDK 提供。  

以下示例演示如何使用 **jarsigner** 和 **xample.keystore** 密钥存储文件中包含的密钥 `publishingdoc` 对 APK 进行签名：

```shell
$ jarsigner -verbose -sigalg SHA1withRSA -digestalg SHA1 -keystore xample.keystore mono.samples.helloworld.apk publishingdoc
```

> [!NOTE]
> 使用 jarsigner  时，务必首先  对 APK 进行签名，然后再使用 zipalign  。  



## <a name="related-links"></a>相关链接

- [应用程序签名](https://source.android.com/security/apksigning/)
- [Java JAR 签名](https://docs.oracle.com/javase/8/docs/technotes~/jar/jar.html#Signed_JAR_File)
- [jarsigner](https://docs.oracle.com/javase/8/docs/technotes/tools/windows/jarsigner.html)
- [keytool](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html)
- [zipalign](https://developer.android.com/studio/command-line/zipalign.html)
- [生成工具 26.0.0 - apksigner 去哪里了？](https://issuetracker.google.com/issues/62696222)
