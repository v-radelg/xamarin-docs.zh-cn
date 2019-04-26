---
title: 如何在内容提供程序的效果
ms.prod: xamarin
ms.assetid: B9E2EF89-7EBE-45F5-1ED9-7D2C70BE792C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: df4c2e10e34c308e4fadb44fba9c6a14714ae1b9
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60952844"
---
# <a name="how-content-providers-work"></a>如何在内容提供程序的效果

有两个类中所涉及`ContentProvider`交互：

- **ContentProvider** &ndash;实现以标准方式公开一组数据的 API。 主要方法为查询、 Insert、 Update 和 Delete。

- **ContentResolver** &ndash;与通信的静态代理`ContentProvider`来访问其数据，或者从同一应用程序内或从另一个应用程序。

内容提供商通常由一个 SQLite 数据库，但 API，则意味着，使用代码不需要知道有关基础 SQL 的任何信息。 查询都是通过 Uri 使用常量引用列名称 （若要减少对基础数据结构的依赖项），和一个`ICursor`返回有关使用代码来循环访问。


## <a name="consuming-a-contentprovider"></a>使用 ContentProvider

`ContentProviders` 公开其功能通过在中注册的 Uri **AndroidManifest.xml**的应用程序的发布的数据。 是一种约定，Uri 和公开的数据列应该可用作常量来轻松地绑定到数据。 Android 的内置`ContentProviders`所有提供方便的类具有引用中的数据结构的常量[ `Android.Providers` ](https://developer.xamarin.com/api/namespace/Android.Provider/)命名空间。



### <a name="built-in-providers"></a>内置提供程序

Android 提供访问各种系统和用户数据使用`ContentProviders`:

- *浏览器*&ndash;书签和浏览器历史记录 (需要权限`READ_HISTORY_BOOKMARKS`和/或`WRITE_HISTORY_BOOKMARKS`)。

- *CallLog* &ndash;还是与设备接收了最新的调用。

- *联系人*&ndash;详细的用户的联系人列表，其中包括人员、 手机、 照片和组中的信息。

- *MediaStore* &ndash;用户的设备的内容： 音频 （相册、 艺术家、 流派、 播放列表）、 （包括缩略图） 的图像和视频。

- *设置*&ndash;系统范围内的设备设置和首选项。

- *UserDictionary* &ndash;用于预测文本输入的用户定义的字典的内容。

- *语音邮件*&ndash;语音邮件消息的历史记录。



## <a name="classes-overview"></a>类概述

处理时使用的主要类`ContentProvider`如下所示：

[![内容提供商应用程序和使用应用程序交互的类图](how-it-works-images/classdiagram1.png)](how-it-works-images/classdiagram1.png#lightbox)

在此图中，`ContentProvider`实现查询，并注册 URI 的其他应用程序用来查找数据。 `ContentResolver`充当到的代理`ContentProvider`（查询、 插入、 更新和删除方法）。 `SQLiteOpenHelper`包含使用的数据`ContentProvider`，但系统不直接公开给使用应用。
`CursorAdapter`传递返回的游标`ContentResolver`中显示`ListView`。 `UriMatcher`是处理查询时分析 Uri 的帮助器类。

每个类的用途如下所述：

- **ContentProvider** &ndash;实现此抽象类的方法来公开数据。 该 API 可供其他类和应用程序中通过添加到类定义的 Uri 属性。

- **SQLiteOpenHelper** &ndash;可帮助实现由公开的 SQLite 数据存储`ContentProvider`。

- **UriMatcher** &ndash;使用`UriMatcher`中你`ContentProvider`实现，以帮助管理用于查询内容的 Uri。

- **ContentResolver** &ndash;使用代码使用`ContentResolver`访问`ContentProvider`实例。 两个类一起进程间通信问题，从而允许应用程序之间轻松共享数据处理。 使用代码永远不会创建`ContentProvider`类显式; 相反，数据访问通过创建基于公开的 Uri 的游标`ContentProvider`应用程序。

- **CursorAdapter** &ndash;使用`CursorAdapter`或`SimpleCursorAdapter`以显示数据通过访问`ContentProvider`。

`ContentProvider` API 允许使用者执行各种操作的数据，例如：

-  查询数据，以返回列表或单个记录。
-  修改单个记录。
-  添加新记录。
-  删除记录。

本文档包含使用系统提供的示例， `ContentProvider`，以及一个简单的只读的示例实现一个自定义`ContentProvider`。

