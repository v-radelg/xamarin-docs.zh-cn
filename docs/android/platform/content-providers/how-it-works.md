---
title: 内容提供商的工作方式
ms.prod: xamarin
ms.assetid: B9E2EF89-7EBE-45F5-1ED9-7D2C70BE792C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: e61be6f0189eb825c15fd75764a16706e588ebc9
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73020520"
---
# <a name="how-content-providers-work"></a>内容提供商的工作方式

`ContentProvider` 交互涉及两个类：

- **ContentProvider** &ndash; 实现以标准方式公开一组数据的 API。 Main 方法为 Query、Insert、Update 和 Delete。

- **ContentResolver** &ndash; 与 `ContentProvider` 通信的静态代理，可从同一应用程序或其他应用程序访问其数据。

内容提供商通常由 SQLite 数据库提供支持，但 API 意味着使用代码并不需要了解有关基础 SQL 的任何信息。 查询是通过 Uri 使用常量来引用列名（以减少对基础数据结构的依赖关系），并为使用代码循环访问返回 `ICursor`。

## <a name="consuming-a-contentprovider"></a>使用 ContentProvider

`ContentProviders` 通过在发布数据的应用程序的**androidmanifest.xml**中注册的 Uri 公开其功能。 有一种约定，其中所公开的 Uri 和数据列应作为常量提供，以方便绑定到数据。 Android 的内置 `ContentProviders` 都提供了方便的类，其中包含引用[`Android.Providers`](xref:Android.Provider)命名空间中的数据结构的常量。

### <a name="built-in-providers"></a>内置提供程序

Android 通过 `ContentProviders`提供对各种系统和用户数据的访问权限：

- *浏览器*&ndash; 书签和浏览器历史记录（需要权限 `READ_HISTORY_BOOKMARKS` 和/或 `WRITE_HISTORY_BOOKMARKS`）。

- *CallLog* &ndash; 设备发出或收到的最近调用。

- *联系人*&ndash; 用户联系人列表中的详细信息，包括人员、电话、照片 & 组。

- 用户设备的*MediaStore* &ndash; 内容：音频（唱片集、艺术家、流派、播放列表）、图像（包括缩略图） & 视频。

- *设置*&ndash; 系统范围的设备设置和首选项。

- *UserDictionary* &ndash; 用于预测文本输入的用户定义字典的内容。

- *语音邮件 &ndash; 语音*邮件的历史记录。

## <a name="classes-overview"></a>类概述

使用 `ContentProvider` 时使用的主要类如下所示：

[内容提供程序应用程序的![类关系图，并使用应用程序交互](how-it-works-images/classdiagram1.png)](how-it-works-images/classdiagram1.png#lightbox)

在此关系图中，`ContentProvider` 实现了查询并注册了其他应用程序用于查找数据的 URI。 `ContentResolver` 充当 `ContentProvider` （查询、插入、更新和删除方法）的 "代理"。 `SQLiteOpenHelper` 包含 `ContentProvider`所使用的数据，但它不是直接公开以使用应用。
`CursorAdapter` 传递 `ContentResolver` 返回的光标以在 `ListView`中显示。 `UriMatcher` 是在处理查询时解析 Uri 的帮助器类。

每个类的用途如下所述：

- **ContentProvider** &ndash; 实现此抽象类的方法来公开数据。 该 API 通过添加到类定义的 Uri 特性可用于其他类和应用程序。

- **SQLiteOpenHelper** &ndash; 有助于实现 `ContentProvider`公开的 SQLite 数据存储。

- **UriMatcher** &ndash; 在 `ContentProvider` 实现中使用 `UriMatcher` 来帮助管理用于查询内容的 uri。

- 使用代码的**ContentResolver** &ndash; 使用 `ContentResolver` 访问 `ContentProvider` 的实例。 这两个类共同处理进程间通信问题，使数据能够在应用程序之间轻松共享。 使用代码永远不会创建 `ContentProvider` 类显式;相反，可以通过基于 `ContentProvider` 应用程序公开的 Uri 创建游标来访问数据。

- **CursorAdapter** &ndash; 使用 `CursorAdapter` 或 `SimpleCursorAdapter` 显示通过 `ContentProvider`访问的数据。

使用 `ContentProvider` API，使用者可以对数据执行各种操作，例如：

- 查询数据以返回列表或单个记录。
- 修改各个记录。
- 添加新记录。
- 删除记录。

本文档包含一个示例，该示例使用系统提供的 `ContentProvider`，以及一个简单的只读示例，用于实现自定义 `ContentProvider`。
