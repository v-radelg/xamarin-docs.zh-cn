---
title: 应用程序本地化和字符串资源
ms.prod: xamarin
ms.assetid: 374A9DA6-1853-8B98-6954-7FE3F591C07C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/30/2017
ms.openlocfilehash: d9d90e371199c8587d61199240523cf0a23f5efd
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61013196"
---
# <a name="application-localization-and-string-resources"></a>应用程序本地化和字符串资源

应用程序本地化是提供备用资源，以针对某个特定区域设置的操作。 例如，你可能会对于各种国家/地区，提供本地化的语言字符串或可能会更改颜色或布局以匹配特定区域性。 Android 将加载并在运行时无需更改源代码时使用适用于设备的区域设置的资源。

例如下, 图显示了在三个不同的设备区域设置中，运行的相同应用程序，但每个按钮中显示的文本是特定于每个设备设置为的区域设置：

[![三个不同的区域设置的示例](application-localization-images/01-click-me-sml.png)](application-localization-images/01-click-me.png#lightbox)

在此示例中，布局文件的内容**Main.axml**外观如下所示：

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

在上面的示例中，为按钮的字符串从资源加载通过提供的资源 ID 的字符串：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![这三种语言的资源字符串](application-localization-images/02-resource-strings-vs.png)
 
# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![这三种语言的资源字符串](application-localization-images/02-resource-strings-xs.png)
 
-----
 
## <a name="localizing-android-apps"></a>本地化的 Android 应用

读取[简介本地化](~/cross-platform/app-fundamentals/localization.md)的提示和本地化移动应用的指南。

[本地化 Android 应用](~/android/app-fundamentals/localization.md)指南包含有关如何将字符串翻译和本地化使用 Xamarin.Android 的映像更具体的示例。



## <a name="related-links"></a>相关链接

- [本地化的 Android 应用](~/android/app-fundamentals/localization.md)
- [跨平台本地化概述](~/cross-platform/app-fundamentals/localization.md)
