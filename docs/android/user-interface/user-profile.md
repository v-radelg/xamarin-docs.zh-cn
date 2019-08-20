---
title: 用户配置文件
ms.prod: xamarin
ms.assetid: 6BB01F75-5E98-49A1-BBA0-C2680905C59D
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/22/2018
ms.openlocfilehash: ef8f6aeb3c6577825210c0bce594ea0eb4af4e2f
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68648250"
---
# <a name="user-profile"></a>用户配置文件

自 API 级别5起, Android 支持枚举具有[ContactsContract](xref:Android.Provider.ContactsContract)提供程序的联系人。 例如, 列出联系人非常简单, 只是使用[ContactContracts](xref:Android.Provider.ContactsContract.Contacts)类, 如下面的代码示例所示:

```csharp
// Get the URI for the user's contacts:
var uri = ContactsContract.Contacts.ContentUri;

// Setup the "projection" (columns we want) for only the ID and display name:
string[] projection = {
    ContactsContract.Contacts.InterfaceConsts.Id, 
    ContactsContract.Contacts.InterfaceConsts.DisplayName };

// Use a CursorLoader to retrieve the user's contacts data:
CursorLoader loader = new CursorLoader(this, uri, projection, null, null, null);
ICursor cursor = (ICursor)loader.LoadInBackground();

// Print the contact data to the console if reading back succeeds:
if (cursor != null)
{
    if (cursor.MoveToFirst())
    {
        do
        {
            Console.WriteLine("Contact ID: {0}, Contact Name: {1}",
                               cursor.GetString(cursor.GetColumnIndex(projection[0])),
                               cursor.GetString(cursor.GetColumnIndex(projection[1])));
        } while (cursor.MoveToNext());
    }
}
```

从 Android 4 (API 级别 14) 开始, 可通过`ContactsContract`提供程序使用 [ContactsContact.Profile](xref:Android.Provider.ContactsContract.Profile) 类。 `ContactsContact.Profile`提供对设备所有者的个人配置文件的访问权限, 包括设备所有者姓名和电话号码之类的联系人数据。

## <a name="required-permissions"></a>所需权限

若要读取和写入联系人数据, 应用程序必须`READ_CONTACTS`分别`WRITE_CONTACTS`请求和权限。
此外, 若要读取和编辑用户配置文件, 应用程序必须`READ_PROFILE`请求`WRITE_PROFILE`和权限。

## <a name="updating-profile-data"></a>更新配置文件数据

设置这些权限后, 应用程序可以使用普通 Android 技术与用户配置文件的数据进行交互。 例如, 若要更新配置文件的显示名称, 请通过[ContactsContract](xref:Android.Provider.ContactsContract.Profile.ContentRawContactsUri)属性调用[ContentResolver](xref:Android.Content.ContentResolver.Update*) `Uri`调用, 如下所示:

```csharp
var values = new ContentValues ();
values.Put (ContactsContract.Contacts.InterfaceConsts.DisplayName, "John Doe");

// Update the user profile with the name "John Doe":
ContentResolver.Update (ContactsContract.Profile.ContentRawContactsUri, values, null, null);
```

## <a name="reading-profile-data"></a>读取配置文件数据

向 ContactsContact 发出查询时, [ContentUri](xref:Android.Provider.ContactsContract.Profile.ContentUri)将读取该配置文件数据。 例如, 以下代码将读取用户配置文件的显示名称:

```csharp
// Read the profile
var uri = ContactsContract.Profile.ContentUri;

// Setup the "projection" (column we want) for only the display name:
string[] projection = {
    ContactsContract.Contacts.InterfaceConsts.DisplayName };

// Use a CursorLoader to retrieve the data:
CursorLoader loader = new CursorLoader(this, uri, projection, null, null, null);
ICursor cursor = (ICursor)loader.LoadInBackground();
if (cursor != null)
{
    if (cursor.MoveToFirst ())
    {
        Console.WriteLine(cursor.GetString (cursor.GetColumnIndex (projection [0])));
    }
}
```

## <a name="navigating-to-the-user-profile"></a>导航到用户配置文件

最后, 若要导航到用户配置文件, 请使用`ActionView`操作创建意向, 然后将`ContactsContract.Profile.ContentUri`其传递给方法`StartActivity` , 如下所示:

```csharp
var intent = new Intent (Intent.ActionView,
    ContactsContract.Profile.ContentUri);
StartActivity (intent);
```

运行上述代码时, 将显示用户配置文件, 如以下屏幕截图所示:

[![显示 John Doe 用户配置文件的配置文件的屏幕截图](user-profile-images/01-profile-screen-sml.png)](user-profile-images/01-profile-screen.png#lightbox)

使用用户配置文件类似于与 Android 中的其他数据进行交互, 并且它还提供额外的设备个性化级别。

## <a name="related-links"></a>相关链接

- [ContactsProviderDemo (示例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/contactsproviderdemo)
- [冰淇淋三明治](http://www.android.com/about/ice-cream-sandwich/)
- [Android 4.0 平台](https://developer.android.com/sdk/android-4.0.html)
