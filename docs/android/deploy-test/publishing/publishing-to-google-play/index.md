---
title: 发布到 Google Play
ms.prod: xamarin
ms.assetid: FB1CC234-3554-8566-48BD-2B9B3A28CC7F
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
---

# <a name="publishing-to-google-play"></a>发布到 Google Play

尽管有许多应用市场可以分发应用程序，但 Google Play 无疑是世界上最大和访问次数最多的 Android 应用商店。 Google Play 提供单一平台，可用于 Android 应用程序的分发、宣传推广、销售和销售分析。

本部分将介绍一些特定于 Google Play 的主题，例如注册成为发布者、收集资产以便帮助 Google Play 推广和宣传应用程序、Google Play 上的应用程序评分指南以及使用筛选器限定应用程序只部署到某些特定设备。


## <a name="requirements"></a>要求

若要通过 Google Play 分发应用程序，必须创建开发者帐户。 只需执行一次此操作，并且支付一次性费用 25 美元。

所有应用程序都需使用加密密钥进行签名，该密钥将于 2033 年 10 月 22 日后过期。

在 Google Play 上发布的 APK 的最大大小为 100MB。 如果应用程序超出该大小，Google Play 允许通过 APK 扩展文件交付额外资产。 Android 扩展文件允许 APK 具有 2 个额外文件，每个文件大小最大为 2GB。 Google Play 会免费托管和分发这些文件。 将在另一部分中对扩展文件进行讨论。

Google Play 并非全球可用。 一些区域可能不支持应用程序分发。



## <a name="becoming-a-publisher"></a>成为发布者

若要在 Google Play 上发布应用程序，需要具有发布者帐户。 若要注册发布者帐户，请按照以下步骤操作：

