---
title: Contentprovider 简介
description: Android 操作系统使用内容提供程序来帮助访问共享数据，如媒体文件、联系人和日历信息。 本文介绍了 ContentProvider 类，并提供了两个如何使用它的示例。
ms.prod: xamarin
ms.assetid: 6E1810AA-EB70-9AD0-1B32-D9418908CC97
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: 496e5c092c79f4f71bddaad30bea6acd1d58d375
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027544"
---
# <a name="intro-to-contentproviders"></a>Contentprovider 简介

_Android 操作系统使用内容提供程序来帮助访问共享数据，如媒体文件、联系人和日历信息。本文介绍了 ContentProvider 类，并提供了两个如何使用它的示例。_

## <a name="content-providers-overview"></a>内容提供程序概述

*ContentProvider*封装数据存储库，并提供 API 来访问它。 提供程序作为 Android 应用程序的一部分存在，通常还提供用于显示/管理数据的 UI。 使用内容提供程序的主要好处是使其他应用程序可以使用提供程序客户端对象（称为*ContentResolver*）轻松访问封装的数据。 内容提供商和内容解析器共同提供了一个用于数据访问的一致的应用程序间 API，可轻松生成和使用。 任何应用程序都可以选择使用 `ContentProviders` 来管理内部数据，还可以将数据公开给其他应用程序。

您的应用程序也需要 `ContentProvider` 提供自定义搜索建议，或者，如果您希望能够将复杂数据从您的应用程序复制到其他应用程序，则也是如此。 本文档演示如何使用 Xamarin 访问和生成 `ContentProviders`。

本部分的结构如下所示：

- **它的工作原理**&ndash; 对 `ContentProvider` 的设计及其工作原理的概述。

- **使用内容提供程序**&ndash; 访问联系人列表的示例。

- **使用 ContentProvider 共享数据**&ndash; 在同一应用程序中写入和使用 `ContentProvider`。

`ContentProviders` 和操作数据的游标通常用于填充 Listview。 有关如何使用这些类的详细信息，请参阅[listview 和适配器指南](~/android/user-interface/layouts/list-view/index.md)。

Android （或其他应用程序）公开的 `ContentProviders` 是在应用程序中包含来自其他源的数据的一种简单方法。 它们允许您访问和显示应用程序中的联系人列表、照片或日历事件等数据，并让用户与这些数据进行交互。

自定义 `ContentProviders` 是将数据打包以便在自己的应用程序中使用或供其他应用程序（包括自定义搜索和复制/粘贴等特殊使用）使用的一种简便方法。

本节中的主题提供了一些使用和编写 `ContentProvider` 代码的简单示例。

## <a name="related-links"></a>相关链接

- [ContactsAdapter 演示（示例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-contactsadapterdemo)
- [SimpleContentProvider （示例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-simplecontentprovider)
- [内容提供商开发人员指南](https://developer.android.com/guide/topics/providers/content-providers.html)
- [ContentProvider 类引用](xref:Android.Content.ContentProvider)
- [ContentResolver 类引用](xref:Android.Content.ContentResolver)
- [ListView 类引用](xref:Android.Widget.ListView)
- [CursorAdapter 类引用](xref:Android.Widget.CursorAdapter)
- [UriMatcher 类引用](xref:Android.Content.UriMatcher)
- [Android. 提供程序](xref:Android.Provider)
- [ContactsContract 类引用](xref:Android.Provider.ContactsContract)
