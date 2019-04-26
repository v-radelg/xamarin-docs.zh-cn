---
title: 在 Android 应用链接
description: 本指南介绍了 Android 6.0 如何支持应用的链接，该技术允许移动应用来响应在网站上的 Url。 它将讨论哪些应用链接、 如何实现应用链接在 Android 6.0 应用程序，以及如何配置网站以授予对域的移动应用的权限。
ms.prod: xamarin
ms.assetid: 48174E39-19FD-43BC-B54C-9AF11D4B1F91
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: dd4ba236df8e5993c7f7ed86393eb66ce01db595
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60956146"
---
# <a name="app-linking-in-android"></a>在 Android 应用链接

_本指南介绍了 Android 6.0 如何支持应用的链接，该技术允许移动应用来响应在网站上的 Url。它将讨论哪些应用链接、 如何实现应用链接在 Android 6.0 应用程序，以及如何配置网站以授予对域的移动应用的权限。_

## <a name="app-linking-overview"></a>应用链接的概述

移动应用程序不再位于接收器&ndash;在许多情况下它们是其业务，以及他们的网站的重要组件。 最好让企业能够将其网络影响力和移动应用程序，无缝连接上启动移动应用程序和移动应用中显示相关内容的网站的链接。 *将应用链接*(也称为*深度链接*) 是一种方法，允许移动设备，以响应一个 URI，并启动与该 URI 相对应的移动应用程序。

Android 处理通过将应用链接*意向系统*&ndash;当用户单击移动浏览器中的链接，在移动浏览器将调度 Android 将委托给已注册的应用程序意向。 例如，单击烹饪与网站上的链接可打开该网站与关联的移动应用，并向用户显示特定的 recipe。 如果有多个应用程序注册用于处理该意向，则 Android 将引发所谓*消除二义性对话框*，会询问用户要选择应为处理目的，应用程序的应用程序示例：

![一个消除二义性对话框的屏幕截图示例](app-linking-images/01-disambiguation-dialog.png)

通过使用自动链接处理情况下，android 6.0 提高此状态。 适用于 Android 的 uri，用作默认处理程序会自动注册应用程序可以&ndash;应用将自动启动，并直接导航到相关活动。 决定如何处理 URI 单击 Android 6.0 取决于以下条件：

1. **现有应用程序已与 URI 相关联**&ndash;用户可能已经关联现有应用程序 URI。 在这种情况下，Android 将继续使用该应用程序。
2. **没有任何现有应用程序与 URI，但安装支持的应用程序**&ndash;在此方案中，用户未指定现有应用程序，因此 Android 将使用已安装支持的应用程序来处理该请求。
3. **没有任何现有应用程序与 URI，但许多支持的应用程序安装**&ndash;因为有多个应用程序支持的 URI，将显示消除二义性对话框并且用户必须选择将哪个应用处理 URI。

如果用户已安装的任何应用支持 URI，并且随后安装一个，则 Android 将设置该应用程序用作默认处理程序对 uri 验证利用与 URI 相关联的网站关联后。

本指南将讨论如何配置 Android 6.0 应用程序以及如何创建和发布数字资产链接文件以支持 Android 6.0 中的应用链接。

## <a name="requirements"></a>要求

本指南需要 Xamarin.Android 6.1 和面向 Android 6.0 （API 级别 23） 的应用程序或更高版本。

