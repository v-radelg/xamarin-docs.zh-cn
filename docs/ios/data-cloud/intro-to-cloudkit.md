---
title: 在 Xamarin.iOS CloudKit
description: 本文档介绍如何使用 Xamarin.iOS 中 CloudKit。 它提供 CloudKit 的概述，并讨论了如何启用它 CloudKit 方便 API、 可伸缩性、 用户帐户，以及开发和生产环境。
ms.prod: xamarin
ms.assetid: 66B207F2-FAA0-4551-B43B-3DB9F620C397
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/11/2016
ms.openlocfilehash: 8ef12c8b0822f3d0486f584878f572a266b0d44e
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67831866"
---
# <a name="cloudkit-in-xamarinios"></a>在 Xamarin.iOS CloudKit

CloudKit 框架简化了开发应用程序的访问 iCloud。 这包括应用程序数据和资产的权限，以及能够安全地存储应用程序信息的检索。 此工具包通过允许访问其 iCloud Id 与应用程序而无需共享个人信息，使用户匿名的层。

开发人员可以专注于其客户端应用程序，并让 iCloud 无需编写服务器端应用程序逻辑。 CloudKit 提供身份验证、 专用和公用数据库和结构化的数据和资产存储服务。

> [!IMPORTANT]
> Apple [提供工具](https://developer.apple.com/support/allowing-users-to-manage-data/)，用于帮助开发人员正确处理欧盟一般数据保护条例 (GDPR)。

## <a name="requirements"></a>要求

以下被所要完成本文中介绍的步骤：

-  **Xcode 和 iOS SDK** – Apple 的 Xcode 和 iOS 8 Api 需要安装并配置开发人员的计算机上。
-  **Visual Studio for Mac** – 应安装并配置用户设备上的 Visual Studio for Mac 的最新版本。
-  **iOS 8 设备**– 运行 iOS 8 的测试的最新版本的 iOS 设备。

## <a name="what-is-cloudkit"></a>什么是 CloudKit？

CloudKit 是到 iCloud 服务器使开发人员可以访问的方法。 为 iCloud 驱动器和 iCloud 照片库，它提供了基础。 CloudKit 适用于 Mac OS X 和 Apple iOS 设备。

 [![](intro-to-cloudkit-images/image1.png "如何在 Mac OS X 和 Apple iOS 设备上支持 CloudKit")](intro-to-cloudkit-images/image1.png#lightbox)

CloudKit 使用 iCloud 帐户基础结构。 如果没有用户登录到 iCloud 帐户在设备上，CloudKit 将使用其 ID 来标识用户。 如果没有帐户可用，则将提供有限的只读访问权限。

CloudKit 支持公共和专用数据库的概念。 公共数据库提供用户有权访问的所有数据"代号的语言"。 专用数据库用于存储专用数据绑定到特定用户。

CloudKit 支持结构化和大容量数据。 它很强的无缝地处理大型文件传输。 CloudKit 负责高效地传输大型文件到和从 iCloud 服务器在后台，使开发人员可以专注于其他任务。

> [!NOTE]
> 是要特别注意 CloudKit_传输技术_。 它不提供任何持久性;它仅使应用程序能够发送和接收来自服务器的信息有效。

在撰写本文时，Apple 会最初向 CloudKit 免费提供高带宽和存储容量限制。 对于较大项目或具有大量用户的应用程序，Apple 已都曾提及过将提供经济实惠的定价小数位数。


## <a name="enabling-cloudkit-in-a-xamarin-application"></a>在 Xamarin 应用程序中启用 CloudKit

Xamarin 应用程序可以利用 CloudKit Framework 之前，必须正确配置应用程序中所述[使用功能](~/ios/deploy-test/provisioning/capabilities/icloud-capabilities.md)并[使用权利](~/ios/deploy-test/provisioning/entitlements.md)指南

1.  在 Visual Studio for Mac 或 Visual Studio 打开的项目。
2.  在中**解决方案资源管理器**，打开**Info.plist**文件，并确保**捆绑包标识符**中定义相匹配**应用 ID**创建部分预配的设置：
 
    [![](intro-to-cloudkit-images/image26a.png "输入捆绑标识符")](intro-to-cloudkit-images/image26a-orig.png#lightbox "Info.plist file displaying Bundle Identifier")

3.  向下的滚动到底部**Info.plist**文件，然后选择**已启用后台模式**，**位置更新**和**远程通知**:

    [![](intro-to-cloudkit-images/image27a.png "选择已启用的后台模式、 位置更新和远程通知")](intro-to-cloudkit-images/image27a-orig.png#lightbox "Info.plist file displaying background modes")
4.  右键单击 iOS 项目在解决方案中，选择**选项**。
5.  选择**iOS 捆绑签名**，选择**开发人员标识**并**预配配置文件**上述步骤中创建。
6.  请确保**Entitlements.plist**包括**启用 iCloud** ，**键-值存储**并**CloudKit** 。
7.  请确保**无处不在容器**存在应用程序 （如上面创建）。 示例：`iCloud.com.your-company.CloudKitAtlas`
8.  保存对文件所做的更改。


在准备好这些设置，该应用程序现在已准备好访问 CloudKit Framework Api。

## <a name="cloudkit-api-overview"></a>CloudKit API 概述

在实施之前 CloudKit Xamarin iOS 应用程序中，这篇文章将介绍 CloudKit 框架，它将包括以下主题的基础知识：

1.  **容器**– 隔离 iCloud 通信的接收器。
2.  **数据库**– Public 和 private 可供该应用程序。
3.  **记录**– 与 CloudKit 结构化的数据移动的机制。
4.  **记录区域**– 是一组记录。
5.  **记录标识符**– 完全标准化和表示该记录的特定位置。
6.  **引用**– 提供给定数据库内的相关记录之间的关系的父-子。
7.  **资产**– 允许的大型、 非结构化数据上传到 iCloud 并与给定的记录关联的文件。


### <a name="containers"></a>容器

始终运行给定的 iOS 设备上运行的应用程序以及其他应用程序和该设备上的服务端。 在客户端设备上，应用程序将孤立或以某种方式沙盒。 在某些情况下，这是文本沙盒，其他情况下，应用程序只需在其中运行的是自己的内存空间。

获取客户端应用程序并运行它与其他客户端分离的概念是非常强大，可提供以下优势：

1.  **安全**– 一个应用程序不能干扰其他客户端应用程序或操作系统本身。
1.  **稳定性**– 如果客户端应用程序发生故障，它不能采用出操作系统的其他应用。
1.  **隐私**– 每个客户端应用程序具有有限访问存储在设备中的个人信息。


CloudKit 旨在提供相同的好处，如上面列出，并将其应用到使用基于云的信息：

 [![](intro-to-cloudkit-images/image31.png "CloudKit 应用程序的通信使用容器")](intro-to-cloudkit-images/image31.png#lightbox)

就像应用程序正在的多设备上运行因此是与 iCloud 多的应用程序的通信。 每个这些不同的通信真空区被称为容器。

容器中通过 CloudKit 框架公开`CKContainer`类。 默认情况下，一个容器和此容器的一个应用程序将会话承受程度分离该应用程序的数据。 这意味着，多个应用程序可以存储信息与同一 iCloud 帐户，同时还可将混合永远不会出现此信息。

ICloud 数据的容器化还允许 CloudKit 封装用户信息。 这样一来，应用程序将具有一些的受限的访问权限的 iCloud 帐户和用户信息存储中，同时仍保护隐私和用户的安全。

容器完全托管的应用程序通过 ww，DR 门户开发人员。 容器的命名空间是全局跨所有 Apple 开发人员，因此容器必须不只是唯一的给定开发人员的应用程序内容，但所有 Apple 开发人员和应用程序。

Apple 建议在创建用于应用程序容器的命名空间时使用反向 DNS 表示法。 示例:

```csharp
iCloud.com.company-name.application-name
```

虽然容器，默认情况下，绑定到给定的应用程序的一对一，它们可以在共享跨应用程序。 使多个应用程序可以协调单个容器上。 单个应用程序还可以与多个容器。

### <a name="databases"></a>数据库

CloudKit 的主要功能之一是利用应用程序的数据模型和复制该模型最多 iCloud 服务器。 某些信息适用于创建它的用户、 其他信息是以公共方式使用 （如餐馆评论），用户可以创建的公共数据或它可能是开发人员已发布的应用程序的信息。 在任一情况下，受众是不只是单个用户，但社区的人员。

 [![](intro-to-cloudkit-images/image32.png "CloudKit 容器关系图")](intro-to-cloudkit-images/image32.png#lightbox)

在容器内部首先是公共的数据库。 这是其中的所有公共信息位于和共同 mingles。 此外，还有多个单个专用应用程序的每个用户数据库。

IOS 设备上运行时，应用程序才会对当前登录的 iCloud 用户访问信息。 因此，容器的应用程序的视图将按如下所示：

 [![](intro-to-cloudkit-images/image33.png "容器的应用程序视图")](intro-to-cloudkit-images/image33.png#lightbox)

它仅可以看到公共数据库和与当前登录的 iCloud 用户关联的专用数据库。

在通过 CloudKit Framework 中公开数据库`CKDatabase`类。 每个应用程序有权访问两个数据库： 专用和公共数据库。

容器是 CloudKit 初始入口点。 下面的代码可以用于从应用程序的默认容器访问的公共和专用数据库：

```csharp
using CloudKit;
...

public CKDatabase PublicDatabase { get; set; }
public CKDatabase PrivateDatabase { get; set; }
...

// Get the default public and private databases for
// the application
PublicDatabase = CKContainer.DefaultContainer.PublicCloudDatabase;
PrivateDatabase = CKContainer.DefaultContainer.PrivateCloudDatabase;
```

以下是数据库类型之间的差异：

||公共数据库|专用数据库|
|---|--- |--- |
|**数据类型**|共享的数据|当前用户的数据|
|**Quota**|在开发人员的配额|在用户的配额|
|**默认权限**|可读的世界|用户可读|
|**编辑权限**|通过记录类级别的 iCloud 仪表板角色|不可用|

### <a name="records"></a>记录

容器存放数据库，并在内部数据库是记录。 记录是与 CloudKit 结构化的数据移动的机制：

 [![](intro-to-cloudkit-images/image34.png "容器存放数据库，并在内部数据库是记录")](intro-to-cloudkit-images/image34.png#lightbox)

在通过 CloudKit Framework 中公开记录`CKRecord`类，该类包装键-值对。 应用程序中的对象的实例是等效于`CKRecord`CloudKit 中。 此外，每个`CKRecord`拥有一个记录类型，等效于对象的类。

记录具有中实时的架构，因此关闭在提交以进行处理之前的数据到 CloudKit 进行了描述。 从该点 CloudKit 将解释的信息和处理逻辑的存储和检索记录。

`CKRecord`类还支持范围广泛的元数据。 例如，一条记录包含有关创建时和创建它的用户的信息。 一条记录还包含有关上次修改时间和修改它的用户的信息。

记录包含更改标记的概念。 这是记录的以前版本的给定的修订版本。 更改标记使用轻量来确定是否在客户端和服务器具有相同版本的给定的记录。

如上所述，`CKRecords`包装键-值对，并在这种情况下，以下类型的数据可以存储在一条记录：

1.   `NSString`
1.   `NSNumber`
1.   `NSData`
1.   `NSDate`
1.   `CLLocation`
1.   `CKReferences`
1.   `CKAssets`


一个值类型，除了一条记录可以包含任何上述所列类型的一个同构数组。

下面的代码可用于创建新记录并将其存储在数据库中：

```csharp
using CloudKit;
...

private const string ReferenceItemRecordName = "ReferenceItems";
...

var newRecord = new CKRecord (ReferenceItemRecordName);
newRecord ["name"] = (NSString)nameTextField.Text;
await CloudManager.SaveAsync (newRecord);
```

### <a name="record-zones"></a>记录区域

记录给定数据库内不存在本身 – 在记录区域内一起存在的组的记录。 在传统的关系数据库中，记录区域可以是看作表：

 [![](intro-to-cloudkit-images/image35.png "在记录区域内一起存在的组的记录")](intro-to-cloudkit-images/image35.png#lightbox)

可以在给定的记录区域内的多个记录和给定数据库内的多个记录区域。 每个数据库包含默认记录区域：

 [![](intro-to-cloudkit-images/image36.png "每个数据库包含默认记录区域和自定义区域")](intro-to-cloudkit-images/image36.png#lightbox)

这是默认情况下存储记录的位置。 此外，可以创建自定义记录的区域。 记录完成的原子性提交和更改跟踪的基粒度的区域表示。

## <a name="record-identifiers"></a>记录标识符

记录标识符表示为元组，其中包含客户端提供记录名称和区域存在该记录。 记录标识符具有以下特征：

-  在创建客户端应用程序。
-  它们完全规范化，表示记录的特定位置。
-  通过为记录名称分配外的数据库中记录的唯一 ID，它们可以用于桥接不会存储 CloudKit 中的本地数据库。


当开发人员创建新记录时，他们可以选择传入记录标识符。 如果未指定记录标识符，将自动创建并分配给记录 UUID。

当开发人员创建新的记录标识符时，它们可以选择指定每个记录都将从属记录区域。 如果未指定，将使用默认记录区域。

记录标识符公开通过 CloudKit Framework 中`CKRecordID`类。 下面的代码可以用于创建新的记录标识符：

```csharp
var recordID =  new CKRecordID("My Record");
```

### <a name="references"></a>参考资料

引用提供给定数据库内的相关记录之间的关系：

 [![](intro-to-cloudkit-images/image37.png "引用提供给定数据库内的相关记录之间的关系")](intro-to-cloudkit-images/image37.png#lightbox)

在上述示例中，父项，以便父记录的子记录，该子级则拥有子级。 关系将从子记录转到父记录并被称为*返回引用*。

在通过 CloudKit Framework 中公开引用`CKReference`类。 它们是一种方法让 iCloud 服务器了解记录之间的关系。

引用提供级联删除背后的机制。 如果父记录从数据库中删除，自动将从以及数据库中删除任何子记录 （在关系中指定）。

> [!NOTE]
> 无关联的指针是可能使用 CloudKit 时。 例如，应用程序已提取的记录指针的列表中，选择一条记录，并请求的记录时，记录可能不再存在的数据库中。 应用程序必须进行编码以适当地处理这种情况。

尽管不要求这样做，使用 CloudKit 框架时返回的引用是首选。 Apple 已进行微调以使此引用的最有效的类型系统。

时创建的引用，开发人员可以提供已在内存中的记录或创建记录标识符的引用。 如果在数据库中，使用记录标识符和指定的引用不存在，将创建无关联的指针。

下面是创建针对已知的记录的引用的示例：

```csharp
var reference = new CKReference(newRecord, new CKReferenceAction());
```

### <a name="assets"></a>资产

对于大型的、 非结构化数据上传到 iCloud 并与给定的记录关联的文件允许资产：

 [![](intro-to-cloudkit-images/image38.png "对于大型的、 非结构化数据上传到 iCloud 并与给定的记录关联的文件允许资产")](intro-to-cloudkit-images/image38.png#lightbox)

在客户端上`CKRecord`创建用于描述要上传到 iCloud 服务器上的文件。 一个`CKAsset`创建了包含该文件并链接到描述该记录。

时将文件上载到服务器，记录放置在数据库中，并且将文件复制到一个特殊的大容量存储数据库。 指针的记录和上传的文件之间创建链接。

资产中通过 CloudKit 框架公开`CKAsset`类，用于存储大型的、 非结构化数据。 因为开发人员永远不会想要在内存中具有大型的、 非结构化数据，资产是使用磁盘上的文件实现的。

允许从 iCloud 用作一个指针，该记录要检索的资产的记录属于资产。 在这种方式在服务器中删除拥有资产的记录时可以垃圾收集的资产。

因为`CKAssets`旨在处理大型数据文件，Apple 设计 CloudKit 高效上传和下载的资产。

下面的代码可用于创建资产并将其与记录相关联：

```csharp
var fileUrl = new NSUrl("LargeFile.mov");
var asset = new CKAsset(fileUrl);
newRecord ["name"] = asset;
```

现在，我们已介绍的所有内 CloudKit 基本对象。 容器是关联到应用程序，包含数据库。 数据库包含分组到记录区域和记录标识符所指向的记录。 使用引用的记录之间定义父-子关系。 最后，可以上传大型文件并将其关联到使用资产的记录。

## <a name="cloudkit-convenience-api"></a>CloudKit Convenience API

Apple 提供两个不同的 API 集，用于处理 CloudKit:

-  **操作 API** – 提供 CloudKit 每个单个功能。 对于更复杂的应用程序，此 API 提供对 CloudKit 进行精细控制。
-  **Api** – 提供常见的预配置 CloudKit 功能的子集。 它用于在 iOS 应用程序中包含 CloudKit 功能提供方便的便捷地访问解决方案。


方便起见 API 通常是大多数 iOS 应用程序的最佳选择，它从开始，Apple 提供建议。 本部分的其余部分将介绍以下方便 API 主题：

-  正在保存记录。
-  正在提取一条记录。
-  更新记录。


### <a name="common-setup-code"></a>常见设置代码

开始使用 CloudKit 方便 API 之前, 没有所需的一些标准设置代码。 通过修改应用程序的启动`AppDelegate.cs`文件，并使其看起来如下所示：

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using Foundation;
using UIKit;
using CloudKit;

namespace CloudKitAtlas
{
    [Register ("AppDelegate")]
    public partial class AppDelegate : UIApplicationDelegate
    {
        #region Computed Properties
        public override UIWindow Window { get; set;}
        public CKDatabase PublicDatabase { get; set; }
        public CKDatabase PrivateDatabase { get; set; }
        #endregion

        #region Override Methods
        public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
        {
            application.RegisterForRemoteNotifications ();

            // Get the default public and private databases for
            // the application
            PublicDatabase = CKContainer.DefaultContainer.PublicCloudDatabase;
            PrivateDatabase = CKContainer.DefaultContainer.PrivateCloudDatabase;

            return true;
        }

        public override void RegisteredForRemoteNotifications (UIApplication application, NSData deviceToken)
        {
            Console.WriteLine ("Registered for Push notifications with token: {0}", deviceToken);
        }

        public override void FailedToRegisterForRemoteNotifications (UIApplication application, NSError error)
        {
            Console.WriteLine ("Push subscription failed");
        }

        public override void ReceivedRemoteNotification (UIApplication application, NSDictionary userInfo)
        {
            Console.WriteLine ("Push received");
        }
        #endregion
    }
}
```

上面的代码公开为快捷方式，以使它们更易于使用的应用程序的其余部分中的公钥和私钥 CloudKit 数据库。

接下来，将以下代码添加到任何视图或视图将使用 CloudKit 的容器：

```csharp
using CloudKit;
...

#region Computed Properties
public AppDelegate ThisApp {
    get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
}
#endregion
```

这会添加快捷方式即可`AppDelegate`并访问上面创建的公共和专用数据库快捷方式。

此代码，让我们看一看在 Xamarin iOS 8 应用程序中实现 CloudKit 方便 API。

### <a name="saving-a-record"></a>正在保存记录

使用讨论记录时，上面介绍的模式，以下代码将创建一个新的记录，并使用方便 API 将其保存到公共数据库：

```csharp
private const string ReferenceItemRecordName = "ReferenceItems";
...

// Create a new record
var newRecord = new CKRecord (ReferenceItemRecordName);
newRecord ["name"] = (NSString)nameTextField.Text;

// Save it to the database
ThisApp.PublicDatabase.SaveRecord(newRecord, (record, err) => {
    // Was there an error?
    if (err != null) {
        ...
    }
});
```

需要注意上面的代码中的三个事项：

1.  通过调用`SaveRecord`方法的`PublicDatabase`，开发人员无需指定如何发送数据，它被编写为等哪些区域。方便起见 API 会负责所有这些详细信息本身。
1.  在调用是异步的与成功或失败而在调用完成时提供的回调例程。 如果调用失败，将提供一条错误消息。
1.  CloudKit 不提供本地存储/暂留;它是仅一个传输媒介。 因此当发出请求来保存记录，它被立即发送到 iCloud 服务器。


> [!NOTE]
> "丢失"的性质移动网络通信，其中连接不断地删除或中断，开发人员必须进行错误处理使用 CloudKit 时的第一个考虑因素之一。

### <a name="fetching-a-record"></a>获取记录

包含创建并已成功存储在 iCloud 服务器上一记录，使用以下代码以检索的记录：

```csharp
// Create a record ID and fetch the record from the database
var recordID = new CKRecordID("MyRecordName");
ThisApp.PublicDatabase.FetchRecord(recordID, (record, err) => {
    // Was there an error?
    if (err != null) {
        ...
    }
});
```

如同保存该记录，上面的代码是异步的简单，需要很好的错误处理。

### <a name="updating-a-record"></a>更新记录

已从 iCloud 服务器中提取一条记录后，可以使用下面的代码修改的记录并将所做的更改保存回数据库：

```csharp
// Create a record ID and fetch the record from the database
var recordID = new CKRecordID("MyRecordName");
ThisApp.PublicDatabase.FetchRecord(recordID, (record, err) => {
    // Was there an error?
    if (err != null) {

    } else {
        // Modify the record
        record["name"] = (NSString)"New Name";

        // Save changes to database
        ThisApp.PublicDatabase.SaveRecord(record, (r, e) => {
            // Was there an error?
            if (e != null) {
                 ...
            }
        });
    }
});
```

`FetchRecord`方法`PublicDatabase`返回`CKRecord`如果调用成功。 然后，应用程序修改的记录和调用`SaveRecord`再次将所做的更改写回到数据库。

本部分说明了使用 CloudKit 方便 API 时，将使用应用程序的典型周期。 应用程序会将记录保存到 iCloud、 从 iCloud 检索这些记录、 修改的记录和将这些更改保存回 iCloud。

## <a name="designing-for-scalability"></a>实现可伸缩性设计

到目前为止本文已介绍了存储和检索应用程序的整个对象模型从 iCloud 服务器，每次它会与合作。 虽然这种方法适用于少量的数据和非常小的用户群，其可伸缩性差时的信息和/或用户量基增加。

### <a name="big-data-tiny-device"></a>大数据、 小型设备

更多热门的应用程序变得，在数据库中的多个数据和该设备上的整个数据的缓存是不可行。 可以使用以下方法来解决此问题：

-  **将大型数据保留在云中**– CloudKit 已设计为高效地处理大型数据。
-  **客户端应只查看该数据的切片**– 关闭数据在给定时间处理的任何任务所需的最低。
-  **客户端视图可以更改**– 因为每个用户具有不同的首选项、 要显示的数据切片可以更改用户和用户的单个任何的视图给定切片可以不同。
-  **客户端使用的查询可以将精力集中视点**– 查询允许用户在云内查看存在较大数据集的一小部分。


### <a name="queries"></a>查询

如前所述，查询允许开发人员可以选择存在于云中的大数据集的一小部分。 查询中通过 CloudKit 框架公开`CKQuery`类。

查询结合了三个不同的事物： 记录类型 ( `RecordType`)，谓词 ( `NSPredicate`) 和 （可选） 一个排序描述符 ( `NSSortDescriptors`)。 CloudKit 支持的大多数`NSPredicate`。

#### <a name="supported-predicates"></a>受支持的谓词

CloudKit 支持以下类型的`NSPredicates`处理查询时：


1. 匹配名称等于存储在变量中的值的记录：
    
    ```
    NSPredicate.FromFormat(string.Format("name = '{0}'", recordName))
    ```
   
2. 允许匹配基于动态密钥值，以便该密钥无需在编译时知道：
    
    ```
    NSPredicate.FromFormat(string.Format("{0} = '{1}'", key, value))
    ```
    
3. 匹配记录的值大于给定的值的记录：
   
    ```
    NSPredicate.FromFormat(string.Format("start > {0}", (NSDate)date))
    ```

4. 匹配记录的位置在 100 米的给定位置的记录：
    
    ```
    var location = new CLLocation(37.783,-122.404);
    var predicate = NSPredicate.FromFormat(string.Format("distanceToLocation:fromLocation(Location,{0}) < 100", location));
    ```

5. CloudKit 支持已标记化的搜索。 此调用将创建两个标记，一个用于`after`，另一个用于`session`。 它将返回一个包含这些两个令牌的记录：
    
    ```
    NSPredicate.FromFormat(string.Format("ALL tokenize({0}, 'Cdl') IN allTokens", "after session"))
    ```
    
6. CloudKit 支持复合使用联接谓词`AND`运算符。
    
    ```
    NSPredicate.FromFormat(string.Format("start > {0} AND name = '{1}'", (NSDate)date, recordName))
    ```
    


#### <a name="creating-queries"></a>创建查询

下面的代码可用于创建`CKQuery`Xamarin iOS 8 应用程序中：

```csharp
var recordName = "MyRec";
var predicate = NSPredicate.FromFormat(string.Format("name = '{0}'", recordName));
var query = new CKQuery("CloudRecords", predicate);
```

首先，它创建一个谓词，选择与给定名称匹配的记录。 然后创建一个查询，将选择给定记录类型的谓词相匹配的记录。

#### <a name="performing-a-query"></a>执行查询

一旦创建查询后，使用以下代码来执行查询并处理返回的记录：

```csharp
var recordName = "MyRec";
var predicate = NSPredicate.FromFormat(string.Format("name = {0}", recordName));
var query = new CKQuery("CloudRecords", predicate);

ThisApp.PublicDatabase.PerformQuery(query, CKRecordZone.DefaultRecordZone().ZoneId, (NSArray results, NSError err) => {
    // Was there an error?
    if (err != null) {
       ...
    } else {
        // Process the returned records
        for(nint i = 0; i < results.Count; ++i) {
            var record = (CKRecord)results[i];
        }
    }
});
```

上面的代码生成上面创建查询，并针对公共数据库执行。 由于未记录指定区域，将搜索所有区域。 如果未不发生任何错误，数组`CKRecords`将匹配的查询参数返回。

考虑查询的方法是它们是轮询，并且是适合于通过大型数据集进行切片。 查询，但是，不是很适合主要是静态的大型数据集的原因如下：

-  它们是错误的设备的电池使用寿命。
-  它们是错误的网络流量。
-  它们是错误的用户体验，因为他们将看到的信息受到应用程序何种频率轮询数据库。 用户现在希望发生更改时获得推送通知。


### <a name="subscriptions"></a>订阅

在处理时通常处于静态的大型数据集，不应在客户端设备上执行查询，它应代表客户端的服务器上运行。 查询应在后台中运行，并保存每个记录，由当前的设备或另一台设备涉及同一个数据库后应执行。

最后，应在运行服务器端查询时的数据库上每台设备发送推送通知。

订阅中通过 CloudKit 框架公开`CKSubscription`类。 它们组合了一种记录类型 ( `RecordType`)，谓词 ( `NSPredicate`) 和 Apple 推送通知 ( `Push`)。

> [!NOTE]
> 因为它们包含的有效负载包含 CloudKit 特定信息，如是什么原因导致推送发生这种情况，是略有增加 CloudKit 推送。

#### <a name="how-subscriptions-work"></a>订阅的工作原理

实现中的订阅之前C#代码，让我们快速了解订阅的工作原理：

 [![](intro-to-cloudkit-images/image39.png "订阅的工作方式的概述")](intro-to-cloudkit-images/image39.png#lightbox)

上面的关系图显示了典型订阅过程，如下所示：

1.  客户端设备创建新的订阅包含将触发的订阅和将触发器发生时发送推送通知的条件集。
2.  订阅是发送到数据库，其中它被添加到现有订阅的集合。
3.  第二个设备创建新记录，并将该记录保存到数据库。
4.  在数据库中搜索其新的记录是否与任何其条件匹配的订阅的列表。
5.  如果找到匹配项，则将推送通知发送到导致其触发的记录有关的信息注册了订阅的设备。


在位置了解这一点，让我们看一下在 Xamarin iOS 8 的应用程序创建订阅。

#### <a name="creating-subscriptions"></a>创建订阅

下面的代码可以用于创建订阅：

```csharp
// Create a new subscription
DateTime date;
var predicate = NSPredicate.FromFormat(string.Format("start > {0}", (NSDate)date));
var subscription = new CKSubscription("RecordType", predicate, CKSubscriptionOptions.FiresOnRecordCreation);

// Describe the type of notification
var notificationInfo = new CKNotificationInfo();
notificationInfo.AlertLocalizationKey = "LOCAL_NOTIFICATION_KEY";
notificationInfo.SoundName = "ping.aiff";
notificationInfo.ShouldBadge = true;

// Attach the notification info to the subscription
subscription.NotificationInfo = notificationInfo;
```

首先，它创建一个谓词，提供要触发订阅的条件。 接下来，它创建针对特定记录类型的订阅，并设置在测试该触发器时的选项。 最后，它定义订阅会触发，并将其附加订阅时，将发生的通知的类型。

### <a name="saving-subscriptions"></a>正在保存订阅

创建的订阅，使用下面的代码会将其保存到数据库：

```csharp
// Save the subscription to the database
ThisApp.PublicDatabase.SaveSubscription(subscription, (s, err) => {
    // Was there an error?
    if (err != null) {

    }
});
```

使用方便 API 时，调用是异步的简单，并提供简单错误处理。

#### <a name="handling-push-notifications"></a>处理推送通知

如果开发人员具有以前使用 Apple 推送通知 (AP)，处理生成的 CloudKit 通知的过程应该熟悉。

在中`AppDelegate.cs`，重写`ReceivedRemoteNotification`类，如下所示：

```csharp
public override void ReceivedRemoteNotification (UIApplication application, NSDictionary userInfo)
{
    // Parse the notification into a CloudKit Notification
    var notification = CKNotification.FromRemoteNotificationDictionary (userInfo);

    // Get the body of the message
    var alertBody = notification.AlertBody;

    // Was this a query?
    if (notification.NotificationType == CKNotificationType.Query) {
        // Yes, convert to a query notification and get the record ID
        var query = notification as CKQueryNotification;
        var recordID = query.RecordId;
    }
}
```

上面的代码会要求 CloudKit 将解析为 CloudKit 通知的用户信息。 接下来，有关该警报提取信息。 最后，测试通知的类型，并进行相应处理通知。

本部分说明了如何回答大数据、 查询和订阅使用上面介绍的小型设备问题。 应用程序会将其大型数据保留在云中，并使用这些技术来提供对此数据集。

## <a name="cloudkit-user-accounts"></a>CloudKit 用户帐户

如本文开头所述，CloudKit 是基于现有 iCloud 基础结构。 以下部分将介绍，详细信息，如何向开发人员使用 CloudKit API 公开帐户。

### <a name="authentication"></a>身份验证

在处理时的用户帐户，首先要考虑是身份验证。 CloudKit 支持在设备上的当前登录的 iCloud 用户通过身份验证。 身份验证会在后台发生，并由 iOS 处理。 这样一来，开发人员无需担心如何实现身份验证的详细信息。 它们仅测试以确定是否将用户记录。

### <a name="user-account-information"></a>用户帐户信息

CloudKit 向开发人员提供了以下用户信息：

-  **标识**– 一种方法唯一地标识用户。
-  **元数据**– 能够保存和检索有关用户的信息。
-  **隐私**– 在隐私意识 manor 中处理的所有信息。 除非用户已同意它公开执行任何操作。
-  **发现**– 使用户能够发现他们使用的同一个应用程序的朋友。


接下来，我们将查看这些主题的详细信息。

#### <a name="identity"></a>标识

如上面所述，CloudKit 提供了一种方法来唯一地标识给定的用户的应用程序：

 [![](intro-to-cloudkit-images/image40.png "唯一地确定给定用户")](intro-to-cloudkit-images/image40.png#lightbox)

没有用户的设备和所有 CloudKit 容器内的特定用户专用数据库上运行的客户端应用程序。 将客户端应用程序链接到这些特定用户之一。 这基于用户登录到本地设备上的 iCloud。

由于这来自 iCloud，没有丰富的后备存储区的用户信息。 和 iCloud 实际托管容器，因为它可以关联用户。 在上面的图中，用户的 iCloud 帐户`user@icloud.com`链接到当前的客户端。

基于容器的容器，唯一的随机生成的用户 ID 创建并与用户的 iCloud 帐户 （电子邮件地址） 相关联。 此用户 ID 返回到应用程序，并可以在任何开发人员认为适当的方式。

> [!NOTE]
> 同一 iCloud 用户在同一设备上运行的不同应用程序将具有不同的用户 Id，因为它们已连接到不同的 CloudKit 容器。

以下代码获取 CloudKit 用户 ID 的当前记录在设备上的 iCloud 用户：

```csharp
public CKRecordID UserID { get; set; }
...

// Get the CloudKit User ID
CKContainer.DefaultContainer.FetchUserRecordId ((recordID, err) => {
    // Was there an error?
    if (err!=null) {
        Console.WriteLine("Error: {0}", err.LocalizedDescription);
    } else {
        // Save user ID
        UserID = recordID;
    }
});
```

上面的代码在要求 CloudKit 容器提供当前已登录用户的 ID。 因为此信息来自 iCloud 服务器时，调用是异步的则需进行错误处理。

#### <a name="metadata"></a>元数据

每个用户都 CloudKit 具有描述了这些功能的特定元数据。 此元数据表示为 CloudKit 记录：

 [![](intro-to-cloudkit-images/image41.png "CloudKit 中的每个用户具有描述了这些功能的特定元数据")](intro-to-cloudkit-images/image41.png#lightbox)

查找专用数据库内存在的特定用户是容器的一条记录，用于定义该用户。 有公共数据库，另一个用于每个用户的容器内的多个用户记录。 其中一种将具有与当前登录用户的记录 id。 匹配的记录 ID

公共数据库中的用户记录均可读的世界。 它们将处理，大多数情况下，与普通记录，并且有一种类型的`CKRecordTypeUserRecord`。 这些记录由系统保留并不是可用于查询。

使用以下代码来访问用户记录：

```csharp
public CKRecord UserRecord { get; set; }
...

// Get the user's record
PublicDatabase.FetchRecord(UserID, (record ,er) => {
    //was there an error?
    if (er != null) {
        Console.WriteLine("Error: {0}", er.LocalizedDescription);
    } else {
        // Save the user record
        UserRecord = record;
    }
});
```

上面的代码在要求要返回的是我们访问上面的 ID 的用户的用户记录的公共数据库。 因为此信息来自 iCloud 服务器时，调用是异步的则需进行错误处理。

#### <a name="privacy"></a>隐私

CloudKit 是设计，默认情况下，当前登录用户的隐私性。 默认情况下不公开有关用户的任何个人标识信息。 有某些情况下，应用程序需要有关用户的有限的信息。

在这些情况下，应用程序可以请求用户披露此信息。 将显示一个对话框，向用户询问他们要参加公开其帐户信息。

#### <a name="discovery"></a>发现

假设中选择允许应用程序的用户限制到他们的用户帐户信息的访问权限，他们可以向其他用户的应用程序的可发现：

 [![](intro-to-cloudkit-images/image42.png "用户可以向其他用户的应用程序的可发现")](intro-to-cloudkit-images/image42.png#lightbox)

客户端应用程序讨论到的容器，而容器通信 iCloud 访问用户信息。 用户可以提供电子邮件地址，并且发现可用于获取返回有关用户的信息。 （可选） 用户 ID，也可以用来发现有关用户的信息。

CloudKit 还提供了一种方法来发现有关的任何用户都可能是当前已登录的朋友的信息通过查询整个通讯簿 iCloud 用户。 CloudKit 过程将用户的联系人一书中提取和使用的电子邮件地址来看看它是否可以找到这些地址相匹配的应用程序的其他用户。

这样，应用程序以利用用户的联系人书籍，而无需提供对其访问权限或要求用户批准对联系人的访问。 在任何时间的联系信息将提供给应用程序，只有 CloudKit 进程可以访问。

概括来说，有三种不同的输入可用于用户发现：

-  **用户记录 ID** – 发现可以进行针对当前已登录的用户 ID 在 CloudKit 用户。
-  **用户电子邮件地址**– 用户可以提供电子邮件地址，并可用于发现。
-  **请联系书籍**– 用户的通讯簿可用于发现具有相同的电子邮件地址，其联系人中列出的应用程序的用户。


用户发现将返回以下信息：

-  **用户记录 ID** -公共数据库中的用户的唯一 ID。
-  **第一个和最后一个名称**-如存储在公共数据库中。


对于已选择加入到发现的用户才会返回此信息。

下面的代码将发现有关当前已登录到设备上的 iCloud 用户的信息：

```csharp
public CKDiscoveredUserInfo UserInfo { get; set; }
...

// Get the user's metadata
CKContainer.DefaultContainer.DiscoverUserInfo(UserID, (info, e) => {
    // Was there an error?
    if (e != null) {
        Console.WriteLine("Error: {0}", e.LocalizedDescription);
    } else {
        // Save the user info
        UserInfo = info;
    }
});
```

使用以下代码来查询联系人一书中的所有用户：

```csharp
// Ask CloudKit for all of the user's friends information
CKContainer.DefaultContainer.DiscoverAllContactUserInfos((info, er) => {
    // Was there an error
    if (er != null) {
        Console.WriteLine("Error: {0}", er.LocalizedDescription);
    } else {
        // Process all returned records
        for(int i = 0; i < info.Count(); ++i) {
            // Grab a user
            var userInfo = info[i];
        }
    }
});
```

在本部分中，我们已介绍如何 CloudKit 可以提供对应用程序的用户的帐户的访问权限的四个主要区域。 获取用户的标识和元数据，对隐私策略的内置 CloudKit 和最后，若要发现的应用程序的其他用户的功能。

## <a name="the-development-and-production-environments"></a>开发和生产环境

CloudKit 有关应用程序的记录类型和数据提供单独的开发和生产环境。 在开发环境是一个更灵活的环境，仅供开发团队的成员。 当应用程序将新字段添加到一条记录，并将该记录保存在开发环境中时，服务器会自动更新的架构信息。

开发人员可以使用此功能在开发期间，从而可节省时间，对架构进行更改。 一个需要注意的是，一个字段添加到记录后，以编程方式不能更改与该字段关联的数据类型。 若要更改字段的类型，开发人员必须删除中的字段[CloudKit 仪表板](https://icloud.developer.apple.com/dashboard/)并将其再次添加使用新的类型。

在部署之前应用程序，开发人员可以将迁移其架构和数据到生产环境使用**CloudKit 仪表板**。 当对生产环境中运行，服务器将阻止应用程序以编程方式更改架构。 开发人员仍可进行的更改**CloudKit 仪表板**，但尝试将字段添加到在生产环境中导致错误的记录。

> [!NOTE]
> IOS 模拟器仅适用于**开发环境**。 当开发人员已准备好测试中的应用程序**生产环境**，是需要在物理 iOS 设备。


## <a name="shipping-a-cloudkit-enabled-app"></a>传送 CloudKit 启用应用程序

在传送使用 CloudKit 的应用程序，它将需要配置到目标**生产 CloudKit 环境**或 Apple 将拒绝应用程序。

请执行以下操作：

1. 在针对 Ma 的 Visual Studio 中编译的应用程序**发行** > **iOS 设备**: 

    [![](intro-to-cloudkit-images/shipping01.png "编译应用程序发布")](intro-to-cloudkit-images/shipping01.png#lightbox)

2. 从**构建**菜单中，选择**存档**: 

    [![](intro-to-cloudkit-images/shipping02.png "选择存档")](intro-to-cloudkit-images/shipping02.png#lightbox)

3. **存档**将创建并显示在 Visual Studio for Mac: 

    [![](intro-to-cloudkit-images/shipping03.png "将创建并显示存档")](intro-to-cloudkit-images/shipping03.png#lightbox)

4. 启动 **Xcode**。
5. 从**窗口**菜单中，选择**组织者**: 

    [![](intro-to-cloudkit-images/shipping04.png "选择组织程序")](intro-to-cloudkit-images/shipping04.png#lightbox)

6. 选择应用程序的存档，然后单击**导出...** 按钮： 

    [![](intro-to-cloudkit-images/shipping05.png "应用程序的存档")](intro-to-cloudkit-images/shipping05.png#lightbox)
    
7. 选择导出的方法，然后单击**下一步**按钮： 

    [![](intro-to-cloudkit-images/shipping06.png "选择用于导出的方法")](intro-to-cloudkit-images/shipping06.png#lightbox)

8. 选择**开发团队**从下拉列表中单击**选择**按钮： 

    [![](intro-to-cloudkit-images/shipping07.png "从下拉列表中选择的开发团队")](intro-to-cloudkit-images/shipping07.png#lightbox)

9. 选择**生产**从下拉列表中单击**下一步**按钮： 

    [![](intro-to-cloudkit-images/shipping08.png "从下拉列表中选择生产")](intro-to-cloudkit-images/shipping08.png#lightbox)

10. 查看设置，然后单击**导出**按钮： 

    [![](intro-to-cloudkit-images/shipping09.png "查看设置")](intro-to-cloudkit-images/shipping09.png#lightbox)

11. 选择要生成最终的应用程序的位置`.ipa`文件。

用于提交直接到 iTunes Connect 应用程序类似，只需单击**提交...** 按钮而不是在管理器窗口中选择存档后...导出。

## <a name="when-to-use-cloudkit"></a>何时使用 CloudKit

如我们在本文中所见，CloudKit 提供了用于轻松对应用程序来存储和从 iCloud 服务器中检索信息。 话虽如此，CloudKit 不会过时或不推荐使用的任何现有的工具或框架。

### <a name="use-cases"></a>用例

在以下用例应帮助开发人员决定何时使用特定 iCloud 框架或技术：

-  **iCloud 键值存储**– 以异步方式保留少量数据保持最新状态，并且非常适用于使用应用程序首选项。 但是，它会被限定为极少量的信息。
-  **iCloud 驱动器**– 基于现有 iCloud 文档 Api 并提供了一个简单的 API 来同步文件系统中的非结构化的数据。 它在 Mac OS X 上提供完整的脱机缓存并非常适用于文档为中心的应用程序。
-  **iCloud 核心数据**– 允许所有用户的设备之间都复制数据。 数据是单用户，而且非常适合保留专用的结构化数据同步。
-  **CloudKit** – 提供这两个结构的公共数据和大容量，一个是能够处理大型数据集和大型非结构化的文件。 其绑定到用户的 iCloud 帐户，并提供客户端定向的数据传输。


保持这些用例记住、 开发人员应选择正确的 iCloud 技术提供当前的所需应用程序功能，并为未来的发展提供良好的可伸缩性。

## <a name="summary"></a>总结

本文只讨论了 CloudKit API 的快速简介。 它说明了如何设置和配置为使用 CloudKit 的 Xamarin iOS 应用程序。 提到了 CloudKit 方便 API 的功能。 它具有显示如何设计 CloudKit 已启用应用程序的可伸缩性使用查询和订阅。 并且，它具有最后显示 CloudKit 通过向应用程序公开的用户帐户信息。

## <a name="related-links"></a>相关链接

- [CloudKitAtlas (sample)](https://developer.xamarin.com/samples/monotouch/ios8/CloudKitAtlas/)
- [iOS 8 简介](~/ios/platform/introduction-to-ios8.md)
- [创建预配配置文件](~/ios/get-started/installation/device-provisioning/index.md)
