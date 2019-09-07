---
title: 应用程序本地化和字符串资源
ms.prod: xamarin
ms.assetid: 374A9DA6-1853-8B98-6954-7FE3F591C07C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/30/2017
ms.openlocfilehash: 45fe5c783e737fb913730082841e0dfafc555684
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70755141"
---
# <a name="application-localization-and-string-resources"></a>应用程序本地化和字符串资源

应用程序本地化是提供备用资源以面向特定区域或区域设置的操作。 例如，您可以为各个国家/地区提供本地化的语言字符串，也可以更改颜色或布局以与特定的区域性匹配。 在运行时，Android 将加载并使用适用于设备区域设置的资源，而无需更改源代码。

例如，下图显示了在三个不同的设备区域设置中运行的同一应用程序，但每个按钮中显示的文本特定于每个设备设置为的区域设置：

[![三个不同区域设置的示例](application-localization-images/01-click-me-sml.png)](application-localization-images/01-click-me.png#lightbox)

在此示例中，布局文件的内容**main.axml**如下所示：

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
   android:orientation="vertical"
   android:layout_width="fill_parent"
   android:layout_height="fill_parent"
   >
<Button  
   android:id="@+id/myButton"
   android:layout_width="fill_parent"
   android:layout_height="wrap_content"
android:text="@string/hello"
   />
</LinearLayout>
```

在上面的示例中，通过提供字符串的资源 ID 从资源中加载了该按钮的字符串：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![适用于三种语言的资源字符串](application-localization-images/02-resource-strings-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![适用于三种语言的资源字符串](application-localization-images/02-resource-strings-xs.png)

-----

## <a name="localizing-android-apps"></a>本地化 Android 应用

阅读[本地化简介](~/cross-platform/app-fundamentals/localization.md)，了解有关本地化移动应用程序的提示和指南。

[本地化 Android 应用](~/android/app-fundamentals/localization.md)指南包含有关如何使用 Xamarin 转换字符串和本地化映像的更具体的示例。

## <a name="related-links"></a>相关链接

- [本地化 Android 应用](~/android/app-fundamentals/localization.md)
- [跨平台本地化概述](~/cross-platform/app-fundamentals/localization.md)
