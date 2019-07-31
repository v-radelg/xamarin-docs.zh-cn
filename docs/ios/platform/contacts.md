---
title: Xamarin 中的联系人和 ContactsUI
description: 本文介绍如何在 Xamarin iOS 应用中使用新的 "联系人" 和 "联系人" UI 框架。 这些框架替换以前版本的 iOS 中使用的现有通讯簿和通讯簿 UI。
ms.prod: xamarin
ms.assetid: 7b6fb66a-5e19-4a5a-9ed2-f6b02af099af
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: fe69e596193aab306a3701ea1ae0792362270557
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68644683"
---
# <a name="contacts-and-contactsui-in-xamarinios"></a>Xamarin 中的联系人和 ContactsUI

_本文介绍如何在 Xamarin iOS 应用中使用新的 "联系人" 和 "联系人" UI 框架。这些框架替换以前版本的 iOS 中使用的现有通讯簿和通讯簿 UI。_

随着 ios 9 的引入, Apple 发布了两个新框架, `Contacts`以及`ContactsUI`替换 iOS 8 及更早版本使用的现有通讯簿和通讯簿 UI 框架。

这两个新框架包含以下功能:

- [**联系人**](#contacts)-提供对用户的联系人列表数据的访问。
    由于大多数应用只需要只读访问权限, 因此此框架已针对线程安全只读访问进行了优化。

- [**ContactsUI**](#contactsui) -提供 XAMARIN ios UI 元素, 用于在 iOS 设备上显示、编辑、选择和创建联系人。

[![](contacts-images/add01.png "IOS 设备上的示例联系人表")](contacts-images/add01.png#lightbox)

> [!IMPORTANT]
> Ios 8 `AddressBook` ( `AddressBookUI`及更早) 使用的现有和框架已在 ios 9 中弃用, 并且应尽快为任何现有`Contacts`的`ContactsUI` Xamarin 应用替换为新的和框架。 新应用应针对新框架编写。




在以下部分中, 我们将介绍这些新框架, 以及如何在 Xamarin iOS 应用程序中实现它们。

<a name="contacts" />

## <a name="the-contacts-framework"></a>联系人框架

Contact 框架提供对用户的联系人信息的 Xamarin iOS 访问权限。 由于大多数应用只需要只读访问权限, 因此此框架已针对线程安全只读访问进行了优化。

<a name="Contact_Objects" />

### <a name="contact-objects"></a>联系人对象

`CNContact`类提供对联系人属性 (如姓名、地址或电话号码) 的线程安全、只读访问。 `CNContact``NSDictionary`和等函数包含多个只读属性集合 (如地址或电话号码):

[![](contacts-images/contactobjects.png "联系人对象概述")](contacts-images/contactobjects.png#lightbox)

对于可以具有多个值 (如电子邮件地址或电话号码) 的任何属性, 这些属性将表示为对象的`NSLabeledValue`数组。 `NSLabeledValue`是由一组只读标签和值组成的线程安全元组, 其中标签定义用户的值 (例如, 家庭或工作电子邮件)。 联系人框架提供了一系列预定义标签 (通过`CNLabelKey`和`CNLabelPhoneNumberKey`静态类), 可以在应用中使用这些标签, 也可以根据需要定义自定义标签。

对于需要调整现有联系人 (或创建新联系人) 的值的任何 Xamarin 应用程序, 请使用`NSMutableContact`该类及其子类的版本 ( `CNMutablePostalAddress`如)。

例如, 以下代码将创建一个新联系人并将其添加到用户的联系人集合中:

```csharp
// Create a new Mutable Contact (read/write)
var contact = new CNMutableContact();

// Set standard properties
contact.GivenName = "John";
contact.FamilyName = "Appleseed";

// Add email addresses
var homeEmail = new CNLabeledValue<NSString>(CNLabelKey.Home, new NSString("john.appleseed@mac.com"));
var workEmail = new CNLabeledValue<NSString>(CNLabelKey.Work, new NSString("john.appleseed@apple.com"));
contact.EmailAddresses = new CNLabeledValue<NSString>[] { homeEmail, workEmail };

// Add phone numbers
var cellPhone = new CNLabeledValue<CNPhoneNumber>(CNLabelPhoneNumberKey.iPhone, new CNPhoneNumber("713-555-1212"));
var workPhone = new CNLabeledValue<CNPhoneNumber>("Work", new CNPhoneNumber("408-555-1212"));
contact.PhoneNumbers = new CNLabeledValue<CNPhoneNumber>[] { cellPhone, workPhone };

// Add work address
var workAddress = new CNMutablePostalAddress()
{
    Street = "1 Infinite Loop",
    City = "Cupertino",
    State = "CA",
    PostalCode = "95014"
};
contact.PostalAddresses = new CNLabeledValue<CNPostalAddress>[] { new CNLabeledValue<CNPostalAddress>(CNLabelKey.Work, workAddress) };

// Add birthday
var birthday = new NSDateComponents()
{
    Day = 1,
    Month = 4,
    Year = 1984
};
contact.Birthday = birthday;

// Save new contact
var store = new CNContactStore();
var saveRequest = new CNSaveRequest();
saveRequest.AddContact(contact, store.DefaultContainerIdentifier);

// Attempt to save changes
NSError error;
if (store.ExecuteSaveRequest(saveRequest, out error))
{
    Console.WriteLine("New contact saved");
}
else
{
    Console.WriteLine("Save error: {0}", error);
}
```

如果在 iOS 9 设备上运行此代码, 则会将新联系人添加到用户的集合。 例如:

[![](contacts-images/add01.png "添加到用户的集合中的新联系人")](contacts-images/add01.png#lightbox)

### <a name="contact-formatting-and-localization"></a>联系人格式设置和本地化

联系人框架包含几个对象和方法, 可帮助您设置内容的格式并对内容进行本地化以便向用户显示。 例如, 下面的代码会正确设置联系人姓名和邮件地址的格式以显示:

```csharp
Console.WriteLine(CNContactFormatter.GetStringFrom(contact, CNContactFormatterStyle.FullName));
Console.WriteLine(CNPostalAddressFormatter.GetStringFrom(workAddress, CNPostalAddressFormatterStyle.MailingAddress));
```

对于将在应用程序的 UI 中显示的属性标签, Contact 框架还提供了用于本地化这些字符串的方法。 同样, 这是基于运行应用的 iOS 设备的当前区域设置。 例如：

```csharp
// Localized properties
Console.WriteLine(CNContact.LocalizeProperty(CNContactOptions.Nickname));
Console.WriteLine(CNLabeledValue<NSString>.LocalizeLabel(CNLabelKey.Home));
```

### <a name="fetching-existing-contacts"></a>正在获取现有联系人

通过使用`CNContactStore`类的实例, 可以从用户的 contact 数据库中提取联系信息。 `CNContactStore`包含从数据库提取或更新联系人和组所需的所有方法。 由于这些方法是同步的, 因此建议您在后台线程上运行这些方法, 以防止阻止 UI。

使用谓词 (从`CNContact`类生成), 可以在从数据库获取联系人时筛选返回的结果。 若要仅提取包含字符串`Appleseed`的联系人, 请使用以下代码:

```csharp
// Create predicate to locate requested contact
var predicate = CNContact.GetPredicateForContacts("Appleseed");
```

> [!IMPORTANT]
> 联系人框架不支持泛型和复合谓词。

例如, 若要将提取限制为仅获取联系人的**GivenName**和**FamilyName**属性, 请使用以下代码:

```csharp
// Define fields to be searched
var fetchKeys = new NSString[] {CNContactKey.GivenName, CNContactKey.FamilyName};
```

最后, 若要搜索数据库并返回结果, 请使用以下代码:

```csharp
// Grab matching contacts
var store = new CNContactStore();
NSError error;
var contacts = store.GetUnifiedContacts(predicate, fetchKeys, out error);
```

如果在上面的**Contact 对象**部分中创建的示例后运行此代码, 它将返回刚刚创建的 "John Appleseed" 联系人。

### <a name="contact-access-privacy"></a>联系访问隐私

由于最终用户可以基于每个应用程序授予或拒绝对其联系人信息的访问权限, 因此在首次调用`CNContactStore`时, 将会显示一个对话框, 询问他们是否允许访问你的应用。

仅在应用程序第一次运行时, 权限请求将会出现一次, 后续运行或对的`CNContactStore`调用将使用用户在该时间选择的权限。

你应设计应用程序, 使其能够正常处理用户拒绝其 contact 数据库的访问。

#### <a name="fetching-partial-contacts"></a>提取分部联系人

_部分联系人_是指只有部分可用属性从的联系人存储区中提取的联系人。 如果尝试访问先前未提取的属性, 将导致异常。

通过使用`IsKeyAvailable` `CNContact`实例的或`AreKeysAvailable`方法, 可以轻松查看给定联系人是否具有所需的属性。 例如：

```csharp
// Does the contact contain the requested key?
if (!contact.IsKeyAvailable(CNContactOption.PostalAddresses)) {
    // No, re-request to pull required info
    var fetchKeys = new NSString[] {CNContactKey.GivenName, CNContactKey.FamilyName, CNContactKey.PostalAddresses};
    var store = new CNContactStore();
    NSError error;
    contact = store.GetUnifiedContact(contact.Identifier, fetchKeys, out error);
}
```

> [!IMPORTANT]
> 类的`GetUnifiedContact`和`GetUnifiedContacts`方法  仅返回仅限从提供的提取密钥请求的属性的部分联系人。 `CNContactStore`

### <a name="unified-contacts"></a>统一联系人

用户可能会在其 contact 数据库中为单个用户提供不同的联系信息源 (如 iCloud、Facebook 或 Google Mail)。 在 iOS 和 OS X 应用中, 此联系人信息将自动链接在一起, 并作为单个_统一联系人_显示给用户:

[![](contacts-images/unified01.png "统一联系人概述")](contacts-images/unified01.png#lightbox)

此统一联系人是一个临时的内存中视图, 其中提供了链接联系人信息, 该联系人信息将获得自己唯一的标识符 (如果需要, 应使用该联系人重新提取联系人)。 默认情况下, Contact 框架将尽可能返回统一的联系人。

### <a name="creating-and-updating-contacts"></a>创建和更新联系人

正如上面的 "[联系对象](#Contact_Objects)" 一节中所述`CNContactStore` , 你可以使用和`CNMutableContact`的实例来创建新联系人, 然后使用将`CNSaveRequest`其写入用户的 Contact 数据库:

```csharp
// Create a new Mutable Contact (read/write)
var contact = new CNMutableContact();

// Set standard properties
contact.GivenName = "John";
contact.FamilyName = "Appleseed";

// Save new contact
var store = new CNContactStore();
var saveRequest = new CNSaveRequest();
saveRequest.AddContact(contact, store.DefaultContainerIdentifier);

NSError error;
if (store.ExecuteSaveRequest(saveRequest, out error)) {
    Console.WriteLine("New contact saved");
} else {
    Console.WriteLine("Save error: {0}", error);
}
```

还可用于将多个联系人和组更改缓存到一个操作中, 并将这些修改`CNContactStore`分批。 `CNSaveRequest`

若要更新从提取操作获取的非可变联系人, 您必须首先请求一个可变副本, 然后将其修改并保存回联系人存储区。 例如：

```csharp
// Get mutable copy of contact
var mutable = contact.MutableCopy() as CNMutableContact;
var newEmail = new CNLabeledValue<NSString>(CNLabelKey.Home, new NSString("john.appleseed@xamarin.com"));

// Append new email
var emails = new NSObject[mutable.EmailAddresses.Length+1];
mutable.EmailAddresses.CopyTo(emails,0);
emails[mutable.EmailAddresses.Length+1] = newEmail;
mutable.EmailAddresses = emails;

// Update contact
var store = new CNContactStore();
var saveRequest = new CNSaveRequest();
saveRequest.UpdateContact(mutable);

NSError error;
if (store.ExecuteSaveRequest(saveRequest, out error)) {
    Console.WriteLine("Contact updated.");
} else {
    Console.WriteLine("Update error: {0}", error);
}
```

### <a name="contact-change-notifications"></a>联系更改通知

一旦修改了联系人, contact Store 就会将发送`CNContactStoreDidChangeNotification`到默认的通知中心。 如果已缓存或当前正在显示任何联系人, 则需要从联系人存储区 (`CNContactStore`) 刷新这些对象。

### <a name="containers-and-groups"></a>容器和组

用户的联系人可以位于用户的设备上, 也可以是从一个或多个服务器帐户 (例如 Facebook 或 Google) 同步到设备的联系人。 联系人的每个池都有其自己的_容器_, 并且给定联系人只能存在于一个容器中。

[![](contacts-images/containers01.png "容器和组概述")](contacts-images/containers01.png#lightbox)

某些容器允许将联系人分成一个或多个_组_或_子组_。 此行为取决于给定容器的后备存储。 例如, iCloud 只有一个容器, 但可以有多个组 (但不包含子组)。 另一方面, Microsoft Exchange 不支持组, 但可以有多个容器 (每个 Exchange 文件夹一个)。

[![](contacts-images/containers02.png "容器和组内的重叠")](contacts-images/containers02.png#lightbox)

<a name="contactsui" />

## <a name="the-contactsui-framework"></a>ContactsUI 框架

对于应用程序不需要提供自定义 UI 的情况, 可以使用 ContactsUI 框架来显示 UI 元素, 以便在 Xamarin iOS 应用中显示、编辑、选择和创建联系人。

通过使用 Apple 的内置控件, 你不仅可以减少必须创建的代码量来支持 Xamarin iOS 应用中的联系人, 而且还能为应用的用户提供一致的界面。

### <a name="the-contact-picker-view-controller"></a>联系人选取器视图控制器

联系人选取器视图控制器 (`CNContactPickerViewController`) 管理标准的 "联系人选择器" 视图, 该视图允许用户从用户的 contact 数据库中选择联系人或联系人属性。 用户可以选择一个或多个联系人 (基于其使用情况), 并且在显示该选取器之前, 联系人选取器视图控制器不会提示用户提供权限。

在调用`CNContactPickerViewController`类之前, 您可以定义用户可选择哪些属性并定义谓词来控制联系人属性的显示和选择。

使用从`CNContactPickerDelegate`继承的类的实例, 以响应用户与选取器的交互。 例如:

```csharp
using System;
using System.Linq;
using UIKit;
using Foundation;
using Contacts;
using ContactsUI;

namespace iOS9Contacts
{
    public class ContactPickerDelegate: CNContactPickerDelegate
    {
        #region Constructors
        public ContactPickerDelegate ()
        {
        }

        public ContactPickerDelegate (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ContactPickerDidCancel (CNContactPickerViewController picker)
        {
            Console.WriteLine ("User canceled picker");

        }

        public override void DidSelectContact (CNContactPickerViewController picker, CNContact contact)
        {
            Console.WriteLine ("Selected: {0}", contact);
        }

        public override void DidSelectContactProperty (CNContactPickerViewController picker, CNContactProperty contactProperty)
        {
            Console.WriteLine ("Selected Property: {0}", contactProperty);
        }
        #endregion
    }
}
```

若要允许用户从其数据库的联系人中选择一个电子邮件地址, 可以使用以下代码:

```csharp
// Create a new picker
var picker = new CNContactPickerViewController();

// Select property to pick
picker.DisplayedPropertyKeys = new NSString[] {CNContactKey.EmailAddresses};
picker.PredicateForEnablingContact = NSPredicate.FromFormat("emailAddresses.@count > 0");
picker.PredicateForSelectionOfContact = NSPredicate.FromFormat("emailAddresses.@count == 1");

// Respond to selection
picker.Delegate = new ContactPickerDelegate();

// Display picker
PresentViewController(picker,true,null);
```

### <a name="the-contact-view-controller"></a>联系人视图控制器

Contact view Controller (`CNContactViewController`) 类提供了一个控制器, 用于向最终用户显示标准联系人视图。 在通过调用正确的静态构造函数 (`FromNewContact`、 `FromUnknownContact`、 `FromContact`) 来显示视图之前, 联系人视图可以显示新的、未知的或现有的联系人以及必须指定的类型。 例如：

```csharp
// Create a new contact view
var view = CNContactViewController.FromContact(contact);

// Display the view
PresentViewController(view, true, null);
```

## <a name="summary"></a>总结

本文详细介绍了如何在 Xamarin iOS 应用程序中使用联系人和联系人 UI 框架。 首先, 它涵盖了 Contact framework 提供的不同类型的对象, 以及如何使用它们来创建新联系人或访问现有联系人。 它还检查联系 UI 框架以选择现有联系人并显示联系信息。


## <a name="related-links"></a>相关链接

- [联系人示例](https://docs.microsoft.com/samples/xamarin/ios-samples/contacts/)
- [IOS 9 中的新增功能](https://developer.apple.com/library/content/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [联系人框架参考](https://developer.apple.com/documentation/contacts?language=objc)
- [ContactsUI Framework 参考](https://developer.apple.com/documentation/contactsui?language=objc)
