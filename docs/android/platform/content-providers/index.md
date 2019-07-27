---
title: Contentprovider 简介
description: Android 操作系统使用内容提供程序来帮助访问共享数据, 如媒体文件、联系人和日历信息。 本文介绍了 ContentProvider 类, 并提供了两个如何使用它的示例。
ms.prod: xamarin
ms.assetid: 6E1810AA-EB70-9AD0-1B32-D9418908CC97
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: 1e62dc32e9764667cb8737167a49bcc9a4516f0f
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68510627"
---
# <a name="intro-to-contentproviders"></a>Contentprovider 简介

_Android 操作系统使用内容提供程序来帮助访问共享数据, 如媒体文件、联系人和日历信息。本文介绍了 ContentProvider 类, 并提供了两个如何使用它的示例。_


## <a name="content-providers-overview"></a>内容提供程序概述

*ContentProvider*封装数据存储库, 并提供 API 来访问它。 提供程序作为 Android 应用程序的一部分存在, 通常还提供用于显示/管理数据的 UI。 使用内容提供程序的主要好处是使其他应用程序可以使用提供程序客户端对象 (称为*ContentResolver*) 轻松访问封装的数据。 内容提供商和内容解析器共同提供了一个用于数据访问的一致的应用程序间 API, 可轻松生成和使用。 任何应用程序都可以选择`ContentProviders`使用来管理内部数据, 还可以将数据公开给其他应用程序。

`ContentProvider`您的应用程序也需要提供自定义搜索建议, 或者您希望能够将复杂数据从您的应用程序复制到其他应用程序。 本文档演示如何使用 Xamarin 进行访问`ContentProviders`和生成。

本部分的结构如下所示:

- **工作原理**概述设计的`ContentProvider`内容及其工作原理。 &ndash;

- **使用内容提供程序**&ndash;访问联系人列表的示例。

- **使用 ContentProvider 共享数据**在同一`ContentProvider`应用程序中写入和使用。 &ndash;

`ContentProviders`和对其数据进行操作的游标通常用于填充 Listview。 有关如何使用这些类的详细信息, 请参阅[listview 和适配器指南](~/android/user-interface/layouts/list-view/index.md)。

`ContentProviders`由 Android (或其他应用程序) 公开是一种在应用程序中包含来自其他源的数据的简单方法。 它们允许您访问和显示应用程序中的联系人列表、照片或日历事件等数据, 并让用户与这些数据进行交互。

自`ContentProviders`定义是将数据打包以便在自己的应用程序中使用或供其他应用程序 (包括自定义搜索和复制/粘贴等特殊使用) 使用的一种简便方法。

本节中的主题提供了一些使用和编写`ContentProvider`代码的简单示例。



## <a name="related-links"></a>相关链接

- [ContactsAdapter 演示 (示例)](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/ContactsAdapterDemo/)
- [SimpleContentProvider (示例)](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/SimpleContentProvider)
- [内容提供商开发人员指南](https://developer.android.com/guide/topics/providers/content-providers.html)
- [ContentProvider 类引用](xref:Android.Content.ContentProvider)
- [ContentResolver 类引用](xref:Android.Content.ContentResolver)
- [ListView 类引用](xref:Android.Widget.ListView)
- [CursorAdapter 类引用](xref:Android.Widget.CursorAdapter)
- [UriMatcher 类引用](xref:Android.Content.UriMatcher)
- [Android.Provider](xref:Android.Provider)
- [ContactsContract 类引用](xref:Android.Provider.ContactsContract)
