---
title: 手动上传 APK
ms.prod: xamarin
ms.assetid: 1309C251-ABF0-4412-B1F5-200DC8321A9D
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/15/2018
ms.openlocfilehash: 3852e2a1460cb5659cf5a00dcc17fb9a6e9c26a8
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69525308"
---
# <a name="manually-uploading-the-apk"></a>手动上传 APK


第一次将 APK 提交到 Google Play（或如果使用 Xamarin.Android 的早期版本）时，必须通过 [Google Play 开发者控制台](https://play.google.com/apps/publish)手动上传 APK。 本指南介绍此过程所需的步骤。 


## <a name="google-play-developer-console"></a>Google Play 开发者控制台

已编译 APK 并准备好促销资产后，必须将该应用程序上传到 Google Play。 通过登录到 [Google Play 开发者控制台](https://play.google.com/apps/publish)完成此操作，如图所示。 单击“在 Google Play 上发布 Android 应用”  按钮，启动分发应用程序的进程。

[![Google Play 开发者控制台](manually-uploading-the-apk-images/00-google-play-developer-console-sml.png)](manually-uploading-the-apk-images/00-google-play-developer-console.png#lightbox)

如果某个现有应用已注册了 Google Play，请单击“添加新应用程序”  按钮：

[![添加新应用程序按钮](manually-uploading-the-apk-images/01-existing-app-sml.png)](manually-uploading-the-apk-images/01-existing-app.png#lightbox)

当显示“添加新应用程序”  对话框时，输入应用名称并单击“上传 APK”  ：

[![上传 APK 按钮](manually-uploading-the-apk-images/02-add-new-application-sml.png)](manually-uploading-the-apk-images/02-add-new-application.png#lightbox)

显示如下屏幕时，可发布应用以进行 alpha 测试、Beta 测试或生产。 下面的示例选择了“ALPHA 测试”  选项卡。 由于“MyApp”  不使用授权服务，因此在本示例中，不需要单击“获取许可证密钥”  按钮。 在此处，单击“将第一个 APK 上传到 Alpha”  按钮以发布到 Alpha 通道：

[![将第一个 APK 上传到 Alpha 按钮](manually-uploading-the-apk-images/03-upload-to-alpha-sml.png)](manually-uploading-the-apk-images/03-upload-to-alpha.png#lightbox)

随即显示“将新 APK 上传到 ALPHA”  对话框。 可通过单击“浏览文件”  按钮或拖放 APK 来上传 APK： 

[![将新 APK 上传到 Alpha 对话框](manually-uploading-the-apk-images/04-upload-dialog-sml.png)](manually-uploading-the-apk-images/04-upload-dialog.png#lightbox)

请务必上传已准备好进行发布的待分发 APK。
下一个对话框指示 APK 上传的进度：

[![上传进度指示](manually-uploading-the-apk-images/05-upload-progress-sml.png)](manually-uploading-the-apk-images/05-upload-progress.png#lightbox)

上传 APK 后，可以选择测试方法：

[![“选择测试方法”对话框](manually-uploading-the-apk-images/06-select-testing-method-sml.png)](manually-uploading-the-apk-images/06-select-testing-method.png#lightbox)

有关应用测试的详细信息，请参阅 Google 的[设置 alpha/beta 测试](https://support.google.com/googleplay/android-developer/answer/3131213?hl=en)指南。

上传 APK 后，会将它保存为草稿。 如下文所述，向 Google Play 提供更多详细信息之后才能将其发布。


## <a name="store-listing"></a>应用商店一览

单击“Google Play 开发者控制台”  中的“应用商店一览”  ，输入 Google Play 将向应用程序的潜在用户显示的信息： 

[![“应用商店一览”对话框](manually-uploading-the-apk-images/07-store-listing-sml.png)](manually-uploading-the-apk-images/07-store-listing.png#lightbox)


### <a name="graphics-assets"></a>图形资产

向下滚动到“应用商店一览”  页的“图形资产”  部分：

[![“图形资产”部分](manually-uploading-the-apk-images/08-graphic-assets-sml.png)](manually-uploading-the-apk-images/08-graphic-assets.png#lightbox)

在本部分中上传前面已准备好的所有促销资产。 提供有关必须提供哪些促销资产，以及应以何种格式提供它们的指导。


### <a name="categorization"></a>分类

“图形资产”  部分之后是“分类”  部分，选择应用程序类型和类别：

[![“分类”部分](manually-uploading-the-apk-images/09-categorization-sml.png)](manually-uploading-the-apk-images/09-categorization.png#lightbox)

下一部分介绍内容分级。


### <a name="contact-details"></a>联系人详细信息

此页的最后一个部分是“联系人详细信息”  。 此部分用于收集有关应用程序的开发者的联系信息：

[![“联系人详细信息”部分](manually-uploading-the-apk-images/10-contact-details-sml.png)](manually-uploading-the-apk-images/10-contact-details.png#lightbox)

可以在“隐私策略”  部分提供应用隐私策略的 URL，如上所示。


## <a name="content-rating"></a>内容分级

单击“Google Play 开发者控制台”  中的“内容分级”  。 在此页中指定应用的内容分级。 Google Play 要求所有应用程序都指定内容分级。 单击“继续”  按钮完成内容分级问卷：

[![“内容分级”部分](manually-uploading-the-apk-images/11-content-rating-sml.png)](manually-uploading-the-apk-images/11-content-rating.png#lightbox)

Google Play 上的所有应用程序必须根据 Google Play 分级系统进行分级。 除内容分级以外，所有应用程序还必须遵守 Google 的[开发者内容政策](http://www.android.com/us/developer-content-policy.html)。

下表列出了 Google Play 分级系统中的四个级别，并提供有关可能要求或强制分级的功能或内容的一些指导： 

- **任何人** &ndash; 不可访问、发布或共享位置数据。 不可承载用户生成的任何内容。 不可实现用户之间的通信。 

- **低成熟度** &ndash; 访问但不共享位置数据的应用程序。 轻度暴力或卡通级暴力描述。 

- **中等成熟度** &ndash; 涉及毒品、酒精或烟草。 赌博主题或模拟赌博。 煽动性内容。 不敬或粗俗幽默内容。 性暗示内容或性相关内容。 
    激烈的幻想暴力。 现实的暴力内容。 允许用户相互查找。 允许用户相互通信。 
    用户位置数据共享。 

- **高成熟度** &ndash; 侧重酒精、烟草或毒品消费或销售。 侧重于性暗示或性相关的内容。 图像暴力。 

中等成熟度列表中的项的判断具有主观性，这样的话，有可能根据某个准则判断为中等成熟度级别的内容也可能有充分的合理性被判断为高成熟度级别。 


## <a name="pricing-amp-distribution"></a>定价和分发

单击“Google Play 开发者控制台”  中的“定价和分发”  。 如果应用是付费应用，则在此页中设置价格。
或者，可以将应用程序免费分发给所有用户。 一旦将应用程序指定为免费，则必须始终保持免费。
Google Play 不允许将免费应用程序更改为付费应用（但是，可以使用应用内计费销售免费应用的内容）。 Google Play 允许随时将付费应用更改为免费应用。

在发布付费应用之前，需要有商家帐户。若需要帐户，请单击“设置商家帐户”  并按说明进行操作。

[![“报价和分发”对话框](manually-uploading-the-apk-images/12-pricing-sml.png)](manually-uploading-the-apk-images/12-pricing.png#lightbox)


### <a name="manage-countries"></a>管理国家/地区

下一部分，“管理国家/地区”  支持对应用可能会分发到哪些国家/地区进行控制：

[![“管理国家/地区”对话框](manually-uploading-the-apk-images/13-manage-countries-sml.png)](manually-uploading-the-apk-images/13-manage-countries.png#lightbox)


### <a name="other-information"></a>其他信息

继续向下滚动来指定应用是否包含广告。 此外，“设备类别”  部分提供分发适用于 Android Wear、Android TV 或 Android Auto 的应用的选项（可选）：

[![“包含广告”部分](manually-uploading-the-apk-images/14-contains-ads-sml.png)](manually-uploading-the-apk-images/14-contains-ads.png#lightbox)

此部分之后是可以选择的其他选项，如选择“家庭专用”  和通过 Google Play for Education 分发应用。


### <a name="consent"></a>许可

“定价和分发”  页面的底部是“许可”  部分。
这是必需的部分，用于声明应用程序满足 [Android 内容准则](http://www.android.com/market/terms/developer-content-policy.html#hl=us)，并确认应用程序受美国出口法律显示：

[![“许可”部分](manually-uploading-the-apk-images/15-consent-sml.png)](manually-uploading-the-apk-images/15-consent.png#lightbox)

关于发布 Xamarin.Android 应用的信息远远不止本指南中所包含的内容。
有关在 Google Play 中发布应用的详细信息，请参阅[欢迎使用 Google Play 开发者控制台帮助中心](https://support.google.com/googleplay/android-developer#topic=3450769)。



## <a name="google-play-filters"></a>Google Play 筛选器

当用户浏览 Google Play 网站的应用程序时，他们能够搜索所有已发布的应用程序。 用户从 Android 设备浏览 Google Play 时，搜索结果会略有不同。 结果将会根据与正在使用的设备的兼容性进行筛选。 例如，如果某个应用程序必须发送 SMS 消息，则 Google Play 不会向不能发送 SMS 消息的任何设备显示该应用程序。 可通过以下项创建应用于搜索的筛选器：

1. 设备的硬件配置。
2. 应用程序清单文件中的声明。
3. 使用的载波（如果有）。
4. 设备的位置。

可以向应用的清单添加元素，以帮助控制在 Google Play 商店中筛选应用的方式。 下表列出了可用于筛选应用程序的清单元素和特性：

- [supports-screen](https://developer.android.com/guide/topics/manifest/supports-screens-element.html) &ndash; Google Play 使用此属性，根据屏幕大小确定应用程序是否可部署到设备中。 
    Google Play 假定 Android 可将较小布局调整为较大屏幕，但反之不成立。 因此，声明支持标准屏幕的应用程序会在搜索较大屏幕而非较小屏幕时的搜索结果中出现。 如果 Xamarin.Android 应用程序在清单文件中不提供 `<supports-screen>` 元素，Google Play 将假定所有属性均具有值 true，并且该应用程序支持所有屏幕大小。 必须将此元素手动添加到 **AndroidManifest.xml**。 

- [uses-configuration](https://developer.android.com/guide/topics/manifest/uses-configuration-element.html) &ndash; 此清单元素用于请求某些硬件功能，例如键盘类型、导航设备和触摸屏等。必须将此元素手动添加到 **AndroidManifest.xml**。 

- [uses-feature](https://developer.android.com/guide/topics/manifest/uses-feature-element.html) &ndash; 此清单元素声明为确保应用程序正常运行，设备所必需的硬件或软件功能。 此特性仅提供信息。 Google Play 不会向设备显示不符合此筛选条件的应用程序。 仍可通过其他方式（手动或下载）安装该应用程序。 必须将此元素手动添加到 **AndroidManifest.xml**。 

- [uses-library](https://developer.android.com/guide/topics/manifest/uses-library-element.html) &ndash; 此元素指定设备上必须存在的某些共享库（如 Google Maps）。 还可使用 `Android.App.UsesLibraryAttribute` 指定此元素。 例如: 

    ```csharp
    [assembly: UsesLibrary("com.google.android.maps", true)]
    ```

- [uses-permission](https://developer.android.com/guide/topics/manifest/uses-permission-element.html) &ndash; 此元素用于推断应用程序正常运行所必需的某些硬件功能可能未通过 `<uses-feature>` 元素正确声明。 例如，如果应用程序请求照相机使用权限，则 Google Play 会假定设备必定装有摄像头，即使没有任何声明摄像头的 `<uses-feature>` 元素。 此元素可通过 `Android.App.UsesPermissionsAttribute` 进行设置。 例如: 

    ```csharp
    [assembly: UsesPermission(Manifest.Permission.Camera)]
    ```

- [uses-sdk](https://developer.android.com/guide/topics/manifest/uses-sdk-element.html) &ndash; 该元素用于声明应用程序所需的最低 Android API 级别。 此元素可在 Xamarin.Android 项目的 Xamarin.Android 选项中进行设置。 

- [compatible-screens](https://developer.android.com/guide/topics/manifest/compatible-screens-element.html) &ndash; 此元素用于筛选与其指定的屏幕大小和密度不匹配的应用程序。 大多数应用程序不应使用此筛选器。 此筛选器专用于特定的高性能游戏或需要严格控制应用程序分发的应用程序。 上面提到的 `<support-screen>` 特性是首选特性。 

- [supports-gl-texture](https://developer.android.com/guide/topics/manifest/supports-gl-texture-element.html) &ndash; 此元素用于声明应用程序所需的 GL 纹理压缩构造。 大多数应用程序不应使用此筛选器。 此筛选器专用于特定的高性能游戏或需要严格控制应用程序分发的应用程序。 

有关配置应用清单的详细信息，请参阅 Android [应用清单](https://developer.android.com/guide/topics/manifest/manifest-intro.html)主题。
