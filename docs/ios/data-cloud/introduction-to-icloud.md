---
title: 通过 Xamarin.iOS 使用 iCloud
description: 本文档介绍 iCloud 和在 Xamarin.iOS 应用程序中的使用它。 它讨论了键-值存储、 文档存储和 iCloud 备份。
ms.prod: xamarin
ms.assetid: C6F3B87C-C195-4434-EF14-D66E63894F09
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/09/2016
ms.openlocfilehash: 56456372b13dad5b5040c0c466f2efdf6ecdbc2a
ms.sourcegitcommit: 650458de1d362cd7de174cacef7838f0e74426f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/15/2019
ms.locfileid: "58071016"
---
# <a name="using-icloud-with-xamarinios"></a>通过 Xamarin.iOS 使用 iCloud

在 iOS 5 中的 iCloud 存储 API 允许应用程序将用户文档和特定于应用程序数据保存到一个中心位置并从所有用户的设备访问这些项。

有四种类型的存储：

- **键-值存储**-共享上较少的数据与应用程序用户的其他设备。

- **UIDocument 存储**-若要将文档和其他数据存储在用户的 iCloud 帐户使用的 UIDocument 子类。

- **CoreData** -SQLite 数据库存储。

- **单个文件和目录**-用于直接在文件系统中管理大量不同的文件。

本文档介绍前两种类型的键 / 值对和 UIDocument 子类-以及如何在 Xamarin.iOS 中使用这些功能。

