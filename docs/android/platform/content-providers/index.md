---
title: 简介 Contentprovider
description: Android 操作系统使用的内容提供商来方便地对媒体文件、 联系人和日历信息等的共享数据的访问。 本文介绍 ContentProvider 类，并提供如何使用它的两个示例。
ms.prod: xamarin
ms.assetid: 6E1810AA-EB70-9AD0-1B32-D9418908CC97
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: 60f9420fc630bcfc44a6b19356b2315ac8bcd523
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60951780"
---
# <a name="intro-to-contentproviders"></a>简介 Contentprovider

_Android 操作系统使用的内容提供商来方便地对媒体文件、 联系人和日历信息等的共享数据的访问。本文介绍 ContentProvider 类，并提供如何使用它的两个示例。_


## <a name="content-providers-overview"></a>内容提供程序概述

一个*ContentProvider*封装的数据存储库，并提供一个 API 来访问它。 提供程序存在通常还提供一个 UI 用于显示/管理数据的 Android 应用程序的一部分。 使用内容提供商的主要好处，让其他应用程序能够轻松地访问封装的数据使用的提供程序客户端对象 (称为*ContentResolver*)。 在一起，内容提供商和内容冲突解决程序提供一致的应用程序间 API 进行生成和使用简单的数据访问。 任何应用程序可以选择使用`ContentProviders`在内部管理数据以及将其公开给其他应用程序。

一个`ContentProvider`，还需要为应用程序提供自定义搜索建议，或如果你想要提供应用程序以粘贴到其他应用程序中的复杂数据复制的功能。 本文档演示如何访问和生成`ContentProviders`使用 Xamarin.Android。

本部分的结构如下所示：

- **其工作原理**&ndash;的内容概述`ContentProvider`旨在为，以及如何配合工作。

- **使用内容提供商**&ndash;示例： 访问联系人列表。

- **使用 ContentProvider 共享数据**&ndash;编写和使用方`ContentProvider`在同一应用程序。

`ContentProviders` 并对其数据的游标通常用于填充 Listview。 请参阅[Listview 和适配器指南](~/android/user-interface/layouts/list-view/index.md)有关如何使用这些类的详细信息。

`ContentProviders` 公开的 Android （或其他应用程序），可以轻松在应用程序中包括来自其他源的数据。 它们使你能够访问和呈现数据，如联系人列表、 照片或从应用程序中的日历事件，让用户使用该数据进行交互。

自定义`ContentProviders`是由其他应用程序 （包括自定义搜索和复制/粘贴等特殊用途） 打包你的数据在你自己的应用，内部使用或供使用的简便方法。

在本部分中的主题提供使用和编写一些简单示例`ContentProvider`代码。



## <a name="related-links"></a>相关链接

- [ContactsAdapter 演示 （示例）](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/ContactsAdapterDemo/)
- [SimpleContentProvider （示例）](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/SimpleContentProvider)
- [内容提供程序开发人员指南](https://developer.android.com/guide/topics/providers/content-providers.html)
- [ContentProvider 类引用](https://developer.xamarin.com/api/type/Android.Content.ContentProvider/)
- [ContentResolver 类引用](https://developer.xamarin.com/api/type/Android.Content.ContentResolver/)
- [ListView 类引用](https://developer.xamarin.com/api/type/Android.Widget.ListView/)
- [CursorAdapter 类引用](https://developer.xamarin.com/api/type/Android.Widget.CursorAdapter/)
- [UriMatcher 类引用](https://developer.xamarin.com/api/type/Android.Content.UriMatcher/)
- [Android.Provider](https://developer.xamarin.com/api/namespace/Android.Provider/)
- [ContactsContract 类引用](https://developer.xamarin.com/api/type/Android.Provider.ContactsContract/)
