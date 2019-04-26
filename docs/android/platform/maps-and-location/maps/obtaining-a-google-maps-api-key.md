---
title: 获取 Google Maps API 密钥
description: 如何获取 Google Maps API 密钥，以添加映射到您的应用程序的功能。
ms.prod: xamarin
ms.assetid: D5969C57-3444-465E-D6FF-249AEE62E127
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/25/2018
ms.openlocfilehash: bfeb9d8fa2a0b5a9b18ab8266500586e2e3b6c68
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61155264"
---
# <a name="obtaining-a-google-maps-api-key"></a>获取 Google Maps API 密钥

若要在 Android 中使用 Google 地图功能，你需要注册为 Google 地图 API 密钥。 执行此操作时，你只需将应用程序中看到而不是映射空白网格。 必须获取 Google Maps Android API v2 密钥-从较旧的 Google Maps Android API 密钥 v1 的密钥将不可用。

获取地图 API v2 密钥涉及以下步骤：

1.  检索用于对应用程序签名的密钥存储的 sha-1 指纹。
2.  在 Google Api 控制台中创建一个项目。
3.  获取 API 密钥。


## <a name="obtaining-your-signing-key-fingerprint"></a>获取签名的密钥指纹

若要请求来自 Google 地图 API 密钥，您需要知道用于签名应用程序的密钥存储的 sha-1 指纹。
通常情况下，这意味着您需要确定调试密钥存储，sha-1 指纹，然后 sha-1 指纹为用于发布应用程序进行签名的密钥存储。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

默认情况下使用的应用程序可以是的 Xamarin.Android 的调试版本进行签名的密钥存储在以下位置找到：

**C:\\用户\\[USERNAME]\\AppData\\本地\\Xamarin\\适用于 Android Mono\\debug.keystore**

可通过从 JDK 运行 `keytool` 命令来获取有关密钥存储的信息。 此工具通常 Java 的 bin 目录中找到：

**C:\\Program Files (x86)\\Java\\jdk [版本]\\bin\\keytool.exe**

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

默认情况下使用的应用程序可以是的 Xamarin.Android 的调试版本进行签名的密钥存储在以下位置找到：

**有关 Android/debug.keystore /Users/[USERNAME]/.local/share/Xamarin/Mono**

可通过从 JDK 运行 `keytool` 命令来获取有关密钥存储的信息。 此工具通常 Java 的 bin 目录中找到：

**/System/Library/Java/JavaVirtualMachines/[VERSION].jdk/Contents/Home/bin/keytool**

-----


运行 keytool 使用以下命令 （使用如上所示的文件路径）：

```shell
keytool -list -v -keystore [STORE FILENAME] -alias [KEY NAME] -storepass [STORE PASSWORD] -keypass [KEY PASSWORD]
```

### <a name="debugkeystore-example"></a>Debug.keystore 示例

对于默认调试密钥 （这自动为您创建用于调试），使用以下命令：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

```cmd
keytool.exe -list -v -keystore "C:\Users\[USERNAME]\AppData\Local\Xamarin\Mono for Android\debug.keystore" -alias androiddebugkey -storepass android -keypass android
```

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

```bash
keytool -list -v -keystore /Users/[USERNAME]/.local/share/Xamarin/Mono\ for\ Android/debug.keystore -alias androiddebugkey -storepass android -keypass android
```

-----


### <a name="production-keys"></a>生产密钥

当将应用部署到 Google Play，它必须是[使用私钥签名](~/android/deploy-test/signing/index.md)。
`keytool`将需要使用专用密钥的详细信息和生成 sha-1 指纹用于创建生产 Google 地图 API 密钥运行。 请记住更新**AndroidManifest.xml**部署前的正确 Google Maps API 密钥的文件。

### <a name="keytool-output"></a>Keytool 输出

您应看到类似于以下输出控制台窗口中的内容：

```shell
Alias name: androiddebugkey
Creation date: Jan 01, 2016
Entry type: PrivateKeyEntry
Certificate chain length: 1
Certificate[1]:
Owner: CN=Android Debug, O=Android, C=US
Issuer: CN=Android Debug, O=Android, C=US
Serial number: 4aa9b300
Valid from: Mon Jan 01 08:04:04 UTC 2013 until: Mon Jan 01 18:04:04 PST 2033
Certificate fingerprints:
    MD5:  AE:9F:95:D0:A6:86:89:BC:A8:70:BA:34:FF:6A:AC:F9
    SHA1: BB:0D:AC:74:D3:21:E1:43:07:71:9B:62:90:AF:A1:66:6E:44:5D:75
    Signature algorithm name: SHA1withRSA
    Version: 3
```