1.  访问 [Google Play 开发者控制台](https://play.google.com/apps/publish)。
1.  输入开发者基本身份信息。
1.  阅读并接受适用于所在区域的开发者分发协议。
1.  支付 25 美元注册费用。
1.  通过电子邮件确认验证。
1.  创建帐户后，便可使用 Google Play 发布应用程序。


并非所有的国家/地区都支持 Google Play。 可通过以下链接获取最新的国家/地区列表：

1.  [开发者 &amp; 商户注册支持区域](https://support.google.com/googleplay/android-developer/bin/answer.py?hl=en&amp;answer=150324) &ndash; 该列表包括支持开发者注册成为商户并销售付费应用程序的所有国家/地区。

1.  [支持向 Google Play 用户分发的区域](https://support.google.com/googleplay/android-developer/bin/answer.py?hl=en&amp;answer=138294) &ndash; 该列表包括支持分发应用程序的所有国家/地区。



### <a name="preparing-promotional-assets"></a>准备促销资产

为在 Google Play 上有效宣传和推广应用程序，Google 允许开发人员提交屏幕截图、图形和视频等促销资产。 Google Play 随后会使用这些资产宣传和推广应用程序。



#### <a name="launcher-icons"></a>启动器图标

启动器图标是表示应用程序的图形。 每个启动器图标应为具有透明度 alpha 通道的 32 位 PNG。 应用程序应具有适合所有通用屏幕密度的图标，如下面的列表中所述：

-   **ldpi** (120dpi) &ndash; 36 x 36 px
-   **mdpi** (160dpi) &ndash; 48 x 48 px
-   **hdpi** (240dpi) &ndash; 72 x 72 px
-   **xhdpi** (320dpi) &ndash; 96 x 96 px


用户在 Google Play 上首先看到的是应用程序的启动器图标，因此请务必使启动器图标具有视觉吸引力和一定意义。

启动器图标提示：

1.  **简单整洁** &ndash; 启动器图标应保持简单整洁。 这意味着图标中不应包含应用程序名称。 越简单的图标越容易记住，并且在尺寸较小的情况下也更易于识别。

1.  **图标不应细窄** &ndash; 过窄的图标在所有背景上不易突出。

1.  **使用 alpha 通道** &ndash; 图标应使用 alpha 通道，并且不应为全帧图像。



#### <a name="high-resolution-application-icons"></a>高分辨率应用程序图标

Google Play 上的应用程序需要使用高保真版本的应用程序图标。 其仅供 Google Play 使用，并不会替换应用程序启动器图标。 高分辨率图标的规格为：

1.  具有 alpha 通道的 32 位 PNG
1.  512 x 512 像素
1.  最大大小为 1024KB

[Android Asset Studio](https://romannurik.github.io/AndroidAssetStudio/) 是非常有用的工具，可用于创建合适的启动器图标以及高分辨率应用程序图标。



#### <a name="screen-shots"></a>屏幕截图

对于每个应用程序，Google Play 需要至少 2 张，最多 8 张屏幕截图。 它们会显示在 Google Play 中的应用程序详细信息页面。

屏幕截图规格如下：

1.  无 alpha 通道的 24 位 PNG 或 JPG
1.  320w x 480h、480w x 800h 或 480w x 854h。 会对横向的图像进行裁剪。



#### <a name="promotional-graphic"></a>促销图

这是 Google Play 使用的可选图像：

1.  规格为 180w x 120h 无 alpha 通道 24 位 PNG 或 JPG。
1.  图像无边框。



#### <a name="feature-graphic"></a>特征图形

由 Google Play 特征部分使用。 此图形可能会单独显示，不会附带应用程序图标。

1.  无 alpha 通道和透明度的 1024w x 500h 大小的 PNG 或 JPG。
1.  所有重要内容应在 924x500 大小范围内。 为满足样式要求，可能会裁剪超过此范围的像素。
1.  此图形可能会按比例缩小：使用较大的文本并简化图形。



#### <a name="video-link"></a>视频链接

这是一个展示应用程序的 YouTube 视频 URL。 视频长度应在 30 秒到 2 分钟之间，并能展示应用程序的最佳部分。



### <a name="publishing-to-google-play"></a>发布到 Google Play

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Xamarin Android 7.0 采用集成工作流，将应用从 Visual Studio 发布到 Google Play。 如果使用早于 7.0 版本的 Xamarin Android，则必须通过 Google Play 开发者控制台手动上传 APK。 此外，必须已至少上传 1 个 APK，才能使用此集成工作流。 如果尚未上传第一个 APK，则必须手动进行上传。 有关详细信息，请参阅[手动上传 APK](~/android/deploy-test/publishing/publishing-to-google-play/manually-uploading-the-apk.md)。

[创建新的证书](~/android/deploy-test/signing/index.md#newcert)说明了如何创建新的证书以对 Android 应用进行签名。 下一步，将已签名的应用发布到 Google Play：

1. 登录 Google Play 开发者帐户，创建链接到 Google Play 开发者帐户的新项目。
2. 创建对应用进行身份验证的 **OAuth 客户端**。
3. 在 Visual Studio 中输入生成的客户端 ID 和客户端密码。
4. 通过 Visual Studio 注册帐户。
5. 使用证书对应用进行签名。
6. 将已签名应用发布到 Google Play。

在 [归档以发布](~/android/deploy-test/release-prep/index.md#archive)中，“分发渠道”对话框显示两个分发选项：“临时”和“Google Play”。 如果显示的是“签名标识”对话框，请单击“返回”，返回到“分发渠道”对话框。 选择“Google Play”，然后单击“下一步”：

[![“分发通道”对话框](images/vs/01-distribution-channel-sml.png)](images/vs/01-distribution-channel.png#lightbox)

在“签名标识”对话框中，选择在[创建新的证书](~/android/deploy-test/signing/index.md#newcert)中创建的标识，然后单击“继续”：

[![“签名标识”对话框](images/vs/02-select-identity-sml.png)](images/vs/02-select-identity.png#lightbox)

在“Google Play 帐户”对话框中，单击“+”按钮，添加新的 Google Play 帐户：

[![“Google Play 帐户”对话框](images/vs/03-google-play-accounts-sml.png)](images/vs/03-google-play-accounts.png#lightbox)

在“注册 Google Play API 访问权限”对话框中，必须提供客户端 ID 和客户端密码，从而向 Google Play 开发者帐户提供 API 访问权限：

[![“注册 Google API 访问权限”对话框](images/vs/04-register-google-api-access-sml.png)](images/vs/04-register-google-api-access.png#lightbox)

下一部分介绍如何新建 Google API 项目以及如何生成所需的_客户端 ID_ 和_客户端密码_。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

Visual Studio for Mac 采用集成工作流，将应用发布到 Google Play。

[创建新的证书](~/android/deploy-test/signing/index.md#newcert)讨论了如何创建新的证书以对 Android 应用进行签名。 以下步骤概述了如何将 Xamarin.Android 应用发布到 Google Play：

1. 登录 Google Play 开发者帐户，创建链接到 Google Play 开发者帐户的新项目。
2. 创建对应用进行身份验证的 _OAuth 客户端_。
3. 在 Visual Studio for Mac 中输入生成的_客户端 ID_ 和_客户端密码_。
4. 通过 Visual Studio for Mac 注册帐户。
5. 使用证书对应用程序进行签名。
6. 将已签名应用程序发布到 Google Play。

在[用于发布的存档](~/android/deploy-test/release-prep/index.md#archive)中，“签名和分发...”对话框提供了两种分发选择。 选择“Google Play”，然后单击“下一步”：

[![“选择 Android 分发”对话框](images/xs/01-select-google-play-sml.png)](images/xs/01-select-google-play.png#lightbox)

在“Google Play API 帐户”对话框中，必须提供客户端 ID 和客户端密码，从而向 Google Play 开发者帐户提供 API 访问权限：

[![“Google Play API 帐户”对话框](images/xs/02-google-play-api-account-sml.png)](images/xs/02-google-play-api-account.png#lightbox)

下一部分介绍如何新建 Google API 项目以及如何生成所需的_客户端 ID_ 和_客户端密码_。

-----


#### <a name="create-a-google-api-project"></a>创建 Google API 项目

首先，登录 [Google Play 开发者帐户](https://play.google.com/apps/publish)。
如果尚没有 Google Play 开发者帐户，请参阅[发布入门](https://developer.android.com/distribute/googleplay/start.html)。
此外，Google Play 开发者 API [入门](https://developers.google.com/android-publisher/getting_started)还介绍了如何使用 Google Play 开发者 API。 登录 Google Play 开发者控制台后，请单击“设置”：

[![“设置”图标](images/01-google-play-developer-console-sml.png)](images/01-google-play-developer-console.png#lightbox)

在“设置”页上，选择“API 访问”，然后单击“新建项目”按钮：

[![“新建项目”按钮](images/02-create-new-project-sml.png)](images/02-create-new-project.png#lightbox)

约一分钟后，新 API 项目会自动生成，并链接到 Google Play 开发者控制台帐户。

下一步，为应用创建 OAuth 客户端（如果尚未创建）。 用户使用应用请求访问个人数据时，使用 OAuth 客户端 ID 对应用进行身份验证。
单击“创建 OAuth 客户端”，创建新的 OAuth 客户端：

[![“创建 OAuth 客户端”按钮](images/03-create-oauth-client-sml.png)](images/03-create-oauth-client.png#lightbox)

几秒钟后将生成新的客户端 ID。 单击“在 Google 开发者控制台中查看”，查看 Google 开发者控制台中的新客户端 ID：

[![显示的客户端 ID](images/04-generated-client-id-sml.png)](images/04-generated-client-id.png#lightbox)

显示客户端 ID 及其名称和创建日期。 单击“编辑 OAuth 客户端”图标，查看应用的客户端密码：

[![查看应用凭据](images/05-google-developer-console-sml.png)](images/05-google-developer-console.png#lightbox)

OAuth 客户端的默认名称是 Google Play Android 开发者。 可将其更改为 Xamarin.Android 应用的名称或其他任何合适的名称。 本示例中将 OAuth 客户端名称更改为此应用的名称，即 **MyApp**：

[![显示的客户端 ID 和密码](images/06-client-id-and-secret-sml.png)](images/06-client-id-and-secret.png#lightbox)

单击“保存”以保存更改。 此操作会返回“凭据”页面，在此处可通过单击“下载 JSON”图标下载凭据：

[![“下载 JSON”图标](images/07-download-json-sml.png)](images/07-download-json.png#lightbox)

此 JSON 文件包含客户端 ID 和客户端密码，可将其剪切并粘贴到下一步中的“签名和分发”对话框。


#### <a name="register-google-api-access"></a>注册 Google API 访问权限

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

使用客户端 ID 和客户端密码填写 Visual Studio for Mac 中的“Google Play API 帐户”对话框。 可以为帐户提供说明 &ndash; 这样可以注册多个 Google Play 帐户并可将以后的 APK 上传到其他 Google Play 帐户。 将客户端 ID 和客户端密码复制到此对话框，然后单击“注册”：

[![“注册 Google API 访问权限”对话框](images/vs/05-enter-client-id-and-secret-sml.png)](images/vs/05-enter-client-id-and-secret.png#lightbox)

将打开 Web 浏览器，并提示登录 Google Play Android 开发者帐户（如果尚未登录）。 登录后，Web 浏览器中会显示以下提示。
单击“允许”对应用授权：

[![“向应用授权”对话框](images/vs/06-authorize-app-sml.png)](images/vs/06-authorize-app.png#lightbox)

#### <a name="publish"></a>发布

单击“允许”后，浏览器会报告已接收验证码。即将关闭..._，且应用将被添加到 Visual Studio 中的 Google Play 帐户列表中。 在“Google Play 帐户”对话框中，单击“继续”：

[![已添加到 Google Play 帐户的帐户](images/vs/07-account-added-sml.png)](images/vs/07-account-added.png#lightbox)

接下来，会显示“Google Play 轨道”对话框。 Google Play 提供 4 个可用于上传应用的轨道：

* **Alpha** &ndash; 用于将早期版本的应用上传到较小的测试员列表。
* **Beta** &ndash; 用于将早期版本的应用上传到较大的测试员列表。
* **Rollout** &ndash; 允许一定比例的用户接收应用的更新版本；这样可逐渐增加比例，例如，以 10% 的用户开始，消除 bug 后增加到 100% 用户。
* **Production** &ndash; 准备好从 Google Play 应用商店全面分发应用时，请选择此轨道。

选择用于上传应用的 Google Play 轨道，然后单击“上传”。 如果选择 **Rollout**，请确保输入百分比值：

[![选择 Alpha、Beta、Rollout 或 Production](images/vs/08-google-play-track-sml.png)](images/vs/08-google-play-track.png#lightbox)

有关 Google Play 测试和分步推出的详细信息，请参阅[设置 alpha/beta 测试](https://support.google.com/googleplay/android-developer/answer/3131213?hl=en)。

接下来，在出现的对话框中输入签名证书密码。
输入密码，然后单击“确定”：

[![“签名密码”对话框](images/vs/09-certificate-password-sml.png)](images/vs/09-certificate-password.png#lightbox)

**存档管理器**会显示上传进度：

[![APK 上传进度](images/vs/10-uploading-apk-sml.png)](images/vs/10-uploading-apk.png#lightbox)

上传完成时，Visual Studio 左下角会显示完成状态：

[![“项目发布已完成”消息](images/vs/11-published-sml.png)](images/vs/11-published.png#lightbox)


### <a name="troubleshooting"></a>疑难解答

请注意，使用**发布到 Google Play** 前，必须已经向 Google Play 应用商店提交了 1 个 APK。 如果尚未上传 APK，则“发布向导”会在“错误”窗格中显示如下错误：

[![必须手动上传此应用的首个 APK](images/vs/12-upload-error-sml.png)](images/vs/12-upload-error.png#lightbox)

出现此错误时，请通过 Google Play 开发者控制台手动上传 APK（例如 Ad-Hoc 构建版本），并使用“分发渠道”对话框进行后续 APK 更新。  有关详细信息，请参阅[手动上传 APK](~/android/deploy-test/publishing/publishing-to-google-play/manually-uploading-the-apk.md)。 每次上传时必须更改 APK 版本代码，否则会出现如下错误：

[![已更新包含版本代码 (1) 的 APK](images/vs/13-version-code-error-sml.png)](images/vs/13-version-code-error.png#lightbox)

若要解决此错误，请使用不同的版本号重新生成应用，然后通过“分发渠道”对话框重新提交到 Google Play。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

使用客户端 ID 和客户端密码填写 Visual Studio for Mac 中的“Google Play API 帐户”对话框。 可以为帐户提供说明 &ndash; 这样可以注册多个 Google Play 帐户并可将以后的 APK 上传到其他 Google Play 帐户。 将客户端 ID 和客户端密码复制到此对话框，然后单击“注册”：

[![“授予访问权限”对话框](images/xs/10-register-sml.png)](images/xs/10-register.png#lightbox)

如果接受此客户端 ID 和客户端密码，则会显示“注册成功”消息。 单击“下一步”：

[![“注册成功”消息](images/xs/11-registration-successful-sml.png)](images/xs/11-registration-successful.png#lightbox)

在“Google Play 帐户”对话框中，选择用于上传应用程序的 Google 帐户和轨道：

[![“选择 Google 帐户”对话框](images/xs/12-choose-google-account-sml.png)](images/xs/12-choose-google-account.png#lightbox)

Google Play 提供 4 个可用于上传应用的轨道：

-   **Alpha** &ndash; 用于将早期版本的应用上传到较小的测试员列表。

-   **Beta** &ndash; 用于将早期版本的应用上传到较大的测试员列表。

-   **Rollout** &ndash; 允许一定比例的用户接收应用的更新版本；这样可逐渐增加比例，例如，以 10% 的用户开始，消除 bug 后增加到 100% 用户。

-   **Production** &ndash; 准备好从 Google Play 应用商店全面分发应用时，请选择此轨道。

有关 Google Play 测试和分步推出的详细信息，请参阅[设置 alpha/beta 测试](https://support.google.com/googleplay/android-developer/answer/3131213?hl=en)。

接下来，选择用于对应用进行签名的签名标识。
选择“使用现有密钥”以使用现有的签名标识；如果想详细了解如何创建新密钥，请参阅[创建新的证书](~/android/deploy-test/signing/index.md#newcert)指南。 选择用于对应用程序进行签名的证书后，请单击“下一步”：

[![“Android 签名标识”对话框](images/xs/13-android-signing-identity-sml.png)](images/xs/13-android-signing-identity.png#lightbox)

此时可将应用上传到 Google Play。 “发布到 Google Play”对话框汇总了应用的相关信息 &ndash; 单击“发布”将应用发布到 Google Play：

[![“发布到 Google Play”对话框](images/xs/14-publish-to-google-play-sml.png)](images/xs/14-publish-to-google-play.png#lightbox)

请注意，使用**发布到 Google Play** 前，必须已经向 Google Play 应用商店提交了 1 个 APK。 如果尚未上传 APK，则可能出现如下错误：

> _Google Play 要求手动上传此应用的第一个 APK。可以对此使用 ad-hoc APK。_

or

> 未找到给定包名称的应用程序。[404]

若要解决此错误，请通过 Google Play 开发者控制台手动上传 APK（例如 Ad-Hoc 构建版本），并使用“发布到 Google Play”对话框进行后续 APK 更新。 有关如何手动上传 APK 的信息，请参阅[手动上传 APK](~/android/deploy-test/publishing/publishing-to-google-play/manually-uploading-the-apk.md)。

-----
