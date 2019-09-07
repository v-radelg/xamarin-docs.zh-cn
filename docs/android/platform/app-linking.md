---
title: Android 中的应用链接
description: 本指南将讨论 Android 6.0 如何支持应用链接，这是一种允许移动应用响应网站上的 Url 的技术。 它将讨论什么是应用链接，如何在 Android 6.0 应用程序中实现应用程序链接，以及如何将网站配置为向域的移动应用程序授予权限。
ms.prod: xamarin
ms.assetid: 48174E39-19FD-43BC-B54C-9AF11D4B1F91
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: d65e8fabff88489571bba9d03379ff605a6ed0fe
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70757730"
---
# <a name="app-linking-in-android"></a>Android 中的应用链接

_本指南将讨论 Android 6.0 如何支持应用链接，这是一种允许移动应用响应网站上的 Url 的技术。它将讨论什么是应用链接，如何在 Android 6.0 应用程序中实现应用程序链接，以及如何将网站配置为向域的移动应用程序授予权限。_

## <a name="app-linking-overview"></a>应用链接概述

移动应用程序在许多情况下不再&ndash;出现在接收器中，它们是业务的重要组成部分及其网站。 企业可以无缝地连接其 web 平台和移动应用程序，并提供了一个网站上的链接，可启动移动应用程序并在移动应用程序中显示相关内容。 *应用链接*（也称为 "*深层链接*"）是一种允许移动设备响应 uri 并启动与该 uri 对应的移动应用程序的方法。

当用户单击移动浏览器中的链接时，android 会通过*意向系统* &ndash;处理应用链接，移动浏览器将分派 Android 将委托给已注册应用程序的意图。 例如，单击烹饪网站上的链接将打开与该网站关联的移动应用，并向用户显示特定食谱。 如果注册了多个应用程序来处理该意向，则 Android 会引发所谓*歧义消除对话框*，该对话框会询问用户哪个应用程序选择应处理此目的的应用程序，例如：

![消除歧义对话框的示例屏幕截图](app-linking-images/01-disambiguation-dialog.png)

Android 6.0 通过使用自动链接处理改善了这一点。 Android 可以自动将应用程序注册为 URI &ndash;的默认处理程序，应用程序将自动启动并直接导航到相关活动。 Android 6.0 如何决定处理 URI 单击取决于以下条件：

1. **现有应用已与 URI 关联**&ndash;用户可能已将现有应用与 URI 相关联。 在这种情况下，Android 将继续使用该应用程序。
2. **没有与该 URI 关联的现有应用程序，但安装了支持的应用**&ndash;在此方案中，用户未指定现有应用程序，因此 Android 将使用安装的支持应用程序来处理请求。
3. **没有与该 URI 关联的现有应用程序，但安装了许多支持应用**&ndash;由于存在多个支持 uri 的应用程序，因此将显示消除歧义对话框，用户必须选择将处理 uri 的应用。

如果用户没有安装支持 URI 的应用程序，并且随后安装了一个应用程序，则在验证与该 URI 关联的网站的关联后，Android 会将该应用程序设置为 URI 的默认处理程序。

本指南将讨论如何配置 Android 6.0 应用程序以及如何创建和发布 "数字资产链接" 文件，以支持 Android 6.0 中的应用程序链接。

## <a name="requirements"></a>要求

本指南需要 Xamarin 6.1 和面向 Android 6.0 （API 级别23）或更高版本的应用程序。

