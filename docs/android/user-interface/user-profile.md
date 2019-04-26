---
title: 用户配置文件
ms.prod: xamarin
ms.assetid: 6BB01F75-5E98-49A1-BBA0-C2680905C59D
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/22/2018
ms.openlocfilehash: 0613411e5436a0ea8ed08bf4af52dae84a9a701c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61307942"
---
# <a name="user-profile"></a>用户配置文件

Android 支持枚举联系人[ContactsContract](https://developer.xamarin.com/api/type/Android.Provider.ContactsContract/)自 API 级别 5 的提供程序。 例如，列出联系人非常简单，使用[ContactContracts.Contacts](https://developer.xamarin.com/api/type/Android.Provider.ContactsContract+Contacts/)类，如下面的代码示例中所示：

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

Android 4 (API 级别 14) 开头[ContactsContact.Profile](https://developer.xamarin.com/api/type/Android.Provider.ContactsContract+Profile/)类是可通过`ContactsContract`提供程序。 `ContactsContact.Profile`包括联系人数据，例如设备所有者的姓名和电话号码的设备的所有者提供对个人配置文件的访问。


## <a name="required-permissions"></a>所需权限

若要读取和写入的联系人数据，应用程序必须请求`READ_CONTACTS`和`WRITE_CONTACTS`权限，分别。
此外，若要阅读和编辑用户配置文件，应用程序必须请求`READ_PROFILE`和`WRITE_PROFILE`权限。


## <a name="updating-profile-data"></a>更新配置文件数据

设置这些权限后，应用程序可以使用正常的 Android 技术与用户配置文件的数据进行交互。 例如，若要更新配置文件的显示名称，请调用[ContentResolver.Update](https://developer.xamarin.com/api/member/Android.Content.ContentResolver.Update)与`Uri`通过检索[ContactsContract.Profile.ContentRawContactsUri](https://developer.xamarin.com/api/property/Android.Provider.ContactsContract+Profile.ContentRawContactsUri/)属性，如所示如下：

```csharp
var values = new ContentValues ();
values.Put (ContactsContract.Contacts.InterfaceConsts.DisplayName, "John Doe");

// Update the user profile with the name "John Doe":
ContentResolver.Update (ContactsContract.Profile.ContentRawContactsUri, values, null, null);
```

## <a name="reading-profile-data"></a>读取配置文件数据

发出到查询[ContactsContact.Profile.ContentUri](https://developer.xamarin.com/api/property/Android.Provider.ContactsContract+Profile.ContentUri/)读回的配置文件数据。 例如，下面的代码将读取用户配置文件的显示名称：

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

最后，若要导航到用户配置文件，创建使用意向`ActionView`操作和一个`ContactsContract.Profile.ContentUri`然后将其传递给`StartActivity`方法如下：

```csharp
var intent = new Intent (Intent.ActionView,
    ContactsContract.Profile.ContentUri);           
StartActivity (intent);
```

当运行上面的代码，用户配置文件将显示如以下屏幕截图中所示：

[![显示 John Doe 用户配置文件的配置文件的屏幕截图](user-profile-images/01-profile-screen-sml.png)](user-profile-images/01-profile-screen.png#lightbox)

使用用户配置文件是类似于与 Android 中的其他数据进行交互，它提供了一层额外的设备的个性化。



## <a name="related-links"></a>相关链接

- [ContactsProviderDemo （示例）](https://developer.xamarin.com/samples/monodroid/ContactsProviderDemo/)
- [引入 Ice Cream Sandwich](http://www.android.com/about/ice-cream-sandwich/)
- [Android 4.0 平台](https://developer.android.com/sdk/android-4.0.html)
