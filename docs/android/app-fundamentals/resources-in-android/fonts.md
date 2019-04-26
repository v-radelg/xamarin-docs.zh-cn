---
title: 字体
ms.prod: xamarin
ms.assetid: 3F543FC5-FDED-47F8-8D2C-481FCC98BFDA
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 09/09/2018
ms.openlocfilehash: c7953748e79bd43bc14601c1f0ea05d1a36adf08
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61013395"
---
# <a name="fonts"></a>字体

## <a name="overview"></a>概述

从 API 级别 26 开始，Android SDK 允许字体作为资源，就像布局一样来处理或绘图。 [Android 支持库 26 NuGet](https://www.nuget.org/packages/Xamarin.Android.Support.Compat/26.1.0.1)将向后移植到应用程序的目标 API 级别 14 或更高版本的新字体 API 的。

针对 API 26 或之后安装 Android 支持库 v26，有两种方法中的 Android 应用程序使用的字体：

1. **打包为 Android 资源字体**&ndash;这可确保字体始终可供该应用程序，但会增加 APK 的大小。
2. **下载字体** &ndash; Android 还支持下载从字体_字体提供程序_。 字体提供程序会检查该字体是否已在设备上。 如有必要，将下载并缓存在设备上的字体。 此字体可以在多个应用程序之间共享。

相似的字体 （或可能有几种不同样式的字体） 可能会分组到_字体系列_。 这允许开发人员指定的字体，例如它的权重，某些属性和 Android 将自动选择合适的字体的字体系列。

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

只要字体以正确方式提供给 Android 应用程序，它们可应用于 UI 小组件通过设置[`fontFamily`属性](https://developer.android.com/reference/android/widget/TextView.html#attr_android:fontFamily)。 例如，以下代码片段演示了如何在一个 TextView 中显示字体：

```xml
<TextView
    android:text="The quick brown fox jumped over the lazy dog."
    android:fontFamily="@font/sourcesanspro_regular"
    app:fontFamily="@font/sourcesanspro_regular"
    android:textAppearance="?android:attr/textAppearanceLarge"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" />
```

本指南将首先讨论如何使用字体为 Android 资源，并接着讨论如何在运行时的字体下载。

## <a name="fonts-as-a-resource"></a>字体作为资源

将字体打包进 Android APK 中可确保它对应用程序始终可用。 字体文件（.TTF 或 .OTF 文件）可以像任何其他资源一样添加到 Xamarin.Android 应用程序中，只需将文件复制到 Xamarin.Android 项目的 **Resources** 文件夹中的子目录即可。 字体资源保存在项目 **Resources** 文件夹的 **font** 子目录中。

> [!NOTE]
> 字体应有**生成操作**的**AndroidResource**或它们将不打包到最终 APK。 生成操作应由 IDE 自动设置。

有许多类似字体文件 （例如，使用不同的权重或样式相同字体） 时，可能将它们分组到一个字体系列。

<a name="font_families" />

### <a name="font-families"></a>字体系列

字体系列是一套有不同粗细和样式的字体。 例如，粗体和斜体可能有不同的字体文件。 字体系列由 XML 文件（保留在 **Resources/font** 目录中）中的 `font` 元素定义。 每个字体系列都应该有其自己的 XML 文件。

若要创建字体系列，请首先添加到的所有字体**资源/字体**文件夹。 然后，在字体系列的字体文件夹中创建新的 XML 文件。 XML 文件的名称与所引用的字体没有关联或关系；资源文件可以采用任何合法的 Android 资源文件名称。 此 XML 文件会有一个根 `font-family` 元素，其中包含一个或多个 `font` 元素。 每个 `font` 元素声明一个字体的特性。

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

`fontStyle`属性具有两个可能值：

* **normal** &ndash; 普通字体
* **italic** &ndash; 倾斜字体

`fontWeight` 特性对应于 CSS `font-weight` 特性，是指字体粗细。 值的范围为 100 - 900。 以下列表介绍了常见字体粗细值及其名称：

* **Thin** &ndash; 100
* **Extra Light** &ndash; 200
* **Light** &ndash; 300
* **Normal** &ndash; 400
* **Medium** &ndash; 500
* **Semi Bold** &ndash; 600
* **Bold** &ndash; 700
* **Extra Bold** &ndash; 800
* **Black** &ndash; 900

一旦定义字体系列，它可以是以声明方式使用通过设置`fontFamily`， `textStyle`，和`fontWeight`布局文件中的属性。  例如 600 权重字体 （普通） 和斜体文本样式，将设置以下 XML 代码片段：

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

### <a name="programmatically-assigning-fonts"></a>以编程方式指定字体

可以通过使用以编程方式设置字体[ `Resources.GetFont` ](https://developer.android.com/reference/android/content/res/Resources.html#getFont(int))方法来检索[ `Typeface` ](https://developer.android.com/reference/android/graphics/Typeface.html)对象。 许多视图具有`TypeFace`可用于将字体分配给该小组件的属性。 此代码片段演示如何以编程方式将字体设置上一个 TextView:

```csharp
Android.Graphics.Typeface typeface = this.Resources.GetFont(Resource.Font.caveat_regular);
textView1.Typeface = typeface;
textView1.Text = "Changed the font";
```

`GetFont`方法将自动加载字体系列内的第一种字体。  若要加载与特定样式相符的字体，请使用 `Typeface.Create` 方法。 此方法会尝试加载与指定样式相符的字体。 例如，以下代码片段会尝试从 **Resources/font** 中定义的字体系列加载一个粗体 `Typeface` 对象：

```csharp
var typeface = Typeface.Create("<FONT FAMILY NAME>", Android.Graphics.TypefaceStyle.Bold);
textView1.Typeface = typeface;
```

## <a name="downloading-fonts"></a>下载字体

而不是将字体打包为应用程序资源，Android 可以从远程源下载字体。 这会减少 APK 的大小的理想效果。

字体下载的帮助下_字体提供程序_。 这是专用的内容提供程序管理的设备上的所有应用程序的字体下载和缓存。 Android 8.0 包括字体提供程序来下载从字体[Google 字体存储库](http://fonts.google.com)。 此默认字体提供程序是向后的移植到使用 Android 支持库 v26 API 级别 14。

当应用程序发出请求的一种字体时，字体提供程序将首先检查以查看该字体是否已在设备上。 如果没有，则它将尝试下载字体。 如果字体不能为已下载，然后 Android 将使用默认系统字体。 一旦下载字体，可供所有应用程序在设备上，而不仅仅是发出初始请求的应用。

当发出请求以下载字体时，应用程序不会直接查询字体提供程序。 相反，应用将使用的实例[ `FontsContract` ](https://developer.android.com/reference/android/provider/FontsContract.html) API (或[ `FontsContractCompat` ](https://developer.android.com/reference/android/support/v4/provider/FontsContractCompat.html)如果正在使用支持库 26)。  

Android 8.0 支持两种不同方式下载字体：

1. **声明为资源的可下载字体**&ndash;应用可能会声明到 Android 通过 XML 资源文件的可下载字体。 这些文件将包含的所有 Android 需要字体时应用启动并在设备上对其进行缓存以异步方式下载的元数据。
2. **以编程方式** &ndash; Android API 级别 26 中的 Api 允许应用程序时运行该应用程序，以编程方式下载字体。 应用程序将创建`FontRequest`对象为给定的字体，并将传递到此对象`FontsContract`类。 `FontsContract`采用`FontRequest`，并检索从字体_字体提供程序_。 Android 将以同步方式下载该字体。 创建的示例`FontRequest`将在本指南后面所示。

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

`font-family`元素包含以下属性，声明 Android 需要下载字体的信息：

1. **fontProviderAuthority** &ndash;字体提供程序颁发机构，用于进行请求。
2. **fontPackage** &ndash;请求使用的字体提供程序的包。 这用于验证提供程序的标识。
3. **fontQuery** &ndash;这是一个字符串，将帮助定位所请求的字体的字体提供程序。 字体查询的详细信息是特定于字体提供程序。 [ `QueryBuilder` ](https://github.com/xamarin/monodroid-samples/blob/master/android-o/DownloadableFonts/DownloadableFonts/QueryBuilder.cs)类[可下载字体](https://github.com/xamarin/monodroid-samples/blob/master/android-o/DownloadableFonts/)示例应用程序提供了一些有关查询格式对于字体 Google 字体开放源集合中。
4. **fontProviderCerts** &ndash;的提供程序应使用签名的证书的哈希集的列表的资源数组。

字体定义后，可能需要提供有关的信息_字体证书_涉及的下载。

### <a name="font-certificates"></a>字体证书

如果字体提供程序没有预装在设备上，或者应用使用的是 `Xamarin.Android.Support.Compat` 库，则 Android 需要字体提供程序的安全证书。 这些证书会列在数组资源文件中，该文件保留在 **Resources/values** 目录中。

例如，名为下面的 XML **Resources/values/fonts_cert.xml** ，并将证书存储 Google 字体提供程序：

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

这些资源中的文件的位置，该应用是能够下载字体。

### <a name="declaring-downloadable-fonts-as-resources"></a>声明可下载字体作为资源

通过列出中的可下载字体**AndroidManifest.XML**，Android 将应用程序首次启动时以异步方式下载字体。 该字体的本身列出在数组资源文件中，与以下类似：

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <array name="downloadable_fonts" translatable="false">
        <item>@font/vt323</item>
    </array>
</resources>
```

若要下载这些字体，他们需要在中声明**AndroidManifest.XML**通过添加`meta-data`的小孩`application`元素。 例如，如果在一个资源文件中声明的可下载字体**Resources/values/downloadable_fonts.xml**，则必须添加到清单中此代码片段：

```xml
<meta-data android:name="downloadable_fonts" android:resource="@array/downloadable_fonts" />
```

### <a name="downloading-a-font-with-the-font-apis"></a>下载字体具有字体 Api

可以以编程方式通过实例化下载字体[ `FontRequest` ](https://developer.android.com/reference/android/support/v4/provider/FontRequest.html)对象并传递给`FontContractCompat.RequestFont`方法。 `FontContractCompat.RequestFont`方法会首先检查以查看在设备上是否存在该字体，然后如有必要将以异步方式查询字体提供程序并尝试下载应用程序的字体。 如果`FontRequest`是无法下载字体，则 Android 将使用默认系统字体。

一个`FontRequest`对象包含将使用的字体提供程序查找和下载字体信息。 一个`FontRequest`需要四个条信息：

1. **字体提供程序颁发机构**&ndash;字体提供程序颁发机构，用于进行请求。
2. **字体包**&ndash;请求使用的字体提供程序的包。 这用于验证提供程序的标识。
3. **字体查询**&ndash;这是一个字符串，将帮助定位所请求的字体的字体提供程序。 字体查询的详细信息是特定于字体提供程序。 字符串的详细信息是特定于字体提供程序。 [ `QueryBuilder` ](https://github.com/xamarin/monodroid-samples/blob/master/android-o/DownloadableFonts/DownloadableFonts/QueryBuilder.cs)类[可下载字体](https://github.com/xamarin/monodroid-samples/blob/master/android-o/DownloadableFonts/)示例应用程序提供了一些有关查询格式对于字体 Google 字体开放源集合中。
4. **字体提供程序证书**&ndash;的提供程序应使用签名的证书的哈希集的列表的资源数组。

此代码片段示范了实例化一个新的`FontRequest`对象：

```csharp
FontRequest request = new FontRequest("com.google.android.gms.fonts", "com.google.android.gms", <FontToDownload>, Resource.Array.com_google_android_gms_fonts_certs);
```

在上一代码段`FontToDownload`是一个查询，可帮助在 Google 字体开放源集合中的字体。

然后才传递`FontRequest`到`FontContractCompat.RequestFont`方法，必须创建的两个对象：

* **`FontsContractCompat.FontRequestCallback`** &ndash; 这是一个抽象类，后者必须扩展。 它是将一个回调时调用`RequestFont`已完成。 Xamarin.Android 应用程序必须子类`FontsContractCompat.FontRequestCallback`并重写`OnTypefaceRequestFailed`和`OnTypefaceRetrieved`，提供时下载失败或成功分别执行的操作。
* **`Handler`** &ndash; 这是`Handler`其将通过使用`RequestFont`下载在线程上的字体，如有必要。 字体应**不**UI 线程上下载。

此代码片段示范了C#以异步方式将一种字体下载 Google 字体开放源集合中的类。 它实现`FontRequestCallback`接口，并引发C#事件时`FontRequest`已完成。

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

若要使用此帮助器，一个新`FontDownloadHelper`创建和分配事件处理程序：  

```csharp
var fontHelper = new FontDownloadHelper();

fontHelper.FontDownloaded += (object sender, FontDownloadEventArg e) =>
{
    //React to the request
};
fontHelper.DownloadFonts(this); // this is an Android Context instance.
```

## <a name="summary"></a>总结

本指南介绍 Android 8.0 支持可下载的字体和字体作为资源中的新 Api。 它介绍了如何将现有的字体嵌入在 APK 中以及在布局中使用它们。 它还将讨论如何 Android 8.0 支持下载字体从字体提供程序，以编程方式或通过声明字体元数据资源文件中。

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
- [Google 字体开放源集合](https://fonts.google.com/)
- [San Pro 源](https://fonts.google.com/specimen/Source+Sans+Pro)