通过使用应用程序链接就可以在早期版本的 Android[铆钉 NuGet 包](https://www.nuget.org/packages/Rivets/)从 Xamarin 组件商店。 铆钉包不兼容使用 Android 6.0; 中的应用链接它不支持 Android 6.0 应用程序链接。

## <a name="configuring-app-linking-in-android-60"></a>在 Android 6.0 中配置应用链接

设置 Android 6.0 中的应用链接过程包括两个主要步骤：

1. **添加一个或多个目的的筛选器的网站 URI** &ndash;意向筛选器指导如何处理移动浏览器中的 URL，请单击 Android。
2. **发布*数字资产的链接 JSON*网站上的文件**&ndash;这是一个文件上传到网站和 Android 用于验证的移动应用和网站的域之间的关系。 如果没有，Android 不能安装应用程序作为默认句柄的 URI;用户必须手动执行此操作。

<a name="configure-intent-filter" />

### <a name="configuring-the-intent-filter"></a>配置意向筛选器

它是所需配置一个意向筛选器，将从网站的 URI （或可能的一组 Uri） 映射到 Android 应用程序中的活动。 在 Xamarin.Android 中，此关系的建立方式装饰的活动[IntentFilterAttribute](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/)。 意向筛选器必须声明以下信息：

* **`Intent.ActionView`** &ndash; 此操作将注册意向筛选器可以响应请求来查看信息
* **`Categories`** &ndash;  意向的筛选器应注册同时 **[Intent.CategoryBrowsable](https://developer.xamarin.com/api/field/Android.Content.Intent.CategoryBrowsable/)** 和 **[Intent.CategoryDefault](https://developer.xamarin.com/api/field/Android.Content.Intent.CategoryDefault/)** 为了能够正确处理 web URI。
* **`DataScheme`** &ndash; 意向筛选器必须声明`http`和/或`https`。 这些是仅有两个有效的方案。
* **`DataHost`** &ndash; 这是 Uri 将源于的域。
* **`DataPathPrefix`** &ndash; 这是在网站上的资源的可选路径。
* **`AutoVerify`** &ndash; `autoVerify`属性告知 Android 在验证该应用程序和网站之间的关系。 这将下面详细讨论。

下面的示例演示如何使用[IntentFilterAttribute](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/)来处理来自链接`https://www.recipe-app.com/recipes`并从`http://www.recipe-app.com/recipes`:

```csharp
[IntentFilter(new [] { Intent.ActionView },
              Categories = new[] { Intent.CategoryBrowsable, Intent.CategoryDefault },
              DataScheme = "http",
              DataHost = "recipe-app.com",
              DataPathPrefix = "/recipe",
              AutoVerify=true)]
public class RecipeActivity : Activity
{
    // Code for the activity omitted
}
```

Android 将验证针对该网站上的数字资产文件的意向筛选器之前应用程序的默认处理程序的注册的 uri，标识每个主机。 Android 可以建立应用程序的默认处理程序之前，所有意向筛选器必须通过验证。

### <a name="creating-the-digital-assets-link-file"></a>创建数字资产链接文件

将应用链接的 android 6.0 需要 Android 验证 URI，该应用程序设置为默认处理程序之前应用程序和网站之间的关联。 首次安装应用程序时，将发生此验证。 *数字资产链接*文件是由相关 webdomain(s) 承载一个 JSON 文件。

> [!NOTE]
> `android:autoVerify`属性必须由意向筛选器设置&ndash;否则 Android 不会执行验证。

文件将位于的位置的域的网络管理员通过**https://domain/.well-known/assetlinks.json**。

数字资产文件包含元数据所必需的 Android 验证关联。 **Assetlinks.json**文件具有以下键 / 值对：

* `namespace` &ndash; Android 应用程序的命名空间。
* `package_name` &ndash; （在应用程序清单中声明） 的 Android 应用程序的包名称。
* `sha256_cert_fingerprints` &ndash; 已签名的应用程序的 SHA256 指纹。 请参阅指南[查找密钥存储的 MD5 或 SHA1 签名](~/android/deploy-test/signing/keystore-signature.md)有关如何获取应用程序的 SHA1 指纹的详细信息。

以下代码片段示范了**assetlinks.json**了一个应用程序列出：

```json
[
   {
      "relation":[
         "delegate_permission/common.handle_all_urls"
      ],
      "target":{
         "namespace":"android_app",
         "package_name":"com.example",
         "sha256_cert_fingerprints":[
            "14:6D:E9:83:C5:73:06:50:D8:EE:B9:95:2F:34:FC:64:16:A0:83:42:E6:1D:BE:A8:8A:04:96:B2:3F:CF:44:E5"
         ]
      }
   }
]
```

它可以注册多个 SHA256 指纹，以支持不同版本或版本的应用程序。 此下一步**assetlinks.json**文件是注册多个应用程序的示例：

```json
[
   {
      "relation":[
         "delegate_permission/common.handle_all_urls"
      ],
      "target":{
         "namespace":"android_app",
         "package_name":"example.com.puppies.app",
         "sha256_cert_fingerprints":[
            "14:6D:E9:83:C5:73:06:50:D8:EE:B9:95:2F:34:FC:64:16:A0:83:42:E6:1D:BE:A8:8A:04:96:B2:3F:CF:44:E5"
         ]
      }
   },
   {
      "relation":[
         "delegate_permission/common.handle_all_urls"
      ],
      "target":{
         "namespace":"android_app",
         "package_name":"example.com.monkeys.app",
         "sha256_cert_fingerprints":[
            "14:6D:E9:83:C5:73:06:50:D8:EE:B9:95:2F:34:FC:64:16:A0:83:42:E6:1D:BE:A8:8A:04:96:B2:3F:CF:44:E5"
         ]
      }
   }
]
```

[Google 数字资产链接网站](https://developers.google.com/digital-asset-links/tools/generator)有可能帮助进行创建和测试的数字资产文件的联机工具。

### <a name="testing-app-links"></a>测试应用链接

实现应用链接之后, 应测试的各个部分，以确保它们按预期方式工作。

则可以确认数字资产文件正确格式并托管通过使用 Google 的数字资产的链接 API，在此示例中所示：

```html
https://digitalassetlinks.googleapis.com/v1/statements:list?source.web.site=
  https://<WEB SITE ADDRESS>:&relation=delegate_permission/common.handle_all_urls
```

有两个都可以用来确保已正确配置的意向筛选器和的 uri，该应用程序设置为默认处理程序的测试：

1.  如上文所述正确托管数字资产文件。 第一个测试将调度该 Android 应重定向到移动应用程序意向。 Android 应用程序应启动并显示为 URL 注册的活动。 在命令提示符下键入：

    ```shell
    $ adb shell am start -a android.intent.action.VIEW \
        -c android.intent.category.BROWSABLE \
        -d "http://<domain1>/recipe/scalloped-potato"
    ```

2.  显示处理策略在给定设备上安装的应用程序的现有链接。 以下命令将转储每个用户在设备上使用以下信息的链接策略的列表。 在命令提示符处，键入下列命令：

    ```shell
    $ adb shell dumpsys package domain-preferred-apps
    ```

    * **`Package`** &ndash; 应用程序的包名称。
    * **`Domain`** &ndash; 应用程序将处理其 web 链接域 （由空格分隔）
    * **`Status`** &ndash; 这是应用程序的当前链接处理状态。 值为**始终**意味着该应用程序具有`android:autoVerify=true`声明，并通过了系统验证。 它是后接十六进制数表示的首选项的 Android 系统记录。

    例如：

    ```shell
    $ adb shell dumpsys package domain-preferred-apps

    App linkages for user 0:
    Package: com.android.vending
    Domains: play.google.com market.android.com
    Status: always : 200000002
    ```

## <a name="summary"></a>总结

本指南介绍如何将应用链接 Android 6.0 中的工作原理。 然后介绍了如何配置 Android 6.0 应用程序以支持和响应的应用程序的链接。 它还介绍了如何测试应用程序将 Android 应用程序中的链接。


## <a name="related-links"></a>相关链接

- [查找密钥存储的 MD5 或 SHA1 签名](~/android/deploy-test/signing/keystore-signature.md)
- [活动和意向](https://university.xamarin.com/classes#4)
- [AppLinks](http://applinks.org/)
- [Google 数字资产的链接](https://developers.google.com/digital-asset-links/)
- [语句列表生成器和测试人员](https://developers.google.com/digital-asset-links/tools/generator)
