---
title: 字体
ms.prod: xamarin
ms.assetid: 3F543FC5-FDED-47F8-8D2C-481FCC98BFDA
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 09/09/2018
ms.openlocfilehash: 3bfa3bbde68fab95d729cc8a558d4eb3baf7b4fa
ms.sourcegitcommit: 52fb214c0e0243587d4e9ad9306b75e92a8cc8b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2020
ms.locfileid: "76940657"
---
# <a name="fonts"></a>字体

## <a name="overview"></a>概述

从 API 级别26开始，Android SDK 允许将字体视为资源，就像布局或绘图一样。 [Android 支持库 26 NuGet](https://www.nuget.org/packages/Xamarin.Android.Support.Compat/26.1.0.1)会将新字体 API 向后移植为面向 API 级别14或更高版本的应用。

面向 API 26 或安装 Android 支持库 v26 后，可通过两种方式在 Android 应用程序中使用字体：

1. 将**字体打包为 Android 资源**&ndash; 这可确保字体对应用程序始终可用，但会增加 APK 的大小。
2. **下载字体**&ndash; Android 还支持从_字体提供程序_下载字体。 字体提供程序检查字体是否已在设备上。 如有必要，将在设备上下载并缓存字体。 可以在多个应用程序之间共享此字体。

类似的字体（或可能有多种不同样式的字体）可以分组为_字体系列_。 这允许开发人员指定字体的某些属性（如其权重），Android 会自动从字体系列中选择相应的字体。

Android 支持库 v26 会将对字体的支持向后移植到 API 级别 26。 如果目标是较旧的 API 级别，则需声明 `app` XML 命名空间，并使用 `android:` 命名空间和 `app:` 命名空间来命名各种字体属性。 如果仅使用 `android:` 命名空间，则字体不会显示在运行 API 25 或更低级别的设备上。 例如，以下 XML 代码片段声明一个新的[字体系列](#font_families)资源，该资源可以在 API 14 及更高级别中使用：

```xml
<?xml version="1.0" encoding="utf-8"?>
<font-family
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto">

     <font  android:font="@font/sourcesanspro_regular"
            android:fontStyle="normal"
            android:fontWeight="400"
            app:font="@font/sourcesanspro_regular"
            app:fontStyle="normal"
            app:fontWeight="400" />

</font-family>
```

只要以正确的方式向 Android 应用程序提供字体，它们就可以通过设置[`fontFamily` 特性](https://developer.android.com/reference/android/widget/TextView.html#attr_android:fontFamily)应用于 UI 小组件。 例如，以下代码片段演示了如何在 TextView 中显示字体：

```xml
<TextView
    android:text="The quick brown fox jumped over the lazy dog."
    android:fontFamily="@font/sourcesanspro_regular"
    app:fontFamily="@font/sourcesanspro_regular"
    android:textAppearance="?android:attr/textAppearanceLarge"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" />
```

本指南将首先讨论如何使用字体作为 Android 资源，然后继续讨论如何在运行时下载字体。

## <a name="fonts-as-a-resource"></a>字体作为资源

将字体打包进 Android APK 中可确保它对应用程序始终可用。 字体文件（.TTF 或 .OTF 文件）可以像任何其他资源一样添加到 Xamarin.Android 应用程序中，只需将文件复制到 Xamarin.Android 项目的 **Resources** 文件夹中的子目录即可。 字体资源保存在项目 **Resources** 文件夹的 **font** 子目录中。

> [!NOTE]
> 字体应具有**AndroidResource**的**生成操作**，否则它们将不会打包到最终的 APK 中。 生成操作应由 IDE 自动设置。

如果有很多类似的字体文件（例如，使用不同的字体或样式的相同字体），可以将它们组合成一个字体系列。

<a name="font_families" />

### <a name="font-families"></a>字体系列

字体系列是一套有不同粗细和样式的字体。 例如，粗体和斜体可能有不同的字体文件。 字体系列由 XML 文件（保留在 **Resources/font** 目录中）中的 `font` 元素定义。 每个字体系列都应该有其自己的 XML 文件。

若要创建字体系列，请首先将所有字体添加到**资源/字体**文件夹。 然后，在字体系列的字体文件夹中创建新的 XML 文件。 XML 文件的名称与所引用的字体没有关联或关系；资源文件可以采用任何合法的 Android 资源文件名称。 此 XML 文件会有一个根 `font-family` 元素，其中包含一个或多个 `font` 元素。 每个 `font` 元素声明一个字体的特性。

以下 XML 是 _Sources Sans Pro_ 字体的字体系列示例，定义多个不同的字体粗细。 它在 **Resources/font** 文件夹中另存为名为 **sourcesanspro.xml** 的文件：

```xml
<?xml version="1.0" encoding="utf-8"?>
<font-family xmlns:android="http://schemas.android.com/apk/res/android"
             xmlns:app="http://schemas.android.com/apk/res-auto">
    <font android:font="@font/sourcesanspro_regular"
          android:fontStyle="normal"
          android:fontWeight="400"
          app:font="@font/sourcesanspro_regular"
          app:fontStyle="normal"
          app:fontWeight="400" />
    <font android:font="@font/sourcesanspro_bold"
          android:fontStyle="normal"
          android:fontWeight="800"
          app:font="@font/sourcesanspro_bold"
          app:fontStyle="normal"
          app:fontWeight="800" />
    <font android:font="@font/sourcesanspro_italic"
          android:fontStyle="italic"
          android:fontWeight="400"
          app:font="@font/sourcesanspro_italic"
          app:fontStyle="italic"
          app:fontWeight="400" />
</font-family>
```

`fontStyle` 属性有两个可能的值：

- **normal** &ndash; 普通字体
- **italic** &ndash; 倾斜字体

`fontWeight` 特性对应于 CSS `font-weight` 特性，是指字体粗细。 值的范围为 100 - 900。 以下列表介绍了常见字体粗细值及其名称：

- **Thin** &ndash; 100
- **Extra Light** &ndash; 200
- **浅**&ndash; 300
- **Normal** &ndash; 400
- **Medium** &ndash; 500
- **Semi Bold** &ndash; 600
- **Bold** &ndash; 700
- **Extra Bold** &ndash; 800
- **Black** &ndash; 900

一旦定义了字体系列，就可以通过设置布局文件中的 `fontFamily`、`textStyle`和 `fontWeight` 属性以声明方式使用。  例如，下面的 XML 代码片段设置600权重字体（普通）和斜体文本样式：

```xml
<TextView
    android:text="Sans Source Pro semi-bold italic, 600 weight, italic"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:fontFamily="@font/sourcesanspro"
    android:textAppearance="?android:attr/textAppearanceLarge"
    android:gravity="center_horizontal"
    android:fontWeight="600"
    android:textStyle="italic"
    />
```

### <a name="programmatically-assigning-fonts"></a>以编程方式分配字体

可以通过使用[`Resources.GetFont`](https://developer.android.com/reference/android/content/res/Resources.html#getFont(int))方法来检索[`Typeface`](https://developer.android.com/reference/android/graphics/Typeface.html)对象，以编程方式设置字体。 许多视图都有 `TypeFace` 属性，可用于将字体分配给小组件。 此代码段演示如何以编程方式在 TextView 上设置字体：

```csharp
Android.Graphics.Typeface typeface = this.Resources.GetFont(Resource.Font.caveat_regular);
textView1.Typeface = typeface;
textView1.Text = "Changed the font";
```

`GetFont` 方法将自动加载字体系列中的第一种字体。  若要加载与特定样式相符的字体，请使用 `Typeface.Create` 方法。 此方法会尝试加载与指定样式相符的字体。 例如，以下代码片段会尝试从 **Resources/font** 中定义的字体系列加载一个粗体 `Typeface` 对象：

```csharp
var typeface = Typeface.Create("<FONT FAMILY NAME>", Android.Graphics.TypefaceStyle.Bold);
textView1.Typeface = typeface;
```

## <a name="downloading-fonts"></a>下载字体

Android 可以从远程源下载字体，而不是将字体打包为应用程序资源。 这将具有减小 APK 大小的理想效果。

使用_字体提供程序_的帮助下载字体。 这是一种专用的内容提供程序，用于管理将字体下载并缓存到设备上的所有应用程序的功能。 Android 8.0 包含用于从[Google 字体存储库](https://fonts.google.com)下载字体的字体提供程序。 此默认字体提供程序与 Android 支持库 v26 的 API 级别14向后移植。

当应用程序请求字体时，字体提供程序将首先检查该字体是否已在设备上。 如果不是，则它将尝试下载字体。 如果无法下载字体，则 Android 将使用默认系统字体。 下载字体后，设备上的所有应用程序都可以使用它，而不只是发出初始请求的应用程序。

当请求下载字体时，应用不会直接查询字体提供程序。 相反，应用程序将使用[`FontsContract`](https://developer.android.com/reference/android/provider/FontsContract.html) API 的实例（如果使用的是支持库26，则使用[`FontsContractCompat`](https://developer.android.com/reference/android/support/v4/provider/FontsContractCompat.html) ）。  

Android 8.0 支持通过两种不同的方式下载字体：

1. **将可下载字体声明为资源**&ndash; 应用可以通过 XML 资源文件将可下载字体声明为 Android。 这些文件将包含 Android 在应用程序启动并缓存到设备上时需要以异步方式下载字体的所有元数据。
2. **以编程方式**在 Android api 级别26中 &ndash; api 允许应用程序在应用程序运行时以编程方式下载字体。 应用程序将创建给定字体的 `FontRequest` 对象，并将此对象传递到 `FontsContract` 类。 `FontsContract` 采用 `FontRequest` 并从_字体提供程序_检索字体。 Android 将同步下载此字体。 本指南稍后将显示创建 `FontRequest` 的示例。

无论使用哪种方法，都必须先将资源文件添加到 Xamarin.Android 应用程序，然后才能下载字体。 首先，必须在 **Resources/font** 目录的 XML 文件中声明字体系列中的字体。 以下代码片段是一个示例，演示了如何使用默认的字体提供程序从 [Google 字体开源集合](https://fonts.google.com)下载字体，该提供程序是 Android 8.0（或支持库 v26）附带的：

```xml
<?xml version="1.0" encoding="utf-8"?>
<font-family xmlns:android="http://schemas.android.com/apk/res/android"
             xmlns:app="http://schemas.android.com/apk/res-auto"
             android:fontProviderAuthority="com.google.android.gms.fonts"
             android:fontProviderPackage="com.google.android.gms"
             android:fontProviderQuery="VT323"
             android:fontProviderCerts="@array/com_google_android_gms_fonts_certs"
             app:fontProviderAuthority="com.google.android.gms.fonts"
             app:fontProviderPackage="com.google.android.gms"
             app:fontProviderQuery="VT323"
             app:fontProviderCerts="@array/com_google_android_gms_fonts_certs"
>
</font-family>
```

`font-family` 元素包含以下特性，并声明 Android 下载字体所需的信息：

1. **fontProviderAuthority** &ndash; 要为请求使用的字体提供程序的颁发机构。
2. **fontPackage** &ndash; 要为请求使用的字体提供程序的包。 这用于验证提供程序的标识。
3. **fontQuery** &ndash; 这是一个字符串，可帮助字体提供程序查找所请求的字体。 有关字体查询的详细信息特定于字体提供程序。 [可下载字体](https://github.com/xamarin/monodroid-samples/blob/master/android-o/DownloadableFonts/)示例应用中的[`QueryBuilder`](https://github.com/xamarin/monodroid-samples/blob/master/android-o/DownloadableFonts/DownloadableFonts/QueryBuilder.cs)类提供了一些有关 Google fonts 开源集合中字体的查询格式的信息。
4. **fontProviderCerts** &ndash; 一个资源数组，其中包含提供程序应使用其签名的证书的哈希集的列表。

一旦定义了字体，就可能需要提供有关下载所涉及的_字体证书_的信息。

### <a name="font-certificates"></a>字体证书

如果字体提供程序没有预装在设备上，或者应用使用的是 `Xamarin.Android.Support.Compat` 库，则 Android 需要字体提供程序的安全证书。 这些证书会列在数组资源文件中，该文件保留在 **Resources/values** 目录中。

例如，以下 XML 命名为**资源/值/fonts_cert .xml** ，并存储 Google 字体提供程序的证书：

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <array name="com_google_android_gms_fonts_certs">
        <item>@array/com_google_android_gms_fonts_certs_dev</item>
        <item>@array/com_google_android_gms_fonts_certs_prod</item>
    </array>
    <string-array name="com_google_android_gms_fonts_certs_dev">
        <item>
            MIIEqDCCA5CgAwIBAgIJANWFuGx90071MA0GCSqGSIb3DQEBBAUAMIGUMQswCQYDVQQGEwJVUzETMBEGA1UECBMKQ2FsaWZvcm5pYTEWMBQGA1UEBxMNTW91bnRhaW4gVmlldzEQMA4GA1UEChMHQW5kcm9pZDEQMA4GA1UECxMHQW5kcm9pZDEQMA4GA1UEAxMHQW5kcm9pZDEiMCAGCSqGSIb3DQEJARYTYW5kcm9pZEBhbmRyb2lkLmNvbTAeFw0wODA0MTUyMzM2NTZaFw0zNTA5MDEyMzM2NTZaMIGUMQswCQYDVQQGEwJVUzETMBEGA1UECBMKQ2FsaWZvcm5pYTEWMBQGA1UEBxMNTW91bnRhaW4gVmlldzEQMA4GA1UEChMHQW5kcm9pZDEQMA4GA1UECxMHQW5kcm9pZDEQMA4GA1UEAxMHQW5kcm9pZDEiMCAGCSqGSIb3DQEJARYTYW5kcm9pZEBhbmRyb2lkLmNvbTCCASAwDQYJKoZIhvcNAQEBBQADggENADCCAQgCggEBANbOLggKv+IxTdGNs8/TGFy0PTP6DHThvbbR24kT9ixcOd9W+EaBPWW+wPPKQmsHxajtWjmQwWfna8mZuSeJS48LIgAZlKkpFeVyxW0qMBujb8X8ETrWy550NaFtI6t9+u7hZeTfHwqNvacKhp1RbE6dBRGWynwMVX8XW8N1+UjFaq6GCJukT4qmpN2afb8sCjUigq0GuMwYXrFVee74bQgLHWGJwPmvmLHC69EH6kWr22ijx4OKXlSIx2xT1AsSHee70w5iDBiK4aph27yH3TxkXy9V89TDdexAcKk/cVHYNnDBapcavl7y0RiQ4biu8ymM8Ga/nmzhRKya6G0cGw8CAQOjgfwwgfkwHQYDVR0OBBYEFI0cxb6VTEM8YYY6FbBMvAPyT+CyMIHJBgNVHSMEgcEwgb6AFI0cxb6VTEM8YYY6FbBMvAPyT+CyoYGapIGXMIGUMQswCQYDVQQGEwJVUzETMBEGA1UECBMKQ2FsaWZvcm5pYTEWMBQGA1UEBxMNTW91bnRhaW4gVmlldzEQMA4GA1UEChMHQW5kcm9pZDEQMA4GA1UECxMHQW5kcm9pZDEQMA4GA1UEAxMHQW5kcm9pZDEiMCAGCSqGSIb3DQEJARYTYW5kcm9pZEBhbmRyb2lkLmNvbYIJANWFuGx90071MAwGA1UdEwQFMAMBAf8wDQYJKoZIhvcNAQEEBQADggEBABnTDPEF+3iSP0wNfdIjIz1AlnrPzgAIHVvXxunW7SBrDhEglQZBbKJEk5kT0mtKoOD1JMrSu1xuTKEBahWRbqHsXclaXjoBADb0kkjVEJu/Lh5hgYZnOjvlba8Ld7HCKePCVePoTJBdI4fvugnL8TsgK05aIskyY0hKI9L8KfqfGTl1lzOv2KoWD0KWwtAWPoGChZxmQ+nBli+gwYMzM1vAkP+aayLe0a1EQimlOalO762r0GXO0ks+UeXde2Z4e+8S/pf7pITEI/tP+MxJTALw9QUWEv9lKTk+jkbqxbsh8nfBUapfKqYn0eidpwq2AzVp3juYl7//fKnaPhJD9gs=
        </item>
    </string-array>
    <string-array name="com_google_android_gms_fonts_certs_prod">
        <item>
            MIIEQzCCAyugAwIBAgIJAMLgh0ZkSjCNMA0GCSqGSIb3DQEBBAUAMHQxCzAJBgNVBAYTAlVTMRMwEQYDVQQIEwpDYWxpZm9ybmlhMRYwFAYDVQQHEw1Nb3VudGFpbiBWaWV3MRQwEgYDVQQKEwtHb29nbGUgSW5jLjEQMA4GA1UECxMHQW5kcm9pZDEQMA4GA1UEAxMHQW5kcm9pZDAeFw0wODA4MjEyMzEzMzRaFw0zNjAxMDcyMzEzMzRaMHQxCzAJBgNVBAYTAlVTMRMwEQYDVQQIEwpDYWxpZm9ybmlhMRYwFAYDVQQHEw1Nb3VudGFpbiBWaWV3MRQwEgYDVQQKEwtHb29nbGUgSW5jLjEQMA4GA1UECxMHQW5kcm9pZDEQMA4GA1UEAxMHQW5kcm9pZDCCASAwDQYJKoZIhvcNAQEBBQADggENADCCAQgCggEBAKtWLgDYO6IIrgqWbxJOKdoR8qtW0I9Y4sypEwPpt1TTcvZApxsdyxMJZ2JORland2qSGT2y5b+3JKkedxiLDmpHpDsz2WCbdxgxRczfey5YZnTJ4VZbH0xqWVW/8lGmPav5xVwnIiJS6HXk+BVKZF+JcWjAsb/GEuq/eFdpuzSqeYTcfi6idkyugwfYwXFU1+5fZKUaRKYCwkkFQVfcAs1fXA5V+++FGfvjJ/CxURaSxaBvGdGDhfXE28LWuT9ozCl5xw4Yq5OGazvV24mZVSoOO0yZ31j7kYvtwYK6NeADwbSxDdJEqO4k//0zOHKrUiGYXtqw/A0LFFtqoZKFjnkCAQOjgdkwgdYwHQYDVR0OBBYEFMd9jMIhF1Ylmn/Tgt9r45jk14alMIGmBgNVHSMEgZ4wgZuAFMd9jMIhF1Ylmn/Tgt9r45jk14aloXikdjB0MQswCQYDVQQGEwJVUzETMBEGA1UECBMKQ2FsaWZvcm5pYTEWMBQGA1UEBxMNTW91bnRhaW4gVmlldzEUMBIGA1UEChMLR29vZ2xlIEluYy4xEDAOBgNVBAsTB0FuZHJvaWQxEDAOBgNVBAMTB0FuZHJvaWSCCQDC4IdGZEowjTAMBgNVHRMEBTADAQH/MA0GCSqGSIb3DQEBBAUAA4IBAQBt0lLO74UwLDYKqs6Tm8/yzKkEu116FmH4rkaymUIE0P9KaMftGlMexFlaYjzmB2OxZyl6euNXEsQH8gjwyxCUKRJNexBiGcCEyj6z+a1fuHHvkiaai+KL8W1EyNmgjmyy8AW7P+LLlkR+ho5zEHatRbM/YAnqGcFh5iZBqpknHf1SKMXFh4dd239FJ1jWYfbMDMy3NS5CTMQ2XFI1MvcyUTdZPErjQfTbQe3aDQsQcafEQPD+nqActifKZ0Np0IS9L9kR/wbNvyz6ENwPiTrjV2KRkEjH78ZMcUQXg0L3BYHJ3lc69Vs5Ddf9uUGGMYldX3WfMBEmh/9iFBDAaTCK
        </item>
    </string-array>
</resources>
```

准备好这些资源文件后，应用就可以下载字体了。

### <a name="declaring-downloadable-fonts-as-resources"></a>将可下载字体声明为资源

通过列出**androidmanifest.xml**中的可下载字体，Android 将在应用首次启动时异步下载字体。 字体本身在数组资源文件中列出，如下所示：

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <array name="downloadable_fonts" translatable="false">
        <item>@font/vt323</item>
    </array>
</resources>
```

若要下载这些字体，必须通过添加 `meta-data` 作为 `application` 元素的子元素，在**androidmanifest.xml**中声明它们。 例如，如果在资源文件的**资源/值/downloadable_fonts**中声明了可下载字体，则必须将此代码段添加到清单中：

```xml
<meta-data android:name="downloadable_fonts" android:resource="@array/downloadable_fonts" />
```

### <a name="downloading-a-font-with-the-font-apis"></a>使用字体 Api 下载字体

可以通过实例化[`FontRequest`](https://developer.android.com/reference/android/support/v4/provider/FontRequest.html)对象并将其传递到 `FontContractCompat.RequestFont` 方法，以编程方式下载字体。 `FontContractCompat.RequestFont` 方法将首先检查设备上是否存在该字体，如果需要，则会以异步方式查询该字体提供程序，并尝试下载该应用的字体。 如果 `FontRequest` 无法下载字体，则 Android 将使用默认系统字体。

`FontRequest` 对象包含字体提供程序查找并下载字体时将使用的信息。 `FontRequest` 需要四个信息片段：

1. **字体提供程序授权**&ndash; 要用于请求的字体提供程序的颁发机构。
2. **Font package** &ndash; 用于请求的字体提供程序的包。 这用于验证提供程序的标识。
3. **Font Query** &ndash; 这是一个字符串，该字符串可帮助字体提供程序查找请求的字体。 有关字体查询的详细信息特定于字体提供程序。 字符串的详细信息特定于字体提供程序。 [可下载字体](https://github.com/xamarin/monodroid-samples/blob/master/android-o/DownloadableFonts/)示例应用中的[`QueryBuilder`](https://github.com/xamarin/monodroid-samples/blob/master/android-o/DownloadableFonts/DownloadableFonts/QueryBuilder.cs)类提供了一些有关 Google fonts 开源集合中字体的查询格式的信息。
4. **字体提供程序证书**&ndash; 一个资源数组，其中包含提供程序应使用其签名的证书的哈希集的列表。

此代码片段是实例化新的 `FontRequest` 对象的一个示例：

```csharp
FontRequest request = new FontRequest("com.google.android.gms.fonts", "com.google.android.gms", <FontToDownload>, Resource.Array.com_google_android_gms_fonts_certs);
```

在前面的代码片段中 `FontToDownload` 是一种查询，它将帮助 Google Fonts 开源集合中的字体。

将 `FontRequest` 传递到 `FontContractCompat.RequestFont` 方法之前，必须创建两个对象：

- **`FontsContractCompat.FontRequestCallback`** &ndash; 这是必须扩展的抽象类。 它是在 `RequestFont` 完成时将调用的回调。 Xamarin Android 应用必须划分 `FontsContractCompat.FontRequestCallback` 并重写 `OnTypefaceRequestFailed` 和 `OnTypefaceRetrieved`的子类，并提供在下载失败或执行失败时要执行的操作。
- **`Handler`** &ndash; 这是 `Handler` 如有必要，`RequestFont` 用来在线程上下载字体。 **不**应在 UI 线程上下载字体。

此代码段是一个C#类的示例，该类将从 Google Fonts 开源集合中异步下载字体。 它实现 `FontRequestCallback` 接口，并在 `FontRequest` 完成C#时引发事件。

```csharp
public class FontDownloadHelper : FontsContractCompat.FontRequestCallback
{
    // A very simple font query; replace as necessary
    public static readonly String FontToDownload = "Courgette";

    Android.OS.Handler Handler = null;

    public event EventHandler<FontDownloadEventArg> FontDownloaded = delegate
    {
        // just an empty delegate to avoid null reference exceptions.  
    };

    public void DownloadFonts(Context context)
    {
        FontRequest request = new FontRequest("com.google.android.gms.fonts", "com.google.android.gms",FontToDownload , Resource.Array.com_google_android_gms_fonts_certs);
        FontsContractCompat.RequestFont(context, request, this, GetHandlerThreadHandler());
    }

    public override void OnTypefaceRequestFailed(int reason)
    {
        base.OnTypefaceRequestFailed(reason);
        FontDownloaded(this, new FontDownloadEventArg(null));
    }

    public override void OnTypefaceRetrieved(Android.Graphics.Typeface typeface)
    {
        base.OnTypefaceRetrieved(typeface);
        FontDownloaded(this, new FontDownloadEventArg(typeface));
    }

    Handler GetHandlerThreadHandler()
    {
        if (Handler == null)
        {
            HandlerThread handlerThread = new HandlerThread("fonts");
            handlerThread.Start();
            Handler = new Handler(handlerThread.Looper);
        }
        return Handler;
    }
}

public class FontDownloadEventArg : EventArgs
{
    public FontDownloadEventArg(Android.Graphics.Typeface typeface)
    {
        Typeface = typeface;
    }
    public Android.Graphics.Typeface Typeface { get; private set; }
    public bool RequestFailed
    {
        get
        {
            return Typeface != null;
        }
    }
}
```

若要使用此帮助器，将创建一个新的 `FontDownloadHelper`，并分配一个事件处理程序：  

```csharp
var fontHelper = new FontDownloadHelper();

fontHelper.FontDownloaded += (object sender, FontDownloadEventArg e) =>
{
    //React to the request
};
fontHelper.DownloadFonts(this); // this is an Android Context instance.
```

## <a name="summary"></a>摘要

本指南讨论 Android 8.0 中的新 Api，以支持可下载字体和字体作为资源。 本文探讨了如何在 APK 中嵌入现有字体并在布局中使用它们。 还介绍了 Android 8.0 如何支持以编程方式或通过在资源文件中声明字体元数据从字体提供程序下载字体。

## <a name="related-links"></a>相关链接

- [fontFamily](https://developer.android.com/reference/android/widget/TextView.html#attr_android:fontFamily)
- [FontConfig](https://developer.android.com/reference/android/text/FontConfig.html)
- [FontRequest](https://developer.android.com/reference/android/support/v4/provider/FontRequest.html)
- [FontsContractCompat](https://developer.android.com/reference/android/support/v4/provider/FontsContractCompat.html)
- [Resources.GetFont](https://developer.android.com/reference/android/content/res/Resources.html#getFont(int))
- [Typeface](https://developer.android.com/reference/android/graphics/Typeface.html)
- [Android 支持库 26 NuGet](https://www.nuget.org/packages/Xamarin.Android.Support.Compat/)
- [在 Android 中使用字体](https://www.youtube.com/watch?v=TfB-TsLFJdM)
- [CSS 字体粗细规范](https://www.w3.org/TR/css-fonts-3/#font-weight-numeric-values)
- [Google 字体打开源集合](https://fonts.google.com/)
- [源 San Pro](https://fonts.google.com/specimen/Source+Sans+Pro)