使用 Xamarin 组件存储中的[铆钉 NuGet 包](https://www.nuget.org/packages/Rivets/)，可以在早期版本的 Android 中进行应用链接。 铆钉包与 Android 6.0 中的应用链接不兼容;它不支持 Android 6.0 应用链接。

## <a name="configuring-app-linking-in-android-60"></a>在 Android 6.0 中配置应用链接

设置应用-Android 6.0 中的链接涉及两个主要步骤：

1. **为网站 URI 添加一个或多个意向筛选器**&ndash;意向筛选器指南 Android 在如何处理 URL 中单击移动浏览器。
2. **发布*数字资产链接 JSON*文件网站** &ndash;上的文件这是一个文件，该文件已上传到网站，Android 使用它来验证移动应用与网站域之间的关系。 如果没有此设置，Android 将无法安装应用作为 URI 的默认句柄;用户必须手动完成此操作。

<a name="configure-intent-filter" />

### <a name="configuring-the-intent-filter"></a>配置意向筛选器

需要配置一个意向筛选器，将 URI （或可能为的一组 uri）从网站映射到 Android 应用程序中的活动。 在 Xamarin 中，此关系是通过使用[IntentFilterAttribute](xref:Android.App.IntentFilterAttribute)的活动装饰建立的。 目的筛选器必须声明以下信息：

- **`Intent.ActionView`** &ndash;这将注册意向筛选器以响应查看信息的请求
- **`Categories`** &ndash;  意向的筛选器应注册同时 **[Intent.CategoryBrowsable](xref:Android.Content.Intent.CategoryBrowsable)** 和 **[Intent.CategoryDefault](xref:Android.Content.Intent.CategoryDefault)** 为了能够正确处理 web URI。
- **`DataScheme`** 目的筛选器必须声明`http`和/或`https`。 &ndash; 这是两个有效的方案。
- **`DataHost`** &ndash;这是 uri 将源自的域。
- **`DataPathPrefix`** &ndash;这是网站上的资源的可选路径。
- **`AutoVerify`** &ndash; 特性告诉Android验证应用程序与`autoVerify`网站之间的关系。 下面将对此进行更详细的讨论。

下面的示例演示如何使用[IntentFilterAttribute](xref:Android.App.IntentFilterAttribute)处理来自`https://www.recipe-app.com/recipes`和的`http://www.recipe-app.com/recipes`链接：

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

在将应用程序注册为 URI 的默认处理程序之前，Android 将验证由意向筛选器针对网站上的数字资产文件标识的所有主机。 所有目的筛选器都必须通过验证，才能将应用程序创建为默认处理程序。

### <a name="creating-the-digital-assets-link-file"></a>创建数字资产链接文件

Android 6.0 应用链接要求在将应用程序设置为 URI 的默认处理程序之前，Android 验证应用程序与网站之间的关联。 首次安装应用程序时，将发生此验证。 *数字资产链接*文件是一个 JSON 文件，该文件由相关的 webdomain 托管。

> [!NOTE]
> 特性必须由意向筛选器&ndash;设置，否则 Android 不会执行验证。 `android:autoVerify`

该文件由域的网站管理员放置在该位置 **https://domain/.well-known/assetlinks.json** 。

数字资产文件包含 Android 验证关联所需的元数据。 **Assetlinks**文件具有以下键-值对：

- `namespace`&ndash; Android 应用程序的命名空间。
- `package_name`&ndash; Android 应用程序的包名称（在应用程序清单中声明）。
- `sha256_cert_fingerprints`&ndash;已签名应用程序的 SHA256 指纹。 有关如何获取应用程序的 SHA1 指纹的详细信息，请参阅[查找密钥存储的 MD5 或 SHA1 签名](~/android/deploy-test/signing/keystore-signature.md)指南。

以下代码片段是**assetlinks**的一个示例，其中列出了单个应用程序：

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

可以注册多个 SHA256 指纹以支持不同版本的应用程序。 下一个**assetlinks**文件是注册多个应用程序的示例：

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

[Google "数字资产链接" 网站](https://developers.google.com/digital-asset-links/tools/generator)有一个联机工具，可帮助创建和测试数字资产文件。

### <a name="testing-app-links"></a>测试应用链接

实现应用链接后，应测试各个部分以确保它们按预期方式工作。

可以通过使用 Google 的 "数字资产链接 API" 来确认数字资产文件的格式是否正确并已托管，如以下示例中所示：

```html
https://digitalassetlinks.googleapis.com/v1/statements:list?source.web.site=
  https://<WEB SITE ADDRESS>:&relation=delegate_permission/common.handle_all_urls
```

可以执行两个测试，以确保已正确配置目的筛选器并将应用设置为 URI 的默认处理程序：

1. 正确托管数字资产文件，如上所述。 第一次测试会调度 Android 应重定向到移动应用程序的意图。 Android 应用程序应启动并显示为 URL 注册的活动。 在命令提示符下键入：

    ```shell
    $ adb shell am start -a android.intent.action.VIEW \
        -c android.intent.category.BROWSABLE \
        -d "http://<domain1>/recipe/scalloped-potato"
    ```

2. 显示在给定设备上安装的应用程序的现有链接处理策略。 以下命令将为设备上的每个用户转储一个链接策略列表，其中包含以下信息。 在命令提示符处，键入下列命令：

    ```shell
    $ adb shell dumpsys package domain-preferred-apps
    ```

    - **`Package`** &ndash;应用程序的包名称。
    - **`Domain`** &ndash;将由应用程序处理其 web 链接的域（由空格分隔）
    - **`Status`** &ndash;这是应用程序的当前链接处理状态。 如果值为 "**始终**"，则表示`android:autoVerify=true`应用程序已声明并通过了系统验证。 后跟一个十六进制数字，表示该首选项的 Android 系统记录。

    例如:

    ```shell
    $ adb shell dumpsys package domain-preferred-apps

    App linkages for user 0:
    Package: com.android.vending
    Domains: play.google.com market.android.com
    Status: always : 200000002
    ```

## <a name="summary"></a>总结

本指南讨论了应用链接在 Android 6.0 中的工作原理。 然后，它介绍了如何配置 Android 6.0 应用程序以支持和响应应用程序链接。 还介绍了如何在 Android 应用程序中测试应用程序链接。

## <a name="related-links"></a>相关链接

- [查找密钥存储的 MD5 或 SHA1 签名](~/android/deploy-test/signing/keystore-signature.md)
- [活动和意向](https://university.xamarin.com/classes#4)
- [AppLinks](http://applinks.org/)
- [Google 数字资产链接](https://developers.google.com/digital-asset-links/)
- [语句列表生成器和测试人员](https://developers.google.com/digital-asset-links/tools/generator)