> [!IMPORTANT]
> Apple [提供工具](https://developer.apple.com/support/allowing-users-to-manage-data/)，用于帮助开发人员正确处理欧盟一般数据保护条例 (GDPR)。

## <a name="requirements"></a>要求

- Xamarin.iOS 最新稳定版本
- Xcode 8 或更高版本
- Visual Studio for Mac 或 Visual Studio 2017 和更高版本。

## <a name="preparing-for-icloud-development"></a>为 iCloud 开发准备

应用程序必须配置为使用 iCloud 这两个中的[Apple 预配门户](https://developer.apple.com/account/ios/overview.action)和项目本身。 之前开发的 iCloud （或尝试的示例） 执行以下步骤。

若要正确配置应用程序访问 iCloud:

-   **查找你的团队 Id** -登录到[developer.apple.com](https://developer.apple.com)并访问**Member Center > 帐户 > 开发人员帐户摘要**来获取你的团队 ID （或单个面向单个开发人员 ID). 它将是 10 个字符的字符串 ( **A93A5CM278**例如)-此窗体的"容器标识符"的一部分。

-   **创建新的应用 ID** -若要创建的应用 ID，请按照中所述的步骤[预配存储技术部分中的设备预配指南](~/ios/deploy-test/provisioning/capabilities/icloud-capabilities.md)，并确保将检查**iCloud**为允许的服务：

 [![](introduction-to-icloud-images/icloud-sml.png "检查 iCloud 作为允许的服务")](introduction-to-icloud-images/icloud.png#lightbox)

- **创建新的预配配置文件**-若要创建预配配置文件，请按照中所述的步骤[设备预配指南](~/ios/get-started/installation/device-provisioning/index.md#provisioning-your-device)。

- **将该容器标识符添加到 Entitlements.plist**的容器标识符格式是`TeamID.BundleID`。 有关详细信息，请参阅[使用权利](~/ios/deploy-test/provisioning/entitlements.md)指南。

- **配置项目属性**-在 Info.plist 文件确保**捆绑包标识符**匹配**捆绑 ID**时，设置[创建应用 ID ](~/ios/deploy-test/provisioning/capabilities/index.md);使用 iOS 捆绑签名**预配配置文件**包含具有 iCloud 应用服务的应用 ID 并**自定义权利**文件处于选定状态。 这所有可以在 Visual Studio 中的项目属性窗格下。

- **启用你的设备上的 iCloud** -转到**设置 > iCloud** ，并确保设备已登录。
选择并开启**文档和数据**选项。

- **必须使用设备来测试 iCloud** -它将不在模拟器上运行。
事实上，您确实需要两个或多个设备所有相同的 Apple ID 登录以查看操作中的 iCloud。


## <a name="key-value-storage"></a>键-值存储

键-值存储旨在用于少量的用户可能喜欢间保持的设备-如在书籍或杂志中查看它们的最后一页的数据。 键-值存储不应该用于备份数据。

有一些限制需要注意的使用键-值存储时：

- **最大密钥大小**-密钥名称不能超过 64 个字节。

- **最大值大小**-不能将多个 64 千字节为单位存储在单个值。

- **应用程序的最大键-值存储大小**-应用程序可以仅在总计中存储最多为 64 千字节的键-值数据。 若要设置密钥超过该限制的尝试都将失败并且将会保留以前的值。

- **数据类型**-仅基本类型如字符串、 数字和布尔值可以存储。

**ICloudKeyValue**的示例演示其工作原理。 示例代码创建名为每个设备的密钥： 可以将此项设置一台设备上并观看都会传播到其他人的值。 它还将创建名为在任何设备的可编辑的"共享"，如果在一次编辑多个设备上的项，iCloud 会确定哪个值"胜出"（使用的更改上的时间戳），并获取传播。

此屏幕截图显示了示例中使用。 从 iCloud 收到更改通知时它们在屏幕底部的滚动文本视图中打印并在输入字段中更新。



 [![](introduction-to-icloud-images/icloud-kv-arrows.png "设备之间的消息流")](introduction-to-icloud-images/icloud-kv-arrows.png#lightbox)

### <a name="setting-and-retrieving-data"></a>设置和检索数据

此代码演示如何设置一个字符串值。

```csharp
var store = NSUbiquitousKeyValueStore.DefaultStore;
store.SetString("testkey", "VALUE IN THE CLOUD");  // key and value
store.Synchronize();
```

调用同步可确保值保存到本地磁盘存储区。 同步到 iCloud 在后台发生，并且不能"强制"由应用程序代码。 具有良好的网络连接同步通常会在 5 秒内，但是，如果网络不佳 （或断开连接） 的更新可能需要更长的时间。

您可以检索一个值，此代码：

```csharp
var store = NSUbiquitousKeyValueStore.DefaultStore;
display.Text = store.GetString("testkey");
```

从本地数据存储检索的值-此方法不会尝试联系 iCloud 服务器获取的"最新"的值。 iCloud 将更新本地数据存储区根据自己的计划。

### <a name="deleting-data"></a>删除数据

若要完全删除的键-值对，请使用删除方法如下：

```csharp
var store = NSUbiquitousKeyValueStore.DefaultStore;
store.Remove("testkey");
store.Synchronize();
```

### <a name="observing-changes"></a>观察更改

应用程序还可以通过 iCloud 更改值，应添加到观察程序时接收通知`NSNotificationCenter.DefaultCenter`。
中的以下代码**KeyValueViewController.cs** `ViewWillAppear`方法演示了如何侦听这些通知和创建这些密钥已更改的列表：

```csharp
keyValueNotification =
NSNotificationCenter.DefaultCenter.AddObserver (
    NSUbiquitousKeyValueStore.DidChangeExternallyNotification, notification => {
    Console.WriteLine ("Cloud notification received");
    NSDictionary userInfo = notification.UserInfo;

    var reasonNumber = (NSNumber)userInfo.ObjectForKey (NSUbiquitousKeyValueStore.ChangeReasonKey);
    nint reason = reasonNumber.NIntValue;

    var changedKeys = (NSArray)userInfo.ObjectForKey (NSUbiquitousKeyValueStore.ChangedKeysKey);
    var changedKeysList = new List<string> ();
    for (uint i = 0; i < changedKeys.Count; i++) {
        var key = changedKeys.GetItem<NSString> (i); // resolve key to a string
        changedKeysList.Add (key);
    }
    // now do something with the list...
});
```

然后，你的代码可以采取列表已更改密钥，例如，更新它们的本地副本或使用新值更新 UI 的某些操作。

可能的更改的原因有：ServerChange (0)、 InitialSyncChange (1) 或 QuotaViolationChange (2)。 可以访问的原因，并根据需要执行不同的处理 (例如，您可能需要删除为某些键*QuotaViolationChange*)。

## <a name="document-storage"></a>文档存储

iCloud 文档存储设计用于管理重要到您的应用程序 （和用户） 的数据。 它可以用于管理文件和应用程序需要运行，同时提供基于 iCloud 备份并在用户的所有设备之间共享功能在其他数据。

下图显示了如何它的来龙去脉。 每台设备都保存在本地存储区 (UbiquityContainer) 和操作系统的 iCloud 守护程序负责发送和接收数据在云中的数据。 通过 FilePresenter/FileCoordinator 以防止并发访问，必须完成对 UbiquityContainer 的所有文件访问权限。 `UIDocument`类实现那些; 此示例演示如何使用 UIDocument。

 [![](introduction-to-icloud-images/icloud-overview.png "文档存储概述")](introduction-to-icloud-images/icloud-overview.png#lightbox)

ICloudUIDoc 示例实现了一个简单`UIDocument`包含单个文本字段的子类。 在呈现的文本`UITextView`和编辑以红色显示的通知消息进行传播到其他设备的 iCloud。 示例代码不处理更高级的 iCloud 功能，例如冲突解决方法。

此屏幕截图显示了示例应用程序后更改文本并按**UpdateChangeCount**通过 iCloud 到其他设备同步文档。

 [![](introduction-to-icloud-images/iclouduidoc.png "此屏幕截图显示了示例应用程序后更改文本以及按 UpdateChangeCount")](introduction-to-icloud-images/iclouduidoc.png#lightbox)

有五个部分 iCloudUIDoc 示例：

1. **访问 UbiquityContainer** -确定是否启用 iCloud，如果是这样的应用程序的 iCloud 存储区域路径。

1. **创建 UIDocument 子类**-创建 iCloud 存储与您的模型对象的类。

1. **查找和打开 iCloud 文档**-使用`NSFileManager`和`NSPredicate`查找 iCloud 文档并将其打开。

1. **显示 iCloud 文档**-公开属性从你`UIDocument`，以便你可以与用户界面控件进行交互。

1. **正在保存 iCloud 文档**-确保在 UI 中所做的更改持久保存到磁盘和 iCloud。

所有 iCloud 操作运行 （或应运行） 以异步方式，以便它们不会阻止等待某个操作发生时。 你将看到三种不同方式实现这一点在示例中：

 **线程**-在`AppDelegate.FinishedLaunching`首次调用`GetUrlForUbiquityContainer`在另一个线程以防止阻塞主线程上完成。

 **NotificationCenter** -注册通知时异步操作，例如`NSMetadataQuery.StartQuery`完成。

 **完成处理程序**-等异步操作完成后运行的方法并传入`UIDocument.Open`。

### <a name="accessing-the-ubiquitycontainer"></a>访问 UbiquityContainer

使用 iCloud 文档存储的第一步是确定是否启用 iCloud，如果是"无处不在容器"的位置 （在设备存储启用 iCloud 的文件的目录）。

此代码位于`AppDelegate.FinishedLaunching`示例的方法。

```csharp
// GetUrlForUbiquityContainer is blocking, Apple recommends background thread or your UI will freeze
ThreadPool.QueueUserWorkItem (_ => {
    CheckingForiCloud = true;
    Console.WriteLine ("Checking for iCloud");
    var uburl = NSFileManager.DefaultManager.GetUrlForUbiquityContainer (null);
    // OR instead of null you can specify "TEAMID.com.your-company.ApplicationName"

    if (uburl == null) {
        HasiCloud = false;
        Console.WriteLine ("Can't find iCloud container, check your provisioning profile and entitlements");

        InvokeOnMainThread (() => {
            var alertController = UIAlertController.Create ("No \uE049 available",
            "Check your Entitlements.plist, BundleId, TeamId and Provisioning Profile!", UIAlertControllerStyle.Alert);
            alertController.AddAction (UIAlertAction.Create ("OK", UIAlertActionStyle.Destructive, null));
            viewController.PresentViewController (alertController, false, null);
        });
    } else { // iCloud enabled, store the NSURL for later use
        HasiCloud = true;
        iCloudUrl = uburl;
        Console.WriteLine ("yyy Yes iCloud! {0}", uburl.AbsoluteUrl);
    }
    CheckingForiCloud = false;
});
```

尽管该示例不执行操作，Apple 建议调用 GetUrlForUbiquityContainer，每当应用程序转入前台。

### <a name="creating-a-uidocument-subclass"></a>创建 UIDocument 子类

所有 iCloud 文件和目录 (即。 存储在 UbiquityContainer 目录中的任何内容) 必须使用实现 NSFilePresenter 协议和通过 NSFileCoordinator 编写 NSFileManager 方法进行管理。
执行所有这些操作的最简单方法是不将其写入自己，但子类 UIDocument 会执行它为您。

有只有两个必须在使用 iCloud UIDocument 子类中实现的方法：

- **LoadFromContents** -将中的文件的内容，以便解压缩到您的模型类/es 外的传递。

- **ContentsForType** -保存到磁盘 （和云），用于完善模型类/es 的外表示请求。

此示例代码**iCloudUIDoc\MonkeyDocument.cs**演示如何实现 UIDocument。

```csharp
public class MonkeyDocument : UIDocument
{
    // the 'model', just a chunk of text in this case; must easily convert to NSData
    NSString dataModel;
    // model is wrapped in a nice .NET-friendly property
    public string DocumentString {
        get {
            return dataModel.ToString ();
        }
        set {
            dataModel = new NSString (value);
        }
    }
    public MonkeyDocument (NSUrl url) : base (url)
    {
        DocumentString = "(default text)";
    }
    // contents supplied by iCloud to display, update local model and display (via notification)
    public override bool LoadFromContents (NSObject contents, string typeName, out NSError outError)
    {
        outError = null;

        Console.WriteLine ("LoadFromContents({0})", typeName);

        if (contents != null)
            dataModel = NSString.FromData ((NSData)contents, NSStringEncoding.UTF8);

        // LoadFromContents called when an update occurs
        NSNotificationCenter.DefaultCenter.PostNotificationName ("monkeyDocumentModified", this);
        return true;
    }
    // return contents for iCloud to save (from the local model)
    public override NSObject ContentsForType (string typeName, out NSError outError)
    {
        outError = null;

        Console.WriteLine ("ContentsForType({0})", typeName);
        Console.WriteLine ("DocumentText:{0}",dataModel);

        NSData docData = dataModel.Encode (NSStringEncoding.UTF8);
        return docData;
    }
}
```

在这种情况下，数据模型是非常简单的单个文本字段。 你的数据模型可以很复杂，比如必需的如 Xml 文档或二进制数据。 UIDocument 实现的主要作用是在模型类和可以是磁盘上保存/加载的外表示形式之间进行转换。

### <a name="finding-and-opening-icloud-documents"></a>查找和打开 iCloud 文档

示例应用程序只处理单个文件的 test.txt-因此中的代码**AppDelegate.cs**创建`NSPredicate`和`NSMetadataQuery`专门查找该文件名。 `NSMetadataQuery`以异步方式运行，并在它完成后发送通知。 `DidFinishGathering` 获取由通知观察者调用，停止的查询，并且调用 LoadDocument，使用`UIDocument.Open`方法完成处理程序尝试加载该文件并将其显示在`MonkeyDocumentViewController`。

```csharp
string monkeyDocFilename = "test.txt";
void FindDocument ()
{
    Console.WriteLine ("FindDocument");
    query = new NSMetadataQuery {
        SearchScopes = new NSObject [] { NSMetadataQuery.UbiquitousDocumentsScope }
    };

    var pred = NSPredicate.FromFormat ("%K == %@", new NSObject[] {
        NSMetadataQuery.ItemFSNameKey, new NSString (MonkeyDocFilename)
    });

    Console.WriteLine ("Predicate:{0}", pred.PredicateFormat);
    query.Predicate = pred;

    NSNotificationCenter.DefaultCenter.AddObserver (
        this,
        new Selector ("queryDidFinishGathering:"),
        NSMetadataQuery.DidFinishGatheringNotification,
        query
    );

    query.StartQuery ();
}

[Export ("queryDidFinishGathering:")]
void DidFinishGathering (NSNotification notification)
{
    Console.WriteLine ("DidFinishGathering");
    var metadataQuery = (NSMetadataQuery)notification.Object;
    metadataQuery.DisableUpdates ();
    metadataQuery.StopQuery ();

    NSNotificationCenter.DefaultCenter.RemoveObserver (this, NSMetadataQuery.DidFinishGatheringNotification, metadataQuery);
    LoadDocument (metadataQuery);
}

void LoadDocument (NSMetadataQuery metadataQuery)
{
    Console.WriteLine ("LoadDocument");

    if (metadataQuery.ResultCount == 1) {
        var item = (NSMetadataItem)metadataQuery.ResultAtIndex (0);
        var url = (NSUrl)item.ValueForAttribute (NSMetadataQuery.ItemURLKey);
        doc = new MonkeyDocument (url);

        doc.Open (success => {
            if (success) {
                Console.WriteLine ("iCloud document opened");
                Console.WriteLine (" -- {0}", doc.DocumentString);
                viewController.DisplayDocument (doc);
            } else {
                Console.WriteLine ("failed to open iCloud document");
            }
        });
    } // TODO: if no document, we need to create one
}
```

### <a name="displaying-icloud-documents"></a>显示 iCloud 文档

显示 UIDocument 不应为任何其他与其他任何模型类
- 属性将显示在 UI 控件中，可能是由用户编辑，然后写回模型。

在示例**iCloudUIDoc\MonkeyDocumentViewController.cs** MonkeyDocument 中显示的文本`UITextView`。 `ViewDidLoad` 侦听发送的通知`MonkeyDocument.LoadFromContents`方法。 `LoadFromContents` 时，调用 iCloud 具有新数据的文件，以便通知表明文档已更新。

```csharp
NSNotificationCenter.DefaultCenter.AddObserver (this,
    new Selector ("dataReloaded:"),
    new NSString ("monkeyDocumentModified"),
    null
);
```

示例代码通知处理程序调用的方法来更新 UI-在这种情况下无需任何冲突检测或解决方法。

```csharp
[Export ("dataReloaded:")]
void DataReloaded (NSNotification notification)
{
    doc = (MonkeyDocument)notification.Object;
    // we just overwrite whatever was being typed, no conflict resolution for now
    docText.Text = doc.DocumentString;
}
```

### <a name="saving-icloud-documents"></a>正在保存 iCloud 文档

若要添加到 iCloud 可以调用 UIDocument`UIDocument.Save`直接 （适用于新的文档） 或移动现有的文件使用`NSFileManager.DefaultManager.SetUbiquitious`。 示例代码直接使用此代码在无处不在容器中创建一个新文档 (有两个完成处理程序在这里，一个用于`Save`操作，另一个用于打开):

```csharp
var docsFolder = Path.Combine (iCloudUrl.Path, "Documents"); // NOTE: Documents folder is user-accessible in Settings
var docPath = Path.Combine (docsFolder, MonkeyDocFilename);
var ubiq = new NSUrl (docPath, false);
var monkeyDoc = new MonkeyDocument (ubiq);
monkeyDoc.Save (monkeyDoc.FileUrl, UIDocumentSaveOperation.ForCreating, saveSuccess => {
Console.WriteLine ("Save completion:" + saveSuccess);
if (saveSuccess) {
    monkeyDoc.Open (openSuccess => {
        Console.WriteLine ("Open completion:" + openSuccess);
        if (openSuccess) {
            Console.WriteLine ("new document for iCloud");
            Console.WriteLine (" == " + monkeyDoc.DocumentString);
            viewController.DisplayDocument (monkeyDoc);
        } else {
            Console.WriteLine ("couldn't open");
        }
    });
} else {
    Console.WriteLine ("couldn't save");
}
```

对文档的后续更改不"保存"直接，而是我们告诉`UIDocument`已更改的`UpdateChangeCount`，并且它将自动计划保存到磁盘操作：

```csharp
doc.UpdateChangeCount (UIDocumentChangeKind.Done);
```

### <a name="managing-icloud-documents"></a>管理 iCloud 文档

用户可以管理中的 iCloud 文档**文档**目录的"无处不在容器"设置; 通过在应用程序外部他们可以查看的文件列表和轻扫来删除。 应用程序代码应能够处理这种情况被用户删除文档的位置。 不存储中的内部应用程序数据**文档**目录。

 [![](introduction-to-icloud-images/icloudstorage.png "管理 iCloud 文档工作流")](introduction-to-icloud-images/icloudstorage.png#lightbox)



当用户尝试从其设备，以通知他们的 iCloud 文档与该应用程序相关的状态中删除 iCloud 启用应用程序时，用户也会收到不同的警告。

 [![](introduction-to-icloud-images/icloud-delete1.png "示例对话框时，用户尝试从其设备中删除 iCloud 启用应用程序")](introduction-to-icloud-images/icloud-delete1.png#lightbox)

 [![](introduction-to-icloud-images/icloud-delete2.png "示例对话框时，用户尝试从其设备中删除 iCloud 启用应用程序")](introduction-to-icloud-images/icloud-delete2.png#lightbox)

## <a name="icloud-backup"></a>iCloud 备份

虽然备份到 iCloud 不是一项功能，开发人员直接访问，设计你的应用程序的方式可能会影响用户体验。
Apple 提供[iOS 数据存储准则](https://developer.apple.com/icloud/documentation/data-storage/)开发人员在其 iOS 应用程序跟踪。

最重要的考虑因素是内容的您的应用程序是否将不是内容的用户生成的 （例如，存储 hundred-plus 兆字节为单位的每个问题的杂志读者应用程序） 的大型文件存储。 Apple 倾向于不存储此类数据，它将为备份到 iCloud 并不必要地填充用户的 iCloud 配额。

存储大量此类数据的应用程序应要么将其存储在一个备份 （例如不是用户目录。 缓存或 tmp） 或使用`NSFileManager.SetSkipBackupAttribute`要应用于这些文件的一个标志，以便 iCloud 备份操作过程中忽略它们。

## <a name="summary"></a>总结

本文介绍了新的 iCloud 功能包含在 iOS 5。 它会检查要求若要将项目配置为使用 iCloud，然后提供有关如何实现 iCloud 功能的示例的步骤。

键-值存储的示例演示了如何使用 iCloud 存储少量数据存储 NSUserPreferences 的方式类似。 UIDocument 示例演示了如何使用更多复杂的数据可以存储并跨多个设备通过 iCloud 同步。

最后，它包含添加的 iCloud 备份应如何影响应用程序设计的简要讨论。



## <a name="related-links"></a>相关链接

- [介绍到 iCloud （示例）](https://developer.xamarin.com/samples/monotouch/IntroductionToiCloud)
- [iCloud 研讨会示例代码](https://github.com/xamarin/Seminars/tree/master/2012-03-22-iCloud)
- [iCloud 研讨会幻灯片](https://www.slideshare.net/Xamarin/using-icloud-with-monotouch)
- [iCloud NSUbiquitousKeyValueStore](https://developer.apple.com/library/prerelease/ios/)
- [iCloud 存储](https://support.apple.com/kb/HT4847)
