---
title: IOS 6 中对 StoreKit 的更改
description: iOS 6 引入了对应用商店工具包 API 的两个更改： 能够显示 iTunes （和应用商店/ibooks store 色情） 从您的应用程序和一个新，在应用内产品购买 Apple 将在其中托管您可下载文件的选项。 本文档介绍如何实现这些功能使用 Xamarin.iOS。
ms.prod: xamarin
ms.assetid: 253D37D7-44C7-D012-3641-E15DC41C2699
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: 5d1bb5ab636cd7527a560332a9890e9907fac454
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61295650"
---
# <a name="changes-to-storekit-in-ios-6"></a>IOS 6 中对 StoreKit 的更改

_iOS 6 到应用商店工具包 API 引入了两个更改： 能够显示 iTunes （和应用商店/ibooks store 色情） 从您的应用程序和一个新，在应用内产品购买 Apple 将在其中托管您可下载文件的选项。本文档介绍如何实现这些功能使用 Xamarin.iOS。_

存储工具包 iOS6 中的主要更改是这两个新功能：

- **在应用程序内容显示和 Purchasing** – 用户可以购买和下载应用、 音乐、 书籍和其他 iTunes 内容而无需离开您的应用程序。 此外可以链接到你自己的应用是为了推销购买或只是鼓励评审和评级。
- **应用内购买托管内容**– Apple 将存储并交付与您的应用内购买产品，关联不再需要单独的服务器来托管你的文件，自动支持后台下载，可让你的内容编写更少的代码。

请参阅[应用内购买](~/ios/platform/in-app-purchasing/index.md)StoreKit Api 的详细介绍的指南。

## <a name="requirements"></a>要求

本文档中讨论的应用商店套件功能需要 iOS 6 和 Xcode 4.5，以及 Xamarin.iOS 6.0。

## <a name="in-app-content-display--purchasing"></a>应用内显示内容和购买

在 iOS 中新的应用内购买功能允许用户查看产品信息和购买或下载从您的应用程序中的产品。
以前的应用程序必须触发 iTunes、 应用商店或 ibooks store 色情，这将导致用户离开原始应用程序。 这一新功能在完成时，会自动将用户返回到你的应用。