将使用 sha-1 指纹 (之后列出**SHA1**) 在本指南后面。

## <a name="creating-an-api-project"></a>创建 API 项目

检索签名的密钥存储的 sha-1 指纹后，有必要在 Google Api 控制台中创建一个新的项目 （或向现有项目添加 Google Maps Android API v2 服务）。

1. 在浏览器中，导航到[Google 开发人员控制台 API 和服务仪表板](https://console.developers.google.com/apis/dashboard/)然后单击**选择一个项目**。 单击项目名称，或通过单击创建新**新的项目**:

   [![Google 开发人员控制台中创建项目按钮](obtaining-a-google-maps-api-key-images/01-google-developer-console-vs-sml.png)](obtaining-a-google-maps-api-key-images/01-google-developer-console-vs.png#lightbox)

2. 如果您创建了一个新项目，请输入中的项目名称**新的项目**显示对话框。 此对话框将制造取决于你的项目名称的唯一项目 ID。 接下来，单击**创建**按钮在此示例中所示：

   [![新项目命名为 XamarinMapsDemo](obtaining-a-google-maps-api-key-images/02-new-project-vs-sml.png)](obtaining-a-google-maps-api-key-images/02-new-project-vs.png#lightbox)

3. 后一分钟左右，创建项目，并转到**仪表板**项目页。 在这里，单击**启用的 API 和服务**:

   [![单击库部分中的 Google Maps Android API](obtaining-a-google-maps-api-key-images/03-api-selection-vs-sml.png)](obtaining-a-google-maps-api-key-images/03-api-selection-vs.png#lightbox)

4. 从**API 库**页上，单击**Maps SDK for Android**。 在下一页上，单击**启用**若要打开此项目的服务：

   [![单击仪表板部分中的启用按钮](obtaining-a-google-maps-api-key-images/04-enable-api-vs-sml.png)](obtaining-a-google-maps-api-key-images/04-enable-api-vs.png#lightbox)

此时已创建的 API 项目和 Google Maps Android API v2 已添加到它。 但是，不能在项目中使用此 API 之前为其创建凭据。 下一部分介绍如何创建 API 密钥和允许列表的 Xamarin.Android 应用程序，以便其有权使用此密钥。

## <a name="obtaining-the-api-key"></a>获取 API 密钥

之后**Google Developer Console** API 项目已创建的有必要创建 Android API 密钥。 Xamarin.Android 应用程序必须具有 API 密钥之前向他们授予对 Android 的 Map API v2 的访问。

1. 中**Maps SDK for Android**显示的页 (单击后**启用**上一步中)，请转到**凭据**选项卡，单击**创建凭据**按钮：

   [![适用于 Android 的凭据消息地图 SDK](obtaining-a-google-maps-api-key-images/05-api-is-enabled-vs-sml.png)](obtaining-a-google-maps-api-key-images/05-api-is-enabled-vs.png#lightbox)

2. 单击**API 密钥**:

   [![将凭据添加到你的项目对话框](obtaining-a-google-maps-api-key-images/06-add-credentials-to-your-project-vs-sml.png)](obtaining-a-google-maps-api-key-images/06-add-credentials-to-your-project-vs.png#lightbox)

3. 单击此按钮后，将生成的 API 密钥。 接下来是需要限制此密钥，以便你的应用可以使用此密钥调用 Api。 单击**限制密钥**:

   [![单击凭据页上的限制密钥](obtaining-a-google-maps-api-key-images/07-generate-api-key-vs-sml.png)](obtaining-a-google-maps-api-key-images/07-generate-api-key-vs.png#lightbox)

4. 更改**名称**字段从**API 密钥 1**到将帮助您记住该密钥的使用的名称 (**XamarinMapsDemoKey**此示例中使用)。 接下来，单击**Android 应用**单选按钮：

   [![选择凭据页上的 Android 应用](obtaining-a-google-maps-api-key-images/08-key-restriction-vs-sml.png)](obtaining-a-google-maps-api-key-images/08-key-restriction-vs.png#lightbox)

5. 若要添加的 sha-1 指纹，请单击 **+ 添加包的名称和指纹**:

   [![单击添加包的名称和指纹](obtaining-a-google-maps-api-key-images/09-add-package-fingerprint-vs-sml.png)](obtaining-a-google-maps-api-key-images/09-add-package-fingerprint-vs.png#lightbox)

6. 输入你的应用包名称并输入 sha-1 证书指纹 (通过获得`keytool`本指南中前面所述)。 在以下示例中，包名称`XamarinMapsDemo`是输入后, 跟从获取 sha-1 证书指纹**debug.keystore**:

   [![输入包名称是 com.xamarin.docs.android.map](obtaining-a-google-maps-api-key-images/10-enter-package-and-sha1-vs-sml.png)](obtaining-a-google-maps-api-key-images/10-enter-package-and-sha1-vs.png#lightbox)

7. 请注意，为了使 APK 来访问 Google 地图，您必须包括 sha-1 指纹，包使用对 APK 进行签名每个密钥存储 （调试和发布） 的名称。 例如，如果一台计算机用于调试和生成发布 APK 的另一台计算机，您应包括 sha-1 证书指纹从第一台计算机的调试密钥存储和从的发布密钥存储的 sha-1 证书指纹第二台计算机。 单击 **+ 添加包的名称和指纹**以添加另一个指纹和包名称，在此示例中所示：

   [![添加另一个指纹创建另一个 sha-1 证书](obtaining-a-google-maps-api-key-images/11-second-fingerprint-vs-sml.png)](obtaining-a-google-maps-api-key-images/11-second-fingerprint-vs.png#lightbox)

8. 单击“保存”按钮保存更改。 接下来，你将返回到你的 API 密钥的列表。 如果有其他前面创建的 API 密钥，它们还将此处列出。 在此示例中，列出了只有一个 API 密钥 （在前面的步骤中创建）：

   [![在 API 密钥列表中显示 XamarinMapsDemoKey](obtaining-a-google-maps-api-key-images/12-list-of-apis-vs-sml.png)](obtaining-a-google-maps-api-key-images/12-list-of-apis-vs.png#lightbox)

## <a name="connect-the-project-to-a-billable-account"></a>将项目连接到一个可计费帐户

从 2018 年 6 月 11，API 密钥将无法工作如果项目不连接到计费帐户 （即使该服务仍然免费提供给移动应用）。

1. 单击汉堡菜单按钮，然后选择**计费**页：

   [![选择汉堡菜单计费部分](obtaining-a-google-maps-api-key-images/13-goto-billing-vs-sml.png)](obtaining-a-google-maps-api-key-images/13-goto-billing-vs.png#lightbox)

2. 将项目链接到计费帐户时，通过单击**计费帐户链接**跟**创建计费帐户**在显示弹出窗口 （如果还没有帐户，我们将帮助您创建一个新）：

   [![将项目链接到计费帐户](obtaining-a-google-maps-api-key-images/14-link-billing-account-vs-sml.png)](obtaining-a-google-maps-api-key-images/14-link-billing-account-vs.png#lightbox)

## <a name="adding-the-key-to-your-project"></a>将密钥添加到你的项目

最后，添加到此 API 密钥**AndroidManifest.XML** Xamarin.Android 应用程序的文件。 在以下示例中，`YOUR_API_KEY`是替换为在上一步骤中生成的 API 密钥：

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    android:versionName="4.10" package="com.xamarin.docs.android.mapsandlocationdemo"
    android:versionCode="10">
...
  <application android:label="@string/app_name">
    <!-- Put your Google Maps V2 API Key here. -->
    <meta-data android:name="com.google.android.maps.v2.API_KEY" android:value="YOUR_API_KEY" />
    <meta-data android:name="com.google.android.gms.version" android:value="@integer/google_play_services_version" />
  </application>
</manifest>
```

## <a name="related-links"></a>相关链接

- [Google Api 控制台](https://code.google.com/apis/console/)
- [Google 地图 API 密钥](https://developers.google.com/maps/documentation/android/start#the_google_maps_api_key)
- [keytool](http://docs.oracle.com/javase/6/docs/technotes/tools/windows/keytool.html.)
