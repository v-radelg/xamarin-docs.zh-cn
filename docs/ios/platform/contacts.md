---
title: 联系人和 ContactsUI 在 Xamarin.iOS
description: 本文介绍如何使用新的联系人和联系人 UI 中的 Xamarin.iOS 应用程序的框架。 这些框架替换现有的通讯簿和 iOS 的早期版本中使用的地址簿 UI。
ms.prod: xamarin
ms.assetid: 7b6fb66a-5e19-4a5a-9ed2-f6b02af099af
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: e3f1533605d08df58d8d257714dd8135690c0e5d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61388896"
---
# <a name="contacts-and-contactsui-in-xamarinios"></a>联系人和 ContactsUI 在 Xamarin.iOS

_本文介绍如何使用新的联系人和联系人 UI 中的 Xamarin.iOS 应用程序的框架。这些框架替换现有的通讯簿和 iOS 的早期版本中使用的地址簿 UI。_

IOS 9 的引入，Apple 已发布了两个新框架`Contacts`和`ContactsUI`、 该替换现有的通讯簿和地址簿 UI 框架由 iOS 8 及更早版本。

两个新框架包含以下功能：

- [**联系人**](#contacts) -提供对用户的联系人列表数据的访问。
    由于大多数应用程序仅需要只读访问权限，此框架已经过优化为线程安全的只读访问。

- [**ContactsUI** ](#contactsui) -提供了 Xamarin.iOS UI 元素以显示，请编辑、 选择和 iOS 设备上创建的联系人。

[![](contacts-images/add01.png "IOS 设备上一个示例联系人表")](contacts-images/add01.png#lightbox)

> [!IMPORTANT]
> 现有`AddressBook`并`AddressBookUI`框架供 iOS 8 （和之前的版本） 已弃用在 iOS 9 中，应替换为新`Contacts`和`ContactsUI`框架，可用于任何现有的 Xamarin.iOS 应用程序越早越好。 应根据新框架编写新的应用程序。




在以下部分中，我们将看看这些新框架，以及如何在 Xamarin.iOS 应用程序中实现它们。

<a name="contacts" />

## <a name="the-contacts-framework"></a>联系人 Framework

联系人框架提供了 Xamarin.iOS 权访问用户的联系信息。 由于大多数应用程序仅需要只读访问权限，此框架已经过优化为线程安全的只读访问。

<a name="Contact_Objects" />

### <a name="contact-objects"></a>联系人对象

`CNContact`类提供对联系人的属性，如姓名、 地址或电话号码的线程安全的只读访问。 `CNContact` 功能类似于`NSDictionary`和包含的属性 （如地址或电话号码） 的多个，只读的集合：

[![](contacts-images/contactobjects.png "联系人对象概述")](contacts-images/contactobjects.png#lightbox)

对于可以有多个值 （如电子邮件地址或电话号码） 的任何属性，它们将表示为一个数组`NSLabeledValue`对象。 `NSLabeledValue` 是线程安全元组包含一只读的组的标签和值的标签定义的值为用户 （例如家庭或工作的电子邮件）。 联系人框架提供一系列预定义的标签 (通过`CNLabelKey`和`CNLabelPhoneNumberKey`静态类)，可以在应用中使用或已定义你的需求的自定义标签的选项。

对于需要调整现有联系人的值 （或创建新的） 任何 Xamarin.iOS 应用，使用`NSMutableContact`版本的类和及其子类 (如`CNMutablePostalAddress`)。

例如，下面的代码将创建新的联系人，并将其添加到用户的联系人集合：

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

如果 iOS 9 设备上运行此代码，则新的联系人将添加到用户的集合中。 例如：

[![](contacts-images/add01.png "新的联系人添加到用户的集合")](contacts-images/add01.png#lightbox)

### <a name="contact-formatting-and-localization"></a>联系人的格式设置和本地化

联系人框架包含多个对象和方法，可以帮助你设置的格式和本地化向用户显示的内容。 例如，下面的代码将正确地设置格式的联系人名称和邮件地址的显示：

```csharp
Console.WriteLine(CNContactFormatter.GetStringFrom(contact, CNContactFormatterStyle.FullName));
Console.WriteLine(CNPostalAddressFormatter.GetStringFrom(workAddress, CNPostalAddressFormatterStyle.MailingAddress));
```

你将在您的应用 UI 中显示的属性标签，联系人 framework 有用于本地化这些字符串也方法。 同样，这基于 iOS 设备运行应用时的当前区域设置。 例如：

```csharp
// Localized properties
Console.WriteLine(CNContact.LocalizeProperty(CNContactOptions.Nickname));
Console.WriteLine(CNLabeledValue<NSString>.LocalizeLabel(CNLabelKey.Home));
```

### <a name="fetching-existing-contacts"></a>正在提取现有联系人

通过使用的实例`CNContactStore`类，您可以从用户的联系人数据库中提取的联系信息。 `CNContactStore`包含所有所需提取或更新联系人和组从数据库的方法。 因为这些方法都是同步的建议以防止阻止 UI 在后台线程上运行它们。

通过使用谓词 (从生成`CNContact`类)，您可以筛选从数据库提取联系人时，返回结果。 若要提取仅包含字符串的联系人`Appleseed`，使用以下代码：

```csharp
// Create predicate to locate requested contact
var predicate = CNContact.GetPredicateForContacts("Appleseed");
```

> [!IMPORTANT]
> 联系人框架不支持泛型和复合的谓词。

例如，若要限制为仅提取**GivenName**并**FamilyName**的联系人的属性，可以使用以下代码：

```csharp
// Define fields to be searched
var fetchKeys = new NSString[] {CNContactKey.GivenName, CNContactKey.FamilyName};
```

最后，若要在数据库中搜索并返回结果，请使用以下代码：

```csharp
// Grab matching contacts
var store = new CNContactStore();
NSError error;
var contacts = store.GetUnifiedContacts(predicate, fetchKeys, out error);
```

如果我们在中创建运行示例后运行此代码**联系人对象**上面部分中，它将返回我们刚刚创建的"John Appleseed"联系人。

### <a name="contact-access-privacy"></a>联系人访问隐私

因为最终用户可以授予或拒绝对他们的联系信息基于每个应用程序的访问，第一次进行调用`CNContactStore`，将显示一个对话框，要求他们将能够为你的应用的访问。

权限请求才会一次呈现时，第一次在应用正在运行，且后续运行或调用`CNContactStore`将使用用户选择在该时间的权限。

应设计您的应用程序，使它能够合理处理拒绝对其联系人数据库的访问权限的用户。

#### <a name="fetching-partial-contacts"></a>正在提取部分联系人

一个_部分联系_是只有某些可用的属性的联系人存储从已提取的联系人。 如果您尝试访问不事先提取的属性，则将导致异常。

可以轻松地对其进行检查以查看给定的联系人是否通过使用具有所需的属性`IsKeyAvailable`或`AreKeysAvailable`方法的`CNContact`实例。 例如：

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
> `GetUnifiedContact`并`GetUnifiedContacts`方法的`CNContactStore`类_仅_返回部分联系限于从提供的提取密钥请求的属性。

### <a name="unified-contacts"></a>统一的联系人

用户可能具有不同来源的其联系人数据库 （如 iCloud、 Facebook 或 Google Mail) 中由一个人的联系信息。 在 iOS 和 OS X 应用中，此联系信息将自动链接在一起并显示为作为单个的用户_统一联系_:

[![](contacts-images/unified01.png "统一的联系人概述")](contacts-images/unified01.png#lightbox)

此统一联系是临时内存中将会提供其自己的唯一标识符 （这应该用于根据需要重新提取联系人） 的链接联系信息视图。 默认情况下，联系人框架将返回统一联系，只要有可能。

### <a name="creating-and-updating-contacts"></a>创建和更新联系人

中可以看到[联系对象](#Contact_Objects)上面部分中，使用`CNContactStore`的实例`CNMutableContact`若要创建新的联系人，然后写入到用户的联系数据库使用`CNSaveRequest`:

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

一个`CNSaveRequest`还可以使用缓存到单个操作的多个联系人和组更改，并对这些修改进行批处理`CNContactStore`。

若要更新非可变联系人获取从提取操作，必须首先请求可变的副本，然后修改和保存回联系人存储库。 例如：

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

### <a name="contact-change-notifications"></a>联系人的更改通知

只要修改联系人，请联系应用商店发布`CNContactStoreDidChangeNotification`到默认通知中心。 如果已缓存或当前正在显示的任何联系人，您将需要刷新这些对象从联系人应用商店 (`CNContactStore`)。

### <a name="containers-and-groups"></a>容器和组

用户的联系人可以存在本地或者在用户设备上为联系人同步到设备 （如 Facebook 或 Google） 的一个或多个服务器帐户。 联系人的每个池都有自己_容器_和给定的联系人只能存在于一个容器。

[![](contacts-images/containers01.png "容器和组概述")](contacts-images/containers01.png#lightbox)

某些容器允许排列成一个或多个联系人_组_或_子组_。 此行为是依赖于给定容器的后备存储。 例如，iCloud 具有只有一个容器，但可以具有多个组 （但没有子组）。 但是，Microsoft Exchange 不支持组，但可以有多个容器 （一个用于每个 Exchange 文件夹）。

[![](contacts-images/containers02.png "在容器和组重叠")](contacts-images/containers02.png#lightbox)

<a name="contactsui" />

## <a name="the-contactsui-framework"></a>ContactsUI Framework

情况下，你的应用程序不需要显示自定义 UI，可以使用 ContactsUI 框架提供 UI 元素以显示、 编辑、 选择和 Xamarin.iOS 应用程序中创建的联系人。

通过使用 Apple 的内置控件不仅减少您必须创建要在 Xamarin.iOS 应用中，支持联系人的代码量，但向应用程序用户提供一致的界面。

### <a name="the-contact-picker-view-controller"></a>联系人选取器视图控制器

联系人选取器视图控制器 (`CNContactPickerViewController`) 管理允许用户从用户的联系人数据库中选择联系人或联系人属性标准联系人选取器视图。 用户可以选择一个或多个联系人 （基于其使用情况），并联系选取器视图控制器不提示输入之前显示选取器的权限。

在调用之前`CNContactPickerViewController`类，定义哪些用户可以选择和定义谓词来控制显示和选择的联系人属性的属性。

使用继承的类的实例`CNContactPickerDelegate`以响应在选取器与用户的交互。 例如：

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

若要允许用户选择从其数据库中的联系人的电子邮件地址，可以使用以下代码：

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

### <a name="the-contact-view-controller"></a>联系人的视图控制器

请联系视图控制器 (`CNContactViewController`) 类提供了一个控制器来向最终用户提供标准的联系人视图。 联系人视图显示新的新建、 未知或现有联系人和视图显示通过调用正确的静态构造函数之前，必须指定的类型 (`FromNewContact`， `FromUnknownContact`， `FromContact`)。 例如：

```csharp
// Create a new contact view
var view = CNContactViewController.FromContact(contact);

// Display the view
PresentViewController(view, true, null);
```

## <a name="summary"></a>总结

本文已使用 Xamarin.iOS 应用程序中的联系人和联系人 UI 框架的详细的信息。 首先，它包括不同类型的联系人框架提供的对象以及您如何使用它们来创建新的或访问现有的联系人。 它还检查，请与 UI 框架，以选择现有联系人并显示联系信息。


## <a name="related-links"></a>相关链接

- [QuickContacts 示例](https://developer.xamarin.com/samples/monotouch/ios9/QuickContacts/)
- [What's New iOS 9 中](https://developer.apple.com/library/content/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [联系人框架引用](https://developer.apple.com/documentation/contacts?language=objc)
- [ContactsUI 框架引用](https://developer.apple.com/documentation/contactsui?language=objc)