[![](changes-to-storekit-images/image1.png "购买后自动返回到应用程序")](changes-to-storekit-images/image1.png#lightbox)

如何使用此示例包括：

- **鼓励用户对应用评级**–，以便用户可以速率和无需离开它查看您的应用程序，可打开应用商店页面。
- **将跨升级应用**– 允许用户查看的发布，能够立即购买/下载其他应用。
- **帮助用户查找和下载内容**– 帮助用户购买您的应用程序查找、 管理，或 （例如聚合的内容。 一个与音乐相关的应用程序无法提供的歌曲播放列表并允许每首歌曲，若要从应用内购买）。

一次`SKStoreProductViewController`已显示，就好像它们是在 iTunes、 应用商店或 ibooks store 色情可以进行交互的产品信息。 用户可以：

- 视图 （适用于应用程序） 的屏幕截图
- 示例歌曲或视频 （适用于音乐、 电视节目和电影）
- 读取 （和写入） 评论
- 购买和下载，这种情况发生完全在视图控制器和应用商店套件中。

中的某些选项`SKStoreProductViewController`仍将强制用户离开您的应用程序并打开相关应用商店应用，例如上单击**相关产品**或应用程序的**支持**链接。

### <a name="skstoreproductviewcontroller"></a>SKStoreProductViewController

要显示的任何应用内产品的 API 很简单： 它只需要您创建和显示`SKStoreProductViewController`。 请按照下列步骤以创建和显示产品：

1. 创建`StoreProductParameters`对象将参数传递给视图控制器，包括`productId`构造函数中。
1. 实例化`SKProductViewController`。 将其分配给一个类级字段。
1. 将一个处理程序分配到视图控制器的`Finished`事件，应关闭视图控制器。 调用此事件之后，当用户按取消。或否则完成视图控制器内的事务。
1. 调用`LoadProduct`方法并传入`StoreProductParameters`和完成处理程序。 完成处理程序应该检查产品请求已成功，并因此，如果存在`SKProductViewController`以模式方式。 无法检索该产品的情况下，应添加相应的错误处理。

### <a name="example"></a>示例

*ProductView*项目中*StoreKit*本文的示例代码实现`Buy`接受任何产品的方法的 Apple ID 并将显示`SKStoreProductViewController`。 下面的代码显示任何给定的 Apple ID 的产品信息：

```csharp
void Buy (int productId)
{
    var spp = new StoreProductParameters(productId);
    var productViewController = new SKStoreProductViewController ();
    // must set the Finished handler before displaying the view controller
    productViewController.Finished += (sender, err) => {
        // Apple's docs says to use this method to close the view controller
        this.DismissModalViewControllerAnimated (true);
    };
    productViewController.LoadProduct (spp, (ok, err) => { // ASYNC !!!
        if (ok) {
            PresentModalViewController (productViewController, true);
        } else {
            Console.WriteLine (" failed ");
            if (err != null)
                Console.WriteLine (" with error " + err);
        }
    });
}
```

运行 – 完全内出现下载或购买时，应用程序看起来像下面的屏幕截图`SKStoreProductViewController`:

[![](changes-to-storekit-images/image2.png "应用程序如下所示，运行时")](changes-to-storekit-images/image2.png#lightbox)

### <a name="supporting-older-operating-systems"></a>支持较早的操作系统

示例应用程序包括演示如何在 iOS 的早期版本中打开应用商店、 iTunes 或 ibooks store 色情的代码。 使用`OpenUrl`方法打开一个正确的精心编制**itunes.com** URL。

可实现版本检查，以确定要运行的代码，如下所示：

```csharp
if (UIDevice.CurrentDevice.CheckSystemVersion (6,0)) {
    // do iOS6+ stuff, using SKStoreProductViewController as shown above
} else {
    // don't do stuff requiring iOS 6.0, use the old syntax 
    // (which will take the user out of your app)
    var nsurl = new NSUrl("http://itunes.apple.com/us/app/angry-birds/id343200656?mt=8");
    UIApplication.SharedApplication.OpenUrl (nsurl);
}
```

### <a name="errors"></a>错误

如果您使用的 Apple ID 不是有效的因为它可以是令人困惑表示某种类型的网络或身份验证问题，将会发生以下错误。

 `Error Domain=SKErrorDomain Code=5 "Cannot connect to iTunes Store"`

### <a name="reading-objective-c-documentation"></a>阅读 Objective C 文档

阅读有关在 Apple 开发人员门户上的应用商店套件的开发人员将看到协议 – [SKStoreProductViewControllerDelegate](https://developer.apple.com/library/prerelease/ios/#documentation/StoreKit/Reference/SKITunesProductViewControllerDelegate_ProtocolRef/Reference/Reference.html) – 相对于这一新功能曾进行过讨论。 委托协议具有只有一个方法 – productViewControllerDidFinish – 已公开为`Finished`上的事件`SKStoreProductViewController`Xamarin.iOS 中。

## <a name="determining-apple-ids"></a>确定的 Apple Id

所需的 Apple ID`SKStoreProductViewController`是*数*（不用于与混淆捆绑包 Id"com.xamarin.mwc2012"等）。 有多种不同的方式可以找出你想要显示，下面列出的产品的 Apple ID:

### <a name="itunesconnect"></a>iTunesConnect

对于发布的应用程序，它很容易地找到**Apple ID**在 iTunes Connect 中：

[![](changes-to-storekit-images/image3.png "在 iTunes Connect 中查找 Apple ID")](changes-to-storekit-images/image3.png#lightbox)

 <a name="Search_API" />

### <a name="search-api"></a>搜索 API

Apple 提供动态搜索 API，用于查询中应用商店、 iTunes 和 ibooks store 色情的所有产品。 有关如何访问搜索 API 的信息可在[Apple 的关联资源](http://www.apple.com/itunes/affiliates/resources/documentation/itunes-store-web-service-search-api.html)，尽管该 API 公开给任何人 （不只是已注册的附属机构）。 可以生成的 JSON 进行分析，以发现`trackId`，它是 Apple ID 用于`SKStoreProductViewController`。

结果还包括其他元数据，包括显示信息和可用于呈现应用程序中的产品的图片 Url。

下面是一些可能的恶意活动：

- **iBooks 应用**– [ http://itunes.apple.com/search?term=ibooks&amp; 实体 = 软件&amp;国家/地区 = us](http://itunes.apple.com/search?term=ibooks&amp;entity=software&amp;country=us)
- **圆点和袋鼠 iBook** – [ http://itunes.apple.com/search?term=dot+and+the+kangaroo&amp; 实体 = 电子书&amp;国家/地区 = us](http://itunes.apple.com/search?term=dot+and+the+kangaroo&amp;entity=ebook&amp;country=us)

### <a name="enterprise-partner-feed"></a>企业合作伙伴源

Apple 提供了与所有其产品的完整数据转储的可下载数据库就绪平面文件的窗体中已批准的合作伙伴。 如果您有资格访问[企业合作伙伴馈送](http://www.apple.com/itunes/affiliates/resources/documentation/itunes-enterprise-partner-feed.html)，则可在该数据集的任何产品 Apple ID。

许多用户的企业合作伙伴源是的成员[联盟计划](http://www.apple.com/itunes/affiliates)允许以获得对产品销售额的佣金。 `SKStoreProductViewController` 不支持关联 Id （在撰写本文时）。

### <a name="direct-product-links"></a>直接产品链接

可以从其 iTunes 预览 URL 链接推断 Apple ID 的产品。
任何在 iTunes 中 （适用于应用程序、 音乐或书籍） 的产品链接找到以开头的 URL 的一部分`id`，并使用后面的数字。

例如，则 iBooks 直接链接

```csharp
http://itunes.apple.com/us/app/ibooks/id364709193?mt=8
```

Apple ID 是**364709193**。 同样 MWC2012 应用的直接链接是

```csharp
http://itunes.apple.com/us/app/mwc-2012-unofficial/id496963922?mt=8
```

Apple ID 是**496963922**。

## <a name="in-app-purchase-hosted-content"></a>应用内购买托管的内容

如果应用内购买包含可下载内容 （如书籍或其他媒体、 游戏级别艺术和配置或其他大型文件） 则使用这些文件托管在你的 web 服务器上，应用必须结合代码，安全地下载它们之后购买。 从 iOS 6 开始，Apple 将承载其服务器上的文件无需单独的服务器。 该功能才可用的非易耗型产品 （不可使用或订阅）。 使用 Apple 的托管服务的优点包括：

- 保存托管和带宽成本。
- 可能更具可伸缩性比你当前使用的任何服务器主机。 
- 若要编写，因为无需生成任何服务器端处理更少代码。 
- 后台下载已为您实现。

注意： 测试托管应用内购买内容在 iOS 模拟器不支持，因此必须与真实设备进行测试。

### <a name="hosted-content-basics"></a>托管内容的基础知识

在 iOS 6 之前，没有两种方法提供一种产品 (在更多详细信息中所述[Xamarin 的应用内购买](~/ios/platform/in-app-purchasing/index.md)文档):

- **内置产品**– 功能的已解锁通过购买，但内置到应用程序 （不管是为代码或嵌入的资源）。 内置的产品的示例包括解锁的照片筛选器或在游戏增益道具。
- **服务器传递产品**– 购买，应用程序必须从下载内容的服务器，只需执行操作。 此内容是在购买过程下载、 存储在设备上和呈现为提供该产品的一部分。 示例包括书籍、 杂志问题或背景艺术和配置文件组成的游戏级别。

在 iOS 6 Apple 提供了服务器传递产品的一种变体： 它们将托管其服务器上的内容文件。 这使得更易于生成服务器提供的产品，因为不需要运行单独的服务器，并存储工具包提供了您以前必须编写自己的后台下载功能。 若要利用 Apple 的承载，启用新的应用内购买产品的内容托管和修改存储工具包代码，以充分利用它。 产品内容文件然后将使用 Xcode 生成和上传到 Apple 进行审核和发布服务器。

[![](changes-to-storekit-images/image4.png "在生成和交付过程")](changes-to-storekit-images/image4.png#lightbox)

使用应用商店提供应用内购买*与承载的内容*需要以下安装和配置：

- **在 iTunes Connect** – 你*必须*提供你的银行和税务信息到 Apple 以便它们可以汇出收集代表你的资金。 然后，可以配置要销售的产品，并设置沙盒用户帐户以测试购买的产品。  _您还必须配置所承载的内容对你想要通过 Apple 托管这些非易耗型产品_。
- **iOS 设置门户**– 创建捆绑包标识符和应用商店为启用访问您的应用程序，就像为任何支持应用内购买的应用程序。
- **存储工具包**– 将代码添加到你的应用显示产品、 购买产品，以及还原的事务。  _IOS 6 中存储工具包还将管理的产品中的内容，使用进度更新在后台下载。_
- **自定义代码**– 若要将跟踪通过客户的购买并提供已购买的服务或产品。 利用新的 iOS 6 存储工具包类，如`SKDownload`来检索由 Apple 托管的内容。

以下部分介绍如何实现承载的内容，从创建并将包上载到管理购买和下载过程中，使用本文中的示例代码。

### <a name="sample-code"></a>代码示例

示例项目*HostedNonConsumables* （在 StoreKitiOS6.zip) 使用托管的内容。 应用提供了用于销售，Apple 的服务器托管的内容中两个"书籍章节"。 内容包括文本文件和图像，但要复杂得多的内容也可使用实际的应用程序。

应用程序之前、 期间和之后购买如下所示：

 [![](changes-to-storekit-images/image5.png "应用程序如下所示之前,、 期间和之后购买")](changes-to-storekit-images/image5.png#lightbox)

下载并复制到应用程序的文档目录中的文本文件和图像。 有关可用于存储应用程序的不同目录的详细信息，请参阅[文件系统文档](~/ios/app-fundamentals/file-system.md)。

## <a name="itunes-connect"></a>在 iTunes Connect

创建将使用 Apple 的新产品的内容托管，请务必选择**非易耗型**产品类型。 其他产品类型不支持内容托管。 此外，不应启用的内容托管*现有*销售; 仅启用内容托管的新产品的产品。

 [![](changes-to-storekit-images/image6.png "选择非易耗型产品类型")](changes-to-storekit-images/image6.png#lightbox)

输入**产品 ID**。 此 ID 将需要更高版本时创建此产品的内容。

 [![](changes-to-storekit-images/image7.png "输入产品 ID")](changes-to-storekit-images/image7.png#lightbox)

内容托管的详细信息部分中设置。 在应用内购买上线之前, 取消选中**托管内容与 Apple**复选框，如果你想要取消 （即使已上传一些测试内容）。 但是不能删除后已实时应用内购买内容托管。

 [![](changes-to-storekit-images/image8.png "承载与 Apple 的内容")](changes-to-storekit-images/image8.png#lightbox)

一旦打开承载内容上，该产品将进入**等待上载**状态并显示此消息：

 [![](changes-to-storekit-images/image9.png "该产品将进入等待有关上传状态并显示此消息")](changes-to-storekit-images/image9.png#lightbox)

内容的包应使用 Xcode 创建和上传使用存档工具。 下一节中提供了用于创建内容包的说明**创建。PKG 文件**。

## <a name="creating-pkg-files"></a>正在创建。PKG 文件

向 Apple 上传的内容文件必须满足以下限制：

- 不能超过 2 GB 的大小。
- 不能包含可执行代码 （或指向外部内容的符号链接）。
- 必须采用正确的格式 (包括 **.plist**文件) 和具有 **.pkg**文件扩展名。 这将如果遵循这些说明使用 Xcode 自动完成。

只要它们满足这些限制，可以添加多个不同的文件和类型的文件。 内容是在传递到你的应用程序之前压缩并解压缩存储工具包之前你的代码访问它。

上传后的内容包，它可以替换为较新的内容。 必须将新内容上传并提交以供评审/审核通过正常的过程。 增量`ContentVersion`字段中更新的内容包，以指示它是更高版本。

### <a name="xcode-in-app-purchase-content-projects"></a>Xcode 应用内购买内容的项目

当前创建的应用内购买产品的内容包需要 Xcode。 没有所需; 否 OBJECTIVE C 编写代码Xcode 具有新的项目类型对于这些包，其中只包含您的文件和 plist。

我们的示例应用程序已销售的书籍章节 – 每个章节内容包将包含：

-  文本文件，并
-  图像来表示一章。


首先，选择**文件 > 新建项目**菜单中，并选择**应用内购买内容**:

 [![](changes-to-storekit-images/image10.png "选择应用内购买内容")](changes-to-storekit-images/image10.png#lightbox)

输入**产品名称**并**公司标识符**以便**捆绑包标识符**匹配**产品 ID**在 iTunes 中输入此产品的连接。

[![](changes-to-storekit-images/image11.png "输入名称和标识符")](changes-to-storekit-images/image11.png#lightbox)

现在将具有空白**应用程序内购买内容**项目。 您可以右键单击和**添加文件...** 或拖到**项目导航器**。 絋粄**ContentVersion**是否正确 （它应开始 1.0，但如果在以后选择要更新你的内容，请记住以将其递增）。

此屏幕截图显示包含在项目和主窗口中可见的 plist 条目的内容文件与 Xcode:

[![](changes-to-storekit-images/image12.png "此屏幕截图显示包含在项目和主窗口中可见的 plist 条目的内容文件与 Xcode")](changes-to-storekit-images/image12.png#lightbox)

已添加所有内容文件后可以保存此项目并稍后再对其进行编辑或开始上传过程。

## <a name="uploading-pkg-files"></a>上传。PKG 文件

若要上传内容的包的最简单方法是使用**Xcode 存档工具**。 选择**产品 > 存档**从开始菜单：

![](changes-to-storekit-images/image13.png "选择 Archiven")

内容的包然后将显示在存档中，如下所示。 存档类型和图标显示此行**应用程序内购买内容存档**。 单击**验证...** 若要检查有错误我们内容的包无需实际执行上传。

[![](changes-to-storekit-images/image14.png "验证包")](changes-to-storekit-images/image14.png#lightbox)

在 iTunes Connect 凭据使用的登录名：

[![](changes-to-storekit-images/image15.png "使用 iTunes Connect 凭据登录")](changes-to-storekit-images/image15.png#lightbox)

选择要将此内容与关联的正确的应用程序和应用内购买：

[![](changes-to-storekit-images/image16.png "选择要将此内容与关联的正确的应用程序和应用内购买")](changes-to-storekit-images/image16.png#lightbox)

你应看到一条消息，如以下屏幕截图：

![示例没有问题消息](changes-to-storekit-images/image17.png "示例没有问题消息")

现在通过类似的过程，但单击**分发...** 将实际上传内容。

[![将应用分发](changes-to-storekit-images/image18.png "分发应用")](changes-to-storekit-images/image18.png#lightbox)

选择要上传内容的第一个选项：

![将内容上传](changes-to-storekit-images/image19.png "上载内容")

重新登录：

[![](changes-to-storekit-images/image15.png "中的登录名")](changes-to-storekit-images/image15.png#lightbox)

选择要上传到内容的正确的应用程序和应用内购买记录：

[![](changes-to-storekit-images/image20.png "选择应用程序和应用内购买记录")](changes-to-storekit-images/image20.png#lightbox)

将文件上传稍候：

[![](changes-to-storekit-images/image21.png "内容上传对话框")](changes-to-storekit-images/image21.png#lightbox)

上传完成后，将显示一条消息以通知您，内容已提交到 App Store。

[![](changes-to-storekit-images/image22.png "示例成功上传消息")](changes-to-storekit-images/image22.png#lightbox)

一旦这样做了，当您返回到产品页上在 iTunes Connect，它将显示包详细信息，并采用**已准备好提交**状态。 当产品处于此状态时，可以开始测试在沙盒环境中。 不需要 '提交' 在沙盒中测试产品。

[![](changes-to-storekit-images/image23.png "在 iTunes Connect，它将显示包详细信息并提交状态为准备就绪")](changes-to-storekit-images/image23.png#lightbox)

它 （例如可能需要一些时间。 几分钟） 之间上传存档和 iTunes Connect 状态更新。 可以单独提交进行审阅产品，也可以与应用程序二进制文件一起提交。 仅 Apple 已正式批准内容后它将可在生产应用商店应用程序中的购买。

### <a name="pkg-file-format"></a>PKG 文件格式

使用 Xcode 和存档工具来创建并上载托管的内容包意味着你永远不会看到包本身的内容。 文件和目录中创建的示例应用看上去如以下屏幕截图的包与**plist**根和中的产品文件中的文件**内容**子目录：

[![](changes-to-storekit-images/image24.png "根和内容子目录中的产品文件中的 plist 文件")](changes-to-storekit-images/image24.png#lightbox)

请注意该程序包的目录结构 (尤其是在文件的位置`Contents`子目录) 因为您将需要了解此信息以从设备上的包中提取文件。

### <a name="updating-package-content"></a>更新包内容

已获得批准后更新内容的步骤：

- 编辑在 Xcode 中的应用内购买内容项目。
- 增加版本号。
- 再次上传到 iTunes Connect。 后续的购买者将自动获取最新版本，但已有的旧版本的用户不会收到任何通知。
- 您的应用程序是内容的负责通知用户并鼓励他们检索较新版本。 应用程序还必须生成下载最新版本，使用还原功能的应用商店套件的函数。
- 若要确定是否存在较新版本，可以将一项功能内置到应用以提取 SKProducts （例如。 用于检索产品价格的相同过程） 和 ContentVersion 属性进行比较。

## <a name="purchasing-overview"></a>购买概述

在阅读本部分中之前, 查看现有[应用内购买文档](~/ios/platform/in-app-purchasing/index.md)。

购买某种产品的包含承载的内容时发生的事件序列并下载此图所示：

[![](changes-to-storekit-images/image25.png "承载的内容与产品时发生的事件序列购买和下载")](changes-to-storekit-images/image25.png#lightbox)

1. 可以在 iTunes Connect 与承载的内容启用创建的新产品。 在 Xcode 中单独构造 （如只需拖放到的文件夹的文件），然后存档的实际内容，并上传到 iTunes （无需进行编码是必需的）。 每个产品然后提交以供审核之后, 它将成为可供购买。 这些产品 Id 在示例代码中是硬编码，但承载与 Apple 的内容是更灵活，如果存储可用的产品列表的远程服务器上，以便可以在提交新产品和到 iTunes Connect 的内容时进行更新。
1. 当用户购买产品时，事务被置于付款队列中等待处理。
1. 存储工具包购买将请求转发到 iTunes 服务器进行处理。
1. 事务完成 iTunes 服务器上 （例如。 向客户收取费用） 和回执返回到应用程序中，使用附加包括它是可下载的产品信息 (如果是，文件大小和其他元数据)。
1. 你的代码应检查是否该产品已可下载，和如果是这样做还放置在付款队列的内容下载请求。 存储工具包向 iTunes 服务器发送此请求。
1. 服务器返回到应用商店套件，提供一个回调，以返回下载进度和剩余到你的代码的估计时间的内容文件。
1. 完成后，获取通知并传递缓存文件夹中的文件位置。
1. 你的代码应复制文件，并验证它们，您需要记住已购买产品的任何状态保存。 利用此机会来的新文件上设置正确备份标志 (提示： 如果用户永远不会编辑它们来自服务器，则应跳过这些备份，因为用户可以随时检索它们从 Apple 的服务器在将来)。
1. 调用 FinishTransaction。 此步骤非常重要，因为它从付款队列将删除该事务。 还有一点后复制带缓存目录的内容不会调用直到 FinishTransaction。 一旦调用 FinishTransaction，很可能快速清除的缓存文件。

## <a name="implementing-hosted-content-purchase"></a>实现托管内容购买

以下信息应使用完整一起阅读[应用内购买文档](~/ios/platform/in-app-purchasing/index.md)。 本文档中的信息主要上承载的内容和以前的实现之间的差异。

### <a name="classes"></a>类

已添加或更改为 iOS 6 支持托管内容的以下类：

- **SKDownload** – 表示下载正在进行中的新类。 该 API 允许多个产品，但是最初只有一个已实现。
- **SKProduct** – 添加新属性： `Downloadable`， `ContentVersion`，`ContentLengths`数组。
- **SKPaymentTransaction** – 添加新属性： `Downloads`，其中包含一系列`SKDownload`对象本产品已承载内容可供下载。
- **SKPaymentQueue** – 添加的新方法： `StartDownloads`。 调用此方法与`SKDownload`要提取其承载的内容的对象。 下载可以在后台进行。
- **SKPaymentTransactionObserver** – 新方法： `UpdateDownloads`。 存储工具包调用此方法使用当前的下载操作的进度信息。

新的详细信息`SKDownload`类：

- **进度**– 可用于向用户显示完成百分比的指示器的 0 到 1 之间的值。 不使用进度 = = 1，以检测是否完成下载后，检查其状态 = = 已完成。
- **TimeRemaining** – 下载剩余的时间，以秒为单位的估计值。 -1 意味着它仍在计算估计值。
- **状态**– 处于活动状态，等待完成、 失败、 暂停、 已取消。
- **ContentURL** – 文件内容已放置的位置在磁盘上，在`Cache`目录。 下载完毕后，才填充。
- **错误**– 检查此属性，如果状态为失败。

示例代码中的类之间的交互 （特定于托管内容的购买内容的代码显示为绿色） 此关系图所示：

[![](changes-to-storekit-images/image26.png "托管内容的购买内容显示为此关系图中的绿色")](changes-to-storekit-images/image26.png#lightbox)

已在其中使用这些类的示例代码是本部分的其余部分中所示：

### <a name="custompaymentobserver-skpaymenttransactionobserver"></a>CustomPaymentObserver (SKPaymentTransactionObserver)

更改现有`UpdatedTransactions`重写以检查可下载内容和调用`StartDownloads`如有必要：

```csharp
public override void UpdatedTransactions (SKPaymentQueue queue, SKPaymentTransaction[] transactions)
{
    foreach (SKPaymentTransaction transaction in transactions) {
        switch (transaction.TransactionState) {
        case SKPaymentTransactionState.Purchased:
            // UPDATED FOR iOS 6
            if (transaction.Downloads != null && transaction.Downloads.Length > 0) {
                // Purchase complete, and it has downloads... so download them!
                SKPaymentQueue.DefaultQueue.StartDownloads (transaction.Downloads);
                // CompleteTransaction() call has moved after downloads complete
            } else {
                // complete the transaction now
                theManager.CompleteTransaction(transaction);
            }
            break;
        case SKPaymentTransactionState.Failed:
            theManager.FailedTransaction(transaction);
            break;
        case SKPaymentTransactionState.Restored:
            // TODO: you must decide how to handle restored transactions.
            // Triggering all the downloads at once is not advisable.
            theManager.RestoreTransaction(transaction);
            break;
        default:
            break;
        }
    }
}
```

新的重写的方法`UpdatedDownloads`如下所示。 存储工具包调用此方法后的`StartDownloads`中触发`UpdatedTransactions`。 此方法称为*多次*按不确定的时间间隔，以提供您的下载进度，然后再次下载已完成时。 请注意，该方法接受一组`SKDownload`对象，以便每个方法调用可以为您提供的队列中的多个下载的状态。 如中所示下载状态会出现下面的实现将检查每个时间和采取相应的措施。

```csharp
// ENTIRELY NEW METHOD IN iOS6
public override void PaymentQueueUpdatedDownloads (SKPaymentQueue queue, SKDownload[] downloads)
{
    Console.WriteLine (" -- PaymentQueueUpdatedDownloads");
    foreach (SKDownload download in downloads) {
        switch (download.DownloadState) {
        case SKDownloadState.Active:
            // TODO: implement a notification to the UI (progress bar or something?)
            Console.WriteLine ("Download progress:" + download.Progress);
            Console.WriteLine ("Time remaining:   " + download.TimeRemaining); // -1 means 'still calculating'
            break;
        case SKDownloadState.Finished:
            Console.WriteLine ("Finished!!!!");
            Console.WriteLine ("Content URL:" + download.ContentUrl);

            // UNPACK HERE! Calls FinishTransaction when it's done
            theManager.SaveDownload (download);

            break;
        case SKDownloadState.Failed:
            Console.WriteLine ("Failed"); // TODO: UI?
            break;
        case SKDownloadState.Cancelled:
            Console.WriteLine ("Canceled"); // TODO: UI?
            break;
        case SKDownloadState.Paused:
        case SKDownloadState.Waiting:
            break;
        default:
            break;
        }
    }
}
```

### <a name="inapppurchasemanager-skproductsrequestdelegate"></a>InAppPurchaseManager (SKProductsRequestDelegate)

此类包含一个新方法`SaveDownload`每个下载成功完成后调用。

已成功下载并解压缩到所承载的内容`Cache`目录。 结构。包文件要求所有的文件中保存`Contents`子目录中，因此下面的代码中提取文件`Contents`子目录。

代码循环访问的内容的包中的所有文件并将复制到它们`Documents`目录中，对于名为的子文件夹中`ProductIdentifier`。 最后调用`CompleteTransaction`，它调用`FinishTransaction`从付款队列中删除该事务。

```csharp
// ENTIRELY NEW METHOD IN iOS 6
public void SaveDownload (SKDownload download)
{
    var documentsPath = Environment.GetFolderPath (Environment.SpecialFolder.Personal); // Documents folder
    var targetfolder = System.IO.Path.Combine (documentsPath, download.Transaction.Payment.ProductIdentifier);
    // targetfolder will be "/Documents/com.xamarin.storekitdoc.montouchimages/" or something like that
    if (!System.IO.Directory.Exists (targetfolder))
        System.IO.Directory.CreateDirectory (targetfolder);
    foreach (var file in System.IO.Directory.EnumerateFiles 
             (System.IO.Path.Combine(download.ContentUrl.Path, "Contents"))) { // Contents directory is the default in .PKG files
        var fileName = file.Substring (file.LastIndexOf ("/") + 1);
        var newFilePath = System.IO.Path.Combine(targetfolder, fileName);
        if (!System.IO.File.Exists(newFilePath)) // HACK: this won't support new versions...
            System.IO.File.Copy (file, newFilePath);
        else
            Console.WriteLine ("already exists " + newFilePath);
    }
    CompleteTransaction (download.Transaction); // so it gets 'finished'
}
```

当`FinishTransaction`调用时，下载文件不再保证可在`Cache`目录。 应将所有文件都复制，然后再调`FinishTransaction`。


## <a name="other-considerations"></a>其他注意事项

上面的代码示例演示了一个相当简单实现的托管内容购买。 有一些必须考虑的其他点：

### <a name="detecting-updated-content"></a>检测更新的内容

无法更新托管的内容包时，应用商店套件不提供任何机制来这些更新推送到已下载并购买了产品的用户。 若要实现此功能可能会检查你的代码的新`SKProduct.ContentVersion`属性 (如果`SKProduct`是`Downloadable`) 定期，并检测该值递增。 或者可以生成的推送通知系统。

### <a name="installing-updated-content-versions"></a>安装更新的内容版本

上面的示例代码如果跳过文件将复制该文件已存在。 如果你想要支持较新版本的下载内容，则不是个好主意。

另一个方法可能是将其内容复制到的版本，名为的文件夹并跟踪它是当前版本 （例如。 在`NSUserDefaults`或存储已完成的购买记录的任何位置)。

### <a name="restoring-transactions"></a>还原的事务

当`SKPaymentQueue.DefaultQueue.RestoreCompletedTransactions`是调用，应用商店套件返回以前所有的事务的用户。 如果它们已购买了大量的项，或每个购买内容有较大的内容包，然后还原可能导致大量网络流量如所有内容获取排队等待下载在一次。

是否从关联的内容包的实际下载单独购买产品，请考虑跟踪。

### <a name="pausing-restarting-and-canceling-downloads"></a>暂停、 重新启动和取消下载

尽管示例代码不演示此功能，则可以暂停和重新启动托管的内容下载。 `SKPaymentQueue.DefaultQueue`具有用于方法`PauseDownloads`，`ResumeDownloads`和`CancelDownloads`。

如果该代码调用`FinishTransaction`在下载之前支付队列`Finished`然后会自动取消该下载。

### <a name="setting-the-skip-backup-flag-on-the-downloaded-content"></a>在下载的内容上设置跳过备份标志

Apple 的 iCloud 备份指南建议非用户轻松地从服务器中还原的内容应*不*进行备份 （因为它会不必要地使用 iCloud 存储）。 设置备份的属性的详细信息，请参阅[文件系统](~/ios/app-fundamentals/file-system.md)文档。

## <a name="summary"></a>总结

本文已介绍了两项新功能的应用商店套件中 iOS6： 购买 iTunes 和在应用中，从其他内容，并使用 Apple 的服务器来托管应用内购买情况。 应结合现有阅读此简介[应用内购买文档](~/ios/platform/in-app-purchasing/index.md)为完整的覆盖范围的实现存储工具包功能。

## <a name="related-links"></a>相关链接

- [StoreKit （示例）](https://developer.xamarin.com/samples/StoreKit/)
- [应用内购买](~/ios/platform/in-app-purchasing/index.md)
- [StoreKit 框架引用](https://developer.apple.com/library/prerelease/ios/#documentation/StoreKit/Reference/StoreKit_Collection/_index.html)
- [SKStoreProductViewController 类引用](https://developer.apple.com/library/ios/documentation/StoreKit/Reference/SKITunesProductViewController_Ref/SKStoreProductViewController.html)
- [iTunes 搜索 API 参考](http://www.apple.com/itunes/affiliates/resources/documentation/itunes-store-web-service-search-api.html)
- [SKDownload](https://developer.apple.com/library/prerelease/ios/#documentation/StoreKit/Reference/SKDownload_Ref/Introduction/Introduction.html)
- [SKPaymentQueue](https://developer.apple.com/library/prerelease/ios/documentation/StoreKit/Reference/SKPaymentQueue_Class/Reference/Reference.html#/apple_ref/occ/instm/SKPaymentQueue/cancelDownloads:)
- [SKProduct](https://developer.apple.com/library/prerelease/ios/documentation/StoreKit/Reference/SKProduct_Reference/Reference/Reference.html#/apple_ref/occ/instp/SKProduct/downloadable)
- [WWDC 视频：销售产品与应用商店套件](https://developer.apple.com/videos/wwdc/2012/?include=302#302)
