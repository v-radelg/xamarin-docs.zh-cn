---
title: 内容提供商的工作方式
ms.prod: xamarin
ms.assetid: B9E2EF89-7EBE-45F5-1ED9-7D2C70BE792C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 64a12f4f797630ad37e5821cd04a14a9d561c53e
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68510683"
---
# <a name="how-content-providers-work"></a>内容提供商的工作方式

`ContentProvider`交互涉及两个类:

- **ContentProvider**&ndash;实现以标准方式公开一组数据的 API。 Main 方法为 Query、Insert、Update 和 Delete。

- **ContentResolver**与通信的静态代理,可从同一应用程序或其他应用程序中访问其数据。`ContentProvider` &ndash;

内容提供商通常由 SQLite 数据库提供支持, 但 API 意味着使用代码并不需要了解有关基础 SQL 的任何信息。 查询通过 Uri 完成, 使用常量引用列名 (以减少基础数据结构上的依赖关系), 并`ICursor`为使用代码循环访问返回。


## <a name="consuming-a-contentprovider"></a>使用 ContentProvider

`ContentProviders`通过在发布数据的应用程序的**androidmanifest.xml**中注册的 Uri 公开其功能。 有一种约定, 其中所公开的 Uri 和数据列应作为常量提供, 以方便绑定到数据。 Android 的内置`ContentProviders`都提供了方便的类, 其中的常量引用[`Android.Providers`](xref:Android.Provider)命名空间中的数据结构。



### <a name="built-in-providers"></a>内置提供程序

Android 提供对各种系统和用户数据的访问, 使用`ContentProviders`:

- *浏览器*书签和浏览器历史记录 ( `READ_HISTORY_BOOKMARKS`需要权限和`WRITE_HISTORY_BOOKMARKS`/或)。 &ndash;

- *CallLog*&ndash;设备最近进行的调用。

- *联系人*&ndash;用户联系人列表中的详细信息, 包括人员、电话、照片 & 组。

- *MediaStore*&ndash;用户设备的内容: 音频 (唱片集、艺术家、流派、播放列表)、图像 (包括缩略图) & 视频。

- *设置*&ndash;系统范围的设备设置和首选项。

- *UserDictionary*&ndash;用于预测文本输入的用户定义字典的内容。

- *语音邮件*&ndash;语音邮件消息的历史记录。



## <a name="classes-overview"></a>类概述

使用时`ContentProvider` , 使用的主要类如下所示:

[![内容提供程序应用程序的类图, 并使用应用程序交互](how-it-works-images/classdiagram1.png)](how-it-works-images/classdiagram1.png#lightbox)

在此关系图中`ContentProvider` , 实现了查询并注册了其他应用程序用来查找数据的 URI。 `ContentResolver`作为`ContentProvider` (查询、插入、更新和删除方法) 的 "代理"。 包含使用的数据, 但它不是直接公开以使用应用。 `ContentProvider` `SQLiteOpenHelper`
传递由返回的光标`ListView` ,以在中显示。`ContentResolver` `CursorAdapter` `UriMatcher`是一个帮助器类, 用于在处理查询时对 uri 进行分析。

每个类的用途如下所述:

- **ContentProvider**&ndash;实现此抽象类的方法以公开数据。 该 API 通过添加到类定义的 Uri 特性可用于其他类和应用程序。

- **SQLiteOpenHelper**有助于实现由公开的 SQLite 数据存储。 `ContentProvider` &ndash;

- **UriMatcher**在您的实现`UriMatcher`中使用来帮助管理用于查询内容的 uri。 `ContentProvider` &ndash;

- **ContentResolver**使用代码使用`ContentProvider`来访问实例。 `ContentResolver` &ndash; 这两个类共同处理进程间通信问题, 使数据能够在应用程序之间轻松共享。 使用代码绝不会创建`ContentProvider`类显式; 而是通过基于`ContentProvider`应用程序所公开的 Uri 创建游标来访问数据。

- **CursorAdapter**&ndash;使用或显示`SimpleCursorAdapter`通过访问的`ContentProvider`数据。 `CursorAdapter`

`ContentProvider` API 允许使用者对数据执行各种操作, 例如:

-  查询数据以返回列表或单个记录。
-  修改各个记录。
-  添加新记录。
-  删除记录。

本文档包含一个示例, 该示例使用系统提供`ContentProvider`的, 以及一个简单的只读示例, 用于实现自定义。 `ContentProvider`

