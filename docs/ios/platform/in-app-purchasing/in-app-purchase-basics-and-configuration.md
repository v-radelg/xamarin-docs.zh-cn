---
title: Xamarin 中的应用内购买基础知识和配置
description: 本文档介绍了 Xamarin 中的应用内购买，并讨论了有关规则、配置和 iTunes Connect 的相关信息。
ms.prod: xamarin
ms.assetid: 11FB7F02-41B3-2B34-5A4F-69F12897FE10
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/18/2017
ms.openlocfilehash: 9347a801c939fd715101253c6953eeec840af47d
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70288590"
---
# <a name="in-app-purchase-basics-and-configuration-in-xamarinios"></a>Xamarin 中的应用内购买基础知识和配置

实现应用内购买要求应用程序在设备上使用 StoreKit API。 StoreKit 管理与 Apple iTunes 服务器的所有通信，以获取产品信息和执行事务。 必须为应用内购买配置配置文件，并且必须在 iTunes Connect 中输入产品信息。

 [![](in-app-purchase-basics-and-configuration-images/image1.png "StoreKit 管理与 Apple 的所有通信，如此图所示")](in-app-purchase-basics-and-configuration-images/image1.png#lightbox)

使用应用商店提供应用内购买需要以下设置和配置：

- **ITunes Connect** –配置产品以销售和设置沙盒用户帐户以测试购买。 你还必须向 Apple 提供银行和税务信息，以便他们能够为你代表你收集的资金。
- **IOS 预配门户**–创建捆绑标识符，并为应用启用应用商店访问。
- **应用商店工具包**–将代码添加到应用，以显示产品、购买产品和还原交易。
- **自定义代码**-跟踪客户进行的购买，并提供他们购买的产品或服务。 如果你的产品包含从服务器下载的内容（如书籍和杂志问题），则你可能还需要实施服务器端过程来验证回执。


有两个存储包 "服务器环境"：

- **生产**-具有真实费用的交易。 仅可通过 Apple 提交和批准的应用程序进行访问。 在生产环境中提供应用内购买产品之前，还必须对其进行审核和批准。
- **沙盒**–测试发生的位置。 产品在创建后立即可用（审批过程仅适用于生产环境）。 沙盒中的事务需要测试用户（而非实际的 Apple Id）来执行事务。

## <a name="in-app-purchase-rules"></a>应用内购买规则

你不能在应用中接受数字产品或服务的其他支付形式，也不会提及它们，也不能让用户从应用内部获取它们。 这意味着，在应用内购买时，不能接受信用卡或 PayPal，这是最合适的支付机制。 有一种特殊情况用于在应用外购买数字产品，但在应用程序中使用，如在与特定 "登录" 相关联的网站上购买书籍，并使用应用中的 "登录" 功能，用户可以访问购买的书籍。
以这种方式运行的应用程序不能提及或链接到外部购买功能，开发人员必须以其他方式（可能通过电子邮件营销或某个其他直接渠道）向其用户传达此功能。

但是，由于不能对实物货物使用应用内购买，因此在这种情况下，可以使用备用支付机制（例如 信用卡，PayPal）。

Apple 必须在每个产品进入销售之前批准该产品–查看需要 "产品" 的名称、描述和屏幕截图。 产品审查时间与应用程序审阅的时间相同。

不能为产品选择任何价格–您只能选择在 Apple 支持的每个国家/地区内具有特定值的 "价格层"。 不同市场中不能有不同的价格层。

## <a name="configuration"></a>配置

编写任何应用内购买代码之前，必须在 iTunes Connect （ [itunesconnect.apple.com](http://itunesconnect.apple.com)）和 IOS 预配门户（ [developer.apple.com/iOS](https://developer.apple.com/iOS)）中执行一些设置工作。

编写任何代码之前，应完成这三个步骤：

- **Apple 开发人员帐户**–将银行和税务信息提交到 Apple。
- **IOS 预配门户**–确保你的应用具有有效的应用 ID （不是包含星号 * 的通配符），并且已启用应用采购。
- **ITunes Connect 应用程序管理**–将产品添加到你的应用程序。


### <a name="apple-developer-account"></a>Apple 开发人员帐户

构建和分发免费应用程序需要在[ITunes Connect](https://itunesconnect.apple.com)中进行极少的配置，但是，若要销售付费应用或应用内购买，要求你向 Apple 提供银行和税务信息。 单击此处显示的主菜单中的 "**协议"、"税款" 和 "银行**"：

 [![](in-app-purchase-basics-and-configuration-images/image2.png "单击主菜单中的协议、税款和银行")](in-app-purchase-basics-and-configuration-images/image2.png#lightbox)

开发人员帐户应具有有效的**IOS 付费应用程序**合同，如以下屏幕截图所示：

 [![](in-app-purchase-basics-and-configuration-images/image3.png "开发人员帐户应具有有效的 iOS 付费应用程序协定")](in-app-purchase-basics-and-configuration-images/image3.png#lightbox)

你将无法测试任何 StoreKit 功能，除非 Apple 处理你的**合同、税务和银行信息，** 否则代码**中的 StoreKit**调用将失败。

### <a name="ios-provisioning-portal"></a>iOS 预配门户

在**IOS 设置门户**的 "**应用 id** " 部分中设置新应用程序。 若要创建新的应用 ID，请转到[IOS 设置门户的成员中心](https://developer.apple.com/membercenter/index.action)，导航到门户的 "**证书、标识符和配置文件**" 部分，然后单击 " *iOS 应用*" 下的 "**标识符**"。 然后，单击右上方的 "+" 以生成新的应用 ID。


用于创建新**应用程序 id**的窗体

 如下所示：

 [![](in-app-purchase-basics-and-configuration-images/image4.png "用于创建新应用程序 Id 的窗体")](in-app-purchase-basics-and-configuration-images/image4.png#lightbox)

输入适用于*说明*的内容，以便可以轻松地在列表中标识此应用 ID。 对于 "*应用 ID 前缀*"，请选择 "团队 id"。

#### <a name="bundle-identifierapp-id-suffix-format"></a>捆绑标识符/应用 ID 后缀格式

你可以使用你喜欢的任何字符串作为**包标识符**（只要它在帐户中是唯一的），但 Apple 建议你遵循反向 DNS 格式，而不是使用任何任意字符串。 本文附带的示例应用程序使用 storekit 作为捆绑标识符，但使用 my_store_example 等标识符（即使 Apple 不建议这样做）是相同的。

> [!IMPORTANT]
> Apple 还允许将通配符添加到**捆绑标识符**的末尾，以便单个应用 id 可用于多个应用程序，但_通配符应用 id 不能用于 AppPurchase 中_。 例如，通配符捆绑标识符可以是 .com. *

#### <a name="enabling-app-services"></a>启用应用服务

请注意，**在 "应用内购买**" 将自动在服务列表中启用：

 [![](in-app-purchase-basics-and-configuration-images/image5.png "应用内购买将在服务列表中自动启用")](in-app-purchase-basics-and-configuration-images/image5.png#lightbox)

#### <a name="provisioning-profiles"></a>预配配置文件

按常规方式创建开发和生产预配配置文件，选择已为应用内购买设置的应用 ID。 有关详细信息，请参阅[IOS 设备预配](~/ios/get-started/installation/device-provisioning/index.md)和[发布到 App Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)指南。

## <a name="itunes-connect"></a>iTunes 连接

在 iTunes Connect 中单击 **"我的应用**" 以创建或编辑 iOS 应用程序条目。 应用程序概述页如下所示：

 [![](in-app-purchase-basics-and-configuration-images/image6.png "应用程序概述页")](in-app-purchase-basics-and-configuration-images/image6.png#lightbox)

单击 "**应用内购买**"，创建或编辑要销售的产品。 此屏幕截图显示已添加几个产品的示例应用：

 [![](in-app-purchase-basics-and-configuration-images/image7.png "已添加几个产品的示例应用")](in-app-purchase-basics-and-configuration-images/image7.png#lightbox)

添加新产品的过程有两个步骤：

1. 选择产品类型：[![](in-app-purchase-basics-and-configuration-images/image8.png "选择产品类型")](in-app-purchase-basics-and-configuration-images/image8.png#lightbox) 
2. 输入产品的属性，包括产品 Id、定价层和本地化说明：[![](in-app-purchase-basics-and-configuration-images/image9.png "输入 products 特性")](in-app-purchase-basics-and-configuration-images/image9.png#lightbox)

下面介绍了每个应用内购买产品所需的字段：


### <a name="reference-name"></a>引用名称

不会向用户显示引用名称;它供内部使用，且仅出现在 iTunes Connect 中。

### <a name="product-id-format"></a>产品 ID 格式

产品标识符只能包含字母数字字符（A-z、a-z、0-9）、下划线（_）和句点（.）字符。 尽管可以使用任何字符串作为标识符，但 Apple 建议使用反向 DNS 格式。 例如，示例应用程序使用此捆绑标识符：

 `com.xamarin.storekit.testing`

因此，用于标识应用内购买产品的约定如下所示：

```csharp
com.xamarin.storekit.testing.consume5credits
com.xamarin.storekit.testing.consume10credits
com.xamarin.storekit.testing.sepia
com.xamarin.storekit.testing.greyscale
```

此命名约定不是强制性的，只是一种建议，可帮助您管理您的产品。 此外，尽管遵循相同的反向 DNS 约定，产品*标识符与捆绑*标识符无关，不需要以相同的字符串开头。 使用类似于 photo_product_greyscale 的标识符仍然有效（即使 Apple 不建议这样做）。

产品 ID 不会显示给用户，而是用于引用应用程序代码中的产品。

### <a name="product-type"></a>产品类型

可以提供五种类型的应用内购买产品：

1. 可**使用– "** 已用时" 的项，如播放机可花费的游戏货币。 如果用户执行备份/还原或在其他情况下刷新了其设备，则也不会还原可使用的事务（这将有效地为播放机会提供相同的优势）。 在事务完成后，应用程序代码必须确保提供 "可耗用项"。
1. **不可耗用**–用户购买后的产品（如数字杂志问题或游戏级别）。
1. **自动可续订订阅**–与实际杂志订阅一样，在订阅期结束时，Apple 会自动向客户收取费用，并在客户显式取消之前自动向客户收费。 这是 Newsstand 应用的首选付款方式（事实上，应用必须支持将此支付方法用于 Newsstand 分发）。
1. **免费订阅**–只能在已启用 Newsstand 的应用中提供，并允许客户访问其所有设备上的订阅内容。 免费订阅永不过期。
1. **非续订订阅**–应该用于出售对静态内容（例如一个月的照片存档访问权限）的限时访问。


 *本文档当前仅介绍前两种产品类型（可耗用和不可用）。*

 <a name="Price_Tiers" />

### <a name="price-tiers"></a>价格层

应用商店不允许你为你的产品选择任意价格– Apple 提供了你可以从中选择的固定价格层。 价格以每个货币进行固定，并且 Apple 保留调整相对价格的权利（例如，在特定货币和美国元之间的相对外汇汇率发生了持久更改后）。

Apple 提供了一个价格矩阵，可帮助你选择所需的货币/价格的正确层。 价格矩阵（2012年8月）的摘选如下所示：

 [![](in-app-purchase-basics-and-configuration-images/image10.png "2012年8月的价格矩阵摘录")](in-app-purchase-basics-and-configuration-images/image10.png#lightbox)

撰写本文时（2013年6月），有87层（从 USD 0.99 到 USD 999.99）。 定价矩阵显示了你的客户将支付的价格，还显示了你将从 Apple 获得的金额–这少于 30% 的费用，还需要收集的任何当地税（请注意，美国和加拿大卖方收到 99c p 的70c 的示例产品，而澳大利亚卖方只收到63c，因为在销售&amp;价格上征收了 "货物服务税"。

您可以随时更新您的产品的定价，包括计划在将来的日期生效的价格变化。 此屏幕截图显示了如何添加未来的价格变化–价格在9月的第1层临时更改为第3层：

 [![](in-app-purchase-basics-and-configuration-images/image11.png "未来的价格变化，其中价格将从第1层暂时更改为第3个月的第3个月")](in-app-purchase-basics-and-configuration-images/image11.png#lightbox)

### <a name="free-products-not-supported"></a>免费产品不受支持

尽管 Apple 提供了一个用于 Newsstand 应用的特殊免费订阅选项，但不能为任何其他应用内采购类型设置零（免费）价格。 尽管可以编辑（即较低）的销售促销价格，但无法通过 iTunes Connect 进行应用内购买。

### <a name="localization"></a>本地化

在 iTunes Connect 中，可以为任意数量的受支持的语言输入不同的名称和描述文本。 可以通过弹出窗口在中添加/编辑每种语言：

 [![](in-app-purchase-basics-and-configuration-images/image12.png "可以通过弹出窗口添加/编辑每种语言")](in-app-purchase-basics-and-configuration-images/image12.png#lightbox)   
   
   
   
 当你在应用中显示产品信息时，可通过 StoreKit 显示本地化的文本。 还必须本地化货币显示以显示正确的符号和小数点格式，这种格式设置将在此文档的后面部分介绍。

### <a name="app-store-review"></a>App Store 审核

与应用相同–每个产品都是由 Apple 审核的，然后才允许开始使用。 对于名称或说明中不适当的内容，可能会拒绝产品，或者 Apple 可能会决定选择了错误的产品类型（例如 您已经创建了图书或杂志问题，但使用的是可使用的产品类型）。 对于应用评审，产品审查可能需要花费很长时间。

第一次在应用中启用应用内购买（无论它是新应用程序还是已将功能添加到现有应用程序）时，还必须选择要随其一起提交的产品。 ITunes Connect 门户将提示你执行此操作，如以下屏幕截图所示：

 [![](in-app-purchase-basics-and-configuration-images/image13.png "ITunes Connect 门户还会提示你还提交一些产品")](in-app-purchase-basics-and-configuration-images/image13.png#lightbox)   
   
   
   
 应用程序和应用内购买将一起进行查看，因此它们都将立即获得批准（因此，在没有任何批准的产品的情况下，应用程序不会进入存储区！）。

在第一版具有应用内购买功能的第一版获得批准后，可以添加更多产品并随时提交以供查看。 你还可以选择随特定应用内购买产品一起提交新版本，并在提示时使用**版本详细信息**页。

有关详细信息，请参阅[应用商店查看指南](https://developer.apple.com/appstore/guidelines.html)。

 [第2部分-商店工具包概述和检索产品信息](~/ios/platform/in-app-purchasing/store-kit-overview-and-retreiving-product-information.md)
