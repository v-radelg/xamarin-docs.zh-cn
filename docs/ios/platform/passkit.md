---
title: 在 Xamarin.iOS PassKit
description: Wallet 应用允许 iOS 用户在其设备上存储数字传递。 PassKit 框架允许开发人员能够以编程方式与传递进行交互。
ms.prod: xamarin
ms.assetid: 74B9973B-C1E8-B727-3F6D-59C1F98BAB3A
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/13/2018
ms.openlocfilehash: d1c640bef41e875b3bb427d657c9c239e4c3e16d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61192152"
---
# <a name="passkit-in-xamarinios"></a>在 Xamarin.iOS PassKit

IOS Wallet 应用允许用户在其设备上存储数字传递。
这些阶段会生成的商店，然后发送到客户通过电子邮件、 Url，或通过商店的 iOS 应用程序。 这些阶段可以表示各种操作，从电影票证到登机牌会员卡。 PassKit 框架允许开发人员能够以编程方式与传递进行交互。

本文档介绍钱包和 PassKit API 与 Xamarin.iOS 一起使用。

 [![](passkit-images/image1.png "电子钱包存储和组织在手机上的所有阶段")](passkit-images/image1.png#lightbox)

## <a name="requirements"></a>要求

本文档中讨论的 PassKit 功能需要 iOS 6 和 Xcode 4.5，以及 Xamarin.iOS 6.0。

## <a name="introduction"></a>介绍

PassKit 解决的关键问题是条形码的分发和管理。 如何当前使用条形码时遇到的一些实际的示例包括：

-   **电影在线购票**– 客户通常电子邮件发送表示其票证的条形码。 此条形码是打印，转至影院要条目进行扫描。
-   **会员卡**– 客户具有大量不同钱包或钱包中的特定于应用商店的卡，并用于显示和扫描时它们购买商品。
-   **优惠券**– 优惠券通过电子邮件，可打印网页，通过上下黑边和作为报纸或杂志中的条形码的形式分发。 客户将其恢复到的扫描，以接收商品、 服务或折扣返回的存储区。
-   **乘客传递**– 类似于购买电影票证。

PassKit 为每种方案提供一种替代方法：

-   **电影票证**– 后购买时，客户会添加的事件票证传递 （通过电子邮件或网站链接）。 电影方法的时间，提醒一下，在锁定屏幕上将自动出现在传递及上到达影院传递可以轻松地检索并显示钱夹中进行扫描。
-   **会员卡**– 而不是 （或除了） 提供对物理卡，存储可以发出 （通过电子邮件或网站登录后） 存储卡传递。 存储可提供其他功能，例如更新对推送通知，通过传递帐户的余额和使用地理位置服务传递无法自动锁定屏幕上时显示客户存储区位置附近。
-   **优惠券**– 优惠券传递可以轻松地生成具有唯一特征，以便跟踪，并通过电子邮件或网站链接分发。 当用户的特定位置附近和/或在给定日期 （如接近到期日期时） 时，已下载的优惠券可以自动显示在锁定屏幕上。 优惠券存储在用户的手机上，因为它们始终是非常方便，不执行获取错误放置。 优惠券可能鼓励客户下载的配套应用程序，因为应用商店的链接可以合并到增加与客户合作的过程。
-   **乘客传递**– 后联机签入过程，客户会收到其登机牌通过电子邮件或链接。 由传输提供程序提供辅助应用程序可能包括签入过程中，并还允许客户执行其他功能，如选择其席位或食物。 传输提供程序可以使用推送通知可以在传输出现延迟或已取消更新传递。 作为客时间方法传递将显示在锁定屏幕上提醒一下，并提供快速访问到阶段。

就其核心而言，PassKit 提供简单且方便的方式来存储和显示在 iOS 设备上的条形码。 更多时间和位置锁屏界面集成后，推送通知和配套应用程序将其集成提供了一个非常复杂销售 foundation 票证和计费服务。

## <a name="passkit-ecosystem"></a>PassKit Ecosystem

PassKit 不是只在产品 CocoaTouch 的 API，而是一个更大的生态系统的应用、 数据和服务，以方便安全共享和管理的条形码和其他数据的一部分。 此高级别关系图显示可以涉及的不同实体中创建和使用传递：

 [![](passkit-images/image2.png "此高级别关系图显示实体所涉及到创建和使用传递")](passkit-images/image2.png#lightbox)

生态系统的每个段都具有明确定义的角色：

-   **电子钱包**– Apple 的内置 iOS 应用程序存储和显示阶段。 这是传递呈现在现实世界 （即条形码已显示，以及在阶段中的所有本地化数据） 中使用的唯一位置。
-   **伴生应用**– 生成的 iOS 6 应用程序传递提供程序来扩展它们发出，例如将值添加到存储卡，更改座位登机牌或其他特定于业务的函数传递的功能。 辅助应用程序不需要传递非常有用。
-   **你的服务器**– 安全服务器可以生成并分发签名传递。 辅助应用程序可能会连接到服务器以生成新的阶段或请求现有阶段的更新。 （可选） 可以实现的 web 钱包将调用以更新服务 API 将传递。
-   **APNS 服务器**– 你的服务器能够通知 Wallet 是通过使用 APNS 给定设备上的更新。 将通知推送到 Wallet 然后将联系你的服务器的更改的详细信息。 辅助应用程序不需要此功能实现 APNS (可以侦听`PKPassLibraryDidChangeNotification`)。
-   **管道应用**– 无法直接处理的应用程序通过 （例如，辅助应用程序执行操作），但这可以通过识别传递，并允许他们添加到 Wallet 中提高其实用性。 邮件客户端、 社交网络浏览器和其他数据聚合应用所有可能遇到的附件或传递到链接。

整个生态系统看起来复杂，所以值得注意是，某些组件是可选的要简单得多 PassKit 实现是可行的。

## <a name="what-is-a-pass"></a>什么是传递？

一个传递是表示票证、 优惠券或卡数据的集合。 它可能供个人使用一次 （并且因此包含详细信息，如航班号和席位分配） 或可能可由任意数量的用户 （如折扣优惠券） 共享的多个使用令牌。 详细的说明现已推出 Apple[关于传递文件](https://developer.apple.com/library/prerelease/ios/#documentation/UserExperience/Reference/PassKit_Bundle/Chapters/Introduction.html)文档。

### <a name="types"></a>类型

当前五个支持的类型，可以通过在传递的布局和顶部边缘区分 Wallet 应用中：

-  **事件票证**– 小型半圆形切除。
-   **初始启用传递**– 可以 （例如指定在另一方面，特定于传输的图标的凹槽。 总线、 训练、 飞机上）。
-   **存储卡**– 向上舍入前，如信用卡或借记卡。
-  **优惠券**– 穿孔沿着顶部。
-  **泛型**– 与存储卡中，向上舍入前相同。


在此屏幕截图显示五个传递类型 (按顺序： 优惠券，泛型类型，存储卡、 登机牌和事件票证):

 [![](passkit-images/image3.png "在此屏幕截图显示五个传递类型")](passkit-images/image3.png#lightbox)

### <a name="file-structure"></a>文件结构

传递文件是实际使用的 ZIP 存档 **.pkpass**扩展，其中包含一些特定 JSON 文件 （必需）、 各种图像文件 （可选） 以及已本地化的字符串 （也是可选的）。

-   **pass.json** – 必需。 包含在传递的所有信息。
-   **manifest.json** – 必需。 包含每个文件中除签名文件的阶段和 (manifest.json) 此文件的 SHA1 哈希的值。
-   **签名**– 必需。 创建的签名`manifest.json`生成在 iOS 设置门户中的证书文件。
-  **logo.png** – 可选。
-  **background.png** – 可选。
-  **icon.png** – 可选。
-  **可本地化的字符串文件**– 可选。

传递文件的目录结构如下所示 （这是 ZIP 存档的内容）：

 [![](passkit-images/image4.png "传递文件的目录结构如下所示")](passkit-images/image4.png#lightbox)

### <a name="passjson"></a>pass.json

JSON 是格式，因为通常在服务器上创建传递 – 这意味着生成代码与平台无关的服务器上。 在每个阶段中的三个主要部分是信息的：

-   **teamIdentifier** – 这链接到应用商店帐户生成的所有阶段。 此值是在 iOS 设置门户中可见。
-   **passTypeIdentifier** – 预配门户向组中的寄存器将一起传递 （如果生成多个类型）。 例如，咖啡店可能会创建存储卡传递类型以使其客户能够获得忠诚度个积分，但单独优惠券还传递要创建和分发折扣优惠券类型。 该相同的咖啡厅甚至可能容纳实时音乐事件并为这些颁发事件票证传递。
-   **serialNumber** – 在此的唯一字符串`passTypeidentifier`。 值是不透明的钱包，但对于跟踪的特定传递与您的服务器进行通信时非常重要。

还有大量的每个阶段，如下所示的示例中的其他 JSON 密钥：

``` 
{
   "passTypeIdentifier":"com.xamarin.passkitdoc.banana",  //Type Identifier (iOS Provisioning Portal)
   "formatVersion":1,                                     //Always 1 (for now)
   "organizationName":"Xamarin",                          //The name which appears on push notifications
   "serialNumber":"12345436XYZ",                          //A number for you to identify this pass
   "teamIdentifier":"XXXAAA1234",                         //Your Team ID
   "description":"Xamarin Demo",                          //
   "foregroundColor":"rgb(54,80,255)",                    //color of the data text (note the syntax)
   "backgroundColor":"rgb(209,255,247)",                  //color of the background
   "labelColor":"rgb(255,15,15)",                         //color of label text and icons
   "logoText":"Banana ",                                  //Text that appears next to logo on top
   "barcode":{                                            //Specification of the barcode (optional)
      "format":"PKBarcodeFormatQR",                       //Format can be QR, Text, Aztec, PDF417
      "message":"FREE-BANANA",                            //What to encode in barcode
      "messageEncoding":"iso-8859-1"                      //Encoding of the message
   },
   "relevantDate":"2012-09-15T15:15Z",                    //When to show pass on screen. ISO8601 formatted.
  /* The following fields are specific to which type of pass. The name of this object specifies the type, e.g., boardingPass below implies this is a boarding pass. Other options include storeCard, generic, coupon, and eventTicket */
   "boardingPass":{
/*headerFields, primaryFields, secondaryFields, and auxiliaryFields are arrays of field object. Each field has a key, label, and value*/
      "headerFields":[          //Header fields appear next to logoText
         {
            "key":"h1-label",   //Must be unique. Used by iOS apps to get the data.
            "label":"H1-label", //Label of the field
            "value":"H1"        //The actual data in the field
         },
         {
            "key":"h2-label",
            "label":"H2-label",
            "value":"H2"
         }
      ],
      "primaryFields":[       //Appearance differs based on pass type
         {
            "key":"p1-label",
            "label":"P1-label",
            "value":"P1"
         }
      ],
      "secondaryFields":[     //Typically appear below primaryFields
         {
            "key":"s1-label",
            "label":"S1-label",
            "value":"S1"
         }
      ],
      "auxiliaryFields":[    //Appear below secondary fields
         {
            "key":"a1-label",
            "label":"A1-label",
            "value":"A1"
         }
      ],
      "transitType":"PKTransitTypeAir"  //Only present in boradingPass type. Value can
                                        //Air, Bus, Boat, or Train. Impacts the picture
                                        //that shows in the middle of the pass.
   }
}
```

### <a name="barcodes"></a>条形码

仅支持二维格式：PDF417、 Aztec、 QR。 Apple 声明式 1d 条码是背光手机屏幕上扫描到不适合。

条形码下面显示的替换文字是可选的-有些店主想要能够手动读取/类型。

ISO-8859-1 编码是最常见的检查将读取你的货币额度的扫描系统使用的编码。

### <a name="relevancy-lock-screen"></a>关联性 （锁定屏幕）

有两种类型的可能会导致传递要在锁屏界面上显示的数据：

 **位置**

可以传递，例如客户经常访问时，存储或影院或机场的位置中指定最多 10 个位置。 客户可以将设置为辅助应用程序通过这些位置，或提供程序无法确定其使用情况数据 （如果收集与客户的权限）。

在锁定屏幕上显示时传递，以便在用户离开区域时将传递隐藏在锁定屏幕中计算起一道围墙。 Radius 绑定传递样式以防止滥用。

 **日期和时间**

可以传递中指定一个日期/时间。 日期和时间可用于触发登机牌和事件票证的锁屏提醒。

可以更新通过推送或 PassKit API，以便可以在多个使用票证 （例如到影院或体育复杂的赛季票证） 的情况下更新日期/时间。

### <a name="localization"></a>本地化

翻译为多种语言传递到本地化的 iOS 应用程序与之类似，创建语言特定目录具有`.lproj`扩展并将放置在本地化的元素。 应输入文本翻译到`pass.strings`文件，而本地化的图像应该将它们替换传递根目录中的图像相同的名称。

## <a name="security"></a>安全性

通过使用你在 iOS 设置门户中生成一个私有证书进行签名。 若要登录在传递的步骤如下：

1.  计算传递目录中的每个文件的 SHA1 哈希值 (不包括`manifest.json`或`signature`文件，这两者都不应在仍存在这一阶段)。
1.  编写`manifest.json`作为包含它的哈希的每个文件名的 JSON 键/值列表。
1.  使用证书进行签名`manifest.json`文件，并将结果写入名为的文件`signature`。
1.  压缩所有内容，然后为生成的文件提供`.pkpass`文件扩展名。


由于需要你的私钥进行签名传递，此过程应仅进行控制的安全服务器上。 请勿分发密钥来尝试并生成应用程序中传递。

 
## <a name="configuration-and-setup"></a>配置和设置

本部分包含说明，帮助设置你预配的详细信息并创建第一次传递。

### <a name="provisioning-passkit"></a>预配 PassKit

为了使传递输入 App Store，必须将链接到开发人员帐户。 这需要两个步骤：

1.  在传递必须进行注册，使用的唯一标识符，称为传递类型 id。
1.  必须生成有效的证书进行签名与开发人员的数字签名的过程。

若要创建凭证类型 ID 执行以下。

#### <a name="create-a-pass-type-id"></a>创建传递类型 ID

第一步是设置凭证类型 ID 为每个不同_类型_的过程，以支持。 传递 ID （或传递类型标识符） 的阶段创建的唯一标识符。 我们将使用此 ID 与开发人员帐户使用的证书链接在一起传递。

1. 在中[iOS 设置门户的证书、 标识符和配置文件部分](https://developer.apple.com/account/overview.action)，导航到**标识符**，然后选择**传递的类型 Id** 。 然后选择 **+** 按钮以创建新的轮次的类型：[![](passkit-images/passid.png "创建新的传递类型")](passkit-images/passid.png#lightbox)

2.   提供**描述**（名称） 和**标识符**（唯一字符串） 传递。 请注意，所有传递类型 Id 必须以字符串开头`pass.`在此示例中，我们使用`pass.com.xamarin.coupon.banana`:[![](passkit-images/register.png "提供的说明和标识符")](passkit-images/register.png#lightbox)


3.   通过按确认传递 ID**注册**按钮。

#### <a name="generate-a-certificate"></a>生成证书

若要创建此凭证类型 ID 的新证书，请执行以下操作：

1.  从列表中，选择新创建的凭证 ID，然后单击**编辑**:[![](passkit-images/pass-done.png "从列表中选择新的凭证 ID")](passkit-images/pass-done.png#lightbox)

    然后，选择**创建证书...** :

    [![](passkit-images/cert-dist.png "选择创建证书")](passkit-images/cert-dist.png#lightbox)


2.  按照步骤创建证书签名请求 (CSR)。
  
3. 按**继续**开发人员门户上按钮并上载 CSR 以生成您的证书。

4. 下载的证书，并双击它，以在 keychain 中安装它。


现在，我们已经创建的此凭证类型 ID 的证书下, 一节将介绍如何手动构建一个传递。

有关 Wallet 的预配的详细信息，请参阅[使用功能](~/ios/deploy-test/provisioning/capabilities/wallet-capabilities.md)指南。

### <a name="create-a-pass-manually"></a>手动创建流程

现在，我们已创建传递类型，我们可以手动创建过程，以在模拟器或设备上进行测试。 若要创建流程的步骤如下：

-  创建一个目录以包含传递文件。
-  创建包含所有所需的数据的 pass.json 文件。
-  包含图像文件夹中 （如果需要）。
-  计算的在文件夹中，每个文件的 SHA1 哈希值并将写入到 manifest.json。
-  使用下载的证书.p12 文件进行签名 manifest.json。
-  压缩目录的内容，并使用.pkpass 扩展重命名。


有一些中的源文件[示例代码](https://developer.xamarin.com/samples/monotouch/PassKit/)可用于生成一个传递此项目。 使用中的文件`CouponBanana.raw`CreateAPassManually 目录的目录。 存在下列文件：

 [![](passkit-images/image18.png "这些文件存在")](passkit-images/image18.png#lightbox)

打开 pass.json 并编辑 JSON。 至少必须更新`passTypeIdentifier`和`teamIdentifer`以匹配你的 Apple 开发人员帐户。

```csharp
"passTypeIdentifier" : "pass.com.xamarin.coupon.banana",
"teamIdentifier" : "?????????",
```

然后必须计算每个文件的哈希，并创建`manifest.json`文件。 完成后，它将看起来像这样：

```csharp
{
  "icon@2x.png" : "30806547dcc6ee084a90210e2dc042d5d7d92a41",
  "icon.png" : "87e9ffb203beb2cce5de76113f8e9503aeab6ecc",
  "pass.json" : "c83cd1441c17ecc6c5911bae530d54500f57d9eb",
  "logo.png" : "b3cd8a488b0674ef4e7d941d5edbb4b5b0e6823f",
  "logo@2x.png" : "3ccd214765507f9eab7244acc54cc4ac733baf87"
}
```

接下来必须为使用证书 （.p12 文件） 生成此阶段类型 id。 此文件中生成签名

#### <a name="signing-on-a-mac"></a>在 Mac 上签名

下载**钱包种子支持材料**从[Apple 下载](https://developer.apple.com/downloads/index.action?name=Passbook)站点。 使用`signpass`工具，将你的文件夹转变为一个传递 （这还将计算 SHA1 哈希处理和压缩到.pkpass 文件输出）。

#### <a name="testing"></a>正在测试

如果您打算检查这些工具的输出 （通过设置文件名为.zip，然后将其打开），您会看到以下文件 (请注意添加`manifest.json`和`signature`文件):

 [![](passkit-images/image19.png "检查这些工具的输出")](passkit-images/image19.png#lightbox)

已签名，ZIPped 并将文件 （例如重命名后。 到`BananaCoupon.pkpass`) 可以将其拖动到模拟器，若要测试，或电子邮件发送给自己以检索在真实设备上。 您应看到到一个屏幕**添加**的传递，像这样：

 [![](passkit-images/image20.png "添加通过屏幕")](passkit-images/image20.png#lightbox)

通常情况下会在服务器上，但是手动传递创建可能是一个用于仅创建不需要的后端服务器支持的优惠券的小型企业的选项自动完成该过程。

## <a name="wallet"></a>Wallet

Wallet 是 PassKit 生态系统的核心部分。 此屏幕截图显示空钱包，并传递列表和各个阶段的外观：

 [![](passkit-images/image21.png "此屏幕截图显示空钱包，并传递列表和各个阶段的外观")](passkit-images/image21.png#lightbox)

电子钱包功能包括：

-  它是用其进行扫描的条形码呈现阶段的唯一位置。
-  用户可以更改更新的设置。 如果启用，推送通知可以触发对阶段中数据的更新。
-  用户可以启用或禁用锁定屏幕集成。 如果启用，这样，若要自动显示在锁定屏幕上，根据相关的时间和位置数据嵌入在传递的传递。
-  如果 web 服务器 URL 提供传递 JSON 中，在传递了反面支持下拉刷新。
-  可以打开 （或下载） 配套应用如果传递 JSON 中提供的应用程序的 ID。
-  （使用可爱的切碎动画），可以删除传递。

## <a name="adding-passes-into-wallet"></a>添加到 Wallet 的阶段

传递可以按以下方式添加到 Wallet:

* **管道应用**– 它们不会直接操作传递，它们只是通过将文件加载并为用户提供选择将其添加到 Wallet。 

* **伴生应用**– 这些是由提供程序以分发凭证，并提供附加功能，可浏览或对其进行编辑。 Xamarin.iOS 应用程序具有完全访问 PassKit API 创建和操作阶段。 然后可以添加将传递到 Wallet 使用`PKAddPassesViewController`。 中更详细地介绍了此过程**配套应用程序**本文档的部分。

### <a name="conduit-applications"></a>管道的应用程序

管道的应用程序是可能会收到传递代表用户，并且应进行编程，以识别其内容类型并提供用于将添加到 Wallet 功能的中间应用。 管道的应用的示例包括：

-   **邮件**– 识别为通过附件。
-   **Safari** – 单击传递 URL 链接时识别的传递内容类型。
-   **其他自定义应用**– 接收附件或打开链接 （社交媒体客户端、 邮件阅读器等） 的任何应用。


此屏幕截图显示了如何**邮件**6 在 iOS 中识别传递附件和 （当接触） 提供给**添加**到 Wallet。

 [![](passkit-images/image22.png "此屏幕截图显示了如何在 iOS 6 中的邮件识别传递附件")](passkit-images/image22.png#lightbox)

 [![](passkit-images/image23.png "此屏幕截图显示如何提供邮件以添加到 Wallet 传递附件")](passkit-images/image23.png#lightbox)

如果要构建的应用程序可能是用于传递通道，他们可以识别的：

-  **文件扩展名**-.pkpass
-  **MIME 类型**-application/vnd.apple.pkpass
-  **UTI** – com.apple.pkpass


通道应用程序的基本操作是检索传递文件，并调用 PassKit 的`PKAddPassesViewController`为用户提供的选项，若要向其钱包传递。 在下一节中介绍了此视图控制器的实现**配套应用程序**。

管道的应用程序不需要为特定传递类型 ID 配套应用程序执行的相同方式在预配。

## <a name="companion-applications"></a>配套应用程序

助手应用程序提供了处理阶段，包括创建一个传递、 更新与阶段关联的信息和否则管理与应用程序关联的阶段的其他功能。

配套应用程序不应尝试重复 Wallet 的功能。 它们将不显示传递进行扫描。

本部分的余下内容介绍如何生成基本辅助应用程序的交互与 PassKit。

### <a name="provisioning"></a>预配

Wallet 是一种存储技术，因为应用程序需要分开预配，且不能使用团队设置配置文件或通配符应用 id。 请参阅[使用功能](~/ios/deploy-test/provisioning/capabilities/wallet-capabilities.md)指南 Wallet 应用程序创建唯一的应用程序 ID 和预配配置文件。

### <a name="entitlements"></a>权利

**Entitlements.plist**文件应包含在所有最近的 Xamarin.iOS 项目。 若要添加新的 Entitlements.plist 文件，按照中的步骤[使用权利](~/ios/deploy-test/provisioning/entitlements.md)指南。

若要设置授权执行以下操作：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

双击**Entitlements.plist** Solution Pad，若要打开 Entitlements.plist 编辑器中的文件：

![](passkit-images/image31.png "Entitlements.plst 编辑器")

在 Wallet 部分中，选择**启用钱包**选项

![](passkit-images/image32.png "启用钱包权利")


默认选项适用于您的应用程序允许所有凭证类型。 但是，它是可以限制您的应用程序并仅允许一部分团队传递类型。 若要启用此选**允许一部分团队传递类型**并输入你想要允许的子集的传递类型标识符。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

双击**Entitlements.plist**文件以打开 XML 源文件。

若要添加 Wallet 授权，设置**属性**到`Passbook Identifiers`在下拉列表中，它将自动设置**类型** `Array`。 然后，设置字符串**值**到`$(TeamIdentifierPrefix)*`:

![](passkit-images/image33.png "启用钱包权利")

这会使应用允许所有凭证类型。 若要限制您的应用程序并仅允许一部分团队传递类型，请将字符串值设置为：

`$(TeamIdentifierPrefix)pass.$(CFBundleIdentifier)`

其中`pass.$(CFBundleIdentifier)`是已创建的传递 ID[上面](~/ios/platform/passkit.md)

-----

### <a name="debugging"></a>调试

如果已部署应用程序的问题，请检查你正在使用正确**预配配置文件**并且`Entitlements.plist`选作**自定义权利**中的文件**iPhone 捆绑包签名**选项。

如果在部署时，你会遇到此错误：

```csharp
Installation failed: Your code signing/provisioning profiles are not correctly configured (error: 0xe8008016)
```

然后`pass-type-identifiers`权利数组不正确 (或不匹配**预配配置文件**)。 验证传递的类型 Id 和你的团队 ID 是否正确。

## <a name="classes"></a>类

以下 PassKit 类是可用于应用程序以访问阶段：

-  **PKPass** – 一个是通过的实例。
-  **PKPassLibrary** – 提供 API 来访问设备上的阶段。
-  **PKAddPassesViewController** – 用于显示用户在其钱包保存一个传递。
-  **PKAddPassesViewControllerDelegate** – Xamarin.iOS 开发人员

## <a name="example"></a>示例

中的 PassLibrary 项目是指[示例代码](https://developer.xamarin.com/samples/monotouch/PassKit/)本文。 它演示了需要在钱包配套应用程序中的以下常用函数：

### <a name="check-that-wallet-is-available"></a>Wallet 是可用的检查

电子钱包不可用，在 iPad 上，因此应用程序应检查尝试访问 PassKit 功能之前。

```csharp
if (PKPassLibrary.IsAvailable) {
    // create an instance and do stuff...
}
```

### <a name="creating-a-pass-library-instance"></a>创建传递库实例

PassKit 库不是单一实例，应用程序应创建和存储以及实例访问 PassKit API。

```csharp
if (PKPassLibrary.IsAvailable) {
    library = new PKPassLibrary ();
    // do stuff...
}
```

### <a name="get-a-list-of-passes"></a>获取传递的列表

应用程序可以从库请求传递的列表。 因此，你只能看到在授权阶段使用你的团队 ID 创建，并列出了这些 PassKit 自动筛选此列表。

```csharp
var passes = library.GetPasses ();  // returns PKPass[]
```

请注意模拟器不会使此方法应始终在真实设备上进行测试筛选返回，传递的列表。 此列表可以显示在 UITableView。 [示例应用](https://developer.xamarin.com/samples/monotouch/PassKit/)如下所示添加两个优惠券之后：

 [![](passkit-images/image29.png "示例应用看上去像这样后已添加两个优惠券")](passkit-images/image29.png#lightbox)

### <a name="displaying-passes"></a>显示传递

一组有限是信息的可用于呈现的配套应用程序中传递。

选择从这一组标准属性会显示列表传递，如示例代码。

```csharp
string passInfo =
                "Desc:" + pass.LocalizedDescription
                + "\nOrg:" + pass.OrganizationName
                + "\nID:" + pass.PassTypeIdentifier
                + "\nDate:" + pass.RelevantDate
                + "\nWSUrl:" + pass.WebServiceUrl
                + "\n#" + pass.SerialNumber
                + "\nPassUrl:" + pass.PassUrl;
```

此字符串显示为中的警报[示例](https://developer.xamarin.com/samples/monotouch/PassKit/):

 [![](passkit-images/image30.png "在此示例中的优惠券选择警报")](passkit-images/image30.png#lightbox)

此外可以使用`LocalizedValueForFieldKey()`方法以从设计的阶段中的字段中检索数据 （因为你将知道哪些字段应为存在）。 示例代码不演示此。

### <a name="loading-a-pass-from-a-file"></a>从文件加载一个传递

因为通过只能添加到 Wallet 与用户的权限，必须提供视图控制器以允许用户决定。 此代码用于**添加**按钮在示例中，加载 （您应将此替换为一个已签名） 的应用中嵌入一个预建的传递：

```csharp
NSData nsdata;
using ( FileStream oStream = File.Open (newFilePath, FileMode.Open ) ) {
        nsdata = NSData.FromStream ( oStream );
}
var err = new NSError(new NSString("42"), -42);
var newPass = new PKPass(nsdata,out err);
var pkapvc = new PKAddPassesViewController(newPass);
NavigationController.PresentModalViewController (pkapvc, true);
```

会看到在传递**外**并**取消**选项：

 [![](passkit-images/image20.png "添加和取消按钮选项出现在传递")](passkit-images/image20.png#lightbox)

### <a name="replace-an-existing-pass"></a>替换现有传递

替换现有传递不需要用户的权限，但如果不存在传递将失败。

```csharp
if (library.Contains (newPass)) {
     library.Replace (newPass);
}
```

### <a name="editing-a-pass"></a>编辑一个传递

PKPass 不可变的因此不能在代码中更新传递对象。 若要更改一个传递中的数据应用程序必须有权可使传递的记录，并生成具有更新后的值可以下载该应用程序的新传递文件的 web 服务器。

通过文件创建必须在服务器上执行，因为必须使用专用的安全必须保持的证书签名传递。

生成更新的传递文件之后, 使用`Replace`方法覆盖在设备上的旧数据。

### <a name="display-a-pass-for-scanning"></a>显示通过的扫描

正如前面提到，仅钱包可以显示传递进行扫描。 可以使用显示一个传递`OpenUrl`方法所示：

 `UIApplication.SharedApplication.OpenUrl (p.PassUrl);`

### <a name="receiving-notifications-of-changes"></a>接收更改通知

应用程序可以侦听对传递库所做更改使用`PKPassLibraryDidChangeNotification`。 可能导致通知触发在后台更新，因此很好的做法来侦听这些应用程序中的更改。

```csharp
noteCenter = NSNotificationCenter.DefaultCenter.AddObserver (PKPassLibrary.DidChangeNotification, (not) => {
    BeginInvokeOnMainThread (() => {
        new UIAlertView("Pass Library Changed", "Notification Received", null, "OK", null).Show();
        // refresh the list
        var passlist = library.GetPasses ();
        table.Source = new TableSource (passlist, library);
        table.ReloadData ();
    });
}, library);  // IMPORTANT: must pass the library in
```

务必要注册的通知，因为 PKPassLibrary 不是单一实例时传递库实例。

## <a name="server-processing"></a>服务器处理

生成服务器应用程序以支持 PassKit 的详细的讨论不在此介绍性文章的范围。

请参阅[dotnet passbook](https://github.com/tomasmcguinness/dotnet-passbook)开放源C#服务器端代码。

## <a name="push-notifications"></a>推送通知

使用推送通知用于更新传递的详细的讨论不在此介绍性文章的范围。

您所需实现由 Apple 响应 web 请求从钱包需要更新时定义的类似于 REST 的 API。

请参阅 Apple[更新一个传递](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/PassKit_PG/Updating.html#//apple_ref/doc/uid/TP40012195-CH5-SW1)指南以获取详细信息。

## <a name="summary"></a>总结

此文引入 PassKit、 概述了一些它为什么很有用的原因，必须为完整 PassKit 解决方案实现的不同部分所述。 它描述配置你的 Apple 开发者帐户以创建传递，以使通过手动以及如何从 Xamarin.iOS 应用程序访问 PassKit Api 的进程所需的步骤。

## <a name="related-links"></a>相关链接

- [面向开发人员的电子钱包](https://developer.apple.com/wallet/)
- [PassKit Sample](https://developer.xamarin.com/samples/monotouch/PassKit/)
- [电子钱包开发人员指南](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/PassKit_PG/index.html#//apple_ref/doc/uid/TP40012195-CH1-SW1)
- [框架 – Apple Pay 和钱包 （WWDC 视频）](https://developer.apple.com/videos/frameworks/apple-pay-and-wallet)
- [PassKit 框架引用](https://developer.apple.com/library/prerelease/ios/#documentation/UserExperience/Reference/PassKit_Framework/_index.html)
- [Passbook Web 服务引用](https://developer.apple.com/library/prerelease/ios/#documentation/PassKit/Reference/PassKit_WebService/WebService.html)
- [有关传递文件](https://developer.apple.com/library/prerelease/ios/#documentation/UserExperience/Reference/PassKit_Bundle/Chapters/Introduction.html)
- [dotnet passbook](https://github.com/tomasmcguinness/dotnet-passbook)，用于生成 iOS 钱包包的开放源代码库
- [iOS 6 简介](~/ios/platform/introduction-to-ios6/index.md)
